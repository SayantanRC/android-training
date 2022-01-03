# View binding

## Warning!
Refactoring view ids do not work.
Refactoring ids in xml do not automatically change them in activity classes.

## Enable in gradle
```
android {
    ...
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
