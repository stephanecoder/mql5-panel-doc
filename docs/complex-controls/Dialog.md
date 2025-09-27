# Dialog Controls - Window Management

The dialog controls (CDialog, CAppDialog) provide the foundation for creating window-based user interfaces in MQL5 applications.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndContainer
        └── CDialog
            └── CAppDialog
```

## Overview

- **Header File**: `<Controls\Dialog.mqh>`
- **Purpose**: Main window containers for user interfaces
- **Key Features**: Title bar, client area, drag support, control management
- **Components**: Caption, close button, client area, child controls

## CDialog Base Class

### Core Methods

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Component Creation
```mql5
virtual bool CreateCaption(void);      // Create title bar
virtual bool CreateButtonClose(void);  // Create close button
virtual bool CreateClientArea(void);   // Create content area
```

### Client Area Properties

#### Size and Position
```mql5
bool ClientAreaVisible() const;        // Check if client area is visible
int ClientAreaLeft() const;            // Get client area left
int ClientAreaTop() const;             // Get client area top
int ClientAreaRight() const;           // Get client area right
int ClientAreaBottom() const;          // Get client area bottom
int ClientAreaWidth() const;           // Get client area width
int ClientAreaHeight() const;          // Get client area height
```

### Event Handling

#### Dialog Events
```mql5
virtual bool OnClickCaption(void);         // Caption clicked
virtual bool OnClickButtonClose(void);     // Close button clicked
```

#### Drag Operations
```mql5
virtual bool OnDialogDragStart(void);                         // Start drag
virtual bool OnDialogDragProcess(const int x, const int y);  // Process drag
virtual bool OnDialogDragEnd(void);                          // End drag
```

## CAppDialog Application Dialog

CAppDialog extends CDialog with additional application-specific functionality and simplified creation patterns.

## Usage Examples

### Basic Application Dialog

```mql5
#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>
#include <Controls\Edit.mqh>
#include <Controls\Label.mqh>

class CMyAppDialog : public CAppDialog
{
private:
    CLabel  m_label_title;
    CEdit   m_edit_input;
    CButton m_button_ok;
    CButton m_button_cancel;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    string GetInputText(void) { return m_edit_input.Text(); }

protected:
    virtual bool CreateControls(void);
    virtual void OnClickButtonOK(void);
    virtual void OnClickButtonCancel(void);
};

bool CMyAppDialog::Create(const long chart_id, const string name, const int subwin,
                         const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreateControls();
}

