# Koin (Part 1)

## Get dependencies
https://insert-koin.io/docs/setup/koin/#koin-bom  
```
implementation(platform("io.insert-koin:koin-bom:$koin_version"))
implementation("io.insert-koin:koin-android")
implementation("io.insert-koin:koin-core-coroutines")
implementation("io.insert-koin:koin-androidx-workmanager")
```

## Initialise Application class
- Create application class annotation. We will use this in the last step.
  ```
  class BaseApplication: Application() {
      override fun onCreate() {
          super.onCreate()
      }
  }
  ```
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

## Create classes to be injected

1. An API class that will be injected in a repository.
   ```
   class MyApi() {
       fun callNetwork()
   }
   ```
2. A repository interface and its implementation that will be injected in a viewmodel.
   ```
   interface MyRepo {
       fun doNetworkCall()
   }
   class MyRepoImpl(
       private val myApi: MyApi
   ): MyRepo() {
       override fun doNetworkCall() {
           myApi.callNetwork()
       }
   }
   ```
3. A viewmodel to be injected in an activity.
   ```
   class MainViewModel(
       private val repo: MyRepo
   ): ViewModel(){
       fun doNetworkCall(){
           repo.doNetworkCall()
       }
   }
   ```

## Create how to inject

Create a class `AppModule.kt`.
<pre>
val appModule = <b><i>module</i></b> {
    <b><i>single</i></b> {
        // create MyApi using Retrofit or something else
        Retrofit.Builder()
            .baseUrl("https://something")
            .addConverterFactory(MoshiConverterFactory.create())
            .build()
            .create(MyApi::class.java)
    }
    // OLD
    //<b><i>single&lt;MyRepo&gt;</i></b> {
        // Provide an implementation of MyRepo.
        // "get()" will automatically fetch the api from above "single" block.
        MyRepoImpl(<b><i>get()</i></b>)
    //}
    <b><i>singleOf(::MyRepoImpl) { bind&lt;MyRepo&gt;() }</i></b>
    <b><i>viewModel</i></b> {
        // Notice different block for viewmodel
        MainViewModel(<b><i>get()</i></b>)
    }
}
</pre>

## Inject into activity

```
class MainActivity: ComponentActivity() {
    private val viewModel by viewModel<MainViewModel>()
}
```

## Inject something not a viewmodel

In this example we inject `MyApi` just for representation.
```
class MainActivity: ComponentActivity() {
    //...
    private val api : MyApi by inject()
}
```

## Start koin in the Application class
<pre>
class BaseApplication: Application() {
    override fun onCreate() {
        super.onCreate()
        <b><i>startkoin {
            androidContext(this)
            modules(appModule)
        }</i></b>
    }
}
</pre>

Done !
