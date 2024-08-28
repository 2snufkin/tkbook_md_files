# ZK Framework: Listbox Component Manual

The `Listbox` component in ZK Framework is a versatile UI component used to display a list of items in a tabular format. It is highly customizable, supports data binding, and can be used for both simple and complex data representations. This manual provides a comprehensive overview of the `Listbox` component, including its subcomponents, attributes, events, data binding capabilities, and practical examples.

## Listbox Components

### Listbox

The `Listbox` is the main container that holds the list of items and columns. It is used to display data in rows and columns, similar to a table.

```xml
<listbox></listbox>
```

### Listhead

The `Listhead` component is used to define the header of the `Listbox`. It contains `Listheader` elements that represent the columns.

```xml
<listhead></listhead>
```

### Listheader

The `Listheader` component represents a single column header in the `Listbox`. It defines the column's label and can include attributes for sorting and alignment.

```xml
<listheader label="Column 1"></listheader>
```

### Listitem

The `Listitem` component represents a single row in the `Listbox`. It contains `Listcell` elements that represent the data in each column for that row.

```xml
<listitem></listitem>
```

### Listcell

The `Listcell` component represents a single cell in a `Listitem`. It holds the data for a particular column in that row.

```xml
<listcell label="Data"></listcell>
```

## Attributes and Properties

### Listbox Attributes

- **`mold`**: Defines the appearance of the `Listbox`. Common values are `"default"` and `"paging"`.
- **`rows`**: Specifies the number of rows to display. If the number of items exceeds this, a scrollbar is shown.
- **`multiple`**: Enables multiple selections when set to `true`.
- **`checkmark`**: Displays checkboxes for item selection when set to `true`.
- **`itemRenderer`**: Specifies a custom renderer for list items.
- **`model`**: Binds the `Listbox` to a data model (usually a `ListModel`).

### Listheader Attributes

- **`label`**: Sets the label of the column header.
- **`align`**: Aligns the content of the column (`"left"`, `"center"`, `"right"`).
- **`sort`**: Enables sorting for the column. Can be `"auto"`, `"none"`, or a custom comparator.
- **`sortDirection`**: Sets the initial sort direction (`"ascending"`, `"descending"`, `"natural"`).

### Listitem Attributes

- **`value`**: Associates a custom object with the `Listitem`. Useful for data binding.
- **`disabled`**: Disables the `Listitem` when set to `true`.
- **`selected`**: Selects the `Listitem` when set to `true`.

### Listcell Attributes

- **`label`**: Sets the label of the cell.
- **`colspan`**: Merges the cell across multiple columns.
- **`style`**: Applies custom CSS styling to the cell.

## Events and Listeners

### Selection Events

- **`onSelect`**: Triggered when a user selects an item or multiple items in the `Listbox`. You can capture this event to determine which items were selected.

```xml
<listbox onSelect="@command('onSelectEvent')">
```

### Sorting Events

- **`onSort`**: Triggered when a column header is clicked to sort the data. Sorting can be handled automatically or manually through event listeners.

```xml
<listheader label="Name" sort="auto" onSort="@command('onSortEvent')"/>
```

## Data Binding

### Model Binding

You can bind a `Listbox` to a `ListModel`, which automatically updates the `Listbox` when the underlying data changes.

```xml
<listbox model="@load(vm.myListModel)">
    <listhead>
        <listheader label="Item"/>
    </listhead>
</listbox>
```

### Renderer Binding

Custom rendering of list items is possible by defining a custom item renderer in Java or through data binding.

```java
// Java-based renderer
listbox.setItemRenderer((ListitemRenderer<MyItem>) (listitem, data, index) -> {
    listitem.appendChild(new Listcell(data.getName()));
    listitem.appendChild(new Listcell(data.getValue()));
});
```

## Examples

### Basic Listbox

```xml
<listbox rows="5">
    <listhead>
        <listheader label="Column 1"/>
        <listheader label="Column 2"/>
    </listhead>
    <listitem>
        <listcell label="Row 1, Cell 1"/>
        <listcell label="Row 1, Cell 2"/>
    </listitem>
    <listitem>
        <listcell label="Row 2, Cell 1"/>
        <listcell label="Row 2, Cell 2"/>
    </listitem>
</listbox>
```

### Listbox with Data Binding

