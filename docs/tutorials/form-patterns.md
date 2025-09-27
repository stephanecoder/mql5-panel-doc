# Form Design Patterns for MQL5 Panels

This tutorial covers common form design patterns and user interface principles for creating professional MQL5 trading panels. Learn how to design intuitive, efficient, and visually appealing trading interfaces.

## Core Design Principles

### 1. Consistency
Maintain consistent design elements throughout your application:

```mql5
// Define standard design constants
class CDesignConstants
{
public:
    // Colors
    static const color COLOR_PRIMARY = clrBlue;
    static const color COLOR_SUCCESS = clrGreen;
    static const color COLOR_WARNING = clrOrange;
    static const color COLOR_DANGER = clrRed;
    static const color COLOR_BACKGROUND = clrWhite;
    static const color COLOR_BORDER = clrGray;

    // Spacing
    static const int MARGIN_SMALL = 5;
    static const int MARGIN_MEDIUM = 10;
    static const int MARGIN_LARGE = 20;
    static const int SPACING_CONTROLS = 5;

    // Sizes
    static const int BUTTON_HEIGHT = 30;
    static const int EDIT_HEIGHT = 25;
    static const int LABEL_HEIGHT = 20;
    static const int ICON_SIZE = 16;

    // Fonts
    static const string FONT_DEFAULT = "Arial";
    static const string FONT_HEADING = "Arial Bold";
    static const int FONT_SIZE_DEFAULT = 9;
    static const int FONT_SIZE_HEADING = 12;
};
```

### 2. Visual Hierarchy
Create clear visual hierarchy using size, color, and positioning:

```mql5
class CVisualHierarchy : public CAppDialog
{
private:
    CLabel m_title;
    CLabel m_subtitle;
    CPanel m_section_panel;
    CLabel m_section_label;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

private:
    bool CreateHierarchicalLayout(void);
};

bool CVisualHierarchy::CreateHierarchicalLayout(void)
{
    int y_pos = CDesignConstants::MARGIN_MEDIUM;

    // Primary title - largest, most prominent
    if(!m_title.Create(m_chart_id, m_name+"Title", m_subwin,
                      CDesignConstants::MARGIN_MEDIUM, y_pos,
                      ClientAreaWidth()-CDesignConstants::MARGIN_MEDIUM, y_pos+25))
        return false;

    m_title.Text("Trading Dashboard");
    m_title.Font(CDesignConstants::FONT_HEADING);
    m_title.FontSize(16);
    m_title.Color(CDesignConstants::COLOR_PRIMARY);
    Add(m_title);

    y_pos += 35;

    // Subtitle - smaller, secondary
    if(!m_subtitle.Create(m_chart_id, m_name+"Subtitle", m_subwin,
                         CDesignConstants::MARGIN_MEDIUM, y_pos,
                         ClientAreaWidth()-CDesignConstants::MARGIN_MEDIUM, y_pos+20))
        return false;

    m_subtitle.Text("Real-time Market Analysis");
    m_subtitle.Font(CDesignConstants::FONT_DEFAULT);
    m_subtitle.FontSize(CDesignConstants::FONT_SIZE_DEFAULT);
    m_subtitle.Color(clrGray);
    Add(m_subtitle);

    y_pos += 30;

    // Section container with visual separation
    if(!m_section_panel.Create(m_chart_id, m_name+"Section", m_subwin,
                              CDesignConstants::MARGIN_MEDIUM, y_pos,
                              ClientAreaWidth()-CDesignConstants::MARGIN_MEDIUM, y_pos+100))
        return false;

    m_section_panel.ColorBackground(clrLightGray);
    m_section_panel.ColorBorder(CDesignConstants::COLOR_BORDER);
    Add(m_section_panel);

    return true;
}
```

### 3. Progressive Disclosure
Reveal information progressively to avoid overwhelming users:

