### java.lang 패키지

---



#### Object 클래스

---

Object 클래스는 멤버변수는 없고 오직 11개의 메서드만 가지고 있다. 

이 메서드들은 모든 인스턴스가 가져야할 기본적인 것들이다.



##### equals(Object obj)

---

매개변수로 객체의 참조변수를 받아서 비교하여 그 결과를 boolean 값으로 알려 주는 역활을 한다.

```java
public boolean equals (Object obj) {
	return (this === obj)
}
```

두 객체의 같고 다름을 참조변수의 값으로 판단한다.

그렇기에 서로 다른 두 객체를 equals 메서드로 비교하면 항상 false를 결과로 얻는다.



Object 클래스로부터 상속받는 equals 메서드는 결국 두 개의 참조변수가 같은 객체를 참조하고 있는지, 즉 두 참조변수에 저장된 값(주소값)이 같은지를 판단하는 기능밖에 할 수 없다.

그렇지만 equals 메서드를 오버라이딩하여 주소가 아닌 객체에 저장된 내용을 비교하도록 변경할 수 있다.

```java
class Person {
	long id;

	public boolean equals(Object obj) {
		if(obj!=null && obj instanceof Person) {
			return id ==((Person)obj).id;
		} else {
			return false;
		}
	}

	Person(long id) {
		this.id = id;
	}
}
```



String 클래스 역시 Object 클래스의 equals 메서드를 그대로 사용하는 것이 아니라 이처럼 오버라이딩을 통해서 String 인스턴스가 갖는 문자열 값을 비교하도록 되어있다. 

그렇기에 같은 내용의 문자열을 갖는 두 String 인스턴스에 equals 메서드를 사용하면 항상 true 값을 얻는다.



String 클래스뿐만 아니라, Date, File, wrapper 클래스 등의 equalas 메서드도 주소값이 아닌 내용을 비교하도록 오버라이딩되어 있다.

그러나 의외로 StringBuffer 클래스는 오버라이딩되어 있지 않다.



##### .hashCode()

---

이 메서드는 해싱기법에 사용되는 해시함수를 구현한 것이다.

해싱은 데이터관리기법 중의 하나인데 다량의 데이터를 저장하고 검색하는 데 유용하다.

해시함수는 찾고자하는 값을 입력하면, 그 값이 저장된 위치를 알려주는 해시코드를 반환한다.

일반적으로 해시코드가 같은 두 객체가 존재하는 것이 가능하지만, Object 클래스에 정의된 hashCode 메서드는 객체의 주소값을 이용해서 해시코드를 만들어 반환하기 때문에 서로 다른 두 객체는 결코 같은 해시코드를 가질 수 없다.



그렇기에 hashCode 메서드도 equals 메서드와 같이 클래스의 인스턴스변수 값으로 객체의 같고 다름을 판단해야하는 경우라면 적절히 오버라이딩해야 한다.



String 클래스는 문자열의 내용이 같으면, 동일한 해시코드를 반환하도록 hashCode 메서드가 오버라이딩되어 있기 때문에, 문자열의 내용이 같은 경우 hashCode()를 호출하면 항상 동일한 해시코드값을 얻는다.

반면 System.identityHashCode(Object x)는 Object 클래스의 hashCode 메서드처럼 객체의 주소값으로 해시코드를 생성하기 때문에 모든 객체에 대해 항상 다른 해시코드값을 반환할 것을 보장한다.



##### .toString()

---

이 메서드는 인스턴스에 대한 정보를 문자열로 제공할 목적으로 정의한 것이다.

Object 클래스에 저장된 toString()은 아래와 같다.

```java
public String toString() {
	return getClass().getName() + "@" + Integer.toHexString(hasCode());
}
```



**String 클래스**의 toString()은 String  인스턴스가 갖고 있는 문자열을 반환하도록 오버라이딩되어 있고, **Date 클래스**의 경우, Date 인스턴스가 갖고 있는 날짜와 시간을 문자열로 변환하여 반환하도록 오버라이딩되어 있다.



##### .clone()

---

이 메서드는 자신을 복제하여 새로운 인스턴스를 생성하는 일을 한다.

