# Scroll Bar Controls

The scroll bar controls (CScroll, CScrollV, CScrollH) provide scrolling functionality for containers with content that exceeds the visible area.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndContainer
        └── CScroll
            ├── CScrollV (Vertical Scroll Bar)
            └── CScrollH (Horizontal Scroll Bar)
```

## Overview

- **Header File**: `<Controls\Scrolls.mqh>`
- **Base Class**: CScroll (abstract base)
- **Implementations**: CScrollV (vertical), CScrollH (horizontal)
- **Key Features**: Position tracking, draggable thumb, increment/decrement buttons

## CScroll Base Class

### Core Methods

#### Creation
```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### Component Creation
```mql5
virtual bool CreateBack(void);      // Create background button
virtual bool CreateInc(void);       // Create increment button
virtual bool CreateDec(void);       // Create decrement button
virtual bool CreateThumb(void);     // Create draggable thumb
```

#### Position Management
```mql5
int MinPos() const;                     // Get minimum position
virtual bool MinPos(const int pos);    // Set minimum position

int MaxPos() const;                     // Get maximum position
virtual bool MaxPos(const int pos);    // Set maximum position

int CurrPos() const;                    // Get current position
virtual bool CurrPos(const int pos);   // Set current position

int CalcPos(const int coord);          // Calculate position from coordinate
```

### Event Handling

#### Core Events
```mql5
virtual bool OnEvent(const int id, const long &lparam,
                    const double &dparam, const string &sparam);
```

#### Button Events
```mql5
virtual bool OnClickInc(void);      // Increment button clicked
virtual bool OnClickDec(void);      // Decrement button clicked
```

#### Thumb Dragging
```mql5
virtual bool OnThumbDragStart(void);                        // Start thumb drag
virtual bool OnThumbDragProcess(const int x, const int y); // Process thumb drag
virtual bool OnThumbDragEnd(void);                         // End thumb drag
```

## CScrollV - Vertical Scroll Bar

### Usage Examples

#### Basic Vertical Scrolling
```mql5
#include <Controls\Scrolls.mqh>
#include <Controls\WndClient.mqh>

class CScrollablePanel : public CWndContainer
{
private:
    CWndClient  m_client_area;
    CScrollV    m_scroll_v;
    int         m_content_height;
    int         m_visible_height;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void SetContentHeight(const int height);
    void ScrollToPosition(const int pos);

private:
    bool CreateScrollV(void);
    void UpdateScrollRange(void);
    void OnScrollChange(void);
};

bool CScrollablePanel::Create(const long chart_id, const string name, const int subwin,
                             const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    m_visible_height = Height();
    m_content_height = Height();

    // Create client area
    if(!m_client_area.Create(chart_id, name+"Client", subwin,
                            0, 0, Width()-20, Height()))
        return false;
    Add(m_client_area);

    // Create vertical scroll bar
    return CreateScrollV();
}

bool CScrollablePanel::CreateScrollV(void)
{
    int scroll_width = 20;

    if(!m_scroll_v.Create(m_chart_id, m_name+"ScrollV", m_subwin,
                         Width()-scroll_width, 0, Width(), Height()))
        return false;

    m_scroll_v.MinPos(0);
    m_scroll_v.MaxPos(0);
    m_scroll_v.CurrPos(0);

    Add(m_scroll_v);
    UpdateScrollRange();

    return true;
}

void CScrollablePanel::SetContentHeight(const int height)
{
    m_content_height = height;
    UpdateScrollRange();
}

void CScrollablePanel::UpdateScrollRange(void)
{
    if(m_content_height > m_visible_height)
    {
        m_scroll_v.MaxPos(m_content_height - m_visible_height);
        m_scroll_v.Show();

        // Adjust client area width to account for scroll bar
        m_client_area.Resize(Width()-20, Height());
    }
    else
    {
        m_scroll_v.MaxPos(0);
        m_scroll_v.Hide();

        // Full width when no scroll bar
        m_client_area.Resize(Width(), Height());
    }
}

bool CScrollablePanel::OnEvent(const int id, const long &lparam,
                              const double &dparam, const string &sparam)
{
    // Handle scroll events
    if(id == CHARTEVENT_CUSTOM)
    {
        if(lparam == m_scroll_v.Id())
        {
            OnScrollChange();
            return true;
        }
    }

    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}

void CScrollablePanel::OnScrollChange(void)
{
    int scroll_pos = m_scroll_v.CurrPos();

    // Move client area content based on scroll position
    m_client_area.Move(0, -scroll_pos);

    Print("Scrolled to position: ", scroll_pos);
}

void CScrollablePanel::ScrollToPosition(const int pos)
{
    int clamped_pos = MathMax(m_scroll_v.MinPos(),
                             MathMin(m_scroll_v.MaxPos(), pos));
    m_scroll_v.CurrPos(clamped_pos);
    OnScrollChange();
}
```

