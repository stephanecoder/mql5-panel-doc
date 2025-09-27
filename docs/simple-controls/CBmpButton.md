# CBmpButton - Bitmap Image Button Control

The `CBmpButton` class provides a button control that displays bitmap images for different states, enabling rich visual interfaces with icons, graphics, and custom button designs.

## Class Overview

```mql5
class CBmpButton : public CWndObj
{
private:
    CChartObjectBmpLabel m_button;        // Underlying chart object
    int                  m_border;        // Border width
    string               m_bmp_off_name;  // BMP file for "OFF" state (default)
    string               m_bmp_on_name;   // BMP file for "ON" state
    string               m_bmp_passive_name; // BMP file for passive state
    string               m_bmp_active_name;  // BMP file for active state

public:
    virtual bool      Create(const long chart, const string name, const int subwin,
                             const int x1, const int y1, const int x2, const int y2);

    // Border management
    int               Border(void) const;
    bool              Border(const int value);

    // Bitmap management
    bool              BmpNames(const string off="", const string on="");
    string            BmpOffName(void) const;
    bool              BmpOffName(const string name);
    string            BmpOnName(void) const;
    bool              BmpOnName(const string name);
    string            BmpPassiveName(void) const;
    bool              BmpPassiveName(const string name);
    string            BmpActiveName(void) const;
    bool              BmpActiveName(const string name);

    // State management
    bool              Pressed(void) const;
    bool              Pressed(const bool pressed);
    bool              Locking(void) const;
    void              Locking(const bool locking);
};
```

## Key Features

### 1. **Multiple State Images**
- **OFF State**: Default unpressed state
- **ON State**: Pressed/selected state
- **Passive State**: Inactive/disabled appearance
- **Active State**: Hover/focus appearance

### 2. **Flexible Sizing**
- Automatic sizing based on bitmap dimensions
- Custom sizing with scaling
- Border management for visual effects

### 3. **Toggle Functionality**
- Locking mode for toggle buttons
- Non-locking mode for momentary buttons
- Pressed state management

## Basic Usage

### Creating a Simple Icon Button

```mql5
class CToolbarPanel : public CAppDialog
{
private:
    CBmpButton m_save_button;
    CBmpButton m_load_button;
    CBmpButton m_settings_button;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

protected:
    bool CreateToolbarButtons();
    void OnSaveClick();
    void OnLoadClick();
    void OnSettingsClick();
};

bool CToolbarPanel::Create(const long chart_id, const string name, const int subwin,
                          const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreateToolbarButtons();
}

bool CToolbarPanel::CreateToolbarButtons()
{
    int x = 10, y = 10;
    int button_size = 32;
    int spacing = 5;

    // Save button
    if(!m_save_button.Create(ChartID(), Name() + "Save", 0,
                            x, y, x + button_size, y + button_size))
        return false;

    // Set bitmap images for save button
    m_save_button.BmpOffName("::Images\\save_off.bmp");
    m_save_button.BmpOnName("::Images\\save_on.bmp");
    m_save_button.BmpPassiveName("::Images\\save_disabled.bmp");
    m_save_button.Border(1);

    if(!Add(m_save_button))
        return false;

    x += button_size + spacing;

    // Load button
    if(!m_load_button.Create(ChartID(), Name() + "Load", 0,
                            x, y, x + button_size, y + button_size))
        return false;

    m_load_button.BmpOffName("::Images\\load_off.bmp");
    m_load_button.BmpOnName("::Images\\load_on.bmp");
    m_load_button.Border(1);

    if(!Add(m_load_button))
        return false;

    x += button_size + spacing;

    // Settings button (toggle mode)
    if(!m_settings_button.Create(ChartID(), Name() + "Settings", 0,
                                x, y, x + button_size, y + button_size))
        return false;

    m_settings_button.BmpOffName("::Images\\settings_off.bmp");
    m_settings_button.BmpOnName("::Images\\settings_on.bmp");
    m_settings_button.Locking(true); // Enable toggle mode
    m_settings_button.Border(1);

    return Add(m_settings_button);
}

// Event handling
EVENT_MAP_BEGIN(CToolbarPanel)
    ON_EVENT(ON_CLICK, m_save_button, OnSaveClick)
    ON_EVENT(ON_CLICK, m_load_button, OnLoadClick)
    ON_EVENT(ON_CLICK, m_settings_button, OnSettingsClick)
EVENT_MAP_END(CAppDialog)

void CToolbarPanel::OnSaveClick()
{
    Print("Save button clicked");
    // Implement save functionality
}

void CToolbarPanel::OnSettingsClick()
{
    if(m_settings_button.Pressed())
    {
        Print("Settings panel opened");
        // Show settings panel
    }
    else
    {
        Print("Settings panel closed");
        // Hide settings panel
    }
}
```

