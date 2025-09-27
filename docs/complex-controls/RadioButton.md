# Radio Button Controls - Exclusive Selection

The radio button controls (CRadioButton, CRadioGroup) provide mutually exclusive selection functionality for user interfaces in MQL5 applications.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndContainer
        ├── CRadioButton
        └── CRadioGroup
```

## Overview

- **Header File**: `<Controls\RadioButton.mqh>`
- **Purpose**: Mutually exclusive selection (one option from many)
- **Key Features**: Group management, state synchronization, exclusive selection
- **Components**: Radio button, text label, group coordination

## CRadioButton Control

### Core Methods

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Component Creation
```mql5
virtual bool CreateButton(void);    // Create button component
virtual bool CreateLabel(void);     // Create label component
```

#### Text Management
```mql5
string Text() const;                    // Get label text
virtual bool Text(const string text);  // Set label text
```

#### Color Management
```mql5
color Color() const;                    // Get text color
virtual bool Color(const color clr);   // Set text color
```

#### State Management
```mql5
bool State() const;                     // Get selected state
virtual bool State(const bool flag);   // Set selected state
```

### Event Handling

#### Click Events
```mql5
virtual bool OnClickButton(void);   // Button clicked
virtual bool OnClickLabel(void);    // Label clicked
```

## CRadioGroup Container

The CRadioGroup manages multiple CRadioButton controls to ensure mutual exclusivity.

## Usage Examples

### Basic Radio Button Group

```mql5
#include <Controls\RadioButton.mqh>
#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>
#include <Controls\Label.mqh>

class CRadioButtonDialog : public CAppDialog
{
private:
    // Timeframe selection group
    CLabel       m_label_timeframe;
    CRadioButton m_radio_tf[5];
    string       m_timeframe_names[5];
    ENUM_TIMEFRAMES m_timeframe_values[5];

    // Trading mode selection group
    CLabel       m_label_mode;
    CRadioButton m_radio_mode[3];
    string       m_mode_names[3];

    // Action buttons
    CButton      m_button_apply;
    CButton      m_button_get_selection;

    int          m_selected_timeframe;
    int          m_selected_mode;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    bool CreateControls(void);
    void InitializeOptions(void);
    void OnTimeframeSelection(const int index);
    void OnModeSelection(const int index);
    void OnApplySelection(void);
    void OnGetSelection(void);
    void UpdateRadioGroup(CRadioButton radio_buttons[], const int count, const int selected_index);
};

bool CRadioButtonDialog::Create(const long chart_id, const string name, const int subwin,
                               const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    InitializeOptions();
    return CreateControls();
}

void CRadioButtonDialog::InitializeOptions(void)
{
    // Timeframe options
    m_timeframe_names[0] = "M1 (1 Minute)";
    m_timeframe_names[1] = "M5 (5 Minutes)";
    m_timeframe_names[2] = "M15 (15 Minutes)";
    m_timeframe_names[3] = "H1 (1 Hour)";
    m_timeframe_names[4] = "D1 (Daily)";

    m_timeframe_values[0] = PERIOD_M1;
    m_timeframe_values[1] = PERIOD_M5;
    m_timeframe_values[2] = PERIOD_M15;
    m_timeframe_values[3] = PERIOD_H1;
    m_timeframe_values[4] = PERIOD_D1;

    // Trading mode options
    m_mode_names[0] = "Conservative";
    m_mode_names[1] = "Moderate";
    m_mode_names[2] = "Aggressive";

    // Default selections
    m_selected_timeframe = 3; // H1
    m_selected_mode = 1;      // Moderate
}

