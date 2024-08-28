# Communication Between Components in ZK Framework
see my forum question
https://forum.zkoss.org/question/115304/communication-between-viewmodel-and-composer/
## Communication via Global Command

Sometimes, components need to communicate across different views or windows. This can be done using global commands.

### Example:
Assume you have a list of items in one window and want to display the selected item details in another window.

#### Step 1: Define the Components and Command
```xml
<!-- Item List Window -->
<window viewModel="@id('vm') @init('com.example.ItemListViewModel')">
    <listbox model="@load(vm.items)" onSelect="@command('itemSelected')" />
</window>

<!-- Item Detail Window -->
<window viewModel="@id('vm') @init('com.example.ItemDetailViewModel')">
    <label value="@load(vm.selectedItemDetails)" />
</window>
```

#### Step 2: Create the ViewModels
**ItemListViewModel.java**
```java
public class ItemListViewModel {
    private List<String> items = Arrays.asList("Item 1", "Item 2", "Item 3");

    public List<String> getItems() {
        return items;
    }

    @GlobalCommand
    @NotifyChange("selectedItemDetails")
    public void itemSelected(@BindingParam("selected") String selectedItem) {
        Map<String, Object> args = new HashMap<>();
        args.put("selected", selectedItem);
        BindUtils.postGlobalCommand(null, null, "updateDetails", args);
    }
}
```

**ItemDetailViewModel.java**
```java
public class ItemDetailViewModel {
    private String selectedItemDetails;

    @GlobalCommand("updateDetails")
    @NotifyChange("selectedItemDetails")
    public void updateDetails(@BindingParam("selected") String selectedItem) {
        this.selectedItemDetails = "Details for " + selectedItem;
    }

    public String getSelectedItemDetails() {
        return selectedItemDetails;
    }
}
```

### Explanation:
- **@GlobalCommand**: Defines a command that can be called from any view.
- **@BindingParam**: Passes parameters to the command.
- **BindUtils.postGlobalCommand**: Triggers a global command, allowing communication across different ViewModels.


# Communication and Data Passing in ZK Framework: MVC and MVVM Approaches


2. **Using the Execution Context**: ZK's `Execution` class allows controllers to share data via attributes.

   ```java
   public class FirstController extends GenericForwardComposer<Component> {
       public void onClick$button(Event event) {
           String data = "Hello from FirstController!";
           Executions.getCurrent().setAttribute("sharedData", data);
       }
   }

   public class SecondController extends GenericForwardComposer<Component> {
       public void doAfterCompose(Component comp) throws Exception {
           super.doAfterCompose(comp);
           String data = (String) Executions.getCurrent().getAttribute("sharedData");
           // Handle the data
       }
   }
   ```

3. **Event-Based Communication**: ZK allows you to use custom events to pass data between controllers.

   ```java
   public class FirstController extends GenericForwardComposer<Component> {
       public void onClick$button(Event event) {
           String data = "Hello from FirstController!";
           Events.postEvent("onDataPass", secondController.getSelf(), data);
       }
   }

   public class SecondController extends GenericForwardComposer<Component> {
       public void onDataPass(Event event) {
           String data = (String) event.getData();
           // Handle the data
       }
   }
   ```



### 3. Communication and Data Passing in ZK MVVM

In the MVVM pattern, communication and data passing are primarily handled via data binding and command binding. The ViewModel is the central component that connects the view and the model.

#### 3.1. **Passing Data Between ViewModels: MVVM -> MVVM**

In ZK MVVM, ViewModels do not directly interact with each other. Instead, they communicate through shared services, events, or via the execution context.

1. **Shared Services**: You can create a shared service class that multiple ViewModels can use to pass data.
   
   ```java
   public class SharedService {
       private String sharedData;
       public String getSharedData() { return sharedData; }
       public void setSharedData(String data) { this.sharedData = data; }
   }

   public class FirstViewModel {
       private SharedService sharedService;
       public void someMethod() {
           sharedService.setSharedData("Hello from FirstViewModel");
       }
   }

   public class SecondViewModel {
       private SharedService sharedService;
       public void someMethod() {
           String data = sharedService.getSharedData();
           // Handle the data
       }
   }
   ```

