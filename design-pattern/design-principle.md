# Design Principles
_update Mar 12, 2020_

---
**SOLID 五大Design Principles:**

- (S) Single Responsibility
- (O) Open/Closed
- (L) Liskov Substitution
- (I) Interface Segregation
- (D) Dependency Inversion


## 1. Single Responsibility
&nbsp;&nbsp;&nbsp;&nbsp;**"A class should have one, and only one, reason to change"**

* ### Introduction
使用Single Responsibility可以降低实现难度，并且避免后续change带来的side effects。当我们在开发的过程中遇到需求变化时，如果每个responsibility对应一个独立的class，那么只有当responsibility改变的时候才需要change这个class。但是如果一个class对应了多个responsibility，则它需要被修改的频率就会提高。同时，对一个class的修改可能会导致其他依赖于它的类需要进行相应的修改，同时有可能带来更多的side effects。
* ### 如何注意
在遇到需求变化的时候，有时候最简单的办法就是在现有的class中添加一个方法，或者在现有的方法中增加一些代码，但是在做这些的时候很容易违背Single Responsibility原则。我们可以在准备修改class之前问自己一个问题：“What is the responsibility of your class/component/microservice？”。如果答案中包含“and”，则需要 take a step back and rethink the current approach。
* ### Example
**1. Java Persistence API（JPA）Specification**  
它只有一个responsibility：Defining a standardized way to manage data persisted in a relational database by using the object-relational mapping concept.  
**2. JPA EntityManager**  
Its responsibility is to manage the entities that are associated with the current persistence context.  
**3. JPA AttributeConverter**  
It converts a data type used in your domain model into one that your persistence provider can persist in the database.
* ### 拓展思路
有时候当最外层的component/class的功能太过high level或者太过复杂，我们可以利用诸如delegation之类的模式将一个大的responsibility分成多个小的responsibility，每个class仍然只负责一个具体的responsibility。
> 例如我们需要实现一个`class ShopController` 的 `pay` 方法，我们需要做很多事情，包括获取所有该用户要购买的商品，获取用户的支付信息，支付，检查支付结果并重定向用户访问的页面。如果我们把每个步骤的逻辑都分别在`pay()` 方法中实现，则这个方法本身的responsibility就太多了，于是我们可以将每个部分分别交给格子的class来负责，在`pay` 方法中我们只需要调用各个接口：
> ```java
> class ShopController {
>
>  // This runs when the user presses “pay now”
>  public function pay()
>  {
>    // Create a basket to handle our session basket data
>    $basket = new Basket($_SESSION[‘basket’]);
>
>    // Get the current user
>    $user = (new Authenticator)->currentUser();
>
>    // Create a payment request that holds payment data
>    $paymentRequest = new PaymentRequest($_POST);
>
>    // Pay and redirect the user (this throws an exception if there are any issues)
>    return $basket->payAndRedirect($user);
>  }
> }
> ```
> 这里的 `pay` 方法的responsibility是：“Taking the request data and passing it to the necessary specialists”
>
> 下面是一个反例：
>```java
>class ShopController {
>
>  // This gets run when the presses “pay now”
>  public function pay()
>  {
>    // Get the basket out of the session
>    $basket = $_SESSION[‘basket’];
>
>    // Get the user out of the session
>    $user = $_SESSION[‘user’];
>
>    // Initialise a variable that will hold the price to pay
>    $totalToPay = 0;
>
>    // Add up the price of all the items in the basket
>    foreach ($basket[‘items’] as $item) {
>      $totalToPay += $item[‘price’]         
>    }
>
>    // Get the credit card information the user sent through
>    $creditCardInformation = $_POST[‘credit_card_info’];
>
>    // Get the payment gateway key and post the customer, price and card data to stripe
>    $paymentGatewayKey = ‘A82hDiha9hhdaonldtumpr2390Jf’;
>    $paymentResponse = API::post(‘stripe’, [
>      ‘credit_card’ => $creditCardInfo,
>      ‘customer’ => $user,
>      ‘price’ => $totalToPay
>    ]);
>
>    // If the payment worked, take them to the payment gateway to confirm
>      if ($paymentResponse[‘success’]) {
>      return redirect($paymentResponse[‘redirect_url’]);
>    } else {
>      // Otherwise, show an error
>      throw new Exception(“There was an error when attempting to pay. - ” . $paymentResponse[‘error_message’]);
>    }
>  }
>} 
>```
> 在反例中，`Pay` 方法做了如下事情：  
> 1. Getting the user from the session  
> 2. Handling post data  
> 3. Making requests to the payment gateway  
> 4. Summing the total cost of the basket up  
> 5. Ensuring the payment was successful and throwing an exception if not  
> 6. Redirecting the user to the payment gateway if successful  
> 7. Storing the payment key (This is a terrible security risk - your payment key is now in your GitHub repository. Great job, George!)  
> 因此，它明显违背了 Single Responsibility principle


