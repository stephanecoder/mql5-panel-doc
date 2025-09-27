# MQL5 Controls Library - Complete Class Hierarchy

This document provides a comprehensive overview of the class hierarchy in the MQL5 Controls Library, showing inheritance relationships and the purpose of each class.

## Base Object Hierarchy

```
CObject (MQL5 Base Class)
└── CWnd (Base Window Class)
    ├── CWndObj (Simple Chart Object Controls)
    │   ├── CLabel
    │   ├── CBmpButton
    │   ├── CButton
    │   ├── CEdit
    │   ├── CPanel
    │   └── CPicture
    │
    └── CWndContainer (Container Controls)
        ├── CDialog
        │   └── CAppDialog
        ├── CWndClient
        │   └── CListView
        ├── CCheckBox
        ├── CCheckGroup
        ├── CRadioButton
        ├── CRadioGroup
        ├── CComboBox
        ├── CSpinEdit
        └── CScroll
            ├── CScrollV (Vertical Scroll Bar)
            └── CScrollH (Horizontal Scroll Bar)
```

## Auxiliary Structures

```
CRect (Rectangle Coordinates)
CDateTime (Date/Time Operations)
```

## Class Categories

### Foundation Classes

#### CObject
- **Purpose**: Base class for all MQL5 objects
- **Features**: Memory management, object identification
- **Direct Descendants**: CWnd

#### CWnd
- **Purpose**: Base class for all window controls
- **Features**: Position, size, visibility, events, mouse handling
- **Key Methods**: Create(), Destroy(), Move(), Resize(), Show(), Hide()
- **Direct Descendants**: CWndObj, CWndContainer

#### CWndObj
- **Purpose**: Base class for simple chart object controls
- **Features**: Text properties, colors, fonts, chart object management
- **Key Methods**: Text(), Color(), Font(), FontSize()
- **Use Case**: Controls that directly wrap chart objects

#### CWndContainer
- **Purpose**: Base class for controls that contain other controls
- **Features**: Child control management, event delegation, layout
- **Key Methods**: Add(), Delete(), ControlsTotal(), Control()
- **Use Case**: Complex controls and dialogs

### Simple Controls (CWndObj Descendants)

#### CLabel
- **Purpose**: Display static text
- **Features**: Text display, font control, color management
- **Typical Use**: Titles, descriptions, status text
- **File**: `<Controls\Label.mqh>`

#### CButton
- **Purpose**: Interactive button for user actions
- **Features**: Click handling, visual states, text display
- **Events**: OnClick(), OnMouseDown(), OnMouseUp()
- **File**: `<Controls\Button.mqh>`

#### CBmpButton
- **Purpose**: Button with bitmap image
- **Features**: Image display, button functionality
- **Use Case**: Icon buttons, toolbar buttons
- **File**: `<Controls\BmpButton.mqh>`

#### CEdit
- **Purpose**: Text input and editing
- **Features**: Text input, validation, read-only mode
- **Events**: OnObjectEndEdit()
- **File**: `<Controls\Edit.mqh>`

#### CPanel
- **Purpose**: Visual container and background
- **Features**: Background colors, borders, grouping
- **Use Case**: Visual organization, section dividers
- **File**: `<Controls\Panel.mqh>`

#### CPicture
- **Purpose**: Display bitmap images
- **Features**: Image loading, scaling, positioning
- **Supported Formats**: BMP files
- **File**: `<Controls\Picture.mqh>`

### Complex Controls (CWndContainer Descendants)

#### CDialog / CAppDialog
- **Purpose**: Main application dialogs and windows
- **Features**: Title bar, client area, drag support, control management
- **Key Methods**: CreateCaption(), CreateClientArea(), CreateButtonClose()
- **Use Case**: Main application windows, modal dialogs
- **File**: `<Controls\Dialog.mqh>`

#### CListView
- **Purpose**: Scrollable list with item selection
- **Features**: Item management, selection, scrolling, visual feedback
- **Key Methods**: AddItem(), Select(), SelectByText(), SelectByValue()
- **Events**: OnItemClick(), OnChangeList()
- **File**: `<Controls\ListView.mqh>`

