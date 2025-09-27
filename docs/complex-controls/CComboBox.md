# CComboBox - Dropdown Selection Control

The `CComboBox` class creates dropdown selection controls that combine an edit field with a collapsible list for item selection.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndContainer
        └── CComboBox
```

## Overview

- **Header File**: `<Controls\ComboBox.mqh>`
- **Purpose**: Dropdown list selection with edit capability
- **Key Features**: Item management, dropdown list, text editing, selection handling
- **Components**: Edit control, dropdown button, list view

## Component Structure

A ComboBox consists of three main components:
- **Edit Control** - Text display/input area
- **Button Control** - Dropdown trigger button
- **List View** - Dropdown selection list

## Core Methods

### Creation

#### Create()
Creates the combo box control with specified parameters.

```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Component Creation
```mql5
virtual bool CreateEdit(void);      // Create edit control
virtual bool CreateButton(void);    // Create dropdown button
virtual bool CreateList(void);      // Create dropdown list
```

### Item Management

#### AddItem()
Adds an item to the dropdown list.

```mql5
bool AddItem(const string text, const long value = 0);
```

**Parameters:**
- `text` - Display text for the item
- `value` - Associated value (optional)

#### ItemsTotal()
Returns the total number of items in the list.

```mql5
int ItemsTotal() const;
```

### Selection Management

#### Select()
Selects an item by index and returns its text.

```mql5
string Select(const int index);
```

#### SelectByText()
Selects an item by its text content.

```mql5
bool SelectByText(const string text);
```

#### SelectByValue()
Selects an item by its value.

```mql5
bool SelectByValue(const long value);
```

#### Value()
Returns the value of the currently selected item.

```mql5
long Value() const;
```

#### Text()
Gets or sets the text in the edit portion.

```mql5
string Text() const;                    // Get current text
virtual bool Text(const string text);  // Set text
```

### List Display

#### ListShow()
Shows the dropdown list.

```mql5
virtual bool ListShow(void);
```

#### ListHide()
Hides the dropdown list.

```mql5
virtual bool ListHide(void);
```

#### ListViewItems()
Sets the number of visible items in the dropdown.

```mql5
virtual bool ListViewItems(const int items);
```

## Event Handling

### Selection Events

#### OnChangeList()
Called when the list selection changes.

```mql5
virtual bool OnChangeList(void);
```

### Component Events
Handles events from child components (edit, button, list).

## Usage Examples

### Basic ComboBox Implementation

