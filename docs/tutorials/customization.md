# Panel Customization and Theming

This guide covers advanced customization techniques for MQL5 Control panels, including constant overrides, custom styling, behavior modification, and creating branded interfaces.

## Constant Override Techniques

### Using #undef to Override Library Constants

The MQL5 Controls Library uses predefined constants for colors, sizes, and other properties. You can customize these by undefining and redefining them:

```mql5
#include <Controls\Dialog.mqh>

// Override default dialog colors
#undef CONTROLS_DIALOG_COLOR_BG
#undef CONTROLS_DIALOG_COLOR_CLIENT_BG
#undef CONTROLS_DIALOG_COLOR_CAPTION_BG

// Define custom colors
#define CONTROLS_DIALOG_COLOR_BG           0x2D2D30    // Dark gray
#define CONTROLS_DIALOG_COLOR_CLIENT_BG    0x1E1E1E    // Darker gray
#define CONTROLS_DIALOG_COLOR_CAPTION_BG   0x007ACC    // Blue accent

// Override button colors
#undef CONTROLS_BUTTON_COLOR_BG
#undef CONTROLS_BUTTON_COLOR_PRESSED

#define CONTROLS_BUTTON_COLOR_BG           0x404040    // Medium gray
#define CONTROLS_BUTTON_COLOR_PRESSED      0x007ACC    // Blue when pressed

// Override default fonts
#undef CONTROLS_FONT_NAME
#undef CONTROLS_FONT_SIZE_NORMAL

#define CONTROLS_FONT_NAME                 "Segoe UI"
#define CONTROLS_FONT_SIZE_NORMAL          10

// Custom panel dimensions
#undef CONTROLS_DIALOG_MIN_WIDTH
#undef CONTROLS_DIALOG_MIN_HEIGHT

#define CONTROLS_DIALOG_MIN_WIDTH          250
#define CONTROLS_DIALOG_MIN_HEIGHT         180
```

### Complete Dark Theme Implementation

```mql5
//+------------------------------------------------------------------+
//|                                               DarkThemePanel.mq5 |
//+------------------------------------------------------------------+

// Dark theme color palette
#undef CONTROLS_DIALOG_COLOR_BG
#undef CONTROLS_DIALOG_COLOR_CLIENT_BG
#undef CONTROLS_DIALOG_COLOR_CAPTION_BG
#undef CONTROLS_DIALOG_COLOR_BORDER_LIGHT
#undef CONTROLS_DIALOG_COLOR_BORDER_DARK

#define CONTROLS_DIALOG_COLOR_BG           0x2D2D30
#define CONTROLS_DIALOG_COLOR_CLIENT_BG    0x252526
#define CONTROLS_DIALOG_COLOR_CAPTION_BG   0x3C3C3C
#define CONTROLS_DIALOG_COLOR_BORDER_LIGHT 0x434343
#define CONTROLS_DIALOG_COLOR_BORDER_DARK  0x1B1B1C

// Button theme
#undef CONTROLS_BUTTON_COLOR_BG
#undef CONTROLS_BUTTON_COLOR_PRESSED

#define CONTROLS_BUTTON_COLOR_BG           0x333337
#define CONTROLS_BUTTON_COLOR_PRESSED      0x094771

// Edit control theme
#undef CONTROLS_EDIT_COLOR_BG
#undef CONTROLS_EDIT_COLOR_BORDER

#define CONTROLS_EDIT_COLOR_BG             0x1E1E1E
#define CONTROLS_EDIT_COLOR_BORDER         0x464647

#include <Controls\Dialog.mqh>
#include <Controls\Button.mqh>
#include <Controls\Edit.mqh>
#include <Controls\Label.mqh>

class CDarkThemePanel : public CAppDialog
{
private:
    CLabel  m_label_title;
    CEdit   m_edit_input;
    CButton m_button_primary;
    CButton m_button_secondary;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

protected:
    bool CreateControls();
    void ApplyDarkTheme();
};

bool CDarkThemePanel::Create(const long chart_id, const string name, const int subwin,
                            const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    if(!CreateControls())
        return false;

    ApplyDarkTheme();
    return true;
}

bool CDarkThemePanel::CreateControls()
{
    int x1 = 10, y1 = 30, x2 = 200, y2 = 50;

    // Title label
    if(!m_label_title.Create(ChartID(), Name() + "Title", 0, x1, y1, x2, y2))
        return false;
    if(!Add(m_label_title))
        return false;
    m_label_title.Text("Dark Theme Panel");

    // Input field
    y1 += 30; y2 += 30;
    if(!m_edit_input.Create(ChartID(), Name() + "Input", 0, x1, y1, x2, y2))
        return false;
    if(!Add(m_edit_input))
        return false;

    // Primary button
    y1 += 40; y2 = y1 + 25;
    x2 = x1 + 90;
    if(!m_button_primary.Create(ChartID(), Name() + "Primary", 0, x1, y1, x2, y2))
        return false;
    if(!Add(m_button_primary))
        return false;
    m_button_primary.Text("Primary");

    // Secondary button
    x1 += 100; x2 += 100;
    if(!m_button_secondary.Create(ChartID(), Name() + "Secondary", 0, x1, y1, x2, y2))
        return false;
    if(!Add(m_button_secondary))
        return false;
    m_button_secondary.Text("Secondary");

    return true;
}

void CDarkThemePanel::ApplyDarkTheme()
{
    // Apply dark text colors
    m_label_title.Color(0xFFFFFF);        // White text
    m_label_title.Font("Segoe UI");
    m_label_title.FontSize(11);

    // Style buttons with custom colors
    m_button_primary.Color(0xFFFFFF);     // White text
    m_button_primary.ColorBackground(0x0E639C);  // Blue background

    m_button_secondary.Color(0xFFFFFF);   // White text
    m_button_secondary.ColorBackground(0x333337); // Gray background

    // Edit control styling is handled by constants
}
```

