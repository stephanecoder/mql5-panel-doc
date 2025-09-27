# CListView - List View Control

The `CListView` class creates scrollable list controls for displaying and selecting items from collections of data.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndContainer
        └── CWndClient
            └── CListView
```

## Overview

- **Header File**: `<Controls\ListView.mqh>`
- **Purpose**: Scrollable list with item selection
- **Key Features**: Item management, selection handling, scrolling, visual feedback
- **Dependencies**: Scroll bars, client area management

## Core Methods

### Creation

#### Create()
Creates the list view control with specified parameters.

```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### CreateRow()
Creates a row within the list view.

```mql5
virtual bool CreateRow(const int index);
```

### Item Management

#### AddItem()
Adds an item to the list.

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

#### ItemText()
Gets or sets the text of a specific item.

```mql5
string ItemText(const int index) const;         // Get item text
bool ItemText(const int index, const string text); // Set item text
```

#### ItemValue()
Gets or sets the value of a specific item.

```mql5
long ItemValue(const int index) const;          // Get item value
bool ItemValue(const int index, const long value); // Set item value
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

#### Current()
Returns the index of the currently selected item.

```mql5
int Current() const;
```

### Display Management

#### TotalView()
Sets the number of visible items in the list.

```mql5
virtual bool TotalView(const int total);
```

#### RowState()
Sets the state of a specific row.

```mql5
virtual bool RowState(const int index, const bool select);
```

#### CheckView()
Checks if a row is visible.

```mql5
virtual bool CheckView(void);
```

#### Redraw()
Redraws the list control.

```mql5
virtual bool Redraw(void);
```

### Scrolling Events

#### OnVScrollShow/OnVScrollHide()
Handles vertical scroll bar visibility.

```mql5
virtual bool OnVScrollShow(void);   // Scroll bar shown
virtual bool OnVScrollHide(void);   // Scroll bar hidden
```

#### OnScrollLineDown/OnScrollLineUp()
Handles scrolling operations.

```mql5
virtual bool OnScrollLineDown(void);    // Scroll down one line
virtual bool OnScrollLineUp(void);      // Scroll up one line
```

## Event Handling

### Item Events

#### OnItemClick()
Called when a list item is clicked.

```mql5
virtual bool OnItemClick(void);
```

#### OnChangeList()
Called when the list selection changes.

```mql5
virtual bool OnChangeList(void);
```

## Usage Examples

### Basic List View

```mql5
#include <Controls\ListView.mqh>
#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>

class CListDialog : public CAppDialog
{
private:
    CListView m_list_view;
    CButton   m_button_add;
    CButton   m_button_remove;
    CButton   m_button_clear;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    bool CreateControls(void);
    void OnButtonAdd(void);
    void OnButtonRemove(void);
    void OnButtonClear(void);
    void OnListSelectionChange(void);
};

bool CListDialog::Create(const long chart_id, const string name, const int subwin,
                        const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreateControls();
}

bool CListDialog::CreateControls(void)
{
    // Create list view
    if(!m_list_view.Create(m_chart_id, m_name+"ListView", m_subwin,
                          10, 10, 250, 200))
        return false;

    // Add initial items
    for(int i = 0; i < 20; i++)
    {
        string item_text = "Item " + IntegerToString(i + 1);
        m_list_view.AddItem(item_text, i + 1);
    }

    m_list_view.TotalView(8); // Show 8 items at once
    Add(m_list_view);

    // Create buttons
    if(!m_button_add.Create(m_chart_id, m_name+"ButtonAdd", m_subwin,
                           260, 10, 340, 40))
        return false;
    m_button_add.Text("Add Item");
    Add(m_button_add);

    if(!m_button_remove.Create(m_chart_id, m_name+"ButtonRemove", m_subwin,
                              260, 50, 340, 80))
        return false;
    m_button_remove.Text("Remove");
    Add(m_button_remove);

    if(!m_button_clear.Create(m_chart_id, m_name+"ButtonClear", m_subwin,
                             260, 90, 340, 120))
        return false;
    m_button_clear.Text("Clear All");
    Add(m_button_clear);

    return true;
}

bool CListDialog::OnEvent(const int id, const long &lparam,
                         const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == m_button_add.Name())
        {
            OnButtonAdd();
            return true;
        }
        if(sparam == m_button_remove.Name())
        {
            OnButtonRemove();
            return true;
        }
        if(sparam == m_button_clear.Name())
        {
            OnButtonClear();
            return true;
        }
    }

    if(id == CHARTEVENT_CUSTOM && lparam == m_list_view.Id())
    {
        OnListSelectionChange();
        return true;
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}

