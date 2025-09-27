# MQL5 Controls Library - Function Reference

This comprehensive reference covers all major functions and methods available in the MQL5 Controls Library, organized by class and functionality.

## Base Classes

### CWnd (Base Window Class)

#### Creation and Destruction
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
// Creates a control with specified position and size

virtual void Destroy();
// Destroys the control and cleans up resources

virtual bool CreateBase();
// Internal method for base initialization
```

#### Position and Size Management
```mql5
int Left() const;           // Get left coordinate
int Top() const;            // Get top coordinate
int Right() const;          // Get right coordinate
int Bottom() const;         // Get bottom coordinate
int Width() const;          // Get control width
int Height() const;         // Get control height

virtual bool Move(const int x, const int y);
// Move control to absolute position

virtual bool Shift(const int dx, const int dy);
// Move control by relative offset

virtual bool Resize(const int w, const int h);
// Resize control to new dimensions

bool SetBounds(const int left, const int top, const int right, const int bottom);
// Set all boundaries at once
```

#### Visibility and State Management
```mql5
virtual bool Show();        // Make control visible
virtual bool Hide();        // Hide control
bool IsVisible() const;     // Check if control is visible

virtual bool Enable();      // Enable user interaction
virtual bool Disable();     // Disable user interaction
bool IsEnabled() const;     // Check if control is enabled

virtual bool Activate();    // Set focus to control
virtual bool Deactivate();  // Remove focus from control
bool IsActive() const;      // Check if control has focus
```

#### Properties
```mql5
long Chart() const;         // Get chart ID
int Subwin() const;         // Get subwindow index
string Name() const;        // Get control name
long Id() const;            // Get control ID

void Chart(const long chart_id);       // Set chart ID
void Subwin(const int subwin);         // Set subwindow
void Name(const string name);          // Set control name
void Id(const long id);                // Set control ID
```

#### Event Handling
```mql5
virtual bool OnEvent(const int id, const long &lparam,
                    const double &dparam, const string &sparam);
// Main event handler - override in derived classes

virtual bool OnMouseEvent(const int x, const int y, const uint flags);
// Handle mouse movement events

virtual bool OnClick();                 // Handle click events
virtual bool OnDblClick();             // Handle double-click events
virtual bool OnMouseDown();            // Handle mouse button down
virtual bool OnMouseUp();              // Handle mouse button up

virtual bool OnDragStart();            // Start drag operation
virtual bool OnDragProcess(const int x, const int y);  // Process drag
virtual bool OnDragEnd();              // End drag operation
```

#### Mouse Tracking
```mql5
int MouseX() const;         // Current mouse X coordinate
int MouseY() const;         // Current mouse Y coordinate
uint MouseFlags() const;    // Current mouse button state

virtual bool MouseFocus(const bool focus);  // Set mouse focus
bool IsMouseFocus() const;                   // Check mouse focus state
```

### CWndObj (Simple Control Base)

#### Text Properties
```mql5
string Text() const;                    // Get control text
virtual bool Text(const string text);  // Set control text

virtual bool OnSetText();              // Called when text changes
```

#### Color Management
```mql5
color Color() const;                    // Get text color
virtual bool Color(const color clr);   // Set text color

color ColorBackground() const;                    // Get background color
virtual bool ColorBackground(const color clr);   // Set background color

color ColorBorder() const;                        // Get border color
virtual bool ColorBorder(const color clr);       // Set border color

virtual bool OnSetColor();                       // Text color changed
virtual bool OnSetColorBackground();             // Background color changed
virtual bool OnSetColorBorder();                 // Border color changed
```

#### Font Management
```mql5
string Font() const;                    // Get font name
virtual bool Font(const string font);  // Set font name

int FontSize() const;                   // Get font size
virtual bool FontSize(const int size); // Set font size

virtual bool OnSetFont();              // Font changed
virtual bool OnSetFontSize();          // Font size changed
```

#### Display Properties
```mql5
long ZOrder() const;                    // Get display order
virtual bool ZOrder(const long value); // Set display order

virtual bool OnSetZOrder();            // Display order changed
```

#### Chart Object Events
```mql5
virtual bool OnObjectCreate();         // Object created
virtual bool OnObjectChange();         // Object properties changed
virtual bool OnObjectDelete();         // Object deleted
virtual bool OnObjectDrag();           // Object dragged
```

### CWndContainer (Container Base)

#### Child Control Management
```mql5
virtual bool Add(CWnd* control);       // Add child control
virtual bool Delete(CWnd* control);    // Remove child control
virtual bool Delete(const int index);  // Remove by index

