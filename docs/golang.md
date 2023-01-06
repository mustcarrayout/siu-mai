- # 基础知识
	- 习惯
		- 怎么用?
		- 如果这样呢,还可以这样吗?
		- 怎么实现的?深入思考一下?如果是我会怎么做?
	- 命令行
	- 变量(类型字面量、值字面量)
		- ```
		  a1 := [3]string{"a","b","c"} # array
		  a2 := []string{"a","b","c"}  # slice=list
		  # 两者是不一样的,类型不一样,什么类型不一样“类型字面量”
		  # slice => array, 可变数组
		  ```
		- 基础类型
			- 申明
				- ```go
				  # #1
				  var name type
				  # #2
				  name := "Hello world!"
				  
				  # Q 这两种方式的区别
				  ```
			- 类型划分
				- 自带类型
				- 别名类型
				- 类型再定义
			- 类型判断
				- ```go
				  # {}
				  val, ok := interface{}(variable).(type)
				  ```
			- 类型转换
				- ```go
				  // 数据转字符串
				  str := strconv.Itoa(12)
				  println("str + '23' = ", str+"23")
				  
				  // 字符串转数字(这里特指整数)
				  n, err := strconv.Atoi("123")
				  if err!=nil {
				    panic(err)
				  }
				  println("12+n = ", 12+n)
				  // %s %d %f
				  str := fmt.Sprintf("123%s", "hello")
				  
				  // 列表拼接
				  str := []string{"hello", "world"}
				  s := strings.Join(str, " ")
				  //输出：`hello world`
				  
				  // 大字符串拼接
				  var build strings.Builder
				  build.WriteString("123")
				  build.WriteString("ade")
				  build.WriteString("ade")
				  build.WriteString("ade")
				  println(build.String())
				  
				  // buffer 写入, buffer是一种io的实现
				  buffer := bytes.Buffer{}
				  buffer.WriteString("hello")
				  buffer.WriteString("golang")
				  println(buffer.String())
				  ```
			- 复合类型的使用和高效应用
			- 两大类型
				- 值类型(**array**|**struct**)
				- 引用类型
					- map|slice|channel|function
					- ```go
					  # 类型字面量,值类型
					  # len只有效内容长度,cap表示上限
					  name := make([]int, 5)    # len, cap = 5
					  name := make([]int, 3, 5) # len, cap = 3,5
					  ```
			- 变量作用域
				- 代码块
				- 包
				- 公开的
		- 常量
	- default lib
		- 概述
			- ```go
					# 字符串转各种类型
					import "strconv"
					// func ParseInt(s string, base int, bitSize int) (i int64, err error)
					// func ParseUint(s string, base int, bitSize int) (uint64, error)
					
					// func Itoa(i int) string
					// func Atoi(s string) (int, error)
					
					// func FormatFloat(f float64, fmt byte, prec, bitSize int) string
					// func ParseFloat(s string, bitSize int) (float64, error)
					
			  ```
		- string
			- ```go
				func main() {
					// 拼接
					s1 := fmt.Sprintf("%s:%d", "127.0.0.1", 8080)
					fmt.Println(s1)
					// slice 拼接
					s2 := strings.Join([]string{"hello", "world"}, "")
					fmt.Println(s2)
					// 字符串拼接3 走字节数组
					s3 := bytes.Buffer{}
					s3.WriteString("hello")
					s3.WriteString("world")
					fmt.Println(s3.String())
					
					// 字符串拼接4
					s4 := "hello" + "world"
					fmt.Println(s4)
				}
				```
		- date
			- ```go
			  # 10 位数的时间戳是以 秒 为单位；
			  # 13 位数的时间戳是以 毫秒 为单位；
			  # 19 位数的时间戳是以 纳秒 为单位；
			  
			  func printDate() {
			    fmt.Printf("时间戳（秒）：%v;\n", time.Now().Unix())
			    fmt.Printf("时间戳（纳秒）：%v;\n",time.Now().UnixNano())
			    fmt.Printf("时间戳（毫秒）：%v;\n",time.Now().UnixNano() / 1e6)
			    fmt.Printf("时间戳（纳秒转换为秒）：%v;\n",time.Now().UnixNano() / 1e9)
			  }
			  ```
		- 正则
		  - ```go
		  import (
		     "fmt"
		     "regexp"
		  )
		  
		  reg = regexp.MustCompile(`(?U)\b.+\b`)          // 查找 “单词边界” 之间的字符串
		  fmt.Printf("%q\n", reg.FindAllString(text, -1)) // ["Hello" "世界！" "123" """Go"]
		  ```
		- io
			- ```go
			  fs, e := os.OpenFile(path,mode,0666)
			  fs.Write([]byte)
			  fs.WriteString(str)
			  
			  ioutil.WriteFile(path, []byte, os.FileMode)
			  ```
		- json
			- ```go
			  import (
			    "fmt"
			    "encoding/json"
			  )
			  
			  // 自定义key
			  type Monster struct {
			    Name string `json:"name"`
			    Age int `json:"age"`
			    Birthday string `json:"birthday"`
			    Sal float64
			    Skill string
			  }
			  
			  func main() {
			    monster := Monster{
			      Name :"牛魔王", Age : 500,
			      Birthday : "2011-11-11",
			      Sal : 8000.0,
			      Skill : "牛魔拳",
			    }
			    // 序列化
			    data, err := json.Marshal(&monster)
			    // 
			    str := "{\"Name\":\"牛魔王\",\"Age\":500,\"Birthday\":\"2011-11-11\",\"Sal\":8000,\"Skill\":\"牛魔拳\"}"
			  
			    //定义一个 Monster 实例
			    var monster Monster
			    // 不需要 make,因为 make 操作被封装到 Unmarshal 函数
			    err := json.Unmarshal([]byte(str), &monster)
			    if err != nil {
			      fmt.Printf("unmarshal err=%v\n", err)
			    }
			    str := "[{\"address\":\"北京\",\"age\":\"7\",\"name\":\"jack\"}," +
			    "{\"address\":[\"墨西哥\",\"夏威夷\"],\"age\":\"20\",\"name\":\"tom\"}]"
			  
			    //定义一个 slice
			    var slice []map[string]interface{}
			    //反序列化，不需要 make,因为 make 操作被封装到 Unmarshal 函数
			    err := json.Unmarshal([]byte(str), &slice)
			  
			  }
				// 反序列化 key-value
				func unmarshalMap() {
					str := "{\"address\":\"洪崖洞\",\"age\":30,\"name\":\"红孩儿\"}"
					
					//定义一个 map
					var a map[string]interface{}
					
					//反序列化
					//注意：反序列化 map,不需要 make,因为 make 操作被封装到 Unmarshal 函数
					err := json.Unmarshal([]byte(str), &a)
					if err != nil {
							fmt.Printf("unmarshal err=%v\n", err)
					}
					fmt.Printf("反序列化后 a=%v\n", a)
				}
			  
			  // RFC 4648规范约定标准Base64对任何二进制数据使用US-ASCII字符子集进行编码，字符包括：A-Z, a-z, 0-9, +, and / 。
			  func hmacSha256(data string, secret string) string {
			    h := hmac.New(sha256.New, []byte(secret))
			    h.Write([]byte(data))
			    // 标准编码
			    // Base64 标准方式编码解码
			    // URLEncoding(url编码)
			    // RawURLEncoding
			    //  RFC 4648 规范实现了 base64 编码解码功能, 包括标准方式以及 URL / 文件名称安全方式编码。本文通过示例演示如何编码和解码。
			    return hex.EncodeToString(h.Sum(nil))
			  }
			  
			  // 日期格式化
			  func parseTime() {
			    t := time.Now()   //2019-07-31 13:55:21.3410012 +0800 CST m=+0.006015601
			    fmt.Println(t.Format("20060102150405"))
			  
			    //当前时间戳
			    t1 := time.Now().Unix()  //1564552562
			    fmt.Println(t1)
			    //时间戳转化为具体时间
			    fmt.Println(time.Unix(t1, 0).String())
			  
			    //基本格式化的时间表示
			    fmt.Println(time.Now().String()) //2019-07-31 13:56:35.7766729 +0800 CST m=+0.005042501
			  
			    fmt.Println(time.Now().Format("2006-01-02"))  //2019-07-31
			    fmt.Println(time.Now().Format("2006-01-02 15:04:05"))  //2019-07-31 13:57:52
			  }
			  ```
## build

交叉编译

参数

GOOS=linux|windows|darwin(mac)|android|freebsd
GOARCH=amd64|arm|386
# 参考
- [go cmd](https://blog.csdn.net/wohu1104/article/details/106295007
- window下安装服务nssm
  ```bat
  nssm install uv-server-minitor D:\apps\uv-server-minitor.exe
  ```
- fyne golang gui
- https://www.jb51.net/article/187533.htm
- https://www.zhihu.com/question/27158146/answer/2474680292