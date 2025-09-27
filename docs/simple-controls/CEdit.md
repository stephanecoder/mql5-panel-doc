# CEdit - Text Input Control

The `CEdit` class creates text input controls based on the chart object "Edit" for collecting user text input in MQL5 applications.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndObj
        └── CEdit
```

## Overview

- **Header File**: `<Controls\Edit.mqh>`
- **Base Object**: Edit chart object
- **Purpose**: Text input and editing
- **Key Features**: Text editing, read-only mode, text alignment, validation

## Core Methods

### Creation

#### Create()
Creates the edit control with specified parameters.

```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

### Text Properties

#### ReadOnly()
Controls whether the text can be edited.

```mql5
bool ReadOnly() const;                      // Get read-only state
virtual bool ReadOnly(const bool flag);    // Set read-only state
```

#### TextAlign()
Sets the text alignment within the control.

```mql5
uint TextAlign() const;                     // Get text alignment
virtual bool TextAlign(const uint align);  // Set text alignment
```

**Alignment Constants:**
- `ALIGN_LEFT` - Left alignment
- `ALIGN_CENTER` - Center alignment
- `ALIGN_RIGHT` - Right alignment

## Event Handling

### Text Editing Events

#### OnObjectEndEdit()
Called when the user finishes editing the text.

```mql5
virtual bool OnObjectEndEdit(void);
```

### Property Change Events

#### OnSetText()
Called when the text content changes.

```mql5
virtual bool OnSetText(void);
```

#### OnSetColor()
Called when the text color changes.

```mql5
virtual bool OnSetColor(void);
```

#### OnSetFont()
Called when the font changes.

```mql5
virtual bool OnSetFont(void);
```

## Usage Examples

### Basic Text Input Dialog

```mql5
#include <Controls\Edit.mqh>
#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>
#include <Controls\Label.mqh>

class CInputDialog : public CAppDialog
{
private:
    CLabel  m_label_name;
    CEdit   m_edit_name;
    CLabel  m_label_email;
    CEdit   m_edit_email;
    CLabel  m_label_notes;
    CEdit   m_edit_notes;
    CButton m_button_ok;
    CButton m_button_cancel;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    string GetName(void) { return m_edit_name.Text(); }
    string GetEmail(void) { return m_edit_email.Text(); }
    string GetNotes(void) { return m_edit_notes.Text(); }

private:
    bool CreateControls(void);
    void OnClickOK(void);
    void OnClickCancel(void);
    bool ValidateInput(void);
};

bool CInputDialog::Create(const long chart_id, const string name, const int subwin,
                         const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreateControls();
}

bool CInputDialog::CreateControls(void)
{
    int y_pos = 10;
    int row_height = 30;
    int label_width = 80;
    int edit_width = 200;

    // Name field
    if(!m_label_name.Create(m_chart_id, m_name+"LabelName", m_subwin,
                           10, y_pos, 10 + label_width, y_pos + 20))
        return false;
    m_label_name.Text("Name:");
    Add(m_label_name);

    if(!m_edit_name.Create(m_chart_id, m_name+"EditName", m_subwin,
                          100, y_pos, 100 + edit_width, y_pos + 25))
        return false;
    m_edit_name.TextAlign(ALIGN_LEFT);
    Add(m_edit_name);

    y_pos += row_height;

    // Email field
    if(!m_label_email.Create(m_chart_id, m_name+"LabelEmail", m_subwin,
                            10, y_pos, 10 + label_width, y_pos + 20))
        return false;
    m_label_email.Text("Email:");
    Add(m_label_email);

    if(!m_edit_email.Create(m_chart_id, m_name+"EditEmail", m_subwin,
                           100, y_pos, 100 + edit_width, y_pos + 25))
        return false;
    Add(m_edit_email);

    y_pos += row_height;

    // Notes field (larger)
    if(!m_label_notes.Create(m_chart_id, m_name+"LabelNotes", m_subwin,
                            10, y_pos, 10 + label_width, y_pos + 20))
        return false;
    m_label_notes.Text("Notes:");
    Add(m_label_notes);

    if(!m_edit_notes.Create(m_chart_id, m_name+"EditNotes", m_subwin,
                           100, y_pos, 100 + edit_width, y_pos + 60))
        return false;
    Add(m_edit_notes);

    y_pos += 70;

    // Buttons
    if(!m_button_ok.Create(m_chart_id, m_name+"ButtonOK", m_subwin,
                          100, y_pos, 170, y_pos + 30))
        return false;
    m_button_ok.Text("OK");
    m_button_ok.ColorBackground(clrGreen);
    Add(m_button_ok);

    if(!m_button_cancel.Create(m_chart_id, m_name+"ButtonCancel", m_subwin,
                              180, y_pos, 250, y_pos + 30))
        return false;
    m_button_cancel.Text("Cancel");
    m_button_cancel.ColorBackground(clrRed);
    Add(m_button_cancel);

    return true;
}

bool CInputDialog::OnEvent(const int id, const long &lparam,
                          const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == m_button_ok.Name())
        {
            OnClickOK();
            return true;
        }
        if(sparam == m_button_cancel.Name())
        {
            OnClickCancel();
            return true;
        }
    }

    if(id == CHARTEVENT_OBJECT_ENDEDIT)
    {
        // Handle end of text editing
        if(sparam == m_edit_name.Name() ||
           sparam == m_edit_email.Name() ||
           sparam == m_edit_notes.Name())
        {
            Print("Text edited in: ", sparam);
            return true;
        }
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CInputDialog::OnClickOK(void)
{
    if(ValidateInput())
    {
        Print("Form submitted:");
        Print("Name: ", GetName());
        Print("Email: ", GetEmail());
        Print("Notes: ", GetNotes());
        Destroy();
    }
}

void CInputDialog::OnClickCancel(void)
{
    Destroy();
}

bool CInputDialog::ValidateInput(void)
{
    // Validate name
    if(StringLen(m_edit_name.Text()) == 0)
    {
        Alert("Please enter a name");
        return false;
    }

    // Validate email format
    string email = m_edit_email.Text();
    if(StringLen(email) > 0)
    {
        if(StringFind(email, "@") == -1 || StringFind(email, ".") == -1)
        {
            Alert("Please enter a valid email address");
            return false;
        }
    }

    return true;
}
```

