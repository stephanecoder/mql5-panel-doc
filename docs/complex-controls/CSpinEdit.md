# CSpinEdit - Numeric Input Control

The `CSpinEdit` class creates numeric input controls with increment/decrement buttons for precise value selection within specified ranges.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndContainer
        └── CSpinEdit
```

## Overview

- **Header File**: `<Controls\SpinEdit.mqh>`
- **Purpose**: Numeric value input with step-based adjustment
- **Key Features**: Min/max ranges, increment/decrement buttons, value validation
- **Components**: Edit control, increment button, decrement button

## Core Methods

### Creation

#### Create()
Creates the spin edit control with specified parameters.

```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Component Creation
```mql5
virtual bool CreateEdit(void);      // Create edit field
virtual bool CreateInc(void);       // Create increment button
virtual bool CreateDec(void);       // Create decrement button
```

### Value Management

#### Value()
Gets or sets the current numeric value.

```mql5
long Value() const;                     // Get current value
virtual bool Value(const long value);  // Set current value
```

#### Range Control
```mql5
long MinValue() const;                      // Get minimum value
virtual bool MinValue(const long value);   // Set minimum value

long MaxValue() const;                      // Get maximum value
virtual bool MaxValue(const long value);   // Set maximum value
```

## Event Handling

### Button Events

#### OnClickInc()
Called when the increment button is clicked.

```mql5
virtual bool OnClickInc(void);
```

#### OnClickDec()
Called when the decrement button is clicked.

```mql5
virtual bool OnClickDec(void);
```

### Value Events

#### OnChangeValue()
Called when the value changes.

```mql5
virtual bool OnChangeValue(void);
```

## Usage Examples

### Basic Spin Edit Control

