
# Functional C#

The .net ecosystem features an object-oriented language, C#, and a functional counterpart, F#. I often find myself using functional concepts in my C# code though, in a more "hybrid" approach. There are various reasons that might constrain us to using C# including team experience or preference, heavily using async operations, or maintaining codebases already written in C#. Can we still benefit from functional patterns though?
## Functional programming
Let's try to define "functional programming". As a very short, and possibly overly simplistic, definition of functional programming I can offer this:
```
Functional programming is combining pure functions via functional composition.
```
Now we need to define two more concepts though:

- *What is a "pure" function?* It is a method that always produces the same result when provided with the same arguments. In other words, a method that does not manage internal state.

- *What is functional composition?* As the name suggests this is combining simpler functions together to make more complex ones.

Now that we have a definition, the question might arise, why would we care about applying functional patterns to an OOP language like C#? I see two big "families" of benefits in that:

- Not having to worry about internal state makes our code easier to test, debug, reason with and understand. I am fairly certain that any engineer who has ever worked maintaining an existing codebase can fully appreciate the importance of that.

- A key concept of functional programming is the idea of "making illegal states unrepresentable", meaning that any runtime state that an application can find itself in will correspond to valid objects in the business domain. We will expand on this later but hopefully the value of such an approach is apparent already.

Assuming the above is a good enough answer to "why" let's move on to the "how".

### Pure functions and internal state

Possibly the pattern that you might already be applying to your code is using immutable classes for requests/responses, DTOs etc. Consider the following example:
```csharp
public sealed class User{
	public string Forename {get;set;}
	public string Surname {get;set;}
	public string Email {get;set;}
}
```
After instantiating this class the properties of the resulting object can change throughout its lifetime thus making it **mutable**. We can rewrite this to make sure that it never mutates after being created like so:
```csharp
public sealed class User{
	public string Forename {get;}
	public string Surname {get;}
	public string Email {get;} 

	public User(string forename, string surname, string email){
		Forename = forename;
		Surname = surname;
		Email = email;
	}
}
```
This approach makes the class **immutable** as the values cannot be changed after an object is created. The problem with this second iteration is that the constructor is basically boilerplate code that we need to write and maintain and it can get very bloated for bigger classes. 
C# 9.0 comes to the rescue with `init` setters:
```csharp
public sealed class User{
	public string Forename {get;init;}
	public string Surname {get;init;}
	public string Email {get;init;}
}
```
The `init` keyword makes ure that the property setter can only be used when creating a new object. This is less verbose and ensures immutability. It also allows us to use object initializer syntax which generally is less error prone than passing positional arguments to a constructor. The downside is that with an object initializer we are allowed to omit some properties when initializing the object.