bool CRadioButtonDialog::CreateControls(void)
{
    int margin = 15;
    int row_height = 30;
    int y_pos = margin;

    // Timeframe selection section
    if(!m_label_timeframe.Create(m_chart_id, m_name+"LabelTF", m_subwin,
                                margin, y_pos, ClientAreaWidth()-margin, y_pos+20))
        return false;
    m_label_timeframe.Text("Select Timeframe:");
    m_label_timeframe.Font("Arial Bold");
    m_label_timeframe.Color(clrBlue);
    Add(m_label_timeframe);

    y_pos += 25;

    // Timeframe radio buttons
    for(int i = 0; i < 5; i++)
    {
        string radio_name = m_name + "RadioTF" + IntegerToString(i);
        if(!m_radio_tf[i].Create(m_chart_id, radio_name, m_subwin,
                                margin+20, y_pos, ClientAreaWidth()-margin, y_pos+25))
            return false;

        m_radio_tf[i].Text(m_timeframe_names[i]);
        m_radio_tf[i].State(i == m_selected_timeframe);
        Add(m_radio_tf[i]);

        y_pos += row_height;
    }

    y_pos += 15;

    // Trading mode selection section
    if(!m_label_mode.Create(m_chart_id, m_name+"LabelMode", m_subwin,
                           margin, y_pos, ClientAreaWidth()-margin, y_pos+20))
        return false;
    m_label_mode.Text("Select Trading Mode:");
    m_label_mode.Font("Arial Bold");
    m_label_mode.Color(clrBlue);
    Add(m_label_mode);

    y_pos += 25;

    // Mode radio buttons
    for(int i = 0; i < 3; i++)
    {
        string radio_name = m_name + "RadioMode" + IntegerToString(i);
        if(!m_radio_mode[i].Create(m_chart_id, radio_name, m_subwin,
                                  margin+20, y_pos, ClientAreaWidth()-margin, y_pos+25))
            return false;

        m_radio_mode[i].Text(m_mode_names[i]);
        m_radio_mode[i].State(i == m_selected_mode);
        Add(m_radio_mode[i]);

        y_pos += row_height;
    }

    y_pos += 20;

    // Action buttons
    int button_width = 100;
    int button_spacing = 20;
    int button_start = (ClientAreaWidth() - (2 * button_width + button_spacing)) / 2;

    if(!m_button_apply.Create(m_chart_id, m_name+"Apply", m_subwin,
                             button_start, y_pos, button_start+button_width, y_pos+30))
        return false;
    m_button_apply.Text("Apply");
    m_button_apply.ColorBackground(clrGreen);
    Add(m_button_apply);

    if(!m_button_get_selection.Create(m_chart_id, m_name+"GetSelection", m_subwin,
                                     button_start+button_width+button_spacing, y_pos,
                                     button_start+2*button_width+button_spacing, y_pos+30))
        return false;
    m_button_get_selection.Text("Get Selection");
    m_button_get_selection.ColorBackground(clrBlue);
    Add(m_button_get_selection);

    return true;
}