## Custom Control Styling

### Creating Branded Controls

```mql5
//--- Brand color palette
#define BRAND_PRIMARY          0xFF6B35    // Orange
#define BRAND_SECONDARY        0x004B87    // Blue
#define BRAND_SUCCESS          0x28A745    // Green
#define BRAND_WARNING          0xFFC107    // Yellow
#define BRAND_DANGER           0xDC3545    // Red
#define BRAND_LIGHT            0xF8F9FA    // Light gray
#define BRAND_DARK             0x343A40    // Dark gray

class CBrandedButton : public CButton
{
private:
    ENUM_BUTTON_STYLE m_style;

public:
    enum ENUM_BUTTON_STYLE
    {
        STYLE_PRIMARY,
        STYLE_SECONDARY,
        STYLE_SUCCESS,
        STYLE_WARNING,
        STYLE_DANGER,
        STYLE_LIGHT,
        STYLE_DARK
    };

    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2,
                       const ENUM_BUTTON_STYLE style = STYLE_PRIMARY);

    void SetStyle(const ENUM_BUTTON_STYLE style);

protected:
    void ApplyStyle();
};

bool CBrandedButton::Create(const long chart_id, const string name, const int subwin,
                           const int x1, const int y1, const int x2, const int y2,
                           const ENUM_BUTTON_STYLE style)
{
    if(!CButton::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    m_style = style;
    ApplyStyle();
    return true;
}

void CBrandedButton::SetStyle(const ENUM_BUTTON_STYLE style)
{
    m_style = style;
    ApplyStyle();
}

void CBrandedButton::ApplyStyle()
{
    color bg_color, text_color;

    switch(m_style)
    {
        case STYLE_PRIMARY:
            bg_color = BRAND_PRIMARY;
            text_color = clrWhite;
            break;

        case STYLE_SECONDARY:
            bg_color = BRAND_SECONDARY;
            text_color = clrWhite;
            break;

        case STYLE_SUCCESS:
            bg_color = BRAND_SUCCESS;
            text_color = clrWhite;
            break;

        case STYLE_WARNING:
            bg_color = BRAND_WARNING;
            text_color = BRAND_DARK;
            break;

        case STYLE_DANGER:
            bg_color = BRAND_DANGER;
            text_color = clrWhite;
            break;

        case STYLE_LIGHT:
            bg_color = BRAND_LIGHT;
            text_color = BRAND_DARK;
            break;

        case STYLE_DARK:
            bg_color = BRAND_DARK;
            text_color = clrWhite;
            break;

        default:
            bg_color = BRAND_PRIMARY;
            text_color = clrWhite;
    }

    ColorBackground(bg_color);
    Color(text_color);
    Font("Segoe UI");
    FontSize(9);
}
```

