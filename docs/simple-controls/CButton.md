# CButton - Button Control

The `CButton` class creates interactive button controls based on the chart object "Button" for user interaction in MQL5 applications.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndObj
        └── CButton
```

## Overview

- **Header File**: `<Controls\Button.mqh>`
- **Base Object**: Button chart object
- **Purpose**: Interactive button for user actions
- **Key Features**: Click handling, button states, visual feedback

## Core Methods

### Creation

#### Create()
Creates the button control with specified parameters.

```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### CreateButton()
Specialized creation methods for different button types.

```mql5
bool CreateButton1(void);  // Example button creation
bool CreateButton2(void);  // Alternative button setup
bool CreateButton3(void);  // Custom button configuration
```

### Button State Management

#### Pressed()
Gets or sets the button's pressed state.

```mql5
bool Pressed() const;                   // Get pressed state
virtual bool Pressed(const bool flag); // Set pressed state
```

#### Locking()
Controls whether the button maintains its pressed state.

```mql5
bool Locking() const;                   // Get locking state
virtual bool Locking(const bool flag); // Set locking state
```

## Event Handling

### Creation Events

#### OnCreate()
Called when the button is created.

```mql5
virtual bool OnCreate(void);
```

### Visibility Events

#### OnShow/OnHide()
Called when button visibility changes.

```mql5
virtual bool OnShow(void);  // Button becomes visible
virtual bool OnHide(void);  // Button becomes hidden
```

### Mouse Events

#### OnMouseDown/OnMouseUp()
Handle mouse button interactions.

```mql5
virtual bool OnMouseDown(void);  // Mouse button pressed
virtual bool OnMouseUp(void);    // Mouse button released
```

### Click Events

#### OnClick()
Called when the button is clicked.

```mql5
virtual bool OnClick(void);
```

#### Custom Click Handlers
```mql5
virtual bool OnClickButton1(void);  // Custom handler for button1
virtual bool OnClickButton2(void);  // Custom handler for button2
```

## Usage Examples

### Basic Button Implementation

```mql5
#include <Controls\Button.mqh>
#include <Controls\Dialog.mqh>

class CMyDialog : public CAppDialog
{
private:
    CButton m_button_ok;
    CButton m_button_cancel;
    CButton m_button_apply;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    bool CreateButtonOK(void);
    bool CreateButtonCancel(void);
    bool CreateButtonApply(void);
    void OnClickButtonOK(void);
    void OnClickButtonCancel(void);
    void OnClickButtonApply(void);
};

bool CMyDialog::Create(const long chart_id, const string name, const int subwin,
                      const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    // Create buttons
    if(!CreateButtonOK()) return false;
    if(!CreateButtonCancel()) return false;
    if(!CreateButtonApply()) return false;

    return true;
}

bool CMyDialog::CreateButtonOK(void)
{
    if(!m_button_ok.Create(m_chart_id, m_name+"ButtonOK", m_subwin,
                          10, 100, 80, 130))
        return false;

    m_button_ok.Text("OK");
    m_button_ok.Color(clrWhite);
    m_button_ok.ColorBackground(clrGreen);
    m_button_ok.Font("Arial Bold");
    m_button_ok.FontSize(12);

    return Add(m_button_ok);
}

bool CMyDialog::CreateButtonCancel(void)
{
    if(!m_button_cancel.Create(m_chart_id, m_name+"ButtonCancel", m_subwin,
                              90, 100, 160, 130))
        return false;

    m_button_cancel.Text("Cancel");
    m_button_cancel.Color(clrWhite);
    m_button_cancel.ColorBackground(clrRed);

    return Add(m_button_cancel);
}

bool CMyDialog::CreateButtonApply(void)
{
    if(!m_button_apply.Create(m_chart_id, m_name+"ButtonApply", m_subwin,
                             170, 100, 240, 130))
        return false;

    m_button_apply.Text("Apply");
    m_button_apply.Color(clrBlack);
    m_button_apply.ColorBackground(clrYellow);

    return Add(m_button_apply);
}

bool CMyDialog::OnEvent(const int id, const long &lparam,
                       const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == m_button_ok.Name())
        {
            OnClickButtonOK();
            return true;
        }
        if(sparam == m_button_cancel.Name())
        {
            OnClickButtonCancel();
            return true;
        }
        if(sparam == m_button_apply.Name())
        {
            OnClickButtonApply();
            return true;
        }
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CMyDialog::OnClickButtonOK(void)
{
    Print("OK button clicked - Processing and closing dialog");
    // Process form data
    // Close dialog
    Destroy();
}

void CMyDialog::OnClickButtonCancel(void)
{
    Print("Cancel button clicked - Closing without saving");
    Destroy();
}

void CMyDialog::OnClickButtonApply(void)
{
    Print("Apply button clicked - Processing without closing");
    // Process form data but keep dialog open
}
```

### Toggle Button Implementation