bool CRadioButtonDialog::OnEvent(const int id, const long &lparam,
                                const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        // Handle timeframe radio buttons
        for(int i = 0; i < 5; i++)
        {
            if(sparam == m_radio_tf[i].Name())
            {
                OnTimeframeSelection(i);
                return true;
            }
        }

        // Handle mode radio buttons
        for(int i = 0; i < 3; i++)
        {
            if(sparam == m_radio_mode[i].Name())
            {
                OnModeSelection(i);
                return true;
            }
        }

        // Handle buttons
        if(sparam == m_button_apply.Name())
        {
            OnApplySelection();
            return true;
        }
        if(sparam == m_button_get_selection.Name())
        {
            OnGetSelection();
            return true;
        }
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CRadioButtonDialog::OnTimeframeSelection(const int index)
{
    m_selected_timeframe = index;
    UpdateRadioGroup(m_radio_tf, 5, index);
    Print("Timeframe selected: ", m_timeframe_names[index]);
}

void CRadioButtonDialog::OnModeSelection(const int index)
{
    m_selected_mode = index;
    UpdateRadioGroup(m_radio_mode, 3, index);
    Print("Mode selected: ", m_mode_names[index]);
}

void CRadioButtonDialog::UpdateRadioGroup(CRadioButton radio_buttons[], const int count, const int selected_index)
{
    for(int i = 0; i < count; i++)
    {
        radio_buttons[i].State(i == selected_index);

        // Visual feedback
        if(i == selected_index)
            radio_buttons[i].Color(clrGreen);
        else
            radio_buttons[i].Color(clrBlack);
    }
}

void CRadioButtonDialog::OnApplySelection(void)
{
    Print("Applying selection:");
    Print("  Timeframe: ", m_timeframe_names[m_selected_timeframe], " (", m_timeframe_values[m_selected_timeframe], ")");
    Print("  Mode: ", m_mode_names[m_selected_mode]);

    Alert("Settings applied: " + m_timeframe_names[m_selected_timeframe] + " / " + m_mode_names[m_selected_mode]);
}

void CRadioButtonDialog::OnGetSelection(void)
{
    string selection = "Current Selection:\n";
    selection += "Timeframe: " + m_timeframe_names[m_selected_timeframe] + "\n";
    selection += "Mode: " + m_mode_names[m_selected_mode];

    Alert(selection);
}
```

### Radio Button Group Manager

```mql5
class CRadioButtonGroup : public CWndContainer
{
private:
    CRadioButton m_radio_buttons[20];
    string       m_button_texts[20];
    long         m_button_values[20];
    int          m_button_count;
    int          m_selected_index;

public:
    CRadioButtonGroup();
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    bool AddRadioButton(const string text, const long value = 0);
    void SetSelection(const int index);
    int  GetSelectedIndex(void) const { return m_selected_index; }
    long GetSelectedValue(void) const;
    string GetSelectedText(void) const;
    void ClearSelection(void);

private:
    void UpdateRadioStates(void);
    void ArrangeButtons(void);
};

CRadioButtonGroup::CRadioButtonGroup(void) : m_button_count(0), m_selected_index(-1)
{
}

bool CRadioButtonGroup::Create(const long chart_id, const string name, const int subwin,
                              const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return true;
}

bool CRadioButtonGroup::AddRadioButton(const string text, const long value = 0)
{
    if(m_button_count >= 20) return false;

    string button_name = Name() + "Radio" + IntegerToString(m_button_count);

    // Position will be set by ArrangeButtons()
    if(!m_radio_buttons[m_button_count].Create(Chart(), button_name, Subwin(),
                                              0, 0, 100, 25))
        return false;

    m_radio_buttons[m_button_count].Text(text);
    m_button_texts[m_button_count] = text;
    m_button_values[m_button_count] = value;
    Add(m_radio_buttons[m_button_count]);

    m_button_count++;
    ArrangeButtons();

    // Select first button by default
    if(m_button_count == 1)
        SetSelection(0);

    return true;
}

void CRadioButtonGroup::SetSelection(const int index)
{
    if(index >= 0 && index < m_button_count)
    {
        m_selected_index = index;
        UpdateRadioStates();
    }
}

long CRadioButtonGroup::GetSelectedValue(void) const
{
    if(m_selected_index >= 0 && m_selected_index < m_button_count)
        return m_button_values[m_selected_index];
    return 0;
}

string CRadioButtonGroup::GetSelectedText(void) const
{
    if(m_selected_index >= 0 && m_selected_index < m_button_count)
        return m_button_texts[m_selected_index];
    return "";
}

void CRadioButtonGroup::ClearSelection(void)
{
    m_selected_index = -1;
    UpdateRadioStates();
}

void CRadioButtonGroup::UpdateRadioStates(void)
{
    for(int i = 0; i < m_button_count; i++)
    {
        bool selected = (i == m_selected_index);
        m_radio_buttons[i].State(selected);

        // Visual feedback
        if(selected)
            m_radio_buttons[i].Color(clrBlue);
        else
            m_radio_buttons[i].Color(clrBlack);
    }
}

void CRadioButtonGroup::ArrangeButtons(void)
{
    int margin = 5;
    int button_height = 25;
    int spacing = 5;

    for(int i = 0; i < m_button_count; i++)
    {
        int y_pos = margin + (i * (button_height + spacing));
        m_radio_buttons[i].Move(margin, y_pos);
        m_radio_buttons[i].Resize(Width() - 2 * margin, button_height);
    }

    // Resize container to fit all buttons
    int total_height = margin + m_button_count * (button_height + spacing);
    if(total_height > Height())
        Resize(Width(), total_height);
}

bool CRadioButtonGroup::OnEvent(const int id, const long &lparam,
                               const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        for(int i = 0; i < m_button_count; i++)
        {
            if(sparam == m_radio_buttons[i].Name())
            {
                SetSelection(i);
                Print("Radio button selected: ", m_button_texts[i], " (Value: ", m_button_values[i], ")");
                return true;
            }
        }
    }

    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}
```

### Tabbed Interface with Radio Buttons

```mql5
class CTabbedRadioInterface : public CWndContainer
{
private:
    // Tab radio buttons
    CRadioButton m_tab_buttons[4];
    string       m_tab_names[4];

    // Tab content panels
    CPanel       m_tab_panels[4];
    CWndContainer m_tab_contents[4];

    int          m_active_tab;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void ShowTab(const int tab_index);

private:
    bool CreateTabs(void);
    bool CreateTabContent(void);
    void SetupTabContent(const int tab_index);
};

bool CTabbedRadioInterface::Create(const long chart_id, const string name, const int subwin,
                                  const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    // Initialize tab names
    m_tab_names[0] = "General";
    m_tab_names[1] = "Trading";
    m_tab_names[2] = "Alerts";
    m_tab_names[3] = "Advanced";

    m_active_tab = 0;

    return CreateTabs() && CreateTabContent();
}

bool CTabbedRadioInterface::CreateTabs(void)
{
    int tab_width = Width() / 4;
    int tab_height = 30;

    for(int i = 0; i < 4; i++)
    {
        string tab_name = Name() + "Tab" + IntegerToString(i);
        int x_pos = i * tab_width;

        if(!m_tab_buttons[i].Create(Chart(), tab_name, Subwin(),
                                   x_pos, 0, x_pos + tab_width, tab_height))
            return false;

        m_tab_buttons[i].Text(m_tab_names[i]);
        m_tab_buttons[i].State(i == m_active_tab);
        Add(m_tab_buttons[i]);
    }

    return true;
}

