## Windows form application

> [!NOTE] windows form application is a desktop application
>
> - it is a GUI application
> - it is a event driven application
> - we can have multiple forms in a single project

```csharp
//view code of form1
Form1.cs
namespace  WindowsFormsApp1
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
    }
}
```

```csharp
//view code of form1 designer
Form1.Designer.cs
namespace  WindowsFormsApp1
{
    partial class Form1
    {
        ///  <summary>
        /// Required designer variable.
        ///  </summary>
        private System.ComponentModel.IContainer components = null;

        ///  <summary>
        /// Clean up any resources being used.
        ///  </summary>
        ///  <param name="disposing">true if managed resources should be disposed; otherwise, false.</param>
        protected override void Dispose(bool disposing)
        {
            if (disposing && (components != null))
            {
                components.Dispose();
            }
            base.Dispose(disposing);
        }

        #region Windows Form Designer generated code

        ///  <summary>
        /// Required method for Designer support - do not modify
        /// the contents of this method with the code editor.
        ///  </summary>
        private void InitializeComponent()
        {
            this.components = new System.ComponentModel.Container();
            this.AutoScaleMode = System.Windows.Forms.AutoScaleMode.Font;
            this.ClientSize = new System.Drawing.Size(800, 450);
            this.Text = "Form1";
        }

        #endregion
    }
}
```

```csharp
//main
//Program.cs
static class Program
{
    ///  <summary>
    /// The main entry point for the application.
    ///  </summary>
    [STAThread]
    static void Main()
    {
        Application.EnableVisualStyles();
        Application.SetCompatibleTextRenderingDefault(false);
        Application.Run(new Form1());//start the application with form1
        //create an object of form1 and pass it to Application.Run()
        //we can make form call another form
    }
}
```

> [!example] Naming of controls
>
> - we can change the name of the controls
> - make sure the name is meaningful (not button1, button2, ...)
> - `txtFirstName` for textbox control that holds first name
> - `btnSubmit` for button control that submits the form
> - `lblFirstName` for label control that holds first name

> [!NOTE] order of controls
>
> - view => tab order order of controls in form1 designer
> - controls are added in the order they are added in the designer
> - we can change the order of controls by changing the order in designer

> [!NOTE] make clear button clear all the textboxes
>
> - make event handler for clear button
>
> ```csharp
> private void btnClear_Click(object sender, EventArgs e)
> {
>     txtFirstName.Text = "";
>     txtLastName.Text = "";
> }
> ```

> [!tip] make button show => show text in textbox in MessageBox
>
> ```csharp
> private void btnShow_Click(object sender, EventArgs e)
> {
> string name = txtFirstName.Text + " " + txtLastName.Text;//this will create 3 objects use string builder instead or use string interpolation
> string name = $"{txtFirstName.Text} {txtLastName.Text}";//string interpolation
> MessageBox.Show(name, "Name");
> }
> ```

> [!example] make button show content of textbox in other form
>
> - btnNewForm_Click
> - create new form `frmNamecs`
> - text box `txtName` and 2 buttons `btnOk` and `btnCancel`
>
> ```csharp
> private void btnNewForm_Click(object sender, EventArgs e)
> {
>    frmNamecs frmName = new frmNamecs();
>   frmName.show();//we can deal with parent form without closing child form
> frmName.ShowDialog();//we can't deal with parent form without closing child form
> //this returns DialogResult
> }
> ```
>
> when click on `btnOk` put the text in `txtName` in `txtFirstName`, `txtLastName` in `Form1`
> to know which button is clicked in `frmNamecs` we can use `DialogResult` property
>
> - change `btnOk` `DialogResult` property to `DialogResult.OK`
> - change `btnCancel` `DialogResult` property to `DialogResult.Cancel`
> - we can use `DialogResult` property to know which button is clicked
>
> ```csharp
> private void btnNewForm_Click(object sender, EventArgs e)
> {
>    frmNamecs frmName = new frmNamecs();
>   if(frmName.ShowDialog() == DialogResult.OK)
>   {
> Text = frmName.Name;//we have to change access modifier of `txtName` to `public` to access it from `Form1`
> //or we can create a property in `frmNamecs` that returns the value of `txtName`
>       Text = "Ok";
>   }
>   else
>   {
>       Text = "Cancel";
>   }
> }
> ```
>
> > [!bug] we have to change access modifier of `txtName` to `public` to access it from `Form1`
> >
> > - or we can create a property in `frmNamecs` that returns the value of `txtName`
> >
> > ```csharp
> > public string Name
> > {
> >    get
> >    {
> >        return txtName.Text;
> >    }
> > }
> > ```

```csharp
//btnNewForm_Click
private  void  btnNewForm_Click(object  sender, EventArgs  e)
{
    frmNamecs  frmName = new  frmNamecs();
    if  (frmName.ShowDialog() == DialogResult.OK)
    {
        string[] names = frmName.Name.Split(' ');
        txtFirstName.Text = names[0];
        txtLastName.Text = names[1];
    }
}

```

