# Advanced MQL5 Panel Patterns

This guide covers sophisticated implementation patterns for MQL5 Controls Library, including dynamic control manipulation, runtime discovery, advanced event handling, and complex UI behaviors.

## Dynamic Control Manipulation

### Runtime Control Discovery and Modification

```mql5
class CDynamicPanel : public CAppDialog
{
private:
    bool m_transparency_mode;
    bool m_auto_hide_enabled;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    // Dynamic control management
    void ToggleControlVisibility(const string control_prefix);
    void RandomizeControlColors();
    void ApplyThemeToAllControls(const color base_color);
    void ShiftControlPositions(const int offset_x, const int offset_y);

    // Control discovery methods
    CWnd* FindControlByName(const string control_name);
    CArrayObj* GetControlsByType(const string type_name);
    void ProcessAllButtons(void (*callback)(CButton* button));

protected:
    void SetControlTransparency(CWnd* control, const bool transparent);
    color GenerateRandomColor();
    bool IsControlInClientArea(CWnd* control);
};

bool CDynamicPanel::Create(const long chart_id, const string name, const int subwin,
                          const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    // Create multiple controls for demonstration
    CreateDynamicControls();
    return true;
}

void CDynamicPanel::RandomizeControlColors()
{
    // Loop through all controls and apply random colors
    for(int i = 0; i < ControlsTotal(); i++)
    {
        CWnd* control = Control(i);
        if(!control)
            continue;

        // Cast to specific control types and apply colors
        CButton* button = dynamic_cast<CButton*>(control);
        if(button)
        {
            button->ColorBackground(GenerateRandomColor());
            continue;
        }

        CLabel* label = dynamic_cast<CLabel*>(control);
        if(label)
        {
            label->Color(GenerateRandomColor());
            continue;
        }

        CPanel* panel = dynamic_cast<CPanel*>(control);
        if(panel)
        {
            panel->ColorBackground(GenerateRandomColor());
            continue;
        }
    }

    ChartRedraw();
}

CWnd* CDynamicPanel::FindControlByName(const string control_name)
{
    for(int i = 0; i < ControlsTotal(); i++)
    {
        CWnd* control = Control(i);
        if(!control)
            continue;

        if(StringFind(control.Name(), control_name) >= 0)
            return control;
    }
    return NULL;
}

void CDynamicPanel::ToggleControlVisibility(const string control_prefix)
{
    for(int i = 0; i < ControlsTotal(); i++)
    {
        CWnd* control = Control(i);
        if(!control)
            continue;

        // Check if control name starts with prefix
        if(StringFind(control.Name(), control_prefix) == 0)
        {
            if(control.IsVisible())
                control.Hide();
            else
                control.Show();
        }
    }
}

void CDynamicPanel::ShiftControlPositions(const int offset_x, const int offset_y)
{
    for(int i = 0; i < ControlsTotal(); i++)
    {
        CWnd* control = Control(i);
        if(!control)
            continue;

        // Skip if control is outside client area after shift
        int new_x = control.Left() + offset_x;
        int new_y = control.Top() + offset_y;

        if(new_x >= 0 && new_y >= 0 &&
           new_x + control.Width() <= ClientAreaWidth() &&
           new_y + control.Height() <= ClientAreaHeight())
        {
            control.Move(new_x, new_y);
        }
    }
}

color CDynamicPanel::GenerateRandomColor()
{
    int red = MathRand() % 256;
    int green = MathRand() % 256;
    int blue = MathRand() % 256;
    return RGB(red, green, blue);
}

bool CDynamicPanel::IsControlInClientArea(CWnd* control)
{
    if(!control)
        return false;

    int client_width = ClientAreaWidth();
    int client_height = ClientAreaHeight();

    return (control.Left() >= 0 &&
            control.Top() >= 0 &&
            control.Right() <= client_width &&
            control.Bottom() <= client_height);
}
```

## Advanced Control Casting and Type Management

### Type-Safe Control Management System

