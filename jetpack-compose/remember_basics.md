# Remember

`remember` is a simple concept. It goes like this. In a function, we need local variables, we cannot put everything as class variables. `remember` comes allows us to define local state variables in a composable function.
1. `remember` block allows initializing a local state variable inside a composable function.
2. Code inside a remember block is executed only ONCE in the scope of the composable.

## Example 1

```
class MainActivity: ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            LiveButton3()
        }
    }
    
    var i = 0

    @Composable
    fun LiveButton3() {
        println("Composed LiveButton3")

        val buttonText3 = remember {        // remember while initializing a local state variable
            println("remember buttonText3")
            mutableStateOf("Un-clicked")
        }

        Button(onClick = {
            println("Button 3 clicked")
            buttonText3.value = "Clicked $i"
        }) {
            i++
            println("Composed button 3: ${buttonText3.value} i: $i")
            Text(text = buttonText3.value)
        }
    }
```

- On executing this we get:
  ```
  I/System.out: Composed LiveButton3
  I/System.out: remember buttonText3
  I/System.out: Composed button 3: Un-clicked i: 1
  ```
- On clicking the button multiple times, we get:
  ```
  I/System.out: Button 3 clicked
  I/System.out: Composed button 3: Clicked 1 i: 2
  I/System.out: Button 3 clicked
  I/System.out: Composed button 3: Clicked 2 i: 3
  I/System.out: Button 3 clicked
  I/System.out: Composed button 3: Clicked 3 i: 4
  ```
  
The button keeps updating as expected. Lets try something else.

## Example 2
```
    var i = 0

    @Composable
    fun LiveButton3() {
        println("Composed LiveButton3")

        val buttonText3 = remember {
            println("remember buttonText3")
            mutableStateOf("Un-clicked")
        }

        Button(onClick = {
            println("Button 3 clicked")
            buttonText3.value = "Clicked $i"
        }) {
            remember {                          // surround the increment with remember
                println("remember i = $i")
                i++
            }
            println("Composed button 3: ${buttonText3.value} i: $i")
            Text(text = buttonText3.value)
        }
    }
```
- First run
  ```
  I/System.out: Composed LiveButton3
  I/System.out: remember buttonText3
  I/System.out: remember i = 0
  I/System.out: Composed button 3: Un-clicked i: 1
  ```
- When button is clicked
  ```
  I/System.out: Button 3 clicked
  I/System.out: Composed button 3: Clicked 1 i: 1
  ```
  It changes the text to "Clicked 1"
- On further clicking of button
  ```
  I/System.out: Button 3 clicked
  I/System.out: Button 3 clicked
  ```
  We get the onClick to execute, but nothing happens. No further UI change.
  
### Why
This is because `i++` was surrounded by `remember`. It was executed only once (during first composition), no further change in the value of `i` was allowed. As such the value of `buttonText3` did not change, and no re-composition took place.