#### Claculator Form

> [!tip] Dock
>
> - to make control fill the form and resize with it

> [!warning] resuing event handler
>
> - all numbers button click event handler is the same except for the number
> - we can use the same event handler for all numbers

```cs
private void btnNumber_Click(object sender, EventArgs e)
{
    Button btn = sender as Button;
    txtResult.Text += btn.Text;
}
```

> [!tip] `+` & `-` & `=`
>
> - `+`: save the number in `txtResult` in `firstNumber` and clear `txtResult`
> - `=`:

```cs
string op = "";
int firstNumber;
int secondNumber;
private void btnPlus_Click(object sender, EventArgs e)
{
    Button btn = sender as Button;
     op = btn.Text;//save the operator
    firstNumber = int.Parse(txtResult.Text);//save the number in txtResult
    txtResult.Text = "";//clear txtResult
}

```

```cs
//btnEqual_Click
private void btnEqual_Click(object sender, EventArgs e)
{
    secondNumber = int.Parse(txtResult.Text);
    int result = 0;
    switch (op)
    {
        case "+":
            result = firstNumber + secondNumber;
            break;
        case "-":
            result = firstNumber - secondNumber;
            break;
        case "*":
            result = firstNumber * secondNumber;
            break;
        case "/":
            result = firstNumber / secondNumber;
            break;
    }
    txtResult.Text = result.ToString();
}
```

> [!danger] complete calculator
>
> - (float,double)

> [!example] validating input
>
> - Enabled
> - we can disable writing in `txtResult` by setting `Enabled` property to `false`
> - we can use `KeyPress` event to validate input

> [!tip] create a new form FrmShapes
>
> - listen for mouse click event
> - `MouseEventArgs`: has information about the mouse event
> - `e.X` & `e.Y`: has the coordinates of the mouse click
> - `e.Button`: has the button that is clicked (left, right)

```cs
//FrmShapes_MouseClick
private void FrmShapes_MouseClick(object sender, MouseEventArgs e)
{
    // if (e.Button == MouseButtons.Left)
    // {
        Graphics g = CreateGraphics();
        //can't create object of Graphics class directly
        //we have to use CreateGraphics() to create an object of Graphics class
        g.FillRectangle(Brushes.Red, e.X, e.Y, 10, 10);//to draw rectangle
        //to draw circle we have to use FillEllipse(ellipse is drawn in rectangle)
        g.FillEllipse(Brushes.Red, e.X, e.Y, 10, 10);
        //brushes is a class that holds all the colors

        //to draw at the point of the mouse click
        g.FillEllipse(Brushes.Red, e.X , e.Y , 10, 10);

        //make the center of the circle at the point of the mouse click
        g.FillEllipse(Brushes.Red, e.X - 5, e.Y - 5, 10, 10);// half of the width and height
    // }

}
```

---

# Break

---

> [!tip] menu strip
>
> - used to create menu (file, edit, color, ...)
> - draw different colors from the menu

```cs
//FrmShapes_MouseClick
private void FrmShapes_MouseClick(object sender, MouseEventArgs e)
{
    Graphics grx = CreateGraphics();
    Brush brush = new SolidBrush(Color.Red);
    grx.FillEllipse(brush, e.X - 5, e.Y - 5, 10, 10);
    //we want to draw a circle with the color of the menu item that is clicked

}
```

```cs
//colorToolStripMenuItem_Click
Brush brush;
private void redToolStripMenuItem_Click(object sender, EventArgs e)
{
    brush = new SolidBrush(Color.Red);
    //to create a color from RGB
    Color.FromArgb(255, 0, 0);
    //to create a color from name
    Color.FromName("Red");
}

```

> [!tip] FrmPaint
>
> - 1. to start from this form `Application.Run(new FrmPaint());`

```cs
//MouseMove
private void FrmPaint_MouseMove(object sender, MouseEventArgs e)
//this event is fired when the mouse is moved
{

    //get object of Graphics class
    Graphics grx = CreateGraphics();

    // draw a circle at the point of the mouse(every time the mouse is moved)
    grx.FillEllipse(Brushes.Red, e.X - 25, e.Y - 25, 50, 50);

    //erase Brush
    Brush eraseBrush = new SolidBrush(BackColor);//BackColor is the background color of the form

    //to draw only when the left button is clicked
       if (e.Button == MouseButtons.Left)
    {
        grx.FillEllipse(Brushes.Red, e.X - 5, e.Y - 5, 10, 10);
    }
    else if (e.Button == MouseButtons.Right)
    //to erase when the right button is clicked(draw with background color)
    {
        grx.FillEllipse(eraseBrush, e.X - 5, e.Y - 5, 10, 10);
    }

}
```

> [!tip] Ball movement
>
> - create new form `FrmBall`
> - start the form from `Program.cs` `Application.Run(new FrmBall());`
> - listen for paint event
> - `PaintEventArgs` has information about the paint event
> - `e.Graphics` has the object of `Graphics` class

