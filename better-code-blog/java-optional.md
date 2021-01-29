# 2021-01-29 Is Optional an Option ?   

Optional was introduced in Java 8 to simplify and reduce the use of Null. And to better prevent NullpointerExceptions.

>  Optional is intended to provide a limited mechanism for library method return types where there needed to be a clear way to represent “no result," and using null for such was overwhelmingly likely to cause errors. 
>> The purpose of Java 8 Optional is clearly defined by Brian Goetz, Java’s language architect

So basically Optional 

* is used as return value for functions that might return null. - to prevent NullPointerExceptions
* can be used as local variable.
* should not be used in class attributes.
* should not be used in constructors

Here are three usages as described in [26 Reasons Why Using Optional Correctly Is Not Optional][26 Reasons Why Using Optional Correctly Is Not Optional]

* Never assign Null to an Optional  
```java
Optional<Cart> optCart = Optional.empty();
```

* Ensure that an Optional has a value before calling  
```java
// don't
Cart myCart = optCart.get();
// do
if (optCart.isPresent()){
	myCart = optCart.get();
else{ ... }
```

* If empty throw an Exception if you please  
```java
public String findUserStatus(long id){

	Optional<String> status = ...; // prone to return an empty Optional
	return status.orElseThrow();
}
```
This will throw a **java.util.NoSuchElementException** .

* When no Value is Present, Set/Return an Already constructed Default Object
```java
// short and concise
Cart myCart = optCart.orElse(new Cart());
```
If the creation of a default value is in general time consuming then it is much better to use  
```java
Cart myCart = optCart.orElseGet(Cart::new);
```
The second version uses lazy evaluation and is only triggered if the empty case is true.

So **Lazy Evaluation** is one of the pillars of functional programming.

The major flaw from my perspective:  

```java
Optional.of(null).get(); // will throw a NullPointerException.
```

The basic intention of Optional was to circumvent NullPointerExceptions in the first place - with the restriction of using it as return values not general algebraic data type.


## The other Option

In contrast to Optional (Java8) the type Option like used in vavr library (java) and Scala is a Functor on algebraic data types. And as such it has far reaching applicability.  
With Option it is not possible to throw a NullPointerException.

Side-note: Javaslang's Option fixes several issues of Java's Optional type:

  * Option is Serializable
  * Option is a container that can contain null (correctness of map method)
  * Option is Iterable
  * Option can be pattern matched (Some and None)
    taken from Ideomatic Javaslang: For Comprehension

**Resources**:  

* [26 Reasons Why Using Optional Correctly Is Not Optional](https://dzone.com/articles/using-optional-correctly-is-not-optional)  
* [Vavr - Java functional Library](https://www.vavr.io/)  
* http://mvpjava.com/java-optional/ Andy Luis Blog - with reference to a youtube video with live coding

