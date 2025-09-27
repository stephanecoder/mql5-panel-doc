# MQL5 Controls Library - Constants and Enumerations

This reference documents all constants, enumerations, and predefined values used throughout the MQL5 Controls Library.

## Event Constants

### Controls Library Event Types
```mql5
// Controls Library specific events (from Defines.mqh)
#define ON_CLICK                (0)   // Clicking on control event
#define ON_DBL_CLICK            (1)   // Double clicking on control event
#define ON_SHOW                 (2)   // Showing control event
#define ON_HIDE                 (3)   // Hiding control event
#define ON_CHANGE               (4)   // Changing control event
#define ON_START_EDIT           (5)   // Start of editing event
#define ON_END_EDIT             (6)   // End of editing event
#define ON_SCROLL_INC           (7)   // Increment of scrollbar event
#define ON_SCROLL_DEC           (8)   // Decrement of scrollbar event
#define ON_MOUSE_FOCUS_SET      (9)   // Mouse cursor entered the control event
#define ON_MOUSE_FOCUS_KILL     (10)  // Mouse cursor exited the control event
#define ON_DRAG_START           (11)  // Control dragging start event
#define ON_DRAG_PROCESS         (12)  // Control is being dragged event
#define ON_DRAG_END             (13)  // Control dragging end event
#define ON_BRING_TO_TOP         (14)  // Mouse events priority increase event
#define ON_APP_CLOSE            (100) // Closing the application event
```

### Chart Event Types
```mql5
// Object interaction events
#define CHARTEVENT_OBJECT_CLICK       1     // Object clicked
#define CHARTEVENT_OBJECT_DRAG        2     // Object being dragged
#define CHARTEVENT_OBJECT_ENDEDIT     3     // Text editing finished
#define CHARTEVENT_OBJECT_DELETE      4     // Object deleted
#define CHARTEVENT_OBJECT_CREATE      5     // Object created
#define CHARTEVENT_OBJECT_CHANGE      6     // Object properties changed

// Chart interaction events
#define CHARTEVENT_CLICK              7     // Chart area clicked
#define CHARTEVENT_KEYDOWN           8     // Key pressed
#define CHARTEVENT_MOUSE_MOVE        9     // Mouse moved
#define CHARTEVENT_CHART_CHANGE      10    // Chart properties changed

// Custom application events
#define CHARTEVENT_CUSTOM            1000   // Base for custom events
```

### Internal Event Constants
```mql5
#define INTERNAL_EVENT                           (-1)
#define CONTROLS_SELF_MESSAGE               (-1)     // Message to oneself
#define CONTROLS_INVALID_ID                 (-1)     // Invalid ID
#define CONTROLS_INVALID_INDEX              (-1)     // Invalid index of array
#define CONTROLS_MAXIMUM_ID                 (10000)  // Maximum number of IDs in application
```

## Window Property and State Enumerations

### Window Property Flags
```mql5
enum ENUM_WND_PROP_FLAGS
{
    WND_PROP_FLAG_CAN_DBL_CLICK  = 1,    // Can be double clicked by mouse
    WND_PROP_FLAG_CAN_DRAG       = 2,    // Can be dragged by mouse
    WND_PROP_FLAG_CLICKS_BY_PRESS= 4,    // Generates "click" event series on pressing left mouse button
    WND_PROP_FLAG_CAN_LOCK       = 8,    // Control with fixed state (usually button)
    WND_PROP_FLAG_READ_ONLY      = 16    // Read only (usually edit control)
};
```

### Window State Flags
```mql5
enum ENUM_WND_STATE_FLAGS
{
    WND_STATE_FLAG_ENABLE        = 1,    // "Object is enabled" flag
    WND_STATE_FLAG_VISIBLE       = 2,    // "Object is visible" flag
    WND_STATE_FLAG_ACTIVE        = 4     // "Object is active" flag
};
```

### Mouse Flags
```mql5
enum ENUM_MOUSE_FLAGS
{
    MOUSE_INVALID_FLAGS          = -1,   // No buttons state
    MOUSE_EMPTY                  = 0,    // Buttons are not pressed
    MOUSE_LEFT                   = 1,    // Left button pressed
    MOUSE_RIGHT                  = 2     // Right button pressed
};
```

