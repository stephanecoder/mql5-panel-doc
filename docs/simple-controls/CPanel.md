# CPanel - Rectangle Panel Control

The `CPanel` class creates rectangular panel controls based on the "Rectangle label" chart object, designed to group related controls with visual boundaries.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndObj
        └── CPanel
```

## Overview

- **Header File**: `<Controls\Panel.mqh>`
- **Base Object**: Rectangle label chart object
- **Purpose**: Visual container and grouping element
- **Key Features**: Background colors, borders, text labels, visual organization

## Core Methods

### Creation

#### Create()
Creates the panel control with specified parameters.

```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

### Appearance Properties

#### BorderType()
Gets or sets the panel's border properties.

```mql5
ENUM_BORDER_TYPE BorderType() const;                          // Get border type
virtual bool BorderType(const ENUM_BORDER_TYPE border_type); // Set border type
```

**Border Type Constants:**
- `BORDER_FLAT` - Flat border
- `BORDER_RAISED` - Raised 3D border
- `BORDER_SUNKEN` - Sunken 3D border

#### Background and Border Colors
```mql5
color ColorBackground() const;                    // Get background color
virtual bool ColorBackground(const color clr);   // Set background color

color ColorBorder() const;                        // Get border color
virtual bool ColorBorder(const color clr);       // Set border color
```

#### Text and Alignment
```mql5
string Text() const;                    // Get panel text
virtual bool Text(const string text);  // Set panel text

uint Alignment() const;                 // Get text alignment
virtual bool Alignment(const uint alignment); // Set text alignment
```

## Event Handling

### Lifecycle Events

#### OnCreate()
Called when the panel is created.

```mql5
virtual bool OnCreate(void);
```

#### OnShow/OnHide()
Called when panel visibility changes.

```mql5
virtual bool OnShow(void);  // Panel becomes visible
virtual bool OnHide(void);  // Panel becomes hidden
```

### Layout Events

#### OnMove()
Called when the panel is moved.

```mql5
virtual bool OnMove(void);
```

#### OnResize()
Called when the panel is resized.

```mql5
virtual bool OnResize(void);
```

#### OnChange()
Called when panel properties change.

```mql5
virtual bool OnChange(void);
```

## Usage Examples

### Basic Panel Layout

```mql5
#include <Controls\Panel.mqh>
#include <Controls\Button.mqh>
#include <Controls\Label.mqh>

class CControlGroup : public CWndContainer
{
private:
    CPanel  m_background_panel;
    CPanel  m_header_panel;
    CPanel  m_content_panel;
    CLabel  m_title_label;
    CButton m_buttons[5];

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

private:
    bool CreatePanels(void);
    bool CreateHeader(void);
    bool CreateContent(void);
};

bool CControlGroup::Create(const long chart_id, const string name, const int subwin,
                          const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreatePanels() && CreateHeader() && CreateContent();
}

bool CControlGroup::CreatePanels(void)
{
    // Main background panel
    if(!m_background_panel.Create(m_chart_id, m_name+"Background", m_subwin,
                                 0, 0, Width(), Height()))
        return false;

    m_background_panel.ColorBackground(CONTROLS_DIALOG_COLOR_BG);
    m_background_panel.ColorBorder(CONTROLS_DIALOG_COLOR_BORDER_DARK);
    m_background_panel.BorderType(BORDER_SUNKEN);
    Add(m_background_panel);

    // Header panel
    if(!m_header_panel.Create(m_chart_id, m_name+"Header", m_subwin,
                             5, 5, Width()-5, 35))
        return false;

    m_header_panel.ColorBackground(CONTROLS_DIALOG_COLOR_CLIENT_BG);
    m_header_panel.ColorBorder(CONTROLS_DIALOG_COLOR_BORDER_LIGHT);
    m_header_panel.BorderType(BORDER_RAISED);
    m_header_panel.Text("Control Group");
    m_header_panel.Alignment(ALIGN_CENTER);
    Add(m_header_panel);

    // Content panel
    if(!m_content_panel.Create(m_chart_id, m_name+"Content", m_subwin,
                              5, 40, Width()-5, Height()-5))
        return false;

    m_content_panel.ColorBackground(CONTROLS_DIALOG_COLOR_CLIENT_BG);
    m_content_panel.ColorBorder(CONTROLS_DIALOG_COLOR_BORDER_LIGHT);
    m_content_panel.BorderType(BORDER_FLAT);
    Add(m_content_panel);

    return true;
}
```

### Status Panel with Dynamic Updates

