# CheckBox Controls - Boolean Selection

The checkbox controls (CCheckBox, CCheckGroup) provide boolean selection functionality for user interfaces in MQL5 applications.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndContainer
        ├── CCheckBox
        └── CCheckGroup
```

## Overview

- **Header File**: `<Controls\CheckBox.mqh>`
- **Purpose**: Boolean state selection (true/false conditions)
- **Key Features**: State management, text labels, grouping support
- **Components**: Check button, text label

## CCheckBox Control

### Core Methods

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
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
bool Checked() const;                   // Get checked state
virtual bool Checked(const bool flag); // Set checked state
```

#### Value Association
```mql5
long Value() const;                     // Get associated value
virtual bool Value(const long value);  // Set associated value
```

## Usage Examples

### Basic Checkbox Implementation

```mql5
#include <Controls\CheckBox.mqh>
#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>

class CCheckBoxDialog : public CAppDialog
{
private:
    CCheckBox m_check_enable_trading;
    CCheckBox m_check_show_alerts;
    CCheckBox m_check_auto_save;
    CCheckBox m_check_expert_advisor;
    CButton   m_button_apply;
    CButton   m_button_get_status;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    bool CreateControls(void);
    void OnApplySettings(void);
    void OnGetStatus(void);
    void UpdateCheckboxAppearance(CCheckBox &checkbox);
};

bool CCheckBoxDialog::Create(const long chart_id, const string name, const int subwin,
                            const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreateControls();
}

bool CCheckBoxDialog::CreateControls(void)
{
    int margin = 15;
    int row_height = 35;
    int y_pos = margin;

    // Enable Trading checkbox
    if(!m_check_enable_trading.Create(m_chart_id, m_name+"EnableTrading", m_subwin,
                                     margin, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_check_enable_trading.Text("Enable Trading");
    m_check_enable_trading.Color(clrBlack);
    m_check_enable_trading.Checked(true);
    m_check_enable_trading.Value(1);
    Add(m_check_enable_trading);

    y_pos += row_height;

    // Show Alerts checkbox
    if(!m_check_show_alerts.Create(m_chart_id, m_name+"ShowAlerts", m_subwin,
                                  margin, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_check_show_alerts.Text("Show Alert Messages");
    m_check_show_alerts.Color(clrBlack);
    m_check_show_alerts.Checked(false);
    m_check_show_alerts.Value(2);
    Add(m_check_show_alerts);

    y_pos += row_height;

    // Auto Save checkbox
    if(!m_check_auto_save.Create(m_chart_id, m_name+"AutoSave", m_subwin,
                                margin, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_check_auto_save.Text("Auto Save Configuration");
    m_check_auto_save.Color(clrBlack);
    m_check_auto_save.Checked(true);
    m_check_auto_save.Value(3);
    Add(m_check_auto_save);

    y_pos += row_height;

    // Expert Advisor checkbox
    if(!m_check_expert_advisor.Create(m_chart_id, m_name+"ExpertAdvisor", m_subwin,
                                     margin, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_check_expert_advisor.Text("Enable Expert Advisor");
    m_check_expert_advisor.Color(clrBlue);
    m_check_expert_advisor.Checked(false);
    m_check_expert_advisor.Value(4);
    Add(m_check_expert_advisor);

    y_pos += row_height + 10;

    // Buttons
    int button_width = 100;
    int button_spacing = 20;
    int button_start = (ClientAreaWidth() - (2 * button_width + button_spacing)) / 2;

    if(!m_button_apply.Create(m_chart_id, m_name+"Apply", m_subwin,
                             button_start, y_pos, button_start+button_width, y_pos+30))
        return false;
    m_button_apply.Text("Apply");
    m_button_apply.ColorBackground(clrGreen);
    Add(m_button_apply);

    if(!m_button_get_status.Create(m_chart_id, m_name+"GetStatus", m_subwin,
                                  button_start+button_width+button_spacing, y_pos,
                                  button_start+2*button_width+button_spacing, y_pos+30))
        return false;
    m_button_get_status.Text("Get Status");
    m_button_get_status.ColorBackground(clrBlue);
    Add(m_button_get_status);

    return true;
}

bool CCheckBoxDialog::OnEvent(const int id, const long &lparam,
                             const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        // Handle checkbox clicks
        if(sparam == m_check_enable_trading.Name() ||
           sparam == m_check_show_alerts.Name() ||
           sparam == m_check_auto_save.Name() ||
           sparam == m_check_expert_advisor.Name())
        {
            Print("Checkbox clicked: ", sparam);
            return true;
        }

        // Handle button clicks
        if(sparam == m_button_apply.Name())
        {
            OnApplySettings();
            return true;
        }
        if(sparam == m_button_get_status.Name())
        {
            OnGetStatus();
            return true;
        }
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CCheckBoxDialog::OnApplySettings(void)
{
    Print("Applying settings:");
    Print("  Trading Enabled: ", m_check_enable_trading.Checked());
    Print("  Show Alerts: ", m_check_show_alerts.Checked());
    Print("  Auto Save: ", m_check_auto_save.Checked());
    Print("  Expert Advisor: ", m_check_expert_advisor.Checked());

    // Apply visual feedback
    UpdateCheckboxAppearance(m_check_enable_trading);
    UpdateCheckboxAppearance(m_check_show_alerts);
    UpdateCheckboxAppearance(m_check_auto_save);
    UpdateCheckboxAppearance(m_check_expert_advisor);

    Alert("Settings applied successfully!");
}

void CCheckBoxDialog::OnGetStatus(void)
{
    string status = "Current Status:\n";
    status += "Trading: " + (m_check_enable_trading.Checked() ? "ON" : "OFF") + "\n";
    status += "Alerts: " + (m_check_show_alerts.Checked() ? "ON" : "OFF") + "\n";
    status += "Auto Save: " + (m_check_auto_save.Checked() ? "ON" : "OFF") + "\n";
    status += "Expert Advisor: " + (m_check_expert_advisor.Checked() ? "ON" : "OFF");

    Alert(status);
}

void CCheckBoxDialog::UpdateCheckboxAppearance(CCheckBox &checkbox)
{
    if(checkbox.Checked())
    {
        checkbox.Color(clrGreen);
    }
    else
    {
        checkbox.Color(clrGray);
    }
}
```