### Window Alignment Flags
```mql5
enum ENUM_WND_ALIGN_FLAGS
{
    WND_ALIGN_NONE               = 0,                                    // No alignment
    WND_ALIGN_LEFT               = 1,                                    // Align by left border
    WND_ALIGN_TOP                = 2,                                    // Align by top border
    WND_ALIGN_RIGHT              = 4,                                    // Align by right border
    WND_ALIGN_BOTTOM             = 8,                                    // Align by bottom border
    WND_ALIGN_WIDTH              = WND_ALIGN_LEFT|WND_ALIGN_RIGHT,       // Justify
    WND_ALIGN_HEIGHT             = WND_ALIGN_TOP|WND_ALIGN_BOTTOM,       // Align by top and bottom border
    WND_ALIGN_CLIENT             = WND_ALIGN_WIDTH|WND_ALIGN_HEIGHT      // Align by all sides
};
```

### Property Check Macros
```mql5
// Check properties
#define IS_CAN_DBL_CLICK     ((m_prop_flags&WND_PROP_FLAG_CAN_DBL_CLICK)!=0)
#define IS_CAN_DRAG          ((m_prop_flags&WND_PROP_FLAG_CAN_DRAG)!=0)
#define IS_CLICKS_BY_PRESS   ((m_prop_flags&WND_PROP_FLAG_CLICKS_BY_PRESS)!=0)
#define IS_CAN_LOCK          ((m_prop_flags&WND_PROP_FLAG_CAN_LOCK)!=0)
#define IS_READ_ONLY         ((m_prop_flags&WND_PROP_FLAG_READ_ONLY)!=0)

// Check state
#define IS_ENABLED           ((m_state_flags&WND_STATE_FLAG_ENABLE)!=0)
#define IS_VISIBLE           ((m_state_flags&WND_STATE_FLAG_VISIBLE)!=0)
#define IS_ACTIVE            ((m_state_flags&WND_STATE_FLAG_ACTIVE)!=0)
```

### Keyboard Virtual Key Codes
```mql5
// Common virtual key codes
#define VK_RETURN           0x0D       // Enter key
#define VK_ESCAPE           0x1B       // Escape key
#define VK_SPACE            0x20       // Spacebar
#define VK_TAB              0x09       // Tab key
#define VK_BACK             0x08       // Backspace key
#define VK_DELETE           0x2E       // Delete key

// Arrow keys
#define VK_LEFT             0x25       // Left arrow
#define VK_UP               0x26       // Up arrow
#define VK_RIGHT            0x27       // Right arrow
#define VK_DOWN             0x28       // Down arrow

// Function keys
#define VK_F1               0x70       // F1 key
#define VK_F2               0x71       // F2 key
#define VK_F3               0x72       // F3 key
#define VK_F4               0x73       // F4 key
#define VK_F5               0x74       // F5 key
#define VK_F12              0x7B       // F12 key

// Modifier keys
#define VK_SHIFT            0x10       // Shift key
#define VK_CONTROL          0x11       // Ctrl key
#define VK_MENU             0x12       // Alt key
```

## Alignment Constants

### Text Alignment
```mql5
// Horizontal alignment
#define ALIGN_LEFT          0x0001     // Align text to the left
#define ALIGN_CENTER        0x0002     // Center text horizontally
#define ALIGN_RIGHT         0x0004     // Align text to the right

// Vertical alignment
#define ALIGN_TOP           0x0008     // Align text to the top
#define ALIGN_MIDDLE        0x0010     // Center text vertically
#define ALIGN_BOTTOM        0x0020     // Align text to the bottom

// Combined alignments (commonly used)
#define ALIGN_LEFT_TOP      (ALIGN_LEFT | ALIGN_TOP)
#define ALIGN_LEFT_MIDDLE   (ALIGN_LEFT | ALIGN_MIDDLE)
#define ALIGN_LEFT_BOTTOM   (ALIGN_LEFT | ALIGN_BOTTOM)
#define ALIGN_CENTER_TOP    (ALIGN_CENTER | ALIGN_TOP)
#define ALIGN_CENTER_MIDDLE (ALIGN_CENTER | ALIGN_MIDDLE)
#define ALIGN_CENTER_BOTTOM (ALIGN_CENTER | ALIGN_BOTTOM)
#define ALIGN_RIGHT_TOP     (ALIGN_RIGHT | ALIGN_TOP)
#define ALIGN_RIGHT_MIDDLE  (ALIGN_RIGHT | ALIGN_MIDDLE)
#define ALIGN_RIGHT_BOTTOM  (ALIGN_RIGHT | ALIGN_BOTTOM)
```

