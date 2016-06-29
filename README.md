#Head first design patterns

## Design Principles
- Identify areas of a system vary and seperate them from areas that stay the same
- Program to an interface and not to an implementation
- Favour composition over inheritence (inhertance is less flexible and has some draw backs, like no runtime behaviour changes)
- Strive for loosly coupled designs for objects that interact
- Classes should be open for extension and closed to modification
- Depend on Abstracts and not on concreate implementations - aka dependency inversion principle (the inversion bit means that your typical thinking is inverted in that you now depend on abstract high level components)
- Principle of least knowledge. - Talk to only your immediate friends. This is basically only call methods on youself, on objects provided to you via a parameter. Try and have your classes import less and know less about other clases.
- Dont call us, we'll call you - Have high level components decide and call low level components. Helps avoid code rot, spaghetti code.
- A class should have only one reason to change - More responsibilies of a class the more chance of a change. The more a class deals with one or one set of things the more cohesive it is, if its doing alot of unrelated things then its has low cohesion.


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
```java
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
```






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
```java
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
```




### Decorator Pattern
- What is it? 

   - A pattern that attaches additional responsibility / behaviour to an object dynamically. Its a flexible alternative to inheritence that allows you to extend functionality

- How, when and why?

   - Sometimes inheritence is not the right choice as it could lead to class explosions. (many classes extending a single or few classes) Or when making a change to a base class makes too much of an impact to child classes
   - Decorators have the same base class / interface as the objects they decorate
   - You take the base class, extend it as a base class decorator and have classes extend the decorator and compose the base class then decorate the interested methods
   - Decorating a method can happen before or after calling the decorated method
   - You can have many layers of decorators
   - Decorators should be transparent

- Pro's con's

   - Can be confusing as alot of small classes will need to be created
   - Confusing if you see many nested instantiated classes within constructors (look at the InputStream from JDK)
   - Adheres to open to extension and close to modification
		
- Example
```java
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
```



### Factory Pattern
- What is it? 

   - There are two factory patterns. Factory Method & Abstract Factory (There is a third called the simple factory, just like abstract but is just a single non abstract class)
   - Abstract factory, defines an interface for a group of related classes without specifying the contreate classes
   - Factory method, define an interface for creating an object but let the subclass define what and how its created
   - Abstract factory looks like it contains many factory methods

- How, when and why?

   - Factory method has an abstract class that uses an abstract method for instantiating an object, subclasses are then used to create those objects
   - Abstract Factory defines an abstract class that defines a set of abstract methods to create a set of classes. its up to the subclass to create these types.
   - Abstract factory has the clients compose the factories for which the use to instantiate objects while factory method uses inheritence
   - You start using factories when you notice your duplicating code that instantiates objects
   - You wrap this in a single interface so that you can maintain the instatiation of objects in one place
   - Factories promote loose coupling as you can hide the instantiation of concreate classes in the factories
   - Use it when you want to encapsulate object creation


- Pro's con's

   - Loose coupling of the instantiated classes and they are created in the factory and use the interfaces of the types
   - Can be harder to read as object instances are hidden behind and interface or abstract class
   - Have better/more meaningful names than constructors
   - can return objects that are sub types of the constructor
   - Having a class with no public or protected constructor cannot be subclassed
		
- Example
```java
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
```
```java
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
```



### Singleton Pattern
- What is it? 

   - It's a pattern that allows for only one instance of a class

- How, when and why?

   - Create a class that has a static reference to an instance of itself, have a private constructor and then a method (getInstance()) to checks to see if the static reference object exists, if not create and return it.
   - You can create a new instance within the static reference line. This ensures that on class loading time, the instance is instantiated
   - If the instantiatation of the object is an issue (you might not need it all the time, or at startup) then you must wrap the getInstance() method in either a synchronise block (can have performance issues as you need to lock on the class) or you can have a 'double check lock' where you check if null, synchronise, and check for null again.
   - You use it in instances where it makes sense to only have one instance, such as configuration, helper objects that don't have state


- Pro's con's

   - Lower memory footprint
   - Pretty much against the one class one purpose principle
   - Doesn't seem to work well with inheritence
   - Can cause multithreading issues if you don't wrap the getInstance() method in a synchronise
   - It will not work well for systems with multiple classloaders as the instances are bound to a class and the classes will be loaded mulitple times(???)
   - Considered as an anti pattern as people misuse it (make it contain state)

	
- Example

