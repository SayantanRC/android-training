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
        Button(onClick = {                                  // button 1's onClick block
            println("Button 1 clicked")
            buttonText1 = "Clicked"
        }) {                                                // compose block of button 1
            println("Composed button 1: $buttonText1")
            Text(text = buttonText1)
        }
    }

    var buttonText2 = "Un-clicked"

    @Composable
    fun LiveButton2() {
        println("Composed LiveButton2")
        Button(onClick = {                                  // button 2's onClick block
            println("Button 2 clicked")
            buttonText1 = "remote clicked"
            buttonText2 = "Clicked"
        }) {                                                // compose block of button 2
            println("Composed button 2: $buttonText2")
            Text(text = buttonText2)
        }
    }
    
}
```

This is a basic example that shows two buttons vertically, with log statements to highlight control flow. We will use this code in next examples with some modifications.

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
> **A composable block will be executed if any of the state variables inside it, whose value is accessed in the block, is updated from any part of the code. Re-composition will happen at the lowest possible level in the UI tree to deal with only the specific state change, no higher element / block will be re-executed.  
> This effect is only valid for a compose block, other types of blocks (like onClick) do not re-execute in similar circumstances.**

## Example 3
If asked how to fix button 2, one obvious answer would be to change `buttonText2` to a `MutableState` variable as was done for `buttonText1`. However, in this paericular example, we can do something else.
```
...
...
    var buttonText1 = mutableStateOf("Un-clicked")

    @Composable
    fun LiveButton1() {
        println("Composed LiveButton1")
        Button(onClick = {
            println("Button 1 clicked")
            buttonText1.value = "Clicked"
        }) {
            println("Composed button 1: ${buttonText1.value}")
            Text(text = buttonText1.value)
        }
    }

    var buttonText2 = "Un-clicked"                                  // simple variable as before

    @Composable
    fun LiveButton2() {
        println("Composed LiveButton2")
        Button(onClick = {
            println("Button 2 clicked")
            buttonText1.value = "remote clicked"
            buttonText2 = "Clicked"
        }) {
            buttonText1.value                                       // we just access the value of the state variable.
            println("Composed button 2: $buttonText2")
            Text(text = buttonText2)
        }
    }
```
Let's run the code and see what happens.

- On running the code
  ```
  I/System.out: Composed LiveButton1
  I/System.out: Composed button 1: Un-clicked
  I/System.out: Composed LiveButton2
  I/System.out: Composed button 2: Un-clicked
  ```
- On clicking the first button
  ```
  I/System.out: Button 1 clicked
  I/System.out: Composed button 1: Clicked
  I/System.out: Composed button 2: Un-clicked
  ```
  Note that first button's text is updated, as expected. But button 2's scope was also executed. This is because `buttonText1`'s value was accessed in it's scope. No visible UI change is noticed though, because `buttonText2`'s value is not changed.
- On clicking the second button
  ```
  I/System.out: Button 2 clicked
  I/System.out: Composed button 1: remote clicked
  I/System.out: Composed button 2: Clicked
  ```
  **Surprise!** Although `buttonText2` is not a state variable, button 2's text got updated. This is because it was accessing the value of `buttonText1` which also got changed at the same time with `buttonText2`. Due to this happy coincidence, button 2's text was updated.
  
**One small and important to note**: If we just had:
```
...
...
    @Composable
    fun LiveButton2() {
        println("Composed LiveButton2")
        Button(onClick = {
            println("Button 2 clicked")
            buttonText1.value = "remote clicked"
            buttonText2 = "Clicked"
        }) {
            buttonText1                                 // no access to value, only the state variable
            println("Composed button 2: $buttonText2")
            Text(text = buttonText2)
        }
    }
```
In this case clicking button 2 would log
```
I/System.out: Button 2 clicked
I/System.out: Composed button 1: remote clicked
```
Button 2's scope would not re-execute, as although a changing state variable (`buttonText1`) is present in the scope, **it's `value` is not being accessed.**

That's all for now. I will write more stuff as I go through my experiments on Jetpack compose basics.