```xml
<listbox model="@load(vm.myListModel)">
    <listhead>
        <listheader label="Item Name"/>
        <listheader label="Item Value"/>
    </listhead>
    <template name="model">
        <listitem>
            <listcell label="@load(each.name)"/>
            <listcell label="@load(each.value)"/>
        </listitem>
    </template>
</listbox>
```

### Listbox with Custom Renderer

```java
// In a ViewModel or Controller
ListModelList<MyItem> model = new ListModelList<>(getItems());
listbox.setModel(model);
listbox.setItemRenderer((ListitemRenderer<MyItem>) (listitem, data, index) -> {
    listitem.appendChild(new Listcell(data.getName()));
    listitem.appendChild(new Listcell(data.getValue()));
});
```

## Best Practices

- **Use Data Binding**: For large datasets, use ZK’s data binding features (`ListModel`, `ListModelList`) to efficiently manage and display data.
- **Optimize Performance**: If your `Listbox` contains many items, consider using paging (`mold="paging"`) to improve performance.
- **Custom Renderers**: Use custom renderers when you need to display complex data or components within `Listbox` cells.
- **Sorting and Filtering**: Implement custom sorting and filtering logic if the default behavior does not meet your requirements.



# Customizing 
Customizing a `Listbox` in ZK Framework involves various techniques, from styling with CSS to creating custom renderers, handling events, and leveraging data binding. By mastering these customization options, you can create rich, dynamic, and user-friendly data displays that are tailored to the needs of your application.

Below are several techniques for customizing a `Listbox`, including styling, custom renderers, templates, and event handling.

## 1. Customizing Appearance with CSS

You can customize the appearance of a `Listbox` using CSS. ZK provides CSS classes for `Listbox`, `Listheader`, `Listitem`, and `Listcell`, which you can override or extend.

### Example

```css
.z-listbox {
    border: 1px solid #ddd;
    background-color: #f9f9f9;
}

.z-listbox-header {
    background-color: #007bff;
    color: white;
    font-weight: bold;
}

.z-listbox-item {
    border-bottom: 1px solid #ddd;
}

.z-listbox-cell {
    padding: 10px;
    font-family: Arial, sans-serif;
}
```

In your ZUL file, these styles will automatically apply:

```xml
<listbox>
    <listhead>
        <listheader label="Name"/>
        <listheader label="Age"/>
    </listhead>
    <listitem>
        <listcell label="John Doe"/>
        <listcell label="28"/>
    </listitem>
    <listitem>
        <listcell label="Jane Smith"/>
        <listcell label="34"/>
    </listitem>
</listbox>
```

### Adding Inline Styles

You can also apply inline styles directly to `Listbox`, `Listheader`, `Listitem`, or `Listcell` components.

```xml
<listbox style="border: 2px solid #000; background-color: #eee;">
    <listhead>
        <listheader label="Name" style="background-color: #007bff; color: white;"/>
        <listheader label="Age" style="background-color: #007bff; color: white;"/>
    </listhead>
    <listitem>
        <listcell label="John Doe" style="padding: 10px;"/>
        <listcell label="28" style="padding: 10px;"/>
    </listitem>
</listbox>
```

## 2. Customizing Content with Templates

ZK allows you to customize the content of each `Listitem` using templates. This is especially useful when displaying complex data or using custom components within the cells.

### Example

```xml
<listbox model="@load(vm.people)">
    <listhead>
        <listheader label="Name"/>
        <listheader label="Details"/>
    </listhead>
    <template name="model">
        <listitem>
            <listcell label="@load(each.name)"/>
            <listcell>
                <vbox>
                    <label value="Age: @load(each.age)"/>
                    <label value="Email: @load(each.email)"/>
                </vbox>
            </listcell>
        </listitem>
    </template>
</listbox>
```

In this example, each `Listitem` uses a custom template that combines labels in a `vbox` layout to display additional details.

## 3. Using a Custom Renderer

When you need more control over how the data is presented, you can use a custom renderer. The `ListitemRenderer` interface allows you to define exactly how each `Listitem` and its `Listcells` are created.

### Example

#### Java Implementation

```java
listbox.setItemRenderer(new ListitemRenderer<Person>() {
    @Override
    public void render(Listitem listitem, Person person, int index) throws Exception {
        Listcell nameCell = new Listcell(person.getName());
        Listcell ageCell = new Listcell(String.valueOf(person.getAge()));
        
        if (person.getAge() > 30) {
            nameCell.setStyle("color: red;");
        }

        listitem.appendChild(nameCell);
        listitem.appendChild(ageCell);
    }
});
```