bool CTabbedRadioInterface::CreateTabContent(void)
{
    int content_top = 35;
    int content_height = Height() - content_top;

    for(int i = 0; i < 4; i++)
    {
        // Create background panel for each tab
        string panel_name = Name() + "Panel" + IntegerToString(i);
        if(!m_tab_panels[i].Create(Chart(), panel_name, Subwin(),
                                  0, content_top, Width(), Height()))
            return false;

        m_tab_panels[i].ColorBackground(clrWhite);
        m_tab_panels[i].ColorBorder(clrGray);
        Add(m_tab_panels[i]);

        // Create content container for each tab
        string content_name = Name() + "Content" + IntegerToString(i);
        if(!m_tab_contents[i].Create(Chart(), content_name, Subwin(),
                                    5, content_top + 5, Width() - 5, Height() - 5))
            return false;

        Add(m_tab_contents[i]);
        SetupTabContent(i);

        // Initially hide all tabs except the first
        if(i != m_active_tab)
        {
            m_tab_panels[i].Hide();
            m_tab_contents[i].Hide();
        }
    }

    return true;
}

void CTabbedRadioInterface::SetupTabContent(const int tab_index)
{
    // Add controls specific to each tab
    switch(tab_index)
    {
        case 0: // General tab
        {
            CLabel general_label;
            string label_name = Name() + "GeneralLabel";
            if(general_label.Create(Chart(), label_name, Subwin(),
                                   10, 10, 200, 30))
            {
                general_label.Text("General Settings");
                general_label.Font("Arial Bold");
                m_tab_contents[tab_index].Add(general_label);
            }
            break;
        }

        case 1: // Trading tab
        {
            CLabel trading_label;
            string label_name = Name() + "TradingLabel";
            if(trading_label.Create(Chart(), label_name, Subwin(),
                                   10, 10, 200, 30))
            {
                trading_label.Text("Trading Configuration");
                trading_label.Font("Arial Bold");
                m_tab_contents[tab_index].Add(trading_label);
            }
            break;
        }

        case 2: // Alerts tab
        {
            CLabel alerts_label;
            string label_name = Name() + "AlertsLabel";
            if(alerts_label.Create(Chart(), label_name, Subwin(),
                                  10, 10, 200, 30))
            {
                alerts_label.Text("Alert Settings");
                alerts_label.Font("Arial Bold");
                m_tab_contents[tab_index].Add(alerts_label);
            }
            break;
        }

        case 3: // Advanced tab
        {
            CLabel advanced_label;
            string label_name = Name() + "AdvancedLabel";
            if(advanced_label.Create(Chart(), label_name, Subwin(),
                                    10, 10, 200, 30))
            {
                advanced_label.Text("Advanced Options");
                advanced_label.Font("Arial Bold");
                m_tab_contents[tab_index].Add(advanced_label);
            }
            break;
        }
    }
}

bool CTabbedRadioInterface::OnEvent(const int id, const long &lparam,
                                   const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        for(int i = 0; i < 4; i++)
        {
            if(sparam == m_tab_buttons[i].Name())
            {
                ShowTab(i);
                return true;
            }
        }
    }

    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}

void CTabbedRadioInterface::ShowTab(const int tab_index)
{
    if(tab_index < 0 || tab_index >= 4) return;

    // Update radio button states
    for(int i = 0; i < 4; i++)
    {
        m_tab_buttons[i].State(i == tab_index);

        // Show/hide tab content
        if(i == tab_index)
        {
            m_tab_panels[i].Show();
            m_tab_contents[i].Show();
            m_tab_buttons[i].Color(clrBlue);
        }
        else
        {
            m_tab_panels[i].Hide();
            m_tab_contents[i].Hide();
            m_tab_buttons[i].Color(clrBlack);
        }
    }

    m_active_tab = tab_index;
    Print("Switched to tab: ", m_tab_names[tab_index]);
}
```

## Best Practices

1. **Group Management**
   - Ensure only one radio button is selected per group
   - Provide clear visual feedback for selection
   - Use consistent naming conventions for related buttons

2. **User Experience**
   - Group related options logically
   - Provide meaningful option labels
   - Support keyboard navigation

3. **Visual Design**
   - Use consistent spacing between options
   - Apply clear selection indicators
   - Consider layout for different screen sizes

4. **State Management**
   - Always maintain group consistency
   - Handle default selections appropriately
   - Validate selection requirements

## Common Use Cases

- **Option Selection** - Choose one from multiple alternatives
- **Mode Selection** - Select application or operation modes
- **Category Filters** - Select data categories or types
- **Preference Settings** - Choose default behaviors
- **Tab Interfaces** - Navigate between different views
- **Configuration Options** - Select system configurations

## See Also

- [CWndContainer](../foundation/CWndContainer.md) - Container base class
- [CheckBox](./CheckBox.md) - Boolean selection controls
- [Form Design Patterns](../tutorials/form-patterns.md)
- [State Management Tutorial](../tutorials/state-management.md)