int ControlsTotal() const;              // Get child count
CWnd* Control(const int index) const;  // Get child by index
CWnd* ControlFind(const int id) const; // Find child by ID
```

#### Group Operations
```mql5
virtual bool Move(const int x, const int y);        // Move all children
virtual bool Shift(const int dx, const int dy);     // Shift all children
virtual bool Enable();                              // Enable all children
virtual bool Disable();                             // Disable all children
virtual bool Show();                                // Show all children
virtual bool Hide();                                // Hide all children
```

#### Container Events
```mql5
virtual bool OnResize();               // Container resized
virtual bool OnActivate();             // Container activated
virtual bool OnDeactivate();           // Container deactivated
```

## Simple Controls

### CLabel

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);

bool CreateLabel(const long chart_id, const string name, const int subwin,
                const int x1, const int y1, const int x2, const int y2);
```

### CButton

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### State Management
```mql5
bool Pressed() const;                   // Get pressed state
virtual bool Pressed(const bool flag); // Set pressed state

bool Locking() const;                   // Get locking mode
virtual bool Locking(const bool flag); // Set locking mode
```

#### Button Events
```mql5
virtual bool OnClick();                 // Button clicked
virtual bool OnMouseDown();             // Mouse button down
virtual bool OnMouseUp();               // Mouse button up
```

### CEdit

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Edit Properties
```mql5
bool ReadOnly() const;                      // Get read-only state
virtual bool ReadOnly(const bool flag);    // Set read-only state

uint TextAlign() const;                     // Get text alignment
virtual bool TextAlign(const uint align);  // Set text alignment
```

#### Edit Events
```mql5
virtual bool OnObjectEndEdit();            // Text editing finished
```

### CPanel

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Panel Properties
```mql5
ENUM_BORDER_TYPE BorderType() const;                          // Get border type
virtual bool BorderType(const ENUM_BORDER_TYPE border_type); // Set border type

uint Alignment() const;                     // Get text alignment
virtual bool Alignment(const uint alignment); // Set text alignment
```

### CPicture

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);

bool CreatePicture(const long chart_id, const string name, const int subwin,
                  const int x1, const int y1, const int x2, const int y2);
```

#### Image Management
```mql5
string BmpName() const;                     // Get bitmap filename
virtual bool BmpName(const string name);   // Set bitmap filename
```

## Complex Controls

### CDialog / CAppDialog

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Component Creation
```mql5
virtual bool CreateCaption();              // Create title bar
virtual bool CreateButtonClose();          // Create close button
virtual bool CreateClientArea();           // Create content area
```

#### Client Area Properties
```mql5
bool ClientAreaVisible() const;            // Check client area visibility
int ClientAreaLeft() const;                // Get client area left
int ClientAreaTop() const;                 // Get client area top
int ClientAreaRight() const;               // Get client area right
int ClientAreaBottom() const;              // Get client area bottom
int ClientAreaWidth() const;               // Get client area width
int ClientAreaHeight() const;              // Get client area height
```

#### Dialog Events
```mql5
virtual bool OnClickCaption();             // Caption clicked
virtual bool OnClickButtonClose();         // Close button clicked
virtual bool OnDialogDragStart();          // Start dialog drag
virtual bool OnDialogDragProcess(const int x, const int y); // Process drag
virtual bool OnDialogDragEnd();            // End dialog drag
```

### CListView

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);

virtual bool CreateRow(const int index);   // Create list row
```

#### Item Management
```mql5
bool AddItem(const string text, const long value = 0);  // Add list item
int ItemsTotal() const;                                  // Get item count

string ItemText(const int index) const;                 // Get item text
bool ItemText(const int index, const string text);     // Set item text

long ItemValue(const int index) const;                  // Get item value
bool ItemValue(const int index, const long value);     // Set item value
```

#### Selection Management
```mql5
string Select(const int index);            // Select item by index
bool SelectByText(const string text);      // Select item by text
bool SelectByValue(const long value);      // Select item by value

long Value() const;                         // Get selected value
int Current() const;                        // Get selected index
```

#### Display Management
```mql5
virtual bool TotalView(const int total);               // Set visible items
virtual bool RowState(const int index, const bool select); // Set row state
virtual bool CheckView();                              // Check row visibility
virtual bool Redraw();                                 // Redraw control
```

#### Scroll Events
```mql5
virtual bool OnVScrollShow();              // Vertical scroll shown
virtual bool OnVScrollHide();              // Vertical scroll hidden
virtual bool OnScrollLineDown();           // Scroll down one line
virtual bool OnScrollLineUp();             // Scroll up one line
```

#### List Events
```mql5
virtual bool OnItemClick();               // Item clicked
virtual bool OnChangeList();              // Selection changed
```

### CComboBox

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Component Creation
```mql5
virtual bool CreateEdit();                // Create edit component
virtual bool CreateButton();              // Create dropdown button
virtual bool CreateList();                // Create dropdown list
```