```mql5
class CStatusPanel : public CPanel
{
private:
    enum ENUM_STATUS_TYPE
    {
        STATUS_NORMAL,
        STATUS_WARNING,
        STATUS_ERROR,
        STATUS_SUCCESS
    };

    ENUM_STATUS_TYPE m_current_status;
    datetime m_last_update;

public:
    CStatusPanel();
    virtual bool OnCreate(void);
    void SetStatus(const ENUM_STATUS_TYPE status, const string message);
    void UpdateTimestamp(void);

private:
    void ApplyStatusColors(const ENUM_STATUS_TYPE status);
};

CStatusPanel::CStatusPanel(void) : m_current_status(STATUS_NORMAL),
                                   m_last_update(0)
{
}

bool CStatusPanel::OnCreate(void)
{
    // Set default appearance
    BorderType(BORDER_SUNKEN);
    Alignment(ALIGN_CENTER);
    SetStatus(STATUS_NORMAL, "Ready");

    return CPanel::OnCreate();
}

void CStatusPanel::SetStatus(const ENUM_STATUS_TYPE status, const string message)
{
    m_current_status = status;
    m_last_update = TimeCurrent();

    // Update text with timestamp
    string full_message = message + " (" + TimeToString(m_last_update, TIME_SECONDS) + ")";
    Text(full_message);

    // Apply status-specific colors
    ApplyStatusColors(status);
}

void CStatusPanel::ApplyStatusColors(const ENUM_STATUS_TYPE status)
{
    switch(status)
    {
        case STATUS_NORMAL:
            ColorBackground(clrLightGray);
            ColorBorder(clrGray);
            Color(clrBlack);
            break;

        case STATUS_WARNING:
            ColorBackground(clrYellow);
            ColorBorder(clrOrange);
            Color(clrBlack);
            break;

        case STATUS_ERROR:
            ColorBackground(clrRed);
            ColorBorder(clrDarkRed);
            Color(clrWhite);
            break;

        case STATUS_SUCCESS:
            ColorBackground(clrLimeGreen);
            ColorBorder(clrGreen);
            Color(clrBlack);
            break;
    }
}

void CStatusPanel::UpdateTimestamp(void)
{
    string current_text = Text();
    int paren_pos = StringFind(current_text, " (");

    if(paren_pos > 0)
    {
        string base_message = StringSubstr(current_text, 0, paren_pos);
        SetStatus(m_current_status, base_message);
    }
}
```

### Information Display Panels

```mql5
class CInfoPanelGroup : public CWndContainer
{
private:
    struct SInfoPanel
    {
        CPanel panel;
        CLabel label;
        CLabel value;
        string caption;
    };

    SInfoPanel m_info_panels[10];
    int        m_panel_count;

public:
    bool CreateInfoGroup(const long chart_id, const string name, const int subwin,
                        const int x, const int y, const int columns = 2);
    bool AddInfoPanel(const string caption, const string initial_value = "");
    void UpdateInfoPanel(const int index, const string value);
    void UpdateInfoPanel(const string caption, const string value);

private:
    void CalculatePanelPosition(const int index, const int columns,
                               int &x, int &y, int &width, int &height);
};

bool CInfoPanelGroup::CreateInfoGroup(const long chart_id, const string name, const int subwin,
                                     const int x, const int y, const int columns = 2)
{
    int group_width = columns * 120 + (columns - 1) * 5;
    int group_height = 200; // Initial height, will expand as needed

    if(!Create(chart_id, name, subwin, x, y, x + group_width, y + group_height))
        return false;

    m_panel_count = 0;
    return true;
}

bool CInfoPanelGroup::AddInfoPanel(const string caption, const string initial_value = "")
{
    if(m_panel_count >= 10) return false;

    int panel_x, panel_y, panel_width, panel_height;
    CalculatePanelPosition(m_panel_count, 2, panel_x, panel_y, panel_width, panel_height);

    SInfoPanel &info = m_info_panels[m_panel_count];
    info.caption = caption;

    // Create background panel
    string panel_name = Name() + "Panel" + IntegerToString(m_panel_count);
    if(!info.panel.Create(Chart(), panel_name, Subwin(),
                         panel_x, panel_y, panel_x + panel_width, panel_y + panel_height))
        return false;

    info.panel.ColorBackground(CONTROLS_DIALOG_COLOR_CLIENT_BG);
    info.panel.ColorBorder(CONTROLS_DIALOG_COLOR_BORDER_LIGHT);
    info.panel.BorderType(BORDER_SUNKEN);
    Add(info.panel);

    // Create caption label
    string label_name = Name() + "Label" + IntegerToString(m_panel_count);
    if(!info.label.Create(Chart(), label_name, Subwin(),
                         panel_x + 5, panel_y + 5, panel_x + panel_width - 5, panel_y + 20))
        return false;

    info.label.Text(caption);
    info.label.Font("Arial Bold");
    info.label.FontSize(9);
    info.label.Color(clrNavy);
    Add(info.label);

    // Create value label
    string value_name = Name() + "Value" + IntegerToString(m_panel_count);
    if(!info.value.Create(Chart(), value_name, Subwin(),
                         panel_x + 5, panel_y + 25, panel_x + panel_width - 5, panel_y + 45))
        return false;

    info.value.Text(initial_value);
    info.value.Font("Courier");
    info.value.FontSize(10);
    info.value.Color(clrBlack);
    Add(info.value);

    m_panel_count++;
    return true;
}

void CInfoPanelGroup::UpdateInfoPanel(const int index, const string value)
{
    if(index >= 0 && index < m_panel_count)
    {
        m_info_panels[index].value.Text(value);
    }
}

void CInfoPanelGroup::UpdateInfoPanel(const string caption, const string value)
{
    for(int i = 0; i < m_panel_count; i++)
    {
        if(m_info_panels[i].caption == caption)
        {
            UpdateInfoPanel(i, value);
            break;
        }
    }
}

void CInfoPanelGroup::CalculatePanelPosition(const int index, const int columns,
                                            int &x, int &y, int &width, int &height)
{
    width = 115;
    height = 50;
    int spacing = 5;

    int row = index / columns;
    int col = index % columns;

    x = col * (width + spacing);
    y = row * (height + spacing);
}
```

