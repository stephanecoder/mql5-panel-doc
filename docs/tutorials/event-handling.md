# Event Handling in MQL5 Panel Controls

Event handling is the mechanism that allows your MQL5 panels to respond to user interactions like clicks, key presses, and other chart events. This tutorial covers comprehensive event handling techniques for building interactive trading interfaces.

## Understanding the Event System

### Event Flow
1. **Event occurs** (user clicks, key press, timer, etc.)
2. **Chart generates event** with event ID and parameters
3. **OnChartEvent() receives** the event in your Expert Advisor
4. **Event is delegated** to your panel's OnEvent() method
5. **Panel processes** the event and routes it to appropriate handlers

### Event Types
```mql5
// Chart object events
CHARTEVENT_OBJECT_CLICK      // Object clicked
CHARTEVENT_OBJECT_DRAG       // Object dragged
CHARTEVENT_OBJECT_ENDEDIT    // Text editing finished
CHARTEVENT_OBJECT_DELETE     // Object deleted
CHARTEVENT_OBJECT_CREATE     // Object created
CHARTEVENT_OBJECT_CHANGE     // Object properties changed

// Chart events
CHARTEVENT_CLICK             // Chart clicked
CHARTEVENT_KEYDOWN          // Key pressed
CHARTEVENT_MOUSE_MOVE       // Mouse moved
CHARTEVENT_CHART_CHANGE     // Chart properties changed

// Custom events
CHARTEVENT_CUSTOM           // Custom application events
```

## Basic Event Handling Structure

### Expert Advisor Setup
```mql5
#include <Controls\Dialog.mqh>

class CMyPanel : public CAppDialog
{
public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);
};

CMyPanel MyPanel;

// Essential: Delegate chart events to your panel
void OnChartEvent(const int id,
                  const long &lparam,
                  const double &dparam,
                  const string &sparam)
{
    MyPanel.OnEvent(id, lparam, dparam, sparam);
}
```

### Panel Event Handler
```mql5
bool CMyPanel::OnEvent(const int id, const long &lparam,
                      const double &dparam, const string &sparam)
{
    switch(id)
    {
        case CHARTEVENT_OBJECT_CLICK:
            return OnObjectClick(sparam);

        case CHARTEVENT_OBJECT_ENDEDIT:
            return OnObjectEndEdit(sparam);

        case CHARTEVENT_KEYDOWN:
            return OnKeyDown((int)lparam);

        case CHARTEVENT_CUSTOM:
            return OnCustomEvent(lparam, dparam, sparam);

        default:
            // Always call parent handler for unhandled events
            return CAppDialog::OnEvent(id, lparam, dparam, sparam);
    }
}
```

## Advanced Event Handling Patterns

### 1. Comprehensive Button Event Handler

```mql5
class CButtonEventHandler : public CAppDialog
{
private:
    CButton m_buttons[10];
    string  m_button_actions[10];
    int     m_button_count;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

protected:
    bool AddButton(const string text, const string action, const int x, const int y);
    bool OnObjectClick(const string object_name);
    void ExecuteAction(const string action);
    void ProvideVisualFeedback(CButton &button);
};

bool CButtonEventHandler::AddButton(const string text, const string action,
                                   const int x, const int y)
{
    if(m_button_count >= 10) return false;

    string button_name = m_name + "Btn" + IntegerToString(m_button_count);
    if(!m_buttons[m_button_count].Create(m_chart_id, button_name, m_subwin,
                                        x, y, x + 80, y + 30))
        return false;

    m_buttons[m_button_count].Text(text);
    m_button_actions[m_button_count] = action;
    Add(m_buttons[m_button_count]);

    m_button_count++;
    return true;
}

bool CButtonEventHandler::OnObjectClick(const string object_name)
{
    // Find which button was clicked
    for(int i = 0; i < m_button_count; i++)
    {
        if(object_name == m_buttons[i].Name())
        {
            ProvideVisualFeedback(m_buttons[i]);
            ExecuteAction(m_button_actions[i]);
            return true;
        }
    }
    return false;
}

void CButtonEventHandler::ExecuteAction(const string action)
{
    if(action == "buy")
    {
        Print("Executing BUY action");
        // Implement buy logic
    }
    else if(action == "sell")
    {
        Print("Executing SELL action");
        // Implement sell logic
    }
    else if(action == "close_all")
    {
        Print("Closing all positions");
        // Implement close all logic
    }
    else if(action == "refresh")
    {
        Print("Refreshing data");
        // Implement refresh logic
    }
}

void CButtonEventHandler::ProvideVisualFeedback(CButton &button)
{
    color original_color = button.ColorBackground();

    // Flash effect
    button.ColorBackground(clrYellow);
    ChartRedraw();
    Sleep(100);
    button.ColorBackground(original_color);
    ChartRedraw();
}
```