## Border and Frame Types

### Border Styles
```mql5
enum ENUM_BORDER_TYPE
{
    BORDER_FLAT,            // Flat border (no 3D effect)
    BORDER_RAISED,          // Raised 3D border
    BORDER_SUNKEN           // Sunken 3D border
};
```

### Frame Styles
```mql5
enum ENUM_FRAME_TYPE
{
    FRAME_NONE,             // No frame
    FRAME_SIMPLE,           // Simple line frame
    FRAME_THICK,            // Thick line frame
    FRAME_DOUBLE            // Double line frame
};
```

## Color and Font Constants

### Font Settings
```mql5
// Common font settings (from Defines.mqh)
#define CONTROLS_FONT_NAME                  "Trebuchet MS"
#define CONTROLS_FONT_SIZE                  (10)
```

### Standard UI Colors
```mql5
// Text colors
#define CONTROLS_COLOR_TEXT                 C'0x3B,0x29,0x28'
#define CONTROLS_COLOR_TEXT_SEL             clrWhite
#define CONTROLS_COLOR_BG                   clrWhite
#define CONTROLS_COLOR_BG_SEL               C'0x33,0x99,0xFF'

// Button colors
#define CONTROLS_BUTTON_COLOR               C'0x3B,0x29,0x28'
#define CONTROLS_BUTTON_COLOR_BG            C'0xDD,0xE2,0xEB'
#define CONTROLS_BUTTON_COLOR_BORDER        C'0xB2,0xC3,0xCF'

// Label colors
#define CONTROLS_LABEL_COLOR                C'0x3B,0x29,0x28'

// Edit control colors
#define CONTROLS_EDIT_COLOR                 C'0x3B,0x29,0x28'
#define CONTROLS_EDIT_COLOR_BG              clrWhite
#define CONTROLS_EDIT_COLOR_BORDER          C'0xB2,0xC3,0xCF'

// Scroll bar colors
#define CONTROLS_SCROLL_COLOR_BG            C'0xEC,0xEC,0xEC'
#define CONTROLS_SCROLL_COLOR_BORDER        C'0xD3,0xD3,0xD3'

// Client area colors
#define CONTROLS_CLIENT_COLOR_BG            C'0xDE,0xDE,0xDE'
#define CONTROLS_CLIENT_COLOR_BORDER        C'0x2C,0x2C,0x2C'

// ListView colors
#define CONTROLS_LISTITEM_COLOR_TEXT        C'0x3B,0x29,0x28'
#define CONTROLS_LISTITEM_COLOR_TEXT_SEL    clrWhite
#define CONTROLS_LISTITEM_COLOR_BG          clrWhite
#define CONTROLS_LISTITEM_COLOR_BG_SEL      C'0x33,0x99,0xFF'
#define CONTROLS_LIST_COLOR_BG              clrWhite
#define CONTROLS_LIST_COLOR_BORDER          C'0xB2,0xC3,0xCF'

// CheckGroup colors
#define CONTROLS_CHECKGROUP_COLOR_BG        C'0xF7,0xF7,0xF7'
#define CONTROLS_CHECKGROUP_COLOR_BORDER    C'0xB2,0xC3,0xCF'

// RadioGroup colors
#define CONTROLS_RADIOGROUP_COLOR_BG        C'0xF7,0xF7,0xF7'
#define CONTROLS_RADIOGROUP_COLOR_BORDER    C'0xB2,0xC3,0xCF'

// Dialog colors
#define CONTROLS_DIALOG_COLOR_BORDER_LIGHT  clrWhite
#define CONTROLS_DIALOG_COLOR_BORDER_DARK   C'0xB6,0xB6,0xB6'
#define CONTROLS_DIALOG_COLOR_BG            C'0xF0,0xF0,0xF0'
#define CONTROLS_DIALOG_COLOR_CAPTION_TEXT  C'0x28,0x29,0x3B'
#define CONTROLS_DIALOG_COLOR_CLIENT_BG     C'0xF7,0xF7,0xF7'
#define CONTROLS_DIALOG_COLOR_CLIENT_BORDER C'0xC8,0xC8,0xC8'
```