```java

	class MySingleton{
		private MySingleton(){}
		private static MySingleton INSTANCE = null;
		//or
		private final static MySingleton INSTANCE = new MySingleton(); //inline instantiation

		public static MySingleton getInstance(){ //if not using inline instantiation either synchronise the entire method
			if(INSTANCE == null){
				synchronise(MySingleton.class){  //or synchronise here
					if(INSTANCE == null){
						INSTANCE = new MySingleton();
					}
				}
			}

			return INSTANCE;
		}

		//or

		public static MySingleton getInstance(){
			return INSTANCE;
		}
	}

```




### Command Pattern
- What is it? 

   - Allows you to encapsulate a request (command) as an object and paramatize clients with these requests. Which in tern could be invoked by the client or something else
   - Allows you to queue, log and undo requests
   - Think of the Diner example where the client (customer) creates the command (order), sends it to the invoker (waitress) which the calls the execute method (order up) on the receiver (cook)
   - 

- How, when and why?

   - Implement a command interface that has at least a public execute method. compose an object into the implementation. have the execute method run a single specific method on the composed object
   - Use it when you wish to decouple the client that is creating it to the invoker thats using it. i.e multiple generators creating commands and sending them to a thread pool to have threads call execute on them
   - Commands should have a single purpose
   - Macro commands are commands that contain many commands which allow it to run them in order
   - Undo funcationality
   - You can implement commands that do nothing, this is done to avoid the use of null and a good starting point for unitialized commands

- Pro's con's

   - Decoupling
   - Commands can be run at a later date
   - Commands can be batched and run over a large dataset like patches which allow it to create lesser snapshots
	
- Example

```java

	class Command{
		void execute();
	}

	class CalculateHighRiskCommand(){
		int riskAmount = 10000000;
		RiskReceiver receiver;
		public CalculateHighRiskCommand(RiskReceiver receiver){
			this.receiver = receiver;
		}

		void execute(){
			receiver.calculateRisk(riskAmount);
		}
	}

	class RiskReceiver{
		void calculateRisk(int risk);
	}

	class HighRiskPunterReceiver{
		void calculateRisk(int risk){
			if(risk * specialFormula < acceptableRisk)
			{
				buyIntoIt();
			}
		}
	}

	class Invoker{
		runRisks();
	}

	class StockBroker{
		Command[] commands
		runRisk(int i){
			commands[i].execute();
		}
	}

	class Client{
		void main(){
			RiskReceiver receiver = new HighRiskPunterRecevier();
			Command command = new CalculateHighRiskCommand(receiver);

			Invoker invoker = new StockBroker(Lists.array(command))
			invoker.runRisk(0);
		}
	}

```




### Adapter Pattern
- What is it? 

   - Also known as the wrapper pattern, it converts an interface of a class to another that a client expects. Adapters let classes work together that otherwise wouldn't be able to.

- How, when and why?

   - Compose the object that needs to work with the client (the plug) into the adapter, implement the required interface that the socket requires, translate each method into somthing the plug can do. If the plug cant operate that method, then throw a runtime exception like UnsupportedOperationException.
   - You use this pattern when you have classes that you can't or don't wish to change/refactor
   - If you've got classes that you can't change, such as an api class and you don't wish to change the code that will use it, you can use an adapter
   - There are two types of adapters object and class based.
   - Object is described above with the use of compose
   - Class isn't possible in java as it requires you to inherite from multiple classes

- Pro's con's

   - Lets you keep existing code without refactoring
   - Can be quite complex as the number of methods or behaviours could be huge
   - Not always viable as the compsed object might not have the required behaviour for all of the require methods
	
- Example

```java

	class SocketUK{
		UkStylePlug plug;
		void setPlug();
		void shortPlug(){
			plug.short();
		}

		void powerOn(){
			plug.switchOn();
		}
	}

	interface UkStylePlug{
		void short();
	}

	class PlugUK implements UkStylePlug{
		void switchOn(){}
		void short(){
			System.out.println("Fuse is tripped, appliance is ok");
		}
	}

	class PlugUS{ //this plug cannot be used with a UK Socket
		void switchOn(){
		}
	}

	class TravelAdapter implments UkStylePlug {
		PlugUS plug;

		void short(){
			System.out.println("not doing anything, this might end with the appliance setting on fire!")
		}
		void switchOn(){
			plug.switchOn();
		}
	}

	class Client{
		void main(){
			SocketUk socket = new SocketUk();
			socket.setPlug(new PlugUS());
			socket.switchOn(); //should now call the us plug switch on
			socket.shortPlug(); 
		}
	}

```