## 2. Open Closed Principle
&nbsp;&nbsp;&nbsp;&nbsp;**"The most important principle of object-oriented design."  --Robert C.Martin**  
&nbsp;&nbsp;&nbsp;&nbsp;**"Software entities(classes, modules, functions, etc.) should be open for extension, but closed for modification."  --Bertrand Meyer**  
&nbsp;&nbsp;&nbsp;&nbsp;**"You should be able to extend the behavior of a system without having to modify that system."  --Bob Martin**

* ### Introduction
简单来说Open Closed principle就是一个class的功能可以被extend，但是不能通过修改这个class或者module的code自身来实现。对扩展开放，但是对修改关闭。但是需要注意的是由于inheritance是具有强耦合的，而且super class和sub class之间的关系是在编译时确定，不利于在运行时切换实现类，因此在开发中一般利用Interface（abstraction）以及composition来实现对于功能的拓展。
* ### Example
> 举例说明，例如我们需要编写一个软件来控制咖啡机做咖啡，于是我们可以定义两个class `class CoffeeApp` and `class CoffeeMachine`，当需求只需要适配一款咖啡机的时候，我们的程序可能是这样：
> ```java
> public class CoffeeMachine {
>   private Coffee getCoffee() {//...}
>   private void brewCoffee(Coffee) {//...}
>   public void makeCoffee() {
>     Coffee coffee = getCoffee();
>     brewCoffee(coffee);
>   }
> }
> public class CoffeeApp {
>   private CoffeeMachine machine;
>   public CoffeeApp() {
>     this.machine = new CoffeeMachine();
>   }
>   public void prepareCoffee() {
>     CoffeeMachine.makeCoffee();
>   }
>   public static void main(String[] args) {
>     CoffeeApp app = new CoffeeApp();
>     app.prepareCoffee();
>   }
> }
> ```
> 这样的实现可以满足当前要求，但是如果将来需求变化，需要适配多种咖啡机，这里的getCoffee以及brewCoffee就需要重新实现，同时还有可能加入研磨grind等步骤。为了适应新变化，我们就需要对现有对代码做refactor，这里会用到open closed principle。
>
> 首先我们需要让代码可以被extend，则需要将一些logic抽象出来，这里我们发现在CoffeeApp中实际上只需要调用CoffeeMachine::makeCoffee方法就够了，于是我们可以按照如下方法来实现：
> ```java
>   Interface BasicCoffeeMachine {
>      void makeCoffee();
>   }
>   public class CoffeeMachine1 implements BasicCoffeeMachine {
>      private Coffee getCoffee() {//...}
>      private void brewCoffee(Coffee) {//...}
>      @Override
>      public void makeCoffee() {
>         Coffee coffee = getCoffee();
>         brewCoffee(coffee);
>      }
>   }
>   public class CoffeeMachine2 implements BasicCoffeeMachine {
>      private Coffee getCoffee() {//...}
>      private void brewCoffee(Coffee) {//...}
>      private Coffee grindCoffee(Coffee) {//...}
>      @Override
>      public void makeCoffee() {
>         Coffee coffee = getCoffee();
>         coffee = grindCoffee(coffee);
>         brewCoffee(coffee);
>      }
>   }
> public class CoffeeApp {
>   private BasicCoffeeMachine machine;
>   public CoffeeApp(BasicCoffeeMachine machine) {
>     this.machine = machine;
>   }
>   public void prepareCoffee() {
>     CoffeeMachine.makeCoffee();
>   }
>   public static void main(String[] args) {
>     // 使用第一种咖啡机
>     CoffeeApp app = new CoffeeApp(new CoffeeMachine1);
>     app.prepareCoffee();
>
>     // 使用第二种咖啡机
>     CoffeeApp app2 = new CoffeeApp(new CoffeeMachine2);
>     app.prepareCoffee();
>   }
> }
> ```
> 这样refactor之后，原本对CoffeeMachine就有了可扩展性，我们可以通过在CoffeeApp的constructor中传入不同的concrete CoffeeMachine实现来做到兼容不同的咖啡机。需要注意这里的设计细节，CoffeeApp和CoffeeMachine之间的关系是 **has a** 的关系，利用了Composite的模式，而不是采用让CoffeeApp来继承某个CoffeeMachine，这样才使得动态替换CoffeeMachine instance成为了可能。

