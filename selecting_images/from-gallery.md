# Selecting images from gallery / files.

## Full sample code
<pre>
class ImageActivity : AppCompatActivity() {

                                                    // Binding object to refer to xml.
                                                    // Not important.
    private lateinit var binding: ActivityImageBinding

                                                    // Modern alternative for onActivityResult().
                                                    // Cannot be lazy initialised.
                                                    // This is used to launch any gallery application,
                                                    // or the system file picker to select an image.
    private val imageSelectionLauncher =
        registerForActivityResult(ActivityResultContracts.StartActivityForResult()){

                                                    // If user selects an image, 
                                                    // then show it on Imageview.
            if (it.resultCode == Activity.RESULT_OK){
                <b>it.data?.data?.let { uri ->
                    setImageFromUri(uri)
                }</b>
            }
        }

                                                    // Function to show image on Imageview.
    private fun setImageFromUri(uri: Uri){
        <b>binding.imageToShow.setImageURI(uri)</b>
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding = ActivityImageBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.imageFromGalleryButton.setOnClickListener {
        
                                                    // For opening gallery, use intent action
                                                    // <i>ACTION_PICK</i>
                                                    // For uri, no visible difference was seen
                                                    // by using MediaStore.Images.Media.INTERNAL_CONTENT_URI
            val imageFromGalleryIntent = Intent(<i>Intent.ACTION_PICK, MediaStore.Images.Media.EXTERNAL_CONTENT_URI</i>)
            imageSelectionLauncher.launch(Intent.createChooser(<i>imageFromGalleryIntent</i>, "Choose"))
        }
        
        binding.imageFromFilesButton.setOnClickListener {
        
                                                    // For selecting files, use intent action
                                                    // <i>ACTION_OPEN_DOCUMENT</i>
            <b>val fileSelectionIntent = Intent(<i>ACTION_OPEN_DOCUMENT</i>).apply {
                addCategory(Intent.CATEGORY_OPENABLE)
                type = "image/*"
            }</b>
            
                                                    // Another alternative is to use ACTION_GET_CONTENT
            //val fileSelectionIntent = Intent(<i>ACTION_GET_CONTENT</i>).apply {
            //  type = "image/*"
            //}
            
            imageSelectionLauncher.launch(Intent.createChooser(<i>fileSelectionIntent</i>, "Choose"))
        }
    }
}
</pre>