void CListDialog::OnButtonAdd(void)
{
    static int item_counter = 21;
    string new_item = "New Item " + IntegerToString(item_counter);
    m_list_view.AddItem(new_item, item_counter);
    item_counter++;

    Print("Added item: ", new_item);
}

void CListDialog::OnButtonRemove(void)
{
    int current = m_list_view.Current();
    if(current >= 0)
    {
        string removed_text = m_list_view.ItemText(current);
        // Note: Standard CListView doesn't have a direct remove method
        // This would require custom implementation
        Print("Would remove item: ", removed_text);
    }
}

void CListDialog::OnButtonClear(void)
{
    // Clear all items - would require custom implementation
    // as standard CListView doesn't have a clear method
    Print("Would clear all items");
}

void CListDialog::OnListSelectionChange(void)
{
    int current = m_list_view.Current();
    if(current >= 0)
    {
        string selected_text = m_list_view.ItemText(current);
        long selected_value = m_list_view.Value();
        Print("Selected: ", selected_text, " (Value: ", selected_value, ")");
    }
}
```

### Data-Driven List View

```mql5
struct SListItem
{
    string symbol;
    double price;
    double change;
    string status;
};

class CDataListView : public CListView
{
private:
    SListItem m_data[];
    int       m_data_count;

public:
    CDataListView();
    bool AddDataItem(const string symbol, const double price,
                    const double change, const string status);
    bool UpdateDataItem(const int index, const double price, const double change);
    void RefreshDisplay(void);
    virtual bool OnItemClick(void);

private:
    string FormatItemText(const SListItem &item);
    color GetItemColor(const SListItem &item);
};

CDataListView::CDataListView(void) : m_data_count(0)
{
    ArrayResize(m_data, 100);
}

bool CDataListView::AddDataItem(const string symbol, const double price,
                               const double change, const string status)
{
    if(m_data_count >= ArraySize(m_data))
        ArrayResize(m_data, ArraySize(m_data) + 50);

    m_data[m_data_count].symbol = symbol;
    m_data[m_data_count].price = price;
    m_data[m_data_count].change = change;
    m_data[m_data_count].status = status;

    string item_text = FormatItemText(m_data[m_data_count]);
    AddItem(item_text, m_data_count);

    m_data_count++;
    return true;
}

bool CDataListView::UpdateDataItem(const int index, const double price, const double change)
{
    if(index < 0 || index >= m_data_count) return false;

    m_data[index].price = price;
    m_data[index].change = change;

    // Update display
    string new_text = FormatItemText(m_data[index]);
    ItemText(index, new_text);

    return true;
}

string CDataListView::FormatItemText(const SListItem &item)
{
    string change_str = (item.change >= 0 ? "+" : "") + DoubleToString(item.change, 2);
    return StringFormat("%-8s %8.4f %8s %s",
                       item.symbol,
                       item.price,
                       change_str,
                       item.status);
}

void CDataListView::RefreshDisplay(void)
{
    // Update all item displays
    for(int i = 0; i < m_data_count; i++)
    {
        string updated_text = FormatItemText(m_data[i]);
        ItemText(i, updated_text);
    }
    Redraw();
}

bool CDataListView::OnItemClick(void)
{
    int current = Current();
    if(current >= 0 && current < m_data_count)
    {
        Print("Selected symbol: ", m_data[current].symbol,
              " Price: ", m_data[current].price,
              " Change: ", m_data[current].change,
              " Status: ", m_data[current].status);
    }

    return CListView::OnItemClick();
}
```

### Searchable List View

```mql5
class CSearchableListView : public CWndContainer
{
private:
    CEdit     m_search_edit;
    CListView m_list_view;
    CButton   m_clear_button;

    string    m_all_items[];
    long      m_all_values[];
    int       m_total_items;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    bool AddSearchableItem(const string text, const long value = 0);
    void PerformSearch(const string query);
    void ClearSearch(void);

private:
    bool CreateControls(void);
    void OnSearchTextChange(void);
    void OnClearSearch(void);
};

bool CSearchableListView::Create(const long chart_id, const string name, const int subwin,
                                const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    m_total_items = 0;
    ArrayResize(m_all_items, 1000);
    ArrayResize(m_all_values, 1000);

    return CreateControls();
}

