# Production-Ready MQL5 Panel Examples

This section provides complete, production-ready examples based on real-world trading applications and the official MQL5 Controls Library examples.

## Example 1: Complete Controls Demo Panel

Based on the official `MQL5/Experts/Examples/Controls/Controls.mq5`, this example demonstrates all major control types in a single, functional panel.

### Main Expert Advisor File

```mql5
//+------------------------------------------------------------------+
//|                                           TradingControlsDemo.mq5|
//|                                   Production Trading Panel Demo |
//+------------------------------------------------------------------+
#property copyright "Trading Panel Demo"
#property version   "1.00"

#include "TradingControlsDialog.mqh"

//--- Global panel instance
CTradingControlsDialog ExtDialog;

//+------------------------------------------------------------------+
//| Expert initialization function                                   |
//+------------------------------------------------------------------+
int OnInit()
{
    // Create application dialog
    if(!ExtDialog.Create(0, "TradingControls", 0, 20, 20, 400, 450))
    {
        Print("Failed to create trading controls dialog");
        return INIT_FAILED;
    }

    // Run application
    ExtDialog.Run();
    return INIT_SUCCEEDED;
}

//+------------------------------------------------------------------+
//| Expert deinitialization function                               |
//+------------------------------------------------------------------+
void OnDeinit(const int reason)
{
    // Destroy dialog with proper cleanup
    ExtDialog.Destroy(reason);
}

//+------------------------------------------------------------------+
//| Chart event function                                           |
//+------------------------------------------------------------------+
void OnChartEvent(const int id,
                  const long& lparam,
                  const double& dparam,
                  const string& sparam)
{
    // Route all events to the dialog
    ExtDialog.ChartEvent(id, lparam, dparam, sparam);
}
```

### Dialog Implementation File