### Numeric Input with Validation

```mql5
class CNumericEdit : public CEdit
{
private:
    double m_min_value;
    double m_max_value;
    int    m_decimal_places;
    bool   m_allow_negative;

public:
    CNumericEdit();
    void SetRange(const double min_val, const double max_val);
    void SetDecimalPlaces(const int places);
    void SetAllowNegative(const bool allow);
    virtual bool OnObjectEndEdit(void);
    virtual bool OnSetText(void);

    double GetNumericValue(void);
    bool SetNumericValue(const double value);
    bool IsValidNumber(const string text);
};

CNumericEdit::CNumericEdit(void) : m_min_value(-DBL_MAX),
                                   m_max_value(DBL_MAX),
                                   m_decimal_places(2),
                                   m_allow_negative(true)
{
}

void CNumericEdit::SetRange(const double min_val, const double max_val)
{
    m_min_value = min_val;
    m_max_value = max_val;
}

void CNumericEdit::SetDecimalPlaces(const int places)
{
    m_decimal_places = MathMax(0, places);
}

void CNumericEdit::SetAllowNegative(const bool allow)
{
    m_allow_negative = allow;
}

bool CNumericEdit::OnObjectEndEdit(void)
{
    string text = Text();

    if(!IsValidNumber(text))
    {
        // Restore previous valid value
        double current_value = GetNumericValue();
        SetNumericValue(current_value);
        Alert("Invalid number format");
        return false;
    }

    double value = StringToDouble(text);

    // Check range
    if(value < m_min_value || value > m_max_value)
    {
        Alert("Value must be between " + DoubleToString(m_min_value) +
              " and " + DoubleToString(m_max_value));
        SetNumericValue(MathMax(m_min_value, MathMin(m_max_value, value)));
        return false;
    }

    // Format to correct decimal places
    SetNumericValue(value);

    return CEdit::OnObjectEndEdit();
}

bool CNumericEdit::IsValidNumber(const string text)
{
    if(StringLen(text) == 0) return false;

    string clean_text = text;

    // Check for negative sign
    if(StringGetCharacter(clean_text, 0) == '-')
    {
        if(!m_allow_negative) return false;
        clean_text = StringSubstr(clean_text, 1);
    }

    // Check for valid numeric characters
    int decimal_count = 0;
    for(int i = 0; i < StringLen(clean_text); i++)
    {
        ushort char_code = StringGetCharacter(clean_text, i);
        if(char_code == '.')
        {
            decimal_count++;
            if(decimal_count > 1) return false;
        }
        else if(char_code < '0' || char_code > '9')
        {
            return false;
        }
    }

    return true;
}

double CNumericEdit::GetNumericValue(void)
{
    return StringToDouble(Text());
}

bool CNumericEdit::SetNumericValue(const double value)
{
    string formatted = DoubleToString(value, m_decimal_places);
    return Text(formatted);
}
```

