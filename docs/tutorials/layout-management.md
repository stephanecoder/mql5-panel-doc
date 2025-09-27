# Layout Management in MQL5 Panels

Effective layout management is crucial for creating professional-looking and user-friendly MQL5 panels. This tutorial covers techniques for positioning, sizing, and organizing controls within your panels.

## Core Layout Principles

### 1. Container-Based Layout
MQL5 panels use container-based layouts where controls are positioned within parent containers:

```mql5
// Main dialog container
CAppDialog main_panel;

// Child containers for organization
CWndContainer header_section;
CWndContainer content_section;
CWndContainer button_section;
```

### 2. Coordinate System
- **Origin (0,0)** is at the top-left corner
- **X increases** moving right
- **Y increases** moving down
- **Coordinates are in pixels**

### 3. Client Area vs Full Area
Always use the client area for positioning child controls:

```mql5
// Get available space inside the dialog
int available_width = ClientAreaWidth();
int available_height = ClientAreaHeight();

// Position controls within client area
control.Create(chart_id, name, subwin, x, y, x + width, y + height);
```

## Basic Layout Patterns

### 1. Vertical Stack Layout

```mql5
class CVerticalStackPanel : public CAppDialog
{
private:
    CLabel m_labels[5];
    CEdit  m_edits[5];

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

private:
    bool CreateVerticalLayout(void);
};

bool CVerticalStackPanel::CreateVerticalLayout(void)
{
    int margin = 10;
    int row_height = 30;
    int label_width = 80;
    int edit_width = 120;
    int y_pos = margin;

    string labels[] = {"Name:", "Email:", "Phone:", "Address:", "Notes:"};

    for(int i = 0; i < 5; i++)
    {
        // Create label
        string label_name = m_name + "Label" + IntegerToString(i);
        if(!m_labels[i].Create(m_chart_id, label_name, m_subwin,
                              margin, y_pos, margin + label_width, y_pos + 20))
            return false;

        m_labels[i].Text(labels[i]);
        Add(m_labels[i]);

        // Create edit field
        string edit_name = m_name + "Edit" + IntegerToString(i);
        if(!m_edits[i].Create(m_chart_id, edit_name, m_subwin,
                             margin + label_width + 10, y_pos,
                             margin + label_width + 10 + edit_width, y_pos + 25))
            return false;

        Add(m_edits[i]);

        // Move to next row
        y_pos += row_height;
    }

    return true;
}
```

### 2. Horizontal Layout

```mql5
class CHorizontalButtonPanel : public CWndContainer
{
private:
    CButton m_buttons[6];
    string  m_button_texts[6];

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

private:
    bool CreateHorizontalLayout(void);
    void CalculateButtonSizes(int &button_width, int &spacing);
};

bool CHorizontalButtonPanel::CreateHorizontalLayout(void)
{
    // Initialize button texts
    m_button_texts[0] = "New";
    m_button_texts[1] = "Open";
    m_button_texts[2] = "Save";
    m_button_texts[3] = "Close";
    m_button_texts[4] = "Help";
    m_button_texts[5] = "Exit";

    int button_width, spacing;
    CalculateButtonSizes(button_width, spacing);

    int margin = 5;
    int x_pos = margin;
    int y_pos = margin;
    int button_height = 30;

    for(int i = 0; i < 6; i++)
    {
        string button_name = m_name + "Button" + IntegerToString(i);
        if(!m_buttons[i].Create(Chart(), button_name, Subwin(),
                               x_pos, y_pos, x_pos + button_width, y_pos + button_height))
            return false;

        m_buttons[i].Text(m_button_texts[i]);
        Add(m_buttons[i]);

        x_pos += button_width + spacing;
    }

    return true;
}

void CHorizontalButtonPanel::CalculateButtonSizes(int &button_width, int &spacing)
{
    int margin = 5;
    int available_width = Width() - (2 * margin);
    int button_count = 6;

    // Calculate optimal button width and spacing
    spacing = 5;
    int total_spacing = spacing * (button_count - 1);
    button_width = (available_width - total_spacing) / button_count;

    // Ensure minimum button width
    if(button_width < 50)
    {
        button_width = 50;
        spacing = (available_width - (button_count * button_width)) / (button_count - 1);
        if(spacing < 2) spacing = 2;
    }
}
```

