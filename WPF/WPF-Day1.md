# WPF (Windows Presentation Foundation)

> [!Note] WPF (Windows Presentation Foundation)
>
> - formerly known as "Avalon"
> - WPF is a UI framework for building Windows desktop applications using .NET Framework.
> - WPF is a part of .NET Framework 3.0 and later.

> [!tip] WPF vs WinForms
>
> - WPF is a newer technology than WinForms.
> - WPF is more flexible and powerful than WinForms.
> - WPF uses XAML for designing the UI

> [!warning] XAML (Extensible Application Markup Language)
>
> - XAML is a declarative markup language.
> - XAML is used to create the UI of WPF applications.
> - used as seperation of UI and business logic - code(C# / or other .NET languages)
> - allows to create UI without writing code.

> [!danger] features of WPF
>
> - Data binding
> - MVVM (Model-View-ViewModel) design pattern
> - Better controls than WinForms

![](WPF/Pasted%20image%2020240504100211.png)

> [!bug] WPF Architecture
>
> - need CLR (Common Language Runtime) + OS part ( Kernel + DirectX - User32 )
> - DirectX is used for rendering the UI (Graphics)

![](WPF/Pasted%20image%2020240504110129.png)

![](WPF/Pasted%20image%2020240504110235.png)

---

# XML

> [!danger] XML rules
>
> - ==XAML== has the same rules as XML.
>
> 1. only one root element
> 2. any opening tag must have a closing tag. `<tag> </tag>`
> 3. tags are case sensitive. `<tag> </tag>` is different from `<Tag> </Tag>`
> 4. any attribute value must be enclosed in double quotes. `<tag attribute="value"> </tag>`
> 5. you cannot write the same attribute twice in the same tag. `<tag attribute="value" attribute="value"> </tag>`
> 6. No overlapping tags. `<tag1> <tag2> </tag1> </tag2>`

> [!note] XAML Controls
>
> - XAML controls are like C# classes.
> - to create a control, you need to create an instance of the control class. (Button, TextBox, Label, etc.)

```csharp
//Employee.cs
//to use namespace
using System;//namespace
public class Employee
{
    public string Name { get; set; }
    public string Department { get; set; }
}
```

```csharp
//create an instance of the Employee class
Employee emp = new Employee();
emp.Salary = 1000;
emp.Name = "Ash";
emp.id = "IT";
```

```xml
<!-- XAML -->
<!--
    create an instance of Employee class
 -->
 <Employee Name="Ash" id="IT" salary="1000" x:Name="emp" >
 <!--
    Name is not the same as x:Name
    x:Name is used to give a name to the control so that you can access it in the code behind.
  -->

 </Employee>
```

> [!tip] XAML Namespaces
>
> - XAML uses namespaces to define the classes.
> - xmlns (XML Namespace) is used to define the namespace.
> - xmlns is used to define the XAML namespace. `xmlns="namespace"`
> - to add more than one namespace, but only one default namespace and the rest are added as prefixes.(xmlns:prefix="namespace")

```xml
<!-- XAML -->
<root xmlns="namespace" xmlns:prefix="namespace">
    <tag> </tag>
</root>
```

```xml
<!-- MainWindow.xaml -->

<!--
    window is the root element of the XAML file.
    is an instance of the Window class.
 -->
<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"

        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
        <!--
            title is the title of the window.
            property of the Window class.
            used to set the title of the window.

            Height, Width are properties of FrameworkElement class which is the base class of the Window class.

         -->
</Window>


```

> [!warning] if we used prefix we have to use it in the tag

```xml
<!-- MainWindow.xaml -->

<W:Window x:Class="WPFApp1.MainWindow"
        xmlns:W="http://schemas.microsoft.com/winfx/2006/xaml/presentation">

  </W:Window>
```

> [!danger] `x:Class` attribute
>
> - `x:Class` attribute is used to define the class name responsible for the code behind.

> [!tip] add a button to the window

```xml
<!-- MainWindow.xaml -->
<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <!--
        Content is a property of the ContentControl class.
        any class that inherits from the ContentControl class can have the Content property.
     -->
    <Button Content="Click Me" Width="100" Height="50" >
    </Button>

    <!--
        any content between the opening and closing tags of the Button control is displayed as the content of the button.
     -->
<Button Width="100" Height="50" >
    Click Me
</Button>

<Button Click="Save" Width="100" Height="50" >
    Click Me
</Button>
</Window>
```

> [!note] Events in XAML
>
> - press `f4` to open the properties window.
> - click on the button and go to the events tab.
> - you can write the event handler in the code behind.
> - and add the event handler in the XAML file.`Click="Save"`

```csharp
//MainWindow.xaml.cs
using System;
using System.Windows;
using System.Windows.Controls;

namespace WPFApp1
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void Save(object sender, RoutedEventArgs e)
        {
            MessageBox.Show("Button Clicked");
        }
    }
}
```

> [!danger] `StartupUri` attribute
>
> - `StartupUri` attribute is used to set the startup window of the application.
> - `StartupUri` is a property of the Application class.

```xml
<!--  App.xaml -->

<Application x:Class="WPFApp1.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             StartupUri="MainWindow.xaml">
             <!--
                StartupUri is used to set the startup window of the application.

              -->
</Application>
```

> [!bug] `Content` property
>
> - is a property of the ContentControl class.
> - is of type ==object==.
> - any thing between the opening and closing tags of the ContentControl is set to the Content property.
> - that's why you can add only one child to the Content property.
> - so we use some layout controls to add more than one child like `Panel` controls. (StackPanel, Grid, etc.)
> - `Panel` can have multiple children.

```xml
<!-- MainWindow.xaml -->

<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">

    <StackPanel>
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
    </StackPanel>

</Window>



```

---

## Window

![](WPF/Pasted%20image%2020240504105002.png)

### Panel

![](WPF/Pasted%20image%2020240504105324.png)

---

# WPF Layout Controls

> [!danger] Panels
>
> - Panels are used to layout the controls in WPF.
> - Panels are used to arrange the controls in a specific way.
> - `Panel` has a `Children` property which is a collection of controls. `UIElementCollection`
> - `Panel` is a base class for all layout controls.

![](WPF/Pasted%20image%2020240504110035.png)

> [!tip] types of Panels
>
> - `Canvas`: allows elements to be positioned absolutely using fixed coordinates.
> - `DockPanel`: aligns elements against an entire edge of the container.
> - `Grid`: allows elements to be arranged in rows and columns according to invisible table.
> - `StackPanel`: arranges elements in a single line, either horizontally or vertically.
> - `WrapPanel`: arranges elements in a single line, either horizontally or vertically, and when the line is full, it wraps to the next line.

---

# Break

---

> [!tip] `StackPanel`
>
> - `StackPanel` is a layout control that arranges the child controls in a single line.(Horizontally by default)
> - `StackPanel` has an `Orientation` property that can be set to `Horizontal` or `Vertical`.
>   > [!warning] if elements are bigger than the window, they will be cut off.
>   >
>   > - so we can use `WrapPanel` instead of `StackPanel`.

```xml
<!-- MainWindow.xaml -->

<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
<!--
    <StackPanel Orientation="Horizontal">
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
    </StackPanel> -->

    <WrapPanel Orientation="Horizontal">
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
        <Button Content="Click Me" Width="100" Height="50" >
        </Button>
    </WrapPanel>
</Window>
```

> [!danger] `DockPanel`
>
> - `DockPanel` is a layout control that arranges the child controls against an entire edge of the container.
> - (Top, Bottom, Left, Right, Center)
> - by default, the last child fills the remaining space.
> - `DockPanel` has a `LastChildFill` property that can be set to `True` or `False`.

> [!tip] `DockPanel.Dock` attached property
>
> - `DockPanel.Dock` is an attached property that is used to set the position of the ==child== control in the `DockPanel`.
> - `DockPanel.Dock` can be set to `Top`, `Bottom`, `Left`, `Right`.

```xml
<!-- MainWindow.xaml -->

<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <DockPanel LastChildFill="False">
        <Button Content="Click Me" Width="100" Height="50" DockPanel.Dock="Top" Margin="10">
        </Button>
        <Button Content="Click Me" Width="100" Height="50" DockPanel.Dock="Bottom" Margin="10">
        </Button>
        <Button Content="Click Me" Width="100" Height="50" DockPanel.Dock="Left" Margin="10">
        </Button>
        <Button Content="Click Me" Width="100" Height="50" DockPanel.Dock="Right" Margin="10">
        </Button>
        <Button Content="Click Me" Width="100" Height="50"  Margin="10">
        </Button>
    </DockPanel>
</Window>
```

> [!done] we can embed panels inside each other.

> [!done] `HorizontalAlignment` and `VerticalAlignment` properties
>
> - `StackPanel` by default aligns vertically in the center
> - `HorizontalAlignment` and `VerticalAlignment` properties are used to set the alignment of the child controls in the `StackPanel`.
> - `WrapPanel` by default aligns vertically in the top
> - those can be used with the panel or the child control.
> - on the child control, it will override the panel alignment.
> - `<WrapPanel HorizontalAlignment="Center" VerticalAlignment="Center">` this will align the `WrapPanel` in the center. - and all the child controls will be aligned in the center.

> [!danger] `Grid`
>
> - `Grid` is a layout control that arranges the child controls in rows and columns according to an invisible table.
> - overlapping can happen in the `Grid` if 2 controls are in the same cell.
> - `Grid.RowDefinitions` and `Grid.ColumnDefinitions` are used to define the rows and columns of the `Grid`.
> - `Grid.Row` and `Grid.Column` attached properties are used to set the position of the child control in the `Grid`.
> - if not set, the default value is 0.

```xml
<!-- MainWindow.xaml -->

<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <Grid>
        <Grid.RowDefinitions>
            <!-- <RowDefinition ></RowDefinition> -->
            <RowDefinition Height="100"></RowDefinition>
            <!--
                Height="100" will set the height of the row to 100.
             -->
            <RowDefinition Height="Auto"></RowDefinition>
            <!--
                Height="Auto" will set the height of the row to the height of the content.
             -->
            <RowDefinition Height="*"></RowDefinition>
            <!--
                Height="*" will set the height of the row to the remaining space.
             -->
             <RowDefinition Height="0.5*"></RowDefinition>
            <!--
                Height="0.5*" will set the height of the row to the half of the row space.
            -->

            <RowDefinition ></RowDefinition>
            <RowDefinition ></RowDefinition>
        </Grid.RowDefinitions>
        <!--
            this will create 3 rows in the grid.
         -->
        <Grid.ColumnDefinitions>
        <!--
            same as RowDefinitions but for columns.
            width="*" will set the width of the column to the remaining space.
         -->
            <ColumnDefinition width="100"></ColumnDefinition>
            <ColumnDefinition width="Auto"></ColumnDefinition>
            <ColumnDefinition width="*"></ColumnDefinition>
        </Grid.ColumnDefinitions>
        <!--
            this will create 3 columns in the grid.
         -->
        <Button Content="Click Me" Width="100" Height="50">
        </Button>
        <!--
            default value for Grid.Row and Grid.Column is 0.
            (0,0)
         -->
        <Button Content="Click Me" Width="100" Height="50" Grid.Row="0" Grid.Column="1">
        </Button>
        <Button Content="Click Me" Width="100" Height="50" Grid.Row="0" Grid.Column="2">
        </Button>
        <Button Content="Click Me" Width="100" Height="50" Grid.Row="1" Grid.Column="0">
        </Button>
        <Button Content="Click Me" Width="100" Height="50" Grid.Row="1" Grid.Column="1">
        </Button>
        <Button Content="Click Me" Width="100" Height="50" Grid.Row="1" Grid.Column="2">
        </Button>
        <!--
            we can use StackPanel inside the Grid.+
         -->
       <StackPanel Grid.Row="2" Grid.Column="0" >
        <Label >
            Name
        </Label>
        <TextBox Width="100" Height="30">
        </TextBox>
       </StackPanel>
       <!--
        we can use RowSpan and ColumnSpan to make the control span multiple rows or columns.
        -->
        <Button Content="Click Me" Width="100" Height="50" Grid.Row="2" Grid.Column="1" Grid.ColumnSpan="2">
        </Button>

        <Button Content="Click Me" Width="100" Height="50" Grid.Row="2" Grid.Column="2" Grid.RowSpan="2">
        </Button>

    </Grid>
</Window>
```

> [!done] `Grid` resizing
>
> - `Grid` by default resizes the child controls according to the size of the window.

> [!done] `UniformGrid`
>
> - `UniformGrid` is a layout control that arranges the child controls in rows and columns.
> - without using `RowDefinitions` and `ColumnDefinitions`.
> - depending on the number of children, the `UniformGrid` will create the rows and columns.
> - 4 children will create 2 rows and 2 columns.
> - 6 children will create 2 rows and 3 columns.

```xml
<!-- MainWindow.xaml -->

<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <UniformGrid Rows="2" Columns="3">
        <!--
            Rows and Columns are used to set the number of rows and columns in the UniformGrid.
         -->
        <Button Content="Click Me" Width="100" Height="50">
        </Button>
        <Button Content="Click Me" Width="100" Height="50">
        </Button>
        <Button Content="Click Me" Width="100" Height="50">
        </Button>
        <Button Content="Click Me" Width="100" Height="50">
        </Button>
        <Button Content="Click Me" Width="100" Height="50">
        </Button>
        <Button Content="Click Me" Width="100" Height="50">
        </Button>
    </UniformGrid>
</Window>
```

---

### Controls

> [!warning] ContentControl
>
> - `ContentControl` is a base class for controls that can have a single child. (`object Content`)
> - `Window`, `Button`, `Label`, `TextBox`, etc.

> [!danger] `HeaderedContentControl`
>
> - `HeaderedContentControl` is a base class for controls that have a header and content.
> - `HeaderedContentControl` has a `Header` property that is used to set the header of the control.
> - `HeaderedContentControl` has a `Content` property that is used to set the content of the control.
> - to add more than one child, you can use a `Panel` control as the content of the `HeaderedContentControl`.

```xml
<!-- MainWindow.xaml -->

<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <HeaderedContentControl Header="Header">
        <StackPanel>
            <RadioButton>
                option1
            </RadioButton>
            <RadioButton>
                option2
            </RadioButton>
        </StackPanel>
    </HeaderedContentControl>
```

> [!done] `TabControl` & `TabItem`
>
> - `TabControl` is a control that contains a collection of `TabItem` controls.
> - `TabItem` is a control that represents a tab in the `TabControl`.
> - `TabItem` has a `Header` property that is used to set the header of the tab.
> - `TabItem` has a `Content` property that is used to set the content of the tab.

```xml

<!-- MainWindow.xaml -->

<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <TabControl>
        <TabItem Header="Tab1">
            <StackPanel>
                <RadioButton>
                    option1
                </RadioButton>
                <RadioButton>
                    option2
                </RadioButton>
            </StackPanel>
        </TabItem>
        <TabItem Header="Tab2">
            <StackPanel>
                <RadioButton>
                    option1
                </RadioButton>
                <RadioButton>
                    option2
                </RadioButton>
            </StackPanel>
        </TabItem>
    </TabControl>
</Window>
```

> [!danger] `Expander`
>
> - `Expander` is a control that allows the user to expand or collapse the content.
> - `Expander` has a `Header` property that is used to set the header of the `Expander`.
> - `Expander` has a `Content` property that is used to set the content of the `Expander`.
> - so we use Panel controls as the content of the `Expander` to add more than one child.
> - `Expander` has an `ExpandDirection` property that can be set to `Up`, `Down`, `Left`, `Right`.

```xml
<!-- MainWindow.xaml -->

<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <Expander Header="Expander" ExpandDirection="Right">
        <StackPanel>
            <RadioButton>
                option1
            </RadioButton>
            <RadioButton>
                option2
            </RadioButton>
        </StackPanel>
    </Expander>
</Window>
```

---

# Lab

---

# Lec 2

> [!tip] `ScrollViewer`
>
> - `ScrollViewer` is a control that provides a scrollable area. (Vertical, Horizontal, or Both)
> - scroll bars will appear when the content is larger than the `ScrollViewer`.
> - `VerticalScrollBarVisibility` and `HorizontalScrollBarVisibility` properties are used to set the visibility of the scroll bars. (default is `Visible`)
> - to make it only appear when needed, set it to `Auto`.
> - to make it scrollable without the scroll bars, set it to `Hidden`.
> - this is applied both directions.(Vertical, Horizontal)
> - has `Orientation` property that can be set to `Vertical` or `Horizontal`.

> [!warning] Document outline
>
> - `Document outline` is a window that shows the hierarchy of the controls in the XAML file.f

```xml
<!-- MainWindow.xaml -->

<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <ScrollViewer VerticalScrollBarVisibility="Auto" HorizontalScrollBarVisibility="Hidden" Orientation="Vertical">
        <StackPanel>
            <RadioButton>
                option1
            </RadioButton>
            <RadioButton>
                option2
            </RadioButton>
           <!--
            rest of the controls
            -->
        </StackPanel>
    </ScrollViewer>

```

---

> [!done] `UserControl`
>
> - combine multiple controls into a single control
> - used to make components of controls that can be reused in multiple windows.
> - can only have ==one direct== child as it has a `Content` property of type `object`.

> [!tip] `ViewBox`
>
> - used for responsive design.
> - `ViewBox` is a control that scales the content to fit the available space.

> [!tip] `label` vs ` `TextBlock`
>
> - have similar UI but different in the properties

> [!bug] all classes that inherits from `ContentControl` can have only one child.

> [!warning] `Button` `IsDefault` and `IsCancel` properties
>
> - `IsDefault` is used to set the button as the default button.
> - `IsCancel` is used to set the button as the cancel button.
> - when the user presses the `Enter` key, the default button is clicked.
> - when the user presses the `Esc` key, the cancel button is clicked.

```xml
<!-- MainWindow.xaml -->

<Window x:Class="WPFApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <StackPanel>
        <Button Content="Save" Width="100" Height="50" IsDefault="True">
        </Button>
        <!--
            when the user presses the Enter key, this button is clicked.
         -->
        <Button Content="Close" Width="100" Height="50" IsCancel="True">
        </Button>
        <!--
            when the user presses the Esc key, this button is clicked.
         -->
         <Button Content="Click Me" Width="100" Height="50" IsDefault="True" ></Button>
         <!--
            when the user presses the Enter key, while having 2 default buttons, they will be focused in the order they are in the XAML file.
          -->

    </StackPanel>

</Window>
```

---

# Break

---

# Paint for kids

![](WPF/Pasted%20image%2020240504173126.png)

> [!tip] `InkCanvas`
>
> - `InkCanvas` is a control that allows the user to draw on it.
> - `InkCanvas` has a `Stroke` property that is used to set the color and thickness of the pen.

> [!info] create a new Window
>
> - change the `StartupUri` in the `App.xaml` file to the new window.

```xml
<!-- InkCanvasWindow.xaml -->

<Window x:Class="WPFApp1.InkCanvasWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="InkCanvasWindow" Height="450" Width="800">
   <Grid>
   <Grid.RowDefinitions>
         <RowDefinition Height="2*"></RowDefinition>
          <!--
            to make the InkCanvas take double the space of the other controls.
           -->
            <RowDefinition ></RowDefinition>
        </Grid.RowDefinitions>
        <Border BorderThickness="5"  Padding="5"  Background="LightGray">
        <InkCanvas x:Name="inkCanvas" >
            <InkCanvas.DefaultDrawingAttributes>
                <DrawingAttributes Color="Black" Width="5"></DrawingAttributes>
            </InkCanvas.DefaultDrawingAttributes>
        </Border>

    <StackPanel Orientation="Horizontal" Grid.Row="1">
    <GroupBox Header="Colors" Width="100">
        <StackPanel>
            <RadioButton Checked="Color_Changed" >
                Red
            </RadioButton>
            <!--
                Checked event is used to set the color of the pen.
             -->
            <RadioButton >
                Blue
            </RadioButton>
            <RadioButton >
                Green
            </RadioButton>
        </StackPanel>
    </GroupBox>
    </Grid>
</Window>
```

> [!warning] `InkCanvas` `DefaultDrawingAttributes` property
>
> - `DefaultDrawingAttributes` is a property of the `InkCanvas` class.
> - `DefaultDrawingAttributes` is of type `DrawingAttributes`.
> - has properties like `Color`, `Width`, `Height`, `StylusTip`, etc.

```csharp
//InkCanvasWindow.xaml.cs

private void Color_Changed(object sender, RoutedEventArgs e)
{
   inkCanvas.DefaultDrawingAttributes.Color = Colors.Red;//set the color of the pen to red.
   //when the radio button is checked.
}
```

> [!done] to make the color change when the radio button is checked depends on the checked color
>
> - add the `Checked` event to the radio buttons.
> - set the color of the pen to the color of the radio button.

```xml
<!-- InkCanvasWindow.xaml -->

<StatckPanel>
    <RadioButton Checked="Color_Changed" >
        Red
    </RadioButton>
    <RadioButton Checked="Color_Changed" >
        Blue
    </RadioButton>
    <RadioButton Checked="Color_Changed" >
        Green
    </RadioButton>
</StackPanel>
```

```csharp
//InkCanvasWindow.xaml.cs

    private void Color_Changed(object sender, RoutedEventArgs e)
    {
        var color = (sender as RadioButton).Content.ToString();
        switch (color)
        {
            case "Red":
                inkCanvas.DefaultDrawingAttributes.Color = Colors.Red;
                break;
            case "Blue":
                inkCanvas.DefaultDrawingjsonAttributes.Color = Colors.Blue;
                break;
            case "Green":
                inkCanvas.DefaultDrawingAttributes.Color = Colors.Green;
                break;
        }
    }
```

> [!warning] to change the mode of the pen
>
> - create multiple radio buttons for the pen mode.
> - add mode change event to the radio buttons.
> - set the mode of the pen to the mode of the radio button.

```xml
<!-- InkCanvasWindow.xaml -->
<SatckPanel>
    <RadioButton Checked="Mode_Changed" >
        Ink
    </RadioButton>
    <RadioButton Checked="Mode_Changed" >
        Select
    </RadioButton>
    <RadioButton Checked="Mode_Changed" >
        Erase
    </RadioButton>
    <RadioButton Checked="Mode_Changed" >
        EraseByStroke
    </RadioButton>
</StackPanel>
```

> [!done] `InkCanvas` `EditingMode` property
>
> - `EditingMode` can be set to any of the following values.
> - `Ink`: to draw on the `InkCanvas`.
> - `EraseByPoint`: to erase the ink on the `InkCanvas` by point.
> - `EraseByStroke`: to erase the ink on the `InkCanvas` by stroke.
> - `Select`: to select the ink on the `InkCanvas`.

```csharp
//InkCanvasWindow.xaml.cs

    private void Mode_Changed(object sender, RoutedEventArgs e)
    {
        var mode = (sender as RadioButton).Content.ToString();
        switch (mode)
        {
            case "Ink":
                inkCanvas.EditingMode = InkCanvasEditingMode.Ink;
                break;
                case "Select":
                inkCanvas.EditingMode = InkCanvasEditingMode.Select;
                break;
                case "Ereae":
                inkCanvas.EditingMode = InkCanvasEditingMode.EraseByPoint;
                break;
                case "EraseByStroke":
                inkCanvas.EditingMode = InkCanvasEditingMode.EraseByStroke;
                break;
        }
    }
```

> [!danger] to draw shapes on the `InkCanvas`
>
> - `StylusTip` property is used to set the shape of the pen.

> [!done] to copy, Cut, and Paste there are Methods in the `InkCanvas` class
>
> - `CopySelection`: to copy the selected ink.
> - `CutSelection`: to cut the selected ink.
> - `Paste`: to paste the copied or cut ink.

> [!warning] to clear the ink on the `InkCanvas`
>
> - `Strokes` property is used to get the collection of strokes on the `InkCanvas`.
> - we can clear the strokes by calling the `Clear` method on the `Strokes` collection.

> [!done] to save the ink to a file
>
> - use `isf` (Ink Serialized Format) file format.

---

## Binding

> [!danger] Binding
>
> - is the process that establishes a connection between the app UI and the data it displays.
> - 1 way binding: from the source to the target.
> - 2 way binding: from the source to the target and from the target to the source.
> - 1 way to source binding: from the target to the source.

> [!danger] make a Slider control and make it change the font size of the text in a TextBlock control
>
> - to get the data from anywhere use `Binding` class.
> - use `ElementName` property to bind to another control.
> - use `Path` property to bind to a property of the control.

```xml
<!-- MainWindow.xaml -->
<StatckPanel>
    <Slider x:Name="slider" Minimum="10" Maximum="50" Value="20" ValueChanged="Slider_ValueChanged">
    </Slider>
    <TextBlock Text="Hello" FontSize="{Binding ElementName=slider, Path=Value}">
    </TextBlock>
</StackPanel>
```

> [!tip] make 2 text boxes change the value of each other
>
> - use `Mode` property to set the binding mode.(OneWay, TwoWay, OneWayToSource)
> - writing in the first text box will change the value of the second text immediately.
> - writing in the second text box will change the value of the first text but only when the second text box loses focus.
>
>   > [!done] to make the change immediately, set the `UpdateSourceTrigger` property to `PropertyChanged`.
>
> - `TextBox` by default is `TwoWay` binding.
> - there are other properties that its default value is `OneWay` binding.
> - so we use `Mode` property to make sure the binding is `TwoWay`.

> [!bug] `OneTime` binding
>
> - `OneTime` binding is used to set the value of the target control to the value of the source control only once.
> - after that, the value of the target control will not change even if the value of the source control changes.

```xml
<!-- MainWindow.xaml -->
<StatckPanel>
    <TextBox x:Name="textBox1">
    </TextBox>
    <TextBox x:Name="textBox2" Text="{Binding ElementName=textBox1, Path=Text, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}">
    </TextBox>

    <!--
        OnwWayToSource is used to set the value of the source control to the value of the target control.
     -->
    <TextBox x:Name="textBox3" Text="{Binding ElementName=textBox1, Path=Text, Mode=OneWayToSource}">
    </TextBox>

</StackPanel>
```

> [!tip] to change font and preview the changes
>
> - `DockPanel` last child fills the remaining space.
> - we use `LastChildFill` to be `False` to make the last child take only the space it needs.
> - get list of font families and bind it to `ListBox` control using `ItemsSource` property.

```csharp
// MainWindow.xaml.cs
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();//this translates the XAML file to C# code.
        listBox.ItemsSource = Fonts.SystemFontFamilies;//has to be after the InitializeComponent method.

        //we can do the same in the XAML file. instead
    }
}
```

```xml
<!-- MainWindow.xaml -->
<DockPanel LastChildFill="False">
    <ListBox Width="200"  Background="LightGray" Name="listBox" ItemsSource="{x:Static Fonts.SystemFontFamilies}">
    <!--
        to use the static property of a class use x:Static.
     -->


    </ListBox>
    <Border BorderBrush="LightSalmon" BorderThickness="2" Margin="10" DockPanel.Dock="Bottom">
        <TextBox Text="Hello" FontSize="20" Name="txt">
        </TextBox>
    </Border>
    <StackPanel>

<!-- 
    use the selected item of the ListBox to change the font family xt.
 -->
    <TextBlock Text={Binding ElementName=txt, Path=Text} FontFamily="{Binding ElementName=listBox, Path=SelectedItem}" FontSize="16">
    </TextBlock>

    <TextBlock Text={Binding ElementName=txt, Path=Text} FontFamily="{Binding ElementName=listBox, Path=SelectedItem}" FontSize="20">
    </TextBlock>

    <TextBlock Text={Binding ElementName=txt, Path=Text} FontFamily="{Binding ElementName=listBox, Path=SelectedItem}" FontSize="24">
    </TextBlock>


    </StackPanel>
</DockPanel>

```
---

# Lab

- complete the paint for kids application.
- 
![](WPF/Pasted%20image%2020240504185342.png)

