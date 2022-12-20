
```shell
killall java

nohup java -jar  -Xms2g -Xmx2g  /opt/bcp-app/api_service/app.jar -Dspring.config.location=/opt/bcp-app/api_service/config/application-prod.yml  > logs/catalina.out &

```