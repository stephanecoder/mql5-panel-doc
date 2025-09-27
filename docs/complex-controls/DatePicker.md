# CDatePicker - Date Selection Control

The `CDatePicker` class provides a comprehensive date selection interface, combining an editable text field with a dropdown calendar for intuitive date input.

## Class Overview

```mql5
class CDatePicker : public CWndContainer
{
private:
    CEdit             m_edit;        // Date display and input field
    CBmpButton        m_drop;        // Dropdown button
    CDateDropList     m_list;        // Calendar dropdown
    datetime          m_value;       // Current selected date

public:
    virtual bool      Create(const long chart, const string name, const int subwin,
                             const int x1, const int y1, const int x2, const int y2);

    // Date value management
    datetime          Value(void) const;
    void              Value(datetime value);

    // State management
    virtual bool      Show(void);

    // File operations
    virtual bool      Save(const int file_handle);
    virtual bool      Load(const int file_handle);
};
```

## Key Features

### 1. **Composite Design**
- **CEdit**: Text field for date display and manual input
- **CBmpButton**: Dropdown trigger with calendar icon
- **CDateDropList**: Calendar interface for date selection

### 2. **Multiple Input Methods**
- Direct text input in various date formats
- Visual calendar selection via dropdown
- Keyboard navigation support

### 3. **Data Persistence**
- Save/Load functionality for date values
- Integration with file-based configuration systems

## Basic Usage

### Creating a Date Picker

```mql5
class CMyPanel : public CAppDialog
{
private:
    CDatePicker m_date_picker;
    CLabel      m_label_selected;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

protected:
    bool CreateDatePicker();
    bool CreateLabel();
    void OnDateChange();
};

bool CMyPanel::Create(const long chart_id, const string name, const int subwin,
                     const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    if(!CreateDatePicker())
        return false;

    if(!CreateLabel())
        return false;

    return true;
}

bool CMyPanel::CreateDatePicker()
{
    // Create date picker control
    if(!m_date_picker.Create(ChartID(), Name() + "DatePicker", 0,
                            10, 30, 160, 55))
        return false;

    // Set initial date to today
    m_date_picker.Value(TimeCurrent());

    return Add(m_date_picker);
}

bool CMyPanel::CreateLabel()
{
    if(!m_label_selected.Create(ChartID(), Name() + "Label", 0,
                               10, 65, 200, 85))
        return false;

    m_label_selected.Text("Selected: " + TimeToString(m_date_picker.Value(), TIME_DATE));
    return Add(m_label_selected);
}
```

### Event Handling

```mql5
// Event map for date picker
EVENT_MAP_BEGIN(CMyPanel)
    ON_EVENT(ON_CHANGE, m_date_picker, OnDateChange)
EVENT_MAP_END(CAppDialog)

void CMyPanel::OnDateChange()
{
    datetime selected_date = m_date_picker.Value();
    string date_text = TimeToString(selected_date, TIME_DATE);

    m_label_selected.Text("Selected: " + date_text);

    Print("Date changed to: ", date_text);
}
```

## Advanced Usage Patterns

### 1. **Date Range Validation**

```mql5
class CDateRangePicker : public CAppDialog
{
private:
    CDatePicker m_start_date;
    CDatePicker m_end_date;
    CLabel      m_validation_label;

    datetime    m_min_date;
    datetime    m_max_date;

public:
    void SetDateRange(datetime min_date, datetime max_date);
    bool ValidateDateRange();

protected:
    void OnStartDateChange();
    void OnEndDateChange();
    void UpdateValidationStatus();
};

void CDateRangePicker::SetDateRange(datetime min_date, datetime max_date)
{
    m_min_date = min_date;
    m_max_date = max_date;

    // Set default values within range
    m_start_date.Value(min_date);
    m_end_date.Value(max_date);
}

bool CDateRangePicker::ValidateDateRange()
{
    datetime start = m_start_date.Value();
    datetime end = m_end_date.Value();

    // Check if start is before end
    if(start >= end)
    {
        m_validation_label.Text("Start date must be before end date");
        m_validation_label.Color(clrRed);
        return false;
    }

    // Check if dates are within allowed range
    if(start < m_min_date || end > m_max_date)
    {
        m_validation_label.Text("Dates must be within allowed range");
        m_validation_label.Color(clrRed);
        return false;
    }

    m_validation_label.Text("Date range is valid");
    m_validation_label.Color(clrGreen);
    return true;
}

EVENT_MAP_BEGIN(CDateRangePicker)
    ON_EVENT(ON_CHANGE, m_start_date, OnStartDateChange)
    ON_EVENT(ON_CHANGE, m_end_date, OnEndDateChange)
EVENT_MAP_END(CAppDialog)

void CDateRangePicker::OnStartDateChange()
{
    ValidateDateRange();
}

void CDateRangePicker::OnEndDateChange()
{
    ValidateDateRange();
}
```