#### ZUL Implementation with Binding

```xml
<listbox model="@load(vm.people)">
    <listhead>
        <listheader label="Name"/>
        <listheader label="Age"/>
    </listhead>
    <template name="model">
        <listitem self="@renderer(vm.renderItem)">
            <listcell>
                <label value="@load(each.name)" style="@load(vm.getStyle(each.age))"/>
            </listcell>
            <listcell label="@load(each.age)"/>
        </listitem>
    </template>
</listbox>
```

In this example, the `getStyle` method in the ViewModel returns different styles based on the person's age.

## 4. Customizing Behavior with Events

You can customize the behavior of `Listbox` by handling events such as `onSelect`, `onCheck`, and `onSort`. 

### Example

#### Handling Selection Event

```xml
<listbox onSelect="@command('handleSelection')" model="@load(vm.people)">
    <listhead>
        <listheader label="Name"/>
        <listheader label="Age"/>
    </listhead>
</listbox>
```

#### In the ViewModel

```java
@Command
@NotifyChange("selectedPerson")
public void handleSelection(@BindingParam("event") SelectEvent<Listitem, Person> event) {
    selectedPerson = event.getSelectedItems().iterator().next().getValue();
}
```

### Example

#### Handling Sorting Event

```xml
<listbox model="@load(vm.people)">
    <listhead>
        <listheader label="Name" sort="auto(@command('sort', column='name'))"/>
        <listheader label="Age" sort="auto(@command('sort', column='age'))"/>
    </listhead>
</listbox>
```

#### In the ViewModel

```java
@Command
@NotifyChange("people")
public void sort(@BindingParam("column") String column) {
    Collections.sort(people, (p1, p2) -> {
        if (column.equals("name")) {
            return p1.getName().compareTo(p2.getName());
        } else if (column.equals("age")) {
            return Integer.compare(p1.getAge(), p2.getAge());
        }
        return 0;
    });
}
```

## 5. Customizing Pagination

When dealing with large datasets, it’s often necessary to paginate the `Listbox`. ZK provides built-in pagination support using the `mold="paging"` attribute.

### Example

```xml
<listbox mold="paging" pageSize="10" model="@load(vm.people)">
    <listhead>
        <listheader label="Name"/>
        <listheader label="Age"/>
    </listhead>
</listbox>
```

### Customizing the Paging Component

You can customize the paging component by styling or by using a custom `Paging` component.

```xml
<listbox mold="paging" pageSize="10" model="@load(vm.people)">
    <listhead>
        <listheader label="Name"/>
        <listheader label="Age"/>
    </listhead>
    <paging mold="os" totalSize="@load(vm.totalSize)"/>
</listbox>
```

## 6. Adding Checkboxes or Radio Buttons

If you want to allow users to select multiple items with checkboxes or radio buttons, you can customize the `Listbox` with the `checkmark` or `radio` attribute.

### Example

#### Using Checkboxes

```xml
<listbox checkmark="true" model="@load(vm.people)">
    <listhead>
        <listheader label="Select"/>
        <listheader label="Name"/>
        <listheader label="Age"/>
    </listhead>
</listbox>
```

#### Using Radio Buttons

```xml
<listbox multiple="false" model="@load(vm.people)">
    <listhead>
        <listheader label="Select"/>
        <listheader label="Name"/>
        <listheader label="Age"/>
    </listhead>
    <listitem>
        <listcell>
            <radio/>
        </listcell>
        <listcell label="@load(each.name)"/>
        <listcell label="@load(each.age)"/>
    </listitem>
</listbox>
```

## 7. Customizing Selection Mode

By default, `Listbox` supports single or multiple selection modes. You can control this using the `multiple` attribute.

### Single Selection

```xml
<listbox multiple="false" model="@load(vm.people)">
    <!-- ... -->
</listbox>
```

### Multiple Selection

```xml
<listbox multiple="true" model="@load(vm.people)">
    <!-- ... -->
</listbox>
```

## 8. Using Different Molds

ZK supports different rendering molds that change the appearance and behavior of the `Listbox`. The `paging` mold, for instance, allows pagination, while the default mold presents the items in a scrollable list.

### Example

#### Default Mold

```xml
<listbox model="@load(vm.people)">
    <!-- ... -->
</listbox>
```

#### Paging Mold

```xml
<listbox mold="paging" pageSize="10" model="@load(vm.people)">
    <!-- ... -->
</listbox>
```

