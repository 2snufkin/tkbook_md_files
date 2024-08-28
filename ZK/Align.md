
# Alignment in ZK Framework

In ZK Framework, alignment attributes are crucial for positioning and aligning UI components. They help in creating flexible and responsive user interfaces. Below is a comprehensive guide on the alignment attributes available in ZK, including their functions, differences, and examples.

## 1. `align`

### **Description:**
The `align` attribute is used to control the horizontal alignment of text or content within a component. It applies to components like `label`, `button`, `text`, and other similar elements.

### **Values:**
- **`left`**: Aligns the content to the left.
- **`center`**: Centers the content horizontally.
- **`right`**: Aligns the content to the right.
- **`justify`**: Justifies the content (stretches it to fit the width of the container).

### **Example:**

```xml
<zk>
    <label value="Left Aligned" align="left" />
    <label value="Centered Text" align="center" />
    <label value="Right Aligned" align="right" />
</zk>
```

### **Description:**
- **"Left Aligned"**: The text will be aligned to the left edge of the label's container.
- **"Centered Text"**: The text will be centered horizontally within the label's container.
- **"Right Aligned"**: The text will be aligned to the right edge of the label's container.

## 2. `pack`

### **Description:**
The `pack` attribute is used in layout containers such as `vbox` and `hbox` to control how child components are aligned within the container. It affects the alignment of the child components relative to the container's axis (horizontal or vertical).

### **Values:**
- **`start`**: Aligns child components at the start of the container.
- **`center`**: Aligns child components at the center of the container.
- **`end`**: Aligns child components at the end of the container.

### **Example:**

```xml
<zk>
    <vbox pack="start">
        <label value="Start Aligned" />
        <label value="Start Aligned" />
    </vbox>
    <vbox pack="center">
        <label value="Center Aligned" />
        <label value="Center Aligned" />
    </vbox>
    <vbox pack="end">
        <label value="End Aligned" />
        <label value="End Aligned" />
    </vbox>
</zk>
```

### **Description:**
- **`pack="start"`**: Both labels will be aligned at the top of the `vbox`.
- **`pack="center"`**: Both labels will be centered vertically within the `vbox`.
- **`pack="end"`**: Both labels will be aligned at the bottom of the `vbox`.

## 3. `halign`

### **Description:**
The `halign` attribute controls the horizontal alignment of a component's content. It is applicable to components that support alignment, such as `label` and `button`.

### **Values:**
- **`left`**: Aligns the content to the left.
- **`center`**: Centers the content horizontally.
- **`right`**: Aligns the content to the right.
- **`justify`**: Justifies the content.

### **Example:**

```xml
<zk>
    <label value="Left Aligned" halign="left" />
    <label value="Centered Content" halign="center" />
    <label value="Right Aligned" halign="right" />
</zk>
```

### **Description:**
- **"Left Aligned"**: The text within the label is aligned to the left.
- **"Centered Content"**: The text within the label is centered horizontally.
- **"Right Aligned"**: The text within the label is aligned to the right.

## 4. `valign`

### **Description:**
The `valign` attribute controls the vertical alignment of a component's content. It affects how the content is positioned vertically within its container.

### **Values:**
- **`top`**: Aligns the content to the top of the container.
- **`middle`**: Centers the content vertically within the container.
- **`bottom`**: Aligns the content to the bottom of the container.

### **Example:**

```xml
<zk>
    <label value="Top Aligned" valign="top" />
    <label value="Middle Aligned" valign="middle" />
    <label value="Bottom Aligned" valign="bottom" />
</zk>
```

### **Description:**
- **"Top Aligned"**: The text will be aligned to the top of the label's container.
- **"Middle Aligned"**: The text will be centered vertically within the label's container.
- **"Bottom Aligned"**: The text will be aligned to the bottom of the label's container.

## 5. `hflex` and `vflex`

### **Description:**
The `hflex` and `vflex` attributes control how a component should grow or shrink to fill available horizontal or vertical space within its parent container.

### **Values:**
- **`hflex`** (horizontal flexibility):
  - **`min`**: Component will take up only the space it needs, without expanding.
  - **`1`**: Component will expand to fill available horizontal space.
  - **`auto`**: Component will take up space based on its content, but can also grow.

- **`vflex`** (vertical flexibility):
  - **`min`**: Component will take up only the space it needs, without expanding.
  - **`1`**: Component will expand to fill available vertical space.
  - **`auto`**: Component will take up space based on its content, but can also grow.

### **Example:**

```xml
<zk>
    <hbox>
        <label value="Left" hflex="min" />
        <label value="Flexible Center" hflex="1" />
        <label value="Right" hflex="min" />
    </hbox>
    <vbox>
        <label value="Top" vflex="min" />
        <label value="Flexible Middle" vflex="1" />
        <label value="Bottom" vflex="min" />
    </vbox>
</zk>
```

### **Description:**
- **Horizontal Flex (`hflex`):**
  - **"Left"**: The label will only occupy the space it needs on the left.
  - **"Flexible Center"**: The label will expand to fill the remaining horizontal space between the left and right labels.
  - **"Right"**: The label will only occupy the space it needs on the right.

- **Vertical Flex (`vflex`):**
  - **"Top"**: The label will only occupy the space it needs at the top.
  - **"Flexible Middle"**: The label will expand to fill the remaining vertical space between the top and bottom labels.
  - **"Bottom"**: The label will only occupy the space it needs at the bottom.

## **Differences Between `align` and `pack`**

- **`align`**: 
  - Affects the alignment of text or content within a component. It controls how text is positioned horizontally or vertically within the component itself.
  - Example: `align="center"` centers the text inside a `label` or `button`.

- **`pack`**:
  - Affects the alignment of child components within a container (`hbox`, `vbox`). It controls how child components are positioned relative to the container's axis.
  - Example: `pack="center"` centers child components vertically in a `vbox`.

