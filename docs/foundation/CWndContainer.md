# CWndContainer - Base Class for Complex Controls

The `CWndContainer` class serves as the foundation for complex controls that contain and manage child controls in the MQL5 Standard Library.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndContainer
        ├── CCheckBox
        ├── CComboBox
        ├── CDialog
        ├── CRadioButton
        ├── CScroll
        ├── CSpinEdit
        └── CWndClient
```

## Overview

- **Header File**: `<Controls\WndContainer.mqh>`
- **Inherits From**: `CWnd`
- **Purpose**: Base class for controls that contain child controls
- **Key Features**: Child control management, group operations, event delegation

## Container Management

### Adding and Removing Controls

#### Add()
Adds a child control to the container.

```mql5
bool Add(CWnd* control);
```

**Parameters:**
- `control` - Pointer to the control to add

**Returns:** `true` if successful, `false` otherwise

#### Delete()
Removes a child control from the container.

```mql5
bool Delete(CWnd* control);
bool Delete(const int index);
```

**Parameters:**
- `control` - Pointer to the control to remove
- `index` - Index of the control to remove

**Returns:** `true` if successful, `false` otherwise

### Accessing Child Controls

#### ControlsTotal()
Returns the total number of child controls.

```mql5
int ControlsTotal() const;
```

#### Control()
Retrieves a child control by index.

```mql5
CWnd* Control(const int index) const;
```

**Parameters:**
- `index` - Zero-based index of the control

**Returns:** Pointer to the control or `NULL` if not found

#### ControlFind()
Finds a control by its identifier.

```mql5
CWnd* ControlFind(const int id) const;
```

**Parameters:**
- `id` - Control identifier to search for

**Returns:** Pointer to the control or `NULL` if not found

## Group Operations

### Movement and Positioning

#### Move()
Moves all child controls to absolute coordinates.

```mql5
virtual bool Move(const int x, const int y);
```

#### Shift()
Moves all child controls by relative offset.

```mql5
virtual bool Shift(const int dx, const int dy);
```

### State Management

#### Enable/Disable
```mql5
virtual bool Enable();      // Enable all child controls
virtual bool Disable();     // Disable all child controls
```

#### Show/Hide
```mql5
virtual bool Show();        // Show all child controls
virtual bool Hide();        // Hide all child controls
```

#### Activate/Deactivate
```mql5
virtual bool Activate();    // Activate container and children
virtual bool Deactivate();  // Deactivate container and children
```

## Event Handling

### Event Delegation

#### OnEvent()
Handles chart events and delegates to child controls.

```mql5
virtual bool OnEvent(const int id, const long &lparam,
                    const double &dparam, const string &sparam);
```

#### OnMouseEvent()
Processes mouse events for the container and children.

```mql5
virtual bool OnMouseEvent(const int x, const int y, const uint flags);
```

### Container-Specific Events

#### OnResize()
Called when the container is resized.

```mql5
virtual bool OnResize(void);
```

#### OnActivate/OnDeactivate()
```mql5
virtual bool OnActivate(void);      // Called when container becomes active
virtual bool OnDeactivate(void);    // Called when container loses focus
```

## Usage Example

```mql5
#include <Controls\WndContainer.mqh>
#include <Controls\Button.mqh>
#include <Controls\Edit.mqh>

class CMyPanel : public CWndContainer
{
private:
    CButton m_button1;
    CButton m_button2;
    CEdit   m_edit;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);
    void OnButtonClick(const int button_id);
};

bool CMyPanel::Create(const long chart_id, const string name, const int subwin,
                     const int x1, const int y1, const int x2, const int y2)
{
    // Create container
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    // Create and add child controls
    if(!m_button1.Create(chart_id, name+"Button1", subwin, 10, 10, 100, 40))
        return false;
    m_button1.Text("Button 1");
    Add(m_button1);

    if(!m_button2.Create(chart_id, name+"Button2", subwin, 110, 10, 200, 40))
        return false;
    m_button2.Text("Button 2");
    Add(m_button2);

    if(!m_edit.Create(chart_id, name+"Edit", subwin, 10, 50, 200, 80))
        return false;
    m_edit.Text("Enter text here");
    Add(m_edit);

    return true;
}

