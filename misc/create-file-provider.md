# Create file provider authority

### Warning!
If cloning from a repo, requires project to be rebuilt for first time use.  
In Android Studio, go to `Build -> Rebuild project`

This will demonstrate defining file provider authority without a hard coded string.  
This utilises the package name to create the authority, using some `build.gradle` magic!  

## Defining in `build.gradle` in app level.
<pre>
android {
    // ...

    defaultConfig {
        // ...
        
        // generate provider authority string
        def contentProviderAuthority = applicationId + ".provider"
        
        // Creates a placeholder property to use in the manifest.
        // This will replace "${cpAuthority}" string in AndroidManifest.xml
        manifestPlaceholders = [cpAuthority: contentProviderAuthority]
        
        // Adds a new field for the authority to the BuildConfig class.
        buildConfigField("String", "CONTENT_PROVIDER_AUTHORITY", "\"${contentProviderAuthority}\"")
    }
}
</pre>

## Defining in `AndroidManifest.xml`
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    ...>

    <application
        ...
        >
        
        <!--       Define the android.authorities as a variable, to be replaced by build.gradle        -->
        <provider
            android:authorities="${cpAuthority}"
            android:name="androidx.core.content.FileProvider"
            android:exported="false"
            android:grantUriPermissions="true"
            >
            <meta-data android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_paths"/>
        </provider>
        
    </application>

</manifest>
```

## Define `file_paths.xml` in under xml directory.
Check https://developer.android.com/reference/androidx/core/content/FileProvider  

## Using the content provider authority in the app
Use from `BuildConfig` file. (Requires a project rebuild if cloning for first time.)  
Snippet:
<pre>
private fun getUri(file: File) =
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N)
                FileProvider.getUriForFile(this, <i>BuildConfig.contentProviderAuthority</i>, file)
            else Uri.fromFile(file.file)
</pre>
