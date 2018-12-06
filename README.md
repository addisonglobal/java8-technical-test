# Addison Global Backend Technical Assesement

## Introduction

Welcome to Addison Global Backend technical test.

The main goal of this exercise is to assess your approach to problem solving, as well as your ability to write clean, well tested and reusable code. There's no hard rules or tricky questions.

## Glossary
* Credentials - A tuple of _username_ and _password_ that are used to authenticate a customer.
* User - Identifies a given customer within the system. For simplicity, it just contains _userId_ which will match the _username_ of the given customer.
* UserToken - Token granted to a user in order to perform further operations in the system. It is the concatenation of the _userId_ and the current time. For example: `user123_2017-01-01T10:00:00.000`

Implementation example:
```java
public class Credentials {
    private String username;
    private String password;

    public String getUsername(){
      return username;
    }
    public String getPassword(){
      return password;
    }
}
```
```java
public class User {
  private String userId;

  public String getUserId(){
    return userId;
  }
}
```
```java
public class UserToken {
    private String token;

    public String getToken(){
      return token;
    }
}
```
## Exercise
The goal of the exercise is to improve the definition of a backend service/module and provide an implementation for it. Once this is finished, you'll create a microservice that offers a REST API to consume the service/module functionality.
> **Note:** Favour simplicity, code the solution as a single module and use packages to structure it..

### 1. Service Interface
Given these two synchronous and asynchronous definitions of the TokenService
```java
public interface ISyncTokenService {

    User authenticate(Credentials credentials);

    UserToken requestToken(User user);

    default UserToken issueToken(Credentials credentials) {
        throw new NotImplementedException(); //TODO: Implement this
    }

}
```
```java
public interface IAsyncTokenService {

    CompletableFuture<User> authenticate(Credentials credentials);

    CompletableFuture<UserToken> requestToken(User user);

    default Future<UserToken> issueToken(Credentials credentials) {
        throw new NotImplementedException(); //TODO: Implement this
    }

}
```
**Task:** Provide both implementations of _requestToken_ in terms of _authenticate_ and _issueToken_. By doing that, whoever implements the service will only need to implement _authenticate_ and _issueToken_.

### 2. Service Implementation

Provide an implementation for the following API, which **is different** from the one designed in the previous section:
```java
public interface ISimpleAsyncTokenService {

  default CompletableFuture<UserToken> requestToken(Credentials credentials) {
    throw new NotImplementedException(); //TODO: Implement this
  }

}
```

**Task** requirements / guidelines:

We prefer you to use an Actor Model implementation such as [Akka](https://akka.io/), but it's not mandatory. You can use other frameworks like [Spring](https://spring.io/) or any other of your choice.

1. Implement an Actor/Service/Module that:
   * Validates the *Credentials* and return an instance of a *User*.
   * The *User* instance will always be returned with a random delay between 0 and 5000 milliseconds.
   * If the password matches the username in uppercase, the validation is a success, otherwise is a failure. Examples:
       * username: house , password: HOUSE => Valid credentials.
       * username: house , password: House => Invalid credentials.
   * The *userId* of the returned user will be the provided *username*.
   * This logic has to be encapsulated in a separate Actor/Service/Module.
    
2. Implement another Actor/Service/Module that:
   * Returns a *UserToken* for a given *User*.
   * The *UserToken* instance will always be returned with a random delay between 0 and 5000 milliseconds.
   * If the *userId* of the provided *User* starts with **A**, the call will fail.
   * The *token* attribute for the *User Token* will be the concatenation of the *userId* and the current date time in UTC: `yyyy-MM-dd'T'HH:mm:ssZ`.
        * Example: `username: house => house_2017-01-01T10:00:00Z`
   * This logic has to be encapsulated in a separate Actor/Service/Module.
   
3. Implement the *requestToken* function/method from the **SimpleAsyncTokenService** trait/interface in a way that:
   * Its logic is encapsulated in an Actor/Service/Module.
   * It makes use of the previously defined actors/services/modules for authenticating users and granting tokens:
        * It will first use the validation of the *Credentials* to obtain a *User*.
        * After that it will then use the actor/service/module to obtain a *UserToken*.
        * Finally, returns the *UserToken* to the original caller.

**Evaluation** notes:

We're particularly interested on how the actor system (or the service orchestration) is designed and tested, paying special attention to the following aspects:
* **Simplicity**: Do not over-engineer the solution, simple is better. 
* **Threading model** and **Non-Blocking APIs**: How you maximize the usage of the available resources.
* **Concurrency**: How concurrent requests are handled to maximize the performance.
* **Testing**: How you design the tests in order to increase the coverage.
* **Fault tolerance**: How the system handles, isolates and reacts to failures.

> **Keep in mind:**
> * As the implementation is intended to serve multiple concurrent requests!!!
>
>      The fact that a computation might take 5 seconds should not prevent other computations to happen during that time.
> * How to model/handle the failures.

### 3. REST API

**Task**: Define a simple REST API to offer the functionality of the **SimpleAsyncTokenService** implemented in the previous block.

For its implementation we prefer you to use [Akka HTTP](https://doc.akka.io/docs/akka-http/current/java/http/), however, it's not mandatory and you can use other frameworks such as [Spring Boot](https://projects.spring.io/spring-boot/).

**Evaluation** notes:

We're interested on the structure and completeness of the API, so as how it is tested.

## Deliverable
* **Source Code**: Either of the following ways to bundle the code, whatever is easier for you:
    * A `zip` file containing the whole project. Remember to skip binaries, logs, etc if you choose this option.
    * A link to an accessible private repository with your work in (Github can host private repositories at a cost; there is no charge for doing so with Bitbucket).

* **Documentation** / **Instructions**: You can bundle it together with the code.
    * A `Readme.md` file explaining the assumptions and decisions you've made solving this task including technology and library choices.
    * Any instructions required to run your solution and tests in a Linux environment.
> **Note:** Remember this is not a thesis, just few lines is enough. Favour self-documenting code.