```mql5
class CControlManager
{
private:
    CArrayObj m_buttons;
    CArrayObj m_labels;
    CArrayObj m_edits;
    CArrayObj m_panels;

public:
    // Control registration
    bool RegisterControl(CWnd* control);
    void UnregisterControl(CWnd* control);
    void ClearAll();

    // Type-specific access
    CButton* GetButton(const int index);
    CLabel* GetLabel(const int index);
    CEdit* GetEdit(const int index);
    CPanel* GetPanel(const int index);

    // Batch operations
    void SetAllButtonColors(const color bg_color, const color text_color);
    void SetAllLabelFonts(const string font_name, const int font_size);
    void EnableAllEdits(const bool enabled);
    void ShowAllPanels(const bool visible);

    // Search operations
    CButton* FindButtonByText(const string text);
    CEdit* FindEditByValue(const string value);
    CLabel* FindLabelContaining(const string partial_text);

protected:
    ENUM_CONTROL_TYPE GetControlType(CWnd* control);
    bool ValidateControlType(CWnd* control, ENUM_CONTROL_TYPE expected_type);
};

enum ENUM_CONTROL_TYPE
{
    CONTROL_TYPE_UNKNOWN,
    CONTROL_TYPE_BUTTON,
    CONTROL_TYPE_LABEL,
    CONTROL_TYPE_EDIT,
    CONTROL_TYPE_PANEL,
    CONTROL_TYPE_LISTVIEW,
    CONTROL_TYPE_COMBOBOX
};

bool CControlManager::RegisterControl(CWnd* control)
{
    if(!control)
        return false;

    ENUM_CONTROL_TYPE type = GetControlType(control);

    switch(type)
    {
        case CONTROL_TYPE_BUTTON:
            return m_buttons.Add(control);

        case CONTROL_TYPE_LABEL:
            return m_labels.Add(control);

        case CONTROL_TYPE_EDIT:
            return m_edits.Add(control);

        case CONTROL_TYPE_PANEL:
            return m_panels.Add(control);

        default:
            return false;
    }
}

ENUM_CONTROL_TYPE CControlManager::GetControlType(CWnd* control)
{
    if(!control)
        return CONTROL_TYPE_UNKNOWN;

    if(dynamic_cast<CButton*>(control))
        return CONTROL_TYPE_BUTTON;
    else if(dynamic_cast<CLabel*>(control))
        return CONTROL_TYPE_LABEL;
    else if(dynamic_cast<CEdit*>(control))
        return CONTROL_TYPE_EDIT;
    else if(dynamic_cast<CPanel*>(control))
        return CONTROL_TYPE_PANEL;
    else if(dynamic_cast<CListView*>(control))
        return CONTROL_TYPE_LISTVIEW;
    else if(dynamic_cast<CComboBox*>(control))
        return CONTROL_TYPE_COMBOBOX;

    return CONTROL_TYPE_UNKNOWN;
}

CButton* CControlManager::FindButtonByText(const string text)
{
    for(int i = 0; i < m_buttons.Total(); i++)
    {
        CButton* button = m_buttons.At(i);
        if(button && button.Text() == text)
            return button;
    }
    return NULL;
}

void CControlManager::SetAllButtonColors(const color bg_color, const color text_color)
{
    for(int i = 0; i < m_buttons.Total(); i++)
    {
        CButton* button = m_buttons.At(i);
        if(button)
        {
            button.ColorBackground(bg_color);
            button.Color(text_color);
        }
    }
}
```

## Bidirectional Communication Patterns

### Panel-to-Program Communication System

