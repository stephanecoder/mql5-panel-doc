# Expert Advisor Integration with MQL5 Panels

This guide covers the essential patterns for integrating MQL5 Control panels into Expert Advisors, including lifecycle management, event handling, and production-ready implementation techniques.

## Standard EA Integration Template

### Basic EA Structure

Every EA that uses Controls Library panels should follow this standard template:

```mql5
//+------------------------------------------------------------------+
//|                                                    PanelEA.mq5 |
//|                                  Copyright 2025, Your Name     |
//+------------------------------------------------------------------+
#property copyright "Copyright 2025, Your Name"
#property link      ""
#property version   "1.00"

#include <Controls\Dialog.mqh>

//--- Input parameters
input int    PanelX        = 20;      // Panel X coordinate
input int    PanelY        = 20;      // Panel Y coordinate
input color  PanelColor    = clrWhite; // Panel background color

//--- Global panel instance
CMyPanel Panel;

//+------------------------------------------------------------------+
//| Expert initialization function                                   |
//+------------------------------------------------------------------+
int OnInit()
{
    // Create and initialize the panel
    if(!Panel.Create(0, "MyPanel", 0, PanelX, PanelY, PanelX + 300, PanelY + 200))
    {
        Print("Failed to create panel");
        return INIT_FAILED;
    }

    // Run the panel (make it visible and active)
    Panel.Run();

    return INIT_SUCCEEDED;
}

//+------------------------------------------------------------------+
//| Expert deinitialization function                               |
//+------------------------------------------------------------------+
void OnDeinit(const int reason)
{
    // Proper cleanup - essential for preventing memory leaks
    Panel.Destroy(reason);
}

//+------------------------------------------------------------------+
//| Chart event function                                           |
//+------------------------------------------------------------------+
void OnChartEvent(const int id,
                  const long &lparam,
                  const double &dparam,
                  const string &sparam)
{
    // Route all chart events to the panel
    Panel.ChartEvent(id, lparam, dparam, sparam);
}
```

## Advanced EA Integration Patterns

### 1. Multiple Panel Management

```mql5
//--- Multiple panel instances
CMainPanel    MainPanel;
CSettingsPanel SettingsPanel;
CStatisticsPanel StatsPanel;

int OnInit()
{
    // Initialize panels in order
    if(!InitializePanels())
    {
        Print("Failed to initialize panels");
        return INIT_FAILED;
    }

    return INIT_SUCCEEDED;
}

bool InitializePanels()
{
    // Main panel
    if(!MainPanel.Create(0, "MainPanel", 0, 20, 20, 320, 250))
        return false;
    MainPanel.Run();

    // Settings panel (initially hidden)
    if(!SettingsPanel.Create(0, "SettingsPanel", 0, 340, 20, 640, 300))
        return false;
    SettingsPanel.Hide();

    // Statistics panel
    if(!StatsPanel.Create(0, "StatsPanel", 0, 20, 270, 320, 450))
        return false;
    StatsPanel.Run();

    return true;
}

void OnDeinit(const int reason)
{
    // Destroy panels in reverse order
    StatsPanel.Destroy(reason);
    SettingsPanel.Destroy(reason);
    MainPanel.Destroy(reason);
}

void OnChartEvent(const int id, const long &lparam,
                  const double &dparam, const string &sparam)
{
    // Route events to all active panels
    MainPanel.ChartEvent(id, lparam, dparam, sparam);
    SettingsPanel.ChartEvent(id, lparam, dparam, sparam);
    StatsPanel.ChartEvent(id, lparam, dparam, sparam);
}
```

### 2. Panel State Persistence

