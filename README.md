# MQL5 Controls Library - Complete Documentation

**The most comprehensive documentation for the MQL5 Controls Library**, covering all controls, advanced patterns, and production-ready examples for creating professional trading interfaces.

## 🌟 Overview

The MQL5 Controls Library (`MQL5\Include\Controls`) provides a powerful framework for building sophisticated graphical user interfaces in MetaTrader 5. This documentation covers every control, pattern, and technique needed to create professional trading applications.

## ✨ Features

- **Complete Control Coverage** - Documentation for all 20+ controls in the library
- **Production-Ready Examples** - Real-world code you can use immediately
- **Advanced Patterns** - Sophisticated techniques for professional applications
- **EA Integration Guides** - Complete lifecycle management and best practices
- **Source-Code Accurate** - Constants and patterns from actual MQL5 library source
- **Performance Optimized** - Efficient implementations for trading environments

## 🚀 Quick Start

Create your first trading panel in minutes:

```mql5
#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>

class CMyTradingPanel : public CAppDialog
{
private:
    CButton m_buy_button;
    CButton m_sell_button;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
protected:
    void OnBuyClick();
    void OnSellClick();
};

// Event handling
EVENT_MAP_BEGIN(CMyTradingPanel)
    ON_EVENT(ON_CLICK, m_buy_button, OnBuyClick)
    ON_EVENT(ON_CLICK, m_sell_button, OnSellClick)
EVENT_MAP_END(CAppDialog)

// Expert Advisor integration
CMyTradingPanel Panel;

int OnInit() {
    return Panel.Create(0, "TradingPanel", 0, 20, 20, 200, 100) ? INIT_SUCCEEDED : INIT_FAILED;
}

void OnDeinit(const int reason) {
    Panel.Destroy(reason);
}

void OnChartEvent(const int id, const long& lparam, const double& dparam, const string& sparam) {
    Panel.ChartEvent(id, lparam, dparam, sparam);
}
```

## 📚 Complete Documentation

### Foundation Classes
- [**CWnd**](./docs/foundation/CWnd.md) - Base class for all controls
- [**CWndObj**](./docs/foundation/CWndObj.md) - Base class for controls and dialogs
- [**CWndContainer**](./docs/foundation/CWndContainer.md) - Base class for complex controls

### Simple Controls
- [**CLabel**](./docs/simple-controls/CLabel.md) - Text label display
- [**CButton**](./docs/simple-controls/CButton.md) - Button control for user interaction
- [**CBmpButton**](./docs/simple-controls/CBmpButton.md) - Bitmap image button with multiple states
- [**CEdit**](./docs/simple-controls/CEdit.md) - Text input field
- [**CPanel**](./docs/simple-controls/CPanel.md) - Rectangle container panel
- [**CPicture**](./docs/simple-controls/CPicture.md) - Bitmap image display

### Complex Controls
- [**Scroll Bars**](./docs/complex-controls/ScrollBars.md) - Vertical and horizontal scrolling
- [**CWndClient**](./docs/complex-controls/CWndClient.md) - Scrollable client area with automatic scrollbar management
- [**CListView**](./docs/complex-controls/CListView.md) - List view for data display
- [**CComboBox**](./docs/complex-controls/CComboBox.md) - Dropdown selection control
- [**CCheckBox & CCheckGroup**](./docs/complex-controls/CheckBox.md) - Checkbox controls
- [**CRadioButton & CRadioGroup**](./docs/complex-controls/RadioButton.md) - Radio button controls
- [**CSpinEdit**](./docs/complex-controls/CSpinEdit.md) - Numeric increment/decrement field
- [**CDatePicker**](./docs/complex-controls/DatePicker.md) - Date selection with calendar dropdown
- [**CDialog & CAppDialog**](./docs/complex-controls/Dialog.md) - Dialog window management

### Auxiliary Structures
- [**CRect**](./docs/auxiliary/CRect.md) - Rectangular area structure
- [**CDateTime**](./docs/auxiliary/CDateTime.md) - Date and time handling

## 🛠️ Examples & Tutorials

### Basic Tutorials
- [**Getting Started**](./docs/tutorials/getting-started.md) - Your first MQL5 panel
- [**Layout Management**](./docs/tutorials/layout-management.md) - Positioning and sizing controls
- [**Event Handling**](./docs/tutorials/event-handling.md) - Mouse, keyboard, and custom events
- [**Form Patterns**](./docs/tutorials/form-patterns.md) - Common UI design patterns

### Advanced Integration
- [**Expert Advisor Integration**](./docs/tutorials/ea-integration.md) - Complete EA lifecycle and EVENT_MAP system
- [**Customization & Theming**](./docs/tutorials/customization.md) - Custom styling, themes, and branding
- [**Advanced Patterns**](./docs/tutorials/advanced-patterns.md) - Dynamic control manipulation and sophisticated techniques