### 2. Text Input Event Handling

```mql5
class CTextInputHandler : public CAppDialog
{
private:
    CEdit   m_edit_symbol;
    CEdit   m_edit_lot_size;
    CEdit   m_edit_stop_loss;
    CLabel  m_label_validation;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

protected:
    bool OnObjectEndEdit(const string object_name);
    bool ValidateSymbol(const string symbol);
    bool ValidateLotSize(const string lot_text);
    bool ValidateStopLoss(const string sl_text);
    void ShowValidationMessage(const string message, const color text_color);
    void ClearValidation(void);
};

bool CTextInputHandler::OnObjectEndEdit(const string object_name)
{
    ClearValidation();

    if(object_name == m_edit_symbol.Name())
    {
        string symbol = m_edit_symbol.Text();
        if(!ValidateSymbol(symbol))
        {
            ShowValidationMessage("Invalid symbol: " + symbol, clrRed);
            return true;
        }
        ShowValidationMessage("Symbol validated", clrGreen);
    }
    else if(object_name == m_edit_lot_size.Name())
    {
        string lot_text = m_edit_lot_size.Text();
        if(!ValidateLotSize(lot_text))
        {
            ShowValidationMessage("Invalid lot size", clrRed);
            return true;
        }
    }
    else if(object_name == m_edit_stop_loss.Name())
    {
        string sl_text = m_edit_stop_loss.Text();
        if(!ValidateStopLoss(sl_text))
        {
            ShowValidationMessage("Invalid stop loss", clrRed);
            return true;
        }
    }

    return false;
}

bool CTextInputHandler::ValidateSymbol(const string symbol)
{
    if(StringLen(symbol) == 0) return false;
    if(StringLen(symbol) > 10) return false;

    // Check if symbol exists in market watch
    return SymbolSelect(symbol, true);
}

bool CTextInputHandler::ValidateLotSize(const string lot_text)
{
    double lot_size = StringToDouble(lot_text);
    if(lot_size <= 0) return false;
    if(lot_size > 100) return false; // Reasonable maximum

    return true;
}

bool CTextInputHandler::ValidateStopLoss(const string sl_text)
{
    int stop_loss = (int)StringToInteger(sl_text);
    if(stop_loss < 0) return false;
    if(stop_loss > 1000) return false; // Reasonable maximum

    return true;
}

void CTextInputHandler::ShowValidationMessage(const string message, const color text_color)
{
    m_label_validation.Text(message);
    m_label_validation.Color(text_color);
    m_label_validation.Show();
}

void CTextInputHandler::ClearValidation(void)
{
    m_label_validation.Hide();
}
```

### 3. Keyboard Event Handling

