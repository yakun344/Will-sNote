# Design Principles
_update Mar 12, 2020_

---
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

简单来说Open Closed principle就是一个class的功能可以被extend，但是不能通过修改这个class或者module的code自身来实现。对扩展开放，但是对修改关闭。