어떤 인스턴스에대해 작업을 할 때, 원래의 인스턴스는 보존하고 clone 메서드를 이용해서 새로운 인스턴스를 생성하여 작업을 하면 작업이전의 값이 보존되므로 작업에 실패해서 원래의 상태로 되돌리거나 변경되기 전의 값을 참고하는데 도움이 될 것이다.

Object 클래스에 정의된 clone()은 단순히 **인스턴스변수의 값만을 복사하기 때문에 참조변수 타입의 인스턴스 변수가 정의되어 있는 클래스는 완전한 인스턴스 복제가 이루어지지 않는다.**



예를 들어 배열의 경우, 복제된 인스턴스도 같은 배열의 주소를 갖기 때문에 복제된 인스턴스의 작업이 원래의 인스턴스에 영향을 미치게 된다.

이런 경우 clone 메서드를 오버라이딩해서 새로운 배열을 생성하고 배열의 내용을 복사하도록 해야 한다.

```java
class Point implements Cloneable { // Cloneable 인터페이스를 구현
	int x;
	int y;

	Point(int x, int y) {
		this.x = x;
		this.y = y;
	}

	public String toString() {
		return "x="+x +", y="+y;
	}

	public Object clone() { // 접근 제어자를 protected에서 public으로 변경
		Object obj = null;
		try {
			obj = super.clone();  // clone()은 반드시 예외처리를 해주어야 한다.
		} catch(CloneNotSupportedException e) {}
		return obj;
	}
}
```

Cloneable 인터페이스르 구현한 클래스에서만 clone()을 호출할 수 있다.

이 인터페이스를 구현하지 않고 clone()을 호출하면 예외가 발생한다.



그래서 **clone()을 사용하려면, 먼저 복제할 클래스가 Cloneable 인터페이스를 구현해야하고**, clone()을 오버라이딩하면서 접근 제어자를 protected에서 public으로 변경한다.

그래야만 상속관계가 없는 다른 클래스에서 clone()을 호출 할 수 있다.

그리고 조상 클래스의 clone()을 호출하는 코드가 포함된 try-catch 문을 작성해야 한다.



###### 공변 반환타입

---

이 기능은 오버라이딩할 때 조상 메서드의 반환타입을 자손 클래스의 타입으로 변경을 허용하는 것이다.

```java
public Point clone() { // 반환타입을 Object에서 Point로 변경
		Object obj = null;
		try {
			obj = super.clone(); 
		} catch(CloneNotSupportedException e) {}
		return (Point)obj; // Point 타입으로 형변환
	}
```

이처럼 공변 변환타입을 사용하면, 조잣ㅇ의 타입이 아닌, 실제로 반환되는 자손 객체의 타입으로 반환할 수 있어서 번거로운 형변환이 줄어든다.



```java
import java.util.*;

class CloneEx2 {
	public static void main(String[] args){
		int[] arr = {1,2,3,4,5};

        // 배열 arr을 복제해서 같은 내용의 새로운 배열을 만든다.
		int[] arrClone = arr.clone(); 
		arrClone[0]= 6;
		
		System.out.println(Arrays.toString(arr));
		System.out.println(Arrays.toString(arrClone));
	}
}
```

clone()을 이용하여 배열을 복사하는 예제이다.

배열도 객체이기 때문에 Object 클래스를 상속받으며, 동시에 Cloneable 인터페이스와 Serializable 인터페이스가 구현되어 있다.

그래서 Object 클래스의 멤버들을 모두 상속받는다.

Object 클래스에는 protected로 정의되어 있는 clone()을 배열에서는 public으로 오버라이딩하였기 때문에 위와 같은 직접 호출이 가능하다. 

그리고 원본과 같은 타입을 반환하므로 형변환이 필요 없다.

일반적으로 배열을 복사할 때, 같은 길이의 새로운 배열을 생성한 다음에 System.arraycopy()를 이용해서 내용을 복사하지만, 이처럼 clone()을 이용해서 간단하게 복사할 수도 있다.