2. **Event-Based Communication**: ZK MVVM supports the use of events to pass data between ViewModels.

   ```java
   public class FirstViewModel {
       @Command
       public void sendData() {
           BindUtils.postGlobalCommand(null, null, "receiveData", Collections.singletonMap("data", "Hello from FirstViewModel"));
       }
   }

   public class SecondViewModel {
       @GlobalCommand
       @NotifyChange("data")
       public void receiveData(@BindingParam("data") String data) {
           this.data = data;
       }
   }
   ```

3. **Execution Context**: Similar to MVC, the `Execution` context can be used to share data between ViewModels.

   ```java
   public class FirstViewModel {
       @Command
       public void sendData() {
           Executions.getCurrent().setAttribute("sharedData", "Hello from FirstViewModel");
       }
   }

   public class SecondViewModel {
       @Init
       public void init() {
           String data = (String) Executions.getCurrent().getAttribute("sharedData");
           // Handle the data
       }
   }
   ```

# Communication Using Event Posting in ZK Framework

Event posting is a powerful feature in the ZK framework that facilitates communication between components, controllers, and view models. In your current application, where event posting is utilized, it's essential to understand how this mechanism works in both MVC and MVVM contexts. This section will focus on how to effectively use event posting to pass data and communicate between different components.



### 1. Event Posting in ZK MVC

In the MVC pattern, controllers manage the communication between the view and model. Event posting allows one controller to send events (including data) to another controller or component. Here’s how you can implement this:

#### 1.1. **Posting Events Between Controllers**

Controllers in ZK can use `Events.postEvent()` to send custom events to other components, which can be another controller or any UI component. The receiving component listens for these events and processes the data.

```java
// FirstController.java
public class FirstController extends GenericForwardComposer<Component> {
    private Component anotherComponent; // Reference to the other component


    public void onClick$sendButton(Event event) {
        String data = "Data from FirstController";
        // Post an event to another component
        Events.postEvent(new Event("onCustomEvent", anotherComponent, data));
    }
}

// SecondController.java
public class SecondController extends GenericForwardComposer<Component> {
 @Override
    public void doAfterCompose(Component comp) throws Exception {
        super.doAfterCompose(comp);

        // Add an event listener for "onCustomEvent"
        comp.addEventListener("onCustomEvent", new EventListener<Event>() {
            @Override
            public void onEvent(Event event) throws Exception {
                String receivedData = (String) event.getData();
                // Process the received data
                System.out.println("Received data: " + receivedData);
            }
        });
    }
}
```

In this example:
- The `FirstController` posts an event named `"onCustomEvent"` to `anotherComponent`.
- The `SecondController` listens for this event and retrieves the data via `event.getData()`.

#### 1.2. **Using Global Events**

ZK also supports global events, which are events broadcast to all interested listeners regardless of component hierarchy. This is useful when controllers or components are not directly related in the component tree.

```java
// FirstController.java
public class FirstController extends GenericForwardComposer<Component> {

    public void onClick$sendButton(Event event) {
        String data = "Data from FirstController";
        // Post a global event
        Events.postEvent("onGlobalCustomEvent", null, data);
    }
}

// SecondController.java
public class SecondController extends GenericForwardComposer<Component> {

    public void doAfterCompose(Component comp) throws Exception {
        super.doAfterCompose(comp);
        // Listen for the global event
        EventQueue<Event> eq = EventQueues.lookup("globalQueue");
        eq.subscribe(new EventListener<Event>() {
            public void onEvent(Event event) {
                String receivedData = (String) event.getData();
                // Handle the received data
            }
        });
    }
}
```

Here:
- `Events.postEvent("onGlobalCustomEvent", null, data);` posts a global event.
- The `SecondController` subscribes to the global event queue and processes the received data.

### 2. Event Posting in ZK MVVM

In the MVVM pattern, event posting is used similarly to MVC, but the communication happens between the ViewModel and the UI, or between different ViewModels.

#### 2.1. **Posting Events Between ViewModels**

ZK MVVM allows posting events using `BindUtils.postGlobalCommand()` or `BindUtils.postNotifyChange()`. This enables communication between different ViewModels.