## Advanced Usage Patterns

### 1. **Dynamic Button States**

```mql5
class CConnectionStatusPanel : public CAppDialog
{
private:
    CBmpButton m_connection_indicator;
    CBmpButton m_connect_button;

    bool m_is_connected;

public:
    void UpdateConnectionStatus(bool connected);
    void SetConnectionIndicator();

protected:
    bool CreateConnectionControls();
    void OnConnectClick();
    void OnConnectionStateChange();
};

bool CConnectionStatusPanel::CreateConnectionControls()
{
    // Connection status indicator
    if(!m_connection_indicator.Create(ChartID(), Name() + "Status", 0,
                                     10, 10, 42, 42))
        return false;

    // Set indicator images
    m_connection_indicator.BmpOffName("::Images\\disconnected.bmp");
    m_connection_indicator.BmpOnName("::Images\\connected.bmp");
    m_connection_indicator.BmpPassiveName("::Images\\connecting.bmp");
    m_connection_indicator.Locking(true);

    if(!Add(m_connection_indicator))
        return false;

    // Connect/Disconnect button
    if(!m_connect_button.Create(ChartID(), Name() + "Connect", 0,
                               60, 10, 120, 42))
        return false;

    SetConnectionIndicator();
    return Add(m_connect_button);
}

void CConnectionStatusPanel::UpdateConnectionStatus(bool connected)
{
    m_is_connected = connected;
    SetConnectionIndicator();
}

void CConnectionStatusPanel::SetConnectionIndicator()
{
    if(m_is_connected)
    {
        // Connected state
        m_connection_indicator.Pressed(true);
        m_connect_button.BmpOffName("::Images\\disconnect_off.bmp");
        m_connect_button.BmpOnName("::Images\\disconnect_on.bmp");
    }
    else
    {
        // Disconnected state
        m_connection_indicator.Pressed(false);
        m_connect_button.BmpOffName("::Images\\connect_off.bmp");
        m_connect_button.BmpOnName("::Images\\connect_on.bmp");
    }
}

EVENT_MAP_BEGIN(CConnectionStatusPanel)
    ON_EVENT(ON_CLICK, m_connect_button, OnConnectClick)
EVENT_MAP_END(CAppDialog)

void CConnectionStatusPanel::OnConnectClick()
{
    if(m_is_connected)
    {
        // Disconnect
        Print("Disconnecting...");
        // Show connecting state
        m_connection_indicator.BmpOffName("::Images\\connecting.bmp");
        // Perform disconnection logic
        UpdateConnectionStatus(false);
    }
    else
    {
        // Connect
        Print("Connecting...");
        // Show connecting state
        m_connection_indicator.BmpOffName("::Images\\connecting.bmp");
        // Perform connection logic
        UpdateConnectionStatus(true);
    }
}
```

### 2. **Navigation Button Panel**