```mql5
// Communication interface
interface IPanelCommunication
{
    void OnParameterChanged(const string param_name, const string new_value);
    void OnActionRequested(const int action_id, const string action_data);
    string GetProgramParameter(const string param_name);
    bool SetProgramParameter(const string param_name, const string value);
};

class CTradingPanel : public CAppDialog
{
private:
    IPanelCommunication* m_communication_handler;
    CEdit* m_edit_lot_size;
    CEdit* m_edit_stop_loss;
    CEdit* m_edit_take_profit;
    CButton* m_button_buy;
    CButton* m_button_sell;
    CLabel* m_label_status;

    // Communication state
    bool m_auto_sync_enabled;
    datetime m_last_sync_time;

public:
    void SetCommunicationHandler(IPanelCommunication* handler);

    // Bidirectional sync methods
    void SyncFromProgram();
    void SyncToProgram();
    void EnableAutoSync(const bool enabled);

    EVENT_MAP_BEGIN(CTradingPanel)
        ON_EVENT(ON_CHANGE, m_edit_lot_size, OnLotSizeChange)
        ON_EVENT(ON_CHANGE, m_edit_stop_loss, OnStopLossChange)
        ON_EVENT(ON_CLICK, m_button_buy, OnBuyClick)
        ON_EVENT(ON_CLICK, m_button_sell, OnSellClick)
    EVENT_MAP_END(CAppDialog)

protected:
    bool OnLotSizeChange();
    bool OnStopLossChange();
    bool OnBuyClick();
    bool OnSellClick();

    void NotifyParameterChange(const string param_name, const string value);
    void RequestAction(const int action_id, const string data = "");
    void UpdateStatus(const string message, const color text_color = clrBlack);
};

void CTradingPanel::SetCommunicationHandler(IPanelCommunication* handler)
{
    m_communication_handler = handler;

    // Initial sync from program
    if(m_communication_handler)
        SyncFromProgram();
}

bool CTradingPanel::OnLotSizeChange()
{
    if(m_communication_handler)
    {
        string new_value = m_edit_lot_size.Text();
        NotifyParameterChange("LotSize", new_value);
    }
    return true;
}

void CTradingPanel::SyncFromProgram()
{
    if(!m_communication_handler)
        return;

    // Get current values from program
    string lot_size = m_communication_handler.GetProgramParameter("LotSize");
    string stop_loss = m_communication_handler.GetProgramParameter("StopLoss");
    string take_profit = m_communication_handler.GetProgramParameter("TakeProfit");

    // Update panel controls without triggering events
    m_edit_lot_size.Text(lot_size);
    m_edit_stop_loss.Text(stop_loss);
    m_edit_take_profit.Text(take_profit);
}

void CTradingPanel::SyncToProgram()
{
    if(!m_communication_handler)
        return;

    // Send current panel values to program
    m_communication_handler.SetProgramParameter("LotSize", m_edit_lot_size.Text());
    m_communication_handler.SetProgramParameter("StopLoss", m_edit_stop_loss.Text());
    m_communication_handler.SetProgramParameter("TakeProfit", m_edit_take_profit.Text());
}

void CTradingPanel::NotifyParameterChange(const string param_name, const string value)
{
    if(m_communication_handler)
    {
        m_communication_handler.OnParameterChanged(param_name, value);
        m_last_sync_time = TimeCurrent();
    }
}

bool CTradingPanel::OnBuyClick()
{
    RequestAction(1, "BUY_ORDER");
    UpdateStatus("Buy order requested...", clrBlue);
    return true;
}

void CTradingPanel::RequestAction(const int action_id, const string data)
{
    if(m_communication_handler)
        m_communication_handler.OnActionRequested(action_id, data);
}

// Expert Advisor implementation of communication interface
class CEACommHandler : public IPanelCommunication
{
private:
    double m_lot_size;
    double m_stop_loss;
    double m_take_profit;
    CTradingPanel* m_panel;

public:
    void SetPanel(CTradingPanel* panel) { m_panel = panel; }

    virtual void OnParameterChanged(const string param_name, const string new_value);
    virtual void OnActionRequested(const int action_id, const string action_data);
    virtual string GetProgramParameter(const string param_name);
    virtual bool SetProgramParameter(const string param_name, const string value);

    // EA-specific methods
    void UpdatePanelFromEA();
    bool ExecuteTradeAction(const int action_id, const string data);
};

void CEACommHandler::OnParameterChanged(const string param_name, const string new_value)
{
    if(param_name == "LotSize")
        m_lot_size = StringToDouble(new_value);
    else if(param_name == "StopLoss")
        m_stop_loss = StringToDouble(new_value);
    else if(param_name == "TakeProfit")
        m_take_profit = StringToDouble(new_value);

    Print("Parameter changed: ", param_name, " = ", new_value);
}

void CEACommHandler::OnActionRequested(const int action_id, const string action_data)
{
    switch(action_id)
    {
        case 1: // Buy order
            ExecuteTradeAction(ORDER_TYPE_BUY, action_data);
            break;

        case 2: // Sell order
            ExecuteTradeAction(ORDER_TYPE_SELL, action_data);
            break;

        case 3: // Close all
            CloseAllPositions();
            break;
    }
}

string CEACommHandler::GetProgramParameter(const string param_name)
{
    if(param_name == "LotSize")
        return DoubleToString(m_lot_size, 2);
    else if(param_name == "StopLoss")
        return DoubleToString(m_stop_loss, 0);
    else if(param_name == "TakeProfit")
        return DoubleToString(m_take_profit, 0);

    return "";
}
```

