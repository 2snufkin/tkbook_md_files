# MVC vs MVVM
The ZK framework provides support for both MVC and MVVM architectures. 
MVC is straightforward but requires more boilerplate code to manage UI updates manually.    
MVVM, on the other hand, allows for cleaner and more modular code, with the benefits of declarative bindings. 


## MVC (Model-View-Controller)
The MVC architecture separates the application logic into three interconnected components:
1. **Model**: Represents the data and business logic.
2. **View**: The user interface (ZUL files in ZK).
3. **Controller**: Manages communication between the Model and View. In ZK's MVC architecture, the controller is typically responsible for handling UI events, updating the view, and interacting with the model.


### MVC Requirements
-**The controller:** must extends ZK's composer and interfaces that ZK provdies. It's ettinal for the wiring to work, otherwise you get Error like   `class ... must implement interface org.zkoss.zk.ui.util.Composer`.

| Feature                        | `GenericForwardComposer`             | `SelectorComposer`                      | `GenericAutowireComposer`               |
|--------------------------------|--------------------------------------|-----------------------------------------|-----------------------------------------|
| **Wiring Method**              | Manual with `@Wire` and `Selectors`  | CSS-like selectors with `@Wire`         | Automatic, based on naming conventions  |
| **Event Handling**             | Events defined in ZUL or controller  | `@Listen` annotation in the controller  | Events defined in ZUL or controller     |
| **Flexibility**                | Limited to ID-based wiring           | High, with CSS-like selectors           | Moderate, with automatic wiring         |
| **Component Selection**        | ID-based                            | CSS-like selectors                      | Automatic                               |


`SelectorComposer` is a modern and versatile option for implementing the MVC pattern in ZK. It provides powerful tools for wiring components and handling events, making it a better choice for applications that require flexibility and clean code organization. Compared to other options like `GenericForwardComposer`, it offers more control and easier management of complex UI components.
-**The zul file:** You need to point to the controller via the apply attribute which connects the UI components to the Java controller.

### Wiring Components in MVC

In the MVC pattern, the wiring of UI components is typically done manually within the Java controller class. Components in the ZUL file are identified using their `id` attribute, and these IDs are linked to Java fields in the controller using annotations or manual wiring.

#### ZUL File Example
The apply attribute  connects the UI components to the Java controller (`MyMvcController`).

```xml
<window title="MVC Example" border="normal" apply="com.example.MyMvcController">
    <vbox>
        <label id="lblMessage" value="Hello, MVC!" />
        <textbox id="txtInput" />
        <button label="Click Me" onClick="onClickEvent" />
    </vbox>
</window>
```

#### Java Controller Example
Extends one of the must extend classes: in this example is GenericForwardComposer<Component>
```java
package com.example;

import org.zkoss.zk.ui.Component;
import org.zkoss.zk.ui.select.Selectors;
import org.zkoss.zk.ui.select.annotation.Wire;
import org.zkoss.zk.ui.util.GenericForwardComposer;
import org.zkoss.zul.Label;
import org.zkoss.zul.Textbox;

public class MyMvcController extends GenericForwardComposer<Component> {

    @Wire
    private Label lblMessage;

    @Wire
    private Textbox txtInput;

    @Override
    public void doAfterCompose(Component comp) throws Exception {
        super.doAfterCompose(comp);
        // Wiring UI components manually
        Selectors.wireComponents(comp, this, false);
    }

    public void onClickEvent() {
        String input = txtInput.getValue();
        lblMessage.setValue("Hello, " + input);
    }
}
```

### Event Handling in MVC

In MVC, event handling is done by associating event listeners in the ZUL file (e.g., `onClick="onClickEvent"`) with corresponding methods in the controller class. The controller handles these events, processes any data, and updates the UI accordingly.

- The `onClickEvent` method is triggered by the button's `onClick` event.
- The controller method then retrieves data from the `textbox`, processes it, and updates the `label`.

### Update UI

- **NullPointerException**: This can occur if the UI components are not correctly wired in the controller. To avoid this:
  - Ensure that each component's `id` in the ZUL file exactly matches the name used in the `@Wire` annotation.
  - Call `Selectors.wireComponents` in the `doAfterCompose` method to link components properly.

- **Component Not Found**: If the `apply` attribute in the ZUL file does not point to the correct controller class, components won't be wired, leading to runtime errors.

### Common Pitfalls in MVC

- **NullPointerException**: This can occur if the UI components are not correctly wired in the controller. To avoid this:
  - Ensure that each component's `id` in the ZUL file exactly matches the name used in the `@Wire` annotation.
  - Call `Selectors.wireComponents` in the `doAfterCompose` method to link components properly.

- **Component Not Found**: If the `apply` attribute in the ZUL file does not point to the correct controller class, components won't be wired, leading to runtime errors.

---