```mql5
//--- Panel state management
bool PanelVisible = true;
int  LastPanelX = 20;
int  LastPanelY = 20;

int OnInit()
{
    // Load saved panel settings
    LoadPanelSettings();

    if(!Panel.Create(0, "Panel", 0, LastPanelX, LastPanelY,
                    LastPanelX + 300, LastPanelY + 200))
        return INIT_FAILED;

    if(PanelVisible)
        Panel.Run();
    else
        Panel.Hide();

    return INIT_SUCCEEDED;
}

void OnDeinit(const int reason)
{
    // Save panel settings before destruction
    SavePanelSettings();
    Panel.Destroy(reason);
}

void LoadPanelSettings()
{
    PanelVisible = (bool)GlobalVariableGet("Panel_Visible");
    LastPanelX = (int)GlobalVariableGet("Panel_X");
    LastPanelY = (int)GlobalVariableGet("Panel_Y");

    // Set defaults if variables don't exist
    if(GetLastError() == ERR_GLOBAL_VARIABLE_NOT_FOUND)
    {
        PanelVisible = true;
        LastPanelX = 20;
        LastPanelY = 20;
    }
}

void SavePanelSettings()
{
    GlobalVariableSet("Panel_Visible", Panel.IsVisible());
    GlobalVariableSet("Panel_X", Panel.Left());
    GlobalVariableSet("Panel_Y", Panel.Top());
}
```

## EVENT_MAP System

The Controls Library provides a clean macro-based system for handling events:

### Basic EVENT_MAP Usage

```mql5
class CMyPanel : public CAppDialog
{
private:
    CButton m_button_buy;
    CButton m_button_sell;
    CButton m_button_close;

public:
    // Event map declaration
    EVENT_MAP_BEGIN(CMyPanel)
        ON_EVENT(ON_CLICK, m_button_buy, OnClickBuy)
        ON_EVENT(ON_CLICK, m_button_sell, OnClickSell)
        ON_EVENT(ON_CLICK, m_button_close, OnClickClose)
    EVENT_MAP_END(CAppDialog)

protected:
    bool OnClickBuy();
    bool OnClickSell();
    bool OnClickClose();
};

// Event handler implementations
bool CMyPanel::OnClickBuy()
{
    Print("Buy button clicked");
    // Implement buy logic
    return true;
}

bool CMyPanel::OnClickSell()
{
    Print("Sell button clicked");
    // Implement sell logic
    return true;
}

bool CMyPanel::OnClickClose()
{
    Print("Close button clicked");
    Hide(); // Hide the panel
    return true;
}
```

### Advanced Event Handling

```mql5
class CAdvancedPanel : public CAppDialog
{
private:
    CEdit     m_edit_lots;
    CComboBox m_combo_symbol;
    CListView m_list_orders;
    CButton   m_button_refresh;

public:
    EVENT_MAP_BEGIN(CAdvancedPanel)
        // Control events
        ON_EVENT(ON_CLICK, m_button_refresh, OnRefreshClick)
        ON_EVENT(ON_CHANGE, m_edit_lots, OnLotsChange)
        ON_EVENT(ON_CHANGE, m_combo_symbol, OnSymbolChange)
        ON_EVENT(ON_CLICK, m_list_orders, OnOrderSelect)

        // Custom events
        ON_EVENT(CHARTEVENT_CUSTOM, CONTROLS_SELF_MESSAGE, OnCustomEvent)
    EVENT_MAP_END(CAppDialog)

protected:
    bool OnRefreshClick();
    bool OnLotsChange();
    bool OnSymbolChange();
    bool OnOrderSelect();
    bool OnCustomEvent();

    // Custom event dispatcher
    void SendCustomEvent(const int event_id, const string data);
};

bool CAdvancedPanel::OnRefreshClick()
{
    // Refresh orders list
    RefreshOrdersList();

    // Send custom notification
    SendCustomEvent(1001, "Orders refreshed");
    return true;
}

void CAdvancedPanel::SendCustomEvent(const int event_id, const string data)
{
    EventChartCustom(ChartID(), CHARTEVENT_CUSTOM + event_id, 0, 0.0, data);
}
```

## Error Handling and Validation

### Robust Panel Creation

