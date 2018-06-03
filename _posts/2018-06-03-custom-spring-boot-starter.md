---
layout: post
title: "Custom Spring boot starter"
categories: java spring
---

# Custom Spring boot starter

## spring.factories

- Auto Configuration과 관련된 설정
    - enable 어노테이션이랑 설정 클래스들을 매핑
- 스프링 부트 앱은 실행시에 클래스패스 상에서 spring.factories 파일들을 찾아서 읽어드림
- 아래의 코드는 spring-boot-autoconfigure 프로젝트의 spring.factories파일 내용의 일부

```
# resources/META-INF/spring.factories
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration
```

- `@EnableAutoConfigurtaion`에 몇몇 설정 클래스들이 매핑되어 있음
    -  위 어노테이션이 사용되면 매핑되어있는 클래스들의 빈이 생성되고 동작할 것임

## Configuration Class

```java
@Configuration
@ConditionalOnClass(MongoClient.class)
@EnableConfigurationProperties(MongoProperties.class)
@ConditionalOnMissingBean(type = "org.springframework.data.mongodb.MongoDbFactory")
public class MongoAutoConfiguration {
    // configuration code
}
```

```java
@ConfigurationProperties(prefix = ... )
class MongoProperties {
    // properties...
}
```

- `@ConfigurationProperties`
- `@EnableConfigurationProperties`
- `@ConditionalOnClass`
- `@ConditionalOnMissingClass`
- `@ConditionalOnBean`
- `@ConditionalOnMissingBean`
- `@Conditional...`

## Main 클래스 이슈

- spring-boot-maven-plugin은 main 클래스를 요구함. 그러나 starter 프로젝트는 메인 클래스가 필요없지, 없어야하지 않나?
    - starter를 만들때에는 spring-boot-maven-plugin이 필요없음
    - 테스트할 때에는 간단히 `mvn clean install`로 local maven repo에 설치해서 사용해보자

## spring.factories를 읽지 못하는 문제

- `{classpath}/META-INF/spring.factories`에 위치해야함

## Custom Starter Naming Comvention

- 스프링 코어 팀에서 관린되는 starter가 아니라면 `{library-name}-spring-boot-starter` 같은 식으로 starter라는 이름이 라이브러리 이름 뒤에 붙어야 한다.
