# MQL5 Panel Controls Documentation

Comprehensive documentation for the MQL5 Controls Library, covering creation of control panels and dialogs for MQL5 applications.

## 🚀 Quick Start

The MQL5 Controls Library is located in `MQL5\Include\Controls` and provides a complete set of UI controls to create interactive trading applications, Expert Advisors, and custom indicators.

## 📚 Documentation Structure

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

## 🎯 Key Features

- **Ready-to-use UI Controls** - Comprehensive set of standard controls
- **Event-driven Architecture** - Handle user interactions efficiently
- **Flexible Layout System** - Position and size controls dynamically
- **Cross-platform Compatibility** - Works with MT4 and MT5
- **Memory Management** - Automatic resource cleanup
- **Extensible Design** - Create custom controls by inheritance

## 🔧 Installation

1. Ensure MQL5 is installed with the standard library
2. Include the required control headers in your project:
```mql5
#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>
#include <Controls\Edit.mqh>
// Add other controls as needed
```

## 💡 Quick Example

```mql5
#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>

class CMyPanel : public CAppDialog
{
private:
    CButton m_button;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin, const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam, const double &dparam, const string &sparam);
};

bool CMyPanel::Create(const long chart_id, const string name, const int subwin, const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2)) return false;

    // Create button
    if(!m_button.Create(m_chart_id, m_name+"Button", m_subwin, 10, 10, 100, 40)) return false;
    m_button.Text("Click Me");
    Add(m_button);

    return true;
}
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for improvements.

---

*This documentation is based on the official MQL5 Controls Library available at [mql5.com](https://www.mql5.com/en/docs/standardlibrary/controls)*