### 2. **Trading Session Date Picker**

```mql5
class CTradingSessionPicker : public CAppDialog
{
private:
    CDatePicker m_session_date;
    CLabel      m_session_info;
    CButton     m_analyze_button;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

protected:
    void OnSessionDateChange();
    void OnAnalyzeClick();
    void DisplaySessionInfo(datetime session_date);
    bool IsMarketOpen(datetime date);
    string GetSessionDescription(datetime date);
};

void CTradingSessionPicker::OnSessionDateChange()
{
    datetime selected_date = m_session_date.Value();
    DisplaySessionInfo(selected_date);
}

void CTradingSessionPicker::DisplaySessionInfo(datetime session_date)
{
    string info_text = "";

    // Check if it's a weekday (market day)
    int day_of_week = TimeDayOfWeek(session_date);

    if(day_of_week == 0 || day_of_week == 6) // Sunday or Saturday
    {
        info_text = "Market Closed - Weekend";
        m_session_info.Color(clrGray);
    }
    else
    {
        info_text = "Trading Day - " + GetSessionDescription(session_date);
        m_session_info.Color(clrBlue);
    }

    m_session_info.Text(info_text);
}

string CTradingSessionPicker::GetSessionDescription(datetime date)
{
    // Add logic to determine trading session characteristics
    // This could include volume analysis, volatility, etc.

    return "Active Session";
}

EVENT_MAP_BEGIN(CTradingSessionPicker)
    ON_EVENT(ON_CHANGE, m_session_date, OnSessionDateChange)
    ON_EVENT(ON_CLICK, m_analyze_button, OnAnalyzeClick)
EVENT_MAP_END(CAppDialog)
```

### 3. **Multiple Date Picker with Presets**

```mql5
class CDatePresetPicker : public CAppDialog
{
private:
    CDatePicker m_custom_date;
    CButton     m_btn_today;
    CButton     m_btn_yesterday;
    CButton     m_btn_week_ago;
    CButton     m_btn_month_ago;
    CLabel      m_selected_info;

public:
    enum ENUM_DATE_PRESET
    {
        PRESET_TODAY,
        PRESET_YESTERDAY,
        PRESET_WEEK_AGO,
        PRESET_MONTH_AGO,
        PRESET_CUSTOM
    };

protected:
    void OnTodayClick();
    void OnYesterdayClick();
    void OnWeekAgoClick();
    void OnMonthAgoClick();
    void OnCustomDateChange();

    void SetPresetDate(ENUM_DATE_PRESET preset);
    void UpdateSelectedInfo(ENUM_DATE_PRESET preset);
};

void CDatePresetPicker::SetPresetDate(ENUM_DATE_PRESET preset)
{
    datetime current_time = TimeCurrent();
    datetime target_date;

    switch(preset)
    {
        case PRESET_TODAY:
            target_date = current_time;
            break;

        case PRESET_YESTERDAY:
            target_date = current_time - 86400; // 24 hours
            break;

        case PRESET_WEEK_AGO:
            target_date = current_time - (7 * 86400); // 7 days
            break;

        case PRESET_MONTH_AGO:
            target_date = current_time - (30 * 86400); // 30 days
            break;

        default:
            return; // Custom date, don't change
    }

    m_custom_date.Value(target_date);
    UpdateSelectedInfo(preset);
}

void CDatePresetPicker::UpdateSelectedInfo(ENUM_DATE_PRESET preset)
{
    string preset_name;
    datetime date_value = m_custom_date.Value();

    switch(preset)
    {
        case PRESET_TODAY:     preset_name = "Today"; break;
        case PRESET_YESTERDAY: preset_name = "Yesterday"; break;
        case PRESET_WEEK_AGO:  preset_name = "Week Ago"; break;
        case PRESET_MONTH_AGO: preset_name = "Month Ago"; break;
        case PRESET_CUSTOM:    preset_name = "Custom"; break;
    }

    string info = StringFormat("%s: %s", preset_name, TimeToString(date_value, TIME_DATE));
    m_selected_info.Text(info);
}

EVENT_MAP_BEGIN(CDatePresetPicker)
    ON_EVENT(ON_CLICK, m_btn_today, OnTodayClick)
    ON_EVENT(ON_CLICK, m_btn_yesterday, OnYesterdayClick)
    ON_EVENT(ON_CLICK, m_btn_week_ago, OnWeekAgoClick)
    ON_EVENT(ON_CLICK, m_btn_month_ago, OnMonthAgoClick)
    ON_EVENT(ON_CHANGE, m_custom_date, OnCustomDateChange)
EVENT_MAP_END(CAppDialog)

void CDatePresetPicker::OnTodayClick()
{
    SetPresetDate(PRESET_TODAY);
}

void CDatePresetPicker::OnCustomDateChange()
{
    UpdateSelectedInfo(PRESET_CUSTOM);
}
```