```mql5
#include <Controls\SpinEdit.mqh>
#include <Controls\Dialog.mqh>
#include <Controls\Label.mqh>
#include <Controls\Button.mqh>

class CSpinEditDialog : public CAppDialog
{
private:
    // Lot size control
    CLabel    m_label_lot_size;
    CSpinEdit m_spin_lot_size;

    // Stop loss control
    CLabel    m_label_stop_loss;
    CSpinEdit m_spin_stop_loss;

    // Take profit control
    CLabel    m_label_take_profit;
    CSpinEdit m_spin_take_profit;

    // Max trades control
    CLabel    m_label_max_trades;
    CSpinEdit m_spin_max_trades;

    // Action buttons
    CButton   m_button_apply;
    CButton   m_button_reset;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    // Getters for current values
    long GetLotSize(void) { return m_spin_lot_size.Value(); }
    long GetStopLoss(void) { return m_spin_stop_loss.Value(); }
    long GetTakeProfit(void) { return m_spin_take_profit.Value(); }
    long GetMaxTrades(void) { return m_spin_max_trades.Value(); }

private:
    bool CreateControls(void);
    void OnApplySettings(void);
    void OnResetSettings(void);
    void OnValueChange(CSpinEdit &spin_edit, const string parameter_name);
};

bool CSpinEditDialog::Create(const long chart_id, const string name, const int subwin,
                            const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreateControls();
}

bool CSpinEditDialog::CreateControls(void)
{
    int margin = 15;
    int row_height = 35;
    int label_width = 100;
    int spin_width = 120;
    int y_pos = margin;

    // Lot Size (0.01 to 10.00, representing hundredths)
    if(!m_label_lot_size.Create(m_chart_id, m_name+"LabelLotSize", m_subwin,
                               margin, y_pos, margin+label_width, y_pos+20))
        return false;
    m_label_lot_size.Text("Lot Size:");
    Add(m_label_lot_size);

    if(!m_spin_lot_size.Create(m_chart_id, m_name+"SpinLotSize", m_subwin,
                              margin+label_width+10, y_pos, margin+label_width+10+spin_width, y_pos+25))
        return false;
    m_spin_lot_size.MinValue(1);      // 0.01 lots
    m_spin_lot_size.MaxValue(1000);   // 10.00 lots
    m_spin_lot_size.Value(10);        // 0.10 lots default
    Add(m_spin_lot_size);

    y_pos += row_height;

    // Stop Loss (0 to 1000 pips)
    if(!m_label_stop_loss.Create(m_chart_id, m_name+"LabelStopLoss", m_subwin,
                                margin, y_pos, margin+label_width, y_pos+20))
        return false;
    m_label_stop_loss.Text("Stop Loss:");
    Add(m_label_stop_loss);

    if(!m_spin_stop_loss.Create(m_chart_id, m_name+"SpinStopLoss", m_subwin,
                               margin+label_width+10, y_pos, margin+label_width+10+spin_width, y_pos+25))
        return false;
    m_spin_stop_loss.MinValue(0);     // 0 pips (no stop loss)
    m_spin_stop_loss.MaxValue(1000);  // 1000 pips max
    m_spin_stop_loss.Value(50);       // 50 pips default
    Add(m_spin_stop_loss);

    y_pos += row_height;

    // Take Profit (0 to 1000 pips)
    if(!m_label_take_profit.Create(m_chart_id, m_name+"LabelTakeProfit", m_subwin,
                                  margin, y_pos, margin+label_width, y_pos+20))
        return false;
    m_label_take_profit.Text("Take Profit:");
    Add(m_label_take_profit);

    if(!m_spin_take_profit.Create(m_chart_id, m_name+"SpinTakeProfit", m_subwin,
                                 margin+label_width+10, y_pos, margin+label_width+10+spin_width, y_pos+25))
        return false;
    m_spin_take_profit.MinValue(0);     // 0 pips (no take profit)
    m_spin_take_profit.MaxValue(1000);  // 1000 pips max
    m_spin_take_profit.Value(100);      // 100 pips default
    Add(m_spin_take_profit);

    y_pos += row_height;

    // Max Trades (1 to 20)
    if(!m_label_max_trades.Create(m_chart_id, m_name+"LabelMaxTrades", m_subwin,
                                 margin, y_pos, margin+label_width, y_pos+20))
        return false;
    m_label_max_trades.Text("Max Trades:");
    Add(m_label_max_trades);

    if(!m_spin_max_trades.Create(m_chart_id, m_name+"SpinMaxTrades", m_subwin,
                                margin+label_width+10, y_pos, margin+label_width+10+spin_width, y_pos+25))
        return false;
    m_spin_max_trades.MinValue(1);   // Minimum 1 trade
    m_spin_max_trades.MaxValue(20);  // Maximum 20 trades
    m_spin_max_trades.Value(5);      // 5 trades default
    Add(m_spin_max_trades);

    y_pos += row_height + 15;

    // Action buttons
    int button_width = 80;
    int button_spacing = 20;
    int button_start = (ClientAreaWidth() - (2 * button_width + button_spacing)) / 2;

    if(!m_button_apply.Create(m_chart_id, m_name+"Apply", m_subwin,
                             button_start, y_pos, button_start+button_width, y_pos+30))
        return false;
    m_button_apply.Text("Apply");
    m_button_apply.ColorBackground(clrGreen);
    Add(m_button_apply);

    if(!m_button_reset.Create(m_chart_id, m_name+"Reset", m_subwin,
                             button_start+button_width+button_spacing, y_pos,
                             button_start+2*button_width+button_spacing, y_pos+30))
        return false;
    m_button_reset.Text("Reset");
    m_button_reset.ColorBackground(clrOrange);
    Add(m_button_reset);

    return true;
}

bool CSpinEditDialog::OnEvent(const int id, const long &lparam,
                             const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        // Handle button clicks
        if(sparam == m_button_apply.Name())
        {
            OnApplySettings();
            return true;
        }
        if(sparam == m_button_reset.Name())
        {
            OnResetSettings();
            return true;
        }
    }

    if(id == CHARTEVENT_CUSTOM)
    {
        // Handle spin edit value changes
        if(lparam == m_spin_lot_size.Id())
        {
            OnValueChange(m_spin_lot_size, "Lot Size");
            return true;
        }
        if(lparam == m_spin_stop_loss.Id())
        {
            OnValueChange(m_spin_stop_loss, "Stop Loss");
            return true;
        }
        if(lparam == m_spin_take_profit.Id())
        {
            OnValueChange(m_spin_take_profit, "Take Profit");
            return true;
        }
        if(lparam == m_spin_max_trades.Id())
        {
            OnValueChange(m_spin_max_trades, "Max Trades");
            return true;
        }
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CSpinEditDialog::OnApplySettings(void)
{
    Print("Applying trading settings:");
    Print("  Lot Size: ", DoubleToString(GetLotSize() / 100.0, 2), " lots");
    Print("  Stop Loss: ", GetStopLoss(), " pips");
    Print("  Take Profit: ", GetTakeProfit(), " pips");
    Print("  Max Trades: ", GetMaxTrades());

    Alert("Trading settings applied successfully!");
}

void CSpinEditDialog::OnResetSettings(void)
{
    m_spin_lot_size.Value(10);      // 0.10 lots
    m_spin_stop_loss.Value(50);     // 50 pips
    m_spin_take_profit.Value(100);  // 100 pips
    m_spin_max_trades.Value(5);     // 5 trades

    Alert("Settings reset to defaults!");
}

void CSpinEditDialog::OnValueChange(CSpinEdit &spin_edit, const string parameter_name)
{
    Print(parameter_name, " changed to: ", spin_edit.Value());

    // Validate relationships between parameters
    if(parameter_name == "Stop Loss" || parameter_name == "Take Profit")
    {
        long stop_loss = m_spin_stop_loss.Value();
        long take_profit = m_spin_take_profit.Value();

        if(stop_loss > 0 && take_profit > 0 && take_profit <= stop_loss)
        {
            Alert("Warning: Take Profit should be greater than Stop Loss");
        }
    }
}
```