### Semantic Colors
```mql5
// Status colors
#define COLOR_SUCCESS       0x00AA00       // Green for success
#define COLOR_WARNING       0x0080FF       // Orange for warnings
#define COLOR_ERROR         0x0000FF       // Red for errors
#define COLOR_INFO          0xAA6600       // Blue for information

// Trading colors
#define COLOR_BUY           0x00AA00       // Green for buy orders
#define COLOR_SELL          0x0000FF       // Red for sell orders
#define COLOR_PENDING       0x0080FF       // Orange for pending orders
#define COLOR_PROFIT        0x00AA00       // Green for profit
#define COLOR_LOSS          0x0000FF       // Red for loss
```

## Size and Spacing Constants

### Standard Dimensions
```mql5
// Margins and padding
#define CONTROLS_MARGIN_X        10        // Horizontal margin
#define CONTROLS_MARGIN_Y        10        // Vertical margin
#define CONTROLS_SPACING_X       5         // Horizontal spacing
#define CONTROLS_SPACING_Y       5         // Vertical spacing

// Control heights
#define CONTROLS_BUTTON_HEIGHT   30        // Standard button height
#define CONTROLS_EDIT_HEIGHT     25        // Standard edit height
#define CONTROLS_LABEL_HEIGHT    20        // Standard label height
#define CONTROLS_COMBO_HEIGHT    25        // Standard combo height

// Control widths
#define CONTROLS_BUTTON_WIDTH    80        // Standard button width
#define CONTROLS_EDIT_WIDTH      120       // Standard edit width
#define CONTROLS_COMBO_WIDTH     120       // Standard combo width

// Icon sizes
#define CONTROLS_ICON_SIZE_SMALL  16       // Small icon (16x16)
#define CONTROLS_ICON_SIZE_MEDIUM 24       // Medium icon (24x24)
#define CONTROLS_ICON_SIZE_LARGE  32       // Large icon (32x32)
```

### Panel Dimensions
```mql5
// Dialog sizes
#define CONTROLS_DIALOG_MIN_WIDTH    200   // Minimum dialog width
#define CONTROLS_DIALOG_MIN_HEIGHT   150   // Minimum dialog height
#define CONTROLS_DIALOG_MAX_WIDTH    800   // Maximum dialog width
#define CONTROLS_DIALOG_MAX_HEIGHT   600   // Maximum dialog height

// Caption and scrollbar dimensions
#define CONTROLS_CAPTION_HEIGHT      25    // Caption bar height
#define CONTROLS_SCROLLBAR_WIDTH     20    // Scrollbar width
#define CONTROLS_SCROLLBAR_HEIGHT    20    // Scrollbar height
```

## Font Constants

### Standard Fonts
```mql5
#define CONTROLS_FONT_NAME        "Arial"           // Default font
#define CONTROLS_FONT_NAME_BOLD   "Arial Bold"      // Bold font
#define CONTROLS_FONT_NAME_MONO   "Courier New"     // Monospace font

// Font sizes
#define CONTROLS_FONT_SIZE_SMALL   8                // Small text
#define CONTROLS_FONT_SIZE_NORMAL  9                // Normal text
#define CONTROLS_FONT_SIZE_LARGE   11               // Large text
#define CONTROLS_FONT_SIZE_HEADING 12               // Heading text
#define CONTROLS_FONT_SIZE_TITLE   14               // Title text
```

## Control State Constants

### Visibility States
```mql5
enum ENUM_VISIBILITY_STATE
{
    VISIBILITY_HIDDEN,      // Control is hidden
    VISIBILITY_VISIBLE,     // Control is visible
    VISIBILITY_COLLAPSED    // Control is collapsed (takes no space)
};
```

### Enable States
```mql5
enum ENUM_ENABLE_STATE
{
    ENABLE_DISABLED,        // Control is disabled
    ENABLE_ENABLED,         // Control is enabled
    ENABLE_READONLY         // Control is read-only
};
```

