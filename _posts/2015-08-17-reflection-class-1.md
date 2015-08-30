---
layout: post
title: "Java Reflection Class 1/2"
categories: Java Reflection
---

#Summary
Java는 클래스에 대한 정보를 가지고 있는 클래스가 존재하며, 이러한 클래스들을 리플렉션(reflection) 클래스라고 합니다. 이번 포스팅에서는 이 리플렉션 클래스에 대해서 간단히 살펴보고 어떤 클래스 객체던지 상관없이 가지고 있는 모든 멤버필드를 출력해주는 간단한 예제를 구현해봅니다. (Java8 기준)

#Reflection Class 소개
Java하면 가장 먼저 떠오르는 것은 클래스일 것입니다. Java에는 별별 클래스가 다 존재하는데 이러한 Java의 클래스 정보를 가지고 있는 클래스가 존재합니다. 클래스 정보를 가지고 있는 클래스이죠. 뭔가 모순처럼 보이지만... 아무튼 이런 클래스를 보고 Reflection 클래스라고 합니다. 이 Reflection 클래스를 사용하면 클래스의 정보를 얻을 수 있을 뿐만이 아니라 기본의 방법과는 색다르게 클래스를 다룰 수 있습니다. 예로들어 클래스 명을 인자로 받아 그 클래스의 객체를 생성한다거나, 임의의 문자열로 받은 메서드 이름을 통해 메서드를 호출한다거나, 멤버필드갑을 바꾼다거나 등의 기존과는 다른 방법으로 클래스를 사용할 수 있습니다.

#Class 클래스
Reflection 클래스의 가장 대표적인 클래스는 Class 클래스입니다. 이름이 참 특이합니다. class 아닙니다! 첫 문자가 대문자인 Class 입니다! 구두로 어떻게 말해야할지 모르겠네요. 클래스 클래스? Class class? 클래스 class? Class 클래스? 이 Class 클래스는 해당 클래스에 대한 정보를 가지고 있으며 멤버필드, 메서드에 대한 정보도 함께 가지고 있습니다.

##Field
멤버필드를 나타내는 클래스명은 Field입니다. 이처럼 Reflection 클래스들은 모두 저희가 사용하는 용어와 동일합니다. Class 클래스를 통해 getFields메서드나 getField메서드를 통해 얻을 수 있습니다. getFields메서드는 모든 필드를 반환하며, getField는 지정한 이름의 필드를 반환합니다. 그런데 getFields메서드는 지정한 클래스의 상위 클래스의 멤버필드까지 모두 가져오지만, 접근 가능한 필드만 가져옵니다. 즉 public으로 선언된 필드만을 반환합니다. 하지만 getDeclaredMethods메서드를 사용하면 public을 포함한 private, protected, package로 선언된 필드도 모두 얻을 수 있습니다. 그러나 이떄에는 상속받은 필드는 반환하지 않습니다.

	Field[] fields = classType.getFields();
	Field[] allFields = classType.getDeclaredFields();
	Field field = classType.getField(fieldName);
	

##Method
마찬가지로 메서드를 나타내는 클래스는 Method입니다. 이 역시 Class 클래스의 getMethods메서드나 getMethod메서드를 통해 얻을 수 있습니다. getMethods는 모든 메서드를 반환하며, getMethod메서드는 지정한 이름의 메서드를 반환합니다. 메서드 역시 getMethods메서드는 외부에서 접근 가능한 public 메서드만을 반환하며, private, protected, package로 선언된 메서드를 얻기 위해서는 getDeclaredMethods메서드를 사용해야한다. 하지만 getDeclaredMethods메서드는 상속받은 클래스를 반환하지 않습니다. 

	Method[] methods = classType.getMethods();
	Method[] allMethods = classType.getDeclaredMethods();
	Method method = classType.getMethod(methodName);


#Reflection 클래스를 얻는 3가지 방법

##ClassName.class
클래스명을 통해 직접 Class 클래스를 얻을 수 있습니다. 이때 반환되는 타입은 **Class<T>**입니다.

	Class<Integer> intClassType = Integer.class;
	Class<String> strClassType = String.class;
	
##objectName.getClass()
Object클래스의 메서드 중 getClass라는 메서드가 있습니다. 즉 존재하는 모든 클래스는 getClass메서드릁 통해 Class 클래스를 얻을 수 있습니다. 이 메서드는 해당 객체의 클래스 타입을 반환합니다. 이때 반환되는 타입은 **Class<?>**입니다.

		String str = "101010Test";
		Class<?> strTestClassType = str.getClass();
		Class<String> strTest2ClassType = (Class<String>)str.getClass();
		