Finally, to expand on how we can write pure functions let's consider a new requirement to allow users to change their names after they have been created. Typically we would implement it somewhat like:
```csharp
public sealed class User{
	public string Forename {get; private set;}
	public string Surname {get; private set;}
	public string Email {get;}
 
	public User(string forename, string surname, string email){
		Forename = forename;
		Surname = surname;
		Email = email;
	}	  

	public void Rename(string forename, string surname){
		Forename = forename;
		Surname = surname;
	}
}
```
This class is now mutable again and the `Rename` method changes internal state. Even though this is a valid approach we could take it one step further and maintain immutability for our class like this:
```csharp
public sealed class User{
	public string Forename {get;}
	public string Surname {get;}
	public string Email {get;}

	public User(string forename, string surname, string email){
		Forename = forename;
		Surname = surname;
		Email = email;
	}

	public User Rename(string forename, string surname){
		return new User(forename, surname, this.Email);
	}
}
```
Or if we use C# 9.0 syntactic sugar again, namely a `record` and the `with` statement:
```csharp
public sealed record User{
	public string Forename {get;init;}
	public string Surname {get;init;}
	public string Email {get;init;}
	  
	public User Rename(string forename, string surname)
		=> this with {Forename = forename, Surname = surname};
}
```
At this point you might have noticed that C# 9.0 seems to be pretty "functional friendly" with some of its features. You might also notice that our immutable approach can have some negative sideeffects. Each time we rename our user we create a new object and hence allocate more memory. This is the price we pay for the added benefits of immutability and, assuming small objects with rather short lifetimes, it should not be a steep price to pay.
#### Immutable lists
You might be wondering at this point how to deal with collections as the approach above would allow you to add/remove items from a list which would be mutating your state. `System.Collections` offers a number of [immutable collections](https://docs.microsoft.com/en-us/dotnet/api/system.collections.immutable) we can use.
#### Yielding over listing
Another notable "tip" is to leverage `yield` when possible to avoid unnecessary internal function state. Consider the following code that creates some users from the example above given their emails:
```csharp
public IEnumerable<User> CreateUsers(IEnumerable<string> emails){
	var users = new List<User>(count);

	for(var email in emails){
		users.Add(new User {Email = email});
	}
	return users;
}
```
We could improve on it like:
```csharp
public IEnumerable<User> CreateUsers(IEnumerable<string> emails){
	for(var email in emails){
		yield return new User {Email = email};
	}
}
```
This iteration is easier to read and only creates the objects if and when they are needed without the need for an internal list to accumulate the returned objects.
### Functional composition
Now that we have seen how to make immutable classes and keep our functions pure, let's look at some ways in which we can combine those functions with functional composition.
#### Higher-order functions
A higher-order function (HOF) is simply one that takes another function as a parameter. A lot of LINQ functions are HOFs. Consider for instance the following code:
```csharp
public IEnumerable<User> FilterUsers(IEnumerable<User> source, string surname){
	return source.Where(u => u.Surname == surname);
}
```
In this example, `u => u.Surname == surname` is an anonymous function taking in a User and returning a boolean, ie. `Func<User,bool>`, and is used as a parameter to the LINQ `Where` extension method that is a HOF function.
#### Method chaining
Another common way to compose functions is by method chaining. Method chaining can be used on classes that implement the fluent builder pattern or using extension methods. Consider the following code that is likely found in the `Startup.cs` file of many C# applications
```csharp
services
	.AddTransient<IInterface1,Implementation1>()
	.AddScoped<IInterface2,Implementation2>();
```
Because the `IServiceCollection` extension methods return the modified `IServiceCollection` they allow us to chain the calls to them. The same approach can be applied to pure functions giving us a "poor man's" function pipes equivalent. Function pipes is a common feature of functional languages and offers a way to pass the result of a function as an input to another one (think scripting language pipes). 
Consider the following code using our User type again:
```csharp
public sealed record User{
	public string Forename {get;init;}
	public string Surname {get;init;}
	public string Email {get;init;}

	public User Rename(string forename, string surname)
		=> this with {Forename = forename, Surname = surname};
		
	public User ChangeEmail(string email)
		=> this with {Email = email};
}
```
Someone could write a new method like so:
```csharp
public User UpdateUser(User user, string forename, string surname, string email)
	=> user.Rename(forename, surname).ChangeEmail(email);
```
Which is functionally equivalent to a function pipe that passes the result of the `Rename` method to the `ChangeEmail` method.
### Making illegal state unrepresentable
Another important feature of many functional languages that I find myself using time and time again is using abstractions that allow us to represent certain things in a clearer and less error-prone manner. Specifically, **option types**, that signify the presence or absence of a value, and **discriminated unions**, that signify an object can be one of multiple types. Those data structures allow for code that shows much clearer intent than alternative approaches of using nullable types and polymorphism. Let's have a closer look.
#### Getting rid of null - option types
Traditionally, the way to have an object that could contain a value or nothing in C# would be to use a nullable type. Reference types are nullable by default and value types can be made nullable by appending a `?` after their type like `int? couldBeAnIntOrNull`. When it is time to use the value we would typically have code like
```csharp
if(couldBeAnIntOrNull == null) //handle null value here

//use int value here without worrying for null
```
The problem with this approach is that intent is not always clear. Is this nullable by design or just because it is a reference type? Has it already been checked for null? Is null a valid value or is it caused by a bug?
A clearer approach could be using a library like [Optional](https://github.com/nlkl/Optional) to have an option or "maybe" type that is basically a type that explicitly holds a value or nothing. For example:
```csharp
Option<int> couldBeAIntOrNothing;
```
signifies that `couldBeAIntOrNothing` is a variable holding an `int` value or nothing and there are methods to check which one at runtime or exhaustively match the two cases. 

If you are using C# 8.0 or above you can also add
```XML
<Nullable>enable</Nullable>
```
to your `.csproj` file to make reference types non-nullable by default. Enjoy a brighter  future without null reference exceptions and second guessing every variable for nullability.
#### Discriminated unions
Another common requirement can be that a return type is one of multiple types and we do not necessarily know which one at compile time. We would typically model this by having all the types inherit from a single base class or implement an interface and then return an object of that type that is set to an object of the "real" type at runtime.
The problem with this approach is again that intent is not clear and exhaustive matching of the return value is not guaranteed (although many IDEs might help). A better approach would be to use a library like [OneOf](https://github.com/mcintyre321/OneOf) that implements a discriminated union type. For example:
```csharp
OneOf<AdminUser,SimpleUser> user;
```
signifies that the variable named `user` can be either of type `AdminUser` or `SimpleUser` and we have methods to check which one and exhaustively match all types and perform actions on them at runtime.
I think the benefits can be made apparent with an example. Let's consider that both `AdminUser` and `SimpleUser` classes from above inherit from the same `User` class. Typically we would have code like the following to handle the different types at runtime:
```csharp
User user; 

switch (user)
{
	case AdminUser admin:
		//handle admin user here
		break;
	case SimpleUser simple:
		//handle simple user here
		break;
}
```
If we now add a new `GuestUser` to our type hierarchy our `switch` statement is not exhaustive anymore and the compiler will not complain if we neglect to add a case for this new class. The equivalent using `OneOf` would be:
```csharp
OneOf<AdminUser,SimpleUser> user;

user.Switch(
	admin => //handle admin here,
	simple => //handle simple here
);
```
and if we change `user` to be `OneOf<AdminUser,SimpleUser, GuestUser>` our code will not compile until we go and handle `GuestUser` too. It is also very obvious to anyone seeing the type of `user` what possible types it can be without having to explore the type hierarchy.
##### OneOf<Result,Error> (exceptionless error handling)
What I mostly use `OneOf` for is to do error handling without (over)using exceptions. Let's consider some code that fetches something from an upstream or the database and that could throw an exception. Typically we might let the exception propagate to catch it further up the call chain or in some middleware and return an error to the end user. Exception handling is expensive though and obscures our intent as it is not immediately apparent whether a method can throw or not. Instead, we can leverage discriminated unions like so:
```csharp
public OneOf<Resource,Error> GetById(string id){
	try{
		// method that can throw for http errors
		return _client.Get<Resource>(id);
	}
	catch(Exception ex){
		return ex.ToError(); //extension method that maps exceptions to an Error
	}
}
```
What happened here is that we limited exception handling only to the edge of our application where exceptions can occur and transformed it to a result type instead where we explicitly tell our consumers that we can return the resource they were looking for or an error. Consumers can know what to expect and can handle the errors themselves or bubble them up the call chain. Our error handling is now "exceptionless" and can benefit from all the advantages of discriminated unions discussed above.
### I/O in a functional paradigm
I/O is inherently non-pure in any system. The whole point of I/O is to mutate state (or retrieve it) so how does it fit in a functional paradigm? A "pure" app that does not mutate state would be possible but probably not very useful. So how do we deal with mutating state? The idea is to push code that mutates state towards the edges of our system but keep our core business logic pure.
When interfacing with databases, other microservices, event buses etc. we unavoidably mutate state but we try to restrict that to the absolutely necessary. Eg. a repository fetches a row from a database, puts it in an immutable class and then hands it to the pure core of our system. Or it gets an immutable object from a pure function and uses it to mutate some state in the database.
This way our core business logic maintains a functional approach and hence is easily testable and comprehensible and we limit mutations to I/O adapters.
## Conclusion
Even in an OOP language like C# applying some functional language concepts can greatly benefit the testability and readability of our code. Whether it is making our functions pure, using immutable objects or adopting data types like options and discriminated unions we can make the intent of our code much clearer to the reader and we can reduce its complexity by eliminating internal state.
This is not an all or nothing approach. An application that never mutates state would hardly have any use for anyone so even in functional languages we do eventually have to mutate state (saving to a database, publishing events etc). The more of our core logic we can keep pure however and the easier it is to tell which parts of our codebase we can trust to be "functional", the more benefits we will get.
C# has been moving towards a more functional direction with `init` properties, records and the `with` keyword and will likely continue to do so I hope with [native discriminated unions](https://github.com/dotnet/csharplang/discussions/399). We can make use of those features to write code that is easier to maintain. If you are writing code in a business setting, maintainability is likely the absolute most important trait your codebases can have. 