## CScrollH - Horizontal Scroll Bar

### Usage Examples

#### Horizontal Content Scrolling
```mql5
class CHorizontalScrollPanel : public CWndContainer
{
private:
    CWndClient  m_client_area;
    CScrollH    m_scroll_h;
    int         m_content_width;
    int         m_visible_width;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void SetContentWidth(const int width);

private:
    bool CreateScrollH(void);
    void UpdateScrollRange(void);
    void OnScrollChange(void);
};

bool CHorizontalScrollPanel::Create(const long chart_id, const string name, const int subwin,
                                   const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    m_visible_width = Width();
    m_content_width = Width();

    // Create client area
    if(!m_client_area.Create(chart_id, name+"Client", subwin,
                            0, 0, Width(), Height()-20))
        return false;
    Add(m_client_area);

    return CreateScrollH();
}

bool CHorizontalScrollPanel::CreateScrollH(void)
{
    int scroll_height = 20;

    if(!m_scroll_h.Create(m_chart_id, m_name+"ScrollH", m_subwin,
                         0, Height()-scroll_height, Width(), Height()))
        return false;

    m_scroll_h.MinPos(0);
    m_scroll_h.MaxPos(0);
    m_scroll_h.CurrPos(0);

    Add(m_scroll_h);
    UpdateScrollRange();

    return true;
}

void CHorizontalScrollPanel::SetContentWidth(const int width)
{
    m_content_width = width;
    UpdateScrollRange();
}

void CHorizontalScrollPanel::UpdateScrollRange(void)
{
    if(m_content_width > m_visible_width)
    {
        m_scroll_h.MaxPos(m_content_width - m_visible_width);
        m_scroll_h.Show();
        m_client_area.Resize(Width(), Height()-20);
    }
    else
    {
        m_scroll_h.MaxPos(0);
        m_scroll_h.Hide();
        m_client_area.Resize(Width(), Height());
    }
}

void CHorizontalScrollPanel::OnScrollChange(void)
{
    int scroll_pos = m_scroll_h.CurrPos();
    m_client_area.Move(-scroll_pos, 0);
}
```

### Combined Vertical and Horizontal Scrolling
```mql5
class CFullScrollPanel : public CWndContainer
{
private:
    CWndClient  m_client_area;
    CScrollV    m_scroll_v;
    CScrollH    m_scroll_h;
    CPanel      m_corner_panel;

    int         m_content_width;
    int         m_content_height;
    int         m_visible_width;
    int         m_visible_height;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

    void SetContentSize(const int width, const int height);

private:
    bool CreateScrollBars(void);
    void UpdateScrollRanges(void);
    void UpdateClientArea(void);
    void OnVerticalScroll(void);
    void OnHorizontalScroll(void);
};

bool CFullScrollPanel::Create(const long chart_id, const string name, const int subwin,
                             const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndContainer::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    m_visible_width = Width();
    m_visible_height = Height();
    m_content_width = Width();
    m_content_height = Height();

    // Create client area
    if(!m_client_area.Create(chart_id, name+"Client", subwin, 0, 0, Width(), Height()))
        return false;
    Add(m_client_area);

    return CreateScrollBars();
}

bool CFullScrollPanel::CreateScrollBars(void)
{
    int scroll_size = 20;

    // Vertical scroll bar
    if(!m_scroll_v.Create(m_chart_id, m_name+"ScrollV", m_subwin,
                         Width()-scroll_size, 0, Width(), Height()-scroll_size))
        return false;
    Add(m_scroll_v);

    // Horizontal scroll bar
    if(!m_scroll_h.Create(m_chart_id, m_name+"ScrollH", m_subwin,
                         0, Height()-scroll_size, Width()-scroll_size, Height()))
        return false;
    Add(m_scroll_h);

    // Corner panel
    if(!m_corner_panel.Create(m_chart_id, m_name+"Corner", m_subwin,
                             Width()-scroll_size, Height()-scroll_size, Width(), Height()))
        return false;
    m_corner_panel.ColorBackground(CONTROLS_DIALOG_COLOR_BG);
    Add(m_corner_panel);

    UpdateScrollRanges();
    return true;
}

void CFullScrollPanel::SetContentSize(const int width, const int height)
{
    m_content_width = width;
    m_content_height = height;
    UpdateScrollRanges();
}

void CFullScrollPanel::UpdateScrollRanges(void)
{
    int scroll_size = 20;
    bool need_v_scroll = m_content_height > m_visible_height;
    bool need_h_scroll = m_content_width > m_visible_width;

    // Adjust visible area based on scroll bar visibility
    int available_width = Width() - (need_v_scroll ? scroll_size : 0);
    int available_height = Height() - (need_h_scroll ? scroll_size : 0);

    // Re-check if scroll bars are needed with adjusted sizes
    need_v_scroll = m_content_height > available_height;
    need_h_scroll = m_content_width > available_width;

    // Configure vertical scroll
    if(need_v_scroll)
    {
        m_scroll_v.MaxPos(m_content_height - available_height);
        m_scroll_v.Show();
    }
    else
    {
        m_scroll_v.MaxPos(0);
        m_scroll_v.CurrPos(0);
        m_scroll_v.Hide();
    }

    // Configure horizontal scroll
    if(need_h_scroll)
    {
        m_scroll_h.MaxPos(m_content_width - available_width);
        m_scroll_h.Show();
    }
    else
    {
        m_scroll_h.MaxPos(0);
        m_scroll_h.CurrPos(0);
        m_scroll_h.Hide();
    }

    // Show/hide corner panel
    if(need_v_scroll && need_h_scroll)
        m_corner_panel.Show();
    else
        m_corner_panel.Hide();

    UpdateClientArea();
}

void CFullScrollPanel::UpdateClientArea(void)
{
    int scroll_size = 20;
    int client_width = Width() - (m_scroll_v.IsVisible() ? scroll_size : 0);
    int client_height = Height() - (m_scroll_h.IsVisible() ? scroll_size : 0);

    m_client_area.Resize(client_width, client_height);

    // Position based on scroll positions
    int offset_x = -m_scroll_h.CurrPos();
    int offset_y = -m_scroll_v.CurrPos();
    m_client_area.Move(offset_x, offset_y);
}

bool CFullScrollPanel::OnEvent(const int id, const long &lparam,
                              const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_CUSTOM)
    {
        if(lparam == m_scroll_v.Id())
        {
            OnVerticalScroll();
            return true;
        }
        if(lparam == m_scroll_h.Id())
        {
            OnHorizontalScroll();
            return true;
        }
    }

    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}

void CFullScrollPanel::OnVerticalScroll(void)
{
    UpdateClientArea();
}

void CFullScrollPanel::OnHorizontalScroll(void)
{
    UpdateClientArea();
}
```

