# Hilt (Part 1)

## Get dependencies
https://developer.android.com/training/dependency-injection/hilt-android

In project level `build.gradle` file
```
buildscript {

    ext.dagger_version = "2.38.1"
    
    // ...
    
    dependencies {
        // ...
        classpath "com.google.dagger:hilt-android-gradle-plugin:$dagger_version"
        
    }
}
```

In app level `build.gradle` file
```
plugins {
    // ...
    id 'kotlin-kapt'
    id 'dagger.hilt.android.plugin'
}

// ...

dependencies {
    // ...
    implementation "com.google.dagger:hilt-android:$dagger_version"
    kapt "com.google.dagger:hilt-compiler:$dagger_version"
}
```

## Initialise Application class
- Create application class and add <b>`@HiltAndroidApp`</b> annotation.
  <pre>
  <i>@HiltAndroidApp</i>
  class BaseApplication: Application() {
  }
  </pre>
- Add to manifest.
  ```
  <manifest ...
    >

    <application
        android:name=".BaseApplication"
        ...
        >

        ...

    </application>

  </manifest>
  ```

## Simple FIELD injection

- Activities / Fragment / Service / custom views, i.e. all android framework receiving an injection needs the <b>`@AndroidEntryPoint`</b> annotation
  <pre>
  <i>@AndroidEntryPoint</i>
  class MainActivity: AppCompatActivity() {
  </pre>
- The `lateinit var` into which injection is performed <b>`@Inject`</b> annotation.
  <pre>
  <i>@Inject</i>
  lateinit var testClass: TestClass
  </pre>
- The source class, whose instance is being injected, must also have the <b>`@Inject`</b> annotation above its constructor.
  <pre>
  class TestClass
  <i>@Inject</i>
  constructor()
  {
    ...
  }
  </pre>

### Full FIELD injection sample
<pre>
<i>@AndroidEntryPoint</i>
class MainActivity: AppCompatActivity() {

    <i>@Inject</i>
    lateinit var testClass: TestClass

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        println("Result: ${testClass.getSampleString()}")
    }

}

class TestClass
<i>@Inject</i>
constructor()
/*
 * Note that even though the constructor is empty (constructor would not have been normally needed),
 * but since we are using dependency injection, we need the @Inject annotation, on a contructor.
 */
{
    fun getSampleString() = "A sample string"
}
</pre>

## Simple CONSTRUCTOR injection

Almost same as above. Add `@Inject` annotation above the constructor where the injection is to be performed.

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
<i>@Inject</i>             <b>// CONSTRUCTOR INJECTION</b>
constructor(private val n1: RandomNumberClass, private val n2: RandomNumberClass){
    fun getStringAddition() =
        "Number 1: ${n1.value}, Number 2: ${n2.value}, Addition: ${n1.value + n2.value}"
}

class RandomNumberClass
<i>@Inject</i>
constructor()
{
    val value = (Math.random()*100).toInt()
}
</pre>

#### WARNING!
The source class (in the above example `RandomNumberClass`) cannot have constructor arguments.  
[Check Part 2](part-2.md)  