## Behavior Modification

### Disabling Default Behaviors

```mql5
class CCustomDialog : public CAppDialog
{
public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    // Override to disable minimize functionality
    virtual bool Minimize() { return false; }

    // Override to customize close behavior
    virtual bool OnClickButtonClose();

    // Override to prevent moving
    virtual bool OnDialogDragStart() { return false; }

    // Custom resize behavior
    virtual bool OnDialogDragProcess();

protected:
    bool m_allow_resize;
    bool m_confirm_close;

public:
    void AllowResize(const bool allow) { m_allow_resize = allow; }
    void ConfirmClose(const bool confirm) { m_confirm_close = confirm; }
};

bool CCustomDialog::OnClickButtonClose()
{
    if(m_confirm_close)
    {
        int result = MessageBox("Are you sure you want to close this panel?",
                               "Confirm Close", MB_YESNO | MB_ICONQUESTION);
        if(result != IDYES)
            return false;
    }

    return CAppDialog::OnClickButtonClose();
}

bool CCustomDialog::OnDialogDragProcess()
{
    if(!m_allow_resize)
        return false;

    return CAppDialog::OnDialogDragProcess();
}
```

### Custom Drawing and Graphics

```mql5
class CGraphicalPanel : public CAppDialog
{
private:
    string m_background_resource;
    color  m_gradient_start;
    color  m_gradient_end;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    void SetBackgroundGradient(const color start_color, const color end_color);
    void SetBackgroundImage(const string resource_name);

protected:
    virtual void DrawBackground();
    void CreateGradientBackground();
    void CreateImageBackground();
};

void CGraphicalPanel::SetBackgroundGradient(const color start_color, const color end_color)
{
    m_gradient_start = start_color;
    m_gradient_end = end_color;
    m_background_resource = "";
    DrawBackground();
}

void CGraphicalPanel::DrawBackground()
{
    if(StringLen(m_background_resource) > 0)
        CreateImageBackground();
    else if(m_gradient_start != clrNONE && m_gradient_end != clrNONE)
        CreateGradientBackground();
}

void CGraphicalPanel::CreateGradientBackground()
{
    string gradient_name = Name() + "_Gradient";
    int width = Width();
    int height = Height();

    // Create gradient effect using multiple rectangles
    for(int i = 0; i < height; i++)
    {
        double factor = (double)i / height;
        color blend_color = RGB(
            (int)((1.0 - factor) * (m_gradient_start & 0xFF) + factor * (m_gradient_end & 0xFF)),
            (int)((1.0 - factor) * ((m_gradient_start >> 8) & 0xFF) + factor * ((m_gradient_end >> 8) & 0xFF)),
            (int)((1.0 - factor) * ((m_gradient_start >> 16) & 0xFF) + factor * ((m_gradient_end >> 16) & 0xFF))
        );

        string line_name = gradient_name + "_" + IntegerToString(i);
        ObjectCreate(ChartID(), line_name, OBJ_RECTANGLE_LABEL, 0, 0, 0);
        ObjectSetInteger(ChartID(), line_name, OBJPROP_XDISTANCE, Left());
        ObjectSetInteger(ChartID(), line_name, OBJPROP_YDISTANCE, Top() + i);
        ObjectSetInteger(ChartID(), line_name, OBJPROP_XSIZE, width);
        ObjectSetInteger(ChartID(), line_name, OBJPROP_YSIZE, 1);
        ObjectSetInteger(ChartID(), line_name, OBJPROP_BGCOLOR, blend_color);
        ObjectSetInteger(ChartID(), line_name, OBJPROP_BORDER_TYPE, BORDER_FLAT);
        ObjectSetInteger(ChartID(), line_name, OBJPROP_BACK, true);
    }
}
```

## Theme System Implementation

### Complete Theme Manager