```mql5
#include <Controls\ComboBox.mqh>
#include <Controls\Dialog.mqh>
#include <Controls\Label.mqh>

class CComboDialog : public CAppDialog
{
private:
    CLabel    m_label_symbols;
    CComboBox m_combo_symbols;
    CLabel    m_label_timeframes;
    CComboBox m_combo_timeframes;
    CLabel    m_label_selected;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    bool CreateControls(void);
    void PopulateSymbols(void);
    void PopulateTimeframes(void);
    void OnSymbolChange(void);
    void OnTimeframeChange(void);
};

bool CComboDialog::Create(const long chart_id, const string name, const int subwin,
                         const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreateControls();
}

bool CComboDialog::CreateControls(void)
{
    int y_pos = 10;
    int row_height = 40;

    // Symbol selection
    if(!m_label_symbols.Create(m_chart_id, m_name+"LabelSymbols", m_subwin,
                              10, y_pos, 100, y_pos+20))
        return false;
    m_label_symbols.Text("Symbol:");
    Add(m_label_symbols);

    if(!m_combo_symbols.Create(m_chart_id, m_name+"ComboSymbols", m_subwin,
                              110, y_pos, 250, y_pos+25))
        return false;
    m_combo_symbols.ListViewItems(8);
    Add(m_combo_symbols);
    PopulateSymbols();

    y_pos += row_height;

    // Timeframe selection
    if(!m_label_timeframes.Create(m_chart_id, m_name+"LabelTimeframes", m_subwin,
                                 10, y_pos, 100, y_pos+20))
        return false;
    m_label_timeframes.Text("Timeframe:");
    Add(m_label_timeframes);

    if(!m_combo_timeframes.Create(m_chart_id, m_name+"ComboTimeframes", m_subwin,
                                 110, y_pos, 250, y_pos+25))
        return false;
    m_combo_timeframes.ListViewItems(6);
    Add(m_combo_timeframes);
    PopulateTimeframes();

    y_pos += row_height;

    // Selected info display
    if(!m_label_selected.Create(m_chart_id, m_name+"LabelSelected", m_subwin,
                               10, y_pos, 300, y_pos+20))
        return false;
    m_label_selected.Text("Selected: None");
    m_label_selected.Color(clrBlue);
    Add(m_label_selected);

    return true;
}

void CComboDialog::PopulateSymbols(void)
{
    // Add major currency pairs
    m_combo_symbols.AddItem("EURUSD", 1);
    m_combo_symbols.AddItem("GBPUSD", 2);
    m_combo_symbols.AddItem("USDJPY", 3);
    m_combo_symbols.AddItem("USDCHF", 4);
    m_combo_symbols.AddItem("AUDUSD", 5);
    m_combo_symbols.AddItem("USDCAD", 6);
    m_combo_symbols.AddItem("NZDUSD", 7);
    m_combo_symbols.AddItem("EURJPY", 8);
    m_combo_symbols.AddItem("EURGBP", 9);
    m_combo_symbols.AddItem("GBPJPY", 10);

    // Select first item by default
    m_combo_symbols.Select(0);
}

void CComboDialog::PopulateTimeframes(void)
{
    // Add common timeframes
    m_combo_timeframes.AddItem("M1", PERIOD_M1);
    m_combo_timeframes.AddItem("M5", PERIOD_M5);
    m_combo_timeframes.AddItem("M15", PERIOD_M15);
    m_combo_timeframes.AddItem("M30", PERIOD_M30);
    m_combo_timeframes.AddItem("H1", PERIOD_H1);
    m_combo_timeframes.AddItem("H4", PERIOD_H4);
    m_combo_timeframes.AddItem("D1", PERIOD_D1);
    m_combo_timeframes.AddItem("W1", PERIOD_W1);
    m_combo_timeframes.AddItem("MN1", PERIOD_MN1);

    // Select H1 by default
    m_combo_timeframes.SelectByValue(PERIOD_H1);
}

bool CComboDialog::OnEvent(const int id, const long &lparam,
                          const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_CUSTOM)
    {
        if(lparam == m_combo_symbols.Id())
        {
            OnSymbolChange();
            return true;
        }
        if(lparam == m_combo_timeframes.Id())
        {
            OnTimeframeChange();
            return true;
        }
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CComboDialog::OnSymbolChange(void)
{
    string symbol = m_combo_symbols.Text();
    long symbol_value = m_combo_symbols.Value();

    Print("Symbol changed to: ", symbol, " (ID: ", symbol_value, ")");
    UpdateSelectedDisplay();
}

void CComboDialog::OnTimeframeChange(void)
{
    string timeframe = m_combo_timeframes.Text();
    long tf_value = m_combo_timeframes.Value();

    Print("Timeframe changed to: ", timeframe, " (Period: ", tf_value, ")");
    UpdateSelectedDisplay();
}

void CComboDialog::UpdateSelectedDisplay(void)
{
    string symbol = m_combo_symbols.Text();
    string timeframe = m_combo_timeframes.Text();
    string display_text = "Selected: " + symbol + " " + timeframe;
    m_label_selected.Text(display_text);
}
```

### Dynamic ComboBox with Data Binding

