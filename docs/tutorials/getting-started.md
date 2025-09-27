# Getting Started with MQL5 Panel Controls

This tutorial will guide you through creating your first MQL5 panel using the Controls Library. You'll learn the basics of setting up a simple dialog with controls and handling user interactions.

## Prerequisites

- MetaTrader 5 platform installed
- Basic knowledge of MQL5 programming
- Understanding of object-oriented programming concepts

## Setting Up Your First Panel

### Step 1: Create the Basic Structure

First, let's create a simple Expert Advisor that will host our panel:

```mql5
//+------------------------------------------------------------------+
//|                                              MyFirstPanel.mq5 |
//|                                  Copyright 2025, Your Name     |
//+------------------------------------------------------------------+
#property copyright "Copyright 2025, Your Name"
#property link      ""
#property version   "1.00"

#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>
#include <Controls\Label.mqh>
#include <Controls\Edit.mqh>

// Panel class declaration
class CMyFirstPanel : public CAppDialog
{
private:
    CLabel  m_label_title;
    CEdit   m_edit_symbol;
    CButton m_button_buy;
    CButton m_button_sell;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

protected:
    bool CreateControls(void);
    void OnButtonBuy(void);
    void OnButtonSell(void);
};

// Global panel instance
CMyFirstPanel MyPanel;

//+------------------------------------------------------------------+
//| Expert initialization function                                   |
//+------------------------------------------------------------------+
int OnInit()
{
    // Create the panel
    if(!MyPanel.Create(0, "MyFirstPanel", 0, 50, 50, 300, 200))
    {
        Print("Failed to create panel");
        return INIT_FAILED;
    }

    MyPanel.Show();
    return INIT_SUCCEEDED;
}

//+------------------------------------------------------------------+
//| Expert deinitialization function                                |
//+------------------------------------------------------------------+
void OnDeinit(const int reason)
{
    MyPanel.Destroy();
}

//+------------------------------------------------------------------+
//| Expert tick function                                             |
//+------------------------------------------------------------------+
void OnTick()
{
    // Handle tick events if needed
}

//+------------------------------------------------------------------+
//| Chart event function                                             |
//+------------------------------------------------------------------+
void OnChartEvent(const int id,
                  const long &lparam,
                  const double &dparam,
                  const string &sparam)
{
    MyPanel.OnEvent(id, lparam, dparam, sparam);
}
```

### Step 2: Implement the Panel Class

Now let's implement the panel creation and control setup:

```mql5
//+------------------------------------------------------------------+
//| Create the panel and its controls                                |
//+------------------------------------------------------------------+
bool CMyFirstPanel::Create(const long chart_id, const string name, const int subwin,
                          const int x1, const int y1, const int x2, const int y2)
{
    // Create the main dialog
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    // Create all controls
    return CreateControls();
}

//+------------------------------------------------------------------+
//| Create individual controls                                       |
//+------------------------------------------------------------------+
bool CMyFirstPanel::CreateControls(void)
{
    int margin = 10;
    int row_height = 30;
    int y_pos = margin;

    // Title label
    if(!m_label_title.Create(m_chart_id, m_name+"Title", m_subwin,
                            margin, y_pos, ClientAreaWidth()-margin, y_pos+20))
        return false;

    m_label_title.Text("Trading Panel");
    m_label_title.Font("Arial Bold");
    m_label_title.FontSize(14);
    m_label_title.Color(clrBlue);
    Add(m_label_title);

    y_pos += row_height;

    // Symbol input
    if(!m_edit_symbol.Create(m_chart_id, m_name+"Symbol", m_subwin,
                            margin, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;

    m_edit_symbol.Text(Symbol());  // Default to current chart symbol
    Add(m_edit_symbol);

    y_pos += row_height + 10;

    // Buy button
    int button_width = (ClientAreaWidth() - 3 * margin) / 2;
    if(!m_button_buy.Create(m_chart_id, m_name+"Buy", m_subwin,
                           margin, y_pos, margin+button_width, y_pos+30))
        return false;

    m_button_buy.Text("BUY");
    m_button_buy.ColorBackground(clrGreen);
    m_button_buy.Color(clrWhite);
    Add(m_button_buy);

    // Sell button
    if(!m_button_sell.Create(m_chart_id, m_name+"Sell", m_subwin,
                            margin*2+button_width, y_pos, ClientAreaWidth()-margin, y_pos+30))
        return false;

    m_button_sell.Text("SELL");
    m_button_sell.ColorBackground(clrRed);
    m_button_sell.Color(clrWhite);
    Add(m_button_sell);

    return true;
}
```