배열 뿐 아니라 java.util 패키지의 Vector, ArrayList, LinkedList, HashSet, TreeSet, HashMap, TreeMap, Calendar, Date와 같은 클래스들이 이와 같은 방식으로 복제가 가능하다.



###### 얕은 복사와 깊은 복사

---

clone()은 단순히 객체에 저장된 값을 그대로 복제할 뿐, **객체가 참조하고 있는 객체까지 복제하지는 않는다.**

객체배열을 clone()으로 복제하는 경우에는 원본과 복제본이 같은 객체를 공유하므로 완전한 복제라고 보기 어렵다.

이러한 복제를 '얕은 복사'라고 한다.

얕은 복사에서는 원본을 변경하면 복사본도 영향을 받는다.

반면 원본이 참조하고 있는 객체까지 복제하는 것을 '깊은 복사' 라고 하며, 깊은 복사에서는 원본과 복사본이 서로 다른 객체를 참조하기 대문에 원본의 변경이 복사본에 영향을 미치지 않는다.



```java
class Circle implements Cloneable {
	Point p;  // 원점, 객체가 참조하고 있는 객체
	double r; // 반지름

	Circle(Point p, double r) {
		this.p = p;
		this.r = r;
	}

	public Circle shallowCopy() { // 얕은 복사
		Object obj = null;

		try {
			obj = super.clone();
		} catch (CloneNotSupportedException e) {}

		return (Circle)obj;
	}

	public Circle deepCopy() { // 깊은 복사
		Object obj = null;

		try {
			obj = super.clone();
		} catch (CloneNotSupportedException e) {}

		Circle c = (Circle)obj; 
		c.p = new Point(this.p.x, this.p.y); // 객체가 참조하고 있는 객체까지 복사

		return c;
	}

	public String toString() {
		return "[p=" + p + ", r="+ r +"]";
	}
}
```



##### .getClass()

---

이 메서드는 자신이 속한 클래스의 Class 객체를 반환하는 메서드이다.

Class 객체는 이름이 'Class'인 클래스의 객체이다.



Class 객체는 클래스의 모든 정보를 담고 있으며, 클래스당 단 1개만 존재한다.

그리고 클래스 파일이 '클래스 로더'에 의해서 메모리에 올라갈 때, 자동적으로 생성된다.



클래스 로더는 실행 시에 필요한 클래스를 동적으로 메모리에 로드하는 역활을 한다.

먼저 기존에 생성된 클래스 객체가 메모리에 존재하는지 확인하고, 있으면 객체의 참조를 반환하고 없으면 클래스 패스에 지정된 경로를 따라서 클래스 파일을 찾는다.

못 찾으면 ClassNotFoundException이 발생하고, 찾으면 해당 클래스 파일을 읽어서 Class 객체로 변환한다.



###### Class 객체를 얻는 방법

---

클래스의 정보가 필요할 때, 먼저 Class 객체에 대한 참조를 얻어 와야 하는데, 해당 Class 객체에 대한 참조를 얻는 방법은 여러 가지가 있다.

```java
Class cObj = new Card().getClass();
Class cObj = Card.class;
Class cObj = Class.forName("Card");
```

.forName()은 특정 클래스 파일, 예를 들어 데이터베이스 드라이버를 메모리에 올릴 때 주로 사용한다.



#### String 클래스

---



##### 변경 불가능한 클래스

---

String 클래스에는 문자열을 저장하기 위해서 문자형 배열 변수(char[]) value를 인스턴스 변수로 정의한다.

인스턴스 생성 시 생성자의 매개변수로 입력받는 문자열은 이 인스턴스변수에 문자형 배열로 저장되는 것이다.



한번 생성된 String 인스턴스가 갖고 있는 문자열은 읽어 올 수만 있고, 변경할 수는 없다.

예를 들어 '+' 연산자를 이용해서 문자열을 결합하는 경우 인스턴스내의 문자열이 바뀌는 것이 아니라 새로운 문자열일 담긴 String 인스턴스가 생성되는 것이다.



그렇기에 문자열간의 결합이나 추출 등의 문자열을 다루는 작업이 많이 필요한 경우에는 String 클래스 대신 StringBuffer 클래스를 사용하는 것이 좋다.