```mql5
struct SComboItem
{
    string display_text;
    long   value;
    string category;
};

class CDataComboBox : public CComboBox
{
private:
    SComboItem m_items[];
    int        m_item_count;
    string     m_current_category;

public:
    CDataComboBox();
    bool AddDataItem(const string text, const long value, const string category = "");
    void FilterByCategory(const string category);
    void ClearFilter(void);
    SComboItem GetSelectedItem(void);
    virtual bool OnChangeList(void);

private:
    void RebuildList(void);
};

CDataComboBox::CDataComboBox(void) : m_item_count(0), m_current_category("")
{
    ArrayResize(m_items, 100);
}

bool CDataComboBox::AddDataItem(const string text, const long value, const string category = "")
{
    if(m_item_count >= ArraySize(m_items))
        ArrayResize(m_items, ArraySize(m_items) + 50);

    m_items[m_item_count].display_text = text;
    m_items[m_item_count].value = value;
    m_items[m_item_count].category = category;
    m_item_count++;

    // Add to combo if it matches current filter
    if(StringLen(m_current_category) == 0 || category == m_current_category)
    {
        AddItem(text, value);
    }

    return true;
}

void CDataComboBox::FilterByCategory(const string category)
{
    m_current_category = category;
    RebuildList();
}

void CDataComboBox::ClearFilter(void)
{
    m_current_category = "";
    RebuildList();
}

void CDataComboBox::RebuildList(void)
{
    // Clear current items (would need custom implementation)
    // For now, we'll just track what should be shown

    Print("Rebuilding combo list for category: ",
          (StringLen(m_current_category) == 0 ? "All" : m_current_category));

    int visible_count = 0;
    for(int i = 0; i < m_item_count; i++)
    {
        if(StringLen(m_current_category) == 0 ||
           m_items[i].category == m_current_category)
        {
            Print("  Item: ", m_items[i].display_text);
            visible_count++;
        }
    }

    Print("Total visible items: ", visible_count);
}

SComboItem CDataComboBox::GetSelectedItem(void)
{
    SComboItem empty_item = {};

    long selected_value = Value();
    for(int i = 0; i < m_item_count; i++)
    {
        if(m_items[i].value == selected_value)
            return m_items[i];
    }

    return empty_item;
}

bool CDataComboBox::OnChangeList(void)
{
    SComboItem selected = GetSelectedItem();
    Print("Selected item: ", selected.display_text,
          " Category: ", selected.category,
          " Value: ", selected.value);

    return CComboBox::OnChangeList();
}
```

### Cascading ComboBoxes

```mql5
class CCascadingComboBoxes : public CWndContainer
{
private:
    CLabel    m_label_country;
    CComboBox m_combo_country;
    CLabel    m_label_state;
    CComboBox m_combo_state;
    CLabel    m_label_city;
    CComboBox m_combo_city;

    struct SLocation
    {
        string country;
        string state;
        string city;
    };

    SLocation m_locations[];
    int       m_location_count;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void AddLocation(const string country, const string state, const string city);

private:
    bool CreateControls(void);
    void PopulateCountries(void);
    void PopulateStates(const string country);
    void PopulateCities(const string country, const string state);
    void OnCountryChange(void);
    void OnStateChange(void);
    void OnCityChange(void);
};

bool CCascadingComboBoxes::Create(const long chart_id, const string name, const int subwin,
                                 const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    m_location_count = 0;
    ArrayResize(m_locations, 1000);

    // Add sample data
    AddLocation("USA", "California", "Los Angeles");
    AddLocation("USA", "California", "San Francisco");
    AddLocation("USA", "New York", "New York City");
    AddLocation("USA", "New York", "Buffalo");
    AddLocation("Canada", "Ontario", "Toronto");
    AddLocation("Canada", "Ontario", "Ottawa");
    AddLocation("Canada", "Quebec", "Montreal");
    AddLocation("Canada", "Quebec", "Quebec City");

    return CreateControls();
}

bool CCascadingComboBoxes::CreateControls(void)
{
    int y_pos = 10;
    int row_height = 40;
    int label_width = 80;
    int combo_width = 150;

    // Country selection
    if(!m_label_country.Create(m_chart_id, m_name+"LabelCountry", m_subwin,
                              10, y_pos, 10+label_width, y_pos+20))
        return false;
    m_label_country.Text("Country:");
    Add(m_label_country);

    if(!m_combo_country.Create(m_chart_id, m_name+"ComboCountry", m_subwin,
                              100, y_pos, 100+combo_width, y_pos+25))
        return false;
    Add(m_combo_country);

    y_pos += row_height;

    // State selection
    if(!m_label_state.Create(m_chart_id, m_name+"LabelState", m_subwin,
                            10, y_pos, 10+label_width, y_pos+20))
        return false;
    m_label_state.Text("State:");
    Add(m_label_state);

    if(!m_combo_state.Create(m_chart_id, m_name+"ComboState", m_subwin,
                            100, y_pos, 100+combo_width, y_pos+25))
        return false;
    Add(m_combo_state);

    y_pos += row_height;

    // City selection
    if(!m_label_city.Create(m_chart_id, m_name+"LabelCity", m_subwin,
                           10, y_pos, 10+label_width, y_pos+20))
        return false;
    m_label_city.Text("City:");
    Add(m_label_city);

    if(!m_combo_city.Create(m_chart_id, m_name+"ComboCity", m_subwin,
                           100, y_pos, 100+combo_width, y_pos+25))
        return false;
    Add(m_combo_city);

    PopulateCountries();
    return true;
}

void CCascadingComboBoxes::AddLocation(const string country, const string state, const string city)
{
    if(m_location_count >= ArraySize(m_locations))
        ArrayResize(m_locations, ArraySize(m_locations) + 500);

    m_locations[m_location_count].country = country;
    m_locations[m_location_count].state = state;
    m_locations[m_location_count].city = city;
    m_location_count++;
}

void CCascadingComboBoxes::PopulateCountries(void)
{
    string countries[];
    int country_count = 0;

    // Get unique countries
    for(int i = 0; i < m_location_count; i++)
    {
        bool found = false;
        for(int j = 0; j < country_count; j++)
        {
            if(countries[j] == m_locations[i].country)
            {
                found = true;
                break;
            }
        }

        if(!found)
        {
            ArrayResize(countries, country_count + 1);
            countries[country_count] = m_locations[i].country;
            country_count++;
        }
    }

    // Add to combo
    for(int i = 0; i < country_count; i++)
    {
        m_combo_country.AddItem(countries[i], i);
    }

    if(country_count > 0)
    {
        m_combo_country.Select(0);
        OnCountryChange();
    }
}

bool CCascadingComboBoxes::OnEvent(const int id, const long &lparam,
                                  const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_CUSTOM)
    {
        if(lparam == m_combo_country.Id())
        {
            OnCountryChange();
            return true;
        }
        if(lparam == m_combo_state.Id())
        {
            OnStateChange();
            return true;
        }
        if(lparam == m_combo_city.Id())
        {
            OnCityChange();
            return true;
        }
    }

    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}

void CCascadingComboBoxes::OnCountryChange(void)
{
    string selected_country = m_combo_country.Text();
    PopulateStates(selected_country);
}

void CCascadingComboBoxes::OnStateChange(void)
{
    string selected_country = m_combo_country.Text();
    string selected_state = m_combo_state.Text();
    PopulateCities(selected_country, selected_state);
}

void CCascadingComboBoxes::OnCityChange(void)
{
    string country = m_combo_country.Text();
    string state = m_combo_state.Text();
    string city = m_combo_city.Text();

    Print("Selected location: ", city, ", ", state, ", ", country);
}
```

