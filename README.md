# SOLID Principles in Swift
SOLID is a mnemonic acronym named by Robert C. Martin used in Software Programming, It represents 5 principles of Object Oriented Programming.
- **S**ingle Responsibility Principle 
- **O**pen Closed
- **L**iskov Subsititution 
- **I**nterface Segregation
- **D**ependency Inversion 

These principle solve bad architecture problems like 
- **Fragility** — A change in one place breaks unexpected parts, its difficult to trace if there is no good test coverage.

- **Immobility** — A component is difficult to reuse in another project or use in multiple places of same project, because it has to many dependencies.

- **Rigidity** — Takes a lot of effort for change, because it affects several parts. 

These SOLID principle are not strict rules, but just guidelines to improve the quality of the your architecture.
Lets dig in the principles one by one

---

## Single Responsiblity Principle
> Each an every class you create/change should have only one responsibility

Lets see an example explaining it, I have to develop a messenger app screen to get the list of past conversations. So I have a **ConversationDataController** which gets me the array of previous Conversation objects.

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*mwovY8zOe6G90ZdifWXatQ.png)

How many responsiblity does this class have ? 
- Gets the conversation data form the API
- Parse and create conversation objects from API response
- Saves the conversation array to CoreData Database

If we have to implement NSURLSession for getting Json Data from API, and JSONSerialization to parse and Core Data Stack for saving to Database, this class becomes huge monster class.

we can solve this problem moving the each responsiblity down to different classes

I have created seperate classes for each responsiblity which ConversationDataController was handling, after segregating this is how our classes looks

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*vdkSmtvdrmVleBhiKOUrvQ.png)

This principle helps you to keep your classes as clean as possible, and we have a advantage of testing each and every API separately, with previous implementation we would not be able to test `requestDataFromAPI()`, `parseAndCreateConversationsFrom(data:Any)`and `saveToDatabase(conversations:[Any])` because they were private funtions inside ConversationDataController.


---

## Open Close Principle
> Classes and Modules should be open for extension but closed for modification

Strive to write code that doesnt have to change everytime the requirements change.

To understand this principle easily lets take a basic example of calculating the area of geometry shape.

Lets say we have requirement to calculate area of rectangle, so the **Rectangle** and related **AreaCalculator** class looks like shown below

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*pz2kvaDqYsO5rPDLi1LSBg.png)

Now the requirement has come to calculate area of not only Rectangle even for Circle, so our AreaCalculator class will look something like 

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*ylE1fuKzce20hoqjWe2w4A.png)

This will work but what if tomorrow one more requirement comes to calculate area of Triangle, the area funtion inside AreaCalculator class keeps growing with if else conditions, according to Open Close Principle we should not modify the existing class rather extent it.

One way of solving this problem is create a protocol (which is similar to Interface in Java) called Shape and declare area protocol method, implement the Shape protocols inside Rectangle or Circle or any other geometirc shape class, if it is confusing still may be checking the code below may clear it.

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*aq6r9bLOP08r2ChbRzpWgw.png)

so if there is requiremnt tomorrow to calculate the area of even Triangle, we should be able to achive it without modifying the existing AreaCalculator class just by extending the Shape Protocol for Triangle, This obeys the SOLID Open Close Principle.

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*afyrhL04fXt3qpsXj-Vw9A.png)

Lets see some real world example for Open Close Principle, suppose we have a requirement to calculate diffrent discount values for different customers say between normal and premium customers, applying OCP the classes architecture will look somthing like as shown below

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*xuNdP7YnugEmKPOHS1PXNQ.png)

---

## Liskov’s Subsitution Principle 
> Child classes should never break the parent class type definitions 

It means, we must make sure that new derived classes should extend the base classes without changing the base class behavior

As simple as, a subclass should override the parent class methods in a way that doesnt break the funtionality of base class from client point of view.

Lets see this with an example, we have two classes **Rectangle** and **Square** since Square is also a form of Rectangle, so Square is subclass of Rectangle

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*PF_LSEmGTgvhDZ4kquYy3g.png)

Here in the above block Square inherits the width and height property from Rectangle and overrides the width and height property to return the same height and width since it is Square.
Since our requirement is to calculate the area of Rectangle and Square, we will to use the **AreaCalculator** class.

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*obMQ4CsQm2eDYpEjet1wwQ.png)

Lets go a head and write Unit test cases to calulate the area of Rectangle and Square

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*C7PDlBAW-MSFg6CHSuBvng.png)

In above both test cases should pass since we have used instance of **Rectangle** to find area of retangle and instance of **Square** to find area of sqauare, now lets go a head and create a test case in which we will try to substitute object of Rectangle with object of Square, since Square inherits from Rectangle we should be able to find area of rectangle with Square instance.

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*BnySXUeUCeZujzyIMdEjlA.png)

In the above test case we have used instance of Square class to find the area of rectangle, since square inherits from Rectangle, but its very unfortunate that the above test case fails, the AreaCalculator returns the value of **16**, were as the expected area was **12**, this violates the Liskov’s Principle of Derived class (Square) breaking the parent class (Rectangle) funtionality of caluculating the area.
We can solve this problem breaking the inheritance 

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*b2cdsBeh783n45sE7dBa-g.png)

The solution seems similar to Open Close Principle, so based on above example we can conclude that violating **Liskov’s Principle** violates Open Close Principle as well. 


---

## The Interface Segregation Principle
> Make fine grained interface that are client specific

This principle solves FAT interface problems of Object Oriented Programming

A interface is called FAT when it has too many methods which contains more information than we really want.

Lets understand **Fat Interface in Protocols** with an example, we have a GestureProtocol with the method didTap: which a View would be using it to handle the Gesture

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*oPafL4wY5Y6IX1KenL4R_Q.png)

After sometime as usual the requirement came to handle more gesture like didDoubleTap: and didLongPress: so our GestureProtocol becomes

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*cZh1h-LubEhAabxW_VLxjg.png)

Again as usual not all the Views in our app requires all the gestures some View would require only didTap: gesture in that case our UserDetailView class which would need only didTap: gesture would look like 

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*bPSrS2T_4ouPk8DGW_Tl_g.png)

We can solve the problem using little protocols instead of a big ones, the below code snippet shows segregation of Gesture protocols into small ones

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*Xv3AJClspHSuRtPvXtEffw.png)

Other way we can solve this problem in swift 3.0 is using @objc key before the protocol declarations and protocol methods, which in turn gives us a options to declare a protocol method as optional, who ever wants can implement, the below code snippet shows the same.

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*68NKscKI01sLPMNkHj7GwQ.png)

Now lets dig into **Fat Interface in Classes** with an example, we have view in our app which displays the user profile photo, the two classes **User** and **UserProfileImageView** classes looks like shown below.

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*C8gyX1r997jVX9Oe_a8tJA.png)

Observing closely the above snippet, the **UserProfileImageView’s loadProfileFor(user:User)** function requires only the profileImageURL details, but unfortunately we are injecting too many informations in it, we can solve this problem using UserProfileViewDetails protocol which just has the information required by UserProfileImageView’s loadProfileFor(user:User), the below code will explain you detail.

![solarized vim](https://cdn-images-1.medium.com/max/1600/1*Qsk_ivU7BE3SAFJJlZb6HQ.png)

Now the UserProfileImageView’s loadProfileFor(user:UserProfileViewDetails)  which is the client has only the profileImageURL information with it to display the User Profile Image, which agrees with the Interface Segregation Principle