bool CMyAppDialog::CreateControls(void)
{
    int margin = 10;
    int row_height = 30;
    int y_pos = margin;

    // Title label
    if(!m_label_title.Create(m_chart_id, m_name+"Title", m_subwin,
                            margin, y_pos, ClientAreaWidth()-margin, y_pos+20))
        return false;
    m_label_title.Text("Enter Information:");
    m_label_title.Font("Arial Bold");
    m_label_title.FontSize(12);
    Add(m_label_title);

    y_pos += row_height;

    // Input field
    if(!m_edit_input.Create(m_chart_id, m_name+"Input", m_subwin,
                           margin, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_edit_input.Text("");
    Add(m_edit_input);

    y_pos += row_height + 10;

    // Buttons
    int button_width = 80;
    int button_spacing = 10;
    int buttons_start = (ClientAreaWidth() - (2 * button_width + button_spacing)) / 2;

    if(!m_button_ok.Create(m_chart_id, m_name+"OK", m_subwin,
                          buttons_start, y_pos, buttons_start+button_width, y_pos+25))
        return false;
    m_button_ok.Text("OK");
    m_button_ok.ColorBackground(clrGreen);
    Add(m_button_ok);

    if(!m_button_cancel.Create(m_chart_id, m_name+"Cancel", m_subwin,
                              buttons_start+button_width+button_spacing, y_pos,
                              buttons_start+2*button_width+button_spacing, y_pos+25))
        return false;
    m_button_cancel.Text("Cancel");
    m_button_cancel.ColorBackground(clrRed);
    Add(m_button_cancel);

    return true;
}

bool CMyAppDialog::OnEvent(const int id, const long &lparam,
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
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CMyAppDialog::OnClickButtonOK(void)
{
    string input_text = GetInputText();
    if(StringLen(input_text) == 0)
    {
        Alert("Please enter some text");
        return;
    }

    Print("User entered: ", input_text);
    Destroy();
}

void CMyAppDialog::OnClickButtonCancel(void)
{
    Print("Dialog cancelled");
    Destroy();
}
```

### Configuration Dialog

```mql5
class CConfigDialog : public CAppDialog
{
private:
    // Controls for different settings
    CLabel    m_label_trading;
    CEdit     m_edit_lot_size;
    CEdit     m_edit_stop_loss;
    CEdit     m_edit_take_profit;

    CLabel    m_label_display;
    CCheckBox m_check_show_info;
    CCheckBox m_check_auto_trade;

    CLabel    m_label_timeframe;
    CComboBox m_combo_timeframe;

    CButton   m_button_save;
    CButton   m_button_load;
    CButton   m_button_reset;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void LoadSettings(void);
    void SaveSettings(void);
    void ResetToDefaults(void);

protected:
    virtual bool CreateControls(void);
};

bool CConfigDialog::Create(const long chart_id, const string name, const int subwin,
                          const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreateControls();
}

bool CConfigDialog::CreateControls(void)
{
    int margin = 15;
    int label_height = 20;
    int control_height = 25;
    int section_spacing = 35;
    int y_pos = margin;

    // Trading Settings Section
    if(!m_label_trading.Create(m_chart_id, m_name+"LabelTrading", m_subwin,
                              margin, y_pos, ClientAreaWidth()-margin, y_pos+label_height))
        return false;
    m_label_trading.Text("Trading Settings:");
    m_label_trading.Font("Arial Bold");
    m_label_trading.Color(clrBlue);
    Add(m_label_trading);

    y_pos += 25;

    // Lot Size
    CLabel lot_label;
    if(!lot_label.Create(m_chart_id, m_name+"LotLabel", m_subwin,
                        margin+10, y_pos, margin+80, y_pos+label_height))
        return false;
    lot_label.Text("Lot Size:");
    Add(lot_label);

    if(!m_edit_lot_size.Create(m_chart_id, m_name+"LotSize", m_subwin,
                              margin+90, y_pos, margin+160, y_pos+control_height))
        return false;
    m_edit_lot_size.Text("0.1");
    Add(m_edit_lot_size);

    y_pos += 30;

    // Stop Loss
    CLabel sl_label;
    if(!sl_label.Create(m_chart_id, m_name+"SLLabel", m_subwin,
                       margin+10, y_pos, margin+80, y_pos+label_height))
        return false;
    sl_label.Text("Stop Loss:");
    Add(sl_label);

    if(!m_edit_stop_loss.Create(m_chart_id, m_name+"StopLoss", m_subwin,
                               margin+90, y_pos, margin+160, y_pos+control_height))
        return false;
    m_edit_stop_loss.Text("50");
    Add(m_edit_stop_loss);

    y_pos += section_spacing;

    // Display Settings Section
    if(!m_label_display.Create(m_chart_id, m_name+"LabelDisplay", m_subwin,
                              margin, y_pos, ClientAreaWidth()-margin, y_pos+label_height))
        return false;
    m_label_display.Text("Display Settings:");
    m_label_display.Font("Arial Bold");
    m_label_display.Color(clrBlue);
    Add(m_label_display);

    y_pos += 25;

    // Checkboxes
    if(!m_check_show_info.Create(m_chart_id, m_name+"ShowInfo", m_subwin,
                                margin+10, y_pos, ClientAreaWidth()-margin, y_pos+control_height))
        return false;
    m_check_show_info.Text("Show Trading Info");
    m_check_show_info.Checked(true);
    Add(m_check_show_info);

    y_pos += 30;

    if(!m_check_auto_trade.Create(m_chart_id, m_name+"AutoTrade", m_subwin,
                                 margin+10, y_pos, ClientAreaWidth()-margin, y_pos+control_height))
        return false;
    m_check_auto_trade.Text("Enable Auto Trading");
    m_check_auto_trade.Checked(false);
    Add(m_check_auto_trade);

    y_pos += section_spacing;

    // Timeframe Selection
    if(!m_label_timeframe.Create(m_chart_id, m_name+"LabelTF", m_subwin,
                                margin, y_pos, ClientAreaWidth()-margin, y_pos+label_height))
        return false;
    m_label_timeframe.Text("Default Timeframe:");
    m_label_timeframe.Font("Arial Bold");
    m_label_timeframe.Color(clrBlue);
    Add(m_label_timeframe);

    y_pos += 25;

    if(!m_combo_timeframe.Create(m_chart_id, m_name+"Timeframe", m_subwin,
                                margin+10, y_pos, ClientAreaWidth()-margin-10, y_pos+control_height))
        return false;

    // Populate timeframes
    m_combo_timeframe.AddItem("M1", PERIOD_M1);
    m_combo_timeframe.AddItem("M5", PERIOD_M5);
    m_combo_timeframe.AddItem("M15", PERIOD_M15);
    m_combo_timeframe.AddItem("M30", PERIOD_M30);
    m_combo_timeframe.AddItem("H1", PERIOD_H1);
    m_combo_timeframe.AddItem("H4", PERIOD_H4);
    m_combo_timeframe.AddItem("D1", PERIOD_D1);
    m_combo_timeframe.Select(4); // H1
    Add(m_combo_timeframe);

    y_pos += section_spacing;

    // Action Buttons
    int button_width = 70;
    int button_spacing = 10;
    int total_button_width = 3 * button_width + 2 * button_spacing;
    int button_start = (ClientAreaWidth() - total_button_width) / 2;

    if(!m_button_save.Create(m_chart_id, m_name+"Save", m_subwin,
                            button_start, y_pos, button_start+button_width, y_pos+30))
        return false;
    m_button_save.Text("Save");
    m_button_save.ColorBackground(clrGreen);
    Add(m_button_save);

    if(!m_button_load.Create(m_chart_id, m_name+"Load", m_subwin,
                            button_start+button_width+button_spacing, y_pos,
                            button_start+2*button_width+button_spacing, y_pos+30))
        return false;
    m_button_load.Text("Load");
    m_button_load.ColorBackground(clrBlue);
    Add(m_button_load);

    if(!m_button_reset.Create(m_chart_id, m_name+"Reset", m_subwin,
                             button_start+2*(button_width+button_spacing), y_pos,
                             button_start+3*button_width+2*button_spacing, y_pos+30))
        return false;
    m_button_reset.Text("Reset");
    m_button_reset.ColorBackground(clrOrange);
    Add(m_button_reset);

    return true;
}

bool CConfigDialog::OnEvent(const int id, const long &lparam,
                           const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == m_button_save.Name())
        {
            SaveSettings();
            return true;
        }
        if(sparam == m_button_load.Name())
        {
            LoadSettings();
            return true;
        }
        if(sparam == m_button_reset.Name())
        {
            ResetToDefaults();
            return true;
        }
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CConfigDialog::SaveSettings(void)
{
    Print("Saving settings:");
    Print("  Lot Size: ", m_edit_lot_size.Text());
    Print("  Stop Loss: ", m_edit_stop_loss.Text());
    Print("  Show Info: ", m_check_show_info.Checked());
    Print("  Auto Trade: ", m_check_auto_trade.Checked());
    Print("  Timeframe: ", m_combo_timeframe.Text());

    // Here you would save to file or global variables
    Alert("Settings saved successfully!");
}

void CConfigDialog::LoadSettings(void)
{
    // Here you would load from file or global variables
    Print("Loading settings...");

    // Example of loading default values
    m_edit_lot_size.Text("0.1");
    m_edit_stop_loss.Text("50");
    m_check_show_info.Checked(true);
    m_check_auto_trade.Checked(false);
    m_combo_timeframe.SelectByValue(PERIOD_H1);

    Alert("Settings loaded successfully!");
}

void CConfigDialog::ResetToDefaults(void)
{
    m_edit_lot_size.Text("0.01");
    m_edit_stop_loss.Text("100");
    m_check_show_info.Checked(true);
    m_check_auto_trade.Checked(false);
    m_combo_timeframe.Select(0);

    Alert("Settings reset to defaults!");
}
```

### Modal Dialog Helper

```mql5
class CModalDialog : public CAppDialog
{
private:
    bool m_modal_result;
    bool m_dialog_closed;

public:
    CModalDialog() : m_modal_result(false), m_dialog_closed(false) {}

    bool ShowModal(void);
    bool GetModalResult(void) const { return m_modal_result; }

protected:
    void CloseModal(const bool result);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);
};

bool CModalDialog::ShowModal(void)
{
    // Show the dialog
    Show();

    // Wait for dialog to close
    m_dialog_closed = false;
    while(!m_dialog_closed && IsVisible())
    {
        Sleep(10); // Small delay to prevent high CPU usage
        // Process events here if needed
    }

    return m_modal_result;
}

void CModalDialog::CloseModal(const bool result)
{
    m_modal_result = result;
    m_dialog_closed = true;
    Hide();
}

bool CModalDialog::OnEvent(const int id, const long &lparam,
                          const double &dparam, const string &sparam)
{
    // Handle ESC key to close dialog
    if(id == CHARTEVENT_KEYDOWN && lparam == 27) // ESC key
    {
        CloseModal(false);
        return true;
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}
```

## Dialog Lifecycle Management

### Dialog Factory
```mql5
class CDialogFactory
{
public:
    static CMyAppDialog* CreateInputDialog(const long chart_id, const string title);
    static CConfigDialog* CreateConfigDialog(const long chart_id);
    static bool ShowMessageBox(const string message, const string title = "Info");
};

CMyAppDialog* CDialogFactory::CreateInputDialog(const long chart_id, const string title)
{
    CMyAppDialog* dialog = new CMyAppDialog();
    if(dialog.Create(chart_id, "InputDialog", 0, 100, 100, 400, 250))
    {
        // Set dialog title if caption is available
        return dialog;
    }

    delete dialog;
    return NULL;
}

bool CDialogFactory::ShowMessageBox(const string message, const string title = "Info")
{
    // Simple message box implementation
    Alert(title + ": " + message);
    return true;
}
```

## Best Practices

1. **Dialog Design**
   - Use consistent spacing and margins
   - Group related controls logically
   - Provide clear navigation and actions

2. **Event Handling**
   - Always call parent event handlers
   - Handle dialog closure properly
   - Validate user input before processing

3. **Resource Management**
   - Properly destroy dialogs when done
   - Clean up child controls automatically
   - Handle memory allocation carefully

4. **User Experience**
   - Provide keyboard shortcuts
   - Support drag and drop where appropriate
   - Use meaningful titles and labels

## Common Use Cases

- **Configuration Dialogs** - Settings and preferences
- **Input Forms** - Data collection and entry
- **Property Editors** - Object and parameter editing
- **About Dialogs** - Application information
- **Progress Dialogs** - Operation status display
- **Confirmation Dialogs** - User action verification

## See Also

- [CWndContainer](../foundation/CWndContainer.md) - Container base class
- [Layout Management Tutorial](../tutorials/layout-management.md)
- [Event Handling Guide](../tutorials/event-handling.md)
- [Form Design Patterns](../tutorials/form-patterns.md)