### Advanced Spin Edit with Custom Step Sizes

```mql5
class CCustomSpinEdit : public CSpinEdit
{
private:
    long m_step_size;
    bool m_allow_decimal;
    int  m_decimal_places;

public:
    CCustomSpinEdit();
    void SetStepSize(const long step);
    void SetDecimalMode(const bool allow_decimal, const int decimal_places = 2);
    virtual bool OnClickInc(void);
    virtual bool OnClickDec(void);
    virtual bool OnChangeValue(void);

    double GetDecimalValue(void) const;
    void SetDecimalValue(const double value);

private:
    void ValidateValue(void);
    string FormatDisplay(const long value) const;
};

CCustomSpinEdit::CCustomSpinEdit(void) : m_step_size(1),
                                         m_allow_decimal(false),
                                         m_decimal_places(2)
{
}

void CCustomSpinEdit::SetStepSize(const long step)
{
    m_step_size = MathMax(1, step);
}

void CCustomSpinEdit::SetDecimalMode(const bool allow_decimal, const int decimal_places = 2)
{
    m_allow_decimal = allow_decimal;
    m_decimal_places = MathMax(0, decimal_places);
}

bool CCustomSpinEdit::OnClickInc(void)
{
    long current_value = Value();
    long new_value = current_value + m_step_size;

    if(new_value <= MaxValue())
    {
        Value(new_value);
        return true;
    }
    else
    {
        // Reached maximum, could provide feedback
        Print("Maximum value reached: ", MaxValue());
        return false;
    }
}

bool CCustomSpinEdit::OnClickDec(void)
{
    long current_value = Value();
    long new_value = current_value - m_step_size;

    if(new_value >= MinValue())
    {
        Value(new_value);
        return true;
    }
    else
    {
        // Reached minimum, could provide feedback
        Print("Minimum value reached: ", MinValue());
        return false;
    }
}

bool CCustomSpinEdit::OnChangeValue(void)
{
    ValidateValue();
    Print("Value changed to: ", FormatDisplay(Value()));
    return CSpinEdit::OnChangeValue();
}

void CCustomSpinEdit::ValidateValue(void)
{
    long current_value = Value();

    // Ensure value is within range
    if(current_value < MinValue())
        Value(MinValue());
    else if(current_value > MaxValue())
        Value(MaxValue());

    // Ensure value aligns with step size
    long remainder = (current_value - MinValue()) % m_step_size;
    if(remainder != 0)
    {
        long adjusted_value = current_value - remainder;
        if(remainder >= m_step_size / 2)
            adjusted_value += m_step_size;
        Value(adjusted_value);
    }
}

double CCustomSpinEdit::GetDecimalValue(void) const
{
    if(m_allow_decimal)
    {
        double divisor = MathPow(10, m_decimal_places);
        return Value() / divisor;
    }
    return (double)Value();
}

void CCustomSpinEdit::SetDecimalValue(const double value)
{
    if(m_allow_decimal)
    {
        double multiplier = MathPow(10, m_decimal_places);
        Value((long)(value * multiplier + 0.5)); // Round to nearest
    }
    else
    {
        Value((long)(value + 0.5)); // Round to nearest integer
    }
}

string CCustomSpinEdit::FormatDisplay(const long value) const
{
    if(m_allow_decimal)
    {
        double decimal_value = GetDecimalValue();
        return DoubleToString(decimal_value, m_decimal_places);
    }
    return IntegerToString(value);
}
```