### Search Input with Live Filtering

```mql5
class CSearchEdit : public CEdit
{
private:
    string m_search_terms[];
    int    m_search_count;
    CWndContainer* m_results_container;

public:
    CSearchEdit();
    virtual bool OnSetText(void);
    void SetResultsContainer(CWndContainer* container);
    void AddSearchTerm(const string term);
    void ClearSearchTerms(void);
    void PerformSearch(const string query);
    void DisplayResults(const string results[], const int count);
};

bool CSearchEdit::OnSetText(void)
{
    // Perform live search as user types
    string current_text = Text();
    if(StringLen(current_text) >= 2) // Start searching after 2 characters
    {
        PerformSearch(current_text);
    }
    else if(m_results_container != NULL)
    {
        // Clear results if text is too short
        DisplayResults(NULL, 0);
    }

    return CEdit::OnSetText();
}

void CSearchEdit::PerformSearch(const string query)
{
    string results[];
    int result_count = 0;

    string lower_query = query;
    StringToLower(lower_query);

    // Search through available terms
    for(int i = 0; i < m_search_count; i++)
    {
        string lower_term = m_search_terms[i];
        StringToLower(lower_term);

        if(StringFind(lower_term, lower_query) >= 0)
        {
            ArrayResize(results, result_count + 1);
            results[result_count] = m_search_terms[i];
            result_count++;
        }
    }

    DisplayResults(results, result_count);
}
```

## Input Validation Patterns

### Email Validation
```mql5
bool IsValidEmail(const string email)
{
    if(StringLen(email) == 0) return false;

    int at_pos = StringFind(email, "@");
    if(at_pos <= 0) return false;

    int dot_pos = StringFind(email, ".", at_pos);
    if(dot_pos <= at_pos + 1) return false;

    if(dot_pos >= StringLen(email) - 1) return false;

    return true;
}
```

### Phone Number Formatting
```mql5
string FormatPhoneNumber(const string phone)
{
    string digits = "";

    // Extract only digits
    for(int i = 0; i < StringLen(phone); i++)
    {
        ushort char_code = StringGetCharacter(phone, i);
        if(char_code >= '0' && char_code <= '9')
            digits += CharToString(char_code);
    }

    // Format as (XXX) XXX-XXXX
    if(StringLen(digits) == 10)
    {
        return "(" + StringSubstr(digits, 0, 3) + ") " +
               StringSubstr(digits, 3, 3) + "-" +
               StringSubstr(digits, 6, 4);
    }

    return phone; // Return original if not 10 digits
}
```

## Best Practices

1. **Input Validation**
   - Validate input in OnObjectEndEdit()
   - Provide clear error messages
   - Restore valid values on invalid input

2. **User Experience**
   - Use appropriate text alignment
   - Set reasonable field sizes
   - Provide placeholder text where helpful

3. **Read-Only Usage**
   - Use ReadOnly(true) for display-only fields
   - Style read-only fields differently
   - Consider using CLabel for truly static text

4. **Text Formatting**
   - Format numeric inputs consistently
   - Use appropriate decimal places
   - Handle currency and percentage formatting

5. **Event Handling**
   - Handle both text changes and edit completion
   - Provide real-time feedback where appropriate
   - Validate on focus loss

## Common Use Cases

- **Data Entry Forms** - User information collection
- **Search Interfaces** - Query input with live results
- **Numeric Inputs** - Trading parameters and calculations
- **Configuration Settings** - Application preferences
- **Filter Controls** - Data filtering and sorting
- **Command Input** - Script and command execution

## See Also

- [CWndObj](../foundation/CWndObj.md) - Base class for simple controls
- [CLabel](./CLabel.md) - Text display control
- [CButton](./CButton.md) - Interactive button control
- [Input Validation Tutorial](../tutorials/input-validation.md)
- [Form Design Patterns](../tutorials/form-patterns.md)