StringBuffer 인스턴스에 저장된 문자열은 변경이 가능하므로 하나의 StringBuffer 인스턴스만으로도 문자열을 다루는 것이 가능하다.



##### 문자열의 비교

----

문자열을 만들 때는 2가지 방법, 문자열 리터럴을 지정하는 방법과 String 클래스의 생성자를 사용해서 만드는 방법이 있다.



String 클래스의 생성자를 이용한 경우에는 new 연산자에 의해서 메모리할당이 이루어지기 때문에 항상 새로운 String 인스턴스가 생성된다. 

그러나 문자열 리터럴은 이미 존재하는 것을 재사용하는 것이다.



##### 문자열 리터럴 

---

자바 소스파일에 포함된 모든 문자열 리터럴은 컴파일 시에 클래스 파일에 저장된다.

이때 같은 내용의 문자열 리터럴은 한번만 저장된다.

문자열 리터럴도 String 인스턴스이고, 한번 생성하면 내용을 변경할 수 없기 때문에 하나의 인스턴스를 공유하면 되기 때문이다.



클래스 파일에는 소스파일에 포함된 모든 리터럴의 목록이 있다.

해당 클래스 파일이 클래스 로더에 의해 메모리에 올라갈 때, 이 리터럴의 목록에 있는 리터럴들이 JVM내에 있는 '상수 저장소'에 저장된다.



##### 빈문자열

---

char형 배열도 길이가 0인 배열을 생성할 수 있고, 이 배열을 내부적으로 가지고 있는 문자열이 바로 빈 문자열이다.

`String s = ""`; 과 같은 문장이 있을 대, 참조 변수 s가 참조하고 있는 String 인스턴스는 내부에 `new char[0]` 과 같이 길이가 0인 char 형 배열을 저장하고 있는 것이다.



그러나 `String s = "" ;` 과 같은 표현이 가능하다고 해서 `char c = "";` 와 같은 표현도 가능한 것은 아니다.

char 형 변수에는 반드시 하나의 문자를 지정해야 한다.



##### String 클래스의 생성자와 메서드

---



###### String.join(CharSequence delimiter, CharSequence... elements)과 StringJoiner

---

join()은 여러 문자열 사이에 구분자를 넣어서 결합한다.

구분자로 문자열을 자르는 split()과는 반대의 작업을 한다고 생각하면 된다.



java.util.StringJoiner 클래스를 사용해서 문자열을 결합할 수도 있는데, 사용하는 방법은 다음과 같다.



```java
import java.util.StringJoiner;

class StringEx4 {
	public static void main(String[] args) {
		String animals = "dog,cat,bear";
		String[] arr   = animals.split(",");

		System.out.println(String.join("-", arr)); // dog-cat-bear

		StringJoiner sj = new StringJoiner("/","[","]");
		for(String s : arr)
			sj.add(s);

		System.out.println(sj.toString());// [dog/cat/bear]
	}
}
```



###### String.format()

---

format()은 형식화된 문자열을 만들어내는 간단한 방법이다.

사용법의 경우는 printf()와 완전히 동일하다.

```java
String str = String.format("%d", 3);
```



###### 기본형 값을 String으로 변환

---

기본형 값은 String으로 변환하기 위해서는 빈문자열 ""을 더해주는 방법도 있지만 valueOf()를 사용하는 방법도 있다.

성능은 valueOf()가 더 좋다.

```java
int i = 100;
String str1 = i +"";
String str2 = String.valueOf(i);
```



###### String을 기본형 값으로 변환

---

반대로 String을 기본형으로 변환하는 방법도 간단하다.

valueOf()를 쓰거나 parseInt()를 사용하면된다.

```java
int i = Integer.parseInt("100");
int i2 = Integer.valueOf("100");
```

예전에는 parseInt()와 같은 메서드를 많이 사용했는데, 메서드의 이름을 통일하기 위해 valueOf()가 나중에 추가되었다.

valueOf(String s)는 메서드 내부에서 그저 parseInt(String s)를 호출할 뿐으로, 두 메서드는 반환 타입만 다르지 같은 메서드이다.