## Scroll Bar Customization

### Custom Scroll Step Size
```mql5
class CCustomScroll : public CScrollV
{
private:
    int m_step_size;

public:
    CCustomScroll() : m_step_size(10) {}
    void SetStepSize(const int step) { m_step_size = step; }
    virtual bool OnClickInc(void);
    virtual bool OnClickDec(void);
};

bool CCustomScroll::OnClickInc(void)
{
    int new_pos = CurrPos() + m_step_size;
    CurrPos(MathMin(new_pos, MaxPos()));
    return true;
}

bool CCustomScroll::OnClickDec(void)
{
    int new_pos = CurrPos() - m_step_size;
    CurrPos(MathMax(new_pos, MinPos()));
    return true;
}
```

### Smooth Scrolling
```mql5
class CSmoothScroll : public CScrollV
{
private:
    int m_target_pos;
    int m_animation_steps;
    int m_current_step;

public:
    void ScrollToSmooth(const int target_pos, const int steps = 10);
    void UpdateAnimation(void);
};

void CSmoothScroll::ScrollToSmooth(const int target_pos, const int steps = 10)
{
    m_target_pos = MathMax(MinPos(), MathMin(MaxPos(), target_pos));
    m_animation_steps = steps;
    m_current_step = 0;
}

void CSmoothScroll::UpdateAnimation(void)
{
    if(m_current_step < m_animation_steps)
    {
        int start_pos = CurrPos();
        int diff = m_target_pos - start_pos;
        int step_diff = diff / (m_animation_steps - m_current_step);

        CurrPos(start_pos + step_diff);
        m_current_step++;
    }
}
```

## Best Practices

1. **Range Management**
   - Always set appropriate MinPos and MaxPos values
   - Update ranges when content size changes
   - Handle edge cases (content smaller than container)

2. **Performance**
   - Update scroll position efficiently
   - Minimize redraws during scrolling
   - Use incremental updates for large content

3. **User Experience**
   - Provide smooth scrolling animations
   - Handle mouse wheel events
   - Show scroll bars only when needed

4. **Integration**
   - Coordinate with client area updates
   - Handle both vertical and horizontal scrolling
   - Manage corner panel visibility

## Common Use Cases

- **Long Lists** - Scrollable list views and data tables
- **Large Content** - Documents and text areas
- **Image Galleries** - Scrollable image collections
- **Data Grids** - Large datasets with row/column scrolling
- **Chart Areas** - Scrollable time series and data visualization

## See Also

- [CWndContainer](../foundation/CWndContainer.md) - Container base class
- [CWndClient](./CWndClient.md) - Client area management
- [CListView](./CListView.md) - Scrollable list control
- [Layout Management Tutorial](../tutorials/layout-management.md)