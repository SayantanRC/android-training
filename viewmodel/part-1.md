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
    
    fun increamentNumber(){
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
            viewModel.increamentNumber()
            setTextFromViewModel()
        }
        
    }
}

// ===================================================================================
</pre>