```mql5
class CToggleButton : public CButton
{
private:
    bool m_toggle_state;
    string m_text_on;
    string m_text_off;
    color m_color_on;
    color m_color_off;

public:
    CToggleButton();
    void SetToggleTexts(const string text_on, const string text_off);
    void SetToggleColors(const color color_on, const color color_off);
    virtual bool OnClick(void);
    bool ToggleState(void) const { return m_toggle_state; }
    void SetToggleState(const bool state);
};

CToggleButton::CToggleButton(void) : m_toggle_state(false),
                                    m_text_on("ON"),
                                    m_text_off("OFF"),
                                    m_color_on(clrGreen),
                                    m_color_off(clrRed)
{
}

void CToggleButton::SetToggleTexts(const string text_on, const string text_off)
{
    m_text_on = text_on;
    m_text_off = text_off;
    SetToggleState(m_toggle_state); // Refresh display
}

void CToggleButton::SetToggleColors(const color color_on, const color color_off)
{
    m_color_on = color_on;
    m_color_off = color_off;
    SetToggleState(m_toggle_state); // Refresh display
}

bool CToggleButton::OnClick(void)
{
    m_toggle_state = !m_toggle_state;
    SetToggleState(m_toggle_state);
    return true;
}

void CToggleButton::SetToggleState(const bool state)
{
    m_toggle_state = state;

    if(m_toggle_state)
    {
        Text(m_text_on);
        ColorBackground(m_color_on);
        Pressed(true);
    }
    else
    {
        Text(m_text_off);
        ColorBackground(m_color_off);
        Pressed(false);
    }
}
```

### Button Toolbar

```mql5
class CButtonToolbar : public CWndContainer
{
private:
    CButton m_buttons[10];
    int     m_button_count;

public:
    bool CreateToolbar(const long chart_id, const string name, const int subwin,
                      const int x, const int y);
    bool AddButton(const string text, const color bg_color = clrLightGray);
    bool OnButtonClick(const int button_index);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);
};

bool CButtonToolbar::CreateToolbar(const long chart_id, const string name, const int subwin,
                                  const int x, const int y)
{
    int toolbar_width = 400;
    int toolbar_height = 40;

    if(!Create(chart_id, name, subwin, x, y, x + toolbar_width, y + toolbar_height))
        return false;

    m_button_count = 0;
    return true;
}

bool CButtonToolbar::AddButton(const string text, const color bg_color = clrLightGray)
{
    if(m_button_count >= 10) return false;

    int button_width = 35;
    int button_height = 25;
    int button_spacing = 5;
    int x_pos = 5 + (m_button_count * (button_width + button_spacing));

    string button_name = Name() + "Btn" + IntegerToString(m_button_count);

    if(!m_buttons[m_button_count].Create(Chart(), button_name, Subwin(),
                                        x_pos, 5, x_pos + button_width, 5 + button_height))
        return false;

    m_buttons[m_button_count].Text(text);
    m_buttons[m_button_count].ColorBackground(bg_color);
    m_buttons[m_button_count].FontSize(9);

    if(!Add(m_buttons[m_button_count]))
        return false;

    m_button_count++;
    return true;
}

bool CButtonToolbar::OnEvent(const int id, const long &lparam,
                            const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        for(int i = 0; i < m_button_count; i++)
        {
            if(sparam == m_buttons[i].Name())
            {
                return OnButtonClick(i);
            }
        }
    }

    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}

bool CButtonToolbar::OnButtonClick(const int button_index)
{
    string button_text = m_buttons[button_index].Text();
    Print("Toolbar button clicked: ", button_text, " (index: ", button_index, ")");

    // Highlight clicked button
    for(int i = 0; i < m_button_count; i++)
    {
        if(i == button_index)
            m_buttons[i].ColorBackground(clrYellow);
        else
            m_buttons[i].ColorBackground(clrLightGray);
    }

    return true;
}
```

## Button State Patterns

### Disabled State Handling
```mql5
void SetButtonEnabled(CButton &button, bool enabled)
{
    if(enabled)
    {
        button.Enable();
        button.Color(clrBlack);
        button.ColorBackground(clrLightGray);
    }
    else
    {
        button.Disable();
        button.Color(clrGray);
        button.ColorBackground(clrDarkGray);
    }
}
```

### Press State Management
```mql5
void SetButtonPressed(CButton &button, bool pressed)
{
    button.Pressed(pressed);
    if(pressed)
    {
        button.ColorBackground(clrDarkGray);
        button.ColorBorder(clrRed);
    }
    else
    {
        button.ColorBackground(clrLightGray);
        button.ColorBorder(clrGray);
    }
}
```

## Best Practices

1. **Clear Button Text**
   - Use concise, action-oriented labels
   - Consider button size when setting text
   - Use consistent terminology across the application

2. **Visual Feedback**
   - Provide clear pressed/unpressed states
   - Use color coding for different button types
   - Ensure sufficient contrast for readability

3. **Event Handling**
   - Always handle click events appropriately
   - Provide user feedback for actions
   - Validate conditions before performing actions

4. **Button Sizing**
   - Make buttons large enough for easy clicking
   - Maintain consistent sizing within button groups
   - Consider touch interface requirements

5. **State Management**
   - Use Locking() for toggle-style buttons
   - Disable buttons when actions are unavailable
   - Provide visual cues for button states

## Common Button Types

- **Action Buttons** - Execute specific commands (OK, Cancel, Apply)
- **Toggle Buttons** - Switch between states (ON/OFF, Play/Pause)
- **Navigation Buttons** - Move between views or pages
- **Tool Buttons** - Activate tools or modes
- **Command Buttons** - Trigger trading operations

## See Also

- [CWndObj](../foundation/CWndObj.md) - Base class for simple controls
- [CLabel](./CLabel.md) - Text display control
- [CEdit](./CEdit.md) - Text input control
- [Event Handling Tutorial](../tutorials/event-handling.md)
- [Button Styling Guide](../tutorials/styling-controls.md)