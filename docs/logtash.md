
## logtash是什么

## logtash能做什么

## 基本操作

```shell
# execute
logstash -f ./config/sqlserver.conf

```

## 分页查询

```
input {
  jdbc {
	  jdbc_connection_string => "jdbc:sqlserver://192.168.8.210:1433;DatabaseName=LTT_Distributors;"
	  jdbc_user => "sa"
	  jdbc_password => "Ltt1324."
	  jdbc_driver_library => "D:\logstash\connector\sqljdbc_8.4\chs\mssql-jdbc-8.4.1.jre11.jar"
	  jdbc_driver_class => "com.microsoft.sqlserver.jdbc.SQLServerDriver"
	  jdbc_paging_enabled => "true"
	  jdbc_page_size => "50"
	  statement => "SELECT * from OrderProducts"
          schedule => "* * * * *"
          lowercase_column_names => false
  }    
}
 
 
output {
  elasticsearch {
	  hosts => "127.0.0.1:9200" 
	  index => "order_products"
	  document_id => "%{OrderProductID}"
  }
  stdout {
      codec => json_lines
  }
  
}
```