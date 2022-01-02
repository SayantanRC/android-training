# ViewModel - Part 1 (Basics)

## Full sample code (without observables)
<pre>
// <b>ViewModel class for main activity</b>
// ===================================================================================

                                                    // Stores data of the activity.
                                                    // Persists through configuration changes
                                                    // like device rotation.
                                                    
                                                    // Cannot have reference to activity context,
                                                    // but application context is allowed.
                                                    
                                                    // This viewmodel stores a number,
                                                    // has method to increase the number.
                                                    // The main activity just reads this number.
                                                    // The data of the number or the logic
                                                    // is not in the activity class.
class MainViewModel: ViewModel() {

    var number = 0
    
    fun incrementNumber(){
        number++
    }

}
// ===================================================================================



// <b>MainActivity</b>
// ===================================================================================

class MainActivity: AppCompatActivity(R.layout.activity_main) {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

                                                    // Initialise the viewmodel using
                                                    // ViewModelFactory.
                                                    // The constructor argument takes
                                                    // the lifecycle.
                                                    
        val viewModel = ViewModelProvider(this).get(MainViewModel::class.java)
        
        val numberText = findViewById<TextView>(R.id.number_as_text)
        fun setTextFromViewModel(){
                                                    // function to set the text from
                                                    // viewmodel data.
            numberText.text = viewModel.number.toString()
        }
        
                                                    // Initially set the data.
        setTextFromViewModel()

                                                    // On button press, call viewmodel's
                                                    // increment function.
                                                    // Then set the new value in textview again.
        findViewById<Button>(R.id.increment_number).setOnClickListener {
            viewModel.incrementNumber()
            setTextFromViewModel()
        }
        
    }
}

// ===================================================================================
</pre>

## With observables (using StateFlow)

In the above example, everytime the new value of number had to be manually set into the textview.  
Now we will just observe the number and update the textview whenever the number changes.

### Dependencies

https://developer.android.com/jetpack/androidx/releases/lifecycle

<pre>
def lifecycleVersion = "2.4.0"
// To use <i>lifecycleScope</i> inside activity class.
implementation "androidx.lifecycle:lifecycle-runtime-ktx:$lifecycleVersion"
// Optional. To use <i>viewModelScope</i> inside view model class.
implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:$lifecycleVersion")
</pre>

### Full sample code
<pre>
// <b>ViewModel class for main activity</b>
// ===================================================================================

class MainViewModel: ViewModel() {

                                                    // Make the number private
                                                    // as we will be using the
                                                    // <i>numberToText</i> state flow to 
                                                    // observe data changes.
    private var number = 0

                                                    // Private mutable state flow to update
                                                    // inside this class.
    private val _numberToText = MutableStateFlow("0")

                                                    // Immutable state flow.
                                                    // This state flow is public and can be
                                                    // only used to observe state changes.
    val numberToText = _numberToText.asStateFlow()

                                                    // Function to increment number
                                                    // and also update the flow.
    fun incrementNumber(){
        number++
        _numberToText.value = number.toString()

        
                                                    // If we want to use <i>emit()</i> 
                                                    // function of stateflow, 
                                                    // we need to do it from viewModelScope.
        // requires dependency
        // implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:$lifecycleVersion")

        //viewModelScope.launch {
        //  _numberToText.emit(number.toString())
        //}
    }

}

// ===================================================================================



// <b>MainActivity</b>
// ===================================================================================

class MainActivity: AppCompatActivity(R.layout.activity_main) {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
                                                    // Initialise the viewmodel using
                                                    // ViewModelFactory.
                                                    // The constructor argument takes
                                                    // the lifecycle.

        val viewModel = ViewModelProvider(this).get(MainViewModel::class.java)

        lifecycleScope.launch {
        
                                                    // Start observing the flow from viewmodel.
                                                    // Initially the value is "0".
                                                    // We don't need to manually set the 
                                                    // value each time on the textview.
                                                    // Whenever the number increases, 
                                                    // the observer will update the textview.
                                                    
            val numberText = findViewById<TextView>(R.id.number_as_text)
            viewModel.numberToText.collect {
                numberText.text = it
            }
        }
        
                                                    // Button to increase the count.
                                                    // Need not set the text manually
                                                    // as it is being observed.

        findViewById<Button>(R.id.increment_number).setOnClickListener {
            viewModel.incrementNumber()
        }

    }
}

// ===================================================================================
</pre>