### Grouped Checkboxes with Dependencies

```mql5
class CAdvancedCheckBoxDialog : public CAppDialog
{
private:
    // Master checkbox
    CCheckBox m_check_enable_features;

    // Dependent checkboxes
    CCheckBox m_check_feature1;
    CCheckBox m_check_feature2;
    CCheckBox m_check_feature3;

    // Selection checkboxes
    CCheckBox m_check_select_all;
    CCheckBox m_check_items[10];
    int       m_item_count;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    bool CreateControls(void);
    void OnMasterCheckboxChange(void);
    void OnSelectAllChange(void);
    void OnItemCheckboxChange(const int item_index);
    void UpdateDependentCheckboxes(void);
    void UpdateSelectAllState(void);
    int  GetSelectedItemCount(void);
};

bool CAdvancedCheckBoxDialog::Create(const long chart_id, const string name, const int subwin,
                                    const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    m_item_count = 5; // Number of selectable items
    return CreateControls();
}

bool CAdvancedCheckBoxDialog::CreateControls(void)
{
    int margin = 15;
    int row_height = 30;
    int y_pos = margin;

    // Master enable checkbox
    if(!m_check_enable_features.Create(m_chart_id, m_name+"EnableFeatures", m_subwin,
                                      margin, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_check_enable_features.Text("Enable Advanced Features");
    m_check_enable_features.Color(clrBlue);
    m_check_enable_features.Checked(true);
    Add(m_check_enable_features);

    y_pos += row_height;

    // Dependent feature checkboxes (indented)
    int indent = 30;

    if(!m_check_feature1.Create(m_chart_id, m_name+"Feature1", m_subwin,
                               margin+indent, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_check_feature1.Text("Feature 1: Auto Analysis");
    m_check_feature1.Checked(false);
    Add(m_check_feature1);

    y_pos += row_height;

    if(!m_check_feature2.Create(m_chart_id, m_name+"Feature2", m_subwin,
                               margin+indent, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_check_feature2.Text("Feature 2: Smart Alerts");
    m_check_feature2.Checked(true);
    Add(m_check_feature2);

    y_pos += row_height;

    if(!m_check_feature3.Create(m_chart_id, m_name+"Feature3", m_subwin,
                               margin+indent, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_check_feature3.Text("Feature 3: Risk Management");
    m_check_feature3.Checked(false);
    Add(m_check_feature3);

    y_pos += row_height + 15;

    // Selection section
    CLabel section_label;
    if(!section_label.Create(m_chart_id, m_name+"SectionLabel", m_subwin,
                            margin, y_pos, ClientAreaWidth()-margin, y_pos+20))
        return false;
    section_label.Text("Select Items:");
    section_label.Font("Arial Bold");
    section_label.Color(clrBlue);
    Add(section_label);

    y_pos += 25;

    // Select All checkbox
    if(!m_check_select_all.Create(m_chart_id, m_name+"SelectAll", m_subwin,
                                 margin, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_check_select_all.Text("Select All Items");
    m_check_select_all.Color(clrRed);
    Add(m_check_select_all);

    y_pos += row_height;

    // Individual item checkboxes
    for(int i = 0; i < m_item_count; i++)
    {
        string item_name = m_name + "Item" + IntegerToString(i);
        if(!m_check_items[i].Create(m_chart_id, item_name, m_subwin,
                                   margin+indent, y_pos, ClientAreaWidth()-margin, y_pos+25))
            return false;

        string item_text = "Item " + IntegerToString(i + 1);
        m_check_items[i].Text(item_text);
        m_check_items[i].Checked(i % 2 == 0); // Check even items
        m_check_items[i].Value(i);
        Add(m_check_items[i]);

        y_pos += row_height;
    }

    UpdateDependentCheckboxes();
    UpdateSelectAllState();

    return true;
}

bool CAdvancedCheckBoxDialog::OnEvent(const int id, const long &lparam,
                                     const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        // Master checkbox
        if(sparam == m_check_enable_features.Name())
        {
            OnMasterCheckboxChange();
            return true;
        }

        // Select all checkbox
        if(sparam == m_check_select_all.Name())
        {
            OnSelectAllChange();
            return true;
        }

        // Individual item checkboxes
        for(int i = 0; i < m_item_count; i++)
        {
            if(sparam == m_check_items[i].Name())
            {
                OnItemCheckboxChange(i);
                return true;
            }
        }
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CAdvancedCheckBoxDialog::OnMasterCheckboxChange(void)
{
    Print("Master checkbox changed: ", m_check_enable_features.Checked());
    UpdateDependentCheckboxes();
}

void CAdvancedCheckBoxDialog::UpdateDependentCheckboxes(void)
{
    bool enabled = m_check_enable_features.Checked();

    // Enable/disable dependent features
    if(enabled)
    {
        m_check_feature1.Enable();
        m_check_feature2.Enable();
        m_check_feature3.Enable();
        m_check_feature1.Color(clrBlack);
        m_check_feature2.Color(clrBlack);
        m_check_feature3.Color(clrBlack);
    }
    else
    {
        m_check_feature1.Disable();
        m_check_feature2.Disable();
        m_check_feature3.Disable();
        m_check_feature1.Color(clrGray);
        m_check_feature2.Color(clrGray);
        m_check_feature3.Color(clrGray);
    }
}

void CAdvancedCheckBoxDialog::OnSelectAllChange(void)
{
    bool select_all = m_check_select_all.Checked();
    Print("Select all changed: ", select_all);

    // Set all items to the select all state
    for(int i = 0; i < m_item_count; i++)
    {
        m_check_items[i].Checked(select_all);
    }
}

void CAdvancedCheckBoxDialog::OnItemCheckboxChange(const int item_index)
{
    Print("Item ", item_index, " changed: ", m_check_items[item_index].Checked());
    UpdateSelectAllState();
}

void CAdvancedCheckBoxDialog::UpdateSelectAllState(void)
{
    int selected_count = GetSelectedItemCount();

    if(selected_count == 0)
    {
        m_check_select_all.Checked(false);
        m_check_select_all.Color(clrRed);
    }
    else if(selected_count == m_item_count)
    {
        m_check_select_all.Checked(true);
        m_check_select_all.Color(clrGreen);
    }
    else
    {
        // Partial selection - could show different visual state
        m_check_select_all.Checked(false);
        m_check_select_all.Color(clrBlue);
    }
}

int CAdvancedCheckBoxDialog::GetSelectedItemCount(void)
{
    int count = 0;
    for(int i = 0; i < m_item_count; i++)
    {
        if(m_check_items[i].Checked())
            count++;
    }
    return count;
}
```

