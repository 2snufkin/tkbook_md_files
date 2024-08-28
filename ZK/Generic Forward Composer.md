# Annotations

## Controller
In summary, the package an annotation comes from helps determine its primary use case within the ZK framework. 
- Annotations from `org.zkoss.zk.ui.annotation` are typically associated with MVC
- while those from `org.zkoss.bind.annotation` are used in MVVM.

| **Annotation** | **Pattern** | **Description** | **Example** |
|----------------|-------------|-----------------|-------------|
| **`@id`**      | MVC, MVVM    | Specifies the ID of a ZUL component for reference in the controller or ViewModel. | `<button id="myButton" label="Click Me"/>` |
| **`@bind`**    | MVVM        | Binds a ZUL component to a property in the ViewModel for two-way data binding. | `<label value="@bind(vm.myModelData)" />` |
| **`@init`**    | MVVM        | Initializes the ViewModel for the View. Specifies the ViewModel class to be used. | `<window apply="org.zkoss.bind.BindComposer" viewModel="@id('vm') @init('com.example.MyViewModel')">` |
| **`@Wire`**    | MVC    | Injects a reference to a ZUL component by its ID into a field in the controller or ViewModel. | `@Wire private Button myButton;` |
| **`@Listen`**  | MVC        | Binds a method to an event for a specific component. | `@Listen("onClick = #myButton")` |
| **`@NotifyChange`** | MVVM   | Indicates that a property has changed and should notify the UI to update. | `@NotifyChange("myModelData")` |
| **`@Command`** | MVVM        | Marks a method in the ViewModel as a command that can be invoked from the UI. | `@Command public void doSomething() {}` |


## Key Annotations in ZUL Files
  It is generally true that if you see annotations like @bind, @init, @command, or @notifyChange in a ZUL file, it typically indicates that the application is using the MVVM (Model-View-ViewModel) architecture. Here’s why
#### `@bind`

- **Package**: `org.zkoss.bind.annotation`
- **Purpose**: Binds a ZUL component to a property in the ViewModel.
- **Description**: The `@bind` annotation enables two-way data binding between a UI component and a property in the ViewModel. This means changes in the UI component are automatically reflected in the ViewModel, and updates in the ViewModel are reflected in the UI component.
- **Example**:
  ```xml
  <window apply="org.zkoss.bind.BindComposer" viewModel="@id('vm') @init('com.example.MyViewModel')">
      <label value="@bind(vm.myModelData)" />
      <textbox value="@bind(vm.myModelData)" />
  </window>
  ```

  In this example, the `label` and `textbox` components are bound to `myModelData` in the ViewModel, allowing for automatic synchronization between the UI and the data model.

#### `@init`

- **Package**: `org.zkoss.bind.annotation`
- **Purpose**: Initializes the ViewModel for the View.
- **Description**: The `@init` annotation is used to specify the ViewModel class and initialize it when the View is loaded. It sets up the initial state and binds the View to the ViewModel.
- **Example**:
  ```xml
  <window apply="org.zkoss.bind.BindComposer" viewModel="@id('vm') @init('com.example.MyViewModel')">
      <!-- UI components here -->
  </window>
  ```

  In this example, `@init` specifies that `MyViewModel` should be initialized and bound to the View as `vm`.

#### `@command`

- **Package**: `org.zkoss.bind.annotation`
- **Purpose**: Defines a command that can be invoked from the UI.
- **Description**: The `@command` annotation marks methods in the ViewModel as commands that can be triggered from ZUL components. These commands handle user actions and are executed in response to events such as button clicks.
- **Example**:
  ```xml
  <button label="Do Something" onClick="@command('doSomething')" />
  ```

  In this example, the `doSomething` command is invoked when the button is clicked. The corresponding method in the ViewModel will handle the action.

#### `@notifyChange`

- **Package**: `org.zkoss.bind.annotation`
- **Purpose**: Notifies the UI of changes to a property.
- **Description**: The `@notifyChange` annotation is used in the ViewModel to indicate that a specific property has changed and that the UI should be updated to reflect this change. It ensures that the data binding is kept in sync with the ViewModel.
- **Example**:
  ```java
  public class MyViewModel {
      private String myModelData;

      @NotifyChange("myModelData")
      public void setMyModelData(String myModelData) {
          this.myModelData = myModelData;
      }

      public String getMyModelData() {
          return myModelData;
      }
  }
  ```

  In this example, the `@NotifyChange` annotation ensures that changes to `myModelData` are automatically reflected in the UI components bound to this property.

### Summary

- **`@id`**: Used to assign a unique ID to a UI component in ZUL, facilitating references in the controller or ViewModel.
- **`@bind`**: Enables two-way data binding between a ZUL component and a property in the ViewModel.
- **`@init`**: Initializes the ViewModel for the View, setting up the initial state and binding.
- **`@command`**: Marks methods in the ViewModel as commands that can be triggered by UI events.
- **`@notifyChange`**: Notifies the UI to update when a property in the ViewModel changes.

These annotations are integral to managing interactions and data binding in ZK applications, enabling efficient and dynamic updates between the UI and underlying data models.
### Summary

- **MVC**:
  - **Annotations Used**: `@id`, `@Wire`, `@Listen`
  - **Focus**: Manual event handling and component wiring. The Controller manages interactions between the View and the Model.

- **MVVM**:
  - **Annotations Used**: `@bind`, `@init`, `@NotifyChange`, `@Command`
  - **Focus**: Two-way data binding between the View and ViewModel. Direct method invocation from the UI to the ViewModel and automatic synchronization of data.

- **Common Annotations**:
  - **`@Wire`**: For injecting component references.
  - **`@Listen`**: For binding methods to events.

This table provides a clear overview of how annotations are utilized in each pattern and highlights the tools available for managing data binding and event handling in ZK applications.