## File Persistence

### Saving and Loading Date Settings

```mql5
class CDatePickerWithPersistence : public CAppDialog
{
private:
    CDatePicker m_date_picker;
    string      m_settings_file;

public:
    void SetSettingsFile(const string filename) { m_settings_file = filename; }
    bool SaveSettings();
    bool LoadSettings();

protected:
    bool SaveToFile();
    bool LoadFromFile();
};

bool CDatePickerWithPersistence::SaveSettings()
{
    if(StringLen(m_settings_file) == 0)
        return false;

    int file_handle = FileOpen(m_settings_file, FILE_WRITE | FILE_BIN);
    if(file_handle == INVALID_HANDLE)
        return false;

    bool result = m_date_picker.Save(file_handle);
    FileClose(file_handle);

    return result;
}

bool CDatePickerWithPersistence::LoadSettings()
{
    if(StringLen(m_settings_file) == 0)
        return false;

    int file_handle = FileOpen(m_settings_file, FILE_READ | FILE_BIN);
    if(file_handle == INVALID_HANDLE)
        return false;

    bool result = m_date_picker.Load(file_handle);
    FileClose(file_handle);

    return result;
}
```

## Integration with Trading Applications

### Backtest Date Range Selector

```mql5
class CBacktestDateSelector : public CAppDialog
{
private:
    CDatePicker m_start_date;
    CDatePicker m_end_date;
    CButton     m_run_backtest;
    CLabel      m_period_info;

public:
    datetime GetStartDate() const { return m_start_date.Value(); }
    datetime GetEndDate() const { return m_end_date.Value(); }

protected:
    void OnRunBacktest();
    void UpdatePeriodInfo();
    bool ValidateBacktestPeriod();
};

void CBacktestDateSelector::OnRunBacktest()
{
    if(!ValidateBacktestPeriod())
    {
        MessageBox("Invalid date range for backtesting", "Error", MB_OK | MB_ICONERROR);
        return;
    }

    datetime start = GetStartDate();
    datetime end = GetEndDate();

    // Trigger backtest with selected date range
    Print("Starting backtest from ", TimeToString(start, TIME_DATE),
          " to ", TimeToString(end, TIME_DATE));

    // Send custom event to EA for backtest execution
    EventChartCustom(ChartID(), CHARTEVENT_CUSTOM + 1, (long)start, (double)end, "BACKTEST_START");
}

bool CBacktestDateSelector::ValidateBacktestPeriod()
{
    datetime start = GetStartDate();
    datetime end = GetEndDate();

    // Check if start is before end
    if(start >= end)
        return false;

    // Check minimum period (e.g., at least 7 days)
    if((end - start) < (7 * 86400))
        return false;

    // Check if dates are not in the future
    if(start > TimeCurrent() || end > TimeCurrent())
        return false;

    return true;
}

EVENT_MAP_BEGIN(CBacktestDateSelector)
    ON_EVENT(ON_CHANGE, m_start_date, UpdatePeriodInfo)
    ON_EVENT(ON_CHANGE, m_end_date, UpdatePeriodInfo)
    ON_EVENT(ON_CLICK, m_run_backtest, OnRunBacktest)
EVENT_MAP_END(CAppDialog)
```

## Best Practices

### 1. **Date Format Consistency**
- Use `TimeToString()` with `TIME_DATE` for consistent formatting
- Handle different regional date formats appropriately
- Validate user input when allowing manual date entry

### 2. **User Experience**
- Set reasonable default dates (current date, trading session dates)
- Provide visual feedback for invalid date selections
- Include preset buttons for common date choices

### 3. **Integration Patterns**
- Use events to notify parent controls of date changes
- Implement validation before processing date-dependent operations
- Save user preferences for date picker states

### 4. **Performance Considerations**
- Cache frequently used date calculations
- Avoid frequent redrawing during date range validation
- Use appropriate date precision for your application needs

The CDatePicker control provides a professional and user-friendly interface for date selection in trading applications, supporting both manual input and visual calendar selection with comprehensive validation and persistence capabilities.

## See Also

- [Complex Controls Overview](./README.md) - Overview of all complex controls
- [CComboBox Documentation](./CComboBox.md) - Dropdown selection patterns
- [Event Handling Tutorial](../tutorials/event-handling.md) - Advanced event management
- [Form Patterns Guide](../tutorials/form-patterns.md) - UI design patterns