```java
// FirstViewModel.java
public class FirstViewModel {

    @Command
    public void sendData() {
        String data = "Data from FirstViewModel";
        // Post a global command
        BindUtils.postGlobalCommand(null, null, "receiveData", Collections.singletonMap("data", data));
    }
}

// SecondViewModel.java
public class SecondViewModel {

    private String data;

    @GlobalCommand
    @NotifyChange("data")
    public void receiveData(@BindingParam("data") String receivedData) {
        this.data = receivedData;
        // Handle the received data
    }
}
```

- `FirstViewModel` posts a global command `"receiveData"` with associated data.
- `SecondViewModel` listens for this command using the `@GlobalCommand` annotation and updates its state.

#### 2.2. **Using EventQueues for Asynchronous Communication**

EventQueues provide a way to handle events asynchronously in MVVM, similar to the global events in MVC but with better control over the event flow.

```java
// FirstViewModel.java
public class FirstViewModel {

    @Command
    public void sendData() {
        String data = "Data from FirstViewModel";
        // Post an event to the queue
        EventQueues.lookup("customQueue", EventQueues.DESKTOP, true).publish(new Event("onCustomEvent", null, data));
    }
}

// SecondViewModel.java
public class SecondViewModel {

    @Init
    public void init() {
        // Subscribe to the event queue
        EventQueues.lookup("customQueue", EventQueues.DESKTOP, true).subscribe(new EventListener<Event>() {
            public void onEvent(Event event) {
                String receivedData = (String) event.getData();
                // Handle the received data
            }
        });
    }
}
```

- `FirstViewModel` posts an event to a named event queue `"customQueue"`.
- `SecondViewModel` subscribes to this queue and processes events asynchronously.



### EventQueues vs. Global Events in ZK Framework

In the ZK framework, both **EventQueues** and **Global Events** provide mechanisms for communicating between different components or controllers, but they serve slightly different purposes and offer distinct advantages depending on the use case. Below is a comparison of the two approaches to help you understand when to use each.

---

### 1. Global Events

**Global Events** in ZK are used to broadcast events across the entire application, regardless of the component hierarchy. This allows different parts of the application to listen for and respond to events, even if they are not directly related in the component tree.

#### 1.1. **How Global Events Work**

Global events are posted using the `Events.postEvent()` method, where the event is typically associated with a component, but can also be broadcast globally without a specific target component.

```java
// Posting a global event
Events.postEvent("onGlobalCustomEvent", null, data);
```

Components or controllers that want to listen for these events subscribe to them by overriding event handlers or using annotations.

```java
public class MyController extends GenericForwardComposer<Component> {
    public void doAfterCompose(Component comp) throws Exception {
        super.doAfterCompose(comp);
        // Listen for the global event
        EventQueues.lookup("myGlobalEventQueue", EventQueues.APPLICATION, true).subscribe(new EventListener<Event>() {
            public void onEvent(Event event) {
                String receivedData = (String) event.getData();
                // Handle the received data
            }
        });
    }
}
```

#### 1.2. **Use Cases for Global Events**

- **Broadcasting Information Across the Application**: When you need to broadcast a message or data across different parts of your application, global events are a straightforward choice.
- **Simple Communication**: If your communication needs are relatively simple, global events provide an easy-to-implement solution.
- **Component-agnostic Communication**: Use global events when the listener does not need to be tied to a specific component.

#### 1.3. **Limitations of Global Events**

- **No Asynchronous Processing**: Global events are handled synchronously, meaning they are processed immediately when fired, which could block the UI if the event handler contains heavy processing logic.
- **Potential for Tight Coupling**: Overusing global events can lead to tightly coupled components, making the application harder to maintain.

---

### 2. EventQueues

**EventQueues** in ZK provide a more advanced and flexible way to manage events. They allow events to be queued and processed asynchronously, which is particularly useful in complex applications where you need more control over event flow and processing.

#### 2.1. **How EventQueues Work**

EventQueues allow you to create named queues where events can be published and subscribed to. These queues can operate in different scopes, such as desktop, session, or application, and they support asynchronous event processing.

```java
// Publishing an event to an EventQueue
EventQueues.lookup("myQueue", EventQueues.DESKTOP, true).publish(new Event("onCustomEvent", null, data));
```

