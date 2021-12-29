# Hilt (Part 3) - Passing different instances of the same type

## Using `@Named` annotation

- Inside the `@Module` class, mark different methods with `@Named(SOME_STRING_ID)`.
- At site of injection as well, use `@Named(SOME_STRING_ID)`. The `SOME_STRING_ID` passed in the bracket will be used to differentiate which injection goes where.

### Sample code
<pre>
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

class TestClassAdd
<i>@Inject</i>
constructor(                <b>CONSTRUCTOR INJECTION</b>
    <b><i>@Named("n1")</i></b> private val n1: RandomNumberClass,       // Corresponds to RandomNumberClass(3)
    <b><i>@Named("n2")</i></b> private val n2: RandomNumberClass        // Corresponds to RandomNumberClass(5)
) {
    fun getStringAddition() =
        "Number 1: ${n1.value}, base: ${n1.base}, " +
                "Number 2: ${n2.value}, base: ${n2.base}; Addition: ${n1.value + n2.value}"
}
</pre>