```mql5
class CProgressiveDisclosure : public CAppDialog
{
private:
    CButton m_basic_mode_btn;
    CButton m_advanced_mode_btn;
    CWndContainer m_basic_panel;
    CWndContainer m_advanced_panel;
    bool m_advanced_mode;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);
    void ToggleMode(void);

private:
    bool CreateModeToggle(void);
    bool CreateBasicPanel(void);
    bool CreateAdvancedPanel(void);
};

void CProgressiveDisclosure::ToggleMode(void)
{
    m_advanced_mode = !m_advanced_mode;

    if(m_advanced_mode)
    {
        m_basic_panel.Hide();
        m_advanced_panel.Show();
        m_advanced_mode_btn.ColorBackground(CDesignConstants::COLOR_PRIMARY);
        m_basic_mode_btn.ColorBackground(clrLightGray);
    }
    else
    {
        m_advanced_panel.Hide();
        m_basic_panel.Show();
        m_basic_mode_btn.ColorBackground(CDesignConstants::COLOR_PRIMARY);
        m_advanced_mode_btn.ColorBackground(clrLightGray);
    }
}
```

## Common Form Patterns

### 1. Master-Detail Pattern

```mql5
class CMasterDetailPanel : public CAppDialog
{
private:
    CListView     m_symbol_list;      // Master: list of symbols
    CWndContainer m_detail_panel;     // Detail: symbol information
    CLabel        m_detail_labels[10];
    string        m_selected_symbol;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    bool CreateMasterDetailLayout(void);
    void OnSymbolSelected(const string symbol);
    void UpdateDetailPanel(const string symbol);
    void PopulateSymbolList(void);
};

bool CMasterDetailPanel::CreateMasterDetailLayout(void)
{
    int panel_width = ClientAreaWidth();
    int panel_height = ClientAreaHeight();
    int master_width = panel_width * 30 / 100; // 30% for master

    // Master panel (symbol list)
    if(!m_symbol_list.Create(m_chart_id, m_name+"SymbolList", m_subwin,
                            CDesignConstants::MARGIN_MEDIUM,
                            CDesignConstants::MARGIN_MEDIUM,
                            master_width,
                            panel_height - CDesignConstants::MARGIN_MEDIUM))
        return false;

    m_symbol_list.TotalView(10);
    Add(m_symbol_list);
    PopulateSymbolList();

    // Detail panel
    if(!m_detail_panel.Create(m_chart_id, m_name+"DetailPanel", m_subwin,
                             master_width + CDesignConstants::MARGIN_MEDIUM * 2,
                             CDesignConstants::MARGIN_MEDIUM,
                             panel_width - CDesignConstants::MARGIN_MEDIUM,
                             panel_height - CDesignConstants::MARGIN_MEDIUM))
        return false;

    Add(m_detail_panel);

    return true;
}

void CMasterDetailPanel::OnSymbolSelected(const string symbol)
{
    m_selected_symbol = symbol;
    UpdateDetailPanel(symbol);
}

void CMasterDetailPanel::UpdateDetailPanel(const string symbol)
{
    // Update detail panel with symbol-specific information
    double bid = SymbolInfoDouble(symbol, SYMBOL_BID);
    double ask = SymbolInfoDouble(symbol, SYMBOL_ASK);
    double spread = ask - bid;
    int digits = (int)SymbolInfoInteger(symbol, SYMBOL_DIGITS);

    // Update labels with current data
    string details[] = {
        "Symbol: " + symbol,
        "Bid: " + DoubleToString(bid, digits),
        "Ask: " + DoubleToString(ask, digits),
        "Spread: " + DoubleToString(spread, digits),
        "Point: " + DoubleToString(SymbolInfoDouble(symbol, SYMBOL_POINT), digits+1)
    };

    for(int i = 0; i < 5; i++)
    {
        if(i < ArraySize(m_detail_labels))
        {
            m_detail_labels[i].Text(details[i]);
        }
    }
}
```

### 2. Wizard Pattern