## Advanced Event Handling with Custom Drag Operations

### Enhanced Drag and Drop System

```mql5
class CDraggablePanel : public CAppDialog
{
private:
    bool m_is_dragging;
    bool m_is_resizing;
    bool m_transparency_enabled;
    int  m_drag_start_x;
    int  m_drag_start_y;
    int  m_resize_start_width;
    int  m_resize_start_height;

    // Drag constraints
    int m_min_width;
    int m_min_height;
    int m_max_width;
    int m_max_height;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    // Drag configuration
    void SetDragConstraints(const int min_w, const int min_h, const int max_w, const int max_h);
    void EnableTransparencyDuringDrag(const bool enabled);

    // Override drag methods
    virtual bool OnDialogDragStart();
    virtual bool OnDialogDragProcess();
    virtual bool OnDialogDragEnd();

    // Custom resize methods
    virtual bool OnResizeStart();
    virtual bool OnResizeProcess();
    virtual bool OnResizeEnd();

protected:
    bool IsInResizeZone(const int x, const int y);
    void ApplyDragTransparency();
    void RemoveDragTransparency();
    void ConstrainPosition(int& x, int& y);
    void ConstrainSize(int& width, int& height);
    void UpdateChildControlVisibility();
};

bool CDraggablePanel::OnDialogDragStart()
{
    m_is_dragging = true;
    m_drag_start_x = Left();
    m_drag_start_y = Top();

    if(m_transparency_enabled)
        ApplyDragTransparency();

    Print("Drag started at: ", m_drag_start_x, ", ", m_drag_start_y);
    return true;
}

bool CDraggablePanel::OnDialogDragProcess()
{
    if(!m_is_dragging)
        return false;

    // Get current mouse position relative to chart
    int mouse_x = (int)ChartGetInteger(ChartID(), CHART_EVENT_MOUSE_X);
    int mouse_y = (int)ChartGetInteger(ChartID(), CHART_EVENT_MOUSE_Y);

    // Calculate new position
    int new_x = mouse_x - Width() / 2;
    int new_y = mouse_y - Height() / 2;

    // Apply constraints
    ConstrainPosition(new_x, new_y);

    // Move panel
    Move(new_x, new_y);

    // Update child control visibility based on client area
    UpdateChildControlVisibility();

    return true;
}

bool CDraggablePanel::OnDialogDragEnd()
{
    m_is_dragging = false;

    if(m_transparency_enabled)
        RemoveDragTransparency();

    // Log final position
    Print("Drag ended at: ", Left(), ", ", Top());

    // Save position for persistence
    GlobalVariableSet(Name() + "_X", Left());
    GlobalVariableSet(Name() + "_Y", Top());

    return true;
}

void CDraggablePanel::ApplyDragTransparency()
{
    // Make panel semi-transparent during drag
    for(int i = 0; i < ControlsTotal(); i++)
    {
        CWnd* control = Control(i);
        if(!control)
            continue;

        // Apply transparency effect by changing colors
        CButton* button = dynamic_cast<CButton*>(control);
        if(button)
        {
            color current = button.ColorBackground();
            color transparent = RGB(
                (current & 0xFF) / 2,
                ((current >> 8) & 0xFF) / 2,
                ((current >> 16) & 0xFF) / 2
            );
            button.ColorBackground(transparent);
        }
    }
}

void CDraggablePanel::UpdateChildControlVisibility()
{
    // Hide controls that are outside client area during drag
    for(int i = 0; i < ControlsTotal(); i++)
    {
        CWnd* control = Control(i);
        if(!control)
            continue;

        bool should_be_visible = IsControlInClientArea(control);

        if(should_be_visible && !control.IsVisible())
            control.Show();
        else if(!should_be_visible && control.IsVisible())
            control.Hide();
    }
}

void CDraggablePanel::ConstrainPosition(int& x, int& y)
{
    // Keep panel within chart boundaries
    int chart_width = (int)ChartGetInteger(ChartID(), CHART_WIDTH_IN_PIXELS);
    int chart_height = (int)ChartGetInteger(ChartID(), CHART_HEIGHT_IN_PIXELS);

    if(x < 0)
        x = 0;
    if(y < 0)
        y = 0;
    if(x + Width() > chart_width)
        x = chart_width - Width();
    if(y + Height() > chart_height)
        y = chart_height - Height();
}

bool CDraggablePanel::IsInResizeZone(const int x, const int y)
{
    // Define resize zone as bottom-right corner
    int resize_zone_size = 15;

    return (x >= Right() - resize_zone_size && x <= Right() &&
            y >= Bottom() - resize_zone_size && y <= Bottom());
}
```