bool CMyPanel::OnEvent(const int id, const long &lparam,
                      const double &dparam, const string &sparam)
{
    // Handle button clicks
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == m_button1.Name())
        {
            OnButtonClick(1);
            return true;
        }
        if(sparam == m_button2.Name())
        {
            OnButtonClick(2);
            return true;
        }
    }

    // Delegate to parent
    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}

void CMyPanel::OnButtonClick(const int button_id)
{
    string text = "Button " + IntegerToString(button_id) + " clicked!";
    m_edit.Text(text);
}
```

## Container Management Patterns

### Dynamic Control Addition
```mql5
// Add controls dynamically
void AddNewButton(CWndContainer &container, const string text, const int y_pos)
{
    CButton* new_button = new CButton();
    if(new_button.Create(container.Chart(), "DynamicBtn"+IntegerToString(y_pos),
                        container.Subwin(), 10, y_pos, 100, y_pos+30))
    {
        new_button.Text(text);
        container.Add(new_button);
    }
}
```

### Batch Operations
```mql5
// Enable/disable all controls based on condition
void SetContainerState(CWndContainer &container, bool is_enabled)
{
    if(is_enabled)
        container.Enable();
    else
        container.Disable();
}

// Move entire container with all children
void MovePanel(CWndContainer &container, const int new_x, const int new_y)
{
    container.Move(new_x, new_y);
}
```

### Control Enumeration
```mql5
// Process all child controls
void ProcessAllControls(CWndContainer &container)
{
    int total = container.ControlsTotal();
    for(int i = 0; i < total; i++)
    {
        CWnd* control = container.Control(i);
        if(control != NULL)
        {
            // Process each control
            Print("Control ", i, ": ", control.Name());
        }
    }
}
```

## Event Delegation Pattern

```mql5
bool CMyContainer::OnEvent(const int id, const long &lparam,
                          const double &dparam, const string &sparam)
{
    // First, let child controls handle the event
    int total = ControlsTotal();
    for(int i = 0; i < total; i++)
    {
        CWnd* control = Control(i);
        if(control != NULL && control.OnEvent(id, lparam, dparam, sparam))
            return true;  // Event was handled by child
    }

    // Handle container-specific events
    switch(id)
    {
        case CHARTEVENT_CUSTOM:
            // Handle custom container events
            return OnCustomEvent(lparam, dparam, sparam);
    }

    // Delegate to parent class
    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}
```

## Best Practices

1. **Proper Control Lifecycle**
   - Create child controls after container creation
   - Add controls to container immediately after creation
   - Let container handle destruction of child controls

2. **Event Handling**
   - Always delegate unhandled events to child controls
   - Call parent event handlers when overriding
   - Use specific event handlers for better organization

3. **Layout Management**
   - Use relative positioning for child controls
   - Consider container resize events for dynamic layouts
   - Group related controls logically

4. **Resource Management**
   - Container automatically manages child control memory
   - Remove controls properly before container destruction
   - Avoid circular references between containers

## Common Use Cases

- **Dialog Windows** - Main application dialogs with multiple controls
- **Toolbar Panels** - Groups of related buttons and controls
- **Settings Interfaces** - Configuration panels with various input controls
- **Data Entry Forms** - Forms with labels, inputs, and validation
- **Dashboard Layouts** - Complex trading panel interfaces

## See Also

- [CWnd](./CWnd.md) - Base control class
- [CWndObj](./CWndObj.md) - Simple controls base
- [CDialog](../complex-controls/Dialog.md) - Dialog implementation
- [Layout Management Tutorial](../tutorials/layout-management.md)
- [Event Handling Guide](../tutorials/event-handling.md)