### 3. Grid Layout

```mql5
class CGridLayoutPanel : public CAppDialog
{
private:
    CButton m_grid_buttons[12]; // 3x4 grid
    int     m_rows;
    int     m_columns;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

private:
    bool CreateGridLayout(void);
    void CalculateGridPositions(const int row, const int col, int &x, int &y, int &w, int &h);
};

bool CGridLayoutPanel::CreateGridLayout(void)
{
    m_rows = 4;
    m_columns = 3;

    for(int row = 0; row < m_rows; row++)
    {
        for(int col = 0; col < m_columns; col++)
        {
            int index = row * m_columns + col;
            if(index >= 12) break;

            int x, y, w, h;
            CalculateGridPositions(row, col, x, y, w, h);

            string button_name = m_name + "GridBtn" + IntegerToString(index);
            if(!m_grid_buttons[index].Create(m_chart_id, button_name, m_subwin,
                                            x, y, x + w, y + h))
                return false;

            string button_text = "Btn " + IntegerToString(index + 1);
            m_grid_buttons[index].Text(button_text);
            Add(m_grid_buttons[index]);
        }
    }

    return true;
}

void CGridLayoutPanel::CalculateGridPositions(const int row, const int col,
                                             int &x, int &y, int &w, int &h)
{
    int margin = 10;
    int spacing = 5;

    int available_width = ClientAreaWidth() - (2 * margin);
    int available_height = ClientAreaHeight() - (2 * margin);

    w = (available_width - (spacing * (m_columns - 1))) / m_columns;
    h = (available_height - (spacing * (m_rows - 1))) / m_rows;

    x = margin + col * (w + spacing);
    y = margin + row * (h + spacing);
}
```

## Advanced Layout Techniques

### 1. Responsive Layout

```mql5
class CResponsivePanel : public CAppDialog
{
private:
    CPanel m_header_panel;
    CPanel m_sidebar_panel;
    CPanel m_content_panel;
    CPanel m_footer_panel;

    bool m_show_sidebar;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnResize(void);

    void ToggleSidebar(void);

private:
    bool CreateResponsiveLayout(void);
    void UpdateLayout(void);
};

bool CResponsivePanel::CreateResponsiveLayout(void)
{
    m_show_sidebar = true;

    // Header panel
    if(!m_header_panel.Create(m_chart_id, m_name+"Header", m_subwin, 0, 0, 100, 40))
        return false;
    m_header_panel.ColorBackground(clrLightBlue);
    m_header_panel.Text("Header");
    Add(m_header_panel);

    // Sidebar panel
    if(!m_sidebar_panel.Create(m_chart_id, m_name+"Sidebar", m_subwin, 0, 40, 80, 100))
        return false;
    m_sidebar_panel.ColorBackground(clrLightGray);
    m_sidebar_panel.Text("Sidebar");
    Add(m_sidebar_panel);

    // Content panel
    if(!m_content_panel.Create(m_chart_id, m_name+"Content", m_subwin, 80, 40, 100, 100))
        return false;
    m_content_panel.ColorBackground(clrWhite);
    m_content_panel.Text("Content");
    Add(m_content_panel);

    // Footer panel
    if(!m_footer_panel.Create(m_chart_id, m_name+"Footer", m_subwin, 0, 100, 100, 120))
        return false;
    m_footer_panel.ColorBackground(clrLightBlue);
    m_footer_panel.Text("Footer");
    Add(m_footer_panel);

    UpdateLayout();
    return true;
}

void CResponsivePanel::UpdateLayout(void)
{
    int header_height = 40;
    int footer_height = 25;
    int sidebar_width = m_show_sidebar ? 80 : 0;

    int total_width = ClientAreaWidth();
    int total_height = ClientAreaHeight();

    // Header spans full width
    m_header_panel.Move(0, 0);
    m_header_panel.Resize(total_width, header_height);

    // Sidebar
    if(m_show_sidebar)
    {
        m_sidebar_panel.Move(0, header_height);
        m_sidebar_panel.Resize(sidebar_width, total_height - header_height - footer_height);
        m_sidebar_panel.Show();
    }
    else
    {
        m_sidebar_panel.Hide();
    }

    // Content area
    m_content_panel.Move(sidebar_width, header_height);
    m_content_panel.Resize(total_width - sidebar_width, total_height - header_height - footer_height);

    // Footer spans remaining width
    m_footer_panel.Move(0, total_height - footer_height);
    m_footer_panel.Resize(total_width, footer_height);
}

bool CResponsivePanel::OnResize(void)
{
    UpdateLayout();
    return CAppDialog::OnResize();
}

void CResponsivePanel::ToggleSidebar(void)
{
    m_show_sidebar = !m_show_sidebar;
    UpdateLayout();
}
```

