#Head first design patterns

## Design Principles
- Identify areas of a system vary and seperate them from areas that stay the same
- Program to an interface and not to an implementation
- Favour composition over inheritence (inhertance is less flexible and has some draw backs, like no runtime behaviour changes)
- Strive for loosly coupled designs for objects that interact
- Classes should be open for extension and closed to modification
- Depend on Abstracts and not on concreate implementations - aka dependency inversion principle (the inversion bit means that your typical thinking is inverted in that you now depend on abstract high level components)



## Design patterns

### Strategy Pattern
- What is it? 
		
   - Also known as the policy pattern, It allows a program to dynamically decide on its behaviour during runtime by grouping them using interfaces

- When, how and why?

   - When inheritence doesn't seem to work where making a change to a hierachy tree (in terms of new or a change in methods) would have too much affect on existing code. You can choose to then compose the behaviour (algorithm) and dynamically set it during runtime. In this case you would 'Have a' behaviour stored as a member property to which you could inject/contruct/set. This composed type would typically be an Interface and you would implement a set of possible behavoiurs to use.

- Pro's con's

   - Pro's you can change a types behaviour at run time by setting it. 
   - More flexible than inheritance
   - Group behavours or algorithms by interfaces

- Example

	abstract class Animal {
		private Noise behaviour;
	
		public void makeNoise(){
			behaviour.performNoise();
		}
	
		pubic setMakeNoise(Noise behaviour)
		{
			this.behaviour = behaviour;
		}
	}

	interface Noise {
		void performNoise();
	}

	class Howl implements Noise{
		void performNoise(){
			System.out.println("HOOOOOWWWWWWLLLLLLL");
		}
	}

	class Dog extends Animal {}
	class Wold extends Animal {}

	Animal dog = new Dog();
	Animal wolf = new Wolf();

	Noise howl = new Howl();
	dog.setMakeNoise(howl);
	wolf.setMakeNoise(howl);







### Observer Pattern
- What is it? 

   - A pattern that defines a one to many relationship between an observable object and many observers(subscribers) such that when something happens that the observerable knows about (state or otherwise) it notifies the observers. Think about the publisher/subscription model of a magazine and thats pretty much it

- How, when and why?

   - The observable will hold a collection of registered observers (typically by a collection of the observers interface). These observers can register by using the a register method on the observable (likewise unregister when it no longer needs to know of updates).
   - When something happens on the observable, an update method will run that will iterate through the observers and run an update method on them.
   - There are two modes in which the Observer pattern can run (push or pull).
   - When an update occurs, the observable can push the updated info (during the update) or the observers and pull the info from a reference to the observable that they hold.
   - You would use this pattern when there are many dependant objects looking at a single thing, all wanting to be updated when something happens
   - You can use the JDK's Observer class but because its a concrete class you have to extend and therefore cannot inherit from anything else.
   - When using the JDK Observer class, you must use the setChanged method to true as it will not notify the observer if its false. Its a guard as not all updates need to update all observers

- Pro's con's

   - Could be difficult to follow if you don't know what it is before hand
   - JDK Observer is a class and some methods are protected meaning you need to open them up if you wish to call them
   - Objects and register/unregister at runtime
   - Obervable and oberver are loosly coupled as they only know about the other via interfaces and updating/adding the code to either wont have any impact of the other
   - Flexible as it can be either pull or push
   - Doesnt always depend on the order of notifications
   - Possible that code in an observer can blow up the chain of observers during a notify
		
- Example

	interface Observable{
		addObserver(Observer ob);
		removeObserver(Observer ob);
		notifyObservers();
	}

	class MyObservable{
		Set<Observer> observers;
		String myState;
		addObserver(Observer ob){
			observers.add(ob);
		}
		removeObserver(Observer ob){
			observer.remove(obj);
		}
		notifyObservers()
		{
			for(Observer ob : observers){
				observers.update(myState); //push config
			}
		}
		updateState(String aState){
			this.myState = aState;
			notifyObservers();
		}
	}

	interface Observer{
		update(String state);
	}

	class MyMonitoringObserver{
		update(String observableState)
		{
			System.out.println("just got this update: " + observableState);
		}
	}





### Decorator Pattern
--- What is it? 
		A pattern that attaches additional responsibility / behaviour to an object dynamically. Its a flexible alternative to inheritence that allows you to extend functionality