```mql5
class CWizardPanel : public CAppDialog
{
private:
    CWndContainer m_steps[4];
    CButton       m_next_btn;
    CButton       m_prev_btn;
    CButton       m_finish_btn;
    CLabel        m_step_indicator;
    int           m_current_step;
    int           m_total_steps;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    bool CreateWizardLayout(void);
    bool CreateStep(const int step_index, const string title);
    void NavigateToStep(const int step);
    void UpdateNavigationButtons(void);
    void UpdateStepIndicator(void);
    bool ValidateCurrentStep(void);
};

bool CWizardPanel::CreateWizardLayout(void)
{
    m_total_steps = 4;
    m_current_step = 0;

    // Create step indicator
    if(!m_step_indicator.Create(m_chart_id, m_name+"StepIndicator", m_subwin,
                               CDesignConstants::MARGIN_MEDIUM, CDesignConstants::MARGIN_MEDIUM,
                               ClientAreaWidth()-CDesignConstants::MARGIN_MEDIUM, 30))
        return false;

    m_step_indicator.Font(CDesignConstants::FONT_HEADING);
    Add(m_step_indicator);

    // Create step containers
    int step_top = 50;
    int step_height = ClientAreaHeight() - 100;

    for(int i = 0; i < m_total_steps; i++)
    {
        string step_name = m_name + "Step" + IntegerToString(i);
        if(!m_steps[i].Create(m_chart_id, step_name, m_subwin,
                             CDesignConstants::MARGIN_MEDIUM, step_top,
                             ClientAreaWidth()-CDesignConstants::MARGIN_MEDIUM,
                             step_top + step_height))
            return false;

        Add(m_steps[i]);

        // Initially hide all steps except first
        if(i != 0) m_steps[i].Hide();
    }

    // Create navigation buttons
    int btn_y = ClientAreaHeight() - 40;
    int btn_width = 80;

    if(!m_prev_btn.Create(m_chart_id, m_name+"PrevBtn", m_subwin,
                         CDesignConstants::MARGIN_MEDIUM, btn_y,
                         CDesignConstants::MARGIN_MEDIUM + btn_width, btn_y + 30))
        return false;

    m_prev_btn.Text("< Previous");
    Add(m_prev_btn);

    if(!m_next_btn.Create(m_chart_id, m_name+"NextBtn", m_subwin,
                         ClientAreaWidth() - CDesignConstants::MARGIN_MEDIUM - btn_width * 2 - 10, btn_y,
                         ClientAreaWidth() - CDesignConstants::MARGIN_MEDIUM - btn_width - 10, btn_y + 30))
        return false;

    m_next_btn.Text("Next >");
    Add(m_next_btn);

    if(!m_finish_btn.Create(m_chart_id, m_name+"FinishBtn", m_subwin,
                           ClientAreaWidth() - CDesignConstants::MARGIN_MEDIUM - btn_width, btn_y,
                           ClientAreaWidth() - CDesignConstants::MARGIN_MEDIUM, btn_y + 30))
        return false;

    m_finish_btn.Text("Finish");
    m_finish_btn.Hide(); // Show only on last step
    Add(m_finish_btn);

    UpdateNavigationButtons();
    UpdateStepIndicator();

    return true;
}

void CWizardPanel::NavigateToStep(const int step)
{
    if(step < 0 || step >= m_total_steps) return;

    // Hide current step
    m_steps[m_current_step].Hide();

    // Show new step
    m_current_step = step;
    m_steps[m_current_step].Show();

    UpdateNavigationButtons();
    UpdateStepIndicator();
}

void CWizardPanel::UpdateNavigationButtons(void)
{
    // Previous button
    if(m_current_step == 0)
        m_prev_btn.Disable();
    else
        m_prev_btn.Enable();

    // Next/Finish buttons
    if(m_current_step == m_total_steps - 1)
    {
        m_next_btn.Hide();
        m_finish_btn.Show();
    }
    else
    {
        m_next_btn.Show();
        m_finish_btn.Hide();
    }
}

void CWizardPanel::UpdateStepIndicator(void)
{
    string indicator = StringFormat("Step %d of %d", m_current_step + 1, m_total_steps);
    m_step_indicator.Text(indicator);
}
```

### 3. Dashboard Pattern