```mql5
class CNavigationPanel : public CAppDialog
{
private:
    CBmpButton m_nav_buttons[4]; // Up, Down, Left, Right
    CBmpButton m_home_button;
    CBmpButton m_zoom_in_button;
    CBmpButton m_zoom_out_button;

public:
    enum ENUM_NAV_DIRECTION
    {
        NAV_UP,
        NAV_DOWN,
        NAV_LEFT,
        NAV_RIGHT
    };

protected:
    bool CreateNavigationButtons();
    void OnNavigationClick(ENUM_NAV_DIRECTION direction);
    void OnHomeClick();
    void OnZoomInClick();
    void OnZoomOutClick();
    void MoveChart(ENUM_NAV_DIRECTION direction);
};

bool CNavigationPanel::CreateNavigationButtons()
{
    int center_x = 60, center_y = 60;
    int button_size = 24;
    int offset = 30;

    // Navigation buttons in cross pattern
    string nav_images[4][2] = {
        {"::Images\\arrow_up_off.bmp", "::Images\\arrow_up_on.bmp"},     // UP
        {"::Images\\arrow_down_off.bmp", "::Images\\arrow_down_on.bmp"}, // DOWN
        {"::Images\\arrow_left_off.bmp", "::Images\\arrow_left_on.bmp"}, // LEFT
        {"::Images\\arrow_right_off.bmp", "::Images\\arrow_right_on.bmp"} // RIGHT
    };

    int positions[4][2] = {
        {center_x - button_size/2, center_y - offset},              // UP
        {center_x - button_size/2, center_y + offset - button_size}, // DOWN
        {center_x - offset, center_y - button_size/2},              // LEFT
        {center_x + offset - button_size, center_y - button_size/2}  // RIGHT
    };

    for(int i = 0; i < 4; i++)
    {
        if(!m_nav_buttons[i].Create(ChartID(), Name() + "Nav" + IntegerToString(i), 0,
                                   positions[i][0], positions[i][1],
                                   positions[i][0] + button_size,
                                   positions[i][1] + button_size))
            return false;

        m_nav_buttons[i].BmpOffName(nav_images[i][0]);
        m_nav_buttons[i].BmpOnName(nav_images[i][1]);
        m_nav_buttons[i].Border(0);

        if(!Add(m_nav_buttons[i]))
            return false;
    }

    // Home button in center
    if(!m_home_button.Create(ChartID(), Name() + "Home", 0,
                            center_x - button_size/2, center_y - button_size/2,
                            center_x + button_size/2, center_y + button_size/2))
        return false;

    m_home_button.BmpOffName("::Images\\home_off.bmp");
    m_home_button.BmpOnName("::Images\\home_on.bmp");

    if(!Add(m_home_button))
        return false;

    // Zoom buttons
    if(!m_zoom_in_button.Create(ChartID(), Name() + "ZoomIn", 0,
                               10, center_y - button_size/2,
                               10 + button_size, center_y + button_size/2))
        return false;

    m_zoom_in_button.BmpOffName("::Images\\zoom_in_off.bmp");
    m_zoom_in_button.BmpOnName("::Images\\zoom_in_on.bmp");

    if(!Add(m_zoom_in_button))
        return false;

    if(!m_zoom_out_button.Create(ChartID(), Name() + "ZoomOut", 0,
                                10, center_y + button_size/2 + 5,
                                10 + button_size, center_y + button_size + 5))
        return false;

    m_zoom_out_button.BmpOffName("::Images\\zoom_out_off.bmp");
    m_zoom_out_button.BmpOnName("::Images\\zoom_out_on.bmp");

    return Add(m_zoom_out_button);
}

EVENT_MAP_BEGIN(CNavigationPanel)
    ON_INDEXED_EVENT(ON_CLICK, m_nav_buttons, OnNavigationIndexClick)
    ON_EVENT(ON_CLICK, m_home_button, OnHomeClick)
    ON_EVENT(ON_CLICK, m_zoom_in_button, OnZoomInClick)
    ON_EVENT(ON_CLICK, m_zoom_out_button, OnZoomOutClick)
EVENT_MAP_END(CAppDialog)

bool OnNavigationIndexClick(int index)
{
    OnNavigationClick((ENUM_NAV_DIRECTION)index);
    return true;
}

void CNavigationPanel::OnNavigationClick(ENUM_NAV_DIRECTION direction)
{
    MoveChart(direction);
}

void CNavigationPanel::MoveChart(ENUM_NAV_DIRECTION direction)
{
    // Get current chart scale and position
    double chart_scale = ChartGetDouble(ChartID(), CHART_SCALE);
    datetime first_bar = (datetime)ChartGetInteger(ChartID(), CHART_FIRST_VISIBLE_BAR);

    switch(direction)
    {
        case NAV_UP:
            // Zoom in
            ChartSetDouble(ChartID(), CHART_SCALE, chart_scale + 1);
            break;

        case NAV_DOWN:
            // Zoom out
            if(chart_scale > 0)
                ChartSetDouble(ChartID(), CHART_SCALE, chart_scale - 1);
            break;

        case NAV_LEFT:
            // Move left (back in time)
            ChartNavigate(ChartID(), CHART_BEGIN, -50);
            break;

        case NAV_RIGHT:
            // Move right (forward in time)
            ChartNavigate(ChartID(), CHART_BEGIN, 50);
            break;
    }

    ChartRedraw(ChartID());
}
```

