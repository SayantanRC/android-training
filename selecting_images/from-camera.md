# Selecting image from camera

### Create a provider
The same provider is created as demonstrated here: [Creating file provider authority (by package name)](../misc/create-file-provider.md)

### Full sample code
<pre>
class ImageActivity : AppCompatActivity() {

    private lateinit var binding: ActivityImageBinding

                                                  // File to store image from camera
    private val imageFile by lazy {
        File(filesDir.canonicalPath + "/app_images/", "test.img").apply {
            parentFile?.mkdirs()
        }
    }

                                                  // Method to display image from file
                                                  // on ImageView
    private fun setImageViewFromFile(){
        val bitmap = BitmapFactory.decodeFile(imageFile.absolutePath)

                                                  // <b>---------------- STEP 5 ----------------</b>
        binding.imageToShow.setImageBitmap(bitmap)
    }
    
                                                  // Receiver after image from camera is returned.
    private val imageCaptureLauncher =
        registerForActivityResult(ActivityResultContracts.StartActivityForResult()){
            if (it.resultCode == Activity.RESULT_OK){
            
                                                  // <b>---------------- STEP 4 ----------------</b>
                                                  // If successfully snapped a photo,
                                                  // display file in ImageView
                setImageViewFromFile()
            }
        }
        
                                                  // Method to create intent and launch camera.
    private fun launchCameraIntent(){
        imageFile.delete()
        val imageFileUri = getUri(imageFile)

        val imageFromGalleryIntent = Intent(<i>MediaStore.ACTION_IMAGE_CAPTURE</i>).apply {
            putExtra(<i>MediaStore.EXTRA_OUTPUT, imageFileUri</i>)
        }
                                                  // <b>---------------- STEP 3 ----------------</b>
        imageCaptureLauncher.launch(imageFromGalleryIntent)
    }
    
    
                                                  // Camera permission launcher
    private val cameraRequestLauncher =
        registerForActivityResult(ActivityResultContracts.RequestPermission()){
            if (it){
                                                  // <b>---------------- STEP 2 ----------------</b>
                                                  // If permission is granted, 
                                                  // launch intent to start camera.
                launchCameraIntent()
            }
        }

                                                  // Get Uri for a File
    private fun getUri(file: File) =
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N)
            FileProvider.getUriForFile(this, BuildConfig.CONTENT_PROVIDER_AUTHORITY, file)
        else Uri.fromFile(file)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding = ActivityImageBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.browseImages.setOnClickListener {
        
                                                  // <b>---------------- STEP 1 ----------------</b>
                                                  // Launch camera permission
            cameraRequestLauncher.launch(Manifest.permission.CAMERA)
        }
    }
}
</pre>
