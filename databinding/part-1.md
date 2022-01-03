# Data binding - Part 1 - View binding

## Warning!
Refactoring view ids do not work.  
Refactoring ids in xml do not automatically change them in activity classes.  

## Enable in gradle
```
android {
    //...
    buildFeatures {
        viewBinding true
    }
}
```

## Binding in Activity

### Layout XML file

Let the layout file name be `activity_main_1.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <!--           Note the view id          -->
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/number_as_text"
        />
        
</LinearLayout>
```

### Activity class

<pre>
                                                    // DO NOT PASS layout reference in
                                                    // AppCompatActivity constructor.
class MainActivity: AppCompatActivity() {

                                                    // This class is generated automatically.
                                                    // Note the name is <i>ActivityMain<b>1</b>Binding</i>
                                                    // as layout file is <i>activity_main_<b>1</b>.xml</i>
    lateinit var binding: ActivityMain1Binding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

                                                    // Inflate the layout.
                                                    // Set the root layout.
        binding = ActivityMain1Binding.inflate(layoutInflater)
        setContentView(binding.root)
        
                                                    // Access the views.
                                                    // Note that original ID of view
                                                    // is <i>simple_text</i>
                                                    // but the binding has the name in
                                                    // camel case as <i>simpleText</i>.
        binding.simpleText.text = "Hello world"
        
    }

}
</pre>

## Binding in Fragment

### Layout XML file

Let the layout file name be `just_a_fragment.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!--           Note the view id          -->
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/just_a_text"/>

</LinearLayout>
```

### Fragment class

<pre>
class FragmentClass: Fragment() {

                                                    // Note the name of binding class.
                                                    // XML name - <i>just_a_fragment.xml</i>
                                                    // Binding class - <i>JustAFragmentBinding</i>
    lateinit var binding: JustAFragmentBinding

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
    
                                                    // Inflate the binding and return the root.
        binding = JustAFragmentBinding.inflate(inflater)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
                                                    // Access the views.
                                                    // Note the name of id in xml is
                                                    // <i>just_a_text</i>
                                                    // but in the binding it is in camel case
                                                    // <i>justAText</i>
        binding.justAText.text = "Another text"
    }

}
</pre>

### Alternate way for the same thing as above

<pre>
                                                    // Pass the layout in Fragment constructor
class FragmentClass: Fragment(R.layout.just_a_fragment) {

    lateinit var binding: JustAFragmentBinding
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

                                                    // Use the <i>bind</i> function of binding class
                                                    // to bind to the parent view.
        binding = JustAFragmentBinding.bind(view)

        binding.justAText.text = "Another text"
    }

}
</pre>

[Part 2](part-2.md)