## Modular Panel Design with Include Files

### Modular Architecture Pattern

```mql5
//+------------------------------------------------------------------+
//|                                        PanelFramework.mqh       |
//+------------------------------------------------------------------+

// Base panel framework
#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>
#include <Controls\Edit.mqh>
#include <Controls\Label.mqh>

// Forward declarations
class CPanelModule;
class CModularPanel;

// Module interface
class CPanelModule
{
protected:
    CModularPanel* m_parent_panel;
    string m_module_name;
    bool m_is_initialized;

public:
    CPanelModule(const string name) : m_module_name(name), m_is_initialized(false) {}
    virtual ~CPanelModule() {}

    // Virtual interface
    virtual bool Initialize(CModularPanel* parent) = 0;
    virtual bool CreateControls() = 0;
    virtual bool OnEvent(const int id, const long& lparam,
                        const double& dparam, const string& sparam) = 0;
    virtual void Update() = 0;
    virtual void Cleanup() = 0;

    // Getters
    string ModuleName() const { return m_module_name; }
    bool IsInitialized() const { return m_is_initialized; }

protected:
    void SetInitialized(const bool init) { m_is_initialized = init; }
};

// Main modular panel
class CModularPanel : public CAppDialog
{
private:
    CArrayObj m_modules;
    int m_current_y_offset;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    // Module management
    bool AddModule(CPanelModule* module);
    bool RemoveModule(const string module_name);
    CPanelModule* GetModule(const string module_name);

    // Layout management
    int GetNextYOffset() { return m_current_y_offset; }
    void AdvanceYOffset(const int height) { m_current_y_offset += height + 5; }

    // Event routing
    virtual bool OnEvent(const int id, const long& lparam,
                        const double& dparam, const string& sparam);

protected:
    bool InitializeModules();
    void UpdateAllModules();
};

bool CModularPanel::AddModule(CPanelModule* module)
{
    if(!module)
        return false;

    // Initialize module
    if(!module.Initialize(this))
        return false;

    // Create module controls
    if(!module.CreateControls())
        return false;

    // Add to module list
    return m_modules.Add(module);
}

bool CModularPanel::OnEvent(const int id, const long& lparam,
                           const double& dparam, const string& sparam)
{
    // Route event to all modules first
    for(int i = 0; i < m_modules.Total(); i++)
    {
        CPanelModule* module = m_modules.At(i);
        if(module && module.OnEvent(id, lparam, dparam, sparam))
            return true; // Event handled by module
    }

    // Let base class handle if no module processed it
    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}
```

