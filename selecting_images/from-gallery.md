# Selecting images from gallery / files.

## Full sample code
<pre>
class ImageActivity : AppCompatActivity() {

                                                    // Binding object to refer to xml.
                                                    // Not important.
    private lateinit var binding: ActivityImageBinding

                                                    // Modern alternative for onActivityResult().
                                                    // Cannot be lazy initialised.
                                                    // This is used to launch system file picker
                                                    // to select an image.
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
                                                    // <i>ACTION_GET_CONTENT</i>
            <b>val imageFromGalleryIntent = Intent(<i>ACTION_GET_CONTENT</i>).apply {
                type = "image/*"
            }</b>
            imageSelectionLauncher.launch(Intent.createChooser(<i>imageFromGalleryIntent</i>, "Choose"))
        }
        
        binding.imageFromFilesButton.setOnClickListener {
        
                                                    // For selecting files, use intent action
                                                    // <i>ACTION_OPEN_DOCUMENT</i>
            <b>val fileSelectionIntent = Intent(<i>ACTION_OPEN_DOCUMENT</i>).apply {
                addCategory(Intent.CATEGORY_OPENABLE)
                type = "image/*"
            }</b>
            imageSelectionLauncher.launch(Intent.createChooser(<i>fileSelectionIntent</i>, "Choose"))
        }
    }
}
</pre>
