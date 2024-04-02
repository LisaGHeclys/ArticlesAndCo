# Clean Code Structure and MVVM
*by Lisa Mai Linh Glaziou*

*created: July 6th 2023*

*updated: April 2nd 2024*

# I - Introduction

## A - Brief overview of clean code principles

"Clean code" is a concept that refers to writing code that is readable, understandable, maintainable, and easily modifiable. The main objective of clean code is to make the source code clear and explicit, so it's easy for developers to understand and maintain throughout the project lifecycle. A clean base would also make it easier to introduce changes in a clear way. The ‘soft’ attribute of code can continue to support changes in business without the need for it to be replaced (which can be costly and disruptive for organizations), also guaranteeing it's longetivity.

## B - Introduction to MVVM architecture

The MVVM (Model-View-ViewModel) architecture is a software architectural model widely used in mobile application development. It aims to clearly separate business logic from the user interface, which facilitates maintenance, testing, and code reusability.

---

# II - Understanding Clean Code

## A - Definition and importance of clean code

![](https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg)

The core of software is its code. Code not only directs behavior, but it also results in secure, reliable and maintainable performance. Keeping code clean will ensure that you get the most value out of your software and the right clean code tool can help you get there.

## B - Key principles of clean code

- Readability
- Maintainability
- Simplicity
- Consistency

## C - Benefits of writing clean code

By employing clean architecture, you can design applications with very low coupling and independent of technical implementation details, such as databases and frameworks. That way, the application becomes easy to maintain and flexible to change. It also becomes intrinsically testable.

---

# III - Introduction to MVVM Architecture

## A - Explanation of MVVM architecture