```mql5
class CKeyboardHandler : public CAppDialog
{
private:
    bool m_ctrl_pressed;
    bool m_shift_pressed;
    bool m_alt_pressed;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

protected:
    bool OnKeyDown(const int key_code);
    void UpdateModifierKeys(void);
    void ExecuteKeyboardShortcut(const int key_code);
};

bool CKeyboardHandler::OnKeyDown(const int key_code)
{
    UpdateModifierKeys();

    // Handle specific key combinations
    if(m_ctrl_pressed)
    {
        switch(key_code)
        {
            case 'S': // Ctrl+S
                Print("Save shortcut activated");
                // Implement save functionality
                return true;

            case 'O': // Ctrl+O
                Print("Open shortcut activated");
                // Implement open functionality
                return true;

            case 'N': // Ctrl+N
                Print("New shortcut activated");
                // Implement new functionality
                return true;
        }
    }

    // Function keys
    switch(key_code)
    {
        case VK_F1:
            Print("Help (F1) pressed");
            // Show help dialog
            return true;

        case VK_F5:
            Print("Refresh (F5) pressed");
            // Refresh data
            return true;

        case VK_ESCAPE:
            Print("Escape pressed");
            // Close dialog or cancel operation
            return true;

        case VK_RETURN:
            Print("Enter pressed");
            // Execute default action
            return true;
    }

    return false;
}

void CKeyboardHandler::UpdateModifierKeys(void)
{
    m_ctrl_pressed = (GetKeyState(VK_CONTROL) & 0x8000) != 0;
    m_shift_pressed = (GetKeyState(VK_SHIFT) & 0x8000) != 0;
    m_alt_pressed = (GetKeyState(VK_MENU) & 0x8000) != 0;
}
```

### 4. Custom Event System

```mql5
// Custom event types
#define EVENT_DATA_UPDATED     1001
#define EVENT_CONNECTION_LOST  1002
#define EVENT_ORDER_EXECUTED   1003
#define EVENT_TIMER_EXPIRED    1004

class CCustomEventHandler : public CAppDialog
{
private:
    datetime m_last_update;
    bool     m_auto_refresh;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void SendCustomEvent(const int event_type, const long param1 = 0,
                        const double param2 = 0, const string param3 = "");

protected:
    bool OnCustomEvent(const long lparam, const double dparam, const string sparam);
    void OnDataUpdated(const string data_type);
    void OnConnectionLost(void);
    void OnOrderExecuted(const long ticket, const double price);
    void OnTimerExpired(const int timer_id);
};

bool CCustomEventHandler::OnCustomEvent(const long lparam, const double dparam, const string sparam)
{
    int event_type = (int)lparam;

    switch(event_type)
    {
        case EVENT_DATA_UPDATED:
            OnDataUpdated(sparam);
            return true;

        case EVENT_CONNECTION_LOST:
            OnConnectionLost();
            return true;

        case EVENT_ORDER_EXECUTED:
            OnOrderExecuted((long)dparam, dparam);
            return true;

        case EVENT_TIMER_EXPIRED:
            OnTimerExpired((int)dparam);
            return true;

        default:
            Print("Unknown custom event: ", event_type);
            return false;
    }
}

void CCustomEventHandler::SendCustomEvent(const int event_type, const long param1 = 0,
                                         const double param2 = 0, const string param3 = "")
{
    EventChartCustom(m_chart_id, event_type, param1, param2, param3);
}

void CCustomEventHandler::OnDataUpdated(const string data_type)
{
    Print("Data updated: ", data_type);
    m_last_update = TimeCurrent();

    // Update relevant controls based on data type
    if(data_type == "prices")
    {
        // Update price displays
    }
    else if(data_type == "positions")
    {
        // Update position information
    }
}

void CCustomEventHandler::OnConnectionLost(void)
{
    Print("Connection lost - switching to offline mode");

    // Update UI to reflect offline state
    // Disable trading controls
    // Show reconnection status
}
```

### 5. Event Delegation and Routing

