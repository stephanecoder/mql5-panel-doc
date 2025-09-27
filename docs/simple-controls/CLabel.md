# CLabel - Text Label Control

The `CLabel` class creates simple non-editable text labels for displaying static text in MQL5 applications.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndObj
        └── CLabel
```

## Overview

- **Header File**: `<Controls\Label.mqh>`
- **Base Object**: Text label chart object
- **Purpose**: Display static, non-editable text
- **Key Features**: Text display, font control, color customization

## Core Methods

### Creation

#### Create()
Creates the label control with specified parameters.

```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

**Parameters:**
- `chart_id` - Chart identifier
- `name` - Label object name
- `subwin` - Subwindow index
- `x1, y1` - Top-left coordinates
- `x2, y2` - Bottom-right coordinates

**Returns:** `true` if successful, `false` otherwise

#### CreateLabel()
Specialized creation method for label-specific initialization.

```mql5
bool CreateLabel(const long chart_id, const string name, const int subwin,
                const int x1, const int y1, const int x2, const int y2);
```

### Text Management

#### Text()
Gets or sets the label's text content.

```mql5
string Text() const;                    // Get current text
virtual bool Text(const string text);  // Set new text
```

### Appearance Properties

#### Color Management
```mql5
color Color() const;                    // Get text color
virtual bool Color(const color clr);   // Set text color
```

#### Font Control
```mql5
string Font() const;                    // Get font name
virtual bool Font(const string font);  // Set font name

int FontSize() const;                   // Get font size
virtual bool FontSize(const int size); // Set font size
```

## Event Handling

### Property Change Events

#### OnSetText()
Called when the label text changes.

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

#### OnSetFontSize()
Called when the font size changes.

```mql5
virtual bool OnSetFontSize(void);
```

## Usage Examples

### Basic Label Creation

```mql5
#include <Controls\Label.mqh>

class CMyPanel : public CAppDialog
{
private:
    CLabel m_title_label;
    CLabel m_status_label;
    CLabel m_info_label;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
};

bool CMyPanel::Create(const long chart_id, const string name, const int subwin,
                     const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    // Create title label
    if(!m_title_label.Create(chart_id, name+"Title", subwin, 10, 10, 200, 30))
        return false;
    m_title_label.Text("Trading Panel");
    m_title_label.Font("Arial Bold");
    m_title_label.FontSize(14);
    m_title_label.Color(clrBlue);
    Add(m_title_label);

    // Create status label
    if(!m_status_label.Create(chart_id, name+"Status", subwin, 10, 40, 200, 60))
        return false;
    m_status_label.Text("Status: Ready");
    m_status_label.Color(clrGreen);
    Add(m_status_label);

    // Create info label
    if(!m_info_label.Create(chart_id, name+"Info", subwin, 10, 70, 200, 90))
        return false;
    m_info_label.Text("Click buttons to trade");
    m_info_label.Color(clrGray);
    m_info_label.FontSize(10);
    Add(m_info_label);

    return true;
}
```

### Dynamic Label Updates

```mql5
class CStatusLabel : public CLabel
{
private:
    datetime m_last_update;

public:
    virtual bool OnSetText(void);
    void UpdateStatus(const string status);
    void UpdateTime(void);
};

bool CStatusLabel::OnSetText(void)
{
    // Log text changes
    Print("Status updated: ", Text());
    m_last_update = TimeCurrent();

    return CLabel::OnSetText();
}

void CStatusLabel::UpdateStatus(const string status)
{
    // Update with timestamp
    string full_text = status + " (" + TimeToString(TimeCurrent()) + ")";
    Text(full_text);

    // Color based on status
    if(StringFind(status, "Error") >= 0)
        Color(clrRed);
    else if(StringFind(status, "Success") >= 0)
        Color(clrGreen);
    else
        Color(clrBlue);
}

void CStatusLabel::UpdateTime(void)
{
    Text("Current Time: " + TimeToString(TimeCurrent()));
}
```

### Informational Display Panel