**기본형과 문자열간의 형변환 방법**을 정리하면 다음과 같다.

- 기본형 to 문자열
  `String String.valueOf( 기본형타입 참조변수 )`
- 문자열 to 기본형
  `기본형타입 기본형의Wrapper클래스.parse기본형타입(String s)`
  `기본형타입 기본형의Wrapper클래스.valueOf(String s)`



##### StringBuffer 클래스와 StringBuilder 클래스

---

String 클래스는 인스턴스를 생성할 때 지정된 문자열을 변경할 수 없지만 StringBuffer 클래스는 변경이 가능하다.

내부적으로 문자열 편집을 위한 버퍼를 가지고 있으며, StringBuffer 인스턴스를 생성할 때 그 크기를 지정할 수 있다.



StringBuffer 클래스는 String 클래스와 유사한 점이 많다.

StringBuffer 클래스는 String 클래스와 같이 문자열을 저장하기 위한 char 형 배열의 참조 변수를 인스턴스 변수로 선언해 놓고 있다.

StringBuffer 인스턴스가 생성될 때, char형 배열이 생성되며 이 때 생성된 char형 배열을 인스턴스변수 value가 참조하게 된다.

```java
public final class StringBuffer implements java.io.Serializable {
	private char[] value;
}
```



###### StringBuffer의 생성자

---

StringBuffer 클래스의 인스턴스를 생성할 때, 적절한 길이의 char형 배열이 생성되고, 이 배열은 문자열을 저장하고 편집하기 위한 공간(buffer)으로 사용된다.

StringBuffer 인스턴스를 생성할 때는 생성자 StringBuffer(int length)를 사용해서 StringBuffer 인스턴스에 저장될 문자열의 길이를 고려하여 충분히 여유있는 크기로 지정하는 것이 좋다.

```java
public StringBuffer(int length) {
	value = new char[length];
	shared = false;
}

public StringBuffer() {
	this(16);
}

public StringBuffer(String str) {
	this(str.length() + 16);
	append(str);
}
```



###### StringBuffer의 변경

---

append()는 반환타입이 StringBuffer인데 자신의 주소를 반환한다.

그래서 하나의 StringBuffer 인스턴스에 대해 연속적으로 append()를 호출하는 것이 가능하다.



```java
StringBuffer sb = new StringBuffer("abc");
sb.append("123").append("zz");
```

만일 append()의 반환 타입이 void라면 이렇게 할 수 없을 것이다.



###### StringBuffer의 비교

---

StringBuffer 클래스는 equals 메서드를 오버라이딩하지 않아서 StringBuffer 클래스의 equal 메서드를 사용해도 "=="로 비교한 것과 같은 결과를 얻는다.



반면 toString()은 오버라이딩되어 있어서 StringBuffer 인스턴스에 toString()을 호출하면, 담고 있는 문자열을 String으로 반환한다.



###### StringBuffer 클래스의 생성자와 메서드

---

StringBuffer 클래스는 String 클래스와 유사한 메서드를 많이 가지고 있다.

StringBuffer는 추가, 변경, 삭제와 같이 저장된 내용을 변경할 수 있는 메서들이 추가로 제공된다.



##### StringBuilder

----

StringBuffer은 멀티쓰레드에 안전하도록 동기화되어 있다.

동기화가 StringBuffer의 성능을 떨어뜨린다.

멀티쓰레드로 작성된 프로그램이 아닌 경우, StringBuffer의 동기화는 불필요하게 성능만 떨어뜨리게 된다.

StringBuilder는 StringBuffer에서 쓰레드의 동기화만 뺀 것이다.



#### 래퍼(wrapper) 클래스

---

때로는 기본형 변수도 어쩔 수 없이 객체로 다뤄야 하는 경우가 있다.

예를 들면, **매개변수로 객체를 요구할 때, 기본형 값이 아닌 객체로 저장해야할 때, 객체 간의 비교가 필요할 때 등등**의 경우에는 기본형 값들을 객체로 변환하여 작업을 수행해야 한다.