Listeners can subscribe to these queues to receive events asynchronously:

```java
EventQueues.lookup("myQueue", EventQueues.DESKTOP, true).subscribe(new EventListener<Event>() {
    public void onEvent(Event event) {
        String receivedData = (String) event.getData();
        // Handle the received data
    }
});
```

#### 2.2. **Use Cases for EventQueues**

- **Asynchronous Processing**: If you need to process events without blocking the UI thread, EventQueues provide a way to handle events asynchronously.
- **Complex Communication Scenarios**: When you have more complex event-driven communication requirements, such as handling events across different scopes (e.g., between different desktops or sessions), EventQueues are a better fit.
- **Decoupling Components**: EventQueues can help decouple components by allowing them to communicate through a loosely coupled event-driven mechanism.

#### 2.3. **Advantages of EventQueues**

- **Asynchronous Handling**: Events can be queued and processed in the background, improving application responsiveness.
- **Flexible Scoping**: EventQueues can operate in various scopes (desktop, session, application), giving you more control over where and how events are handled.
- **Event Throttling**: EventQueues support features like event throttling, which can help manage the rate at which events are processed.

---

### 3. Key Differences

| **Feature**           | **Global Events**                                        | **EventQueues**                                       |
|-----------------------|----------------------------------------------------------|-------------------------------------------------------|
| **Processing Mode**   | Synchronous                                              | Asynchronous or Synchronous (based on configuration)  |
| **Scope**             | Application-wide (can be broadcast globally)             | Desktop, session, or application-wide (based on queue type) |
| **Coupling**          | Potential for tighter coupling due to direct event handling | Promotes loose coupling by decoupling event publishing and handling |
| **Complexity**        | Simple to implement for straightforward communication    | More complex, suitable for advanced scenarios          |
| **Use Cases**         | Simple, synchronous communication                        | Complex, asynchronous, or cross-scope communication    |
| **Throttling**        | Not supported                                            | Supported (via queue configuration)                   |

---

### 4. Summary

- **Use Global Events** when you need simple, synchronous communication across different parts of your application. They are easy to implement and sufficient for many straightforward use cases.
- **Use EventQueues** when you require asynchronous event handling, need to manage events across different scopes, or want to decouple components to reduce tight coupling. EventQueues are more powerful and flexible, making them ideal for complex applications with sophisticated event-driven requirements.

Choosing between Global Events and EventQueues depends on the specific needs of your application, including the complexity of the communication patterns and the need for asynchronous processing.

### Communicating Across Different Views or Windows in ZK

**Introduction:**

ZK (ZKoss) is a popular Java-based framework that allows developers to build rich web applications without the need for extensive JavaScript coding. It supports both the MVC (Model-View-Controller) and MVVM (Model-View-ViewModel) design patterns, offering flexibility in how developers structure their applications. One common requirement in web applications is the need to communicate across different views or windows. This document explains the methods and best practices for achieving inter-window or inter-view communication in ZK, and examines the differences depending on whether the MVVM or MVC pattern is used.

---

### 1. **Communication Across Views or Windows in ZK**

In ZK, views or windows are typically represented as components (like `Window`, `Div`, `Include`, etc.). Communication between them can be essential for creating interactive and dynamic user interfaces. The communication can occur in different ways, depending on whether the views are part of the same page or different pages, whether they are loaded in the same browser window, different tabs, or as modal/popup windows.

#### 1.1 **Same Page Communication**
- **Direct Access:** If the components or views are part of the same ZK page, they can be accessed directly via their component IDs or by traversing the component tree. For instance, using `getFellow()` or `getParent()` methods to access another component.
  
- **Event Binding:** Another way is to use ZK’s event-driven model, where one component can send an event that another component is listening for. This can be done using `sendEvent()`, `postEvent()`, or by using custom event listeners.

- **Data Binding:** If you are using MVVM, data binding is a powerful way to automatically propagate changes across views without manually handling events.

#### 1.2 **Cross-Window Communication**
- **Execution Queues:** ZK provides an `ExecutionQueue` mechanism that allows communication across different browser windows or tabs. By pushing messages to a named queue, other windows can subscribe to receive these messages and act accordingly.

