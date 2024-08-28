# Opening and Handling Modals 
Opening modals, passing parameters, and receiving data back in ZK can be done efficiently in both MVC and MVVM patterns. However, MVVM provides a more modern and clean approach, especially for complex applications where separation of concerns and data binding are critical. 


## 1. Opening a Modal and Passing parameters

```java
public void openModal() {
    Window modalWindow = (Window) Executions.createComponents("/path/to/modal.zul", null, null);
    modalWindow.doModal();
}
```
### Executions.createComponents

The `Executions.createComponents` method in the ZK Framework is a powerful tool that allows developers to dynamically create ZK components (such as windows, grids, or any other UI elements) at runtime. It is commonly used to load a ZUL  file and generate the corresponding UI components.
It provides flexibility in specifying the component's source (ZUL file), its parent, and any arguments that might be needed to customize its behavior or appearance. This method is particularly useful for creating modals, pop-ups, or any dynamic content that is not known at compile-time.
The `createComponents` method has several overloaded versions, but the most commonly used one has the following signature:
```java
public static Component createComponents(String uri, Component parent, Map<String, Object> arg);

```

1. **`uri` (String)**:  This is the URI (Uniform Resource Identifier) of the ZUL file that you want to load. The URI is typically relative to the web application's context root. The ZUL file at this location will be parsed, and the corresponding ZK components will be created.

2. **`parent` (Component)**: This is the parent component to which the newly created components will be attached. If `parent` is `null`, the newly created components will not be attached to any parent and will need to be attached manually later. If you pass an existing `Window` or `Div` component as the parent, the components defined in the ZUL file will be added as children of that component.

3. **`arg` (Map<String, Object>)**: This is a map of arguments that you can pass to the components defined in the ZUL file. These arguments can be accessed within the ZUL or its corresponding controller or ViewModel. The map’s keys are `String` values that represent the names of the arguments, and the map’s values are `Object` types, which can be anything you need to pass (e.g., strings, lists, custom objects).
   - **Example**: `Map<String, Object> args = new HashMap<>(); args.put("paramName", paramValue);`
   - In the ZUL file's controller or ViewModel, these arguments can be accessed to initialize the component with specific data or configurations.


There are other overloaded versions of `createComponents` that allow more flexibility:

1. **`createComponents(String uri, Component parent)`**: 
   - Creates components without passing any arguments.
   - **Usage Example**: `Executions.createComponents("/path/to/file.zul", parent);`

2. **`createComponents(String uri, Page page, Map<String, Object> arg)`**:
   - Allows specifying a `Page` instead of a parent component.
   - **Usage Example**: `Executions.createComponents("/path/to/file.zul", page, args);`   

```java
// Define the arguments to pass to the modal
Map<String, Object> args = new HashMap<>();
args.put("title", "My Modal Title");
args.put("itemList", Arrays.asList("Item 1", "Item 2", "Item 3"));

// Create the components (load the ZUL file)
Window modalWindow = (Window) Executions.createComponents("/path/to/modal.zul", null, args);

// Display the modal
modalWindow.doModal();
```

In this example:
- The ZUL file at `"/path/to/modal.zul"` is loaded.
- The modal window (`Window` component) is created but not attached to any parent (since `parent` is `null`).
- The arguments `title` and `itemList` are passed to the ZUL file and can be accessed within the ZUL or its controller to initialize the UI.
- The `doModal()` method is called to display the window as a modal dialog.

## 2. Accessing Parameters paased to the Modal Window

In the ZUL's associated controller (MVC) or ViewModel (MVVM), you can access the passed arguments like this:

**In MVC:**

```java
public void doAfterCompose(Component comp) throws Exception {
    super.doAfterCompose(comp);

    Map<String, Object> args = Executions.getCurrent().getArg();
    String title = (String) args.get("title");
    List<String> itemList = (List<String>) args.get("itemList");

    // Use the arguments to initialize components
}
```

**In MVVM:**

```java
public class ModalWindow {

    private String title;
    private String mainLabel;
    private String listHeaderLabel;
    private List<String> itemList;
    private String selectionLabel;
    private int maxSelections;


    @Init
    public void init(@ExecutionArgParam("title") String title,
                     @ExecutionArgParam("mainLabel") String mainLabel,
                     @ExecutionArgParam("listHeaderLabel") String listHeaderLabel,
                     @ExecutionArgParam("itemList") List<String> itemList,
                     @ExecutionArgParam("selectionLabel") String selectionLabel,
                     @ExecutionArgParam("maxSelections") int maxSelections) {
        this.title = title;
        this.mainLabel = mainLabel;
        this.listHeaderLabel = listHeaderLabel;
        this.itemList = itemList;
        this.selectionLabel = selectionLabel;
        this.maxSelections = maxSelections;
    }

    // getters are a Must!
```



## 3.Receiving Response from the Modal

### Receiving Parameters in MVC

To receive parameters from a modal in MVC, you can use events and listeners. After the modal closes, you can trigger an event to send the data back:

```java
// In the modalWindow controller
///////////////////////////////////////////////////////In the modalWindow controller ////////////////////////////////////////////////////////////////////////

import org.zkoss.zk.ui.event.Events;
import org.zkoss.zk.ui.event.Event;

// inside the method that listen to the action button
Events.postEvent(new Event("onModalResult", null, selectedItems));
this.detach();

///////////////////////////////////////////////////////////In the parent controller ////////////////////////////////////////////////////////////////////////

// In the parent controller
public void openModal() {
    Window modalWindow = (Window) Executions.createComponents("/path/to/modal.zul", null, null);
    modalWindow.addEventListener("onModalResult", event -> {
        List<String> selectedItems = (List<String>) event.getData();
        // Process selectedItems...
    });
    modalWindow.doModal();
}
```

### Receiving Parameters in MVVM

In MVVM, you typically use bindings and event listeners as well, but they are often bound directly in the ZUL file or handled within the ViewModel:

```java
// In the modal ViewModel
private void sendResult(List<String> selectedItems) {
    BindUtils.postGlobalCommand(null, null, "onModalResult", Map.of("selectedItems", selectedItems));
}

// In the parent ViewModel
@GlobalCommand
@NotifyChange("selectedItems")
public void onModalResult(@BindingParam("selectedItems") List<String> selectedItems) {
    // Process selectedItems...
}
```

In this approach, you use a global command to send the data back to the parent ViewModel.

## Differences Between MVC and MVVM

- **Separation of Concerns**: MVVM provides a better separation of concerns by keeping the ViewModel separate from the view logic, while MVC often mixes them within the controller.
- **Data Binding**: MVVM relies heavily on data binding, making it easier to manage UI state, whereas MVC requires more manual management of component states.
- **Parameter Passing**: While both patterns allow passing parameters using a `Map`, MVVM provides a cleaner way to inject these parameters into the ViewModel using annotations.
- **Event Handling**: MVVM uses global commands and binding for event handling, making it more declarative compared to the event listener approach in MVC.

## Conclusion