### 2. Tabbed Layout

```mql5
class CTabbedLayoutPanel : public CAppDialog
{
private:
    CButton       m_tab_buttons[4];
    CWndContainer m_tab_panels[4];
    int           m_active_tab;
    string        m_tab_names[4];

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void SwitchToTab(const int tab_index);

private:
    bool CreateTabbedLayout(void);
    void UpdateTabAppearance(void);
};

bool CTabbedLayoutPanel::CreateTabbedLayout(void)
{
    m_active_tab = 0;
    m_tab_names[0] = "General";
    m_tab_names[1] = "Advanced";
    m_tab_names[2] = "Display";
    m_tab_names[3] = "About";

    int tab_height = 30;
    int tab_width = ClientAreaWidth() / 4;

    // Create tab buttons
    for(int i = 0; i < 4; i++)
    {
        string tab_name = m_name + "Tab" + IntegerToString(i);
        int x_pos = i * tab_width;

        if(!m_tab_buttons[i].Create(m_chart_id, tab_name, m_subwin,
                                   x_pos, 0, x_pos + tab_width, tab_height))
            return false;

        m_tab_buttons[i].Text(m_tab_names[i]);
        Add(m_tab_buttons[i]);
    }

    // Create tab content panels
    for(int i = 0; i < 4; i++)
    {
        string panel_name = m_name + "Panel" + IntegerToString(i);
        if(!m_tab_panels[i].Create(m_chart_id, panel_name, m_subwin,
                                  0, tab_height, ClientAreaWidth(), ClientAreaHeight()))
            return false;

        Add(m_tab_panels[i]);

        // Initially hide all panels except first
        if(i != m_active_tab)
            m_tab_panels[i].Hide();
    }

    UpdateTabAppearance();
    return true;
}

bool CTabbedLayoutPanel::OnEvent(const int id, const long &lparam,
                                const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        for(int i = 0; i < 4; i++)
        {
            if(sparam == m_tab_buttons[i].Name())
            {
                SwitchToTab(i);
                return true;
            }
        }
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CTabbedLayoutPanel::SwitchToTab(const int tab_index)
{
    if(tab_index < 0 || tab_index >= 4) return;

    // Hide current tab
    m_tab_panels[m_active_tab].Hide();

    // Show new tab
    m_active_tab = tab_index;
    m_tab_panels[m_active_tab].Show();

    UpdateTabAppearance();
}

void CTabbedLayoutPanel::UpdateTabAppearance(void)
{
    for(int i = 0; i < 4; i++)
    {
        if(i == m_active_tab)
        {
            m_tab_buttons[i].ColorBackground(clrWhite);
            m_tab_buttons[i].Color(clrBlack);
        }
        else
        {
            m_tab_buttons[i].ColorBackground(clrLightGray);
            m_tab_buttons[i].Color(clrGray);
        }
    }
}
```

## Layout Helper Classes

### 1. Automatic Layout Manager

