# Retrofit - Part 1 - GET request

## Add dependencies
```
def retrofitVersion = "2.9.0"
implementation "com.squareup.retrofit2:retrofit:$retrofitVersion"
implementation "com.squareup.retrofit2:converter-gson:$retrofitVersion"
```
Check the latest version from here: https://github.com/square/retrofit#download

## Using Coroutines

[Add coroutine if not already added](https://github.com/Kotlin/kotlinx.coroutines#gradle)  

<pre>
// <b>Base URL - <i>Must have an ending slash</i></b>
// ==========================================================================
val BASE_URL = "https://food2fork.ca/api/"    



// <b>MODEL classes</b>
// ==========================================================================

                                              // Model class, JSON response
                                              // from network will be mapped
                                              // to this class.

data class ResponseMain(
    val count: Int,
    val next: String,
    val previous: String,
    val results: List<ResponseIndividualRecipe>
)

                                              // Another model class.
                                              // The main JSON response contains
                                              // a list of other JSON objects
                                              // using key: "results".
                                              // This class is used for those
                                              // JSON objets.

data class ResponseIndividualRecipe(
    val cooking_instructions: Any,
    val description: String,
    val featured_image: String,
    val ingredients: List<String>,
    val rating: Int,
    val source_url: String,
    val title: String
)

                                              // There can be many other model classes
                                              // as required to map JSON response.

// ==========================================================================



// <b>RETROFIT interface</b>
// Interface functions MUST be <b><i>suspend</i></b> functions.
// Function return type - data model class.
// ==========================================================================

                                              // The interface outlines the methods
                                              // to be called for network operations.
                                              // Methods are implemented by retrofit,
                                              // we only need to provide some information
                                              // using annotations.
                                              
                                              // <b><i>@GET</i> - takes the api end point</b>
                                              // <b><i>@Header</i> - takes info like token</b>
                                              // <b><i>@Query</i> - marks query parameters</b>
                                              
// Example: https://food2fork.ca/api/recipe/search/?page=2&query=chicken
// Base URL - https://food2fork.ca/api/
// End point - recipe/search
// Query parameters : "page", "query"

interface RetrofitInterface {

    <b><i>@GET</i></b>("recipe/search/")
    <i>suspend</i> fun queryByRecipes(
        <b><i>@Header</i></b>("Authorization") token: String,
        <b><i>@Query</i></b>("page") pageNo: Int,
        <b><i>@Query</i></b>("query") recipes: String
    ): ResponseMain

                                              // There can be other methods
                                              // for GET requests or even
                                              // PUT, POST, DELETE requests

}

// ==========================================================================



// <b>RETROFIT Service object</b>
// ==========================================================================

                                              // Should have the base url,
                                              // converter used here is Gson
                                              // (Converts JSON to Model classes).
                                              // Finally create() takes the
                                              // name of the interface.

val RetrofitService = Retrofit.Builder()
    .baseUrl(BASE_URL)
    .addConverterFactory(GsonConverterFactory.create())
    .build()
    .create(RetrofitInterface::class.java)

// ==========================================================================



// <b> Make the network call - from a coroutine scope.</b>
// ==========================================================================

class MainActivity: AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        CoroutineScope(IO).launch {
            val response = RetrofitService.queryByRecipes(
                token = "Token 9c8b06d329136da358c2d00e76946b0111ce2c48",
                pageNo = 1,
                recipes = "beef"
            )
            println("Result from network: ${response.results.size}")
        }
        
    }

}

// ==========================================================================
</pre>

## Without using Coroutines

<pre>
// MODEL classes - same as above.

// Retrofit service object - same as above

// <b>Retrofit interface<b>
// Functions MUST NOT be suspend functions.
// They must return a type <i>Call<model_class></i>
// ==========================================================================

interface RetrofitInterface {

    @GET("recipe/search/")
    fun queryByRecipesByCall(
        @Header("Authorization") token: String,
        @Query("page") pageNo: Int,
        @Query("query") recipes: String
    ): Call<ResponseMain>

}

// ==========================================================================


// <b>Make the network call, get the response using callback</b>
// ==========================================================================

val networkCall = RetrofitService.queryByRecipesByCall(
    token = "Token 9c8b06d329136da358c2d00e76946b0111ce2c48",
    pageNo = 1,
    recipes = "beef"
)

                                              // enqueue methods puts the request
                                              // on a queue for retrofit to execute.
                                              // Once the call is executed the callback
                                              // methods are called.

networkCall.enqueue(object : Callback<ResponseMain> {
    override fun onResponse(call: Call<ResponseMain>, response: Response<ResponseMain>) {
        println("Result from network: ${response.body()?.results?.size}")
    }

    override fun onFailure(call: Call<ResponseMain>, t: Throwable) {

    }
})

// ==========================================================================

</pre>