```mql5
class CThemeManager
{
private:
    static ENUM_THEME_TYPE s_current_theme;

public:
    enum ENUM_THEME_TYPE
    {
        THEME_LIGHT,
        THEME_DARK,
        THEME_BLUE,
        THEME_CUSTOM
    };

    static void SetTheme(const ENUM_THEME_TYPE theme);
    static ENUM_THEME_TYPE GetCurrentTheme() { return s_current_theme; }

    static color GetDialogBackground();
    static color GetClientBackground();
    static color GetCaptionBackground();
    static color GetButtonBackground();
    static color GetButtonPressed();
    static color GetEditBackground();
    static color GetTextColor();
    static string GetFontName();
    static int GetFontSize();

protected:
    static void ApplyLightTheme();
    static void ApplyDarkTheme();
    static void ApplyBlueTheme();
};

ENUM_THEME_TYPE CThemeManager::s_current_theme = THEME_LIGHT;

void CThemeManager::SetTheme(const ENUM_THEME_TYPE theme)
{
    s_current_theme = theme;

    switch(theme)
    {
        case THEME_LIGHT:
            ApplyLightTheme();
            break;

        case THEME_DARK:
            ApplyDarkTheme();
            break;

        case THEME_BLUE:
            ApplyBlueTheme();
            break;
    }
}

color CThemeManager::GetDialogBackground()
{
    switch(s_current_theme)
    {
        case THEME_LIGHT:  return 0xF0F0F0;
        case THEME_DARK:   return 0x2D2D30;
        case THEME_BLUE:   return 0xE6F3FF;
        default:           return 0xF0F0F0;
    }
}

color CThemeManager::GetTextColor()
{
    switch(s_current_theme)
    {
        case THEME_LIGHT:  return clrBlack;
        case THEME_DARK:   return clrWhite;
        case THEME_BLUE:   return 0x003366;
        default:           return clrBlack;
    }
}

// Themed panel implementation
class CThemedPanel : public CAppDialog
{
public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    void ApplyTheme();
    void SetTheme(const CThemeManager::ENUM_THEME_TYPE theme);

protected:
    void UpdateControlThemes();
};

bool CThemedPanel::Create(const long chart_id, const string name, const int subwin,
                         const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    ApplyTheme();
    return true;
}

void CThemedPanel::SetTheme(const CThemeManager::ENUM_THEME_TYPE theme)
{
    CThemeManager::SetTheme(theme);
    ApplyTheme();
}

void CThemedPanel::ApplyTheme()
{
    // Apply dialog colors
    ColorBackground(CThemeManager::GetDialogBackground());
    Color(CThemeManager::GetTextColor());

    // Update all child controls
    UpdateControlThemes();

    // Redraw
    ChartRedraw();
}

void CThemedPanel::UpdateControlThemes()
{
    for(int i = 0; i < ControlsTotal(); i++)
    {
        CWnd* control = Control(i);
        if(!control)
            continue;

        // Apply theme to different control types
        CButton* button = dynamic_cast<CButton*>(control);
        if(button)
        {
            button->ColorBackground(CThemeManager::GetButtonBackground());
            button->Color(CThemeManager::GetTextColor());
            button->Font(CThemeManager::GetFontName());
            continue;
        }

        CLabel* label = dynamic_cast<CLabel*>(control);
        if(label)
        {
            label->Color(CThemeManager::GetTextColor());
            label->Font(CThemeManager::GetFontName());
            continue;
        }

        CEdit* edit = dynamic_cast<CEdit*>(control);
        if(edit)
        {
            edit->ColorBackground(CThemeManager::GetEditBackground());
            edit->Color(CThemeManager::GetTextColor());
            edit->Font(CThemeManager::GetFontName());
            continue;
        }
    }
}
```

## Configuration and Settings

### Persistent Theme Settings

