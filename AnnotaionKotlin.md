Let me show you how to manually simulate what happens during annotation processing without using additional dependencies like `KAPT` or `KotlinPoet`. Here's an easier example where we use a custom annotation and reflection to dynamically generate and execute boilerplate code during runtime.

---

### **Objective**
We will create a custom annotation `@GenerateBoilerplate` that wraps a function in boilerplate logic. At runtime, we will use reflection to dynamically generate and execute the boilerplate code.

---

### **Step 1: Create the Custom Annotation**

Define a custom annotation in your project:

```kotlin
@Target(AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.RUNTIME) // Ensure it is available at runtime
annotation class GenerateBoilerplate(val className: String)
```

Here, the annotation `GenerateBoilerplate` has one property (`className`), which specifies the name of the "generated" class.

---

### **Step 2: Annotate a Function**

Annotate your function with `@GenerateBoilerplate`:

```kotlin
@GenerateBoilerplate(className = "MyGeneratedClass")
fun myFunction() {
    println("This is the core functionality of myFunction.")
}
```

---

### **Step 3: Simulate Boilerplate Generation with Reflection**

Write a utility function to simulate generating boilerplate code and executing the annotated function:

```kotlin
import kotlin.reflect.full.declaredFunctions
import kotlin.reflect.full.findAnnotation

fun generateAndExecuteBoilerplate(targetClass: Any) {
    // Get the class of the object passed
    val kClass = targetClass::class

    // Iterate through all functions in the class
    for (function in kClass.declaredFunctions) {
        // Check if the function has the @GenerateBoilerplate annotation
        val annotation = function.findAnnotation<GenerateBoilerplate>()

        if (annotation != null) {
            // Print a simulated class name from the annotation
            println("Generating class: ${annotation.className}")

            // Simulate the boilerplate logic
            println("// Boilerplate Code Starts")
            println("println(\"Logging before function execution\")")

            // Call the actual annotated function
            println("// Calling the original function:")
            function.call(targetClass)

            // Simulate the end of the boilerplate logic
            println("println(\"Logging after function execution\")")
            println("// Boilerplate Code Ends\n")
        }
    }
}
```

---

### **Step 4: Execute the Utility**

Create an object containing the annotated function and run the utility:

```kotlin
class MyClass {
    @GenerateBoilerplate(className = "MyGeneratedClass")
    fun myFunction() {
        println("This is the core functionality of myFunction.")
    }

    @GenerateBoilerplate(className = "AnotherGeneratedClass")
    fun anotherFunction() {
        println("This is the core functionality of anotherFunction.")
    }
}

fun main() {
    val myClass = MyClass()
    generateAndExecuteBoilerplate(myClass)
}
```

---

### **Expected Output**

When you run the code, it dynamically generates boilerplate and executes the annotated functions:

```
Generating class: MyGeneratedClass
// Boilerplate Code Starts
println("Logging before function execution")
// Calling the original function:
This is the core functionality of myFunction.
println("Logging after function execution")
// Boilerplate Code Ends

Generating class: AnotherGeneratedClass
// Boilerplate Code Starts
println("Logging before function execution")
// Calling the original function:
This is the core functionality of anotherFunction.
println("Logging after function execution")
// Boilerplate Code Ends
```

---

### **Explanation**

1. **Annotation Definition**:
   - The `@GenerateBoilerplate` annotation is defined with a property `className` for identifying the "generated" class.

2. **Reflection**:
   - Using `kotlin.reflect`, we find all functions in the class and check for the `@GenerateBoilerplate` annotation.

3. **Dynamic Code Generation**:
   - The `generateAndExecuteBoilerplate` function simulates generating boilerplate code around the annotated function.

4. **Simulated Execution**:
   - It executes the annotated function using `function.call(targetClass)` and surrounds it with boilerplate logic.

---

### **How It Simulates Actual Annotation Processing**

- **Compile-Time Annotations (via KAPT)**: Normally, an annotation processor generates code during compilation. Here, we simulate this with a runtime utility.
- **Generated Code**: Instead of creating physical files, we print the boilerplate logic and execute it dynamically.

This simplified example helps you understand the concept without relying on complex tooling.