```mql5
class CDashboardPanel : public CAppDialog
{
private:
    struct SDashboardWidget
    {
        CPanel container;
        CLabel title;
        CLabel value;
        CLabel status;
        string data_source;
    };

    SDashboardWidget m_widgets[9]; // 3x3 grid
    int              m_widget_count;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

private:
    bool CreateDashboardLayout(void);
    bool CreateWidget(const int index, const string title, const string data_source);
    void UpdateWidget(const int index, const string value, const string status, const color status_color);
    void CalculateWidgetPosition(const int index, int &x, int &y, int &w, int &h);
    void RefreshAllWidgets(void);
};

bool CDashboardPanel::CreateDashboardLayout(void)
{
    m_widget_count = 6;

    // Create widgets
    CreateWidget(0, "Account Balance", "account");
    CreateWidget(1, "Open Positions", "positions");
    CreateWidget(2, "Daily P&L", "profit");
    CreateWidget(3, "Margin Level", "margin");
    CreateWidget(4, "Server Status", "connection");
    CreateWidget(5, "Active Orders", "orders");

    return true;
}

bool CDashboardPanel::CreateWidget(const int index, const string title, const string data_source)
{
    if(index >= 9) return false;

    int x, y, w, h;
    CalculateWidgetPosition(index, x, y, w, h);

    SDashboardWidget &widget = m_widgets[index];
    widget.data_source = data_source;

    // Container
    string container_name = m_name + "Widget" + IntegerToString(index);
    if(!widget.container.Create(m_chart_id, container_name, m_subwin, x, y, x+w, y+h))
        return false;

    widget.container.ColorBackground(clrWhite);
    widget.container.ColorBorder(CDesignConstants::COLOR_BORDER);
    Add(widget.container);

    // Title
    string title_name = container_name + "Title";
    if(!widget.title.Create(m_chart_id, title_name, m_subwin,
                           x+5, y+5, x+w-5, y+20))
        return false;

    widget.title.Text(title);
    widget.title.Font(CDesignConstants::FONT_HEADING);
    widget.title.FontSize(CDesignConstants::FONT_SIZE_DEFAULT);
    widget.title.Color(CDesignConstants::COLOR_PRIMARY);
    Add(widget.title);

    // Value
    string value_name = container_name + "Value";
    if(!widget.value.Create(m_chart_id, value_name, m_subwin,
                           x+5, y+25, x+w-5, y+45))
        return false;

    widget.value.Text("Loading...");
    widget.value.Font(CDesignConstants::FONT_HEADING);
    widget.value.FontSize(12);
    Add(widget.value);

    // Status
    string status_name = container_name + "Status";
    if(!widget.status.Create(m_chart_id, status_name, m_subwin,
                            x+5, y+h-20, x+w-5, y+h-5))
        return false;

    widget.status.Text("OK");
    widget.status.FontSize(8);
    widget.status.Color(CDesignConstants::COLOR_SUCCESS);
    Add(widget.status);

    return true;
}

void CDashboardPanel::CalculateWidgetPosition(const int index, int &x, int &y, int &w, int &h)
{
    int columns = 3;
    int rows = 3;
    int margin = CDesignConstants::MARGIN_MEDIUM;
    int spacing = CDesignConstants::SPACING_CONTROLS;

    int available_width = ClientAreaWidth() - (2 * margin);
    int available_height = ClientAreaHeight() - (2 * margin);

    w = (available_width - (spacing * (columns - 1))) / columns;
    h = (available_height - (spacing * (rows - 1))) / rows;

    int row = index / columns;
    int col = index % columns;

    x = margin + col * (w + spacing);
    y = margin + row * (h + spacing);
}

void CDashboardPanel::UpdateWidget(const int index, const string value, const string status, const color status_color)
{
    if(index >= m_widget_count) return;

    m_widgets[index].value.Text(value);
    m_widgets[index].status.Text(status);
    m_widgets[index].status.Color(status_color);
}
```

### 4. Settings Form Pattern