### Step 3: Handle User Events

Implement event handling for user interactions:

```mql5
//+------------------------------------------------------------------+
//| Event handler                                                    |
//+------------------------------------------------------------------+
bool CMyFirstPanel::OnEvent(const int id, const long &lparam,
                           const double &dparam, const string &sparam)
{
    // Handle button clicks
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == m_button_buy.Name())
        {
            OnButtonBuy();
            return true;
        }

        if(sparam == m_button_sell.Name())
        {
            OnButtonSell();
            return true;
        }
    }

    // Call parent event handler
    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

//+------------------------------------------------------------------+
//| Buy button click handler                                         |
//+------------------------------------------------------------------+
void CMyFirstPanel::OnButtonBuy(void)
{
    string symbol = m_edit_symbol.Text();

    Print("Buy button clicked for symbol: ", symbol);

    // Here you would implement actual trading logic
    // For this example, we'll just show an alert
    Alert("Buy order would be placed for " + symbol);

    // Example of basic validation
    if(StringLen(symbol) == 0)
    {
        Alert("Please enter a symbol");
        return;
    }

    // Visual feedback
    m_button_buy.ColorBackground(clrDarkGreen);
    Sleep(100);  // Brief visual feedback
    m_button_buy.ColorBackground(clrGreen);
}

//+------------------------------------------------------------------+
//| Sell button click handler                                        |
//+------------------------------------------------------------------+
void CMyFirstPanel::OnButtonSell(void)
{
    string symbol = m_edit_symbol.Text();

    Print("Sell button clicked for symbol: ", symbol);

    // Here you would implement actual trading logic
    Alert("Sell order would be placed for " + symbol);

    if(StringLen(symbol) == 0)
    {
        Alert("Please enter a symbol");
        return;
    }

    // Visual feedback
    m_button_sell.ColorBackground(clrDarkRed);
    Sleep(100);
    m_button_sell.ColorBackground(clrRed);
}
```

## Understanding the Code Structure

### 1. Include Headers
```mql5
#include <Controls\Dialog.mqh>    // Main dialog functionality
#include <Controls\Button.mqh>    // Button controls
#include <Controls\Label.mqh>     // Text labels
#include <Controls\Edit.mqh>      // Text input fields
```

### 2. Class Inheritance
Your panel class inherits from `CAppDialog`, which provides:
- Window management
- Control container functionality
- Event handling framework
- Drawing and display management

### 3. Control Creation Pattern
Each control follows this pattern:
1. **Create** - Initialize the control with position and size
2. **Configure** - Set properties like text, colors, fonts
3. **Add** - Add the control to the parent container

### 4. Event Handling
The `OnEvent` method receives all chart events and:
- Filters for relevant events (like button clicks)
- Identifies which control generated the event
- Calls appropriate handler methods
- Returns `true` if the event was handled

## Running Your Panel

1. **Save the file** as `MyFirstPanel.mq5` in your MQL5 Experts folder
2. **Compile** the Expert Advisor in MetaEditor
3. **Attach** the EA to any chart
4. **See your panel** appear in the top-left corner of the chart

## Common Issues and Solutions

### Panel Doesn't Appear
- Check that `Create()` returns `true`
- Verify coordinates are within chart boundaries
- Ensure `Show()` is called after creation

### Controls Not Responding
- Verify `OnChartEvent` is implemented in your EA
- Check that event delegation to panel is working
- Ensure control names are unique

### Visual Issues
- Use `ClientAreaWidth()` and `ClientAreaHeight()` for proper sizing
- Add proper margins between controls
- Test with different chart sizes

## Next Steps

Now that you have a basic panel working, you can:

1. **Add more controls** - Try adding checkboxes, combo boxes, or lists
2. **Improve the layout** - Create more sophisticated arrangements
3. **Add real functionality** - Implement actual trading operations
4. **Style your panel** - Customize colors, fonts, and appearance
5. **Handle more events** - Add keyboard shortcuts, drag operations, etc.