## 3. Liskov Substitution Principle
**"Let Φ(x) be a property provable about objects x of type T. Then Φ(y) should be true for objects y of type S where S is a subtype of T."  --Barbara Liskov**

* ### Introduction
用人话来说，就是说subclass的object应该可以用来替换super class的object。具体来说，一个subclass的overriden method必须接受和super clas的相同method有相同的parameter，subclass中方法的validation rule可以比super class中的宽松，而不可以更严格。相对应的，subclass中方法的return value可以是super class中方法return value的subclass，或者是super class中方法可能return value的subset。
<br><br>
这个原则在实际开发中其实用到的地方非常多，我们经常需要将子类的object传入父类或者接口类型的argument中。例如在Open/Closed原则中，我们可以通过继承来拓展一个父类的功能，之后在调用的时候，我们可以将我们新建的subclass的object当作原本的base class的object来在原本的地方使用。
<br><br>
岔开一点话题，在Java中当我们在sub class中override父类方法的时候，return type只能是父类方法return type 的sub class，也就是assignable的。而access modifier只能比父类方法的更加visible而不能less visible。这些规则有Java语言本身的限制，但是更多遵循Liskov Substitution的规则涉及到具体业务逻辑则需要在写代码的时候特别注意。


## 4. Interface Segregation Principle
**“Clients should not be forced to depend upon interfaces that they do not use.”  --Robert C.Martin**

* ### Introduction
这个原则的作用和Single Responsibility类似，都是为了将程序划分成多个独立部分，从而减少side effect以及降低修改代码的频率。
<br><br>
简单来说，这个原则要求我们在设计Interface的时候要将不同的功能隔离开来成为不同的interface，当需求变化或者需要添加新功能的时候，要思考是否需要create新的interface而不是一味的在现有的interface中添加新的方法。因为如果我们修改现有的interface，所有implement这个interface的subclass都需要相应修改，同时还有可能需要对client进行相应修改。另一方面，在选择加入interface中的方法的时候要思考，是否所有client都需要实现这么多方法，哪些是非必要的。
<br><br>
当我们设计interface的时候应该倾向于将可以分为不同类的功能放入不同的interface，这样在写实现类的时候就可以根据需求选择合适的interface的组合来实现。

* ### Example
> 例如上面咖啡机的例子，如果我们除了想要控制咖啡机之外还想控制另一种咖啡机煮茶，原本的BasicCoffeeMachine接口就会变成这样：
> ```java
> public Interface BasicCoffeeMachine {
>   void makeCoffee();
>   void makeTea();
> }
> ```
> 但是这样的话无论是哪种咖啡机都必须要实现这两个方法，即使"CoffeeMachine1"并不能用来煮茶。因此更好的做法其实是将其分割开来，这样可以煮茶的咖啡机就 implement "BasicTeaMachine" 接口，而不能煮茶的咖啡机类也不需要改变。


## 5. Dependency Inversion Principle
* ### Introduction
这个原则的基本思想并不负责：提供complex logic的high level module应该可以轻易被reuse，不应被提供utility features的low level module的变化所影响。因此需要引入abstraction来decouple high level module and low level module。
<br><br>
**Robert C.Martin's definition:**  
1. High-level module should not depend on low-level modules. Both should depend on abstractions;
2. Abstractions should not depend on details, details should depend on abstractions;