--- How, when and why?
		- Sometimes inheritence is not the right choice as it could lead to class explosions. (many classes extending a single or few classes) Or when making a change to a base class makes too much of an impact to child classes
		- Decorators have the same base class / interface as the objects they decorate
		- You take the base class, extend it as a base class decorator and have classes extend the decorator and compose the base class then decorate the interested methods
		- Decorating a method can happen before or after calling the decorated method
		- You can have many layers of decorators
		- Decorators should be transparent

--- Pro's con's
		- Can be confusing as alot of small classes will need to be created
		- Confusing if you see many nested instantiated classes within constructors (look at the InputStream from JDK)
		- Adheres to open to extension and close to modification
		
--- Example

	abstract class Meal{
		double cost();
	}

	abstract class MealDecorator extends Meal{
	}

	class FishChips extends Meal{
		double cost(){
			return 5.50d;
		}
	}

	class KetchupDecorator extends MealDecorator{
		Meal composedMeal;
		public KetchupDecorator(Meal meal){
			this.composedMeal = meal;
		}

		double cost(){
			this.composedMeal.cost() + 0.10d;
		}
	}

	class GherkinDecorator extends MealDecorator{
		Meal composedMeal;
		...
	}




### Factory Pattern
--- What is it? 
		- There are two factory patterns. Factory Method & Abstract Factory (There is a third called the simple factory, just like abstract but is just a single non abstract class)
		- Abstract factory, defines an interface for a group of related classes without specifying the contreate classes
		- Factory method, define an interface for creating an object but let the subclass define what and how its created
		- Abstract factory looks like it contains many factory methods

--- How, when and why?
		- Factory method has an abstract class that uses an abstract method for instantiating an object, subclasses are then used to create those objects
		- Abstract Factory defines an abstract class that defines a set of abstract methods to create a set of classes. its up to the subclass to create these types.
		- Abstract factory has the clients compose the factories for which the use to instantiate objects while factory method uses inheritence
		- You start using factories when you notice your duplicating code that instantiates objects
		- You wrap this in a single interface so that you can maintain the instatiation of objects in one place
		- Factories promote loose coupling as you can hide the instantiation of concreate classes in the factories
		- Use it when you want to encapsulate object creation


--- Pro's con's
		- Loose coupling of the instantiated classes and they are created in the factory and use the interfaces of the types
		- Can be harder to read as object instances are hidden behind and interface or abstract class
		
--- Example
		- factory method

		abstract class GlassShop{
			Glass sellBeverage(){
				Glass glass = createItem();
				return glass;
			}

			abstract Glass createItem();
		}

		class Jar extends Glass{}
		class WineBottle extends Glass{}

		class JarShop extends GlassShop{
			Glass createItem(){
				return new Jar();
			}
		}

		class WineShop extends GlassShop{
			Glass createItem(){
				return new WineBottle();
			}
		}

		static void main(...){
			GlassShop bottleShop = new BottleShop();
			GlassShop Jarshop = new JarShop();

			bottleShop.sellBeverage(); //returns a winebottle
			jarShop.sellBeverage(); // returns a jar
		}


		- abstract factory ----------------------------

		abstract class FastFoodShop{
			abstract MainFood createMainFood();
			abstract Drink createDrink();
			abstract Desert createDesert();
		}

		class ChickenShop extends FastFoodShop{
			MainFood createMainFood(){ return new ChickenChips(); }
			Drink createDrink(){ return new Coke(); }
			Desert createDesert(){ return new Cookie(); }
		}

		class FishAndChips extends FastFoodShop{
			MainFood createMainFood(){ return FishAndChips(); }
			Drink createDrink(){ return new Water(); }
			Desert createDesert(){ return new Gherkin(); }
		}

		abstract class MainFood {}
		class ChickenChips extends MainFood{}
		class FishAndChips extends MainFood{}
		abstract class Drink{}
		class Coke extends Drink{}
		class Water extends Drink{}
		...

		class FastFoodService{
			private FastFoodShop shop;
			public FastFoodService(FastFoodShop shop){this.shop = shop;} //inject a chicken or fish n chips shop
			void serveMain(){
				shop.creatMainFood()....;
			}
		}




### Singleton Pattern
--- What is it? 
		- It's a pattern that allows for only one instance of a class

--- How, when and why?
		- Create a class that has a static reference to an instance of itself, have a private constructor and then a method (getInstance()) to checks to see if the static reference object exists, if not create and return it.
		- You can create a new instance within the static reference line. This ensures that on class loading time, the instance is instantiated
		- You use it in instances where it makes sense to only have one instance, such as configuration, helper objects that don't have state

--- Pro's con's
		- Lower memory footprint
		- Pretty much against the one class one purpose principle
		- Doesn't seem to work well with inheritence
		-
		
--- Example
