> > [!warning] Timer
> >
> > - Control => no visual representation(works in the background)
> > - Timer => fire an event after a specific time interval(Tick event)

```cs
//FrmBall_Paint
private void FrmBall_Paint(object sender, PaintEventArgs e)
{
    //create object of Graphics class
    Graphics grx = e.Graphics;

    //draw ball intially
    grx.FillEllipse(Brushes.Red, 0, 30, 50, 50);

    //use timer to move the ball
}
```

```cs
//start timer
private void btnStart_Click(object sender, EventArgs e)
{
    timer1.Enabled = true;
}
//stop timer
private void btnStop_Click(object sender, EventArgs e)
{
    timer1.Enabled = false;
}
```

```cs
int x = 0;
bool flag = true;
//timer1_Tick
private void timer1_Tick(object sender, EventArgs e)
{



    //we use flag to change the direction of the ball
    if (flag)
    {
        //move the ball to the right
        x += 10;
    }
    else
    {
        //move the ball to the left
        x -= 10;
    }

    //to change the direction of the ball when it reaches the end of the form
    if (x > this.ClientSize.Width - 50)
    {
       flag = false;
    }else if (x <= 0)
    {
        flag = true;
    }

    //fire paint event to draw the ball
    this.invalidate();

}
```

```cs
//FrmBall_Paint
private void FrmBall_Paint(object sender, PaintEventArgs e)
{
    //create object of Graphics class
    Graphics grx = e.Graphics;

    //draw ball intially
    grx.FillEllipse(Brushes.Red, x, 30, 50, 50);//x is the x coordinate of the ball changing every time the timer ticks
}
```

> [!danger] make stop & start as one button
>
> - we can use `timer1.Enabled` to know if the timer is enabled or not
> - if the timer is enabled => stop the timer
> - if the timer is disabled => start the timer

```cs
//btnStart_Click
private void btnStart_Click(object sender, EventArgs e)
{
    timer1.Enabled = !timer1.Enabled;
    if (timer1.Enabled)
    {
        btnStart.Text = "Stop";
    }
    else
    {
        btnStart.Text = "Start";
    }

}
```

> [!tip] formEmployee
>
> - create new form `formEmployee`
> - create class `Employee`

> [!danger] ListBox & ComboBox
>
> - ListBox: display a list of items
> - ComboBox: display a list of items and allow the user to select one item
> - listBox events:
>   - SelectedIndexChanged: fired when the selected item is changed

```cs
//Employee.cs
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string JobTitle { get; set; }

    public override string ToString()
    {
        return $"Name: {Name}, JobTitle: {JobTitle}";
    }
}

```

```cs
//formEmployee
//create object of Employee class in the form
public partial class formEmployee : Form
{
    Employee emp = new Employee();
    public formEmployee()
    {
        InitializeComponent();
    }
}
```

```cs
// lstNames_SelectedIndexChanged
//handle the event when the selected item is changed
private void lstNames_SelectedIndexChanged(object sender, EventArgs e)
{
    //get the selected employee
    emp = lstNames.SelectedItem as Employee;
    //display the selected employee in the textboxes
    txtId.Text = emp.Id.ToString();
    txtName.Text = emp.Name;
    txtJobTitle.Text = emp.JobTitle;
}
```

> [!tip] start form with employee list

```cs
//formEmployee_Load
private void formEmployee_Load(object sender, EventArgs e)
{
    //create employee list
    Employee emp1 = new Employee() { Id = 1, Name = "Ahmed", JobTitle = "Developer" };
    Employee emp2 = new Employee() { Id = 2, Name = "Ali", JobTitle = "Developer" };
    Employee emp3 = new Employee() { Id = 3, Name = "Mohamed", JobTitle = "Developer" };
    lstNames.Items.Add(emp1);
    lstNames.Items.Add(emp2);
    lstNames.Items.Add(emp3);
    //we have to override ToString() method in Employee class to display the name of the employee in the listbox

}
```

> [!tip] add new employee

```cs
//btnAdd_Click
private void btnAdd_Click(object sender, EventArgs e)
{
    //create new employee
    Employee emp = new Employee();
    emp.Id = int.Parse(txtId.Text);//as txtId.Text is string we have to convert it to int
    emp.Name = txtName.Text;
    emp.JobTitle = txtJobTitle.Text;

    //add the new employee to the list
    lstNames.Items.Add(emp);
}
```

> [!example] put selected employee in the textboxes

```cs
//lstEmployee_SelectedIndexChanged
private void lstEmployee_SelectedIndexChanged(object sender, EventArgs e)
{
    //get the selected employee and cast it to Employee class
    Employee emp = lstEmployee.SelectedItem as Employee;

    //display the selected employee in the textboxes
    txtId.Text = emp.Id.ToString();
    txtName.Text = emp.Name;
    txtJobTitle.Text = emp.JobTitle;
}
```

# #lab-10
- try  all lecture notes