```mql5
class CAutoLayoutManager
{
private:
    CWndContainer* m_container;
    CWnd*          m_controls[50];
    int            m_control_count;
    int            m_margin;
    int            m_spacing;

public:
    CAutoLayoutManager(CWndContainer* container);
    void SetMargin(const int margin) { m_margin = margin; }
    void SetSpacing(const int spacing) { m_spacing = spacing; }

    void AddControl(CWnd* control);
    void ArrangeVertically(void);
    void ArrangeHorizontally(void);
    void ArrangeGrid(const int columns);
    void ArrangeCentered(void);
};

CAutoLayoutManager::CAutoLayoutManager(CWndContainer* container) :
    m_container(container), m_control_count(0), m_margin(10), m_spacing(5)
{
}

void CAutoLayoutManager::AddControl(CWnd* control)
{
    if(m_control_count < 50)
    {
        m_controls[m_control_count] = control;
        m_control_count++;
    }
}

void CAutoLayoutManager::ArrangeVertically(void)
{
    if(!m_container || m_control_count == 0) return;

    int y_pos = m_margin;

    for(int i = 0; i < m_control_count; i++)
    {
        CWnd* control = m_controls[i];
        int control_height = control.Height();

        control.Move(m_margin, y_pos);
        y_pos += control_height + m_spacing;
    }
}

void CAutoLayoutManager::ArrangeHorizontally(void)
{
    if(!m_container || m_control_count == 0) return;

    int x_pos = m_margin;

    for(int i = 0; i < m_control_count; i++)
    {
        CWnd* control = m_controls[i];
        int control_width = control.Width();

        control.Move(x_pos, m_margin);
        x_pos += control_width + m_spacing;
    }
}

void CAutoLayoutManager::ArrangeGrid(const int columns)
{
    if(!m_container || m_control_count == 0 || columns <= 0) return;

    int available_width = m_container.Width() - (2 * m_margin);
    int cell_width = available_width / columns;

    for(int i = 0; i < m_control_count; i++)
    {
        int row = i / columns;
        int col = i % columns;

        CWnd* control = m_controls[i];
        int control_width = control.Width();
        int control_height = control.Height();

        // Center control within cell
        int x = m_margin + (col * cell_width) + (cell_width - control_width) / 2;
        int y = m_margin + (row * (control_height + m_spacing));

        control.Move(x, y);
    }
}

void CAutoLayoutManager::ArrangeCentered(void)
{
    if(!m_container || m_control_count == 0) return;

    // Calculate total height of all controls
    int total_height = 0;
    for(int i = 0; i < m_control_count; i++)
    {
        total_height += m_controls[i].Height();
        if(i < m_control_count - 1) total_height += m_spacing;
    }

    // Center vertically
    int start_y = (m_container.Height() - total_height) / 2;
    int y_pos = start_y;

    for(int i = 0; i < m_control_count; i++)
    {
        CWnd* control = m_controls[i];
        int control_width = control.Width();
        int control_height = control.Height();

        // Center horizontally
        int x = (m_container.Width() - control_width) / 2;

        control.Move(x, y_pos);
        y_pos += control_height + m_spacing;
    }
}
```

### 2. Layout Utilities

