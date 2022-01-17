# Hilt (Part 3) - Passing different instances of the same type

[Part 2](part-2.md)

## Using `@Named` annotation

- Inside the `@Module` class, mark different methods with `@Named(SOME_STRING_ID)`.
- At site of injection as well, use `@Named(SOME_STRING_ID)`. The `SOME_STRING_ID` passed in the bracket will be used to differentiate which injection goes where.

### Full sample code
<pre>
// ========================================================================
                                                        // Main dependency
                                                        // to inject
class RandomNumberClass
<i>@Inject</i>
constructor(val base: Int)
{
    val value = (Math.random()*100).toInt() * base
}

// ========================================================================
                                                        // Module defining
                                                        // @Named parameters
<i>@InstallIn(ActivityComponent::class)</i>
<i>@Module</i>
class DIModule(){

    <i><b>@Named("n1")</i></b>
    <i>@Provides</i>
    fun getRandomNumber1(): RandomNumberClass{
        return RandomNumberClass(3)
    }

    <i><b>@Named("n2")</i></b>
    <i>@Provides</i>
    fun getRandomNumber2(): RandomNumberClass{
        return RandomNumberClass(5)
    }

}

// ========================================================================
                                                        // Class needing dependency as
                                                        // CONSTRUCTOR INJECTION
class TestClassAdd
<i>@Inject</i>
constructor(
    <b><i>@Named("n1")</i></b> private val n1: RandomNumberClass,       // Corresponds to RandomNumberClass(3)
    <b><i>@Named("n2")</i></b> private val n2: RandomNumberClass        // Corresponds to RandomNumberClass(5)
) {
    fun getStringAddition() =
        "Number 1: ${n1.value}, base: ${n1.base}, " +
                "Number 2: ${n2.value}, base: ${n2.base}; Addition: ${n1.value + n2.value}"
}
</pre>

## Using annotation classes

- Declare different `annotation class`-es
- Instead of using `@Named`, just use the class name as annotation.

Sample code
<pre>
// ========================================================================
                                                      // Annotation classes
                                                      // used as "markers"
<i>@Qualifier</i> annotation class n1
<i>@Qualifier</i> annotation class n2           // The @Qualifier part may be optional

// ========================================================================
                                                      // Module using above 
                                                      // annotation classes
                                                      // to differentiate
                                                      // instances of dependencies
<i>@InstallIn(ActivityComponent::class)</i>
<i>@Module</i>
class DIModule(){

    <b><i>@n1</i></b>
    @Provides
    fun getRandomNumber1(): RandomNumberClass{
        return RandomNumberClass(3)
    }

    <b><i>@n2</i></b>
    @Provides
    fun getRandomNumber2(): RandomNumberClass{
        return RandomNumberClass(5)
    }
}

// ========================================================================
                                                      // Class needing dependencies as
                                                      // CONSTRUCTOR INJECTION
class TestClassAdd
<i>@Inject</i>
constructor(
    <b><i>@n1</i></b> private val n1: RandomNumberClass,       // Corresponds to RandomNumberClass(3)
    <b><i>@n2</i></b> private val n2: RandomNumberClass        // Corresponds to RandomNumberClass(5)
) {
    fun getStringAddition() =
        "Number 1: ${n1.value}, base: ${n1.base}, " +
                "Number 2: ${n2.value}, base: ${n2.base}; Addition: ${n1.value + n2.value}"
}
</pre>

[Part 4](part-4.md)
