


# OOP   

Object-Oriented Programming (OOP) is a programming paradigm based on the concept of "objects" that
contain data (attributes) and behaviors (methods).   

…   
> Pillars example from dart   

- encapsulation ==> data hiding =>( \_ + setters,getters)   
- polymorphism  ==> method overriding ,Text and Container are Widget, sealed class   
- inheritance  ==>  extending StatelessWidget or StatefulWidget   
- abstraction ==> database can be from firebase/supabase   
   
# 1. Encapsulation   
**Definition:** Bundling attributes and methods into a single unit (class) while hiding implementation details.   
**Purpose:**   
- Protecting data from unauthorized access and modification   
- Controlling how data is accessed and modified   
- Providing a clear interface for usage   
   
<div dir="rtl">
يعني الـ بيانات بتاعة الكلاس تكون محمية ومفيش حد يقدر يغيرها أو يوصلها مباشرة غير من خلال "واجهة استخدام" (Methods أنا معرفها)
</div>

**How Dart does it:**   
> Make fields private by prefixing with  _                                                                                                                   
> Expose only what you want with validation (getters, setters).   


**Example:**

   
```dart
class BankAccount {
  double _balance; // private field

  BankAccount(this._balance);

  // Public method with validation
  void deposit(double amount) {
    if (amount > 0) {
      _balance += amount;
    }
  }

  // Getter for read-only access
  double get balance => _balance;
}
```

**Why it matters:** Consumers can't directly set `\_balance` to a negative value—they must go through `deposit()`, so your invariants stay intact.   

# 2. Polymorphism   
    
**Definition:** "Many forms" - the ability to treat objects of different classes as objects of a common superclass, offering flexibility.   
**Principle:** An object behaves according to its actual type at runtime.   
**Requirements:**   
1. A superclass or interface exists   
2. Other classes inherit from it   
3. Each class overrides methods in its own way   
4. You interact with objects using the general type (not the actual type)   

<div dir="rtl">
والObject يتصرف حسب نوعه الحقيقي في الـ Runtime
</div>
   
**Types:**   
- **Static Polymorphism** → Overloading (compile time)   
- **Dynamic Polymorphism** → Overriding (runtime)    
   
<div dir="rtl">
في فلاتر ال Text , Container ,Row كلهم من كلاس widget بس كل واحد بيتعرض بطريقته الخاصه

كل نوع من الـ Widgets بـ override دالة `build()` بطريقته
</div>
    
### Overriding vs Overloading   
**Overriding:**   
- Changing the behavior of a method from the superclass   
- Requires inheritance (parent and child relationship)   
- Same method name and same parameters   
- Works at runtime   
   
**Overloading:**   
- Creating multiple methods with the same name but different parameters   
- Works at compile time   
- **Important:** Dart does NOT support Method Overloading directly   
- Alternative: Use Optional Parameters, Named Parameters, or Default Values   
   
```dart
class Animal {
  String name;
  Animal(this.name);
  
  void makeSound() {
    print("$name makes a sound");
  }
}

class Dog extends Animal {
  Dog(String name) : super(name);
  
  @override
  void makeSound() {
    print("$name barks: Woof woof!");
  }
}

class Cat extends Animal {
  Cat(String name) : super(name);
  
  @override
  void makeSound() {
    print("$name meows: Meow!");
  }
}

void makeAnimalSpeak(Animal animal) {
  animal.makeSound(); // Correct method is called based on actual type
}

void main() {
  Animal myDog = Dog("Rex");
  Animal myCat = Cat("Cleo");
  
  List<Animal> animals = [myDog, myCat];
  
  for (var animal in animals) {
    animal.makeSound(); // Polymorphism in action!
  }
  
  makeAnimalSpeak(myDog); // Rex barks: Woof woof!
  makeAnimalSpeak(myCat); // Cleo meows: Meow!
}
```
   
###     
\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   
   
# 3. Inheritance   
   
> Definition: The ability to create a new class that inherits properties and behaviors from an existing class.   

<div dir="rtl">

### Inheritance = إعادة استخدام الكود بدل ما تعيد كتابته   

### يعني تقدر تعمل Class جديد يورث خصائص وسلوكيات من Class موجود بالفعل   

### وتضيف عليه حاجات جديدة لو حبيت.   

### تخيل عندك كلاس اسمه Employee   

### فيه حاجات مشتركة زي:   

### name, email, login()   

### بعد كده عندك أنواع مختلفة من الموظفين:   

### Manager, Developer, Designer...   

### كلهم ليهم نفس الخصائص الأساسية، بس كل واحد فيهم ليه حاجة مميزة.   

### فـ بدل ما تكتب نفس الأكواد في كل كلاس،   

### تخليهم كلهم يورثوا من Employee وتزود عليهم لو احتاجت.   

### ليه بنستخدم Inheritance   

</div>