## Styling and Customization

### Custom Dropdown Button
```mql5
class CCustomComboBox : public CComboBox
{
protected:
    virtual bool CreateButton(void);
};

bool CCustomComboBox::CreateButton(void)
{
    if(!CComboBox::CreateButton())
        return false;

    // Customize button appearance
    // Access button through m_button member (if available)
    // Set custom colors, text, etc.

    return true;
}
```

### Read-Only ComboBox
```mql5
void SetComboBoxReadOnly(CComboBox &combo, bool read_only)
{
    // This would require access to the edit control
    // to set its ReadOnly property
    if(read_only)
    {
        // Disable editing, only allow selection from dropdown
    }
    else
    {
        // Allow free text entry
    }
}
```

## Best Practices

1. **Item Management**
   - Use meaningful item values for identification
   - Keep display text concise but descriptive
   - Organize items logically (alphabetical, categorical)

2. **Performance**
   - Limit dropdown size with ListViewItems()
   - Use lazy loading for large datasets
   - Implement search/filter for extensive lists

3. **User Experience**
   - Provide default selections where appropriate
   - Handle invalid entries gracefully
   - Support keyboard navigation

4. **Data Binding**
   - Separate data model from display
   - Implement cascading relationships properly
   - Validate selections against available options

## Common Use Cases

- **Symbol Selection** - Trading instrument choosers
- **Parameter Configuration** - Settings and options
- **Time Period Selection** - Timeframes and intervals
- **Filter Controls** - Data filtering dropdowns
- **Location Pickers** - Geographic selection
- **Category Browsers** - Hierarchical data selection

## See Also

- [CListView](./CListView.md) - List display control
- [CEdit](../simple-controls/CEdit.md) - Text input control
- [CButton](../simple-controls/CButton.md) - Button control
- [Form Design Patterns](../tutorials/form-patterns.md)
- [Data Binding Tutorial](../tutorials/data-binding.md)