##Class.forName("className")
Class 클래스는 forName이라는 정적 메서드를 가지고 있습니다. 메서드의 파라미터를 전달한 클래스명의 클래스타입을 반환합니다. 파라미터로 전달하는 클래스이름은 페키지명까지 전부 입력해야 합니다. 이때 반환되는 타입은 **Class<?>**입니다.

		Class<?> forNameClassType = Class.forName("java.lang.Integer");


#Reflection 클래스로 새로운 객체 생성
##생성자에 파라미터가 없는 경우
선택학 클래스의 생성자에 파라미터가 없는 경우 아래의 코드 처럼 간단하게 새로운 객체를 생성할 수 있습니다. 이때 생성되는 객체의 타입은 어떤 클래스의 Class클래스라도 Object 객체를 반환하이므로 상황에 따라 적절한 캐스팅이 필요합니다.
	
	Class<	Person> personType = Person.class;
	Person person = (Person)personType.newInstance();
	
##생성자에 파라미터가 있는 경우
위의 코드는 파라미터가 없는 생성자가 있는 경우에만 사용할 수 있습니다. 만일 모든 생성자가 파라미터를 필요로할 경우 조금더 다른 방법을 사용해야합니다. 아래의 생성자를 가진 클래스가 있다고 가정하겠습니다.

	public class Book{
		private String name;
		private int count;
		
		public Book(String name){
			this(name, 0);
		}
		
		public Book(String name, int count){
			this.name = name;
			this.count = count;
		}
		
		...code...
	}
	
Book 클래스를 생성하기 위해서는 파라미터가 필요합니다. 거기다 2개의 생성자를 가지고 있습니다. Relection 클래스로 이 클래스의 객체를 생성하기 위해서는 Constructor 클래스를 활용할 필요가 있습니다. Class 클래스는 getConstructors메서드와 getConstructor메서드를 제공합니다.(Field나 Method와 마찬가지로 getDeclaredConstructors메서드도 존재)

		Class<Book> bookClassType = Book.class;
		Constructor constructor1 = bookClassType.getConstructor(String.class, int.class);
		Constructor constructor2 = bookClassType.getConstructor(String.class);
		Book kingBook = (Book)constructor1.newInstance("King", 10);
		Book QueenBook = (Book)constructor2.newInstance("Queen");

위처럼 파라미터의 클래스타입을 통해 생성자를 검색해서 얻을 수 있으며, 얻은 생성자를 통해 객체를 생성할 수 있습니다.

##Primitive 타입과 Wrapper 클래스
Java 언어를 공부하는 사용하는 사람이라면 알고있다시피 Java는 Primitive(원시) 타입과 Wrapper 클래스가 있습니다. 원시타입은 int, float, double 등과 같이 가장 기본적인 데이터 자료형을 말하며, Wrapper 클래스는 Integer, Float, Double 등과 같이 원시타입를 클래스화한 것입니다. 아래의 코드처럼 일반적으로 원시타입과 Wrapper타입은 기능을 제외하면 거의 동알한 타입으로 다루어집니다. 그리고 신기하게도 원시타입은 클래스가 아님에도 클래스타입을 가지고 있습니다. 하지만 Class<int>는 사용할 수 없습니다. 제네릭은 원시타입을 지원하지 않으니까요. 그래서 아래의 코드처럼 Class<Integer>로서 받아야합니다.

		Integer a = 10;
		int b = new Integer(20);
		Class<Integer> intClassType = int.class;

그런데 Reflection 클래스에서 이 둘은 확실하게 구분되어있습니다. 즉 int의 클래스타입과 Integer 클래스타입은 서로 다르다는 것입니다.

		Class<Integer> integerType = Integer.class;
		Class<Integer> intType = int.class;
		System.out.println("결과값 : " + (integerType.hashCode() == intType.hashCode()));
		
		결과값 : false
		
위 결과처럼 int.class와 Integer.class는 서로 다른 클래스타입입니다. 코딩시 이러한 점을 주의할 필요가 있습니다. Integer와 같은 Wrapper 클래스는 TYPE이라는 정적 필드를 가지고 있습니다. 이것이 원시타입과 같은 Class 클래스입니다. 

		Class<Integer> integerType = Integer.TYPE;
		Class<Integer> intType = int.class;
		System.out.println("결과값 : " + (integerType.hashCode() == intType.hashCode()));
		
		결과값 : true

		