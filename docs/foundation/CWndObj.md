# CWndObj - Base Class for Simple Controls

The `CWndObj` class serves as the base class for simple chart object-based controls in the MQL5 Standard Library. It extends `CWnd` with chart object-specific functionality.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndObj
        ├── CBmpButton
        ├── CButton
        ├── CEdit
        ├── CLabel
        ├── CPanel
        └── CPicture
```

## Overview

- **Header File**: `<Controls\WndObj.mqh>`
- **Inherits From**: `CWnd`
- **Purpose**: Base class for simple chart object controls
- **Key Features**: Chart object management, property control, event handling

## Properties Management

### Text Properties

#### Text()
Gets or sets the control's text content.

```mql5
string Text() const;                    // Get text
virtual bool Text(const string text);  // Set text
```

#### Font Management
```mql5
string Font() const;                    // Get font name
virtual bool Font(const string font);  // Set font name

int FontSize() const;                   // Get font size
virtual bool FontSize(const int size); // Set font size
```

### Color Properties

#### Text Color
```mql5
color Color() const;                    // Get text color
virtual bool Color(const color clr);   // Set text color
```

#### Background Color
```mql5
color ColorBackground() const;                    // Get background color
virtual bool ColorBackground(const color clr);   // Set background color
```

#### Border Color
```mql5
color ColorBorder() const;                    // Get border color
virtual bool ColorBorder(const color clr);   // Set border color
```

### Display Order

#### ZOrder()
Controls the display order of overlapping objects.

```mql5
long ZOrder() const;                    // Get Z-order
virtual bool ZOrder(const long value); // Set Z-order
```

## Event Handling

### Chart Object Events

#### OnObjectCreate()
Called when the chart object is created.

```mql5
virtual bool OnObjectCreate(void);
```

#### OnObjectChange()
Called when object properties change.

```mql5
virtual bool OnObjectChange(void);
```

#### OnObjectDelete()
Called when the object is deleted.

```mql5
virtual bool OnObjectDelete(void);
```

#### OnObjectDrag()
Called during object dragging operations.

```mql5
virtual bool OnObjectDrag(void);
```

### Property Change Events

#### Text Change
```mql5
virtual bool OnSetText(void);       // Called when text changes
```

#### Color Change Events
```mql5
virtual bool OnSetColor(void);              // Text color change
virtual bool OnSetColorBackground(void);    // Background color change
virtual bool OnSetColorBorder(void);        // Border color change
```

#### Font Change Events
```mql5
virtual bool OnSetFont(void);       // Font name change
virtual bool OnSetFontSize(void);   // Font size change
```

#### Display Order Change
```mql5
virtual bool OnSetZOrder(void);     // Z-order change
```

### Internal Events

#### OnDestroy()
Called during control destruction.

```mql5
virtual bool OnDestroy(void);
```

#### OnChange()
Called when control state changes.

```mql5
virtual bool OnChange(void);
```

## Usage Example

```mql5
#include <Controls\WndObj.mqh>

class CMyLabel : public CWndObj
{
public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnSetText(void);
    virtual bool OnObjectClick(void);
};

bool CMyLabel::Create(const long chart_id, const string name, const int subwin,
                     const int x1, const int y1, const int x2, const int y2)
{
    // Call parent creation
    if(!CWndObj::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    // Set initial properties
    Text("My Label");
    Color(clrBlue);
    ColorBackground(clrLightGray);
    Font("Arial");
    FontSize(12);

    return true;
}

bool CMyLabel::OnSetText(void)
{
    // Custom behavior when text changes
    Print("Label text changed to: ", Text());
    return CWndObj::OnSetText();
}

bool CMyLabel::OnObjectClick(void)
{
    // Handle click events
    Color(Color() == clrBlue ? clrRed : clrBlue);
    return true;
}
```

## Property Configuration Patterns

### Basic Setup
```mql5
// Create and configure a simple control
if(control.Create(chart_id, "MyControl", 0, 10, 10, 200, 50))
{
    control.Text("Click Me");
    control.Color(clrWhite);
    control.ColorBackground(clrBlue);
    control.Font("Arial Bold");
    control.FontSize(14);
    control.ZOrder(100);
}
```

### Dynamic Property Updates
```mql5
// Update properties based on state
void UpdateControlAppearance(CWndObj &control, bool is_active)
{
    if(is_active)
    {
        control.Color(clrWhite);
        control.ColorBackground(clrGreen);
    }
    else
    {
        control.Color(clrGray);
        control.ColorBackground(clrLightGray);
    }
}
```

## Common Event Handling Patterns

### Property Change Monitoring
```mql5
bool CMyControl::OnSetColor(void)
{
    // Log color changes
    Print("Control color changed to: ", Color());

    // Trigger related updates
    if(Color() == clrRed)
        ColorBorder(clrDarkRed);

    return CWndObj::OnSetColor();
}
```

### State Synchronization
```mql5
bool CMyControl::OnChange(void)
{
    // Synchronize with other controls
    if(m_linked_control != NULL)
        m_linked_control.Color(Color());

    return CWndObj::OnChange();
}
```

## Best Practices

1. **Always call parent methods** when overriding event handlers
2. **Use property change events** for custom validation and side effects
3. **Set properties after creation** for proper initialization
4. **Handle OnDestroy()** for cleanup of custom resources
5. **Use ZOrder strategically** for proper layering of overlapping controls

## Comparison with CWnd

| Feature | CWnd | CWndObj |
|---------|------|---------|
| **Purpose** | Base functionality | Chart object controls |
| **Text Support** | Basic | Full text management |
| **Color Management** | None | Comprehensive |
| **Font Control** | None | Font and size |
| **Chart Object Events** | Basic | Specialized handlers |
| **Property Events** | None | Change notifications |

## See Also

- [CWnd](./CWnd.md) - Base control class
- [CWndContainer](./CWndContainer.md) - Container controls base
- [CLabel](../simple-controls/CLabel.md) - Text label control
- [CButton](../simple-controls/CButton.md) - Button control
- [Property Management Tutorial](../tutorials/property-management.md)