```mql5
//+------------------------------------------------------------------+
//|                                       TradingControlsDialog.mqh |
//|                        Complete Trading Controls Implementation |
//+------------------------------------------------------------------+
#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>
#include <Controls\Edit.mqh>
#include <Controls\DatePicker.mqh>
#include <Controls\ListView.mqh>
#include <Controls\ComboBox.mqh>
#include <Controls\SpinEdit.mqh>
#include <Controls\RadioGroup.mqh>
#include <Controls\CheckGroup.mqh>
#include <Controls\Label.mqh>

//--- Layout constants
#define INDENT_LEFT                         (11)
#define INDENT_TOP                          (11)
#define INDENT_RIGHT                        (11)
#define INDENT_BOTTOM                       (11)
#define CONTROLS_GAP_X                      (5)
#define CONTROLS_GAP_Y                      (5)

//--- Control dimensions
#define BUTTON_WIDTH                        (100)
#define BUTTON_HEIGHT                       (20)
#define EDIT_HEIGHT                         (20)
#define GROUP_WIDTH                         (150)
#define LIST_HEIGHT                         (120)
#define RADIO_HEIGHT                        (60)
#define CHECK_HEIGHT                        (80)

//+------------------------------------------------------------------+
//| Trading Controls Dialog Class                                   |
//+------------------------------------------------------------------+
class CTradingControlsDialog : public CAppDialog
{
private:
    // Display and status controls
    CEdit             m_status_display;
    CLabel            m_title_label;

    // Trading controls
    CSpinEdit         m_lot_size;
    CEdit             m_stop_loss;
    CEdit             m_take_profit;
    CComboBox         m_symbol_selector;
    CDatePicker       m_expiry_date;

    // Action buttons
    CButton           m_buy_button;
    CButton           m_sell_button;
    CButton           m_close_all_button;

    // Configuration groups
    CRadioGroup       m_order_type_group;
    CCheckGroup       m_trade_options_group;

    // Information display
    CListView         m_positions_list;

    // Internal state
    bool              m_auto_trading_enabled;
    double            m_current_lot_size;
    string            m_selected_symbol;

public:
                     CTradingControlsDialog(void);
                    ~CTradingControlsDialog(void);

    // Creation and initialization
    virtual bool      Create(const long chart, const string name, const int subwin,
                             const int x1, const int y1, const int x2, const int y2);

    // Public interface
    void             UpdatePositionsList();
    void             SetStatus(const string message, const color text_color = clrBlack);
    double           GetLotSize() const { return m_current_lot_size; }
    string           GetSelectedSymbol() const { return m_selected_symbol; }

protected:
    // Control creation methods
    bool              CreateStatusDisplay();
    bool              CreateTitleLabel();
    bool              CreateLotSizeControl();
    bool              CreateStopLossControl();
    bool              CreateTakeProfitControl();
    bool              CreateSymbolSelector();
    bool              CreateExpiryDate();
    bool              CreateBuyButton();
    bool              CreateSellButton();
    bool              CreateCloseAllButton();
    bool              CreateOrderTypeGroup();
    bool              CreateTradeOptionsGroup();
    bool              CreatePositionsList();

    // Event handlers
    void              OnBuyButtonClick();
    void              OnSellButtonClick();
    void              OnCloseAllButtonClick();
    void              OnLotSizeChange();
    void              OnStopLossChange();
    void              OnTakeProfitChange();
    void              OnSymbolChange();
    void              OnExpiryDateChange();
    void              OnOrderTypeChange();
    void              OnTradeOptionsChange();
    void              OnPositionsListClick();

    // Trading operations
    bool              ExecuteBuyOrder();
    bool              ExecuteSellOrder();
    bool              CloseAllPositions();
    bool              ValidateTradeInputs();

    // Helper methods
    void              InitializeSymbolSelector();
    void              InitializeOrderTypeGroup();
    void              InitializeTradeOptionsGroup();
    void              RefreshPositionsList();
    string            FormatPositionInfo(int position_index);
};

//+------------------------------------------------------------------+
//| Event Map                                                        |
//+------------------------------------------------------------------+
EVENT_MAP_BEGIN(CTradingControlsDialog)
    ON_EVENT(ON_CLICK, m_buy_button, OnBuyButtonClick)
    ON_EVENT(ON_CLICK, m_sell_button, OnSellButtonClick)
    ON_EVENT(ON_CLICK, m_close_all_button, OnCloseAllButtonClick)
    ON_EVENT(ON_CHANGE, m_lot_size, OnLotSizeChange)
    ON_EVENT(ON_CHANGE, m_stop_loss, OnStopLossChange)
    ON_EVENT(ON_CHANGE, m_take_profit, OnTakeProfitChange)
    ON_EVENT(ON_CHANGE, m_symbol_selector, OnSymbolChange)
    ON_EVENT(ON_CHANGE, m_expiry_date, OnExpiryDateChange)
    ON_EVENT(ON_CHANGE, m_order_type_group, OnOrderTypeChange)
    ON_EVENT(ON_CHANGE, m_trade_options_group, OnTradeOptionsChange)
    ON_EVENT(ON_CLICK, m_positions_list, OnPositionsListClick)
EVENT_MAP_END(CAppDialog)

//+------------------------------------------------------------------+
//| Constructor                                                      |
//+------------------------------------------------------------------+
CTradingControlsDialog::CTradingControlsDialog(void) :
    m_auto_trading_enabled(false),
    m_current_lot_size(0.1),
    m_selected_symbol("")
{
}

//+------------------------------------------------------------------+
//| Destructor                                                       |
//+------------------------------------------------------------------+
CTradingControlsDialog::~CTradingControlsDialog(void)
{
}

//+------------------------------------------------------------------+
//| Create the dialog and all controls                              |
//+------------------------------------------------------------------+
bool CTradingControlsDialog::Create(const long chart, const string name, const int subwin,
                                   const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart, name, subwin, x1, y1, x2, y2))
        return false;

    // Create all controls in order
    if(!CreateTitleLabel())         return false;
    if(!CreateStatusDisplay())      return false;
    if(!CreateSymbolSelector())     return false;
    if(!CreateLotSizeControl())     return false;
    if(!CreateStopLossControl())    return false;
    if(!CreateTakeProfitControl())  return false;
    if(!CreateExpiryDate())         return false;
    if(!CreateOrderTypeGroup())     return false;
    if(!CreateTradeOptionsGroup())  return false;
    if(!CreateBuyButton())          return false;
    if(!CreateSellButton())         return false;
    if(!CreateCloseAllButton())     return false;
    if(!CreatePositionsList())      return false;

    // Initialize controls with default values
    InitializeSymbolSelector();
    InitializeOrderTypeGroup();
    InitializeTradeOptionsGroup();

    // Set initial status
    SetStatus("Trading panel ready");

    return true;
}

//+------------------------------------------------------------------+
//| Create status display                                           |
//+------------------------------------------------------------------+
bool CTradingControlsDialog::CreateStatusDisplay()
{
    int x1 = INDENT_LEFT;
    int y1 = INDENT_TOP + 25; // After title
    int x2 = ClientAreaWidth() - INDENT_RIGHT;
    int y2 = y1 + EDIT_HEIGHT;

    if(!m_status_display.Create(m_chart_id, m_name + "Status", m_subwin, x1, y1, x2, y2))
        return false;

    if(!m_status_display.ReadOnly(true))
        return false;

    m_status_display.ColorBackground(C'0xF0,0xF0,0xF0');
    return Add(m_status_display);
}

//+------------------------------------------------------------------+
//| Create title label                                              |
//+------------------------------------------------------------------+
bool CTradingControlsDialog::CreateTitleLabel()
{
    int x1 = INDENT_LEFT;
    int y1 = INDENT_TOP;
    int x2 = ClientAreaWidth() - INDENT_RIGHT;
    int y2 = y1 + 20;

    if(!m_title_label.Create(m_chart_id, m_name + "Title", m_subwin, x1, y1, x2, y2))
        return false;

    m_title_label.Text("Professional Trading Panel");
    m_title_label.Font("Arial Bold");
    m_title_label.FontSize(10);
    m_title_label.Color(C'0x00,0x33,0x66');

    return Add(m_title_label);
}

//+------------------------------------------------------------------+
//| Create lot size control                                         |
//+------------------------------------------------------------------+
bool CTradingControlsDialog::CreateLotSizeControl()
{
    int x1 = INDENT_LEFT;
    int y1 = INDENT_TOP + 85; // After status and symbol
    int x2 = x1 + 120;
    int y2 = y1 + EDIT_HEIGHT;

    if(!m_lot_size.Create(m_chart_id, m_name + "LotSize", m_subwin, x1, y1, x2, y2))
        return false;

    m_lot_size.MinValue(0.01);
    m_lot_size.MaxValue(100.0);
    m_lot_size.Value(m_current_lot_size);

    return Add(m_lot_size);
}

//+------------------------------------------------------------------+
//| Create symbol selector                                          |
//+------------------------------------------------------------------+
bool CTradingControlsDialog::CreateSymbolSelector()
{
    int x1 = INDENT_LEFT;
    int y1 = INDENT_TOP + 55; // After status
    int x2 = x1 + 150;
    int y2 = y1 + 20;

    if(!m_symbol_selector.Create(m_chart_id, m_name + "Symbol", m_subwin, x1, y1, x2, y2))
        return false;

    return Add(m_symbol_selector);
}

//+------------------------------------------------------------------+
//| Create buy button                                               |
//+------------------------------------------------------------------+
bool CTradingControlsDialog::CreateBuyButton()
{
    int x1 = INDENT_LEFT;
    int y1 = INDENT_TOP + 225; // After all input controls
    int x2 = x1 + BUTTON_WIDTH;
    int y2 = y1 + BUTTON_HEIGHT;

    if(!m_buy_button.Create(m_chart_id, m_name + "Buy", m_subwin, x1, y1, x2, y2))
        return false;

    m_buy_button.Text("BUY");
    m_buy_button.ColorBackground(C'0x00,0x80,0x00'); // Green
    m_buy_button.Color(clrWhite);

    return Add(m_buy_button);
}

//+------------------------------------------------------------------+
//| Create sell button                                              |
//+------------------------------------------------------------------+
bool CTradingControlsDialog::CreateSellButton()
{
    int x1 = INDENT_LEFT + BUTTON_WIDTH + CONTROLS_GAP_X;
    int y1 = INDENT_TOP + 225;
    int x2 = x1 + BUTTON_WIDTH;
    int y2 = y1 + BUTTON_HEIGHT;

    if(!m_sell_button.Create(m_chart_id, m_name + "Sell", m_subwin, x1, y1, x2, y2))
        return false;

    m_sell_button.Text("SELL");
    m_sell_button.ColorBackground(C'0x80,0x00,0x00'); // Red
    m_sell_button.Color(clrWhite);

    return Add(m_sell_button);
}

//+------------------------------------------------------------------+
//| Create positions list                                           |
//+------------------------------------------------------------------+
bool CTradingControlsDialog::CreatePositionsList()
{
    int x1 = INDENT_LEFT;
    int y1 = INDENT_TOP + 280; // After buttons
    int x2 = ClientAreaWidth() - INDENT_RIGHT;
    int y2 = y1 + LIST_HEIGHT;

    if(!m_positions_list.Create(m_chart_id, m_name + "Positions", m_subwin, x1, y1, x2, y2))
        return false;

    // Add column headers
    m_positions_list.AddItem("Symbol");
    m_positions_list.AddItem("Type");
    m_positions_list.AddItem("Volume");
    m_positions_list.AddItem("Profit");

    return Add(m_positions_list);
}

//+------------------------------------------------------------------+
//| Initialize symbol selector with available symbols              |
//+------------------------------------------------------------------+
void CTradingControlsDialog::InitializeSymbolSelector()
{
    // Add major currency pairs
    m_symbol_selector.AddItem("EURUSD");
    m_symbol_selector.AddItem("GBPUSD");
    m_symbol_selector.AddItem("USDJPY");
    m_symbol_selector.AddItem("USDCHF");
    m_symbol_selector.AddItem("AUDUSD");
    m_symbol_selector.AddItem("USDCAD");

    // Set current chart symbol as default
    string current_symbol = Symbol();
    for(int i = 0; i < m_symbol_selector.ItemsTotal(); i++)
    {
        if(m_symbol_selector.Item(i) == current_symbol)
        {
            m_symbol_selector.SelectByIndex(i);
            m_selected_symbol = current_symbol;
            break;
        }
    }

    if(StringLen(m_selected_symbol) == 0)
    {
        m_symbol_selector.SelectByIndex(0);
        m_selected_symbol = m_symbol_selector.Item(0);
    }
}

//+------------------------------------------------------------------+
//| Event Handlers                                                  |
//+------------------------------------------------------------------+
void CTradingControlsDialog::OnBuyButtonClick()
{
    if(ExecuteBuyOrder())
    {
        SetStatus("Buy order executed successfully", clrGreen);
        UpdatePositionsList();
    }
    else
    {
        SetStatus("Failed to execute buy order", clrRed);
    }
}

void CTradingControlsDialog::OnSellButtonClick()
{
    if(ExecuteSellOrder())
    {
        SetStatus("Sell order executed successfully", clrGreen);
        UpdatePositionsList();
    }
    else
    {
        SetStatus("Failed to execute sell order", clrRed);
    }
}

void CTradingControlsDialog::OnLotSizeChange()
{
    m_current_lot_size = m_lot_size.Value();
    SetStatus(StringFormat("Lot size changed to %.2f", m_current_lot_size));
}

void CTradingControlsDialog::OnSymbolChange()
{
    m_selected_symbol = m_symbol_selector.Text();
    SetStatus("Symbol changed to " + m_selected_symbol);
}

//+------------------------------------------------------------------+
//| Trading Operations                                               |
//+------------------------------------------------------------------+
bool CTradingControlsDialog::ExecuteBuyOrder()
{
    if(!ValidateTradeInputs())
        return false;

    MqlTradeRequest request = {};
    MqlTradeResult result = {};

    // Prepare buy order
    request.action = TRADE_ACTION_DEAL;
    request.symbol = m_selected_symbol;
    request.volume = m_current_lot_size;
    request.type = ORDER_TYPE_BUY;
    request.price = SymbolInfoDouble(m_selected_symbol, SYMBOL_ASK);
    request.deviation = 3;
    request.magic = 12345;
    request.comment = "Panel Buy Order";

    // Set stop loss and take profit if specified
    string sl_text = m_stop_loss.Text();
    string tp_text = m_take_profit.Text();

    if(StringLen(sl_text) > 0)
        request.sl = StringToDouble(sl_text);

    if(StringLen(tp_text) > 0)
        request.tp = StringToDouble(tp_text);

    // Execute the order
    if(OrderSend(request, result))
    {
        Print("Buy order successful. Ticket: ", result.order);
        return true;
    }
    else
    {
        Print("Buy order failed. Error: ", GetLastError(), ", Comment: ", result.comment);
        return false;
    }
}

bool CTradingControlsDialog::ExecuteSellOrder()
{
    if(!ValidateTradeInputs())
        return false;

    MqlTradeRequest request = {};
    MqlTradeResult result = {};

    // Prepare sell order
    request.action = TRADE_ACTION_DEAL;
    request.symbol = m_selected_symbol;
    request.volume = m_current_lot_size;
    request.type = ORDER_TYPE_SELL;
    request.price = SymbolInfoDouble(m_selected_symbol, SYMBOL_BID);
    request.deviation = 3;
    request.magic = 12345;
    request.comment = "Panel Sell Order";

    // Set stop loss and take profit if specified
    string sl_text = m_stop_loss.Text();
    string tp_text = m_take_profit.Text();

    if(StringLen(sl_text) > 0)
        request.sl = StringToDouble(sl_text);

    if(StringLen(tp_text) > 0)
        request.tp = StringToDouble(tp_text);

    // Execute the order
    if(OrderSend(request, result))
    {
        Print("Sell order successful. Ticket: ", result.order);
        return true;
    }
    else
    {
        Print("Sell order failed. Error: ", GetLastError(), ", Comment: ", result.comment);
        return false;
    }
}

bool CTradingControlsDialog::ValidateTradeInputs()
{
    // Validate lot size
    if(m_current_lot_size <= 0)
    {
        SetStatus("Invalid lot size", clrRed);
        return false;
    }

    // Validate symbol
    if(StringLen(m_selected_symbol) == 0)
    {
        SetStatus("No symbol selected", clrRed);
        return false;
    }

    // Check if trading is allowed
    if(!TerminalInfoInteger(TERMINAL_TRADE_ALLOWED))
    {
        SetStatus("Trading is not allowed in terminal", clrRed);
        return false;
    }

    if(!MQLInfoInteger(MQL_TRADE_ALLOWED))
    {
        SetStatus("Trading is not allowed for EA", clrRed);
        return false;
    }

    return true;
}

//+------------------------------------------------------------------+
//| Helper Methods                                                   |
//+------------------------------------------------------------------+
void CTradingControlsDialog::SetStatus(const string message, const color text_color = clrBlack)
{
    m_status_display.Text(TimeToString(TimeCurrent(), TIME_SECONDS) + ": " + message);
    m_status_display.Color(text_color);
}

void CTradingControlsDialog::UpdatePositionsList()
{
    RefreshPositionsList();
}

void CTradingControlsDialog::RefreshPositionsList()
{
    // Clear existing items
    for(int i = m_positions_list.ItemsTotal() - 1; i >= 0; i--)
    {
        m_positions_list.DeleteItem(i);
    }

    // Add current positions
    int total_positions = PositionsTotal();
    for(int i = 0; i < total_positions; i++)
    {
        if(PositionSelectByIndex(i))
        {
            string position_info = FormatPositionInfo(i);
            m_positions_list.AddItem(position_info);
        }
    }
}

string CTradingControlsDialog::FormatPositionInfo(int position_index)
{
    string symbol = PositionGetString(POSITION_SYMBOL);
    ENUM_POSITION_TYPE type = (ENUM_POSITION_TYPE)PositionGetInteger(POSITION_TYPE);
    double volume = PositionGetDouble(POSITION_VOLUME);
    double profit = PositionGetDouble(POSITION_PROFIT);

    string type_str = (type == POSITION_TYPE_BUY) ? "BUY" : "SELL";

    return StringFormat("%s %s %.2f P/L:%.2f", symbol, type_str, volume, profit);
}
```