```mql5
class CRobustPanel : public CAppDialog
{
public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

private:
    bool ValidateParameters(const long chart_id, const string name,
                           const int x1, const int y1, const int x2, const int y2);
    bool CreateControlsWithValidation();
    void LogCreationError(const string control_name);
};

bool CRobustPanel::Create(const long chart_id, const string name, const int subwin,
                         const int x1, const int y1, const int x2, const int y2)
{
    // Validate input parameters
    if(!ValidateParameters(chart_id, name, x1, y1, x2, y2))
    {
        Print("Invalid panel parameters");
        return false;
    }

    // Create base dialog
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
    {
        Print("Failed to create base dialog");
        return false;
    }

    // Create controls with validation
    if(!CreateControlsWithValidation())
    {
        Print("Failed to create panel controls");
        Destroy(REASON_INITFAILED);
        return false;
    }

    return true;
}

bool CRobustPanel::ValidateParameters(const long chart_id, const string name,
                                     const int x1, const int y1, const int x2, const int y2)
{
    // Check chart ID
    if(chart_id < 0)
        return false;

    // Check name
    if(StringLen(name) == 0)
        return false;

    // Check dimensions
    if(x2 <= x1 || y2 <= y1)
        return false;

    // Check minimum size
    if((x2 - x1) < 100 || (y2 - y1) < 50)
        return false;

    return true;
}

bool CRobustPanel::CreateControlsWithValidation()
{
    // Create button with validation
    if(!m_button.Create(ChartID(), Name() + "Button", 0, 10, 30, 100, 60))
    {
        LogCreationError("Button");
        return false;
    }

    if(!Add(m_button))
    {
        LogCreationError("Button Add");
        return false;
    }

    // Validate control properties
    if(!m_button.Text("Click Me"))
    {
        LogCreationError("Button Text");
        return false;
    }

    return true;
}

void CRobustPanel::LogCreationError(const string control_name)
{
    Print("Failed to create control: ", control_name, ", Error: ", GetLastError());
}
```

## Performance Considerations

### Efficient Event Handling

```mql5
class COptimizedPanel : public CAppDialog
{
private:
    datetime m_last_update;
    bool     m_update_pending;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    void ScheduleUpdate();
    void ProcessPendingUpdates();
    bool ShouldProcessEvent(const int id);
};

bool COptimizedPanel::OnEvent(const int id, const long &lparam,
                             const double &dparam, const string &sparam)
{
    // Filter unnecessary events for performance
    if(!ShouldProcessEvent(id))
        return false;

    // Handle time-sensitive events immediately
    switch(id)
    {
        case CHARTEVENT_OBJECT_CLICK:
            return CAppDialog::OnEvent(id, lparam, dparam, sparam);

        case CHARTEVENT_CHART_CHANGE:
            // Schedule update instead of immediate processing
            ScheduleUpdate();
            return true;
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

bool COptimizedPanel::ShouldProcessEvent(const int id)
{
    // Skip mouse move events if panel is not focused
    if(id == CHARTEVENT_MOUSE_MOVE && !IsFocused())
        return false;

    // Throttle chart change events
    if(id == CHARTEVENT_CHART_CHANGE)
    {
        datetime current_time = TimeCurrent();
        if(current_time - m_last_update < 1) // 1 second throttle
            return false;
        m_last_update = current_time;
    }

    return true;
}

void COptimizedPanel::ScheduleUpdate()
{
    if(!m_update_pending)
    {
        m_update_pending = true;
        // Use timer or custom event for deferred updates
        EventSetTimer(1);
    }
}
```

## Integration with Trading Functions

### Trading Panel Example