```mql5
class CSettingsForm : public CAppDialog
{
private:
    struct SSettingControl
    {
        CLabel label;
        CWnd*  control;  // Can be CEdit, CComboBox, CCheckBox, etc.
        string setting_name;
        string control_type;
    };

    SSettingControl m_settings[20];
    int             m_setting_count;
    CButton         m_save_btn;
    CButton         m_cancel_btn;
    CButton         m_reset_btn;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    bool AddTextSetting(const string name, const string label, const string default_value);
    bool AddCheckboxSetting(const string name, const string label, const bool default_value);
    bool AddComboSetting(const string name, const string label, const string options[], const int option_count);

private:
    bool CreateSettingsForm(void);
    void LoadSettings(void);
    void SaveSettings(void);
    void ResetToDefaults(void);
    void LayoutSettings(void);
};

bool CSettingsForm::AddTextSetting(const string name, const string label, const string default_value)
{
    if(m_setting_count >= 20) return false;

    SSettingControl &setting = m_settings[m_setting_count];
    setting.setting_name = name;
    setting.control_type = "edit";

    // Create label
    string label_name = m_name + "Label" + IntegerToString(m_setting_count);
    if(!setting.label.Create(m_chart_id, label_name, m_subwin, 0, 0, 100, 20))
        return false;

    setting.label.Text(label);
    Add(setting.label);

    // Create edit control
    CEdit* edit = new CEdit();
    string edit_name = m_name + "Edit" + IntegerToString(m_setting_count);
    if(!edit.Create(m_chart_id, edit_name, m_subwin, 0, 0, 150, 25))
    {
        delete edit;
        return false;
    }

    edit.Text(default_value);
    setting.control = edit;
    Add(*edit);

    m_setting_count++;
    LayoutSettings();

    return true;
}

bool CSettingsForm::AddCheckboxSetting(const string name, const string label, const bool default_value)
{
    if(m_setting_count >= 20) return false;

    SSettingControl &setting = m_settings[m_setting_count];
    setting.setting_name = name;
    setting.control_type = "checkbox";

    // Create checkbox (which includes its own label)
    CCheckBox* checkbox = new CCheckBox();
    string checkbox_name = m_name + "Check" + IntegerToString(m_setting_count);
    if(!checkbox.Create(m_chart_id, checkbox_name, m_subwin, 0, 0, 200, 25))
    {
        delete checkbox;
        return false;
    }

    checkbox.Text(label);
    checkbox.Checked(default_value);
    setting.control = checkbox;
    Add(*checkbox);

    m_setting_count++;
    LayoutSettings();

    return true;
}

void CSettingsForm::LayoutSettings(void)
{
    int y_pos = CDesignConstants::MARGIN_MEDIUM;
    int row_height = 35;

    for(int i = 0; i < m_setting_count; i++)
    {
        SSettingControl &setting = m_settings[i];

        if(setting.control_type == "edit")
        {
            // Position label and edit control
            setting.label.Move(CDesignConstants::MARGIN_MEDIUM, y_pos);
            setting.control.Move(CDesignConstants::MARGIN_MEDIUM + 120, y_pos);
        }
        else if(setting.control_type == "checkbox")
        {
            // Position checkbox
            setting.control.Move(CDesignConstants::MARGIN_MEDIUM, y_pos);
        }

        y_pos += row_height;
    }

    // Position action buttons at bottom
    int btn_y = y_pos + CDesignConstants::MARGIN_LARGE;
    int btn_width = 80;
    int btn_spacing = 10;

    m_save_btn.Move(CDesignConstants::MARGIN_MEDIUM, btn_y);
    m_cancel_btn.Move(CDesignConstants::MARGIN_MEDIUM + btn_width + btn_spacing, btn_y);
    m_reset_btn.Move(CDesignConstants::MARGIN_MEDIUM + 2*(btn_width + btn_spacing), btn_y);
}
```

## Responsive Design Techniques

### 1. Adaptive Layouts

```mql5
class CResponsiveForm : public CAppDialog
{
private:
    enum ENUM_LAYOUT_MODE
    {
        LAYOUT_COMPACT,    // Small windows
        LAYOUT_STANDARD,   // Medium windows
        LAYOUT_EXPANDED    // Large windows
    };

    ENUM_LAYOUT_MODE m_current_layout;

public:
    virtual bool OnResize(void);

private:
    ENUM_LAYOUT_MODE DetermineLayoutMode(void);
    void ApplyLayout(const ENUM_LAYOUT_MODE mode);
    void ApplyCompactLayout(void);
    void ApplyStandardLayout(void);
    void ApplyExpandedLayout(void);
};

bool CResponsiveForm::OnResize(void)
{
    ENUM_LAYOUT_MODE new_mode = DetermineLayoutMode();

    if(new_mode != m_current_layout)
    {
        m_current_layout = new_mode;
        ApplyLayout(new_mode);
    }

    return CAppDialog::OnResize();
}

ENUM_LAYOUT_MODE CResponsiveForm::DetermineLayoutMode(void)
{
    int width = ClientAreaWidth();
    int height = ClientAreaHeight();

    if(width < 300 || height < 200)
        return LAYOUT_COMPACT;
    else if(width < 600 || height < 400)
        return LAYOUT_STANDARD;
    else
        return LAYOUT_EXPANDED;
}
```