#### CComboBox
- **Purpose**: Dropdown selection control
- **Features**: Item list, dropdown display, text editing
- **Components**: Edit control, button control, list view
- **Key Methods**: AddItem(), ListShow(), ListHide()
- **File**: `<Controls\ComboBox.mqh>`

#### CCheckBox / CCheckGroup
- **Purpose**: Boolean selection controls
- **Features**: State management, text labels, grouping
- **Key Methods**: Checked(), Text(), Value()
- **Use Case**: Settings, options, multi-selection
- **File**: `<Controls\CheckBox.mqh>`

#### CRadioButton / CRadioGroup
- **Purpose**: Mutually exclusive selection
- **Features**: Group management, exclusive selection
- **Key Methods**: State(), Text(), Color()
- **Use Case**: Single choice from multiple options
- **File**: `<Controls\RadioButton.mqh>`

#### CSpinEdit
- **Purpose**: Numeric input with increment/decrement
- **Features**: Value ranges, step sizes, validation
- **Components**: Edit control, increment/decrement buttons
- **Key Methods**: Value(), MinValue(), MaxValue()
- **File**: `<Controls\SpinEdit.mqh>`

#### CScroll / CScrollV / CScrollH
- **Purpose**: Scrolling functionality
- **Features**: Position tracking, draggable thumb, increment/decrement
- **Key Methods**: MinPos(), MaxPos(), CurrPos()
- **Use Case**: Content scrolling, list navigation
- **File**: `<Controls\Scrolls.mqh>`

#### CWndClient
- **Purpose**: Client area management for scrollable content
- **Features**: Content clipping, scroll coordination
- **Use Case**: Base for scrollable controls like CListView
- **File**: `<Controls\WndClient.mqh>`

## Inheritance Patterns

### Single Inheritance Chain
Most controls follow a single inheritance pattern:
```
CObject → CWnd → CWndObj → CSpecificControl
CObject → CWnd → CWndContainer → CSpecificControl
```

### Composition Patterns
Complex controls use composition of simpler controls:

#### CComboBox Composition
```
CComboBox
├── CEdit (text input)
├── CButton (dropdown button)
└── CListView (dropdown list)
```

#### CSpinEdit Composition
```
CSpinEdit
├── CEdit (numeric input)
├── CButton (increment)
└── CButton (decrement)
```

#### CDialog Composition
```
CDialog
├── CButton (close button)
├── CLabel (caption)
└── CWndContainer (client area)
```

## Interface Contracts

### CWnd Interface
All controls must implement:
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
virtual bool OnEvent(const int id, const long &lparam,
                    const double &dparam, const string &sparam);
virtual bool Move(const int x, const int y);
virtual bool Show();
virtual bool Hide();
```

### CWndObj Interface
Simple controls must implement:
```mql5
virtual bool Text(const string text);
virtual bool Color(const color clr);
virtual bool Font(const string font);
virtual bool FontSize(const int size);
```

### CWndContainer Interface
Container controls must implement:
```mql5
virtual bool Add(CWnd* control);
virtual bool Delete(CWnd* control);
virtual int ControlsTotal() const;
virtual CWnd* Control(const int index) const;
```

## Design Patterns Used

### Template Method Pattern
Base classes define the algorithm structure:
```mql5
// CWnd defines creation pattern
bool Create(...)
{
    if(!CreateBase()) return false;
    if(!CreateComponents()) return false;  // Implemented by subclasses
    if(!InitializeProperties()) return false;
    return true;
}
```

### Composite Pattern
Container controls manage child controls:
```mql5
class CWndContainer : public CWnd
{
private:
    CArrayObj m_controls;  // Child controls collection

public:
    virtual bool Add(CWnd* control);
    virtual bool OnEvent(...);  // Delegates to children
};
```

### Observer Pattern
Event handling uses observer pattern:
```mql5
// Events flow from chart → dialog → controls
OnChartEvent() → CDialog::OnEvent() → CControl::OnEvent()
```

### Factory Pattern
Control creation follows factory pattern:
```mql5
class CControlFactory
{
public:
    static CButton* CreateButton(const string type);
    static CEdit* CreateEdit(const string type);
    static CPanel* CreatePanel(const string type);
};
```

## Memory Management

### Ownership Model
- **Parent containers** own child controls
- **Automatic cleanup** when parent is destroyed
- **Manual cleanup** available via Delete() method

### Lifecycle Management
1. **Creation**: Create() → Add() to parent
2. **Usage**: Event handling, property updates
3. **Destruction**: Parent destroyed → children auto-destroyed

### Best Practices
```mql5
// Good: Container manages lifetime
class CMyDialog : public CAppDialog
{
private:
    CButton m_button;  // Automatic cleanup

public:
    bool Create(...)
    {
        if(!m_button.Create(...)) return false;
        return Add(m_button);  // Container takes ownership
    }
};