### Facade Pattern
- What is it? 

   - A unified interface to a set of interfaces from an underlying subsystem.
   - A more simple interface that makes working with the subsystem alot easier.
   - A layer that ochestracts the usage of services

- How, when and why?

   - Compose a set of interfaces that work together into a class, have high level (abstract but not in the java sence) methods that combine the usage of methods on each of the composed interfaces
   - You use the facade to simplify the use of an group of interfaces
   - It allows a client to know about abstract things which leaves the facade to know about the nitty gritty
   - Facades are free to add any additional behaviour too (think converter/populators)

- Pro's con's

   - Decouples the client code from the underlying subsystem
   - Can simplify interations - one point of interaction
   - Improve code refactoing
   - Doesn't stop the client using the subsystem if needed
	
- Example

```java

	class MakeCakefacade{
		void PrepOven(){
			oven.switchOn();
			oven.turnDial();
		}
		void mixIngredience(){
			flourService.pourFlour();
			eggService.crackEggs();
			mixerService.mix();
		}
	}
	
	class CakeClient{
		void makeCake(){
			makeCakeFacade.prepOven();
			makeCakeFacade.mixIngredience();
		}
	}

```



### Template Pattern
- What is it? 

   - Defines a skeleton of an algorithm in a method which uses steps that can be defined in the class or delegates to a subclass for it
   - Has optional steps in the form of hooks

- How, when and why?

   - Define a class (can be abstract) that holds a template method (final) that calls a set of other methods (the steps) which can be implements in the current class of in a sublass
   - Define some steps as hooks that are optional. The class can implement them but leave it empty. These provide place holders for a subclass to do additional work
   - Use this pattern when you want to define a framework that provides some steps but not all, or all steps and allows it to be overridden
   - This promotes code reuse as your finding steps that are common and placing them in the parent class

- Pro's con's

   - Code reuse
   - example of the dont' call us, we'll call you design principle - the parent class decides what to call and when
   - Can extends as well as override steps of an algorithm
	
- Example

```java

	class PizzaMaker{
		final void makePizza(){ //lock the template down as final
			preHook();
			prepareDough();
			putBaseOn();
			putToppingsOn();
			bake();
			postHook();
		}

		void preHook(){} //optional steps that can be overridden
		void postHook(){}

		void prepareDough(){
			sout("flatten out a base in a circle");
		}
		final void putBaseOn(){ //don't allow this step to be overriden
			sout("spread tomato base");
		}
		void toppingsOn(){
			sout("cheese and ham");
		}
		void bake(){
			sout("bake for 20 mins");
		}
	}

	class PepperoniPizzaMake extends PizzaMake{
		void toppingsOn(){
			sout("cheese and pepperoni");
		}
		void bake(){
			sout("bake for 30 minutes");
		}
		void postHook(){
			sout("add extra pepperoni");
		}
	}
```

### Iterator Pattern
- What is it? 

   - Provides a way to iterate over a single aggregate (collection) without exposing underlying representation (type)
   - These are known as external iterators. Internal ones are ones that the collections use to iternate internally

- How, when and why?

   - Using an iterator composes the underlying type keeping count of where it is in the collection. By providing methods like hasNext and next it will be able to walk through the collection
   - Have the class that contains the collection generate the iterator and provide it to the client. Done typically via a createIterator method
   - You use this when you have to use the same code to iterate over 2 collection types that cannot or wont change
   - The JDK provides an iterator interface as well as a ListIterator that allows you to go forward and back
   - The JDK iterator can also remove elements which will need to shift all elements once used
   - If you don't want the collection to deal with the iteration then use this pattern

- Pro's con's

   - Simple interface
   - Could use the enhanced for loop with the collections interface instead (or Collection class)
   - Decouples the client from a collection and makes the client only know about an Iterator
   - Allows you to iterate over almost any collection type without knowing the type
	
- Example

```java

	Just use the JDK iterator

```



### Composite Pattern
- What is it? 

   - A pattern that stores a group of things in a tree structure and allows you to treat all elements (both nodes and leaves) the same way (uniformly)

- How, when and why?

   - A composite(node) and object(leaf) implement the same interface/abstract class. For methods that don't make sense, you can return null/false or thrown an unsupported exception. The nodes will contain a collection of the same interface type, allowing you to contain more nodes of nodes (recuresion) or leaves.
   - If you want to be able to travese a group of things in a uniform way without making the client aware of the underlying types
   - Safe composites are ones that can check the type(instance of) of the component to see if it can actually run a method on it. but this loses the transparity and makes the client aware of other types.
   - nodes can also point to parents making two way traversal