#### Item Management
```mql5
bool AddItem(const string text, const long value = 0);  // Add dropdown item
int ItemsTotal() const;                                  // Get item count
```

#### Selection Management
```mql5
string Select(const int index);           // Select item by index
bool SelectByText(const string text);     // Select item by text
bool SelectByValue(const long value);     // Select item by value

long Value() const;                        // Get selected value
string Text() const;                       // Get current text
virtual bool Text(const string text);     // Set current text
```

#### List Display
```mql5
virtual bool ListShow();                  // Show dropdown list
virtual bool ListHide();                  // Hide dropdown list
virtual bool ListViewItems(const int items); // Set visible items
```

#### ComboBox Events
```mql5
virtual bool OnChangeList();              // Selection changed
```

### CCheckBox

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### State Management
```mql5
bool Checked() const;                      // Get checked state
virtual bool Checked(const bool flag);    // Set checked state

long Value() const;                        // Get associated value
virtual bool Value(const long value);     // Set associated value
```

### CRadioButton

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Component Creation
```mql5
virtual bool CreateButton();              // Create button component
virtual bool CreateLabel();               // Create label component
```

#### State Management
```mql5
bool State() const;                        // Get selected state
virtual bool State(const bool flag);      // Set selected state
```

#### Radio Events
```mql5
virtual bool OnClickButton();             // Button clicked
virtual bool OnClickLabel();              // Label clicked
```

### CSpinEdit

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Component Creation
```mql5
virtual bool CreateEdit();                // Create edit component
virtual bool CreateInc();                 // Create increment button
virtual bool CreateDec();                 // Create decrement button
```

#### Value Management
```mql5
long Value() const;                        // Get current value
virtual bool Value(const long value);     // Set current value

long MinValue() const;                     // Get minimum value
virtual bool MinValue(const long value);  // Set minimum value

long MaxValue() const;                     // Get maximum value
virtual bool MaxValue(const long value);  // Set maximum value
```

#### Spin Events
```mql5
virtual bool OnClickInc();                // Increment clicked
virtual bool OnClickDec();                // Decrement clicked
virtual bool OnChangeValue();             // Value changed
```

### CScroll / CScrollV / CScrollH

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Component Creation
```mql5
virtual bool CreateBack();                // Create background
virtual bool CreateInc();                 // Create increment button
virtual bool CreateDec();                 // Create decrement button
virtual bool CreateThumb();               // Create draggable thumb
```

#### Position Management
```mql5
int MinPos() const;                        // Get minimum position
virtual bool MinPos(const int pos);       // Set minimum position

int MaxPos() const;                        // Get maximum position
virtual bool MaxPos(const int pos);       // Set maximum position

int CurrPos() const;                       // Get current position
virtual bool CurrPos(const int pos);      // Set current position

int CalcPos(const int coord);             // Calculate position from coordinate
```

#### Scroll Events
```mql5
virtual bool OnClickInc();                // Increment clicked
virtual bool OnClickDec();                // Decrement clicked
virtual bool OnThumbDragStart();          // Start thumb drag
virtual bool OnThumbDragProcess(const int x, const int y); // Process thumb drag
virtual bool OnThumbDragEnd();            // End thumb drag
```

## Auxiliary Structures

### CRect

#### Properties
```mql5
int Left;       // X coordinate of upper-left corner
int Top;        // Y coordinate of upper-left corner
int Right;      // X coordinate of lower-right corner
int Bottom;     // Y coordinate of lower-right corner

int Width();    // Rectangle width (Right - Left)
int Height();   // Rectangle height (Bottom - Top)
```

#### Methods
```mql5
void SetBound(const int left, const int top, const int right, const int bottom);
// Set rectangle boundaries

void Move(const int x, const int y);
// Move rectangle to absolute position

void Shift(const int dx, const int dy);
// Move rectangle by relative offset

bool Contains(const int x, const int y) const;
// Check if point is inside rectangle

string Format() const;
// Convert coordinates to string representation
```

### CDateTime

#### Property Access
```mql5
string MonthName() const;               // Get full month name
string ShortMonthName() const;          // Get abbreviated month name
string DayName() const;                 // Get full day name
string ShortDayName() const;            // Get abbreviated day name
int DaysInMonth() const;                // Get days in current month
```

#### Date/Time Setting
```mql5
datetime DateTime() const;                      // Get complete date/time
void DateTime(const datetime dt);               // Set complete date/time

void Date(const datetime date);                 // Set date portion
void Time(const datetime time);                 // Set time portion

void Year(const int year);                      // Set year
void Mon(const int month);                      // Set month
void Day(const int day);                        // Set day
void Hour(const int hour);                      // Set hour
void Min(const int minute);                     // Set minute
void Sec(const int second);                     // Set second
```

