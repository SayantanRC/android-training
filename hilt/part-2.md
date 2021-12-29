# Hilt (Part 2)

[Part 1](hilt/part-1.md)  

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
<i>@AndroidEntryPoint</i>
class MainActivity: AppCompatActivity() {

    <i>@Inject</i>
    lateinit var testClassAdd: TestClassAdd

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        println("Result: ${testClassAdd.getStringAddition()}")
    }

}

class TestClassAdd
<i>@Inject</i>        <b>// CONSTRUCTOR INJECTION</b>
constructor(private val n1: RandomNumberClass, private val n2: RandomNumberClass){
    fun getStringAddition() =
        "Number 1: ${n1.value}, base: ${n1.base}, " +
        "Number 2: ${n2.value}, base: ${n2.base}; Addition: ${n1.value + n2.value}"
}

class RandomNumberClass
<i>@Inject</i>
constructor(val base: Int)
{
    val value = (Math.random()*100).toInt() * base
}

<b>// Module creation</b>
<i>@InstallIn(ActivityComponent::class)</i>
<i>@Module</i>
class DIModule(){

    <i>@Provides</i>
    fun provideRandomNumberClass(): RandomNumberClass {
        return RandomNumberClass(3)
    }

}
</pre>

## Using `@Provides` to deal with interfaces

<pre>
/**
 * Interface defining some methods to be implemented.
 */
interface TestInterface {
    fun getAString(): String
}

/**
 * Implementation of the above interface.
 */
class TestInterfaceImpl: TestInterface {
    override fun getAString() = "A string"
}

/**
 * Module defining how to create instance of [TestInterface].
 */
@InstallIn(ActivityComponent::class)
@Module
class DIModule(){

    @Provides
    fun getTestInterface(): TestInterface {
        return TestInterfaceImpl()
    }

}

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