```mql5
class CLayoutUtils
{
public:
    // Alignment functions
    static void AlignLeft(CWnd &control, CWndContainer &container, const int margin = 10);
    static void AlignRight(CWnd &control, CWndContainer &container, const int margin = 10);
    static void AlignCenter(CWnd &control, CWndContainer &container);
    static void AlignTop(CWnd &control, CWndContainer &container, const int margin = 10);
    static void AlignBottom(CWnd &control, CWndContainer &container, const int margin = 10);
    static void AlignMiddle(CWnd &control, CWndContainer &container);

    // Sizing functions
    static void FillWidth(CWnd &control, CWndContainer &container, const int margin = 10);
    static void FillHeight(CWnd &control, CWndContainer &container, const int margin = 10);
    static void SetAspectRatio(CWnd &control, const double ratio);

    // Spacing functions
    static void DistributeHorizontally(CWnd controls[], const int count,
                                      CWndContainer &container, const int margin = 10);
    static void DistributeVertically(CWnd controls[], const int count,
                                    CWndContainer &container, const int margin = 10);
};

void CLayoutUtils::AlignCenter(CWnd &control, CWndContainer &container)
{
    int x = (container.Width() - control.Width()) / 2;
    int y = control.Top(); // Keep current Y position
    control.Move(x, y);
}

void CLayoutUtils::AlignMiddle(CWnd &control, CWndContainer &container)
{
    int x = control.Left(); // Keep current X position
    int y = (container.Height() - control.Height()) / 2;
    control.Move(x, y);
}

void CLayoutUtils::FillWidth(CWnd &control, CWndContainer &container, const int margin = 10)
{
    int new_width = container.Width() - (2 * margin);
    control.Move(margin, control.Top());
    control.Resize(new_width, control.Height());
}

void CLayoutUtils::DistributeHorizontally(CWnd controls[], const int count,
                                         CWndContainer &container, const int margin = 10)
{
    if(count <= 0) return;

    int available_width = container.Width() - (2 * margin);
    int total_control_width = 0;

    // Calculate total width of controls
    for(int i = 0; i < count; i++)
    {
        total_control_width += controls[i].Width();
    }

    // Calculate spacing between controls
    int total_spacing = available_width - total_control_width;
    int spacing = (count > 1) ? total_spacing / (count - 1) : 0;

    // Position controls
    int x_pos = margin;
    for(int i = 0; i < count; i++)
    {
        controls[i].Move(x_pos, controls[i].Top());
        x_pos += controls[i].Width() + spacing;
    }
}
```

## Best Practices

### 1. Consistent Spacing
```mql5
// Define standard measurements
#define MARGIN_SMALL    5
#define MARGIN_MEDIUM   10
#define MARGIN_LARGE    20
#define CONTROL_HEIGHT  25
#define BUTTON_HEIGHT   30
#define ROW_SPACING     5
```

### 2. Responsive Design
```mql5
// Always calculate positions based on container size
int button_width = (ClientAreaWidth() - 3 * MARGIN_MEDIUM) / 2;
int available_height = ClientAreaHeight() - 2 * MARGIN_MEDIUM;
```

### 3. Layout Validation
```mql5
bool ValidateLayout(CWndContainer &container)
{
    // Check if any controls extend beyond container bounds
    for(int i = 0; i < container.ControlsTotal(); i++)
    {
        CWnd* control = container.Control(i);
        if(control.Right() > container.Width() ||
           control.Bottom() > container.Height())
        {
            Print("Warning: Control extends beyond container bounds");
            return false;
        }
    }
    return true;
}
```

### 4. Dynamic Resizing
```mql5
virtual bool OnResize(void)
{
    // Recalculate layout when dialog is resized
    UpdateLayout();
    return CAppDialog::OnResize();
}
```

## Common Layout Patterns

1. **Master-Detail** - List on left, details on right
2. **Wizard** - Step-by-step navigation
3. **Dashboard** - Grid of information panels
4. **Toolbar** - Horizontal row of buttons
5. **Property Sheet** - Tabbed configuration interface
6. **Sidebar** - Navigation panel with collapsible content

## Troubleshooting Layout Issues

### Controls Not Visible
- Check coordinates are positive and within container bounds
- Verify parent container is properly sized
- Ensure `Show()` is called on containers

### Overlapping Controls
- Review positioning calculations
- Add proper spacing between controls
- Use layout debugging to visualize bounds

### Inconsistent Sizing
- Use relative sizing based on container dimensions
- Define standard control sizes
- Test with different dialog sizes

Layout management is fundamental to creating professional MQL5 panels. Master these techniques to build intuitive and visually appealing trading interfaces.

## See Also

- [Getting Started](./getting-started.md) - Basic panel creation
- [Event Handling](./event-handling.md) - User interaction handling
- [CWndContainer Reference](../foundation/CWndContainer.md) - Container functionality