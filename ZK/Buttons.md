
# Buttons

Buttons are a fundamental UI component in ZK applications, allowing users to trigger actions and interact with the system. This document covers the attributes of the `button` component in ZK and how to handle button events in both MVC (Model-View-Controller) and MVVM (Model-View-ViewModel) patterns.

## **1. Button Attributes**

The `button` component in ZK comes with various attributes that can be used to configure its behavior and appearance. Here are the commonly used attributes:
- **`id`**: Specifies a unique identifier for the button.
- **`label`**: Sets the text displayed on the button.
- **`disabled`**: Determines whether the button is disabled (non-interactive). Can be set to `true` or `false`.
- **`image`**: Specifies an image to display on the button instead of text.
- **`style`**: Defines inline CSS styles for the button.
- **`class`**: Assigns CSS classes to the button for styling.
- **`onClick`**: Specifies a client-side JavaScript function to be called when the button is clicked.
- **`onclick`**: Deprecated in favor of `onClick`. It is used for specifying a client-side JavaScript function for click events.
- **`href`**: Used if the button is to act as a hyperlink. Specifies the URL to navigate to when the button is clicked.

# Handling Button Clicks in ZK Framework
Handling buttons in ZK involves configuring various attributes and managing events according to the architectural pattern used:
1. **MVC Pattern**:You can choose one of the follwing. 
- Use `onClick` events directly in ZUL and handle them in a controller.
- Use `@Listen` . You must extends annotations for cleaner and more declarative event handling.
- Attach event listeners programmatically for more control (onClick$buttonId).
2. **MVVM Pattern**: Bind commands to events using annotations and data binding.

for code examples and more detais see the sections below.

## Handling Button Events: MVC
- The button click is handled by defining an `onClick` event in the ZUL file, which maps to a method in the controller.
- The `GenericForwardComposer` is used to wire ZUL components to Java variables. 

### Using onClick
```xml
<window title="MVC Button Example" border="normal" apply="com.example.MyMvcController">
    <vbox>
        <button id="btnSubmit" label="Submit" onClick="onClickSubmit"/>
    </vbox>
</window>
```
```java
package com.example;

import org.zkoss.zk.ui.Component;
import org.zkoss.zk.ui.select.SelectorComposer;
import org.zkoss.zk.ui.select.annotation.Wire;
import org.zkoss.zul.Button;

public class MyMvcController extends SelectorComposer<Component> {

    @Wire
    private Button btnSubmit;

    @Override
    public void doAfterCompose(Component comp) throws Exception {
        super.doAfterCompose(comp);
        // Initialization code if needed
    }

    public void onClickSubmit() {
        // Handle button click event
        System.out.println("Submit button clicked");
    }
}
```

### Using Event Listeners
In this approch, when you examine the button tag, you don't understand what it does and if it does. For better understanding you need to visit the Controller and look for an onClick method with the id of the button

```xml
<?xml version="1.0" encoding="UTF-8"?>
<zk xmlns:n="native">
    <window title="Event Listener Button Click Example" border="normal" apply="org.zkoss.bind.BindComposer">
        <button id="myButton" label="Click Me"/>
        <label id="myLabel" value=""/>
    </window>
</zk>
```


```java
import org.zkoss.zk.ui.Component;
import org.zkoss.zk.ui.event.Event;
import org.zkoss.zk.ui.event.EventListener;
import org.zkoss.zk.ui.util.GenericForwardComposer;
import org.zkoss.zul.Button;
import org.zkoss.zul.Label;

public class Example3Controller extends GenericForwardComposer<Component> {
    private Button myButton;
    private Label myLabel;

    public void onClick$myButton(){
        myLabel.setValue("Button was clicked!");

    }
}
```

### Handling Button Clicks with @Listen
For it to work you must extends SelectorComposer<Component>. The other classes don't support @Listen.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<zk xmlns:n="native">
    <window title="Annotation Button Click Example" border="normal" apply="com.example.Example4Controller">
        <button id="myButton" label="Click Me"/>
        <label id="myLabel" value=""/>
    </window>
</zk>
```
```java
import org.zkoss.zk.ui.Component;
import org.zkoss.zk.ui.select.SelectorComposer;
import org.zkoss.zk.ui.select.annotation.Listen;
import org.zkoss.zul.Button;
import org.zkoss.zul.Label;

public class Example4Controller extends SelectorComposer<Component> {
    private Button myButton;
    private Label myLabel;

    @Listen("onClick = #myButton")
    public void handleButtonClick() {
        myLabel.setValue("Button was clicked!");
    }
}
```
### **Key Points for MVC**:
- **Manual Wiring**: Use `@Wire` to wire the button and manually define the event handling method.


## Handling Button Events: MVVM
In the MVVM pattern, event handling is done through binding.
Use `@command` in ZUL to bind the button click event to a method in the ViewModel.
The button's `onClick` event triggers a command in the ViewModel. The `@Command` annotation defines the method that will be executed, and `@NotifyChange` updates the UI by notifying ZK to rebind the `message` property.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<zk xmlns:n="native" xmlns:w="client">
    <window title="MVVM Button Click Example" border="normal" viewModel="@id('vm') @init('com.example.Example2ViewModel')">
        <button label="Click Me" onClick="@command('onButtonClick')"/>
        <label value="@load(vm.message)"/>
    </window>
</zk>
```

```java
import org.zkoss.bind.annotation.Command;
import org.zkoss.bind.annotation.NotifyChange;

public class Example2ViewModel {

    private String message = "";

    public String getMessage() {
        return message;
    }

    @Command
    @NotifyChange("message")
    public void onButtonClick() {
        message = "Button was clicked!";
    }
}
```


## **4. Comparison**

| Aspect                  | MVC                                         | MVVM                                        |
|-------------------------|---------------------------------------------|---------------------------------------------|
| **Event Handling**      | Explicit, handled in the controller         | Declarative, handled in the ViewModel       |
| **Button Configuration**| Attributes like `id`, `label`, `style`, etc.| Attributes like `label`, `onClick` (command)|
| **Data Binding**        | Manual updates and interactions             | Automatic binding and updates               |
| **Code Complexity**     | Higher, requires more boilerplate code      | Lower, due to declarative binding           |
| **Separation of Concerns** | Moderate, with explicit UI handling       | High, with clear separation of View and ViewModel |




