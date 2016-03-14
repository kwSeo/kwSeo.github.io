---
layout: post
title: "Mybatis plugin"
categories: java db mybatis
---

# Mybatis Plugin

Mybatis3의 XML설정 파일을 보면 `<plugins>`이라는 태그가 존재한다. 이 태그는 무슨 기능을 하는 것일까? 지금 알아보고자 한다.

# AOP

AOP(Aspect-Oriented Programming)이란 관점 지향 프로그래밍이라고 불리며 소프트웨어 개발에서 메인 비지니스 로직으로부터 부가적인 보조기능들을 분리하는 프로그램 기법이다. Spring 프레임워크에서도 매우 강조하고 있는 기법으로, 소프트웨어를 개발하며 공부하는 사람이라면 한번 쯤은 들어봤을 것이다. 하나의 로직을 다양한 관점에서 흘러갈 수 있도록 하는 이 기법은 Mybatis에서 직접 지원하지는 않지만 비슷한 기능은 제공한다. 바로 플러그인이다.

# Mybatis3 플러그인 기능

Mybatis3의 XML설정파일을 보면 `<plugins>`라는 태그가 존재한다. 이 태그를 통해 Mybatis를 확장할 수 있다. 그런데 이렇게 보기에 좋아보이는 기능이 Mybatis API 문서에서는 설명하고 있지 않다. 주석이 텅 비어있다. 그래서 Mybatis 플러그인 기능을 사용한 어느 한 오픈소스 프로젝트를 참고하면서 나름 사용법을 알아보았다.
Mybatis 플러그인 기능은 AOP와 비슷하게 동작한다. 특정 인터페이스의 메소드를 지정하면 그 인터페이스의 메소드가 실행되지 직전에 플러그인으로 등록한 클래스가 호출된다. AOP처럼 특정 메소드를 언제 어디서 어떻게 호출할지를 정할 수 있으며 수행하기 전과 후에 부가적인 기능을 넣을 수 있다.


## 예제 코드

### Mybatis 테스트

```
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

public class MybatisPluginTest {
    private SqlSessionFactory factory;
    
    @Before
    public void setup() throws Exception {
        factory = new SqlSessionFactoryBuilder().build(Resources.getResourceAsStream("mybatis-config.xml"));
    }
    
    @Test
    public void test() {
        try(SqlSession sqlSession = factory.openSession()) {
            SampleMapper sampleMapper = sqlSession.getMapper(SampleMapper.class);
            
            List<String> list = sampleMapper.selectSamples();
            
            System.out.println(list);
        }
    }
}
```

위 코드는 그저 Mybatis 플러그인을 직접 눈으로 확인해보기 위한 테스트(테스트라 말하기 부끄러운) 코드이다. 그저 SqlSession을 생성하고 선언한 메퍼 인터페이스를 통해 샘플 텍스트 리스트를 불러오고 출력하고 있다. 만일 샘플 데이터 `'가', '나', '다'`가 저장되어있다면 최종적으로 `[가, 나, 다]`를 출력할 것이다. 하지만,

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	...  
  
  <plugins>
    <plugin interceptor="test.mybatis.MybatisPlugin"></plugin>
  </plugins>
  
  <environment id="development">
    ...
  </environments>
  
  <mappers>
    ...
  </mappers>
</configuration>
```
위 코드와 같은 플러그인을 사용한다면 결과는 아래와 같다.
```
start intercept
end intercept
[가,나,다]
```

`[가,나,다]` 뿐만이 아니라 두 문장이 너 출력되었다. 어디서 출력된 것일까? 당연히 플러그인이다.


### Mybatis 플러그인

```
package test.mybatis;

...

@Intercepts({
    @Signature(type = Executor.class, method = "update", args = {MappedStatement.class, Object.class}),
    @Signature(type = Executor.class, method = "query", args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class})
})
public class MybatisPlugin implements Interceptor {

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        System.out.println("start intercept");
        
        Object result = invocation.proceed();
        
        System.out.println("end intercept");
        
        return result;
    }

    @Override
    public Object plugin(Object target) {
        return Plugin.wrap(target, this);
    }

    @Override
    public void setProperties(Properties properties) {
        // do nothing
    }
}
```


### 애노테이션들

위 코드는 Mybatis의 플러그인으로 사용하기 위한 코드이다. 가장 먼저 눈에 들어오는 것은 `@Intercepts`이다. 이 애노테이션은 해당 클래스가 Mybatis의 플러그인을 뜻하는 애노테이션으로 내부적으로 다시 `@Signature` 애노테이션을 가지고 있다. `@Signature`는 이 플러그인과 함께 수행될 인터페이스 메소드를 지정하는 애노테이션이다. 위 예제에서는 인터페이스 타입을 `Executor`라는 인터페이스를 지정하였다. 메서드는 `update` 메서드로 지정하였고 파라미터는 `MappedStatement`와 `Object`타입의 파라미터를 받는 메소드를 지정했다. 아래의 `@Signature`도 같은 인터페이스로 비슷하다. 이제 구현된 플러그인은 
`@Signature`로 지정한 메소드를 호출할때 함께 이 플러그인이 호출될 것이다. Aspactj나 Spring AOP, 또는 Java의 다이나믹프록시에 대해 안다면 `invocation`객체와 `proceed`메서드가 반가울 것이다. 모두 이름과 같은 기능을 수행한다. `@Signature`애노테이션을 선언할때 느꼈을지도 모르겠는데 Mybatis 플러그인은 Mybatis의 내부 구조를 알면 알수록 더 많이 활용할 수 있다. 위에서 사용된 `Executor` 인터페이스는 준비된 SQL과 SQL파라미터를 바탕으로 이제 커넥션을 연결해 실제로 데이터베이스에 쿼리를 수행한다. 플러그인을 사용하면 `Executor` 인터페이스 외에도 다른 인터페이스에도 기능을 확장할 수 있다.



### Interceptor 인터페이스

플러그인을 개발하기 위해서는 `Interceptor` 인터페이스를 구현해야한다. 이 인터페이스는 세 가지 메소드를 가지고 있다. 첫 번째 `intercept`메소드는 타깃이 되는 메소드의 바로 아래에서 실행되는 메소드이다. 파라미터로 전달받는 `Invocation` 객체는 다음으로 호출될 메소드의 정보를 가지고 있으며 `proceed`메소드를 통해 호출할 수 있다. 여기서는 `Executor`인터페이스를 구현하고 있는 Mybatis 내부의 클래스 메소드 일 것이다. `proceed`메소드의 반환값은 실제 타깃 메소드의 반환값이다. 두 번째는 `plugin`메소드로 플러그인을 등록하는 메소드이다. 이때 파라미터로 전달받는 `target`객체는 실제 다깃이 되는 대상으로 간단히 `Plugin.wrap(target, this)`를 통해 현재 플러그인과 연결해 줄 수 있다. 마지막으로 `setProperties`는 해당 플러그인에 지정한 프로퍼티를 받아서 처리할 수 있다. 프로퍼티를 등록하는 방법은 아래와 같다.

```
    ...
    <plugin interceptor="test.mybatis.MybatisPlugin">
      <property name="key1" value="value1"/>
      <property name="key2" value="value2"/>
    </plugin>
    ...
```