### Focus States
```mql5
enum ENUM_FOCUS_STATE
{
    FOCUS_NONE,             // No focus
    FOCUS_KEYBOARD,         // Keyboard focus
    FOCUS_MOUSE             // Mouse focus
};
```

## List and Selection Constants

### Selection Modes
```mql5
enum ENUM_SELECTION_MODE
{
    SELECTION_NONE,         // No selection allowed
    SELECTION_SINGLE,       // Single item selection
    SELECTION_MULTIPLE,     // Multiple item selection
    SELECTION_EXTENDED      // Extended selection (Shift/Ctrl)
};
```

### Sort Orders
```mql5
enum ENUM_SORT_ORDER
{
    SORT_NONE,              // No sorting
    SORT_ASCENDING,         // Ascending order
    SORT_DESCENDING         // Descending order
};
```

## Layout Constants

### Layout Types
```mql5
enum ENUM_LAYOUT_TYPE
{
    LAYOUT_NONE,            // No automatic layout
    LAYOUT_VERTICAL,        // Vertical stack layout
    LAYOUT_HORIZONTAL,      // Horizontal row layout
    LAYOUT_GRID,            // Grid layout
    LAYOUT_DOCK             // Docking layout
};
```

### Dock Positions
```mql5
enum ENUM_DOCK_POSITION
{
    DOCK_NONE,              // No docking
    DOCK_LEFT,              // Dock to left edge
    DOCK_RIGHT,             // Dock to right edge
    DOCK_TOP,               // Dock to top edge
    DOCK_BOTTOM,            // Dock to bottom edge
    DOCK_FILL               // Fill remaining space
};
```

## Control-Specific Constants

### Button States
```mql5
enum ENUM_BUTTON_STATE
{
    BUTTON_NORMAL,          // Normal state
    BUTTON_HOVER,           // Mouse hover state
    BUTTON_PRESSED,         // Pressed state
    BUTTON_DISABLED         // Disabled state
};
```

### CheckBox States
```mql5
enum ENUM_CHECKBOX_STATE
{
    CHECKBOX_UNCHECKED,     // Not checked
    CHECKBOX_CHECKED,       // Checked
    CHECKBOX_INDETERMINATE  // Indeterminate state
};
```

### Edit Control Types
```mql5
enum ENUM_EDIT_TYPE
{
    EDIT_TEXT,              // Plain text input
    EDIT_NUMBER,            // Numeric input
    EDIT_PASSWORD,          // Password input (masked)
    EDIT_MULTILINE          // Multi-line text input
};
```

### Scroll Types
```mql5
enum ENUM_SCROLL_TYPE
{
    SCROLL_NONE,            // No scrolling
    SCROLL_HORIZONTAL,      // Horizontal scrolling only
    SCROLL_VERTICAL,        // Vertical scrolling only
    SCROLL_BOTH             // Both horizontal and vertical
};
```

## Time and Date Constants

### Date Formats
```mql5
#define DATE_FORMAT_SHORT    "MM/dd/yyyy"        // Short date format
#define DATE_FORMAT_LONG     "MMMM dd, yyyy"     // Long date format
#define DATE_FORMAT_ISO      "yyyy-MM-dd"        // ISO date format

// Time formats
#define TIME_FORMAT_SHORT    "HH:mm"             // Short time format
#define TIME_FORMAT_LONG     "HH:mm:ss"          // Long time format
#define TIME_FORMAT_12H      "hh:mm tt"          // 12-hour format
```

### Day of Week Constants
```mql5
#define DAYOFWEEK_SUNDAY     0                   // Sunday
#define DAYOFWEEK_MONDAY     1                   // Monday
#define DAYOFWEEK_TUESDAY    2                   // Tuesday
#define DAYOFWEEK_WEDNESDAY  3                   // Wednesday
#define DAYOFWEEK_THURSDAY   4                   // Thursday
#define DAYOFWEEK_FRIDAY     5                   // Friday
#define DAYOFWEEK_SATURDAY   6                   // Saturday
```

## Drawing and Graphics Constants

### Line Styles
```mql5
enum ENUM_LINE_STYLE
{
    LINE_SOLID,             // Solid line
    LINE_DASH,              // Dashed line
    LINE_DOT,               // Dotted line
    LINE_DASHDOT,           // Dash-dot line
    LINE_DASHDOTDOT         // Dash-dot-dot line
};
```