// Avoid: Manual memory management
CButton* button = new CButton();  // Requires manual delete
```

## Control Selection Guide

### When to Use Each Control

#### Simple Text Display
- **CLabel** - Static text, titles, descriptions
- **CEdit (read-only)** - Formatted text, data display

#### User Input
- **CEdit** - Text input, numeric input
- **CSpinEdit** - Numeric input with constraints
- **CComboBox** - Selection from predefined options

#### User Actions
- **CButton** - Commands, actions
- **CBmpButton** - Icon-based actions
- **CCheckBox** - Boolean options, multi-selection
- **CRadioButton** - Single selection from group

#### Data Display
- **CListView** - Multiple items, selection
- **CPanel** - Grouping, visual organization
- **CPicture** - Images, icons, graphics

#### Containers
- **CDialog/CAppDialog** - Main windows
- **CWndContainer** - Custom containers
- **CPanel** - Visual grouping

#### Navigation
- **CScrollV/CScrollH** - Content scrolling
- **CComboBox** - Dropdown navigation

## Extensibility

### Creating Custom Controls

#### Inherit from Appropriate Base
```mql5
// For simple controls
class CMyLabel : public CLabel
{
    // Add custom functionality
};

// For complex controls
class CMyPanel : public CWndContainer
{
    // Compose multiple controls
};
```

#### Override Key Methods
```mql5
class CCustomButton : public CButton
{
public:
    virtual bool OnClick();           // Custom click behavior
    virtual bool OnMouseDown();       // Custom mouse handling
    virtual bool Create(...);         // Custom creation logic
};
```

#### Follow Naming Conventions
- **Class names**: C + DescriptiveName (e.g., CTradingPanel)
- **Member variables**: m_ prefix (e.g., m_button_buy)
- **Method names**: PascalCase (e.g., CreateControls)

## Common Integration Patterns

### Expert Advisor Integration
```mql5
// EA hosts the panel
#include <Controls\Dialog.mqh>

class CMyPanel : public CAppDialog { };
CMyPanel MyPanel;

int OnInit()
{
    return MyPanel.Create(...) ? INIT_SUCCEEDED : INIT_FAILED;
}

void OnChartEvent(...)
{
    MyPanel.OnEvent(id, lparam, dparam, sparam);
}
```

### Indicator Integration
```mql5
// Indicator with control panel
#property indicator_chart_window
#include <Controls\Dialog.mqh>

class CIndicatorPanel : public CAppDialog { };
CIndicatorPanel Panel;
```

### Library Integration
```mql5
// Reusable control library
class CTradingPanelLib
{
public:
    static CAppDialog* CreateTradingPanel();
    static CAppDialog* CreateAnalysisPanel();
};
```

This class hierarchy provides a robust foundation for building sophisticated trading interfaces in MQL5. The clear inheritance structure and well-defined interfaces make it easy to understand, extend, and maintain control-based applications.

## See Also

- [Foundation Classes Documentation](../foundation/) - Detailed base class documentation
- [Simple Controls Documentation](../simple-controls/) - Individual control references
- [Complex Controls Documentation](../complex-controls/) - Advanced control usage
- [Getting Started Tutorial](../tutorials/getting-started.md) - Practical implementation guide