## Panel Styling Patterns

### Standard Dialog Panels
```mql5
void StyleAsDialogPanel(CPanel &panel)
{
    panel.ColorBackground(CONTROLS_DIALOG_COLOR_BG);
    panel.ColorBorder(CONTROLS_DIALOG_COLOR_BORDER_DARK);
    panel.BorderType(BORDER_SUNKEN);
}

void StyleAsHeaderPanel(CPanel &panel)
{
    panel.ColorBackground(CONTROLS_DIALOG_COLOR_CLIENT_BG);
    panel.ColorBorder(CONTROLS_DIALOG_COLOR_BORDER_LIGHT);
    panel.BorderType(BORDER_RAISED);
    panel.Alignment(ALIGN_CENTER);
}

void StyleAsContentPanel(CPanel &panel)
{
    panel.ColorBackground(clrWhite);
    panel.ColorBorder(CONTROLS_DIALOG_COLOR_BORDER_LIGHT);
    panel.BorderType(BORDER_FLAT);
}
```

### Status-Based Styling
```mql5
void SetPanelStatus(CPanel &panel, const string status)
{
    panel.Text(status);

    if(status == "Active")
    {
        panel.ColorBackground(clrLimeGreen);
        panel.ColorBorder(clrGreen);
    }
    else if(status == "Warning")
    {
        panel.ColorBackground(clrYellow);
        panel.ColorBorder(clrOrange);
    }
    else if(status == "Error")
    {
        panel.ColorBackground(clrRed);
        panel.ColorBorder(clrDarkRed);
    }
    else
    {
        panel.ColorBackground(clrLightGray);
        panel.ColorBorder(clrGray);
    }
}
```

## Best Practices

1. **Visual Hierarchy**
   - Use border types to create depth (raised for headers, sunken for content)
   - Maintain consistent color schemes across related panels
   - Group related controls within panels

2. **Layout Organization**
   - Use panels to create logical sections
   - Maintain consistent spacing between panels
   - Consider panel sizing for content requirements

3. **Color Usage**
   - Use subtle color differences for grouping
   - Apply status colors consistently
   - Ensure sufficient contrast for text readability

4. **Text and Alignment**
   - Center-align panel titles
   - Use clear, descriptive panel labels
   - Consider text length when sizing panels

5. **Event Handling**
   - Use panel events for layout updates
   - Handle resize events for dynamic layouts
   - Coordinate panel changes with child controls

## Common Use Cases

- **Section Dividers** - Visual separation of control groups
- **Status Displays** - Current state indication with color coding
- **Content Backgrounds** - Visual containers for related controls
- **Header Panels** - Title bars and section headers
- **Information Displays** - Data presentation with structured layout
- **Dialog Backgrounds** - Main container panels for dialogs

## See Also

- [CWndObj](../foundation/CWndObj.md) - Base class for simple controls
- [CWndContainer](../foundation/CWndContainer.md) - Container base class
- [CLabel](./CLabel.md) - Text display control
- [Layout Management Tutorial](../tutorials/layout-management.md)
- [Visual Design Patterns](../tutorials/visual-patterns.md)