- **Server-Push:** ZK supports server-push, which allows the server to send updates to the client asynchronously. This can be useful when changes in one window need to be reflected in another without requiring a manual refresh.

- **Client-Side JavaScript:** Sometimes, using JavaScript to handle communication across windows can be effective, especially for tasks like opening new windows and passing data between them using `window.opener` or `window.postMessage()`.

- **Custom Event Handling:** You can define custom events that can be fired across windows or tabs using ZK’s event system. This allows for a high degree of control and customization.

---

### 2. **Impact of MVVM and MVC Patterns on Communication**

The choice between MVVM and MVC patterns in ZK affects how communication across views or windows is managed. While both patterns can be used to build ZK applications, they differ fundamentally in how data and actions are handled.

#### 2.1 **MVC Pattern**

In the MVC pattern:
- **Controller-Centric Communication:** The controller acts as the central hub for communication between the view and the model. Communication between different views or windows typically involves the controller coordinating these interactions.
  
- **Manual Data Propagation:** Any updates to the model or the view often require manual handling, such as explicitly setting attributes or invoking methods to update the UI.

- **Event Handling:** Events are captured in the controller and appropriate actions are taken to update the view or model. If communication is needed between multiple controllers (across different windows), the controllers must be aware of each other or use shared services.

- **Complex Interactions:** Cross-window communication can be more complex in MVC because the controller must manage state across multiple views and ensure consistency.

#### 2.2 **MVVM Pattern**

In the MVVM pattern:
- **Data Binding:** MVVM leverages ZK’s data binding features, which allows the view to automatically reflect changes in the ViewModel and vice versa. This simplifies communication between views because changes in the ViewModel are propagated automatically.

- **ViewModel-Centric Communication:** In MVVM, the ViewModel often acts as the mediator for interactions. Different ViewModels can communicate by sharing a common service or using event listeners to respond to changes.

- **Less Boilerplate Code:** MVVM reduces the need for explicit handling of UI updates, as most of the communication is managed through data binding and observer patterns. This makes cross-window communication more seamless, especially when dealing with data changes.

- **Event Handling:** Similar to MVC, events can be used to trigger actions, but in MVVM, these are often bound directly to methods in the ViewModel, reducing the need for manual intervention.

---

### 3. **Best Practices**

- **Use Data Binding in MVVM:** When following the MVVM pattern, leverage data binding to minimize the need for manual updates across views. This ensures that changes in the data model automatically reflect in all bound views.

- **Minimize Direct Component Access:** Avoid directly manipulating components in other views or windows. Instead, use events or shared ViewModels to manage state and communication.

- **Use Execution Queues for Cross-Window Communication:** For robust cross-window communication, consider using ZK’s `ExecutionQueue`. It is designed to handle such scenarios and can simplify the communication process.

- **Decouple Logic in MVC:** In MVC, try to decouple the logic as much as possible by using service layers or utility classes that can be accessed by multiple controllers. This reduces dependencies and makes the application easier to maintain.

- **Leverage Server-Push for Real-Time Updates:** If your application requires real-time communication across views or windows, consider using ZK’s server-push technology to keep all views in sync without needing manual refreshes.

---

**Conclusion:**

Communicating across different views or windows in ZK is a common requirement that can be achieved through various methods, depending on the design pattern employed. While the MVC pattern relies more on the controller for managing communication, the MVVM pattern leverages data binding and ViewModels for a more automated approach. Understanding these patterns and using the right tools and techniques can significantly enhance the interactivity and responsiveness of your ZK applications.

### Examples of `ExecutionQueue` Usage in ZK

The `ExecutionQueue` in ZK is a powerful mechanism that allows communication between different sessions, windows, or tabs. It works by letting you send messages to a named queue, and any listeners that are subscribed to this queue can react to these messages. This is particularly useful for scenarios where you need to synchronize state across multiple browser windows or tabs, or even across different users' sessions.

#### **1. Basic Example: Cross-Window Communication**

Suppose you have a ZK application where you want to notify all open windows when a specific action occurs (like updating data on the server).

##### **Step 1: Sending a Message to the ExecutionQueue**