### Checkbox with Custom States

```mql5
enum ENUM_CHECKBOX_STATE
{
    CHECKBOX_UNCHECKED,
    CHECKBOX_CHECKED,
    CHECKBOX_INDETERMINATE
};

class CTriStateCheckBox : public CCheckBox
{
private:
    ENUM_CHECKBOX_STATE m_state;

public:
    CTriStateCheckBox() : m_state(CHECKBOX_UNCHECKED) {}

    ENUM_CHECKBOX_STATE GetState(void) const { return m_state; }
    void SetState(const ENUM_CHECKBOX_STATE state);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    void UpdateVisualState(void);
};

void CTriStateCheckBox::SetState(const ENUM_CHECKBOX_STATE state)
{
    m_state = state;
    UpdateVisualState();
}

void CTriStateCheckBox::UpdateVisualState(void)
{
    switch(m_state)
    {
        case CHECKBOX_UNCHECKED:
            Checked(false);
            Color(clrBlack);
            break;

        case CHECKBOX_CHECKED:
            Checked(true);
            Color(clrGreen);
            break;

        case CHECKBOX_INDETERMINATE:
            Checked(false); // Visual approximation
            Color(clrBlue);
            // Could add special text indicator
            break;
    }
}

bool CTriStateCheckBox::OnEvent(const int id, const long &lparam,
                               const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK && sparam == Name())
    {
        // Cycle through states
        switch(m_state)
        {
            case CHECKBOX_UNCHECKED:
                SetState(CHECKBOX_CHECKED);
                break;
            case CHECKBOX_CHECKED:
                SetState(CHECKBOX_INDETERMINATE);
                break;
            case CHECKBOX_INDETERMINATE:
                SetState(CHECKBOX_UNCHECKED);
                break;
        }
        return true;
    }

    return CCheckBox::OnEvent(id, lparam, dparam, sparam);
}
```

