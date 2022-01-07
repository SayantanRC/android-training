# Create file provider authority

### Warning!
If cloning from a repo, requires project to be rebuilt for first time use.  
In Android Studio, go to `Build -> Rebuild project`

This will demonstrate defining file provider authority without a hard coded string.  
This utilises the package name to create the authority, using some `build.gradle` magic!  

## Defining in `build.gradle` in app level.
```
android {
    // ...

    defaultConfig {
        // ...
```
```
        // generate provider authority string
        def contentProviderAuthority = applicationId + ".provider"
        
        // Creates a placeholder property to use in the manifest.
        // This will replace "${cpAuthority}" string in AndroidManifest.xml
        manifestPlaceholders = [cpAuthority: contentProviderAuthority]
        
        // Adds a new field for the authority to the BuildConfig class.
        buildConfigField("String", "CONTENT_PROVIDER_AUTHORITY", "\"${contentProviderAuthority}\"")
```
```
    }
}
```

## Defining in `AndroidManifest.xml`
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    ...>

    <application
        ...
        >
        
        <!--       Define the android.authorities as a variable, to be replaced by build.gradle        -->
```
```
        <provider
            android:authorities="${cpAuthority}"
            android:name="androidx.core.content.FileProvider"
            android:exported="false"
            android:grantUriPermissions="true"
            >
            <meta-data android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_paths"/>
        </provider>
```
```
    </application>

</manifest>
```

## Define `file_paths.xml` in under xml directory.
Check https://developer.android.com/reference/androidx/core/content/FileProvider  
Example:
```
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <files-path name="image" path="app_images/"/>
</paths>
```

## Using the content provider authority in the app
Use from `BuildConfig` file. (Requires a project rebuild if cloning for first time.)  
Snippet:
<pre>
private val imageFile by lazy {
    File(filesDir.canonicalPath + "/app_images/", "test.img").apply {
        parentFile.mkdirs()
    }
}
val imageFileUri = FileProvider.getUriForFile(this, <i>BuildConfig.CONTENT_PROVIDER_AUTHORITY</i>, imageFile)
</pre>
