Object Oriented Programming is a programming paradigm that centers around objects and how they interact with on another and pass messages to each other. The objects in object oriented programming usually represent real world entities.Object Oriented Programming has 4 pillars that define it, these are inheritance, encapsulation, abstraction and polymorphism.

## Inheritance
Inheritance in object oriented programming refers to the ability of parent classes to pass down properties and logic to child classes. This involves a child class inheriting class attributes as well as class methods, a child class can choose to override the methods with new content or use them unchanged. Below is an example of class inheritance in java:
```java
class Animal {
	public static void main(String[] args){
	Dog dogObj = new Dog();
	dogObj.speak(); // Dog class overrides this method with custom message
	dogObj.sleeps(); // Dog class instance has access to this method
	despite not being explicitly defined, through inheritance
	}
}
public static void speak(){
	System.out.println("Animal makes sound!");
}
public static void sleeps() {
	System.out.println("All animals sleep!");
}
class Dog extends Animal {
	public static void speak(){
	System.out.println("Dog makes sound woof!");
	}
}
```

## Encapsulation
In Object Oriented Programming encapsulation is the process of hiding the inner workings of a class and avoiding the access or modification of the internal state of the class from outside. It also entails the bundling of data and methods acting on that data into a single entity, i.e. a class. The data is encapsulated in a class. An example of data encapsulation in java would look like this:
```java
/**
The BankAccount class encapsulates the Account Logic by using private access
modifiers to ensure the internal state is only altered internally. It also
provides getter methods ensuring the outside world can still interact with the
class but with no direct access to the internal parts since they've been made
private.
*/
class BankAccount {
	private String AccountNumber;
	private double AccountBalance = 0.0;
	public String getAccountNumber(){
		return AccountNumber;
	}
	public double getAccountBalance(){
		return AccountBalance;
	}
	private void updateAccountBalance(double Amount){
		AccountBalance += Amount;
		System.out.println("Account Balance is " + AccountBalance);
	}
}

class BankApp {
	public static void main(String[] args){
	BankAccount bankAccountObj = new BankAccount();
	System.out.println(
		"Account Balance is: " + bankAccountObj.getAccountBalance()
	);
	}
}
```

## Abstraction
Abstraction in Object Oriented Programming is the process of hiding the unnecessary
details/complexities of a class and only presenting the necessary parts required to interact with it. A real world example is a car, a driver does need to understand its inner workings to be able to drive it. Abstraction is implemented in java using Abstract classes and Interfaces that leave the details of their method implementations to the concrete classes that implement them. An example of an abstract class in Java is as follows:
```java
abstract class ShapeAbstractClass {
	String dimensions;
	abstract void updateDimensions(String shapeDimensions);
	void drawShape(){
		System.out.println("This is a shape with dimensions " + dimensions);
	}
}

class Rectangle extends ShapeAbstractClass {
	void updateDimensions(String shapeDimensions){
	dimensions=shapeDimensions;
	}
}

class Abstraction {
	public static void main(String[] args){
	Rectangle rectangleObj = new Rectangle();
	rectangleObj.updateDimensions("width:23, length:54");
	rectangleObj.drawShape();
	}
}
```

## Polymorphism
In OOP Polymorphism is the ability of a method call to represent different underlying forms. Allowing objects of different classes to be treated uniformly while displaying behaviour specific to the actual class. An example in java would look like:
```java
  

class Animal {
	void speak(){
		System.out.println("An animal can make sound");
	}

	void numberOfLimbs(){
		System.out.println("The animal has four limbs");
	}
}

class Dog extends Animal {
	void speak(){
		System.out.println("A dog makes the sound woof!");
	}
}

class Cat extends Animal {
	void speak(){
		System.out.println("A cat makes the sound meow!");
	}
}


class Polymorph {
	public static void main(String[] args){
	Animal animalObj = new Animal();
	animalObj.speak();
	Dog dogObj = new Dog();
	dogObj.speak();
	Cat catObj = new Cat();
	catObj.speak();
	
	// or using a common superclass reference
	Animal[] animals = {new Animal(), new Dog(), new Cat()};
	for (Animal animal: animals){
		animal.speak();
		}
	}
}
```