### Fill Patterns
```mql5
enum ENUM_FILL_PATTERN
{
    FILL_SOLID,             // Solid fill
    FILL_NONE,              // No fill
    FILL_CROSS,             // Cross hatch
    FILL_DIAGONAL,          // Diagonal lines
    FILL_DOTS               // Dot pattern
};
```

## Validation Constants

### Validation Types
```mql5
enum ENUM_VALIDATION_TYPE
{
    VALIDATION_NONE,        // No validation
    VALIDATION_REQUIRED,    // Required field
    VALIDATION_NUMERIC,     // Numeric validation
    VALIDATION_EMAIL,       // Email format
    VALIDATION_PHONE,       // Phone number format
    VALIDATION_CUSTOM       // Custom validation
};
```

### Error Levels
```mql5
enum ENUM_ERROR_LEVEL
{
    ERROR_NONE,             // No error
    ERROR_WARNING,          // Warning level
    ERROR_ERROR,            // Error level
    ERROR_CRITICAL          // Critical error
};
```

## Application Constants

### Application States
```mql5
enum ENUM_APP_STATE
{
    APP_INITIALIZING,       // Application starting up
    APP_RUNNING,            // Normal operation
    APP_SUSPENDED,          // Temporarily suspended
    APP_TERMINATING         // Shutting down
};
```

### Connection States
```mql5
enum ENUM_CONNECTION_STATE
{
    CONNECTION_DISCONNECTED, // Not connected
    CONNECTION_CONNECTING,   // Connecting
    CONNECTION_CONNECTED,    // Connected
    CONNECTION_ERROR         // Connection error
};
```

## EVENT_MAP Macro System

### Complete Event Map Macros
```mql5
// Internal event constant
#define INTERNAL_EVENT                           (-1)

// Event map macros (from Defines.mqh)
#define EVENT_MAP_BEGIN(class_name)              bool class_name::OnEvent(const int id,const long& lparam,const double& dparam,const string& sparam) {
#define EVENT_MAP_END(parent_class_name)         return(parent_class_name::OnEvent(id,lparam,dparam,sparam)); }

// Event handling by numeric ID
#define ON_EVENT(event,control,handler)          if(id==(event+CHARTEVENT_CUSTOM) && lparam==control.Id()) { handler(); return(true); }

// Event handling by numeric ID by pointer of control
#define ON_EVENT_PTR(event,control,handler)      if(control!=NULL && id==(event+CHARTEVENT_CUSTOM) && lparam==control.Id()) { handler(); return(true); }

// Event handling without ID analysis
#define ON_NO_ID_EVENT(event,handler)            if(id==(event+CHARTEVENT_CUSTOM)) { return(handler()); }

// Event handling by row ID
#define ON_NAMED_EVENT(event,control,handler)    if(id==(event+CHARTEVENT_CUSTOM) && sparam==control.Name()) { handler(); return(true); }

// Handling of indexed event
#define ON_INDEXED_EVENT(event,controls,handler) { int total=ArraySize(controls); for(int i=0;i<total;i++) if(id==(event+CHARTEVENT_CUSTOM) && lparam==controls[i].Id()) return(handler(i)); }

// Handling of external event
#define ON_EXTERNAL_EVENT(event,handler)         if(id==(event+CHARTEVENT_CUSTOM)) { handler(lparam,dparam,sparam); return(true); }
```