### 3. **Trading Action Panel**

```mql5
class CTradingActionPanel : public CAppDialog
{
private:
    CBmpButton m_buy_button;
    CBmpButton m_sell_button;
    CBmpButton m_close_all_button;
    CBmpButton m_emergency_stop_button;

    bool m_trading_enabled;
    bool m_emergency_mode;

public:
    void SetTradingEnabled(bool enabled);
    void SetEmergencyMode(bool emergency);

protected:
    bool CreateTradingButtons();
    void UpdateButtonStates();
    void OnBuyClick();
    void OnSellClick();
    void OnCloseAllClick();
    void OnEmergencyStopClick();
};

bool CTradingActionPanel::CreateTradingButtons()
{
    int x = 10, y = 10;
    int button_width = 60, button_height = 40;
    int spacing = 5;

    // Buy button
    if(!m_buy_button.Create(ChartID(), Name() + "Buy", 0,
                           x, y, x + button_width, y + button_height))
        return false;

    m_buy_button.BmpOffName("::Images\\buy_off.bmp");
    m_buy_button.BmpOnName("::Images\\buy_on.bmp");
    m_buy_button.BmpPassiveName("::Images\\buy_disabled.bmp");
    m_buy_button.Border(2);

    if(!Add(m_buy_button))
        return false;

    x += button_width + spacing;

    // Sell button
    if(!m_sell_button.Create(ChartID(), Name() + "Sell", 0,
                            x, y, x + button_width, y + button_height))
        return false;

    m_sell_button.BmpOffName("::Images\\sell_off.bmp");
    m_sell_button.BmpOnName("::Images\\sell_on.bmp");
    m_sell_button.BmpPassiveName("::Images\\sell_disabled.bmp");
    m_sell_button.Border(2);

    if(!Add(m_sell_button))
        return false;

    // Next row
    x = 10;
    y += button_height + spacing;

    // Close all button
    if(!m_close_all_button.Create(ChartID(), Name() + "CloseAll", 0,
                                 x, y, x + button_width, y + button_height))
        return false;

    m_close_all_button.BmpOffName("::Images\\close_all_off.bmp");
    m_close_all_button.BmpOnName("::Images\\close_all_on.bmp");
    m_close_all_button.Border(2);

    if(!Add(m_close_all_button))
        return false;

    x += button_width + spacing;

    // Emergency stop button
    if(!m_emergency_stop_button.Create(ChartID(), Name() + "Emergency", 0,
                                      x, y, x + button_width, y + button_height))
        return false;

    m_emergency_stop_button.BmpOffName("::Images\\emergency_off.bmp");
    m_emergency_stop_button.BmpOnName("::Images\\emergency_on.bmp");
    m_emergency_stop_button.Locking(true); // Toggle button
    m_emergency_stop_button.Border(3);

    if(!Add(m_emergency_stop_button))
        return false;

    UpdateButtonStates();
    return true;
}

void CTradingActionPanel::SetTradingEnabled(bool enabled)
{
    m_trading_enabled = enabled;
    UpdateButtonStates();
}

void CTradingActionPanel::UpdateButtonStates()
{
    // Enable/disable trading buttons based on state
    if(m_trading_enabled && !m_emergency_mode)
    {
        // Normal trading mode - all buttons enabled
        m_buy_button.BmpOffName("::Images\\buy_off.bmp");
        m_sell_button.BmpOffName("::Images\\sell_off.bmp");
        m_close_all_button.BmpOffName("::Images\\close_all_off.bmp");
    }
    else
    {
        // Disabled mode - show disabled images
        m_buy_button.BmpOffName("::Images\\buy_disabled.bmp");
        m_sell_button.BmpOffName("::Images\\sell_disabled.bmp");
        m_close_all_button.BmpOffName("::Images\\close_all_disabled.bmp");
    }

    // Emergency button state
    m_emergency_stop_button.Pressed(m_emergency_mode);
}

EVENT_MAP_BEGIN(CTradingActionPanel)
    ON_EVENT(ON_CLICK, m_buy_button, OnBuyClick)
    ON_EVENT(ON_CLICK, m_sell_button, OnSellClick)
    ON_EVENT(ON_CLICK, m_close_all_button, OnCloseAllClick)
    ON_EVENT(ON_CLICK, m_emergency_stop_button, OnEmergencyStopClick)
EVENT_MAP_END(CAppDialog)

void CTradingActionPanel::OnEmergencyStopClick()
{
    m_emergency_mode = m_emergency_stop_button.Pressed();

    if(m_emergency_mode)
    {
        Print("EMERGENCY STOP ACTIVATED!");
        // Close all positions immediately
        CloseAllPositions();
        // Disable all trading
        SetTradingEnabled(false);
    }
    else
    {
        Print("Emergency stop deactivated");
        // Re-enable trading if conditions allow
        SetTradingEnabled(true);
    }

    UpdateButtonStates();
}
```