bool CSearchableListView::CreateControls(void)
{
    int search_height = 25;
    int button_width = 60;

    // Search edit box
    if(!m_search_edit.Create(m_chart_id, m_name+"SearchEdit", m_subwin,
                            0, 0, Width()-button_width-5, search_height))
        return false;
    m_search_edit.Text("Search...");
    Add(m_search_edit);

    // Clear button
    if(!m_clear_button.Create(m_chart_id, m_name+"ClearButton", m_subwin,
                             Width()-button_width, 0, Width(), search_height))
        return false;
    m_clear_button.Text("Clear");
    Add(m_clear_button);

    // List view
    if(!m_list_view.Create(m_chart_id, m_name+"ListView", m_subwin,
                          0, search_height+5, Width(), Height()))
        return false;
    m_list_view.TotalView(10);
    Add(m_list_view);

    return true;
}

bool CSearchableListView::AddSearchableItem(const string text, const long value = 0)
{
    if(m_total_items >= ArraySize(m_all_items))
    {
        ArrayResize(m_all_items, ArraySize(m_all_items) + 500);
        ArrayResize(m_all_values, ArraySize(m_all_values) + 500);
    }

    m_all_items[m_total_items] = text;
    m_all_values[m_total_items] = value;
    m_total_items++;

    // Add to visible list
    m_list_view.AddItem(text, value);

    return true;
}

void CSearchableListView::PerformSearch(const string query)
{
    // Clear current list display (would need custom implementation)
    // For demonstration, we'll just print matches

    string lower_query = query;
    StringToLower(lower_query);

    int matches = 0;
    Print("Search results for: ", query);

    for(int i = 0; i < m_total_items; i++)
    {
        string lower_item = m_all_items[i];
        StringToLower(lower_item);

        if(StringFind(lower_item, lower_query) >= 0)
        {
            Print("  Match: ", m_all_items[i]);
            matches++;
        }
    }

    Print("Found ", matches, " matches");
}

bool CSearchableListView::OnEvent(const int id, const long &lparam,
                                 const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_ENDEDIT)
    {
        if(sparam == m_search_edit.Name())
        {
            OnSearchTextChange();
            return true;
        }
    }

    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        if(sparam == m_clear_button.Name())
        {
            OnClearSearch();
            return true;
        }
    }

    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}

void CSearchableListView::OnSearchTextChange(void)
{
    string query = m_search_edit.Text();
    if(StringLen(query) > 0 && query != "Search...")
    {
        PerformSearch(query);
    }
}

void CSearchableListView::OnClearSearch(void)
{
    m_search_edit.Text("");
    ClearSearch();
}

void CSearchableListView::ClearSearch(void)
{
    // Reset to show all items
    // Would require custom implementation to rebuild the list
    Print("Clearing search - showing all items");
}
```

## Advanced Features

### Custom Item Rendering
```mql5
class CCustomListView : public CListView
{
protected:
    virtual bool CreateRow(const int index);
    virtual bool RowState(const int index, const bool select);

private:
    void SetRowColors(const int index, const bool selected);
};

bool CCustomListView::CreateRow(const int index)
{
    if(!CListView::CreateRow(index))
        return false;

    SetRowColors(index, false);
    return true;
}

bool CCustomListView::RowState(const int index, const bool select)
{
    if(!CListView::RowState(index, select))
        return false;

    SetRowColors(index, select);
    return true;
}

void CCustomListView::SetRowColors(const int index, const bool selected)
{
    // Custom coloring based on item data
    long value = ItemValue(index);

    if(selected)
    {
        // Selected item colors
        // Set background and text colors for selection
    }
    else
    {
        // Normal item colors based on value
        if(value > 100)
        {
            // High value items in green
        }
        else if(value < 0)
        {
            // Negative value items in red
        }
        else
        {
            // Normal items in default colors
        }
    }
}
```

## Best Practices

1. **Performance**
   - Use TotalView() to limit visible items for large datasets
   - Implement virtual scrolling for very large lists
   - Update only changed items, not the entire list

2. **User Experience**
   - Provide clear selection feedback
   - Handle keyboard navigation
   - Implement search functionality for long lists

3. **Data Management**
   - Keep data separate from display
   - Use meaningful item values for identification
   - Implement efficient item lookup methods

4. **Event Handling**
   - Handle selection changes appropriately
   - Provide context menus for item actions
   - Support multi-selection when needed

## Common Use Cases

- **Symbol Lists** - Trading instrument selection
- **Order Lists** - Active and historical order display
- **Log Viewers** - Application and trade logs
- **Configuration Lists** - Settings and parameter selection
- **File Browsers** - File and directory navigation
- **Data Tables** - Structured data presentation

## See Also

- [CWndClient](./CWndClient.md) - Client area base class
- [ScrollBars](./ScrollBars.md) - Scrolling functionality
- [CComboBox](./CComboBox.md) - Dropdown list control
- [Data Management Tutorial](../tutorials/data-management.md)
- [Event Handling Guide](../tutorials/event-handling.md)