### 2. Scalable Controls

```mql5
class CScalableControls
{
public:
    static void ScaleControl(CWnd &control, const double scale_factor);
    static void ScaleFont(CWndObj &control, const double scale_factor);
    static void ScaleContainer(CWndContainer &container, const double scale_factor);

private:
    static int ScaleValue(const int value, const double factor);
};

void CScalableControls::ScaleControl(CWnd &control, const double scale_factor)
{
    int new_width = ScaleValue(control.Width(), scale_factor);
    int new_height = ScaleValue(control.Height(), scale_factor);

    control.Resize(new_width, new_height);
}

void CScalableControls::ScaleFont(CWndObj &control, const double scale_factor)
{
    int current_size = control.FontSize();
    int new_size = ScaleValue(current_size, scale_factor);

    control.FontSize(MathMax(8, new_size)); // Minimum font size
}

int CScalableControls::ScaleValue(const int value, const double factor)
{
    return (int)(value * factor + 0.5); // Round to nearest integer
}
```

## Accessibility and Usability

### 1. Keyboard Navigation

```mql5
class CKeyboardNavigation : public CAppDialog
{
private:
    CWnd* m_focusable_controls[50];
    int   m_control_count;
    int   m_focused_index;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void RegisterFocusableControl(CWnd* control);
    void SetFocus(const int index);
    void MoveFocusNext(void);
    void MoveFocusPrevious(void);

protected:
    bool OnKeyDown(const int key_code);
    void UpdateFocusVisuals(void);
};

bool CKeyboardNavigation::OnKeyDown(const int key_code)
{
    switch(key_code)
    {
        case VK_TAB:
            if(GetKeyState(VK_SHIFT) & 0x8000)
                MoveFocusPrevious();
            else
                MoveFocusNext();
            return true;

        case VK_RETURN:
            // Activate focused control
            if(m_focused_index >= 0 && m_focused_index < m_control_count)
            {
                // Simulate click on focused control
                string control_name = m_focusable_controls[m_focused_index].Name();
                OnEvent(CHARTEVENT_OBJECT_CLICK, 0, 0, control_name);
            }
            return true;

        case VK_ESCAPE:
            // Close dialog or cancel operation
            Destroy();
            return true;
    }

    return false;
}

void CKeyboardNavigation::MoveFocusNext(void)
{
    m_focused_index = (m_focused_index + 1) % m_control_count;
    UpdateFocusVisuals();
}

void CKeyboardNavigation::UpdateFocusVisuals(void)
{
    for(int i = 0; i < m_control_count; i++)
    {
        // Update visual focus indicator
        // This would require custom drawing or border manipulation
    }
}
```

### 2. Error Handling and Validation