```mql5
class CEventRouter : public CAppDialog
{
private:
    struct SEventHandler
    {
        string control_name;
        string event_type;
        string handler_method;
    };

    SEventHandler m_handlers[100];
    int           m_handler_count;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void RegisterEventHandler(const string control_name, const string event_type,
                             const string handler_method);
    void UnregisterEventHandler(const string control_name, const string event_type);

protected:
    bool RouteEvent(const int event_id, const string object_name);
    void ExecuteHandler(const string handler_method, const string object_name);
};

void CEventRouter::RegisterEventHandler(const string control_name, const string event_type,
                                       const string handler_method)
{
    if(m_handler_count >= 100) return;

    m_handlers[m_handler_count].control_name = control_name;
    m_handlers[m_handler_count].event_type = event_type;
    m_handlers[m_handler_count].handler_method = handler_method;
    m_handler_count++;
}

bool CEventRouter::RouteEvent(const int event_id, const string object_name)
{
    string event_type = "";

    switch(event_id)
    {
        case CHARTEVENT_OBJECT_CLICK:
            event_type = "click";
            break;
        case CHARTEVENT_OBJECT_ENDEDIT:
            event_type = "endedit";
            break;
        case CHARTEVENT_OBJECT_DRAG:
            event_type = "drag";
            break;
    }

    // Find matching handler
    for(int i = 0; i < m_handler_count; i++)
    {
        if(m_handlers[i].control_name == object_name &&
           m_handlers[i].event_type == event_type)
        {
            ExecuteHandler(m_handlers[i].handler_method, object_name);
            return true;
        }
    }

    return false;
}

void CEventRouter::ExecuteHandler(const string handler_method, const string object_name)
{
    if(handler_method == "buy_action")
    {
        Print("Executing buy action for: ", object_name);
        // Implement buy logic
    }
    else if(handler_method == "sell_action")
    {
        Print("Executing sell action for: ", object_name);
        // Implement sell logic
    }
    else if(handler_method == "validate_input")
    {
        Print("Validating input for: ", object_name);
        // Implement validation logic
    }
    // Add more handlers as needed
}
```

## Event Handling Best Practices

### 1. Always Call Parent Handlers
```mql5
bool CMyPanel::OnEvent(const int id, const long &lparam,
                      const double &dparam, const string &sparam)
{
    // Handle your specific events first
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(HandleMyClicks(sparam))
            return true;
    }

    // Always delegate to parent for unhandled events
    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}
```

### 2. Event Handler Return Values
```mql5
// Return true if event was handled
if(sparam == m_my_button.Name())
{
    OnMyButtonClick();
    return true;  // Event handled, stop propagation
}

// Return false if event wasn't handled
return false;  // Let other handlers process this event
```

### 3. Efficient Event Filtering
```mql5
bool CMyPanel::OnEvent(const int id, const long &lparam,
                      const double &dparam, const string &sparam)
{
    // Quick filter - only process events we care about
    switch(id)
    {
        case CHARTEVENT_OBJECT_CLICK:
        case CHARTEVENT_OBJECT_ENDEDIT:
        case CHARTEVENT_KEYDOWN:
            // Process these events
            break;

        default:
            // Delegate everything else to parent
            return CAppDialog::OnEvent(id, lparam, dparam, sparam);
    }

    // Handle filtered events
    // ... event handling code ...
}
```

### 4. Event Validation
```mql5
bool CMyPanel::OnObjectClick(const string object_name)
{
    // Validate that the object belongs to our panel
    if(StringFind(object_name, m_name) != 0)
        return false;  // Not our object

    // Validate object state
    CWnd* control = FindControl(object_name);
    if(!control || !control.IsEnabled())
        return false;  // Invalid or disabled control

    // Process the valid event
    return ProcessControlClick(control);
}
```

