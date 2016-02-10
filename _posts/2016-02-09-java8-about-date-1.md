---
layout: post
title: "Java8에서의 날짜 - 1/2"
categories: java
---

# Java8
Java는 8버전으로 업그레이드하면서 람다, 스트림 등 다양한 기능들이 추가되고 개선되었는데 새로운 날짜관련 API도 대폭 추가되었다. 이는 기존에 사용하던 Java가 내장한 날짜관련 API에 많은 문제가 있었기 때문이다. 이 포스트(1/2)에서는 이전까지 사용하던 날짜관련 API에 어떤 문제가 있었는지 간단히 살펴보고, 2번째(2/2)포스트에서 Java8에서 새롭게 추가된 날짜관련 API를 살펴볼 것이다.

# 기존 날짜관련 API의 문제점 - Date, Calendar

## 불변객체가 아니다
Java의 가장 큰 장점 중 하나는 멀티스레딩 프로그래밍을 손쉽게 구현할 수 있다는 것이다. 그런데 Calendar와 같은 기존의 날짜 API는 이러한 장점을 갈가먹는데 일조하고 있었다. 멀티스레딩 환경에서는 경쟁조건과 같은 이슈 때문에 객체의 변경가능성이 매우 중요한 요소가 된다. 객체의 정보를 변경하는 것이 불가능하다면 그 객체를 불변객체(immutable object)라고 한다(반대의 경우는 가변객체). Java에서 대표적인 불편 객체로는 String이 있다. 문자열을 다루는 String 객체는 값이 한번 정해지면 그 문자열 객체의 값을 변경할 수 없다. 값을 변경할 수 없는 불변객체는 멀티스레딩 환경에서 정보가 변경될 걱정이 없기 때문에 스레드세이프하다고 한다. 반면, Calendar 클래스는 가변객체이다. 즉, 스레드세이프하지 않다. 

    public class JavaDateTest {
        private SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd");
        
        @Test
        public void testMutableDate(){
            Calendar cal = Calendar.getInstance();
            assertEquals("2016-02-10", toString(cal));
            
            cal.set(2016, Calendar.OCTOBER, 30);
            assertEquals("2016-10-30", toString(cal));
        }
        
        String toString(Calendar cal){
            return df.format(cal.getTime());
            
        }
    }

위 코드처럼 Calendar 클래스는 언제 어디서든 그 상태가 변할 수 있다. 간단히 Calendar.getInstance() 메소드로 현재 시간를 가지고 있는 객체를 얻더라도 이 객체는 set()이라는 간단한 메소드에 의해 그 상태가 변할 수 있다. 이 객체가 여러 스레드에서 공유된다면 다른 현 스레드가 아닌 다른 스레드에도 잘못된 영향을 줄 수 있다.


## 지나친 상수 사용
Calendar 클래스는 날짜에 대한 정보를 표현하기 위해서 수 많은 int형 상수를 사용하고 있다.

    cal.get(Calendar.MONTH);
    cal.add(Calendar.SECOND, 10);
    
위 코드처럼 Calendar 클래스는 특정 날짜에 대한 정보를 얻거나 조작할때 int형 상수를 사용한다. 각 요일과 월에 대한 것들을 포함한 모든 것들이 int형 상수로 되어 있다보니 종종 혼란을 초래하는 경우가 생길 수 있다.

    cal.add(Calendar.OCTOBER, 2);       // OCTOBER는 날짜 단위가 아니다.
    cal.set(Calendar.JUNE, 2014, 1);    // 인자 순서가 바뀌었다.
                                        // 그럼에도 문제없이 실행된다.
    
add()메소드는 첫번째 인자로 증가하거나 감소할 대상이 되는 년, 월, 일, 초 등 단위를 나타내는 상수가 들어가야 하지만, 위 코드와 같이 엉뚱한 상수필드가 들어가더라도 Java는 이를 아무 문제 없는 코드로 인식한다. 또한, 모든 인자가 int형 상수이니 개발자의 실수로 인자의 순서가 바뀌더라도 아무 문제없이 동작할 것이다. 단지 버그가 발생할 뿐. 컴파일 단계에서 이러한 코드를 미리 잡아낼 수 없으며, 개발자는 버그의 원인을 찾기 위해 고생을 해야할 것이다.

## 0부터 시작하는 월
년과 일은 모두 우리가 일반적으로 생각하는 날짜와 동일하게 1부터 시작을 한다. 0년 0일은 없다. 그런데 Calendar에서는 0월이 있다. 정확히는 0이 1월을 나타낸다. 0부터 11까지를 월을 나나타내는데 사용하고 있다. Java에서 날짜를 담당하셨던 분도 이는 좋지 않게 생각하셨는지 Calendar에서는 각 요일을 나타내는 상수를 제공하지만 위 '지나친 상수 사용'에서의 경우처럼 별 도움은 안되고 있다.

## 일관성 없는 요일
날짜관련 기능이 Date와 Calendar로 나뉘어 있다보니 중복되는 기능도 있다. 그런데 기능과 역할이 동일한데도 불구하고 결과가 다른 경우가 있다. 

    cal.get(Calendar.DAY_OF_WEEK);  // Calendar 객체로 요일 얻기
    cal.getTime().getDay();         // Date 객체로 요일 얻기
    
Calendar 클래스의 getTime()메서드로 Date 객체를 얻을 수 있다. 두 클래스 모두 요일을 얻을 수 있는 메소드를 제공하는데, Calendar는 일요일이 1이고 Date는 일요일이 0이다. 서로 동일한 날짜를 나타내며 같은 요일을 표현하는데 다른 상수를 쓰고 있는 것이다.


# 다음글
- [Java8에서의 날짜 - 2/2](http://kwseo.github.io/java/2016/02/10/java8-about-date-2.html)


# References
- [네이버D2 - Java의 날짜와 시간 API](http://d2.naver.com/helloworld/645609)
- 이펙티브 자바 2판, 조슈아 블로크 저
- [Java SE 8 Date and Time](http://www.oracle.com/technetwork/articles/java/jf14-date-time-2125367.html)