#### Time Arithmetic
```mql5
// Second operations
void SecDec();                                  // Subtract one second
void SecDec(const int seconds);                 // Subtract specified seconds
void SecInc();                                  // Add one second
void SecInc(const int seconds);                 // Add specified seconds

// Minute operations
void MinDec();                                  // Subtract one minute
void MinDec(const int minutes);                 // Subtract specified minutes
void MinInc();                                  // Add one minute
void MinInc(const int minutes);                 // Add specified minutes

// Hour operations
void HourDec();                                 // Subtract one hour
void HourDec(const int hours);                  // Subtract specified hours
void HourInc();                                 // Add one hour
void HourInc(const int hours);                  // Add specified hours

// Day operations
void DayDec();                                  // Subtract one day
void DayDec(const int days);                    // Subtract specified days
void DayInc();                                  // Add one day
void DayInc(const int days);                    // Add specified days

// Month operations
void MonDec();                                  // Subtract one month
void MonDec(const int months);                  // Subtract specified months
void MonInc();                                  // Add one month
void MonInc(const int months);                  // Add specified months

// Year operations
void YearDec();                                 // Subtract one year
void YearDec(const int years);                  // Subtract specified years
void YearInc();                                 // Add one year
void YearInc(const int years);                  // Add specified years
```

## Event Constants

### Chart Events
```mql5
CHARTEVENT_OBJECT_CLICK      // Object clicked
CHARTEVENT_OBJECT_DRAG       // Object dragged
CHARTEVENT_OBJECT_ENDEDIT    // Text editing finished
CHARTEVENT_OBJECT_DELETE     // Object deleted
CHARTEVENT_OBJECT_CREATE     // Object created
CHARTEVENT_OBJECT_CHANGE     // Object properties changed

CHARTEVENT_CLICK             // Chart clicked
CHARTEVENT_KEYDOWN          // Key pressed
CHARTEVENT_MOUSE_MOVE       // Mouse moved
CHARTEVENT_CHART_CHANGE     // Chart properties changed

CHARTEVENT_CUSTOM           // Custom application events
```

### Mouse Flags
```mql5
MOUSE_LEFT_BUTTON           // Left mouse button
MOUSE_RIGHT_BUTTON          // Right mouse button
MOUSE_MIDDLE_BUTTON         // Middle mouse button
MOUSE_SHIFT_KEY             // Shift key pressed
MOUSE_CTRL_KEY              // Ctrl key pressed
```

### Alignment Constants
```mql5
ALIGN_LEFT                  // Left alignment
ALIGN_CENTER               // Center alignment
ALIGN_RIGHT                // Right alignment
ALIGN_TOP                  // Top alignment
ALIGN_MIDDLE               // Middle alignment
ALIGN_BOTTOM               // Bottom alignment
```

### Border Types
```mql5
BORDER_FLAT                // Flat border
BORDER_RAISED              // Raised 3D border
BORDER_SUNKEN              // Sunken 3D border
```

## Color Constants
```mql5
// Standard UI colors
CONTROLS_DIALOG_COLOR_BG                // Dialog background
CONTROLS_DIALOG_COLOR_CLIENT_BG         // Client area background
CONTROLS_DIALOG_COLOR_CAPTION_BG        // Caption background
CONTROLS_DIALOG_COLOR_BORDER_LIGHT      // Light border
CONTROLS_DIALOG_COLOR_BORDER_DARK       // Dark border
```

## Common Usage Patterns

### Control Creation Pattern
```mql5
// 1. Declare control
CButton m_button;

// 2. Create with position and size
if(!m_button.Create(chart_id, "MyButton", 0, 10, 10, 100, 40))
    return false;

// 3. Set properties
m_button.Text("Click Me");
m_button.ColorBackground(clrBlue);

// 4. Add to parent container
Add(m_button);
```

### Event Handling Pattern
```mql5
virtual bool OnEvent(const int id, const long &lparam,
                    const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == m_button.Name())
        {
            // Handle button click
            return true;
        }
    }

    // Always call parent handler
    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}
```

### Property Update Pattern
```mql5
// Check if property changed before setting
if(m_label.Text() != new_text)
{
    m_label.Text(new_text);
}

// Batch property updates
m_button.Text("New Text");
m_button.ColorBackground(clrGreen);
m_button.Enable();
```

This function reference provides comprehensive coverage of the MQL5 Controls Library API. Use it as a quick reference when implementing control-based trading applications.

## See Also

- [Class Hierarchy](./class-hierarchy.md) - Complete inheritance structure
- [Getting Started Tutorial](../tutorials/getting-started.md) - Practical implementation guide
- [Control Documentation](../) - Detailed control-specific documentation