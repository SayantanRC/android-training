# Data binding - Part 2

## Enable in gradle
```
android {
    //...
    buildFeatures {
        dataBinding true                          // viewBinding is not needed
    }
}
```

## Layout file XML

XML file name : `activity_main_1.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <variable
            name="string_message"
            type="String"
            />
        <variable
            name="number"
            type="Integer" />
    </data>
    
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        >

        <!-- ========================================= -->
        <!--      Demonstrate simple String text       -->
        <!-- ========================================= -->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{string_message}"/>


        <!-- ========================================= -->
        <!--         Method calls on variables         -->
        <!-- ========================================= -->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{number.toString()}"/>


        <!-- ========================================= -->
        <!--       string + number concatenation       -->
        <!-- ========================================= -->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{`Message is : ` + string_message + `, number is: ` + number}"/>


    </LinearLayout>
</layout>
```

## Activity class file

<pre>
class MainActivity: AppCompatActivity() {
                                                    // Data binding object
                                                    // same as view binding.
    lateinit var binding: ActivityMain1Binding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

                                                    // Set the binding
        binding = ActivityMain1Binding.inflate(layoutInflater)
        setContentView(binding.root)
        
                                                    // Directly set the variables.
                                                    // Note the name change to camel case.
        binding.number = 334
        binding.stringMessage = "Hello world"
        
    }
}
</pre>
