# spring-mysql-redis-cache
Spring Boot Mysql Redis  REST API Cache example

##
###  注意点
```
这里对mysql的地址没有什么限制, 但是对redis有一定的限制,配置文件和代码里面的要一样
配置文件里面的
src/main/resources/application.yml

代码里面的
src/main/java/com/jeonguk/web/config/RedisConfig.java
connectionFactory.setHostName("redis");
```

##
### Prerequisites
- JDK 1.8
- Maven
- Mysql
- Redis

## Quick Start

### Clone source
```
git clone https://github.com/jeonguk/spring-mysql-redis-cache.git
cd spring-mysql-redis-cache
```

```
MySQL START
```

```
Redis START
```

### Build
```
mvn clean package
```

### Run
```
java -jar target/spring-mysql-redis-cache.jar
```

##
### Get information about system health, configurations, etc.
```
http://localhost:8091/env
http://localhost:8091/health
http://localhost:8091/info
http://localhost:8091/metrics
```

##
### TEST using CURL

- data add
```
curl -d '{"title":"post title", "content":"post content"}' -H "Content-Type: application/json" -X POST http://localhost:8080/api/posts
```

- data get cache TimeUnit.SECONDS 10
```
curl http://localhost:8080/api/posts/1
```

##
### Swagger-ui REST API Reference & Test
- http://localhost:8080/swagger-ui.html
- Response Content Type : application/json

##
### Redis monitor
- https://redis.io/commands/monitor
