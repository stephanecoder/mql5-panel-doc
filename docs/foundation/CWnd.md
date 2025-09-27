# CWnd - Base Control Class

The `CWnd` class is the fundamental base class for all MQL5 Standard Library controls. It provides essential functionality for control creation, management, and interaction.

## Class Hierarchy

```
CObject
└── CWnd
    ├── CDragWnd
    ├── CWndContainer
    └── CWndObj
```

## Overview

- **Header File**: `<Controls\Wnd.mqh>`
- **Inherits From**: `CObject`
- **Purpose**: Foundation class providing core control functionality
- **Key Features**: Control geometry, state management, event handling, mouse interactions

## Core Functionality

### Creation and Destruction

#### Create()
Initializes the control with basic parameters.

```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

**Parameters:**
- `chart_id` - Chart identifier
- `name` - Control object name
- `subwin` - Subwindow index
- `x1, y1` - Top-left coordinates
- `x2, y2` - Bottom-right coordinates

**Returns:** `true` if successful, `false` otherwise

#### Destroy()
Removes the control and cleans up resources.

```mql5
virtual void Destroy();
```

### Geometry Management

#### Coordinates
```mql5
int Left() const;           // Get left coordinate
int Top() const;            // Get top coordinate
int Right() const;          // Get right coordinate
int Bottom() const;         // Get bottom coordinate
int Width() const;          // Get control width
int Height() const;         // Get control height
```

#### Movement and Sizing
```mql5
virtual bool Move(const int x, const int y);                    // Absolute move
virtual bool Shift(const int dx, const int dy);                 // Relative move
virtual bool Resize(const int w, const int h);                  // Resize control
```

### State Management

#### Visibility
```mql5
virtual bool Show();        // Show control
virtual bool Hide();        // Hide control
bool IsVisible() const;     // Check visibility
```

#### Interaction State
```mql5
virtual bool Enable();      // Enable interaction
virtual bool Disable();     // Disable interaction
bool IsEnabled() const;     // Check if enabled
```

#### Focus Management
```mql5
virtual bool Activate();    // Activate (focus) control
virtual bool Deactivate();  // Deactivate control
bool IsActive() const;      // Check if active
```

### Event Handling

#### Main Event Handler
```mql5
virtual bool OnEvent(const int id, const long &lparam,
                    const double &dparam, const string &sparam);
```

#### Mouse Events
```mql5
virtual bool OnMouseEvent(const int x, const int y, const uint flags);
virtual bool OnClick(void);
virtual bool OnDblClick(void);
```

#### Drag and Drop
```mql5
virtual bool OnDragStart(void);
virtual bool OnDragProcess(const int x, const int y);
virtual bool OnDragEnd(void);
```

### Mouse Tracking

```mql5
int MouseX() const;         // Current mouse X coordinate
int MouseY() const;         // Current mouse Y coordinate
uint MouseFlags() const;    // Current mouse flags
```

## Properties

| Property | Type | Description |
|----------|------|-------------|
| `m_chart_id` | `long` | Chart identifier |
| `m_subwin` | `int` | Subwindow index |
| `m_name` | `string` | Control name |
| `m_rect` | `CRect` | Control boundaries |
| `m_is_visible` | `bool` | Visibility state |
| `m_is_enabled` | `bool` | Interaction state |
| `m_is_active` | `bool` | Focus state |

## Usage Example

```mql5
#include <Controls\Wnd.mqh>

class CMyControl : public CWnd
{
public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);
};

bool CMyControl::Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2)
{
    // Call parent class creation
    if(!CWnd::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    // Custom initialization
    Enable();
    Show();

    return true;
}

bool CMyControl::OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam)
{
    // Handle custom events
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == Name())
        {
            // Handle click on this control
            return true;
        }
    }

    return false;
}
```

## Best Practices

1. **Always call parent methods** when overriding in derived classes
2. **Check return values** from Create() and other critical methods
3. **Properly manage resources** by calling Destroy() when needed
4. **Handle events appropriately** to maintain control responsiveness
5. **Use coordinate validation** before setting positions and sizes

## Common Patterns

### Control Initialization
```mql5
if(!control.Create(chart_id, "MyControl", 0, 10, 10, 110, 50))
{
    Print("Failed to create control");
    return false;
}
control.Show();
control.Enable();
```

### Event Delegation
```mql5
bool CMyPanel::OnEvent(const int id, const long &lparam,
                      const double &dparam, const string &sparam)
{
    // Delegate to child controls first
    if(m_button.OnEvent(id, lparam, dparam, sparam))
        return true;

    // Handle panel-specific events
    return CWnd::OnEvent(id, lparam, dparam, sparam);
}
```

## See Also

- [CWndObj](./CWndObj.md) - Enhanced base class for control objects
- [CWndContainer](./CWndContainer.md) - Base class for container controls
- [Event Handling Tutorial](../tutorials/event-handling.md)
- [Layout Management](../tutorials/layout-management.md)