이 때 사용되는 것이 래퍼(wrapper) 클래스이다.

8개의 기본형을 대표하는 8개의 래퍼클래스가 있는데, 이 클래스들을 이용하면 기본형 값을 객체로 다룰 수 있다.



래퍼 클래스의 생성자는 **매개변수로 문자열이나 각 자료형의 값들을 인자로 받는다.**

이때 주의해야할 것은 생성자의 매개변수로 문자열을 제공할 때, 각 자료형에 맞는 문자열을 사용해야한다는 것이다.



아래는 int형의 래퍼 클래스인 Integer 클래스의 실제코드이다.

```java
public final class Integer extends Number implements Comparable {
	...
	private int value;
	...
}
```

이처럼 래퍼 클래스는 객체생성 시에 생성자의 인자로 주어진 각 자료형에 알맞는 값을 내부적으로 저장하고 있으며, 이에 관련된 여러 메서드가 정의되어 있다.



래퍼 클래스들은 모두 equals() 가 오버라이딩되어 있어 주소값이 아닌 객체가 가지고 있는 값을 비교한다.

그리고 오토박싱이 된다고 해도 Integer 객체에 비교연산자를 사용할 수는 없다.

대신 compareTo()를 제공한다.

그리고 toString()도 오버라이딩되어 있어서 객체가 가지고 있는 값을 문자열로 변환하여 반환한다.

이외에도 래퍼 클래스들은 MAX_VALUE, MIN_VALUE, SIZE, BYTES, TYPE 등의 static 상수를 공통적으로 가지고 있다.



##### 레퍼클래스의 값을 기본형으로 변환하기

---

- 래퍼클래스의 값 to 기본형
  `래퍼클래스타입 참조변수;  참조변수.기본형Value();`



#### Number 클래스

---

이 클래스는 추상클래스로 내부적으로 **숫자를 맴버변수로 갖는 래퍼 클래스들의 조상**이다.

Number 클래스 자손으로 BigInteger과 BigDecimal 등이 있는데, BigInteger는 long으로도 다룰 수 없는 큰 범위의 정수를 BigDecimal은 double로도 다룰 수 없는 큰 범위의 부동 소수점수를 처리하기 위한 것으로 연산자의 역활을 대신하는 다양한 메서드를 제공한다.



##### 문자열을 숫자로 변환하기

---

- 문자열 to 기본형
  `기본형타입 기본형의Wrapper클래스.parse기본형타입(String s)`
- 문자열 to 래퍼 클래스
  `기본형의Wrapper클래스 기본형의Wrapper클래스.valueOf(String s)`



#### 유용한 클래스

---



##### java.util.Objects

---

Object 클래스의 보조 클래스로 Math 클래스처럼 모든 메서드가 **'static'**이다. 

객체의 비교나 널 체크(null check)에 유용하다.

isNull()은 해당 객체가 널인지 확인해서 null이면 true를 반환하고 아니면 false를 반환한다.

nonNull()은 isNull과 정확히 반대되는 일을 한다.

requireNonNull()은 해당 객체가 널이 아니어야 하는 경우에 사용한다.

```java
void setName(String name) {
	this.name = Objects.requireNonNull(name, "name must not be null");
}
```

이는  위와 같이 사용할 수 있는데 널이 아닌 경우 name을 널인 경우 name must not be null을 NullPointerException과 함께 발생시킨다.



Object 클래스에는 두 객체를 비교하는 메서드가 등가비교를 위한 equals()만 있고 대소 비교를 위한 compare()는 없었다.

하지만 Objects 클래스에는 compare()가 추가 되었다.

compare()는 두 비교대상이 같으면 0, 크면 양수, 작으면 음수를 반환한다.

```java
static int compare (Object a, Object b, Comparator c);
```



Objects 클래스에는 Object 에도 정의된 equals()가 있는데 이는 null 검사를 하지 않아도 된다는 장점이 있다.

```java
public static boolean equals (Object a, Object b) {
	return (a == b) || (a != null && a.equals(b));
}
```



deepEquals() 도 존재하는데 이는 객체를 재귀적으로 비교하기 때문에 다차원 배열도 비교가 가능하다.