## Resource Management and Bitmap Files

### Loading Embedded Resources

```mql5
class CBitmapResourceManager
{
public:
    static bool LoadEmbeddedBitmaps();
    static string GetResourcePath(const string resource_name);

    // Embedded bitmap resources
    #resource "Images\\save_off.bmp"
    #resource "Images\\save_on.bmp"
    #resource "Images\\load_off.bmp"
    #resource "Images\\load_on.bmp"
    #resource "Images\\settings_off.bmp"
    #resource "Images\\settings_on.bmp"
};

bool CBitmapResourceManager::LoadEmbeddedBitmaps()
{
    // Resources are automatically available when embedded
    // Verify resource availability
    return ResourceReadImage("::Images\\save_off.bmp") != INVALID_HANDLE;
}

string CBitmapResourceManager::GetResourcePath(const string resource_name)
{
    return "::" + resource_name;
}
```

### Creating Custom Bitmap Buttons

```mql5
class CCustomBmpButton : public CBmpButton
{
private:
    color m_base_color;
    string m_icon_text;

public:
    void SetBaseColor(color clr) { m_base_color = clr; }
    void SetIconText(string text) { m_icon_text = text; }

protected:
    bool GenerateCustomBitmaps();
    void CreateButtonBitmap(const string filename, color bg_color, color text_color);
};

bool CCustomBmpButton::GenerateCustomBitmaps()
{
    // Generate bitmaps programmatically
    string off_file = "custom_off_" + Name() + ".bmp";
    string on_file = "custom_on_" + Name() + ".bmp";

    CreateButtonBitmap(off_file, m_base_color, clrBlack);
    CreateButtonBitmap(on_file, ColorDarken(m_base_color, 20), clrWhite);

    BmpOffName(off_file);
    BmpOnName(on_file);

    return true;
}

void CCustomBmpButton::CreateButtonBitmap(const string filename,
                                         color bg_color, color text_color)
{
    // Create bitmap programmatically
    // This would require additional bitmap manipulation functions
    // or integration with external bitmap generation tools
}
```

## Best Practices

### 1. **Image Optimization**
- Use appropriate image sizes (16x16, 24x24, 32x32 for common use cases)
- Optimize file sizes for faster loading
- Provide images for all states to avoid visual inconsistencies

### 2. **State Management**
- Always define both OFF and ON state images
- Use passive state images for disabled controls
- Implement consistent visual feedback

### 3. **Resource Organization**
- Group related bitmap files in resource folders
- Use consistent naming conventions
- Embed resources to avoid external file dependencies

### 4. **Performance Considerations**
- Load images once during initialization
- Cache bitmap handles when possible
- Avoid frequent image switching

The CBmpButton control provides powerful visual capabilities for creating professional, icon-based interfaces that enhance user experience and provide intuitive interaction patterns.

## See Also

- [Simple Controls Overview](./README.md) - Overview of all simple controls
- [CButton Documentation](./CButton.md) - Standard text button control
- [CPanel Documentation](./CPanel.md) - Container controls
- [Advanced Patterns Guide](../tutorials/advanced-patterns.md) - Dynamic control techniques