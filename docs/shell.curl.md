```shell

# post
curl http://www.baidu.com/api/service \
	-H "Authorization: token" \
	-H "Content-Type:application/json" \
	-X POST \
	-d '{"id":1,"companyId":3}'

# upload file
curl -F "file=@/path/《Java开发手册》泰山版.pdf" http://localhost:8088/file/upload

# get
curl http://localhost:8088/company/list

# get parameters
curl http://www.baidu.com/api/service/page?idNumber=_jCGxJ5XKJymNMido0LZ20qVH9fUiNlQblWKVj9qOvU&pageNum=1&pageSize=10 \
	-H "Authorization: token" 

# delete
curl http://www.baidu.com/api/service/26 \
	-H "Authorization: token" \
	-X DELETE

curl -H "Content-Type:application/x-www-form-urlencoded" -d "mobiile=" -X post

ww39caa03b9a6df791
dlrWQ6r5HzUh8elHxIv3svuQCa9CoC3GNyBDHgXYM7w

```


mysql -uroot -pxiaoi_219_mysql8_PWD -Ddb_migration_56_80

```
api=%2Fapi%2Fcardbusiness%2Faggregatepay%2Fb2c%2Fonline%2Fconsumepurchase%2FV1&app_id=10000000000000241599&biz_content=%7B%22access_type%22%3A%224%22%2C%22attach%22%3A%22%22%2C%22bank_disc_amt%22%3A%220%22%2C%22card_flag%22%3A%22%22%2C%22card_kind%22%3A%22%22%2C%22card_no%22%3A%22%22%2C%22coupon_amt%22%3A%220%22%2C%22cust_id%22%3A%22%22%2C%22decr_flag%22%3A%22%22%2C%22ecoupon_amt%22%3A%220%22%2C%22mer_disc_amt%22%3A%220%22%2C%22mer_id%22%3A%22100159932297%22%2C%22msg_id%22%3A%22050202892323818195348132299%22%2C%22open_id%22%3A%222088512172959984%22%2C%22order_id%22%3A%22100159932297000722211190016593%22%2C%22out_trade_no%22%3A%22Sp2022111900006%22%2C%22pay_time%22%3A%2220221220000001%22%2C%22pay_type%22%3A%2210%22%2C%22payment_amt%22%3A%221%22%2C%22point_amt%22%3A%220%22%2C%22return_code%22%3A%220%22%2C%22return_msg%22%3A%22%E4%BA%A4%E6%98%93%E6%88%90%E5%8A%9F%22%2C%22third_party_coupon_amt%22%3A%220%22%2C%22third_party_discount_amt%22%3A%220%22%2C%22third_trade_no%22%3A%222022111922001459981459940164%22%2C%22total_amt%22%3A%221%22%2C%22total_disc_amt%22%3A%220%22%7D&charset=UTF-8&format=json&from=icbc-api&sign=NIQi68X23UJU4R9byHBQH1%2BgJP3qtV6AMS7hC0Nhl4gg8ehf8wfqZicjwsCcQ70u5Yj%2FlgszYTQVrhCo2bnn6hVxI8Syo2vjM5O0dGU62j9kcjPqAbh604gA07kBIHD75bHuqYlAImUtOHQEB7jgV7p1Q83jpQM0zHZoDwbNFNQ%3D&sign_type=RSA&timestamp=2022-11-19+19%3A53%3A21
```


## application-mac.yml