## Key Takeaways

- **Always inherit from `CAppDialog`** for main panels
- **Create controls in a dedicated method** for better organization
- **Handle events properly** by delegating to your panel
- **Use the `Add()` method** to register controls with the container
- **Call parent event handlers** to maintain proper functionality

## Complete Example File

Here's the complete working example you can copy and use:

```mql5
//+------------------------------------------------------------------+
//|                                              MyFirstPanel.mq5 |
//+------------------------------------------------------------------+
#property copyright "Copyright 2025, Your Name"
#property version   "1.00"

#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>
#include <Controls\Label.mqh>
#include <Controls\Edit.mqh>

class CMyFirstPanel : public CAppDialog
{
private:
    CLabel  m_label_title;
    CEdit   m_edit_symbol;
    CButton m_button_buy;
    CButton m_button_sell;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

protected:
    bool CreateControls(void);
    void OnButtonBuy(void);
    void OnButtonSell(void);
};

CMyFirstPanel MyPanel;

int OnInit()
{
    if(!MyPanel.Create(0, "MyFirstPanel", 0, 50, 50, 300, 200))
        return INIT_FAILED;
    MyPanel.Show();
    return INIT_SUCCEEDED;
}

void OnDeinit(const int reason)
{
    MyPanel.Destroy();
}

void OnTick() { }

void OnChartEvent(const int id, const long &lparam, const double &dparam, const string &sparam)
{
    MyPanel.OnEvent(id, lparam, dparam, sparam);
}

bool CMyFirstPanel::Create(const long chart_id, const string name, const int subwin,
                          const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;
    return CreateControls();
}

bool CMyFirstPanel::CreateControls(void)
{
    int margin = 10;
    int row_height = 30;
    int y_pos = margin;

    // Title
    if(!m_label_title.Create(m_chart_id, m_name+"Title", m_subwin,
                            margin, y_pos, ClientAreaWidth()-margin, y_pos+20))
        return false;
    m_label_title.Text("Trading Panel");
    m_label_title.Font("Arial Bold");
    m_label_title.Color(clrBlue);
    Add(m_label_title);

    y_pos += row_height;

    // Symbol input
    if(!m_edit_symbol.Create(m_chart_id, m_name+"Symbol", m_subwin,
                            margin, y_pos, ClientAreaWidth()-margin, y_pos+25))
        return false;
    m_edit_symbol.Text(Symbol());
    Add(m_edit_symbol);

    y_pos += row_height + 10;

    // Buttons
    int button_width = (ClientAreaWidth() - 3 * margin) / 2;

    if(!m_button_buy.Create(m_chart_id, m_name+"Buy", m_subwin,
                           margin, y_pos, margin+button_width, y_pos+30))
        return false;
    m_button_buy.Text("BUY");
    m_button_buy.ColorBackground(clrGreen);
    Add(m_button_buy);

    if(!m_button_sell.Create(m_chart_id, m_name+"Sell", m_subwin,
                            margin*2+button_width, y_pos, ClientAreaWidth()-margin, y_pos+30))
        return false;
    m_button_sell.Text("SELL");
    m_button_sell.ColorBackground(clrRed);
    Add(m_button_sell);

    return true;
}

bool CMyFirstPanel::OnEvent(const int id, const long &lparam,
                           const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == m_button_buy.Name())
        {
            OnButtonBuy();
            return true;
        }
        if(sparam == m_button_sell.Name())
        {
            OnButtonSell();
            return true;
        }
    }
    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CMyFirstPanel::OnButtonBuy(void)
{
    string symbol = m_edit_symbol.Text();
    Alert("Buy order for " + symbol);
}

void CMyFirstPanel::OnButtonSell(void)
{
    string symbol = m_edit_symbol.Text();
    Alert("Sell order for " + symbol);
}
```

This example provides a solid foundation for building more complex MQL5 panels. Experiment with it, modify the controls, and gradually add more functionality as you become comfortable with the basics.

## See Also

- [Layout Management](./layout-management.md) - Learn to arrange controls properly
- [Event Handling](./event-handling.md) - Master user interaction handling
- [CAppDialog Reference](../complex-controls/Dialog.md) - Complete dialog documentation