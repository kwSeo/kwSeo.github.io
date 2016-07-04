---
layout: post
title: "정규 표현식 in Java"
categories: java ragex
---

# 정규 표현식(Regular Expression)

정규 표현식은 특정한 문자열 집합을 표현하기 위해서 사용하는 표현식이다. 정규 표현식을 통해 구체적으로 정해진 문자열이 아닌 특정한 패턴을 따르는 문자열을 포현할 수 있다. 이를 이용하면 특정 패턴에 맞는 문자열을 찾거나 수정하는 작업을 보다 간편하게 수행할 수 있다.

## 문법

정규 표현식은 다양한 문자와 특수문자로 문자 패턴을 표현한다. 약자로 쓰기 때문에 정규 표현식을 모른다면 읽기 힘들다. 하지만 알고 있다면 이보다 문자열의 패턴을 찾는데 이보다 유용한 것은 없을 것이다. Java 역시 정규표현식을 지원하고 있다. 여기에서는 Java의 Pattern 클래스에 맞는 정규표현식의 문법을 살펴볼 것이다. 정규 표현식은 언어마다 문법이 조금씩 다른데 큰틀을 동일하기 때문에 크게 걱정하지 않아도 된다.

### Characters

{: .table .table-striped .table-bordered}
표현식 | 의미
---|---
x | 문자 x
\\ | 백슬래시(\)
\0n | 8진수 n (0 <= n <= 7)
\xh | 16진수 h
\t | 탭 문자
\n | 새 줄(line feed)
\r | 캐리지 리턴

### Character classes

{: .table .table-striped .table-bordered}
표현식 | 의미
---|---
[abc] | a 또는 b 또는 c
[^abc] | a 또는 b 또는 c를 제외한 모든 것
[a-zA-Z] | a부터 z까지 또는 A부터 Z까지
[a-z&&[def]] | d 또는 e 또는 f(교집합, intersection)
(x) | 문자 x(그룹)

### Predefined character classes

{: .table .table-striped .table-bordered}
표현식 | 의미
---|---
. | 모든 것(문자, 숫자, 특수문자, 공백 등등)
\d | 숫자
\D | 숫자가 아닌 것
\s | 모든 공백(탭 문자 등 포함)
\S | 공백이 아닌 것
\w | 문자
\W | 문자가 아닌 것

### POSIX Character classes(US-ASCII only)

{: .table .table-striped .table-bordered}
표현식 | 의미
---|---
\p{Lower} | 소문자
\p{Upper} | 대분자
\p{ASCII} | 모든 ASCII 문자
\p{Alpha} | 문자
\p{Digit} | 숫자
\p{Alnum} | 문자와 숫자
\p{Punct} | 특수 문자
\p{Graph} | 문자와 숫자와 특수 문자
\p{Blank} | space 또는 tab
\p{Space} | 모든 공백
\p{XDigit} | 16진수

### Boundary matchers

{: .table .table-striped .table-bordered}
표현식 | 의미
---|---
^x | x로 시작
x$ | x로 끝

### Greedy quantifiers

{: .table .table-striped .table-bordered}
표현식 | 의미
---|---
x? | x가 0번 또는 1번 나타난다.
x* | x가 0번 이상 나타난다.
X+ | x가 1번 이상 나타난다.
x{n} | x가 n번 나타난다.
x{n,} | x가 최소 n번 이상 나타난다.
x{n,m} | x가 n번 이상 m번 이하로 나타난다.

## 사용하기

### String 클래스의 메서드

Java에서 기본적으로 제공하는 String 클래스에서는 정규 표현식을 사용할 수 있는 메서드들이 존재한다. 그 중 하나인  metches 메서드와 split 메서드를 소개하겠다.

#### metches 메서드

metches 메서드는 해당 문자열이 지정한 정규 표현식 패턴에 맞는지 확인하는 메서드이다. 패턴이 일치한다면 true를 일치하지 않는다면 false를 반환한다.


    String a1 = "ASKDAKDKASDKSADASDKWDKAWKD";
    System.out.println(a1.matches("\\w+"));
    String a2 = "!@!#!@";
    System.out.println(a2.matches("\\p{Punct}+"));
    String a3 = "asdasdasdasd!@!#!@ASDAS";
    System.out.println(a3.matches("^\\w+\\p{Punct}+\\w+"));
    System.out.println(a1.matches("^\\w+\\p{Punct}+\\w+"));
    
    
**출력**

```        
    true
    true
    true
    false
```
    
#### split 메서드

split 메서드는 지정한 정규 표현식에 일치하는 부분을 기준으로 문자열을 나누는 메서드이다. 리턴 값으로 해당 문자열이 분할되어 저장된 문자열 배열을 반환한다.


    String b = "ABC DEFG12abcabc12A SDADS";
    String[] bArr = b.split("[\\s\\d]+");
    System.out.println(Arrays.toString(bArr));

    
**출력**

```
    [ABC, DEFG, abcabc, A, SDADS]
```

## Pattern 클래스와 Metcher 클래스

정규 표현식을 위한 클래스로 Java에서 기본적으로 제공하는 Pattern 클래스와 Metcher클래스가 있다.  Pattern 클래스는 실질적으로 정규 표현식을 나타내는 클래스으며, Metcher 클래스는 정규 표현식 사용한 결과라고 할 수 있다. 아래와 같은 방법으로 사용할 수 있다.


    Pattern p = Pattern.compile("[^\\w+\\d$]");
    Matcher m = p.matcher("akdawkdawkdakwd123123");
    System.out.println(m.matches()); 


**출력**

```
    true
```
