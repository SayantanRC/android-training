# Part 4 - Injecting ViewModel in activity.

[Part 3](part-3.md)

## Dependency
Add the below dependencies in app level `build.gradle` along with the dependencies in [Part 1](part-1.md).  
```
dependencies {
    //...
    implementation 'androidx.activity:activity-ktx:1.4.0'
}
```

## Annotate the ViewModel class
<pre>
<i>@HiltViewModel</i>
class ActivityMainViewModel : ViewModel() {
    // ...
}
</pre>

## Use the ViewModel class in activity
<pre>
import androidx.activity.viewModels

<i>@AndroidEntryPoint</i>
class ActivityMain: AppCompatActivity() {
    private val activityMainViewModel: ActivityMainViewModel by <i>viewModels()</i>
    
    // ...
}
</pre>
