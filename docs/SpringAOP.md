
* Advice
  + 前置通知（Before）：在目标方法调用前调用通知功能；
  + 后置通知（After）：在目标方法调用之后调用通知功能，不关心方法的返回结果；
  + 返回通知（AfterReturning）：在目标方法成功执行之后调用通知功能；
  + 异常通知（AfterThrowing）：在目标方法抛出异常后调用通知功能；
  + 环绕通知（Around）：通知包裹了目标方法，在目标方法调用之前和之后执行自定义的行为。
* JoinPoint(通知功能被应用的时机。比如接口方法被调用的时候就是日志切面的连接点。)
* Pointcut(切点定义了通知功能被应用的范围。比如日志切面的应用范围就是所有接口，即所有controller层的接口方法。)
* Aspect(切面是通知和切点的结合，定义了何时、何地应用通知功能。)
* Introduction(在无需修改现有类的情况下，向现有的类添加新方法或属性。)
* Weaving(把切面应用到目标对象并创建新的代理对象的过程。)

## 使用方式
* @Aspect：用于定义切面(事情)
* @Before：通知方法会在目标方法调用之前执行
* @After：通知方法会在目标方法返回或抛出异常后执行
* @AfterReturning：通知方法会在目标方法返回后执行
* @AfterThrowing：通知方法会在目标方法抛出异常后执行
* @Around：通知方法会将目标方法封装起来
* @Pointcut：定义切点表达式(声明)

```
execution(方法修饰符 返回类型 方法所属的包.类名.方法名称(方法参数)

//com.macro.mall.tiny.controller包中所有类的public方法都应用切面里的通知
execution(public * com.macro.mall.tiny.controller.*.*(..))
//com.macro.mall.tiny.service包及其子包下所有类中的所有方法都应用切面里的通知
execution(* com.macro.mall.tiny.service..*.*(..))
//com.macro.mall.tiny.service.PmsBrandService类中的所有方法都应用切面里的通知
execution(* com.macro.mall.tiny.service.PmsBrandService.*(..))
```

```java
/**
 * 统一日志处理切面
 * Created by macro on 2018/4/26.
 */
@Aspect
@Component
@Order(1)
public class WebLogAspect {
    private static final Logger LOGGER = LoggerFactory.getLogger(WebLogAspect.class);

    @Pointcut("execution(public * com.macro.mall.tiny.controller.*.*(..))")
    public void webLog() {
    }

    @Before("webLog()")
    public void doBefore(JoinPoint joinPoint) throws Throwable {
    }

    @AfterReturning(value = "webLog()", returning = "ret")
    public void doAfterReturning(Object ret) throws Throwable {
    }

    @Around("webLog()")
    public Object doAround(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();
        //获取当前请求对象
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();
        //记录请求信息
        WebLog webLog = new WebLog();
        Object result = joinPoint.proceed();
        Signature signature = joinPoint.getSignature();
        MethodSignature methodSignature = (MethodSignature) signature;
        Method method = methodSignature.getMethod();
        if (method.isAnnotationPresent(ApiOperation.class)) {
            ApiOperation apiOperation = method.getAnnotation(ApiOperation.class);
            webLog.setDescription(apiOperation.value());
        }
        long endTime = System.currentTimeMillis();
        String urlStr = request.getRequestURL().toString();
        webLog.setBasePath(StrUtil.removeSuffix(urlStr, URLUtil.url(urlStr).getPath()));
        webLog.setIp(request.getRemoteUser());
        webLog.setMethod(request.getMethod());
        webLog.setParameter(getParameter(method, joinPoint.getArgs()));
        webLog.setResult(result);
        webLog.setSpendTime((int) (endTime - startTime));
        webLog.setStartTime(startTime);
        webLog.setUri(request.getRequestURI());
        webLog.setUrl(request.getRequestURL().toString());
        LOGGER.info("{}", JSONUtil.parse(webLog));
        return result;
    }

    /**
     * 根据方法和传入的参数获取请求参数
     */
    private Object getParameter(Method method, Object[] args) {
        List<Object> argList = new ArrayList<>();
        Parameter[] parameters = method.getParameters();
        for (int i = 0; i < parameters.length; i++) {
            //将RequestBody注解修饰的参数作为请求参数
            RequestBody requestBody = parameters[i].getAnnotation(RequestBody.class);
            if (requestBody != null) {
                argList.add(args[i]);
            }
            //将RequestParam注解修饰的参数作为请求参数
            RequestParam requestParam = parameters[i].getAnnotation(RequestParam.class);
            if (requestParam != null) {
                Map<String, Object> map = new HashMap<>();
                String key = parameters[i].getName();
                if (!StringUtils.isEmpty(requestParam.value())) {
                    key = requestParam.value();
                }
                map.put(key, args[i]);
                argList.add(map);
            }
        }
        if (argList.size() == 0) {
            return null;
        } else if (argList.size() == 1) {
            return argList.get(0);
        } else {
            return argList;
        }
    }
}
```