```java
import org.zkoss.zk.ui.Executions;
import org.zkoss.zk.ui.sys.ExecutionCtrl;
import org.zkoss.zk.ui.util.Clients;

public class UpdateDataViewModel {

    public void updateData() {
        // Perform some data update operation
        performDataUpdate();

        // Send a message to the queue
        String queueName = "dataUpdateQueue";
        String message = "Data has been updated!";
        ExecutionCtrl execCtrl = (ExecutionCtrl) Executions.getCurrent();
        execCtrl.getExecutionInfo().getQueue(queueName).publish(message);
    }

    private void performDataUpdate() {
        // Your data update logic goes here
    }
}
```

##### **Step 2: Listening for Messages in Another Window**

In the other windows or views that need to listen for updates, you would subscribe to the `ExecutionQueue` like this:

```java
import org.zkoss.zk.ui.event.EventListener;
import org.zkoss.zk.ui.event.EventQueues;
import org.zkoss.zk.ui.util.Clients;

public class DataListenerViewModel {

    public DataListenerViewModel() {
        String queueName = "dataUpdateQueue";
        EventQueues.lookup(queueName).subscribe(new EventListener<String>() {
            @Override
            public void onEvent(String message) {
                // Handle the message received from the queue
                Clients.showNotification(message);
                refreshView();
            }
        });
    }

    private void refreshView() {
        // Logic to refresh the view goes here
    }
}
```

##### **Explanation:**
- **Sending Messages:** The `updateData()` method updates some data and then sends a message to the `ExecutionQueue` named `dataUpdateQueue`.
- **Subscribing to the Queue:** In the constructor of `DataListenerViewModel`, we subscribe to the `ExecutionQueue` with the same name. When a message is published to this queue, the listener will receive it and can react accordingly.

#### **2. Advanced Example: User-Specific Communication**

Let's say you want to send a message only to a specific user's session, like notifying them of a private message.

##### **Step 1: Creating a User-Specific Queue**

Each user could have a unique queue based on their session ID or username.

```java
import org.zkoss.zk.ui.Executions;
import org.zkoss.zk.ui.util.Clients;

public class NotificationService {

    public void notifyUser(String username, String message) {
        String queueName = "userQueue_" + username;
        EventQueues.lookup(queueName).publish(message);
    }
}
```

##### **Step 2: Subscribing to the User-Specific Queue**

In the user's session or window, you would subscribe to their specific queue.

```java
import org.zkoss.zk.ui.event.EventListener;
import org.zkoss.zk.ui.event.EventQueues;
import org.zkoss.zk.ui.util.Clients;

public class UserViewModel {

    public UserViewModel(String username) {
        String queueName = "userQueue_" + username;
        EventQueues.lookup(queueName).subscribe(new EventListener<String>() {
            @Override
            public void onEvent(String message) {
                // Notify the user with a custom message
                Clients.showNotification("Private Message: " + message);
            }
        });
    }
}
```

##### **Explanation:**
- **Notify Specific Users:** The `NotificationService` can send messages to a user-specific queue. This queue can be uniquely identified by the user's username.
- **User Subscription:** The `UserViewModel` subscribes to their own queue, ensuring that they only receive messages intended for them.

#### **3. Real-Time Notifications Example**

Imagine a real-time collaborative application where users need to be notified when others make changes to shared data.

##### **Step 1: Publishing Collaborative Updates**

```java
public class CollaborationService {

    public void publishUpdate(String docId, String updateInfo) {
        String queueName = "docUpdateQueue_" + docId;
        EventQueues.lookup(queueName, EventQueues.APPLICATION, true).publish(updateInfo);
    }
}
```

##### **Step 2: Subscribing to Updates for a Specific Document**

```java
import org.zkoss.zk.ui.event.EventListener;
import org.zkoss.zk.ui.event.EventQueues;
import org.zkoss.zk.ui.util.Clients;

public class DocumentViewModel {

    public DocumentViewModel(String docId) {
        String queueName = "docUpdateQueue_" + docId;
        EventQueues.lookup(queueName, EventQueues.APPLICATION, true).subscribe(new EventListener<String>() {
            @Override
            public void onEvent(String updateInfo) {
                // Handle the update for the document
                Clients.showNotification("Document updated: " + updateInfo);
                refreshDocumentView();
            }
        });
    }

    private void refreshDocumentView() {
        // Logic to refresh the document view goes here
    }
}
```

