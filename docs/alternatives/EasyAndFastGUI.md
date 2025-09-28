# EasyAndFastGUI - Alternative GUI Framework

EasyAndFastGUI is a comprehensive alternative to the standard MQL5 Controls Library, offering a more modern approach to GUI development in MetaTrader 5. Originally developed by MetaQuotes and available on [MQL5.com](https://www.mql5.com/en/code/19703), it provides a canvas-based rendering system with more flexible control over visual presentation.

## 🌟 Overview

EasyAndFastGUI takes a different architectural approach compared to the standard Controls Library:

- **Canvas-Based Rendering**: Uses `CRectCanvas` for custom drawing instead of standard MT5 objects
- **Event-Driven Architecture**: Sophisticated event handling with custom event constants
- **Modern Control Set**: More advanced controls like graphs, color pickers, and file navigators
- **Flexible Theming**: Built-in support for custom colors and styling
- **Animation Support**: Native support for control animations and smooth transitions

## 🏗️ Architecture Comparison

### EasyAndFastGUI vs Standard Controls Library

| Aspect | EasyAndFastGUI | Standard Controls |
|--------|----------------|-------------------|
| **Rendering** | Canvas-based custom drawing | Native MT5 graphical objects |
| **Performance** | High (optimized canvas) | Good (native objects) |
| **Customization** | Extensive theming options | Limited styling |
| **Controls** | 35+ advanced controls | 20+ basic controls |
| **Animation** | Built-in animation support | Manual implementation required |
| **Memory Usage** | Lower (shared canvas) | Higher (individual objects) |
| **Learning Curve** | Moderate | Easy |

## 📦 Core Architecture

### Base Classes

```mql5
// Core foundation
CElementBase    // Base element functionality
CElement        // Enhanced element with canvas support
CWindow         // Main window container
CWndEvents      // Event handling system
```

### Key Components

1. **Element System**: All controls inherit from `CElement` which provides:
   - Canvas-based drawing (`CRectCanvas`)
   - Event handling
   - Positioning and sizing
   - Color management
   - Animation support

2. **Event System**: Custom event constants for sophisticated interaction:
   ```mql5
   #define ON_CLICK_BUTTON           (7)
   #define ON_CLICK_CHECKBOX         (11)
   #define ON_CLICK_ELEMENT          (13)
   #define ON_CHANGE_GUI             (28)
   #define ON_MOUSE_FOCUS            (35)
   #define ON_MOUSE_BLUR             (34)
   ```

3. **Window Management**: Advanced window features:
   - Drag and drop
   - Resize handles
   - Minimize/maximize
   - Modal dialogs
   - Multi-window support

## 🎨 Available Controls

### Basic Controls
- **CTextLabel** - Enhanced text display with styling
- **CButton** - Advanced button with multiple states and animations
- **CTextEdit** - Rich text input with validation
- **CPicture** - Image display with scaling and effects
- **CCheckBox** - Styled checkbox controls
- **CPanel** - Container panels with borders and backgrounds

### Advanced Controls
- **CGraph** - Professional charting and graphing
- **CTable** - Data grid with sorting and editing
- **CTreeView** - Hierarchical data display
- **CColorPicker** - Color selection control
- **CFileNavigator** - File system browser
- **CCalendar** - Date selection with dropdown
- **CComboBox** - Advanced dropdown with search
- **CSlider** - Value selection sliders
- **CProgressBar** - Progress indication
- **CStatusBar** - Status information display
- **CTabs** - Tabbed interface support
- **CContextMenu** - Right-click context menus
- **CTooltip** - Rich tooltip support

### Specialized Controls
- **CTimeEdit** - Time input control
- **CSpinEdit** - Numeric spinner with validation
- **CScrolls** - Custom scrollbar implementation
- **CMenuBar** - Professional menu system
- **CStandardChart** - Embedded chart control

## 🚀 Quick Start Example

Here's a simple trading panel using EasyAndFastGUI:

```mql5
#include <EasyAndFastGUI\WndCreate.mqh>

class CProgram : public CWndCreate
{
private:
    CWindow      m_window;
    CButton      m_buy_button;
    CButton      m_sell_button;
    CTextEdit    m_lot_size;
    CStatusBar   m_status_bar;

public:
    bool CreateGUI(void);
    virtual void OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);
};

bool CProgram::CreateGUI(void)
{
    // Create main window
    if(!CreateWindow(m_window, "Trading Panel", 1, 1, 300, 200, true, true))
        return false;

    // Create lot size input
    if(!CreateTextEdit(m_lot_size, "Lot size:", m_window, 0, false,
                      10, 30, 100, 80, 10, 2, 0.01, 2, 1.0))
        return false;

    // Create buy button
    if(!CreateButton(m_buy_button, "BUY", m_window, 0, 10, 60, 80))
        return false;

    // Create sell button
    if(!CreateButton(m_sell_button, "SELL", m_window, 0, 120, 60, 80))
        return false;

    // Create status bar
    string status_items[] = {"Ready", "Connected"};
    int item_widths[] = {0, 100};
    if(!CreateStatusBar(m_status_bar, m_window, 1, 2, 18, status_items, item_widths))
        return false;

    // Complete GUI creation
    CWndEvents::CompletedGUI();
    return true;
}

void CProgram::OnEvent(const int id, const long &lparam,
                      const double &dparam, const string &sparam)
{
    // Handle buy button click
    if(id == CHARTEVENT_CUSTOM + ON_CLICK_BUTTON)
    {
        if(lparam == m_buy_button.Id())
        {
            double lots = m_lot_size.GetValue();
            // Execute buy order
            Print("Buy order: ", lots, " lots");
        }
        else if(lparam == m_sell_button.Id())
        {
            double lots = m_lot_size.GetValue();
            // Execute sell order
            Print("Sell order: ", lots, " lots");
        }
    }
}

// Expert Advisor Integration
CProgram program;

int OnInit()
{
    return program.CreateGUI() ? INIT_SUCCEEDED : INIT_FAILED;
}

void OnDeinit(const int reason)
{
    program.OnDeinitEvent(reason);
}

void OnChartEvent(const int id, const long& lparam,
                 const double& dparam, const string& sparam)
{
    program.ChartEvent(id, lparam, dparam, sparam);
}
```

## 🎨 Styling and Theming

EasyAndFastGUI provides extensive theming capabilities:

```mql5
// Custom button styling
m_button.BackColor(C'240,240,240');           // Normal background
m_button.BackColorHover(C'220,220,220');      // Hover background
m_button.BackColorPressed(C'200,200,200');    // Pressed background
m_button.BorderColor(C'160,160,160');         // Border color
m_button.LabelColor(clrBlack);                // Text color

// Custom window styling
m_window.CaptionColor(C'50,50,50');           // Title bar color
m_window.BackColor(C'240,240,240');           // Window background
m_window.IsMovable(true);                     // Enable dragging
m_window.CloseButton(true);                   // Show close button
```

## 📊 Advanced Features

### 1. Professional Charting

```mql5
CGraph graph;
if(CreateGraph(graph, m_window, 10, 50))
{
    CGraphic* graphic = graph.GetGraphicPointer();
    graphic.BackgroundColor(ColorToARGB(clrWhiteSmoke));

    // Add data series
    CCurve* curve = graphic.CurveAdd();
    curve.Name("Price Data");
    curve.Color(clrBlue);
    curve.Type(CURVE_LINES);

    // Add data points
    for(int i = 0; i < 100; i++)
    {
        curve.DataAdd(i, MathSin(i * 0.1) * 100);
    }

    graphic.Redraw();
}
```

### 2. Data Tables

```mql5
CTable table;
if(CreateTable(table, m_window, 10, 100))
{
    // Configure table
    table.TableSize(3, 10);  // 3 columns, 10 rows
    table.HeadersText(headers);
    table.ColumnWidth(0, 80);
    table.ColumnWidth(1, 100);
    table.ColumnWidth(2, 120);

    // Add data
    for(int row = 0; row < 10; row++)
    {
        table.TextByRowCol(row, 0, "Item " + IntegerToString(row));
        table.TextByRowCol(row, 1, DoubleToString(row * 10.5, 2));
        table.TextByRowCol(row, 2, TimeToString(TimeCurrent() + row * 3600));
    }
}
```

### 3. File Navigation

```mql5
CFileNavigator navigator;
if(CreateFileNavigator(navigator, m_window, 10, 150))
{
    navigator.NavigatorMode(FN_ALL);           // Show all files
    navigator.NavigatorContent(FN_BOTH);       // MQL and common folders
    navigator.CurrentFullPath("\\Files\\");    // Set initial path
}
```

## ⚡ Performance Considerations

### Advantages of EasyAndFastGUI:
1. **Lower Memory Usage**: Canvas-based rendering uses fewer graphical objects
2. **Faster Rendering**: Optimized drawing routines for complex interfaces
3. **Smooth Animations**: Built-in animation system with interpolation
4. **Efficient Updates**: Partial canvas redraws for better performance

### When to Use EasyAndFastGUI:
- Complex interfaces with many controls
- Applications requiring custom styling
- Professional trading tools
- Data visualization requirements
- Animation and smooth transitions needed

### When to Use Standard Controls:
- Simple interfaces with few controls
- Quick prototyping
- Following MetaTrader UI conventions
- Minimal learning curve required

## 🔧 Migration from Standard Controls

Here's how common patterns translate:

### Button Creation
```mql5
// Standard Controls
CButton button;
button.Create(chart_id, "button", subwin, x1, y1, x2, y2);
button.Text("Click Me");

// EasyAndFastGUI
CButton button;
CreateButton(button, "Click Me", window, 0, x, y, width);
```

### Event Handling
```mql5
// Standard Controls
EVENT_MAP_BEGIN(CMyDialog)
    ON_EVENT(ON_CLICK, m_button, OnButtonClick)
EVENT_MAP_END(CAppDialog)

// EasyAndFastGUI
void OnEvent(const int id, const long &lparam, const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_CUSTOM + ON_CLICK_BUTTON)
    {
        if(lparam == m_button.Id())
            OnButtonClick();
    }
}
```

## 📚 Complete Control Reference

### Foundation Classes
- **CElementBase** - Core element functionality
- **CElement** - Enhanced element with canvas support
- **CWindow** - Window management and container

### Input Controls
- **CButton** - Button with multiple states
- **CTextEdit** - Rich text input
- **CCheckBox** - Checkbox control
- **CComboBox** - Dropdown selection
- **CSlider** - Value slider
- **CSpinEdit** - Numeric spinner
- **CTimeEdit** - Time input
- **CColorButton** - Color selection button

### Display Controls
- **CTextLabel** - Text display
- **CPicture** - Image display
- **CProgressBar** - Progress indication
- **CStatusBar** - Status information
- **CTooltip** - Rich tooltips

### Container Controls
- **CPanel** - Container panel
- **CTabs** - Tabbed interface
- **CFrame** - Grouped controls
- **CScrolls** - Scrollable areas

### Advanced Controls
- **CTable** - Data grid
- **CListView** - List display
- **CTreeView** - Hierarchical data
- **CGraph** - Charting and graphs
- **CCalendar** - Date selection
- **CFileNavigator** - File browser
- **CMenuBar** - Menu system
- **CContextMenu** - Context menus

### Specialized Controls
- **CStandardChart** - Embedded charts
- **CPicturesSlider** - Image carousel
- **CDropCalendar** - Calendar dropdown
- **CSeparateLine** - Visual separators
- **CPointer** - Resize handles

## 🎯 Best Practices

### 1. Memory Management
```mql5
// Proper cleanup in OnDeinit
void OnDeinitEvent(const int reason)
{
    CWndEvents::Destroy();  // Clean up all GUI elements
}
```

### 2. Event Handling
```mql5
// Centralized event processing
void OnEvent(const int id, const long &lparam, const double &dparam, const string &sparam)
{
    // Always check event type first
    if(id == CHARTEVENT_CUSTOM + ON_CLICK_BUTTON)
        HandleButtonClick(lparam);
    else if(id == CHARTEVENT_CUSTOM + ON_END_EDIT)
        HandleTextEdit(lparam, sparam);
    else if(id == CHARTEVENT_CUSTOM + ON_CHANGE_GUI)
        HandleGUIChange();
}
```

### 3. Performance Optimization
```mql5
// Use timers for smooth updates
void OnTimerEvent(void)
{
    static int counter = 0;

    // Update only every N timer ticks
    if(++counter >= 5)
    {
        counter = 0;
        UpdateGUI();
    }
}
```

## 🔗 Resources

- **Official Source**: [MQL5.com Code Base](https://www.mql5.com/en/code/19703)
- **Documentation**: Included header files with extensive comments
- **Examples**: Multiple example EAs demonstrating advanced features
- **Community**: Active discussions in MQL5 forums

## 🚀 Conclusion

EasyAndFastGUI represents a more advanced approach to GUI development in MQL5, offering:

- **Modern Architecture**: Canvas-based rendering with better performance
- **Rich Control Set**: Advanced controls not available in standard library
- **Professional Styling**: Extensive theming and customization options
- **Smooth Animations**: Built-in animation support for modern interfaces
- **Better Performance**: Optimized for complex trading applications

While it has a steeper learning curve than the standard Controls Library, EasyAndFastGUI is ideal for developers building sophisticated trading tools and professional applications that require advanced UI capabilities.

Choose EasyAndFastGUI when you need:
- Complex data visualization
- Custom styling and themes
- Advanced controls like graphs and tables
- Smooth animations and transitions
- Professional-grade user interfaces

For simple trading panels and basic interfaces, the standard MQL5 Controls Library may be more appropriate due to its simplicity and integration with MetaTrader's native UI conventions.