### EVENT_MAP Usage Examples
```mql5
// Basic event map
EVENT_MAP_BEGIN(CMyPanel)
    ON_EVENT(ON_CLICK, m_button1, OnButton1Click)
    ON_EVENT(ON_CLICK, m_button2, OnButton2Click)
    ON_EVENT(ON_CHANGE, m_edit, OnEditChange)
EVENT_MAP_END(CAppDialog)

// Advanced event map with multiple event types
EVENT_MAP_BEGIN(CAdvancedPanel)
    // Standard control events
    ON_EVENT(ON_CLICK, m_buy_button, OnBuyClick)
    ON_EVENT(ON_CHANGE, m_lot_size, OnLotSizeChange)

    // Pointer-based events (for dynamically created controls)
    ON_EVENT_PTR(ON_CLICK, m_dynamic_button, OnDynamicButtonClick)

    // Named events (string-based identification)
    ON_NAMED_EVENT(ON_CLICK, m_named_control, OnNamedControlClick)

    // External events (custom application events)
    ON_EXTERNAL_EVENT(1001, OnCustomEvent)

    // No-ID events (global events)
    ON_NO_ID_EVENT(ON_APP_CLOSE, OnApplicationClose)
EVENT_MAP_END(CAppDialog)

// Indexed event handling for control arrays
EVENT_MAP_BEGIN(CButtonArrayPanel)
    ON_INDEXED_EVENT(ON_CLICK, m_button_array, OnButtonArrayClick)
EVENT_MAP_END(CAppDialog)

// Handler method signatures
bool OnButtonArrayClick(int button_index)
{
    Print("Button ", button_index, " clicked");
    return true;
}

void OnCustomEvent(long lparam, double dparam, string sparam)
{
    Print("Custom event received: ", lparam, ", ", dparam, ", ", sparam);
}

bool OnApplicationClose()
{
    Print("Application closing");
    return true; // Allow close
}
```

## Control-Specific Dimension Constants

### Control Size Constants
```mql5
// Border and spacing
#define CONTROLS_BORDER_WIDTH               (1)      // Border width
#define CONTROLS_SUBWINDOW_GAP              (3)      // Gap between sub-windows along the Y axis
#define CONTROLS_DRAG_SPACING               (50)     // Sensitivity threshold for dragging
#define CONTROLS_DBL_CLICK_TIME             (100)    // Double click interval

// BmpButton
#define CONTROLS_BUTTON_SIZE                (16)     // Default size of button (16 x 16)

// Scrolls
#define CONTROLS_SCROLL_SIZE                (18)     // Default lateral size of scrollbar
#define CONTROLS_SCROLL_THUMB_SIZE          (22)     // Default length of scroll box

// RadioButton
#define CONTROLS_RADIO_BUTTON_X_OFF         (3)      // X offset of radio button (for RadioButton)
#define CONTROLS_RADIO_BUTTON_Y_OFF         (3)      // Y offset of radio button (for RadioButton)
#define CONTROLS_RADIO_LABEL_X_OFF          (20)     // X offset of label (for RadioButton)
#define CONTROLS_RADIO_LABEL_Y_OFF          (0)      // Y offset of label (for RadioButton)

// CheckBox
#define CONTROLS_CHECK_BUTTON_X_OFF         (3)      // X offset of check button (for CheckBox)
#define CONTROLS_CHECK_BUTTON_Y_OFF         (3)      // Y offset of check button (for CheckBox)
#define CONTROLS_CHECK_LABEL_X_OFF          (20)     // X offset of label (for CheckBox)
#define CONTROLS_CHECK_LABEL_Y_OFF          (0)      // Y offset of label (for CheckBox)

// SpinEdit
#define CONTROLS_SPIN_BUTTON_X_OFF          (2)      // X offset of button from right (for SpinEdit)
#define CONTROLS_SPIN_MIN_HEIGHT            (18)     // Minimal height (for SpinEdit)
#define CONTROLS_SPIN_BUTTON_SIZE           (8)      // Default size of button (16 x 8) (for SpinEdit)

// ComboBox
#define CONTROLS_COMBO_BUTTON_X_OFF         (2)      // X offset of button from right (for ComboBox)
#define CONTROLS_COMBO_MIN_HEIGHT           (18)     // Minimal height (for ComboBox)
#define CONTROLS_COMBO_ITEM_HEIGHT          (18)     // Height of combo box item (for ComboBox)
#define CONTROLS_COMBO_ITEMS_VIEW           (8)      // Number of items in combo box (for ComboBox)

// ListView
#define CONTROLS_LIST_ITEM_HEIGHT           (18)     // Height of list item (for ListView)

// Dialog
#define CONTROLS_DIALOG_CAPTION_HEIGHT      (22)     // Height of dialog header
#define CONTROLS_DIALOG_BUTTON_OFF          (3)      // Offset of dialog buttons
#define CONTROLS_DIALOG_CLIENT_OFF          (2)      // Offset of dialog client area
#define CONTROLS_DIALOG_MINIMIZE_LEFT       (10)     // Left coordinate of dialog in minimized state
#define CONTROLS_DIALOG_MINIMIZE_TOP        (10)     // Top coordinate of dialog in minimized state
#define CONTROLS_DIALOG_MINIMIZE_WIDTH      (100)    // Width of dialog in minimized state
#define CONTROLS_DIALOG_MINIMIZE_HEIGHT     (4*CONTROLS_BORDER_WIDTH+CONTROLS_DIALOG_CAPTION_HEIGHT) // Height of dialog in minimized state
```