- **Code Reusability:** مش محتاج تكرر الكود في كل مكان   
- **Organized Structure:** الكلاسات ليها علاقة منطقية ببعض   
- **Extensibility:** تقدر تطور الكود بسهولة لما المشروع يكبر   
   
   
**inheritance ==> DRY**

multiple classes that share common fields and methods, the best practice is to create a superclass that contains the shared logic.   

## Inheritance vs Composition   
   
<div dir="rtl">

**Composition => between 2 object**

**inheritance => between 2 classes**

</div>

### Inheritance   
- **"is-a" relationship**   
- Example: `Dog is an Animal`   
- Use when: There's a clear hierarchical relationship   
   
### Composition   
- **"has-a" relationship**   
- Example: `Car has an Engine`   
- Use when: You want more flexibility and avoid tight coupling   
   
```dart
// Engine class
class Engine {
  void start() {
    print('Engine started');
  }
}

// Wheels class
class Wheels {
  void rotate() {
    print('Wheels rotating');
  }
}

// Car class using Composition
class Car {
  final Engine engine;
  final Wheels wheels;

  Car(this.engine, this.wheels);

  void startCar() {
    engine.start();
    wheels.rotate();
    print('Car is ready to go!');
  }
}

void main() {
  var carEngine = Engine();
  var carWheels = Wheels();
  var myCar = Car(carEngine, carWheels);
  myCar.startCar();
}
```

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   
###    
# 4. Abstraction   
**Definition:** Hiding complex implementation details and showing only necessary features through well-defined interfaces.   

<div dir="rtl">

**Abstraction = أنت شايف اللي محتاجه، والباقي مش متشاف**

يعني بتتعامل مع واجهة بسيطة (interface / function)

من غير ما تحتاج تعرف الكود الداخلي بيعمل إيه بالظبط

</div>

>    

- making a contract class that have abstract method then the at any modification in this contract every subclasses must override this change in there class   
   
>    

- hides complex implementation details, showing  only necessary features through well-defined interfaces   
   
>    

- cant take object(instance) from it   
   
>    

### Abstract Class vs Interface   
**Abstract Class:**   
- Contains one or more methods without implementation   
- Can have methods with implementation   
- Using `extends` forces you to implement only abstract methods   
   
**Interface:**   
- All methods without implementation   
- Using `implements` forces you to implement ALL methods and members   
   
**Real-World Example:** Database can be from Firebase or Supabase - you interact with the same interface regardless of implementation.   
      
```dart
abstract class Database {
  void connect();
  void disconnect();
  void query(String sql);
}

class FirebaseDatabase extends Database {
  @override
  void connect() {
    print("Connecting to Firebase...");
  }
  
  @override
  void disconnect() {
    print("Disconnecting from Firebase...");
  }
  
  @override
  void query(String sql) {
    print("Executing Firebase query: $sql");
  }
}

class SupabaseDatabase extends Database {
  @override
  void connect() {
    print("Connecting to Supabase...");
  }
  
  @override
  void disconnect() {
    print("Disconnecting from Supabase...");
  }
  
  @override
  void query(String sql) {
    print("Executing Supabase query: $sql");
  }
}
```

==================================================================================   

### Mixins   
A way to reuse a class's code in multiple class hierarchies without using inheritance. Useful when you want to share behavior among unrelated classes.   
```dart
mixin Swimming {
  void swim() {
    print("Swimming...");
  }
}

mixin Flying {
  void fly() {
    print("Flying...");
  }
}

class Duck with Swimming, Flying {
  String name;
  Duck(this.name);
}

void main() {
  var duck = Duck("Donald");
  duck.swim();  // Swimming...
  duck.fly();   // Flying...
}
```
 --- 
   
### Extensions   
Allow you to add new functionality to existing libraries (even core Dart libraries) without modifying their source code.   
```dart
//<StringParsing> is optional
extension StringParsing on String {
  int? toIntOrNull() => int.tryParse(this);
}

// Usage

  print("123".toIntOrNull()); // 123
  print("abc".toIntOrNull()); // null


extension NumberParsing on String {
  int intParse() {
    return int.parse(this);
  }
}
// Usage
print("33".intParse());
```
 --- 
   
## OOP in Flutter   
   
1. **Every UI element is a Widget** - `StatelessWidget` and `StatefulWidget` are abstract classes   
2. **Create custom components** - by extending these classes (Inheritance)   
3. **Instantiation** - `Text('Hello')`, `Container()`, `Row()` are all instantiations of widget classes   
4. **Polymorphism** - The `build()` method you override is a prime example - Flutter calls `build()` on various widgets, and each renders itself differently   
   
===   
```dart
class User{
  //properties
  final String name;  // final means that the variable is immutable (value can be set only once,can be initialized in the constructor only)
  final int age;

  User(this.name, this.age);
}
```
   
=================
