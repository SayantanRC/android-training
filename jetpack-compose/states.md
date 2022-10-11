# States and it's mysteries

If anyone is barely starting with Jetpack compose, they might get very confused with basics like `mutableStateOf`, `remember` etc. I did. So here is a guide to hopefully clear some concepts, in a programmatic way.

## Example 1
```
package balti.finance.cardx

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Column
import androidx.compose.material.Button
import androidx.compose.material.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.setValue

class MainActivity: ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            Column {
                LiveButton1()
                LiveButton2()
            }
        }
    }

    var buttonText1 = "Un-clicked"

    @Composable
    fun LiveButton1() {
        println("Composed LiveButton1")
        Button(onClick = {
            println("Button 1 clicked")
            buttonText1 = "Clicked"
        }) {
            println("Composed button 1: $buttonText1")
            Text(text = buttonText1)
        }
    }

    var buttonText2 = "Un-clicked"

    @Composable
    fun LiveButton2() {
        println("Composed LiveButton2")
        Button(onClick = {
            println("Button 2 clicked")
            buttonText1 = "remote clicked"
            buttonText2 = "Clicked"
        }) {
            println("Composed button 2: $buttonText2")
            Text(text = buttonText2)
        }
    }
    
}
```

This is a basic example that shows two buttons vertically, with log statements to highlight control flow. We will use this code in nect examples with some modifications.

**NOTE:** We are using global / class variables, not variables inside `@Composable` functions unlike other tutorials. Also note we are not using `remember`, if you don't know what that is, don't worry about it.

- On running this we get the following logs:
  ```
  I/System.out: Composed LiveButton1
  I/System.out: Composed button 1: Un-clicked
  I/System.out: Composed LiveButton2
  I/System.out: Composed button 2: Un-clicked
  ```

- On clicking the first button, we only get the log for clicking the button, no change happens in the UI.
  ```
  I/System.out: Button 1 clicked
  ```
- On clicking the second button, similar to first button, no UI change and only the clicked event log appears.
  ```
  I/System.out: Button 2 clicked
  ```

### Why
If you have read about compose, you would know that Jetpack compose does not play very well with variables directly used in the UI, the UI gets updated only for a **STATE change**. A variable value change is not viewed as a state change, hence compose does not update the UI. This will get clearer in the next examples.

## Example 2
```
...
...

    var buttonText1 = mutableStateOf("Un-clicked")                  // changed to state

    @Composable
    fun LiveButton1() {
        println("Composed LiveButton1")
        Button(onClick = {
            println("Button 1 clicked")
            buttonText1.value = "Clicked"                           // changed
        }) {
            println("Composed button 1: ${buttonText1.value}")      // changed
            Text(text = buttonText1.value)                          // changed
        }
    }

    var buttonText2 = "Un-clicked"

    @Composable
    fun LiveButton2() {
        println("Composed LiveButton2")
        Button(onClick = {
            println("Button 2 clicked")
            buttonText1.value = "remote clicked"                    // changed
            buttonText2 = "Clicked"
        }) {
            println("Composed button 2: $buttonText2")
            Text(text = buttonText2)
        }
    }
```

Let's fix one of the buttons. Here we change the `buttonText1` variable to a state variable. Hovering the pointer over it shows a type of `MutableState<String>` instead of `String`. This will fix the first button, clicking on it will change the text to "Clicked".

- Logs on running the code:
  ```
  I/System.out: Composed LiveButton1
  I/System.out: Composed button 1: Un-clicked
  I/System.out: Composed LiveButton2
  I/System.out: Composed button 2: Un-clicked
  ```
- On pressing the first button
  ```
  I/System.out: Button 1 clicked
  I/System.out: Composed button 1: Clicked
  ```
  **Notice 3 things.** 
  - The text changes to "Clicked".
  - The onClick block for the button as well as compose block for drawing the text is executed.
  - Important: The entire `LiveButton1` function is not executed again, if that would have happened, we would get another log saying "Composed LiveButton1".
- On pressing the second button
  ```
  I/System.out: Button 2 clicked
  I/System.out: Composed button 1: remote clicked
  ```
  **Notice 5 things here.** 
  - Text on button 1 changes.
  - Text on button 2 does not change.
  - Button 2's onClick block is fired, but compose block of button 2 is not executed.
  - Button 1 has not been clicked again (as is evident from the logs), but compose block of button 1 is executed.
  - Neither `LiveButton1` nor `LiveButton2` gets executed from the start.

### Why
The why is simple. Only the first button's composable **accessed a state variable's value**, that block got updated whenever and from wherever that state variable was updated. This is the reason, when button 2 was clicked, the compose block of button 1 was executed as the onClick block of button 2 updated state of a variable `buttonText1` whose value was being accessed in the said block.  
On the other hand button 2's text never got updated because it's compose block never dealt with any state.  
> **A composable block will be executed if any of the state variables inside it, whose value is accessed in the block, is updated from any part of the code. Re-composition will happen at the lowest possible level in the UI tree to deal with only the specific state change, no higher element / block will be re-executed.**