```mql5
class CInfoPanel : public CWndContainer
{
private:
    CLabel m_labels[10];
    int    m_label_count;

public:
    bool CreateInfoPanel(const long chart_id, const string name, const int subwin,
                        const int x1, const int y1);
    void AddInfo(const string caption, const string value);
    void UpdateInfo(const int index, const string value);
    void ClearInfo(void);
};

bool CInfoPanel::CreateInfoPanel(const long chart_id, const string name, const int subwin,
                                const int x1, const int y1)
{
    int panel_width = 250;
    int panel_height = 300;

    if(!Create(chart_id, name, subwin, x1, y1, x1 + panel_width, y1 + panel_height))
        return false;

    m_label_count = 0;
    return true;
}

void CInfoPanel::AddInfo(const string caption, const string value)
{
    if(m_label_count >= 10) return;

    int y_pos = 10 + (m_label_count * 25);
    string label_name = Name() + "Info" + IntegerToString(m_label_count);

    if(m_labels[m_label_count].Create(Chart(), label_name, Subwin(),
                                     10, y_pos, 240, y_pos + 20))
    {
        string display_text = caption + ": " + value;
        m_labels[m_label_count].Text(display_text);
        m_labels[m_label_count].Font("Courier");
        m_labels[m_label_count].FontSize(9);
        Add(m_labels[m_label_count]);
        m_label_count++;
    }
}

void CInfoPanel::UpdateInfo(const int index, const string value)
{
    if(index >= 0 && index < m_label_count)
    {
        string current_text = m_labels[index].Text();
        int colon_pos = StringFind(current_text, ":");
        if(colon_pos > 0)
        {
            string caption = StringSubstr(current_text, 0, colon_pos);
            m_labels[index].Text(caption + ": " + value);
        }
    }
}
```

## Styling and Appearance

### Font Configuration
```mql5
// Configure label appearance
void SetupLabel(CLabel &label, const string text, const color text_color)
{
    label.Text(text);
    label.Color(text_color);
    label.Font("Arial");
    label.FontSize(11);
}

// Different label styles
void CreateStyledLabels(CWndContainer &container, const long chart_id,
                       const string base_name, const int subwin)
{
    CLabel title, subtitle, body;

    // Title style
    title.Create(chart_id, base_name+"Title", subwin, 10, 10, 200, 35);
    title.Text("Main Title");
    title.Font("Arial Bold");
    title.FontSize(16);
    title.Color(clrNavy);
    container.Add(title);

    // Subtitle style
    subtitle.Create(chart_id, base_name+"Subtitle", subwin, 10, 40, 200, 60);
    subtitle.Text("Subtitle Text");
    subtitle.Font("Arial");
    subtitle.FontSize(12);
    subtitle.Color(clrBlue);
    container.Add(subtitle);

    // Body text style
    body.Create(chart_id, base_name+"Body", subwin, 10, 70, 200, 90);
    body.Text("Body text information");
    body.Font("Arial");
    body.FontSize(10);
    body.Color(clrBlack);
    container.Add(body);
}
```

## Best Practices

1. **Clear Text Content**
   - Use concise, descriptive text
   - Avoid overly long labels that may be truncated
   - Consider text length when setting control size

2. **Font Selection**
   - Use system fonts for compatibility
   - Consider readability at different sizes
   - Maintain consistent font families in panels

3. **Color Usage**
   - Use contrasting colors for readability
   - Implement color coding for status information
   - Consider color-blind accessibility

4. **Layout Considerations**
   - Allow adequate space for text content
   - Align labels consistently within containers
   - Consider text expansion for different languages

5. **Dynamic Updates**
   - Use OnSetText() for validation and logging
   - Update labels efficiently to avoid flickering
   - Cache frequently displayed values

## Common Use Cases

- **Panel Headers** - Title and section labels
- **Status Displays** - Current state information
- **Information Panels** - Trading data and statistics
- **Form Labels** - Field descriptions and captions
- **Help Text** - Instructions and guidance
- **Data Displays** - Read-only value presentation

## See Also

- [CWndObj](../foundation/CWndObj.md) - Base class for simple controls
- [CButton](./CButton.md) - Interactive button control
- [CEdit](./CEdit.md) - Editable text control
- [Property Management Tutorial](../tutorials/property-management.md)
- [Layout Management](../tutorials/layout-management.md)