### Trading Module Example

```mql5
//+------------------------------------------------------------------+
//|                                        TradingModule.mqh        |
//+------------------------------------------------------------------+

#include "PanelFramework.mqh"

class CTradingModule : public CPanelModule
{
private:
    CEdit* m_edit_volume;
    CEdit* m_edit_sl;
    CEdit* m_edit_tp;
    CButton* m_button_buy;
    CButton* m_button_sell;
    CLabel* m_label_status;

public:
    CTradingModule() : CPanelModule("Trading") {}

    virtual bool Initialize(CModularPanel* parent);
    virtual bool CreateControls();
    virtual bool OnEvent(const int id, const long& lparam,
                        const double& dparam, const string& sparam);
    virtual void Update();
    virtual void Cleanup();

protected:
    bool OnBuyClick();
    bool OnSellClick();
    void UpdateTradingStatus();
};

bool CTradingModule::CreateControls()
{
    if(!m_parent_panel)
        return false;

    int x1 = 10;
    int y1 = m_parent_panel.GetNextYOffset();
    int width = 100;
    int height = 25;

    // Volume input
    m_edit_volume = new CEdit();
    if(!m_edit_volume.Create(m_parent_panel.ChartID(),
                            m_parent_panel.Name() + "_Volume", 0,
                            x1, y1, x1 + width, y1 + height))
        return false;

    m_edit_volume.Text("0.1");
    m_parent_panel.Add(m_edit_volume);

    y1 += height + 5;

    // Stop Loss input
    m_edit_sl = new CEdit();
    if(!m_edit_sl.Create(m_parent_panel.ChartID(),
                        m_parent_panel.Name() + "_SL", 0,
                        x1, y1, x1 + width, y1 + height))
        return false;

    m_parent_panel.Add(m_edit_sl);

    y1 += height + 5;

    // Buy button
    m_button_buy = new CButton();
    if(!m_button_buy.Create(m_parent_panel.ChartID(),
                           m_parent_panel.Name() + "_Buy", 0,
                           x1, y1, x1 + width/2 - 2, y1 + height))
        return false;

    m_button_buy.Text("BUY");
    m_button_buy.ColorBackground(clrGreen);
    m_parent_panel.Add(m_button_buy);

    // Sell button
    m_button_sell = new CButton();
    if(!m_button_sell.Create(m_parent_panel.ChartID(),
                            m_parent_panel.Name() + "_Sell", 0,
                            x1 + width/2 + 2, y1, x1 + width, y1 + height))
        return false;

    m_button_sell.Text("SELL");
    m_button_sell.ColorBackground(clrRed);
    m_parent_panel.Add(m_button_sell);

    // Update parent layout
    m_parent_panel.AdvanceYOffset((y1 - m_parent_panel.GetNextYOffset()) + height);

    return true;
}

bool CTradingModule::OnEvent(const int id, const long& lparam,
                            const double& dparam, const string& sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == m_button_buy.Name())
            return OnBuyClick();
        else if(sparam == m_button_sell.Name())
            return OnSellClick();
    }

    return false;
}

void CTradingModule::Update()
{
    UpdateTradingStatus();
}
```

This advanced patterns guide demonstrates sophisticated MQL5 Controls Library techniques that enable building complex, dynamic, and highly interactive trading interfaces. These patterns provide the foundation for creating professional-grade trading applications.

## See Also

- [EA Integration Guide](./ea-integration.md) - Panel lifecycle management
- [Customization Guide](./customization.md) - Styling and theming
- [Event Handling Patterns](./event-handling.md) - Basic event management
- [Complex Controls Documentation](../complex-controls/) - Advanced control usage