### 5. Asynchronous Event Handling
```mql5
class CAsyncEventHandler : public CAppDialog
{
private:
    struct SPendingEvent
    {
        int event_id;
        string object_name;
        datetime timestamp;
    };

    SPendingEvent m_pending_events[50];
    int           m_pending_count;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);
    void ProcessPendingEvents(void);

protected:
    void QueueEvent(const int event_id, const string object_name);
    bool ProcessQueuedEvent(const SPendingEvent &event);
};

void CAsyncEventHandler::QueueEvent(const int event_id, const string object_name)
{
    if(m_pending_count >= 50) return;

    m_pending_events[m_pending_count].event_id = event_id;
    m_pending_events[m_pending_count].object_name = object_name;
    m_pending_events[m_pending_count].timestamp = TimeCurrent();
    m_pending_count++;
}

void CAsyncEventHandler::ProcessPendingEvents(void)
{
    for(int i = 0; i < m_pending_count; i++)
    {
        if(ProcessQueuedEvent(m_pending_events[i]))
        {
            // Remove processed event
            for(int j = i; j < m_pending_count - 1; j++)
            {
                m_pending_events[j] = m_pending_events[j + 1];
            }
            m_pending_count--;
            i--; // Adjust index after removal
        }
    }
}
```

## Common Event Handling Patterns

### 1. Command Pattern
```mql5
class ICommand
{
public:
    virtual void Execute() = 0;
    virtual bool CanExecute() = 0;
};

class CBuyCommand : public ICommand
{
public:
    virtual void Execute() { /* Buy logic */ }
    virtual bool CanExecute() { return IsTradeAllowed(); }
};
```

### 2. Observer Pattern
```mql5
class IEventObserver
{
public:
    virtual void OnEventNotified(const int event_type, const string data) = 0;
};

class CEventPublisher
{
private:
    IEventObserver* m_observers[10];
    int m_observer_count;

public:
    void Subscribe(IEventObserver* observer);
    void Unsubscribe(IEventObserver* observer);
    void NotifyObservers(const int event_type, const string data);
};
```

### 3. State Machine Pattern
```mql5
enum ENUM_PANEL_STATE
{
    STATE_IDLE,
    STATE_TRADING,
    STATE_ERROR,
    STATE_DISCONNECTED
};

class CStateMachine : public CAppDialog
{
private:
    ENUM_PANEL_STATE m_current_state;

public:
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

protected:
    void ChangeState(const ENUM_PANEL_STATE new_state);
    bool HandleEventForState(const int event_id, const string object_name);
};
```

## Debugging Event Handling

### 1. Event Logging
```mql5
void LogEvent(const int id, const long lparam, const double dparam, const string sparam)
{
    string event_name = "";
    switch(id)
    {
        case CHARTEVENT_OBJECT_CLICK: event_name = "OBJECT_CLICK"; break;
        case CHARTEVENT_OBJECT_ENDEDIT: event_name = "OBJECT_ENDEDIT"; break;
        case CHARTEVENT_KEYDOWN: event_name = "KEYDOWN"; break;
        default: event_name = "UNKNOWN_" + IntegerToString(id); break;
    }

    Print("EVENT: ", event_name, " | Object: ", sparam, " | LParam: ", lparam, " | DParam: ", dparam);
}
```

### 2. Event Handler Tracing
```mql5
#define TRACE_EVENTS
#ifdef TRACE_EVENTS
    #define TRACE_EVENT(msg) Print("TRACE: ", msg)
#else
    #define TRACE_EVENT(msg)
#endif

bool CMyPanel::OnEvent(const int id, const long &lparam,
                      const double &dparam, const string &sparam)
{
    TRACE_EVENT("OnEvent called - ID: " + IntegerToString(id));

    // Event handling code...

    TRACE_EVENT("OnEvent completed");
    return result;
}
```

Mastering event handling is essential for creating responsive and interactive MQL5 panels. Use these patterns and techniques to build robust trading interfaces that provide excellent user experience.

## See Also

- [Getting Started](./getting-started.md) - Basic panel creation
- [Layout Management](./layout-management.md) - Control positioning
- [Form Design Patterns](./form-patterns.md) - UI design principles