##### **Explanation:**
- **Document-Specific Queues:** Each document being edited has its own queue (e.g., `docUpdateQueue_123` for document ID 123).
- **Publishing Updates:** The `CollaborationService` sends updates to the queue associated with the document being modified.
- **Subscribing to Updates:** Each user's view of the document subscribes to its specific queue, ensuring they receive real-time updates as other users make changes.

---

### **Summary**
- **ExecutionQueue** in ZK allows for powerful communication across different views, windows, or even sessions.
- It is useful for scenarios requiring synchronization between multiple browser windows, such as updating UI elements, sending notifications, or coordinating state changes.
- Depending on the use case, queues can be shared globally, specific to users, or specific to particular documents or resources.
  
By effectively using `ExecutionQueue`, you can build responsive, collaborative applications that keep users in sync with the latest changes happening across different parts of your ZK application.

In ZK, `BindUtils.postGlobalCommand()` is specifically designed for use in the MVVM pattern. It allows a ViewModel to post a command that can be received by another ViewModel, even across different views. This mechanism relies on the MVVM data-binding infrastructure, where commands are typically annotated with `@GlobalCommand` in the receiving ViewModel.

However, if you are working with an MVVM pattern on the sending side and an MVC pattern on the receiving side, `BindUtils.postGlobalCommand()` won't directly work in the MVC context because MVC controllers don't use the same annotation-based command mechanism as MVVM.

### **How to Make MVVM and MVC Communicate**

To communicate between an MVVM-based ViewModel and an MVC-based controller, you need to use a different approach. Here’s how you can achieve communication from MVVM to MVC:

#### **1. Use Events Instead of Global Commands**

Since MVC relies on event handling rather than commands, you can use the ZK event system to send events from the ViewModel to a controller.

##### **Step 1: Posting a Custom Event in MVVM**

In your MVVM ViewModel, you can post a custom event to a specific component or a global event to all components using the event system.

```java
import org.zkoss.bind.BindUtils;
import org.zkoss.zk.ui.event.Event;
import org.zkoss.zk.ui.event.Events;
import org.zkoss.zk.ui.Component;

public class FirstViewModel {

    public void sendData() {
        String data = "Hello from FirstViewModel";
        
        // Post a global event (if the target component is known)
        Events.postEvent("onReceiveData", someComponent, data);
    }
}
```

In this code:
- `Events.postEvent()` is used to send an event named `"onReceiveData"` to `someComponent`.
- The `data` string is passed as the event's data, which the MVC controller can handle.

##### **Step 2: Listening for the Event in MVC**

In your MVC controller, you listen for the `"onReceiveData"` event using the `addEventListener` method.

```java
import org.zkoss.zk.ui.event.Event;
import org.zkoss.zk.ui.event.EventListener;
import org.zkoss.zk.ui.util.GenericForwardComposer;

public class SecondController extends GenericForwardComposer<Component> {

    @Override
    public void doAfterCompose(Component comp) throws Exception {
        super.doAfterCompose(comp);

        // Add an event listener for "onReceiveData"
        comp.addEventListener("onReceiveData", new EventListener<Event>() {
            @Override
            public void onEvent(Event event) throws Exception {
                String receivedData = (String) event.getData();
                // Process the received data
                System.out.println("Received data: " + receivedData);
            }
        });
    }
}
```

In this code:
- The `SecondController` listens for the `"onReceiveData"` event.
- When the event is fired, the `onEvent()` method processes the data received from the ViewModel.

### **Summary**

- **MVVM to MVC Communication**: `BindUtils.postGlobalCommand()` won't work directly with MVC controllers because they don't recognize the command annotations used in MVVM. Instead, use `Events.postEvent()` to send events.
- **Receiving in MVC**: In the MVC controller, use `addEventListener()` to listen for these events and process the data accordingly.

By using ZK’s event system, you can effectively enable communication between MVVM and MVC components, allowing for interaction between different design patterns within the same application.