![](https://miro.medium.com/v2/resize:fit:1400/1*-txHcDXNme5WbEn-oKnYMA.png)

The MVVM (Model-View-ViewModel) architecture is a software architectural model that is widely used in mobile application development. It aims to clearly separate business logic from the user interface, which facilitates maintenance, testing, and code reusability.

```tsx
ProjectInTypescript
   |--src
      |--core
         |--assets
         |--components
         |--navigation
         |--utils
      |--features
         |--data
         |--domain
         |--model
         |--presentation
            |--ui
   |App.tsx
```

## B - Components of MVVM:

- **Model**

The model represents the data and business logic of your application. It can include data objects, network services, databases or any other data source. The model has no direct dependency with the user interface components.


```tsx
ProjectInTypescript
   |--src
      |--features
         |--data
         |--model
```
```tsx
//Example in a React Native (TSX) Project
class AuthenticationRepositoryImpl implements AuthenticationRepository {
  async login(email: string, pwd: string, callback: Callback): Promise<void> {
    try {
      const response = await fetch(`${SERVER_URL}/link`, {
        method: 'POST',
        headers: headers,
        body: JSON.stringify({
          email: email,
          password: pwd,
        }),
      })
      callback.onSuccess(response);
    } catch (error: any) {
      callback.onFailure(`An error occurred while logging the user ${error}`);
      //toSetup Toaster for mobile
    }
  }
  //...
}
```

- **View**

The view is responsible for displaying user interface elements and interacting with the user. In a mobile context, this could correspond to an activity, a fragment, or a widget. The view responds to user actions and communicates with the ViewModel to retrieve or display the necessary data.

```tsx
ProjectInTypescript
   |--src
      |--features
         |--presentation
            |--ui
                |--authentication
```
```tsx
//Example in a React Native (TSX) Project
const LoginScreen = ({navigation}: any) => {
  const [isLoading, setIsLoading] = useState<boolean>(false);
  const [error, setError] = useState<boolean>(false);
  const [email, setEmail] = useState<string>("");
  const [password, setPassword] = useState<string>("");

  const handleLogin = async () => {
    setIsLoading(true);
    try {
      await AuthenticationRepositoryImpl.login(email, password, {
        onSuccess: async (response) => {
          try {
            const resToJSON = await response.json();
            if (response.ok) {
              await AsyncStorage.setItem("token", resToJSON?.accessToken)
              navigation.navigate("UserPage");
            } else {
              throw new Error(resToJSON?.Message || 'Unknown error.');
            }
          } catch (jsonError) {
            setError(true);
          }
        },
        onFailure: (error) => {
          console.error('Login failed. Error:', error);
        },
      });
    } catch (error) {
      console.error('Unexpected error during login:', error);
    } finally {
      setError(false);
      setIsLoading(false);
    }
  };

  return (
    <>
      <View>
        <InputComponent value={email} placeholder={"Email"} setValue={setEmail}/>
        <InputComponent value={password} placeholder={"Password"} setValue={setPassword} pwd/>
        <ButtonComponent title={"Log In"} onPress={handleLogin} />
      </View>
      {isLoading && <LoadingComponent/>}
    </>
  )
}
```

- **ViewModel**

The ViewModel acts as an intermediate layer between the view and the model. It manages the presentation logic and provides the necessary data to the view. The ViewModel does not directly depend on the view, which makes it independent of the platform. It exposes properties and commands that the view can bind to update its state and react to user events.

```tsx
ProjectInTypescript
   |--src
      |--features
         |--domain
```
```tsx
//Example in a React Native (TSX) Project
export interface AuthenticationRepository {
  login: (email: string, pwd: string, callback: Callback) => void;
  //...
}
```


## C - Advantages of MVVM

MVVM, an exceptional pattern for complex UI software development, separates the view from its data model. This allows for one-way databinding for synchronization and promotes easier code testing due to its modularity.

- **Separation of concerns**

MVVM enhances focus on individual aspects of a problem by segregating UI code from business logic, adhering to the principle of separation of concerns.

- **Ease of maintenance and testing**

MVVM simplifies code maintenance and testing as code components are replaceable without affecting others. It supports unit tests by allowing component-wise testing and dependency injection.

- **Increased code reusability**

By breaking the app into maintainable chunks, MVVM encourages code reusability. These code chunks can be templated and reused across projects with minimal modifications.

- **Improved collaboration**

MVVM supports enhanced collaboration by sharing code-based view models and presenters among developers, reducing potential confusion from different file types.

## D - Disadvantages of MVVM

- **Learning Difficulties**

MVVM has a steep learning curve, particularly for newcomers. It won't fix poor design and structure in an application.

- **Complexity**

Understanding MVVM, with its additional layer of abstraction and advanced features, takes time and increases complexity. Mistakes could be hard to spot and occur outside of the UI.

- **Over-engineering**

Over-engineering, adding unnecessary features, leads to bloated, slow and small applications and accumulates "technical debt", which becomes harder to manage as the application grows. Here, I want to emphasize that over-engineering only concern non evolutive project. It's best to use clean code for projects that will tend evolve and change in the future.

## E - How MVVM promotes clean code practices

Combining clean architecture with MVVM ensures a well-organized and maintainable codebase. The layers of clean architecture correspond to the components of MVVM, ensuring a clear separation of concerns and modularity. The ViewModel, representing the presentation layer in MVVM, acts as a bridge between the domain layer and the UI layer, following the principles of clean architecture.

---

# IV - Clean Code Practices in MVVM

In order to deliver software that is of high quality, reliable, maintainable, and secure, it is imperative that the underlying code is robust. By following best coding practices, organizations can achieve the agility, velocity, and scale of development needed for their business goals. 

## A - Separation of concerns

- Ensure clear separation between the Model, View, and ViewModel components.
- The Model should represent the data and business logic, the View should handle UI presentation and interaction, and the ViewModel should act as an intermediary between the Model and View.

## B - Single responsibility principle (SRP)

- Each class or component within the MVVM architecture should have a single responsibility.
- Models should only represent data and contain business logic related to that data.
- Views should solely focus on UI presentation and user interaction.
- ViewModels should handle data binding, logic for processing user inputs, and communication with the Model.

```tsx
//to avoid
const processInput = () => { 
    // long list of instructions
}

//better
const validateInput = () => { 
    // validation logic ONLY
}
```

## C - Naming conventions

- Use clear and descriptive names for classes, methods, and variables.
- Follow consistent naming conventions throughout the project to enhance readability and maintainability.
- Choose meaningful names that accurately reflect the purpose and functionality of each component.

```tsx
const a = calculateValue() //to avoid

const result = calculateFinalValue() //better
```


## D - Use of meaningful comments

- Provide comments to explain complex algorithms, business rules, or any non-obvious parts of the code.
- Avoid redundant comments that merely restate what the code does.
- Focus on explaining the why behind the code rather than the how, as the latter should be evident from the code itself.

## E - Testing considerations

- Write unit tests for the ViewModel to ensure that it behaves as expected under different scenarios.
- Test data binding, user input handling, and other ViewModel logic to verify correctness.
- Implement UI tests to validate the interaction between the View and ViewModel components.
- Strive for high test coverage to detect regressions and ensure the robustness of the MVVM architecture.

---

# V - Conclusion

In software development, clean code principles and the MVVM architecture pattern are crucial for maintaining code quality, reliability, and maintainability. Clean code ensures readability, agility, and efficient development processes. Meanwhile, MVVM separates concerns and enhances testing, reusability, and collaboration among developers. Despite potential challenges, aligning MVVM with clean code principles fosters a modular and understandable codebase. By embracing these practices, organizations can achieve agile scalability, deliver high-quality products, and stay competitive in the ever-evolving software landscape. Therefore, continual learning for developers to enhance coding skills and adapt to emerging technologies is essential for building efficient, scalable, and robust software applications.

---

# Resources, Case of Studies and Examples

Paul Allies. (2021, Oct 25). [**Clean Architecture: Typescript and React**](https://paulallies.medium.com/clean-architecture-typescript-and-react-8e509098abfe) from Medium
 

Sonar Contributors (2008 - 2024). [**Clean Code principles and best practices**](https://www.sonarsource.com/solutions/clean-code/) from Sonar Source


Robert C. Martin (Uncle Bob) (2012, Aug 13). [**The Clean Architecture**](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) from The Clean Code Blog

Ramotion Contributors (2024, Feb 22). [**Understanding MVVM: Model-View-ViewModel Architecture Explained**](https://www.ramotion.com/blog/what-is-mvvm/) from Ramotion

Basanta Behera (2023, Dec 3). [**Android Development: Clean Architecture with MVVM and SOLID Principles**](https://medium.com/@basanta.behera/android-development-clean-architecture-with-mvvm-and-solid-principles-b12d40b0c0b0) from Medium 

Diego Marcher (2023, May 30). [**Unlocking the Potential of MVVM in Android Development with Kotlin**](https://blog.stackademic.com/unlocking-the-potential-of-mvvm-in-android-development-with-kotlin-ea353a70b89d) from Medium 