## MVVM (Model-View-ViewModel)
In the MVVM pattern, the ViewModel is typically a POJO (Plain Old Java Object) that does not require extending or implementing any specific ZK framework class or interface. This is one of the key strengths of the MVVM pattern, as it decouples the ViewModel from the framework, making it easier to test and maintain.

1. **Model**: Represents the business logic and data.
2. **View**: The UI, which is defined in the ZUL file.
3. **ViewModel**: A class that serves as a link between the View and the Model, managing the state of the View and handling user actions.

### ViewModel Requirements
- **The controller:** No Inheritance Required. You do not need to extend or implement any specific class or interface to create a ViewModel 
- **The zul file:** `viewModel` attribute in the `.zul` file binds the ViewModel class (`MyViewModel`) to the UI.



### Wiring Components in MVVM
No Explicit Wiring: Unlike MVC, there's no need to wire UI components manually. The framework handles this automatically based on the bindings.
The ZUL file specifies the ViewModel using the `viewModel` attribute, and data binding is achieved using annotations like `@bind` and `@load`.

#### ZUL File Example
- Annotations for Binding: Use annotations like @Command, @NotifyChange, @BindingParam, etc., to manage the interaction between the ViewModel and the View.
- The `@bind` and `@load` annotations dynamically bind the UI components to the ViewModel properties.
- The `updateMessage()` method, annotated with `@Command` and `@NotifyChange`, is executed when the button is clicked, updating the label’s value.
```xml
<window title="MVVM Example" border="normal" viewModel="@id('vm') @init('com.example.MyViewModel')">
    <vbox>
        <label value="@load(vm.message)" />
        <textbox value="@bind(vm.inputMessage)" />
        <button label="Click Me" onClick="@command('updateMessage')" />
    </vbox>
</window>
```

#### ViewModel Code Example

```java
package com.example;

import org.zkoss.bind.annotation.Command;
import org.zkoss.bind.annotation.NotifyChange;

public class MyViewModel {

    private String message = "Hello, MVVM!";
    private String inputMessage;

    public String getMessage() {
        return message;
    }

    public String getInputMessage() {
        return inputMessage;
    }

    public void setInputMessage(String inputMessage) {
        this.inputMessage = inputMessage;
    }

    @Command
    @NotifyChange("message")
    public void updateMessage() {
        message = "Hello, " + inputMessage;
    }
}
```

### Event Handling in MVVM

Event handling in MVVM is more declarative:
- Events are associated with methods in the ViewModel using the `@command` annotation in the ZUL file.
- When a user interacts with the UI (e.g., clicking a button), the corresponding method in the ViewModel is invoked.

In the example above:
- The `updateMessage` method is invoked when the button is clicked, updating the `message` property in the ViewModel.

### Common Pitfalls in MVVM

- **NullPointerException**: Although less common in MVVM due to automated wiring, this can still occur if:
  - The ViewModel is not correctly initialized in the ZUL file.
  - The ViewModel’s property names do not match those specified in the `@load` or `@bind` annotations.

- **Incorrect Binding**: If the binding syntax (`@load`, `@bind`, `@command`) is incorrect or mismatched with ViewModel properties or commands, the binding won’t work, and data won’t be passed between the UI and ViewModel correctly.

  - **ViewModel Initialization**: Ensure that the `viewModel` attribute in the ZUL file correctly initializes the ViewModel (`@init('com.example.MyViewModel')`).

  - **Property Matching**: The property names in the ViewModel must match exactly with what is specified in the ZUL file. For instance, if the ZUL file references `vm.inputMessage`, the ViewModel must have a `getInputMessage()` method.

## Comparison Between MVC and MVVM in ZK

| Feature                       | MVC                                    | MVVM                           |
|-------------------------------|----------------------------------------|--------------------------------|
| Coupling with View            | Loose with layout                      | Loose                           |
| Coupling with Component       | Tight                                   | Loose                           |
| Coding in View                | Component ID                            | Data binding expression         |
| Controller Implementation     | Extends ZK's composer                   | a POJO                          |
| UI Data Access                | Direct access                           | Automatic                       |
| Backend Data Access           | Direct access                           | Direct access                   |
| UI Updating                   | Manipulate components                   | Automatic (@NotifyChange)       |
| Component Controlling Granularity | Fine-grained                        | Normal                          |
| Performance                   | High                                    | Normal                          |
| Wiring*                       | Manual using `@Wire` and `Selectors.wireComponents` | Automatic via `@bind`, `@load` annotations |


### Key Differences:
- **Data Binding**: MVVM uses declarative data binding (with `@bind` and `@load` annotations), while MVC requires manual component updates through Java code.
- **Decoupling**: MVVM better decouples the View from the logic, while in MVC, the controller has direct access to View components.
- **Testability**: MVVM allows for better testability, as the ViewModel is easier to mock and test without the need for actual UI components.