```mql5
class CThemeSettings
{
private:
    static string s_settings_file;

public:
    static bool SaveThemeSettings();
    static bool LoadThemeSettings();
    static void SetSettingsFile(const string filename);

    // Theme properties
    static void SetCustomColors(const color dialog_bg, const color text_color,
                               const color button_bg, const color edit_bg);
    static void SetCustomFont(const string font_name, const int font_size);

protected:
    static string ColorToString(const color clr);
    static color StringToColor(const string str);
};

string CThemeSettings::s_settings_file = "panel_theme.ini";

bool CThemeSettings::SaveThemeSettings()
{
    int file_handle = FileOpen(s_settings_file, FILE_WRITE | FILE_TXT);
    if(file_handle == INVALID_HANDLE)
        return false;

    // Save current theme type
    FileWrite(file_handle, "Theme=" + IntegerToString(CThemeManager::GetCurrentTheme()));

    // Save colors
    FileWrite(file_handle, "DialogBG=" + ColorToString(CThemeManager::GetDialogBackground()));
    FileWrite(file_handle, "TextColor=" + ColorToString(CThemeManager::GetTextColor()));
    FileWrite(file_handle, "ButtonBG=" + ColorToString(CThemeManager::GetButtonBackground()));
    FileWrite(file_handle, "EditBG=" + ColorToString(CThemeManager::GetEditBackground()));

    // Save font settings
    FileWrite(file_handle, "FontName=" + CThemeManager::GetFontName());
    FileWrite(file_handle, "FontSize=" + IntegerToString(CThemeManager::GetFontSize()));

    FileClose(file_handle);
    return true;
}

bool CThemeSettings::LoadThemeSettings()
{
    int file_handle = FileOpen(s_settings_file, FILE_READ | FILE_TXT);
    if(file_handle == INVALID_HANDLE)
        return false;

    while(!FileIsEnding(file_handle))
    {
        string line = FileReadString(file_handle);
        string parts[];
        int count = StringSplit(line, '=', parts);

        if(count == 2)
        {
            if(parts[0] == "Theme")
            {
                CThemeManager::SetTheme((CThemeManager::ENUM_THEME_TYPE)StringToInteger(parts[1]));
            }
            // Process other settings...
        }
    }

    FileClose(file_handle);
    return true;
}

string CThemeSettings::ColorToString(const color clr)
{
    return IntegerToString(clr);
}

color CThemeSettings::StringToColor(const string str)
{
    return (color)StringToInteger(str);
}
```

## Advanced Customization Examples

### Animated Panel Transitions

```mql5
class CAnimatedPanel : public CAppDialog
{
private:
    bool m_is_animating;
    int  m_animation_steps;
    int  m_current_step;
    int  m_target_x, m_target_y;
    int  m_start_x, m_start_y;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    void AnimateMoveTo(const int x, const int y, const int duration_ms = 500);
    void FadeIn(const int duration_ms = 300);
    void FadeOut(const int duration_ms = 300);

protected:
    void OnAnimationStep();
    void StartAnimation();
    void StopAnimation();
};

void CAnimatedPanel::AnimateMoveTo(const int x, const int y, const int duration_ms)
{
    if(m_is_animating)
        return;

    m_start_x = Left();
    m_start_y = Top();
    m_target_x = x;
    m_target_y = y;
    m_animation_steps = duration_ms / 50; // 50ms per step
    m_current_step = 0;

    StartAnimation();
}

void CAnimatedPanel::StartAnimation()
{
    m_is_animating = true;
    EventSetTimer(50); // 50ms timer for smooth animation
}

void CAnimatedPanel::OnAnimationStep()
{
    if(!m_is_animating)
        return;

    m_current_step++;
    double progress = (double)m_current_step / m_animation_steps;

    if(progress >= 1.0)
    {
        // Animation complete
        Move(m_target_x, m_target_y);
        StopAnimation();
        return;
    }

    // Easing function (ease-out)
    progress = 1.0 - MathPow(1.0 - progress, 3.0);

    int current_x = (int)(m_start_x + (m_target_x - m_start_x) * progress);
    int current_y = (int)(m_start_y + (m_target_y - m_start_y) * progress);

    Move(current_x, current_y);
}
```

This comprehensive customization guide provides the tools and techniques needed to create highly customized, branded, and professionally styled MQL5 panel interfaces that go far beyond the default library appearance.

## See Also

- [EA Integration Guide](./ea-integration.md) - Panel lifecycle management
- [Layout Management](./layout-management.md) - Advanced layout techniques
- [API Constants Reference](../api/constants.md) - Complete list of customizable constants
- [Complex Controls](../complex-controls/) - Advanced control customization