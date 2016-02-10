---
layout: post
title: "Java8에서의 날짜 - 2/2"
categories: java
---

# Java8
Java8에는 JSR-310이라는 표존명세로 새로운 날짜와 시간관련 API가 존재한다. 기존의 Date, Calendar 등을 대체하기 위해서 지금까지 많이 사용되던 Java 날짜 관련 라이브러리를 참고하여 보다 효율적이고 사용하기 편한 API를 내놓았다.

# java.time 패키지
java.time 패키지는 이번 Java8과 함께 등장한 패키지로 새로운 날짜 및 시간관련 API를 제공한다. 기존의 Date, Calendar가 가지고 있던 문제점을 해결하여 보다 효율적이게 날짜와 시간을 다룰 수 있게 돕는다. 즉, java.time이 제공하는 클래스 객체는 불변객체이며, 이전처럼 int형 상수를 통해 요일 및 월을 구분하지 않는다. 월, 요일 등은 Enum 객체로서 다루어지기 떄문에 보다 명확하고 알 수 있으며 사용자의 실수를 예방할 수 있다. 또한, new 같은 키워드를 통해 생성자를 사용하는 것이 아닌 static factory메소드를 통해 객체를 생성한다. 이펙티브 자바에서 권장하듯 new 키워드보다는 팩토리메서드를 지향하고 있다. 그리고 잘못된 필드, 월, 요일이 지정되는 경우도 검증을 하고 있어 만일 잘못된 정보를 입력하는 경우에는 예외처리를 해준다.

## Core Ideas
Java8의 날짜 및 시간 API의 핵심 아이디어는 아래의 3가지라고 한다.

### Immutable-value classes
기존 API의 가장 큰 문제점은 제공되는 객체가 가변객체라 스레드로부터 안전하지 않다는 것이었다. 새로운 API는 모든 핵심 클래스를 분변객체로 제공한다.

### Domain-driven design
도메인 중심의 모델은 개발자에게 보다 명확하고 이해하기 쉬운 API를 제공한다.

### Separation of chronologies
새로운 API는 ISO-8601을 필수적으로 따를 필요가 없는 일본이나 태국같이 서로다른 날짜 시스템을 사용하는 나라에서도 문제없이 동작할 수 있도록 해준다. 이러한 점은 개발자의 부담을 덜어줄 것이다.


## 대표적인 클래스

{: .table .table-striped .table-bordered}
이름 | 설명
---|---
LocalDate | 날짜만을 표현하는 클래스(년, 월, 일 등) 시간 제외.
LocalTime | 시간만을 표현하는 클래스(시, 분, 초 등) 날짜 제외.
LocalDateTime | 날짜와 시간을 모두 표현하는 클래스
ZonedDateTime | 타인존이 지정된 날짜와 시간을 표현하는 클래스.

## 날짜 및 시간 객체 생성
java.time에서 제공하는 클래스는 모두 팩토리메서드 방식으로 객체를 생성하며, 그 방법이 동일하다.

	// 현재 날짜 및 시간 객체 생성
	LocalDate today = LocalDate.now();
	LocalDateTime now = LocalDateTime.now();
	LocalTime currentTime = LocalTime.now();

	// 특정 날짜 및 시간 갱체 생성
	LocalDate yesterday = LocalDate.of(2016, 2, 9);						// 2016/02/09
	LocalTime thatTime = LocalTime.of(18, 30, 34);						// 18:30:34
	LocalDateTime theDay = LocalDateTime.of(yesterday, thatTime);		// 날짜와 시간으로 생성
	LocalDateTime superDay = LocalDateTime.of(2016, 2, 5, 15, 0, 0);	// 2016/02/05 15:00:00
	
	// 날짜 및 시간을 파싱해서 생성
	LocalDate thatDay = LocalDate.parse("2016-01-04");
	LocalTime goHomeTime = LocalTime.parse("18:30:00");
	LocalDateTime dayAndTime = LocalDateTime.parse("2016-01-04T18:30:00");	// 날짜와 시간을 T로 구분
	// 기존 객체를 재활용하여 새로운 객체 생성
	LocalDate localDate = today.withDayOfMonth(5);

## 데이터 얻기

	LocalDateTime now = LocalDateTime.parse("2016-01-31T12:30:58");
	
	assertEquals(2016, now.getYear());
	assertEquals(1, now.getMonthValue());
	assertEquals(31, now.getDayOfMonth());
	assertEquals(12, now.getHour());
	assertEquals(30, now.getMinute());
	assertEquals(58, now.getSecond());

## 포맷

	DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd hh:mm:ss");
	LocalDateTime localDateTime = LocalDateTime.of(2016, 1, 1, 7, 30, 24);
	assertEquals("2016/01/01 07:30:24", formatter.format(localDateTime));

## 연산

	LocalDateTime theDay = LocalDateTime.parse("2016-01-31T12:30:58");
	assertEquals(58, theDay.getSecond());
	
	// 10초 후 얻기
	LocalDateTime after10Seconds = theDay.plusSeconds(10);
	assertEquals(8, after10Seconds.getSecond());
	
	// 10일 후 얻기
	LocalDateTime after10Days = theDay.plusDays(10);
	assertEquals(2, after10Days.getMonthValue());
	assertEquals(10, after10Days.getDayOfMonth());
	
	// 10년 전 얻기
	LocalDateTime before10Years = theDay.minusYears(10);
	assertEquals(2006, before10Years.getYear());

## 특정 주기 지정

	// 5년, 2달, 10일
	Period period = Period.of(5, 2, 10);
	LocalDate theDate = LocalDate.of(2016, 10, 20);
	LocalDate afterDate = theDate.plus(period);
	assertEquals(2021, afterDate.getYear());
	assertEquals(12, afterDate.getMonthValue());
	assertEquals(30, afterDate.getDayOfMonth());
	
## 날짜 간격, 기간

	LocalDateTime today = LocalDateTime.now();
	LocalDateTime yesterday = today.minusDays(1);
	Duration durationOfDays = Duration.between(yesterday, today);
	assertEquals(24, durationOfDays.toHours());
	
	
# Java7을 위한 백포트
Java8에서 추가된 날짜 및 시간 API를 이전 버전에서도 사용할 수 있도록 백포트를 제공한다. 백포트는 상위버전의 기능을 하위버전에 반영해 주는 것을 의미한다.

	<dependency>
	    <groupId>org.threeten</groupId>
	    <artifactId>threetenbp</artifactId>
	    <version>1.3.1</version>
	</dependency>


# References
- [네이버D2 - Java의 날짜와 시간 API](http://d2.naver.com/helloworld/645609)
- 이펙티브 자바 2판, 조슈아 블로크 저
- [Java SE 8 Date and Time](http://www.oracle.com/technetwork/articles/java/jf14-date-time-2125367.html)