```yaml
server:  
  port: 8088  
# DataSource Config  
spring:  
  application:  
    name: honestPlatform  
  redis:  
# redis 集群配置  
    #    cluster:  
    #      nodes: 172.16.6.231:7000,172.16.6.231:7001,172.16.6.231:7002,172.16.6.231:7003,172.16.6.232:7004,172.16.6.232:7005    #      max-redirects: 3    lettuce:  
      pool:  
        max-active: 8 # 连接池最大连接数  
        max-idle: 8 # 连接池最大空闲连接数  
        min-idle: 0 # 连接池最小空闲连接数  
        max-wait: -1ms # 连接池最大阻塞等待时间，负值表示没有限制  
    password:  
    # redis 单机配置  
    host: 127.0.0.1 #本地开发环境单节点  
    port: 6379  
    timeout: 3000  
    database: 1  
  datasource:  
    driver-class-name: com.mysql.cj.jdbc.Driver  
    url: jdbc:mysql://127.0.0.1:6060/db_honest_platform?characterEncoding=UTF-8&useSSL=true&serverTimezone=GMT%2B8  
    username: root  
    password: liancheng  
  #Flyway 数库迁移配置  
  flyway:  
    enabled: false  
    clean-disabled: true  
    baseline-on-migrate: true  
  jackson:  
    date-format: yyyy-MM-dd HH:mm:ss  
    time-zone: GMT+8  
  servlet:  
    multipart:  
      max-request-size: 5MB  
      max-file-size: 5MB  
  
mybatis:  
  # 指定别名设置的包为所有entity  
  type-aliases-package: com.hp.module.**.entity  
  configuration:  
    map-underscore-to-camel-case: true # 驼峰命名规范  
  mapper-locations: # mapper映射文件位置  
    - classpath:mapper/*.xml  
  
#logging:  
#  level:  
#    org.springframework.web: debug  
#    com.hp: debug  
  
# 阿里云oss配置  
aliyun-oss:  
  access-key-id: LTAI4Fi8SUq2jSXS4XHBe4CZ  
  access-key-secret: RM4fvqNOZg66jdlwChzXqkuNkiGWkf  
  buckets: uv-uat  
  end-point: http://oss-cn-shanghai.aliyuncs.com  
  
# Sa-Token配置  
sa-token:  
  # token名称 (同时也是cookie名称)  
  token-name: token  
  # token有效期，单位s 默认30天, -1代表永不过期 10个小时  
  timeout: 36000  
  # token临时有效期 (指定时间内无操作就视为token过期) 单位: 秒  
  activity-timeout: -1  
  # 是否允许同一账号并发登录 (为true时允许一起登录, 为false时新登录挤掉旧登录)  
  is-concurrent: true  
  # 在多人登录同一账号时，是否共用一个token (为true时所有登录共用一个token, 为false时每次登录新建一个token)  
  is-share: true  
  # 是否尝试从 请求体 里读取 Token  is-read-body: false  
  # 是否尝试从 header 里读取 Token  is-read-head: true  
  #是否尝试从 cookie 里读取 Token  is-read-cookie: true  
  # token风格  
  token-style: uuid  
  # 是否输出操作日志  
  is-log: true  
  #是否在初始化配置时打印版本字符画  
  is-print: false  
  cookie:  
    # 是否禁止 js 操作 Cookie    http-only: true  
#生成支付二维码参数配置  
pay:  
  #生成二维码接口地址  
  service_url:  https://apipcs3.dccnet.com.cn/api/cardbusiness/qrcode/consumption/V1  
  #app的编号，应用在API开放平台注册时生成  
  app_id: 10000000000000223602  
  #私钥  
  my_private_key: MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQCacT9obbQ46mU/xh3SvWOtGz+ClyXrdHiiIN2RUklUjkA012HVUpxzjb2lA255ZDEriS0Zyxshr3Arercfg4r/jL/C1dVCam1d07/QcUKQzqqTWE7UP/tekyQO8GxUdJzfa3nSKaYwXNHcga2yjWMImW1IOkQeT3Ps0Kz7nRK5SL9nOgUzc/JWuvjP3aDImbN5Jp+Kf0PS78VbbeSgBGIVgHUstkb9/iTjsOhNnE+g/O5NyPk/m5QDLmz5uGXqBT6+spybIGCiMz60rov3UOSkOLZCYSlyR5Akly20wk/jK1C2gdkvGExMHmhkwVmnSgZ4gqmVXCrxJoJqRyT38oyfAgMBAAECggEAc8HIFdyMBVDiPK+gsNDVAe13Z8kkWdt9g3+AsqhrYB4Sz8hTgtGdhBrL9v+gcQoAtLHnhPqvxkq5l00uLkepCiuTm1QsuNAOUxIgJfqOOv076p54zyt5nARLNYtMluykm+zxbO1cq8kWXuEvsLG6dbqRp15Mqi9BI1GxJhIv84D6koVURHWRs6pB2Lk1pReOpM2XY9xfTgf6QsF+L580jmQmppoQJLoJ7NhwdA92RsbwnTjZbR8egm4wMmJWjT0xWmoSrSlHMutVayUS2vmlMYx42sYSTjQKLQ3qtt84J96B+D7bbjuCcZ7oHOTndPoLnV0T6K7caRe6fr5CsW/mgQKBgQD5FIAa89KXyj+zkw/ywXZoGl9XQMMePcun3ayjCQXJ9IgxfoE6rYkufzjOvR+UOqUNa5bCD4YtQ90+uDp790RYt1OwHBTtxFZa5EMdZ3OVhRRwtdYiWls0p7Cjg/aLlWRTAxq5bmu1hAjfe3m/aHv1b+XK0dYP4NfsDHRWGjotXwKBgQCeu6r9dFi/8d+yGmgNIMHT/FsT5KwlceoND3UcChyovtt6+iGwMd0dAYZOIE67hgtgAoF7HxdDijut8q3PGaYvl8WroZfBCjis2ZLrmqe3k7+LaimTQNqBbubSi20nqF4Q+YKsofe2AkkhmN1DRkFELaiYSyGkDSc55O5IDbCowQKBgFq9SCoJd0fK60f9JEs4hW1JZkBAHJTwyDeAPzspt00qY088mdGJlLc2q7hNF6sFbkmkT0ilJSlqVrhZnxy4KNL0/mzi3hD8fMUh8P1OScJHt/mTlM3xAtb83BHFqlSRePnw4JR7EIwQ7LaVofXEF5yDRkCMDyK7s42Pv/X6GgWbAoGAe7XdtP1pcedLpV5pO9oM1m92eFGfTOu0MwuaBQlofqeJHRY3DUA/iSy4BMmvMv1sfh5V1aRMmEny60YY6jCeUbdiyrn9MOkLdMN5ewAkuLGA9mH+FxLTeU76vJsHroHNFkOv8WWmTocHghTGVXk6wq0nGdJsXJQXv0pJ34Qz68ECgYEAugXGWTAkgM2JUHCNxkfp0AlergmWII/eTakiN/Siw3/bb0C9dK+fs1FTVlxGKCbwl6WC62VRViVqXWJuy1U6HdTHpHr/JOH7IK9RqzLwn/DmpQ4E9myxqyFHN5+3+hKKpJbV7f4YwUVYEIMTwliXivcU0Hqlu/z6E3PpG7iDrkY=  
  #网关公钥  
  apigw_public_key: MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCwFgHD4kzEVPdOj03ctKM7KV+16bWZ5BMNgvEeuEQwfQYkRVwI9HFOGkwNTMn5hiJXHnlXYCX+zp5r6R52MY0O7BsTCLT7aHaxsANsvI9ABGx3OaTVlPB59M6GPbJh0uXvio0m1r/lTW3Z60RU6Q3oid/rNhP3CiNgg0W6O3AGqwIDAQAB  
  #商户编号  
  mer_id: 100159932254  
  #收单协议编号  
  mer_prtcl_no: 1001599322540201  
  #商户账号，商户入账账号，只能交易时指定。（商户付给银行手续费的账户，可以在开户的时候指定，也可以用交易指定方式；用交易指定方式则使用此商户账号）  
  mer_acct: 1001280909004620106  
  #通知商户URL，必须合法的URL，交易结束，银行使用HTTP协议POST方式向此地址发送通知信息；目前只支持80端  
  mer_url: http://localhost:443/notify  
  #订单时间（用于测试，生产环境不用配置）  
  order_date: 2022-03-22  
  
icbc-ui-pay:  
  #生成二维码接口地址  
  service_url: https://gw.open.icbc.com.cn/ui/thirdparty/order/V1/pay  
  #app的编号，应用在API开放平台注册时生成  
  app_id: 10000000000000241599  
  #私钥  
  my_private_key: MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQC4QaLb/7qXmRhaLQNyoKeQHgqzxlHmN3sy+g9QG/e+kUhkgZOoBgegOWVQakiEuX3SIK8/HYaENyiiaOKnEXefMk5F3+mmO3B1VNasZDFdxLJKWAkMZgO3c7htQFOySvvKARk1kabzToZOWA6UJ0FxN+Nu126nWL7N22wF7ljRoIg8+0H0olL6+ueXvigSQPlvr/ie5D+hK8HKwH0ytHyO2qBbuoKYiPzfFQzI900HVV7EzNte88GU0XQ/Lddhqt5ohqfmHB0/goIAL0lYcNPG8II87OsdniIFCZlcfzvfeofzO6idWH7QlX4xqRwWMQdu73BH90bDwvZnJhlA4M9BAgMBAAECggEAJ2Fn5GtQvwvqh4NtZDdv+L5i/S9a28Bp5WhKk42WsrUsasbC/ztir9z0iW5d13gpqxpvOiceU+3SEnwmTRzPm0fOMOL9dhjoHTunsiqr3mWm0XrlXDAFhVUHStpfyRB7ph37/FYH/6J91Aq/+uw08XTHeC41KTEvfqHyAYaClzLBS16hnsRcbyJSqbxPIJavyClpKy9rWQItvAyY7/G7EK5BuPmvwm15Y3piEJm1oconDbYsf5E6wN33ywejmOyO6AGmwWQquvvxYIr4rQyI1+0vAMtJ00mJZEsunhFXI6ieOM9LXES+KPL91MQyiY0B/ivLtrO8Uqw0PxVdyXWuDQKBgQD+YVH7Y1PWbABRJeaHKqGpqsoqEA8us8ddqsGDFo35DVcS9CIkeFMUOmaDn8b46oIp/qXpPRLT2G2+JhDrp+Nq+YNzEPRe0hV/goaqJh0dMG3kyjOzsXzZJfkVRqhfHwUR2TRTvNoVo0VuEgqosvQnEoicos3ZbYvbNHV1S29aewKBgQC5bgDNZ6eUyMFsGMfzIgmjUGrkDv7Ddp8QSUIpdGVUgmKg/i/LQM/NBzu2YKAx+ynbtvp5nw9Tt6k0f7phXJou1NI2Ex1AOZTF4HlIEmAuCQMxKOrYJ4Nc1RyP1SQYaBPnNSRCoGoK/DNoyaCMB3FgBQHHFH6YcZoqsPhOJ0ZecwKBgEsDa+eT7N8oThef85EcczvTT3aD8dY10a6/cf00I32z/19pesACc41dg2mBLm2syeGUZFyZU/oAsqwGEatMgPXEyoyeDVIA5bIKTafTQXagF2gGcKSmzEhEuGUbxv716MrQoGupHy6OTSUeUOJak0EKaBq4r7dUZI+pspF6Q0YNAoGBAKIkGDuD+btV2BwHhz+IcdkGRpFos0iDfw8kn9/T0Y8V+9w+p9gYshvbyVDuKOXLc4yV8WtLtIFFxYUMcxMTgBaXLcDu8dLpBrgIUsS8nLFkXBAucTvVQT55cuIqQ8e7qjuv8ib4UgGNCf8E34fSMkb92dIUj6YJH/9Yg6cDHLsVAoGBAO87+iuvTmB+ggZ9KMwysHt/awgBVQpgEppYGf9AG/6Iiz+NwWqyJdkQC2iNmEpJkXgg6OtyJsZxV99ZC+XY7K8u7W8UwzAVhe4elJJd+DkajLz34yarklnNUEad0jSlHyVNq9tl4QO+/XgvPtkVQ9GRkJ7WTu0Eo9q7rWy2Lgjc  
  #网关公钥  
  apigw_public_key: MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCwFgHD4kzEVPdOj03ctKM7KV+16bWZ5BMNgvEeuEQwfQYkRVwI9HFOGkwNTMn5hiJXHnlXYCX+zp5r6R52MY0O7BsTCLT7aHaxsANsvI9ABGx3OaTVlPB59M6GPbJh0uXvio0m1r/lTW3Z60RU6Q3oid/rNhP3CiNgg0W6O3AGqwIDAQAB  
  #商户编号  
  mer_id: 100159932297  
  #收单协议编号  
  mer_prtcl_no: 1001599322970201  
  #商户账号，商户入账账号，只能交易时指定。（商户付给银行手续费的账户，可以在开户的时候指定，也可以用交易指定方式；用交易指定方式则使用此商户账号）  
  mer_acct: 1001280909004620106  
  #通知商户URL，必须合法的URL，交易结束，银行使用HTTP协议POST方式向此地址发送通知信息；目前只支持80端  
  mer_url: http://localhost:443/notify  
  #订单时间（用于测试，生产环境不用配置）  
  order_date: 2022-08-29  
  
wx:  
  #微信小程序配置  
  miniapp:  
    appid: wxe5b50da4095c45c8 #微信小程序的appid  
    secret: c5b08fecea2749af5061807f6c7ea6d8 #微信小程序的Secret  
    token: #微信小程序消息服务器配置的token  
    aesKey: #微信小程序消息服务器配置的EncodingAESKey  
    msgDataFormat: JSON  
  #微信公众号配置  
  mp:  
    appid: wx36d5a82f965627dd #微信公众号的appid  
    secret: 3c8e9658f4430209331d33d49100ed92 #微信公众号的app secret  
    token: #微信公众号的token  
    aesKey: #微信公众号的EncodingAESKey  
  cp:  
    corpId: ww39caa03b9a6df791 #企业微信的corpId  
    agentId: 1000001 #企业微信应用的AgentId  
    secret: dlrWQ6r5HzUh8elHxIv3svuQCa9CoC3GNyBDHgXYM7w #企业微信应用的Secret  
    token: TvHRK #企业微信应用的token  
    aesKey: APsXqB65gg74aYPHo9nJRTJdNQr5WLNcu6XQCVUxS1J #企业微信应用的EncodingAESKey  
  
api:  
  #包装以前的老接口  
  appkey: 666  
  secret: 777  
  
h5:  
  #培训平台  
  url: https://etp.fdcjj.org/shanghai/login-by-sign/sign/5?sign=  
  
#####JWT配置##### 小程序登录返回token  
jwt:  
  secret: xiaoi_admin  
  # 过期时间秒  
  expiration: 36000  
  # 记录一些常用配置  
common:  
  # 普通二维码地址 （扫码可跳转小程序）  
  baseQrUrl: http://bcp-test.fdcjj.org/scan/agent?sai=  
    # 信息卡图片合成url  
  cardCompositeUrl: http://47.100.43.210:8072/webroot/main.php/json/license/generateCard  
  # 头像默认url 具体需要生产阿里上的路径  
  headDefaultUrl: http://uv-uat.oss-cn-shanghai.aliyuncs.com/ffa7addad78349d684bf6c2fc8a7b73cdefault.jpg  
  # 配置jvm监控  
management:  
  security:  
    # 仅限于 开发环境可对security进行关闭。  
    enabled: false  
  endpoint:  
    health:  
      show-details: always  
    metrics:  
      enabled: true  
    prometheus:  
      enabled: true  
  endpoints:  
    web:  
      exposure:  
        include: health,prometheus,metrics,httptrace,threaddump,heapdump,springmetrics  
        #include: health,info,env,prometheus,metrics,httptrace,threaddump,heapdump,springmetrics  
        #include: '*'  metrics:  
    export:  
      prometheus:  
        enabled: true  
    tags:  
      application: ${spring.application.name}  
  
baidu-ocr:  
  appId: 25926295  
  appKey: lTfjWRMgKQVydlvPFLZn6Vs1  
  appSecret: 31xy3icAHFRjhXGoRATq5CTqzVZ7qoGd  
  
  
starer:  
  entryFee: 5000  
  
#培训平台相关配置  
pxpt:  
  tenantId: pxzh # tenantId  
  userName: admin    # 登录用户名  
  password: xiaoi888! # 登录密码  
  serviceName: iBotCoach1  
  ip: 127.0.0.1  
  loginUrl: http://etp2.fdcjj.org/api/auth-user/login #登录接口地址  
  addUserUrl: http://etp2.fdcjj.org/api/hp-api/users #添加用户接口地址  
  h5AuthLoginUrl: https://etp2.fdcjj.org/h5/#/pages/login/auth #h5前端自动登录认证页面  
  
#h5年审支付完成回调页面地址  
yearAuditReturnUrl:  
#年审微信通知模板id  
yearAuditWxTemplateId: tCMOTAKhstubJ6YdL7EdkjhOYMF6vckV_i0WhJ9sw80
```