```mql5
class CTradingPanel : public CAppDialog
{
private:
    CEdit     m_edit_volume;
    CEdit     m_edit_sl;
    CEdit     m_edit_tp;
    CButton   m_button_buy;
    CButton   m_button_sell;
    CLabel    m_label_status;

public:
    EVENT_MAP_BEGIN(CTradingPanel)
        ON_EVENT(ON_CLICK, m_button_buy, OnBuyClick)
        ON_EVENT(ON_CLICK, m_button_sell, OnSellClick)
        ON_EVENT(ON_CHANGE, m_edit_volume, OnVolumeChange)
    EVENT_MAP_END(CAppDialog)

protected:
    bool OnBuyClick();
    bool OnSellClick();
    bool OnVolumeChange();

    bool ValidateTradeInputs();
    bool ExecuteTrade(const ENUM_ORDER_TYPE order_type);
    void UpdateStatus(const string message, const color clr = clrBlack);
};

bool CTradingPanel::OnBuyClick()
{
    if(!ValidateTradeInputs())
    {
        UpdateStatus("Invalid trade parameters", clrRed);
        return false;
    }

    return ExecuteTrade(ORDER_TYPE_BUY);
}

bool CTradingPanel::ExecuteTrade(const ENUM_ORDER_TYPE order_type)
{
    MqlTradeRequest request = {};
    MqlTradeResult result = {};

    // Fill trade request
    request.action = TRADE_ACTION_DEAL;
    request.symbol = Symbol();
    request.volume = StringToDouble(m_edit_volume.Text());
    request.type = order_type;
    request.price = (order_type == ORDER_TYPE_BUY) ? SymbolInfoDouble(Symbol(), SYMBOL_ASK) :
                                                     SymbolInfoDouble(Symbol(), SYMBOL_BID);
    request.sl = StringToDouble(m_edit_sl.Text());
    request.tp = StringToDouble(m_edit_tp.Text());
    request.deviation = 3;
    request.magic = 12345;

    // Execute trade
    if(OrderSend(request, result))
    {
        UpdateStatus("Trade executed successfully", clrGreen);
        return true;
    }
    else
    {
        UpdateStatus("Trade failed: " + result.comment, clrRed);
        return false;
    }
}

bool CTradingPanel::ValidateTradeInputs()
{
    double volume = StringToDouble(m_edit_volume.Text());
    double sl = StringToDouble(m_edit_sl.Text());
    double tp = StringToDouble(m_edit_tp.Text());

    // Validate volume
    double min_volume = SymbolInfoDouble(Symbol(), SYMBOL_VOLUME_MIN);
    double max_volume = SymbolInfoDouble(Symbol(), SYMBOL_VOLUME_MAX);

    if(volume < min_volume || volume > max_volume)
        return false;

    // Additional validations...
    return true;
}

void CTradingPanel::UpdateStatus(const string message, const color clr = clrBlack)
{
    m_label_status.Text(message);
    m_label_status.Color(clr);
    ChartRedraw();
}
```

## Best Practices Summary

### 1. **Always Use Proper Lifecycle Management**
- Initialize panels in OnInit()
- Clean up in OnDeinit() with proper reason handling
- Route events through OnChartEvent()

### 2. **Implement Robust Error Handling**
- Validate all inputs and parameters
- Check return values of Create() methods
- Provide meaningful error messages

### 3. **Use EVENT_MAP for Clean Code**
- Prefer EVENT_MAP over manual OnEvent() handling
- Organize events logically by control type
- Handle custom events for complex workflows

### 4. **Consider Performance**
- Filter unnecessary events
- Throttle frequent updates
- Use deferred updates for non-critical operations

### 5. **Maintain State Properly**
- Save panel position and settings
- Restore state on restart
- Handle chart template changes

This integration guide provides the foundation for building professional-grade trading applications with MQL5 Controls Library panels integrated into Expert Advisors.

## See Also

- [Getting Started Tutorial](./getting-started.md) - Basic panel creation
- [Event Handling Patterns](./event-handling.md) - Advanced event management
- [Layout Management](./layout-management.md) - Panel organization techniques
- [Complex Controls Documentation](../complex-controls/) - Advanced control usage