- Pro's con's

   - Allows you to ignore the type and just run the operations on all elements
   - If iterating over a large tree to find things, you could have performance issues (this could be mittigate with caching)
   - If you need ordering you would need to think logic in the adding and removing of nodes
	
- Example

```java

	interface Boxable{ //the common interface that the client will use
		void print();
		void add(boxable box);
		String getSize();
		String getItemName();
	}

	class Box implements Boxable{
		List<Boxable> boxes;
		int size;
		Box(int size){
			this.size = size;
			boxes = new arrayList<>();
		}
		void print(){
			sout('im a box with size ' + getSize());
			for(Boxable b : boxes){
				b.print(); //recursive call that will call all boxes within as well as box items print()
			}
		}
		void add(Boxable boxable){
			this.boxes.add(boxable);
		}
		String getSize(){
			return this.size;
		}
		String getItemName(){
			throw new UnsupportedException();
		}
	}

	class BoxItem implements Boxable{
		String itemName;
		BoxItem(String itemName){
			this.itemName = itemName;
		}
		void print(){
			sout('im a box item with name ' + getItemName());
		}
		void add(Boxable boxable){
			throw new UnsupportedOpperation();
		}
		String getSize(){
			throw new UnsupportedOpperation();
		}
		String getItemName(){
			return this.itemName;

		}
	}
	
	class Client(){
		static void main(){
			Box mainBox = new Box(2);
			Box box2 = new Box(1);
			Box box3 = new Box(4);
			Box box4 = new Box(10);
			Box box5 = new Box(9000);

			BoxItem item1 = new BoxItem('derp');
			BoxItem item2 = new BoxItem('herp');
			BoxItem item3 = new BoxItem('burp');

			box2.add(item1);
			box2.add(box3);
			box2.add(box4);

			box4.add(box5);

			box5.add(item2);
			box5.add(item3);

			mainBox.add(box2);

			mainBox.print();

		}
	}
```

### State Pattern
- What is it? 

   - A component that holds a single state but can interchange between many. By doing so will appear to change behaviour has it will be based on state
   - Like the strategy pattern in that behaviour is encapsulated but the intent is that in the strategy pattern, the behaviour is typically locked at instantiation (doesn't mean it can't change at runtime) but state pattern the intent is that the state will be continually changing and therefore its behaviour.

- How, when and why?

   - A context will hold a single state but also a list of other states it can transition to. When runing a behaviour, the context will delegate to the current state.
   - When delgation of behaviour occures, its either up to the context to transition to another state or the state itself. If the state chooses to transition, it will need a reference to the context (usually provided during instantiation) and use it's getters and setters to set the new state.
   - All states will need to either implement the same Interface or abstract class 
   - Use this pattern when you seem to be using alot of large if/else if/else statements that run behaviour depending on state.
   - Using this pattern will help with the maintainability of the context, it allows you to implement new states relativly easily and keep classes focused on a singular responsibility.

- Pro's con's

   - Increaases the amount of classes / increase in complexity
   - More maintainable
   - More focus to one class one responsibility
	
- Example

```java

	interface State{
		drinkManaPotion();
		useMegaMagic();
		useSmallMagic();
	}

	class NoManaState implements State{
		Mage mage;
		public NoManaState(Mage mage){this.mage = mage;}
		drinkManaPotion(){
			sout('mana restored')
			this.mage.setState(this.mage.getFullManaState);
		}
		useMegaMagic(){
			sout('no mana left, cant use mega magic')
		}
		useSmallMagic(){
			sout('no mana left, cant use small magic')
		}
	}

	class LowManaState implements State{
		Mage mage;
		public NoManaState(Mage mage){this.mage = mage;}
		drinkManaPotion(){
			sout('mana restored')
			this.mage.setState(this.mage.getFullManaState);
		}
		useMegaMagic(){
			sout('no mana left, cant use mega magic')
		}
		useSmallMagic(){
			sout('uses small magic')
			this.mage.setState(this.mage.getNoManaState);
		}
	}
	class FullManaState implements State{
		Mage mage;
		public NoManaState(Mage mage){this.mage = mage;}
		drinkManaPotion(){
			sout('mana restored')
			this.mage.setState(this.mage.getFullManaState);
		}
		useMegaMagic(){
			sout('uses mega magic')
			this.mage.setState(this.mage.getLowManaState);
		}
		useSmallMagic(){
			sout('uses small magic')
		}
	}

	class Mage {
		State noManaState;
		State lowManaState;
		State fullManaState;

		State currentState = fullManaState;

		public Mage(){
			noManaState = new NoManaState(this);
			lowManaState = new LowManaState(this);
			fullManaState = new FullManaState(this);
		}
		useMegaMagic(){
			currenttate.useMegaMagic();
		}
		useSmallMagic(){
			currentState.useSmallMagic();
		}
		drinkManaPotion(){
			currentState.drinkManaPotion();
		}

	}
	
```

