---
layout: post
title: "Java Reflection Class 2/2"
categories: Java Reflection
---

#Summary
Java는 클래스에 대한 정보를 가지고 있는 클래스가 존재하며, 이러한 클래스들을 리플렉션(reflection) 클래스라고 합니다. 이번 포스팅에서는 이 리플렉션 클래스에 대해서 간단히 살펴보고 어떤 클래스 객체던지 상관없이 가지고 있는 모든 멤버필드를 출력해주는 간단한 예제를 구현해봅니다. (Java8 기준)

#Method
##Method invoke
Method 클래스를 이용하면 해당 Method 클래스가 의미하는 메서드를 호출할 수 있습니다. 이때 당연히 메서드가 어떤 인스턴스의 메서드인지를 알기 위해서 인스턴스를 매개변수로 필요로하며 호출하고자하는 메서드의 매개변수를 정의한 순서대로 전달해야합니다. 이러한 역할을 하는 것이 Method 클래스의 invoke 메서드입니다. invoke 메서드는 앞에서 말한 것들을 인자로 필요로하며 메서드가 반환값이 있다면 Object 타입으로 반환합니다.

	Person person = new Person("Park", 24);
	Class<?> classType = person.getClass();
	Method method = classType.getMethod("setName", String.class);
	method.invoke(person, "Seo");
	
#Field
##필드 값 접근
Spring 프레임워크를 아신다면 @Autowired라는 어노테이션에 대해서 들어보셨을 겁니다. @Autowired 어노테이션을 사용하면 하댕 클래스 객체가 protected든, private이든 상관없이 객체를 주입할 수 있는 것을 볼 수 있습니다. 이처럼 클래스 각체의 멤버필드에 임의로 접근할 수 있는 방법이 존재합니다. Relfection 클래스 중 하나인 Field 클래스를 사용하면 됩니다. Class 클래스의 getField 또는 getFields 등의 메서드를 사용하여 해당 클래스의 Field들을 얻을 수 있습니다. Field 클래스의 set 메서드를 사용하면 Field의 값을 변경할 수 있으며, 또한 get 메서드를 사용하면 값을 얻을 수 있습니다. 이때에도 역시 어느 인스턴스의 필드인지를 알기 위해 해당 인스턴스를 인자로 필요로 합니다.

	Field field = classType.getField("name");
	field.set(person, "Kim");
	String name = (String)field.get(person);
 
그런데 이때 접근지정자에 따라서 필드에 접근이 불가능할 수도 있습니다. 예로들면 private 필드의 경우가 있죠. private 필드는 외부에서 접근할 수 없습니다. 그렇기 때문에 위 코드는 name 필드가 private을 선언되었다면 에러가 발생할 것입니다. 그런데 어떻게 Spring 프레임워크의 @Autowired 어노테이션은 객체를 주입할 수 있는 것일 까요? 이유는 Field 클래스에는 접근을 제어하는 부분이 포함되어 있기 때문입니다. Field 클래스의 특정 메서드를 사용하면 접근 지정자에 상관없이 접근할 수 있습니다. 그 메서드의 이름은 setAccessible입니다.

	Field field = classType.getDeclaredField("name");
	field.setAccessible(true);
	field.set(person, "Kim");
	field.get(person);
	field.setAccessible(false);


#객체 출력 모듈
이제 지금까지 공부한 내용을 바탕으로 해당 객체의 모든 필드와 필드 값을 문자열로 반환해주는 모듈을 개발할 것입니다. 지금까지의 내용을 숙지했다면 구현은 간단합니다.

	class StringUtil{
		public static String toString(Object obj){
			Class<?> classType = obj.getClass();
			Field[] fields = classType.getDeclaredFields();
			
			StringBuilder builder = new StringBuilder();
			
			try{
				for(Field field : fields){
					field.setAccessible(true);
					String name = field.getName();
					Object value = field.get(obj);
					field.setAccessible(false);
					
					builder.append(name)
						.append(" : ")
						.append(value)
						.append(", ");
				}
				
				return builder.substring(0, builder.length()-2);
				
			}catch(Exception e){
				e.printStackTrace(System.err);
				return null;
			}
		}
	}
	
위 코드는 전달받은 객체의 모든 필드(상속받은 것 제외) 및 필드 값을 문자열로 만들어 반환해 줍니다. 간단히 모든 getDeclearedFields 메서드를 통해 모든 Field를 얻어서 필드명과 값을 가져오고 있습니다. 얻은 값은 StringBuilder를 통해 문자열로 만들어 반환하고 있죠. Field 클래스의 get메서드를 사용할때 예외를 처리해야 하기에 try-catch 구분을 사용해서 처리하고 있습니다. 이래는 사용하는 코드입니다.

		Person person = new Book("Kim", 25);
		System.out.println(StringUtil.toString(book));
		
**결과값**
		
		name : Kim, age : 25
		
---

#[Java Reflection Class 1/2](http://kwseo.github.io/java/reflection/2015/08/17/reflection-class-1.html)		