## CheckBox Data Binding

### Checkbox with Settings

```mql5
struct SApplicationSettings
{
    bool enable_trading;
    bool show_alerts;
    bool auto_save;
    bool expert_advisor;
    bool sound_alerts;
};

class CSettingsCheckBoxPanel : public CWndContainer
{
private:
    SApplicationSettings m_settings;
    CCheckBox m_checkboxes[5];
    string    m_checkbox_names[5];

public:
    CSettingsCheckBoxPanel();
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void LoadSettings(const SApplicationSettings &settings);
    SApplicationSettings GetSettings(void) const { return m_settings; }

private:
    bool CreateCheckboxes(void);
    void UpdateSettingsFromUI(void);
    void UpdateUIFromSettings(void);
};

CSettingsCheckBoxPanel::CSettingsCheckBoxPanel(void)
{
    // Initialize setting names
    m_checkbox_names[0] = "Enable Trading";
    m_checkbox_names[1] = "Show Alerts";
    m_checkbox_names[2] = "Auto Save";
    m_checkbox_names[3] = "Expert Advisor";
    m_checkbox_names[4] = "Sound Alerts";

    // Default settings
    m_settings.enable_trading = true;
    m_settings.show_alerts = false;
    m_settings.auto_save = true;
    m_settings.expert_advisor = false;
    m_settings.sound_alerts = true;
}

bool CSettingsCheckBoxPanel::Create(const long chart_id, const string name, const int subwin,
                                   const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreateCheckboxes();
}

bool CSettingsCheckBoxPanel::CreateCheckboxes(void)
{
    int margin = 10;
    int row_height = 30;

    for(int i = 0; i < 5; i++)
    {
        string checkbox_name = Name() + "Check" + IntegerToString(i);
        int y_pos = margin + (i * row_height);

        if(!m_checkboxes[i].Create(Chart(), checkbox_name, Subwin(),
                                  margin, y_pos, Width()-margin, y_pos+25))
            return false;

        m_checkboxes[i].Text(m_checkbox_names[i]);
        m_checkboxes[i].Value(i);
        Add(m_checkboxes[i]);
    }

    UpdateUIFromSettings();
    return true;
}

void CSettingsCheckBoxPanel::LoadSettings(const SApplicationSettings &settings)
{
    m_settings = settings;
    UpdateUIFromSettings();
}

void CSettingsCheckBoxPanel::UpdateUIFromSettings(void)
{
    m_checkboxes[0].Checked(m_settings.enable_trading);
    m_checkboxes[1].Checked(m_settings.show_alerts);
    m_checkboxes[2].Checked(m_settings.auto_save);
    m_checkboxes[3].Checked(m_settings.expert_advisor);
    m_checkboxes[4].Checked(m_settings.sound_alerts);
}

void CSettingsCheckBoxPanel::UpdateSettingsFromUI(void)
{
    m_settings.enable_trading = m_checkboxes[0].Checked();
    m_settings.show_alerts = m_checkboxes[1].Checked();
    m_settings.auto_save = m_checkboxes[2].Checked();
    m_settings.expert_advisor = m_checkboxes[3].Checked();
    m_settings.sound_alerts = m_checkboxes[4].Checked();
}

bool CSettingsCheckBoxPanel::OnEvent(const int id, const long &lparam,
                                    const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        for(int i = 0; i < 5; i++)
        {
            if(sparam == m_checkboxes[i].Name())
            {
                UpdateSettingsFromUI();
                Print("Setting changed: ", m_checkbox_names[i], " = ", m_checkboxes[i].Checked());
                return true;
            }
        }
    }

    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}
```

## Best Practices

1. **State Management**
   - Always synchronize checkbox state with underlying data
   - Handle dependencies between related checkboxes
   - Provide clear visual feedback for state changes

2. **User Experience**
   - Use descriptive text labels
   - Group related checkboxes logically
   - Support keyboard navigation where possible

3. **Visual Design**
   - Use consistent spacing and alignment
   - Apply appropriate colors for different states
   - Consider indentation for hierarchical relationships

4. **Data Binding**
   - Separate UI state from data model
   - Validate checkbox combinations
   - Provide undo/redo functionality for complex forms

## Common Use Cases

- **Settings Panels** - Application configuration options
- **Feature Toggles** - Enable/disable functionality
- **Item Selection** - Multi-select lists and grids
- **Permission Controls** - Access and security settings
- **Filter Controls** - Data filtering and display options
- **Batch Operations** - Select items for group actions

## See Also

- [CWndContainer](../foundation/CWndContainer.md) - Container base class
- [RadioButton](./RadioButton.md) - Mutually exclusive selection
- [Form Design Patterns](../tutorials/form-patterns.md)
- [State Management Tutorial](../tutorials/state-management.md)