```mql5
class CFormValidation : public CAppDialog
{
private:
    struct SValidationRule
    {
        string control_name;
        string rule_type;
        string parameters;
        string error_message;
    };

    SValidationRule m_rules[20];
    int             m_rule_count;
    CLabel          m_error_label;

public:
    void AddRequiredRule(const string control_name, const string error_message);
    void AddRangeRule(const string control_name, const double min_val, const double max_val, const string error_message);
    void AddPatternRule(const string control_name, const string pattern, const string error_message);

    bool ValidateForm(void);
    void ShowError(const string message);
    void ClearErrors(void);

private:
    bool ValidateControl(const SValidationRule &rule);
    string GetControlValue(const string control_name);
};

bool CFormValidation::ValidateForm(void)
{
    ClearErrors();

    for(int i = 0; i < m_rule_count; i++)
    {
        if(!ValidateControl(m_rules[i]))
        {
            ShowError(m_rules[i].error_message);
            return false;
        }
    }

    return true;
}

void CFormValidation::AddRequiredRule(const string control_name, const string error_message)
{
    if(m_rule_count >= 20) return;

    m_rules[m_rule_count].control_name = control_name;
    m_rules[m_rule_count].rule_type = "required";
    m_rules[m_rule_count].parameters = "";
    m_rules[m_rule_count].error_message = error_message;
    m_rule_count++;
}

void CFormValidation::ShowError(const string message)
{
    m_error_label.Text(message);
    m_error_label.Color(CDesignConstants::COLOR_DANGER);
    m_error_label.Show();
}

bool CFormValidation::ValidateControl(const SValidationRule &rule)
{
    string value = GetControlValue(rule.control_name);

    if(rule.rule_type == "required")
    {
        return StringLen(value) > 0;
    }
    else if(rule.rule_type == "range")
    {
        double num_value = StringToDouble(value);
        // Parse min and max from parameters
        // Return range validation result
    }
    else if(rule.rule_type == "pattern")
    {
        // Implement pattern matching
        // Return pattern validation result
    }

    return true;
}
```

## Performance Optimization

### 1. Lazy Loading

```mql5
class CLazyLoadedPanel : public CAppDialog
{
private:
    bool m_heavy_controls_loaded;
    CButton m_load_btn;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    void LoadHeavyControls(void);
    bool CreateLightweightInterface(void);
};

void CLazyLoadedPanel::LoadHeavyControls(void)
{
    if(m_heavy_controls_loaded) return;

    Print("Loading additional controls...");

    // Create complex controls only when needed
    // This improves initial load time

    m_heavy_controls_loaded = true;
    m_load_btn.Hide();
}
```

### 2. Control Pooling

```mql5
class CControlPool
{
private:
    CButton m_button_pool[50];
    bool    m_button_used[50];
    int     m_pool_size;

public:
    CControlPool() : m_pool_size(50) {}

    CButton* GetButton(void);
    void ReturnButton(CButton* button);
    void InitializePool(const long chart_id, const int subwin);

private:
    int FindAvailableButton(void);
};

CButton* CControlPool::GetButton(void)
{
    int index = FindAvailableButton();
    if(index >= 0)
    {
        m_button_used[index] = true;
        return &m_button_pool[index];
    }
    return NULL;
}

void CControlPool::ReturnButton(CButton* button)
{
    for(int i = 0; i < m_pool_size; i++)
    {
        if(&m_button_pool[i] == button)
        {
            m_button_used[i] = false;
            button.Hide();
            break;
        }
    }
}
```

## Best Practices Summary

### 1. Design Principles
- **Consistency** - Use standard colors, fonts, and spacing
- **Clarity** - Make purpose and actions obvious
- **Efficiency** - Minimize clicks and navigation
- **Feedback** - Provide clear status and error messages

### 2. Technical Guidelines
- **Modular design** - Create reusable components
- **Event handling** - Implement proper event delegation
- **Performance** - Use lazy loading and control pooling
- **Accessibility** - Support keyboard navigation

### 3. User Experience
- **Progressive disclosure** - Show information as needed
- **Visual hierarchy** - Guide user attention effectively
- **Error prevention** - Validate input and prevent mistakes
- **Responsive design** - Adapt to different window sizes

## Common Anti-Patterns to Avoid

1. **Cluttered interfaces** - Too many controls visible at once
2. **Inconsistent styling** - Mixed fonts, colors, and spacing
3. **Poor error handling** - Vague or missing error messages
4. **Non-responsive layouts** - Fixed layouts that don't adapt
5. **Missing validation** - Accepting invalid input without feedback

By following these form design patterns and principles, you'll create professional, user-friendly MQL5 trading panels that provide excellent user experience and maintain high code quality.

## See Also

- [Layout Management](./layout-management.md) - Control positioning techniques
- [Event Handling](./event-handling.md) - User interaction patterns
- [Getting Started](./getting-started.md) - Basic panel creation