### Proxy Pattern
- What is it? 

   - Provides a surrogate/placeholder for a real object for which it tries to control access to
   - There are many different types of proxies out there, some of which are: remote (proxies to objects in another heap / another machine - Gumball remote calls), virtual (controls access to an object or resources that are expensive - CD image example), protection (controls access to an object using invocation handlers - Dating site to person object)
   - Much like the Decorator pattern, in which the an object is composed but the intensions are different. Decorator adds behaviour but Proxy controls access
   - Many more types of proxies exist, Caching proxy, COmplexity (Facade) proxy, copy on write, synchronization proxy

- How, when and why?

   - To create a Remote proxy, you can use Java's RMI (remote method invocation) to generate stubs (the local proxt) and skeletons (the recieving object on the other side of the network) to call methods on objects over the network on a seperate heap. 
   -- To do this you would need to create an interface, an implementation to the interface, generate stub and skeleton using the java command "rmic", start the rmi registry, start the remote service.
   -- Interface must extend the "Remote" interface from the java.rmi package
   -- All methods on the interface must throw a RemoteException
   -- method arguments and return types must all be serializable
   -- The implementation to the interface must also extend the UnicastRemoteObject with a no arg constructor
   -- Register the object of the implementation with the RMI Registry with Naming.rebind("theImplService", myImpl);
   -- Use rmic on the implementation to generate the stub and skeleton

   - Virtual proxies are placeholders that stand in for other objects that are typically expensive to create to use methods on
   - The proxy needs to implement the same interface as the real object
   - Compose the real object
   - Do stuff when the implemented method is called but then will do the real call when available. This can typically be done via starting a new thread

   - Protection proxies are supported out of the JDK using the java.lang reflect package.
   - Like the Virtual proxy, you have a proxy that immplements the same interface as the real object but instead of composing the real object, you compose an Invocation habdler which holds the real object.
   - Once a method is called on the proxy, it immediatly calls the Invocation handlers invoke method, with the proxy object, method name and method parameters
   - The invoke method then uses these arguments to decide whether it should call a method on the real object that it holds

   - You use this design pattern mainly to control access to another object

- Pro's con's

   - Like Decorators it can increase the amount of classes
   - Many proxy types
   - 
	
- Example

```java
:(
	
```


### Telescoping pattern

- What is it? 

   - A pattern that uses multiple constructors with varying paramters, each setting a different or more private members
   - Not used much anymore

- How, when and why?

   - Multiple constructors
   - Use it when you have multiple optional variables that can be set

- Pro's con's

   - Can lead to maintenance issues
   - Can easily forget what each parameter does
	
- Example

```java

public class Recipe {
	private boolean flour;
	private boolean eggs;
	private boolean water; 
	private boolean sugarl
	private boolean colouring;

	public Recipe(boolean flour){};
	public Recipe(boolean flour, boolean eggs){};
	public Recipe(boolean flour, boolean eggs, boolean water){};
	...//and so on
}
	
```



### Builder pattern

- What is it? 

   - Pattern that help construct objects with multiple properties
   - Gives a nice concise way to create, validate and set properties

- How, when and why?

   - You will have 2 classes, the builder and the thing your trying to build (target)
   - The builder will typically be an inner public class of the target
   - The Target would typically have a private constructor so the builder can only create an instance for you
   - the Target can also be immutable, so properties are final and you dont need to provider setters
   - the Builder will always return itself after running a method
   - the Builder will also provide a build method to construct the target with the properties it has received
   - use it when you start using multiple constructors in the target class

- Pro's con's

   - Extra class to maintain
	
- Example

```java
public class Target
{
	private String param1;
	private String param2;
	private Target(Builder builder){
		this.param1 = builder.param1;
		this.param2 = builder.param2;
	}

	public class Builder{
		private String param1;
		private String param2;
		public static getInstance(){ return new Builder();}
		public Target build(){
			return new Target(this);
		}
		public Builder param1(String param1){this.param1 = param1;}
		public Builder param2(String param2){this.param2 = param2;}
	}
}
```