toString()은 equals() 처럼, 내부적으로 널 검사를 한다는 것 빼고는 특별한 것이 없다.



hashCode() 역시 내부적으로 널 검사를 한 후에 Object 클래스의 hashCode()를 호출할 뿐이다.



##### java.util.Random

---

난수를 얻는 방법을 생각하면 Math.random()이 떠오를 것이다.

이 외에도 Random 클래스르 사용하면 난수를 얻을 수 있다.

Math.random()은 내부적으로 Random 클래스의 인스턴스를 생성해서 사용하는 것으로 둘 중 어느것이나 사용하면 된다.

```java
double randNum = Math.random();
double randNum = new Random().nextDouble();
```



Math.random()과 Random의 가장 큰 차이점이라면, 종자값을 설정할 수 있다는 것이다.

종자값이 같은 Random 인스턴스들은 항상 같은 난수를 같은 순서대로 반환한다.

종자값은 난수를 만드는 공식에 사용되는 값으로 같은 공식에 같은 값을 넣으면 같은 결과를 얻는 것처럼 같은 종자값을 넣으면 같은 난수를 얻게 된다.



###### Random 클래스의 생성자와 메서드

---

생성자 Random()은 아래와 같이 종자값을 System.currentTimeMillis()로  하기 때문에 실행할 때마다 얻는 난수가 달라진다.

```java
public Random() {
	this(System.currentTimeMillis());
}
```



하지만 종자값을 다음과 같이 설정하면 같은 값들을 같은 순서로 얻는 것을 확인할 수 있다.

```java
import java.util.*;

class RandomEx1 {
	public static void main(String args[]) {
		Random rand  = new Random(1);
		Random rand2 = new Random(1);

		System.out.println("= rand =");
		for(int i=0; i < 5; i++)
			System.out.println(i + ":" + rand.nextInt());

		System.out.println();
		System.out.println("= rand2 =");
		for(int i=0; i < 5; i++)
			System.out.println(i + ":" + rand2.nextInt());
	}
}
```



> = rand =
> 0:-1155869325
> 1:431529176
> 2:1761283695
> 3:1749940626
> 4:892128508
>
> = rand2 =
> 0:-1155869325
> 1:431529176
> 2:1761283695
> 3:1749940626
> 4:892128508



메서드로는  `.next기본형타입()` 은 생성된 난수를 기본형 타입에 맞게 반환한다.



또한 Math.random()을 이용해서 실제 프로그래밍에서 유용할만한 메서드를 만들 수 있는데 그 코드는 다음과 같다.

```java
import java.util.*;

class RandomEx2 { 
	public static void main(String[] args) { 
		Random rand = new Random();
		int[] number = new int[100]; 
		int[] counter = new int[10]; 

		for (int i=0; i < number.length ; i++ ) { 
//			System.out.print(number[i] = (int)(Math.random() * 10)); 
//                  0<=x<10 범위의 정수 x를 반환한다.
			System.out.print(number[i] = rand.nextInt(10));	  
		} 
		System.out.println(); 

		for (int i=0; i < number.length ; i++ ) { 
			counter[number[i]]++; 
		} 

		for (int i=0; i < counter.length ; i++ ) { 
			System.out.println( i +"의 개수 :"+ printGraph('#',counter[i]) + " " + counter[i]); 
		} 
	} 

	public static String printGraph(char ch, int value) { 
		char[] bar = new char[value]; 

		for(int i=0; i < bar.length; i++) { 
			bar[i] = ch; 
		} 
		return new String(bar); 
	} 
} 
```



##### java.util.regex

---

java.util.regex에는 Pattern과 Matcher이 속해있다.

Pattern은 정규식을 정의하는데 사용되고 Matcher는 정규실(패턴)을 데이터와 비교하는 역활을 한다.

정규식을 정의하고 데이터를 비교하는 과정을 단계별로 설명하면 다음과 같다.

1. 정규식을 매개변수로 Pattern클래스의 static 메서드인 Pattern compile(String regex)을 호출하여 Pattern 인스턴스를 얻는다.
   `Pattern p = Pattern.compile("c[a-z]*");`