## Usage Examples

### Using Color Constants
```mql5
void SetupControlColors(CButton &button)
{
    button.ColorBackground(CONTROLS_BUTTON_COLOR_BG);
    button.Color(clrBlack);

    // Use semantic colors for status
    if(IsError())
        button.ColorBackground(COLOR_ERROR);
    else if(IsSuccess())
        button.ColorBackground(COLOR_SUCCESS);
}
```

### Using Size Constants
```mql5
bool CreateStandardButton(CButton &button, const string name, const int x, const int y)
{
    return button.Create(ChartID(), name, 0,
                        x, y,
                        x + CONTROLS_BUTTON_WIDTH,
                        y + CONTROLS_BUTTON_HEIGHT);
}
```

### Using Event Constants
```mql5
bool CMyPanel::OnEvent(const int id, const long &lparam,
                      const double &dparam, const string &sparam)
{
    switch(id)
    {
        case CHARTEVENT_OBJECT_CLICK:
            return OnObjectClick(sparam);

        case CHARTEVENT_KEYDOWN:
            if(lparam == VK_ESCAPE)
                return OnEscapeKey();
            else if(lparam == VK_RETURN)
                return OnEnterKey();
            break;

        case CHARTEVENT_CUSTOM:
            return OnCustomEvent(lparam, dparam, sparam);
    }

    return CAppDialog::OnEvent(id, lparam, dparam, sparam);
}
```

### Using Alignment Constants
```mql5
void SetupLabelAlignment(CLabel &label, const string alignment_type)
{
    if(alignment_type == "center")
        label.TextAlign(ALIGN_CENTER_MIDDLE);
    else if(alignment_type == "right")
        label.TextAlign(ALIGN_RIGHT_MIDDLE);
    else
        label.TextAlign(ALIGN_LEFT_MIDDLE);
}
```

### Using State Enumerations
```mql5
void UpdateButtonState(CButton &button, const ENUM_BUTTON_STATE state)
{
    switch(state)
    {
        case BUTTON_NORMAL:
            button.ColorBackground(CONTROLS_BUTTON_COLOR_BG);
            button.Enable();
            break;

        case BUTTON_HOVER:
            button.ColorBackground(clrLightBlue);
            break;

        case BUTTON_PRESSED:
            button.ColorBackground(CONTROLS_BUTTON_COLOR_PRESSED);
            break;

        case BUTTON_DISABLED:
            button.ColorBackground(clrGray);
            button.Disable();
            break;
    }
}
```

## Custom Constants

When creating your own applications, you can define custom constants following the same patterns:

```mql5
// Custom event types
#define EVENT_PRICE_UPDATE      (CHARTEVENT_CUSTOM + 1)
#define EVENT_ORDER_FILLED      (CHARTEVENT_CUSTOM + 2)
#define EVENT_CONNECTION_LOST   (CHARTEVENT_CUSTOM + 3)

// Custom colors
#define MY_APP_COLOR_PRIMARY    0x0066CC
#define MY_APP_COLOR_SECONDARY  0x66CCFF
#define MY_APP_COLOR_ACCENT     0xFF6600

// Custom dimensions
#define MY_PANEL_WIDTH          400
#define MY_PANEL_HEIGHT         300
#define MY_BUTTON_SPACING       8
```

These constants provide a standardized foundation for building consistent and professional MQL5 trading applications. Use them to ensure your controls have a uniform appearance and behavior that follows established UI conventions.

## See Also

- [Function Reference](./function-reference.md) - Complete function documentation
- [Class Hierarchy](./class-hierarchy.md) - Inheritance structure
- [Control Documentation](../) - Individual control references