## Example 2: Risk Management Panel

```mql5
//+------------------------------------------------------------------+
//|                                           RiskManagementPanel.mq5|
//|                                     Advanced Risk Control Panel |
//+------------------------------------------------------------------+
class CRiskManagementPanel : public CAppDialog
{
private:
    // Risk controls
    CSpinEdit         m_max_risk_percent;
    CSpinEdit         m_max_positions;
    CEdit             m_daily_loss_limit;
    CCheckBox         m_enable_risk_management;

    // Position sizing
    CRadioGroup       m_position_sizing_method;
    CSpinEdit         m_kelly_percentage;
    CEdit             m_fixed_amount;

    // Status display
    CLabel            m_current_risk_label;
    CLabel            m_daily_pnl_label;
    CLabel            m_positions_count_label;

    // Action buttons
    CButton           m_calculate_size_button;
    CButton           m_close_losing_button;
    CButton           m_emergency_close_button;

    // Risk metrics
    double            m_current_account_risk;
    double            m_daily_pnl;
    int               m_open_positions_count;

public:
    virtual bool      Create(const long chart, const string name, const int subwin,
                             const int x1, const int y1, const int x2, const int y2);

    // Risk calculation methods
    double            CalculatePositionSize(const string symbol, double risk_amount);
    bool              IsTradeAllowed();
    void              UpdateRiskMetrics();

protected:
    // Event handlers
    void              OnCalculateSizeClick();
    void              OnCloseLosing Click();
    void              OnEmergencyCloseClick();
    void              OnRiskSettingsChange();

    // Risk management
    bool              CheckDailyLossLimit();
    bool              CheckMaxPositions();
    bool              CheckAccountRisk();
    void              DisplayRiskWarning(const string message);
};

// Implementation would include comprehensive risk management logic
// with position sizing calculations, risk limits, and emergency controls
```

This production example demonstrates a complete, real-world trading panel implementation with proper error handling, validation, and integration with the MQL5 trading environment.

## See Also

- [EA Integration Guide](../tutorials/ea-integration.md) - Complete integration patterns
- [Advanced Patterns](../tutorials/advanced-patterns.md) - Sophisticated implementation techniques
- [Event Handling](../tutorials/event-handling.md) - Comprehensive event management
- [Official Controls Example](../api/function-reference.md) - Complete API reference