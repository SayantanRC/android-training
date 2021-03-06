# Hilt (Part 2) - Custom objects, passing constructor arguments

[Part 1](part-1.md)  

## Using `@Provides`

Say we need to create instance of a class having constructor arguments.
<pre>
class RandomNumberClass
<i>@Inject</i>
constructor(<b>val base: Int</b>)       <b>// CONSTRUCTOR ARGUMENT</b>
{
    val value = (Math.random()*100).toInt() * base
}
</pre>

- Create a `@Module`.
  <pre>
  <i>@InstallIn(ActivityComponent::class)</i>
  <i>@Module</i>
  class DIModule(){
    ...
  }
  </pre>
- Define method inside the module class to create instance. Method name does NOT matter.
  <pre>
  <i>@Provides</i>
  fun provideRandomNumberClass(): RandomNumberClass {
      return RandomNumberClass(3)
  }
  </pre>
  <b>ALTERNATELY</b> we could also have:
  <pre>
  <i>@Provides</i>
  fun provideNumber() = 3       // CREATE A METHOD TO INJECT AN ARGUMENT IN THE NEXT METHOD
  
  <i>@Provides</i>
  fun provideRandomNumberClass(base: Int): RandomNumberClass {  // ARGUMENT VALUE COMES FROM provideNumber()
      return RandomNumberClass(base)
  }
  </pre>

### Full sample code
<pre>
// ========================================================================

class RandomNumberClass                              // Main dependency
<i>@Inject</i>                                              // to inject
constructor(val base: Int)
{
    val value = (Math.random()*100).toInt() * base
}

// ========================================================================

<i>@InstallIn(ActivityComponent::class)</i>             // Module to define
<i>@Module</i>                                          // how to create
class DIModule(){                                // instances of dependencies

    <i>@Provides</i>
    fun provideRandomNumberClass(): RandomNumberClass {
        return RandomNumberClass(3)
    }

}

// ========================================================================

                                                  // Class needing dependency
class TestClassAdd                                // CONSTRUCTOR INJECTION
<i>@Inject</i>
constructor(private val n1: RandomNumberClass, private val n2: RandomNumberClass){
    fun getStringAddition() =
        "Number 1: ${n1.value}, base: ${n1.base}, " +
        "Number 2: ${n2.value}, base: ${n2.base}; Addition: ${n1.value + n2.value}"
}

// ========================================================================

<i>@AndroidEntryPoint</i>                                   // Injection in Android
class MainActivity: AppCompatActivity() {

    <i>@Inject</i>
    lateinit var testClassAdd: TestClassAdd

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        println("Result: ${testClassAdd.getStringAddition()}")
    }

}

// ========================================================================

</pre>

To see how to inject different instances of the same type, check [part 3](part-3.md).

## Using `@Provides` to deal with interfaces

<pre>

// ========================================================================

                                                    // Interface defining
                                                    // some methods to be implemented.
interface TestInterface {
    fun getAString(): String
}

// ========================================================================

                                                    // Implementation of interface.
                                                    // Needs to be injected
class TestInterfaceImpl: TestInterface {
    override fun getAString() = "A string"
}

// ========================================================================
                                                    // Module defining how to create 
                                                    // instance of interface from
                                                    // implementation class
@InstallIn(ActivityComponent::class)
@Module
class DIModule(){

    @Provides
    fun getTestInterface(): TestInterface {
        return TestInterfaceImpl()
    }

}

// ========================================================================
                                                    // Injection in android
<i>@AndroidEntryPoint</i>
class MainActivity: AppCompatActivity() {

    /**
     * Just defining a generic type, any class implementing the [TestInterface].
     * Dagger / Hilt does not know how to create instance of it.
     * Hence we define it under [DIModule.getTestInterface].
     */
    @Inject
    lateinit var testInterfaceImpl: TestInterface

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        println("Result: ${testInterfaceImpl.getAString()}")
    }

}
</pre>

[Part 3](part-3.md)