### Spin Edit Configuration Panel

```mql5
class CSpinEditConfigPanel : public CWndContainer
{
private:
    struct SParameterConfig
    {
        string name;
        long   min_value;
        long   max_value;
        long   default_value;
        long   step_size;
        string unit;
    };

    SParameterConfig m_configs[10];
    CLabel           m_labels[10];
    CCustomSpinEdit  m_spin_edits[10];
    int              m_parameter_count;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    bool AddParameter(const string name, const long min_val, const long max_val,
                     const long default_val, const long step = 1, const string unit = "");
    long GetParameterValue(const string name);
    void SetParameterValue(const string name, const long value);
    void LoadConfiguration(void);
    void SaveConfiguration(void);

private:
    bool CreateParameterControls(void);
    int  FindParameterIndex(const string name);
    void OnParameterChange(const int index);
};

bool CSpinEditConfigPanel::Create(const long chart_id, const string name, const int subwin,
                                 const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    m_parameter_count = 0;

    // Add example parameters
    AddParameter("Lot Size", 1, 1000, 10, 1, "lots");        // 0.01 to 10.00 lots
    AddParameter("Stop Loss", 0, 500, 50, 5, "pips");        // 0 to 500 pips
    AddParameter("Take Profit", 0, 1000, 100, 10, "pips");   // 0 to 1000 pips
    AddParameter("Max Trades", 1, 50, 5, 1, "trades");       // 1 to 50 trades
    AddParameter("Risk %", 1, 20, 2, 1, "%");                // 1% to 20%

    return CreateParameterControls();
}

bool CSpinEditConfigPanel::AddParameter(const string name, const long min_val, const long max_val,
                                       const long default_val, const long step = 1, const string unit = "")
{
    if(m_parameter_count >= 10) return false;

    m_configs[m_parameter_count].name = name;
    m_configs[m_parameter_count].min_value = min_val;
    m_configs[m_parameter_count].max_value = max_val;
    m_configs[m_parameter_count].default_value = default_val;
    m_configs[m_parameter_count].step_size = step;
    m_configs[m_parameter_count].unit = unit;

    m_parameter_count++;
    return true;
}

bool CSpinEditConfigPanel::CreateParameterControls(void)
{
    int margin = 10;
    int row_height = 35;
    int label_width = 100;
    int spin_width = 100;

    for(int i = 0; i < m_parameter_count; i++)
    {
        int y_pos = margin + (i * row_height);

        // Create label
        string label_name = Name() + "Label" + IntegerToString(i);
        if(!m_labels[i].Create(Chart(), label_name, Subwin(),
                              margin, y_pos, margin+label_width, y_pos+20))
            return false;

        string label_text = m_configs[i].name + ":";
        m_labels[i].Text(label_text);
        Add(m_labels[i]);

        // Create spin edit
        string spin_name = Name() + "Spin" + IntegerToString(i);
        if(!m_spin_edits[i].Create(Chart(), spin_name, Subwin(),
                                  margin+label_width+10, y_pos,
                                  margin+label_width+10+spin_width, y_pos+25))
            return false;

        m_spin_edits[i].MinValue(m_configs[i].min_value);
        m_spin_edits[i].MaxValue(m_configs[i].max_value);
        m_spin_edits[i].Value(m_configs[i].default_value);
        m_spin_edits[i].SetStepSize(m_configs[i].step_size);

        // Set decimal mode for lot size
        if(m_configs[i].name == "Lot Size")
            m_spin_edits[i].SetDecimalMode(true, 2);

        Add(m_spin_edits[i]);
    }

    return true;
}

bool CSpinEditConfigPanel::OnEvent(const int id, const long &lparam,
                                  const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_CUSTOM)
    {
        for(int i = 0; i < m_parameter_count; i++)
        {
            if(lparam == m_spin_edits[i].Id())
            {
                OnParameterChange(i);
                return true;
            }
        }
    }

    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}

long CSpinEditConfigPanel::GetParameterValue(const string name)
{
    int index = FindParameterIndex(name);
    if(index >= 0)
        return m_spin_edits[index].Value();
    return 0;
}

void CSpinEditConfigPanel::SetParameterValue(const string name, const long value)
{
    int index = FindParameterIndex(name);
    if(index >= 0)
        m_spin_edits[index].Value(value);
}

int CSpinEditConfigPanel::FindParameterIndex(const string name)
{
    for(int i = 0; i < m_parameter_count; i++)
    {
        if(m_configs[i].name == name)
            return i;
    }
    return -1;
}

void CSpinEditConfigPanel::OnParameterChange(const int index)
{
    if(index < 0 || index >= m_parameter_count) return;

    long value = m_spin_edits[index].Value();
    string name = m_configs[index].name;
    string unit = m_configs[index].unit;

    if(name == "Lot Size")
    {
        double lot_value = m_spin_edits[index].GetDecimalValue();
        Print("Parameter changed: ", name, " = ", DoubleToString(lot_value, 2), " ", unit);
    }
    else
    {
        Print("Parameter changed: ", name, " = ", value, " ", unit);
    }

    // Validate parameter relationships
    if(name == "Stop Loss" || name == "Take Profit")
    {
        long stop_loss = GetParameterValue("Stop Loss");
        long take_profit = GetParameterValue("Take Profit");

        if(stop_loss > 0 && take_profit > 0 && take_profit <= stop_loss)
        {
            Print("Warning: Take Profit (", take_profit, ") should be greater than Stop Loss (", stop_loss, ")");
        }
    }
}

void CSpinEditConfigPanel::LoadConfiguration(void)
{
    // Load from file or global variables
    Print("Loading configuration from storage...");

    // Example: Load default values
    SetParameterValue("Lot Size", 10);      // 0.10 lots
    SetParameterValue("Stop Loss", 50);     // 50 pips
    SetParameterValue("Take Profit", 100);  // 100 pips
    SetParameterValue("Max Trades", 5);     // 5 trades
    SetParameterValue("Risk %", 2);         // 2%
}

void CSpinEditConfigPanel::SaveConfiguration(void)
{
    // Save to file or global variables
    Print("Saving configuration:");
    for(int i = 0; i < m_parameter_count; i++)
    {
        long value = m_spin_edits[i].Value();
        Print("  ", m_configs[i].name, ": ", value, " ", m_configs[i].unit);
    }
}
```

## Best Practices

1. **Value Ranges**
   - Set appropriate minimum and maximum values
   - Use meaningful step sizes for the parameter type
   - Validate value relationships between controls

2. **User Experience**
   - Provide clear labels and units
   - Use appropriate decimal precision
   - Handle edge cases (min/max reached)

3. **Data Validation**
   - Implement range checking
   - Validate parameter combinations
   - Provide user feedback for invalid values

4. **Visual Design**
   - Use consistent sizing for related controls
   - Align controls in a logical layout
   - Provide visual feedback for value changes

## Common Use Cases

- **Trading Parameters** - Lot sizes, stop losses, take profits
- **Configuration Settings** - Timeouts, limits, thresholds
- **Numeric Inputs** - Quantities, percentages, ratios
- **Time Settings** - Intervals, delays, durations
- **Size Controls** - Dimensions, positions, scales
- **Counter Controls** - Repetitions, iterations, counts

## See Also

- [CWndContainer](../foundation/CWndContainer.md) - Container base class
- [CEdit](../simple-controls/CEdit.md) - Text input control
- [CButton](../simple-controls/CButton.md) - Button control
- [Input Validation Tutorial](../tutorials/input-validation.md)
- [Form Design Patterns](../tutorials/form-patterns.md)