2. 정규식으로 비교할 대상을 매겨변수로 Pattern 클래스의 Matcher matcher (CharSequence input)을 호출해서 Matcher 인스턴스를 얻는다.
   `Matcher m = p.matcher(data[i]);`
3. Matcher 인스턴스에 boolean matches()를 호출해서 정규식에 부합하는지 확인한다.
   `if(m.matches())`



##### java.util.Scanner 

---

Scanner은 화면, 파일, 문자열과 같은 입력소스로부터 문자데이터를 읽어오는데 도움을줄 목적으로 추가되었다.

Scanner에는 다양한 생성자를 지원하기 때문에 다양한 입력소스로부터 데이터를 읽을 수 있다.



또한 Scanner은 정규식 표현을 이용한 라인단위 검색을 지원하며 구분자에도 정규식 표현을 사용할 수 있어서 복잡한 형태의 구분자도 처리가 가능하다.

이를 위한 메서드는 다음과 같다.

```java
Scanner useDelimiter (Pattern pattern) {}
Scanner useDelimiter (String patterm) {}
```



Scanner은 `.next기본형타입()`  과 같은 메서드를 사용하여 입력받은 문자를 변환하여 준다.



##### java.util.StringTokenizer

---

StringTokenizer은 긴 문자열을 지정된 구분자를 기준으로 토큰이라는 여러 개의 문자열로 잘라내는 데 사용한다.

예를 들어 "100.200.300.400"이라는 문자열이 있을 때, '.' 구분자로 잘라내면 "100", "200", "300", "400"이라는 4개의 문자열(토큰)을 얻을 수 있다.



StringTokenizer과 String의 split(String regex)와 Scanner의 useDelimiter(String pattern)이 다른 점은 정규식 사용여부이다. 

StringTokenizer은 정규식을 사용하지 않아 간단하면서도 명확한 결과를 얻을 수 있다.

그러나 StringTokenizer은 구분자로 단 하나의 문자 밖에 사용하지 못하기 때문에 보다 복잡한 형태의 구분자로 문자열을 나누어야 할 때는 어쩔 수 없이 정규식을 사용하는 메서드를 사용해야 한다.



```java
import java.util.*; 

class StringTokenizerEx2 { 
	public static void main(String[] args) { 
		String expression = "x=100*(200+300)/2";
		StringTokenizer st = new StringTokenizer(expression, "+-*/=()", true);

		while(st.hasMoreTokens()){
			System.out.println(st.nextToken());
		}
	} // main의 끝
} 
```



> x
> =
> 100
> *
> (
> 200
> +
> 300
> )
> /
> 2



이는 생성자 StringTokenizer( String str, String delim, boolean returnDelims)를 사용해서 구분자도 토큰으로 간주하여 위와 같은 결과가 나온 것이다.

3번째 인자가 true가 아니었다다면 결과는 다음과 같았을 것이다.



> 100
> 200
> 300
> 2



String 클래스의 split() 과 StringTokenizer는 빈 문자열을 인식하는 것에도 차이를 보인다.

split()은 빈 문자열도 토큰으로 인식하는 반면 StringTokenizer은 빈 문자열을 토큰으로 인식하지 않는다.

성능에서도 차이가 있는데, split()은 데이터를 토큰으로 잘라낸 결과를 배열에 담아서 반환하기 때문에 데이터를 토큰으로 바로바로 잘라서 반환하는 StringTokenizer 보다 성능이 떨어진다.

이는 이러한 차이에 대한 예시를 보여주는 코드이다.

```java
import java.util.*;

class StringTokenizerEx5 {
	public static void main(String[] args) {
		String data = "100,,,200,300";

		String[] result = data.split(",");
		StringTokenizer st = new StringTokenizer(data, ",");

		for(int i=0; i < result.length;i++)
			System.out.print(result[i]+"|");

		System.out.println("개수:"+result.length);

		int i=0;
		for(;st.hasMoreTokens();i++)
			System.out.print(st.nextToken()+"|");

		System.out.println("개수:"+i);
	} // main
}
```