### Production Examples
- [**Complete Trading Panel**](./docs/examples/production-examples.md) - Real-world implementation examples

## 📖 API Reference

- [**Complete Class Hierarchy**](./docs/api/class-hierarchy.md) - Full inheritance tree
- [**Function Reference**](./docs/api/function-reference.md) - All methods and properties
- [**Constants & Enums**](./docs/api/constants.md) - Control constants and enumerations

## 🔀 Alternative GUI Frameworks

### EasyAndFastGUI vs Standard Controls

While this documentation focuses on the official MQL5 Controls Library, we also cover the popular **EasyAndFastGUI** framework:

| Feature | Standard Controls | EasyAndFastGUI |
|---------|------------------|----------------|
| **Rendering** | Native MT5 objects | Canvas-based custom drawing |
| **Controls** | 20+ basic controls | 35+ advanced controls |
| **Theming** | Limited styling | Extensive customization |
| **Performance** | Good for simple UIs | Optimized for complex interfaces |
| **Learning Curve** | Easy | Moderate |
| **Best For** | Quick prototypes, simple panels | Professional applications, data visualization |

- [**EasyAndFastGUI Complete Guide**](./docs/alternatives/EasyAndFastGUI.md) - Modern canvas-based GUI framework with advanced controls and theming
- [**Framework Comparison Guide**](./docs/alternatives/framework-comparison.md) - Detailed comparison to help you choose the right framework

## 🎯 What Makes This Documentation Special

- **100% Control Coverage** - Every control in the MQL5 library documented with examples
- **Real Source Code Integration** - Constants and patterns from actual MetaQuotes source code
- **Production Battle-Tested** - Examples used in live trading applications
- **Advanced Patterns** - Sophisticated techniques not found elsewhere
- **Complete EVENT_MAP System** - Full macro documentation with all variants
- **Performance Optimized** - Efficient patterns for high-frequency trading environments

## 💫 Key Highlights

- **20+ Controls Documented** - From simple buttons to complex data grids
- **500+ Code Examples** - Copy-paste ready implementations
- **Advanced Integration** - Complete EA lifecycle management
- **Custom Theming** - Dark themes, branded interfaces, custom styling
- **Dynamic Patterns** - Runtime control manipulation and discovery
- **Memory Optimized** - Best practices for resource management

## 🚀 Getting Started

### For Beginners
1. [**Getting Started Tutorial**](./docs/tutorials/getting-started.md) - Your first panel in 10 minutes
2. [**Basic Controls**](./docs/simple-controls/CButton.md) - Learn button and input controls
3. [**Layout Management**](./docs/tutorials/layout-management.md) - Positioning and sizing

### For Intermediate Developers
1. [**Event Handling**](./docs/tutorials/event-handling.md) - Master the EVENT_MAP system
2. [**Complex Controls**](./docs/complex-controls/CListView.md) - Data display and selection
3. [**EA Integration**](./docs/tutorials/ea-integration.md) - Professional lifecycle management

### For Advanced Developers
1. [**Advanced Patterns**](./docs/tutorials/advanced-patterns.md) - Dynamic control manipulation
2. [**Customization Guide**](./docs/tutorials/customization.md) - Themes and branding
3. [**Production Examples**](./docs/examples/production-examples.md) - Complete trading applications

## 🎯 Popular Use Cases

- **Trading Panels** - Buy/sell buttons, position management, risk controls
- **Analysis Tools** - Chart navigation, indicator settings, data visualization
- **Risk Management** - Position sizing, stop-loss management, emergency controls
- **Account Management** - Balance display, equity monitoring, trade history
- **Custom Indicators** - Settings panels, alert configuration, visual controls

## 🏆 Why Choose This Documentation

| Feature | This Documentation | Official Docs | Other Resources |
|---------|-------------------|---------------|-----------------|
| **Complete Coverage** | ✅ All 20+ controls | ❌ Basic examples only | ❌ Incomplete |
| **Source-Code Accurate** | ✅ Real constants/enums | ❌ Generic examples | ❌ Outdated |
| **Production Ready** | ✅ Battle-tested code | ❌ Simple demos | ❌ Academic only |
| **Advanced Patterns** | ✅ Professional techniques | ❌ Basic usage | ❌ Limited scope |
| **EVENT_MAP Complete** | ✅ All macro variants | ❌ Basic examples | ❌ Missing details |
| **Performance Focus** | ✅ Trading optimized | ❌ General purpose | ❌ Not optimized |

## 🤝 Contributing

Found an issue or want to improve the documentation?
- Open an issue for bug reports
- Submit pull requests for improvements
- Share your own implementation patterns

## 📊 Documentation Stats

- **33 Documentation Files** - Comprehensive coverage
- **19,000+ Lines** - Detailed explanations and examples
- **500+ Code Examples** - Production-ready implementations
- **Complete API Reference** - Every method and constant documented

---

*Built by the trading community, for the trading community. Based on the official MQL5 Controls Library.*