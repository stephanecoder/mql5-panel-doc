# CDateTime - Date and Time Structure

The `CDateTime` class provides comprehensive date and time functionality for MQL5 applications, extending the MqlDateTime structure with additional methods and operations.

## Overview

- **Header File**: `<Tools\DateTime.mqh>`
- **Base Structure**: MqlDateTime
- **Purpose**: Date and time operations for control applications
- **Key Features**: Date/time manipulation, formatting, arithmetic operations

## Inherited Properties

From MqlDateTime structure:
```mql5
int year;        // Year
int mon;         // Month (1-12)
int day;         // Day of month (1-31)
int hour;        // Hour (0-23)
int min;         // Minutes (0-59)
int sec;         // Seconds (0-59)
int day_of_week; // Day of week (0-Sunday, 1-Monday, etc.)
int day_of_year; // Day of year (1-366)
```

## Core Methods

### Property Access

#### Month Names
```mql5
string MonthName() const;           // Get full month name
string ShortMonthName() const;      // Get abbreviated month name
```

#### Day Names
```mql5
string DayName() const;             // Get full day of week name
string ShortDayName() const;        // Get abbreviated day of week name
```

#### Calendar Information
```mql5
int DaysInMonth() const;            // Get number of days in current month
```

### Date and Time Setting

#### Complete Date/Time
```mql5
datetime DateTime() const;                      // Get complete date/time
void DateTime(const datetime dt);               // Set complete date/time
```

#### Date Components
```mql5
void Date(const datetime date);                 // Set date portion
void Year(const int year);                      // Set year
void Mon(const int month);                      // Set month
void Day(const int day);                        // Set day
```

#### Time Components
```mql5
void Time(const datetime time);                 // Set time portion
void Hour(const int hour);                      // Set hour
void Min(const int minute);                     // Set minute
void Sec(const int second);                     // Set second
```

### Time Arithmetic

#### Second Operations
```mql5
void SecDec();                                  // Subtract one second
void SecDec(const int seconds);                 // Subtract specified seconds
void SecInc();                                  // Add one second
void SecInc(const int seconds);                 // Add specified seconds
```

#### Minute Operations
```mql5
void MinDec();                                  // Subtract one minute
void MinDec(const int minutes);                 // Subtract specified minutes
void MinInc();                                  // Add one minute
void MinInc(const int minutes);                 // Add specified minutes
```

#### Hour Operations
```mql5
void HourDec();                                 // Subtract one hour
void HourDec(const int hours);                  // Subtract specified hours
void HourInc();                                 // Add one hour
void HourInc(const int hours);                  // Add specified hours
```

#### Day Operations
```mql5
void DayDec();                                  // Subtract one day
void DayDec(const int days);                    // Subtract specified days
void DayInc();                                  // Add one day
void DayInc(const int days);                    // Add specified days
```

#### Month Operations
```mql5
void MonDec();                                  // Subtract one month
void MonDec(const int months);                  // Subtract specified months
void MonInc();                                  // Add one month
void MonInc(const int months);                  // Add specified months
```

#### Year Operations
```mql5
void YearDec();                                 // Subtract one year
void YearDec(const int years);                  // Subtract specified years
void YearInc();                                 // Add one year
void YearInc(const int years);                  // Add specified years
```

## Usage Examples

### Basic Date/Time Operations

```mql5
#include <Tools\DateTime.mqh>

void ExampleBasicDateTime()
{
    CDateTime dt;

    // Set current date/time
    dt.DateTime(TimeCurrent());

    Print("Current date/time information:");
    Print("  Full date: ", dt.year, "/", dt.mon, "/", dt.day);
    Print("  Full time: ", dt.hour, ":", dt.min, ":", dt.sec);
    Print("  Month name: ", dt.MonthName());
    Print("  Short month: ", dt.ShortMonthName());
    Print("  Day name: ", dt.DayName());
    Print("  Short day: ", dt.ShortDayName());
    Print("  Days in month: ", dt.DaysInMonth());
    Print("  Day of year: ", dt.day_of_year);

    // Time arithmetic examples
    dt.HourInc(2);
    Print("After adding 2 hours: ", TimeToString(dt.DateTime()));

    dt.DayDec(1);
    Print("After subtracting 1 day: ", TimeToString(dt.DateTime()));

    dt.MonInc(1);
    Print("After adding 1 month: ", TimeToString(dt.DateTime()));
}
```

### Date/Time Calculator

```mql5
class CDateTimeCalculator
{
private:
    CDateTime m_base_time;

public:
    CDateTimeCalculator();
    void SetBaseTime(const datetime base_time);
    datetime AddTime(const int years, const int months, const int days,
                    const int hours, const int minutes, const int seconds);
    datetime SubtractTime(const int years, const int months, const int days,
                         const int hours, const int minutes, const int seconds);
    int GetDaysDifference(const datetime date1, const datetime date2);
    string FormatDateTime(const datetime dt, const string format = "");
    bool IsWeekend(const datetime dt);
    bool IsBusinessDay(const datetime dt);
    datetime GetNextBusinessDay(const datetime dt);
};

CDateTimeCalculator::CDateTimeCalculator(void)
{
    m_base_time.DateTime(TimeCurrent());
}

void CDateTimeCalculator::SetBaseTime(const datetime base_time)
{
    m_base_time.DateTime(base_time);
}

datetime CDateTimeCalculator::AddTime(const int years, const int months, const int days,
                                     const int hours, const int minutes, const int seconds)
{
    CDateTime result = m_base_time;

    if(years != 0) result.YearInc(years);
    if(months != 0) result.MonInc(months);
    if(days != 0) result.DayInc(days);
    if(hours != 0) result.HourInc(hours);
    if(minutes != 0) result.MinInc(minutes);
    if(seconds != 0) result.SecInc(seconds);

    return result.DateTime();
}

datetime CDateTimeCalculator::SubtractTime(const int years, const int months, const int days,
                                          const int hours, const int minutes, const int seconds)
{
    CDateTime result = m_base_time;

    if(years != 0) result.YearDec(years);
    if(months != 0) result.MonDec(months);
    if(days != 0) result.DayDec(days);
    if(hours != 0) result.HourDec(hours);
    if(minutes != 0) result.MinDec(minutes);
    if(seconds != 0) result.SecDec(seconds);

    return result.DateTime();
}

int CDateTimeCalculator::GetDaysDifference(const datetime date1, const datetime date2)
{
    return (int)((date2 - date1) / 86400); // 86400 seconds in a day
}

string CDateTimeCalculator::FormatDateTime(const datetime dt, const string format = "")
{
    CDateTime temp;
    temp.DateTime(dt);

    if(format == "date_only")
    {
        return StringFormat("%04d-%02d-%02d", temp.year, temp.mon, temp.day);
    }
    else if(format == "time_only")
    {
        return StringFormat("%02d:%02d:%02d", temp.hour, temp.min, temp.sec);
    }
    else if(format == "short")
    {
        return StringFormat("%02d/%02d/%04d %02d:%02d",
                           temp.day, temp.mon, temp.year, temp.hour, temp.min);
    }
    else if(format == "long")
    {
        return StringFormat("%s, %s %d, %04d at %02d:%02d:%02d",
                           temp.DayName(), temp.MonthName(), temp.day, temp.year,
                           temp.hour, temp.min, temp.sec);
    }
    else
    {
        return TimeToString(dt, TIME_DATE | TIME_SECONDS);
    }
}

bool CDateTimeCalculator::IsWeekend(const datetime dt)
{
    CDateTime temp;
    temp.DateTime(dt);
    return (temp.day_of_week == 0 || temp.day_of_week == 6); // Sunday or Saturday
}

bool CDateTimeCalculator::IsBusinessDay(const datetime dt)
{
    return !IsWeekend(dt);
}

datetime CDateTimeCalculator::GetNextBusinessDay(const datetime dt)
{
    CDateTime temp;
    temp.DateTime(dt);

    // Move to next day
    temp.DayInc(1);

    // Skip weekends
    while(temp.day_of_week == 0 || temp.day_of_week == 6)
    {
        temp.DayInc(1);
    }

    return temp.DateTime();
}
```

### Trading Time Manager

```mql5
class CTradingTimeManager
{
private:
    struct STradingSession
    {
        int start_hour;
        int start_minute;
        int end_hour;
        int end_minute;
        bool enabled;
    };

    STradingSession m_sessions[4]; // Up to 4 trading sessions per day
    int m_session_count;

public:
    CTradingTimeManager();
    void AddTradingSession(const int start_hour, const int start_minute,
                          const int end_hour, const int end_minute);
    bool IsTradingTime(const datetime dt = 0);
    datetime GetNextTradingTime(const datetime dt = 0);
    datetime GetTradingSessionEnd(const datetime dt = 0);
    string GetTradingStatus(const datetime dt = 0);
    void PrintTradingSessions(void);
};

CTradingTimeManager::CTradingTimeManager(void) : m_session_count(0)
{
    // Add default session: 9:00 AM to 5:00 PM
    AddTradingSession(9, 0, 17, 0);
}

void CTradingTimeManager::AddTradingSession(const int start_hour, const int start_minute,
                                           const int end_hour, const int end_minute)
{
    if(m_session_count >= 4) return;

    m_sessions[m_session_count].start_hour = start_hour;
    m_sessions[m_session_count].start_minute = start_minute;
    m_sessions[m_session_count].end_hour = end_hour;
    m_sessions[m_session_count].end_minute = end_minute;
    m_sessions[m_session_count].enabled = true;

    m_session_count++;
}

bool CTradingTimeManager::IsTradingTime(const datetime dt = 0)
{
    datetime check_time = (dt == 0) ? TimeCurrent() : dt;
    CDateTime current;
    current.DateTime(check_time);

    // Check if weekend
    if(current.day_of_week == 0 || current.day_of_week == 6)
        return false;

    // Check trading sessions
    for(int i = 0; i < m_session_count; i++)
    {
        if(!m_sessions[i].enabled) continue;

        int current_minutes = current.hour * 60 + current.min;
        int session_start = m_sessions[i].start_hour * 60 + m_sessions[i].start_minute;
        int session_end = m_sessions[i].end_hour * 60 + m_sessions[i].end_minute;

        if(current_minutes >= session_start && current_minutes < session_end)
            return true;
    }

    return false;
}

datetime CTradingTimeManager::GetNextTradingTime(const datetime dt = 0)
{
    datetime check_time = (dt == 0) ? TimeCurrent() : dt;
    CDateTime next;
    next.DateTime(check_time);

    // If it's currently trading time, return current time
    if(IsTradingTime(check_time))
        return check_time;

    // Look for next trading session
    for(int days = 0; days < 7; days++) // Check up to a week ahead
    {
        // Skip weekends
        if(next.day_of_week == 0 || next.day_of_week == 6)
        {
            next.DayInc(1);
            next.Hour(0);
            next.Min(0);
            next.Sec(0);
            continue;
        }

        // Check sessions for this day
        for(int i = 0; i < m_session_count; i++)
        {
            if(!m_sessions[i].enabled) continue;

            CDateTime session_start = next;
            session_start.Hour(m_sessions[i].start_hour);
            session_start.Min(m_sessions[i].start_minute);
            session_start.Sec(0);

            if(session_start.DateTime() > check_time)
                return session_start.DateTime();
        }

        // Move to next day
        next.DayInc(1);
        next.Hour(0);
        next.Min(0);
        next.Sec(0);
    }

    return 0; // No trading time found
}

string CTradingTimeManager::GetTradingStatus(const datetime dt = 0)
{
    datetime check_time = (dt == 0) ? TimeCurrent() : dt;
    CDateTime current;
    current.DateTime(check_time);

    if(current.day_of_week == 0 || current.day_of_week == 6)
        return "Market Closed (Weekend)";

    if(IsTradingTime(check_time))
    {
        datetime session_end = GetTradingSessionEnd(check_time);
        CDateTime end_time;
        end_time.DateTime(session_end);

        int minutes_left = (int)((session_end - check_time) / 60);
        return StringFormat("Trading Active (%d minutes left)", minutes_left);
    }
    else
    {
        datetime next_session = GetNextTradingTime(check_time);
        if(next_session > 0)
        {
            int hours_until = (int)((next_session - check_time) / 3600);
            return StringFormat("Market Closed (%d hours until next session)", hours_until);
        }
        else
        {
            return "Market Closed";
        }
    }
}

datetime CTradingTimeManager::GetTradingSessionEnd(const datetime dt = 0)
{
    datetime check_time = (dt == 0) ? TimeCurrent() : dt;

    if(!IsTradingTime(check_time))
        return 0;

    CDateTime current;
    current.DateTime(check_time);

    // Find which session we're in
    for(int i = 0; i < m_session_count; i++)
    {
        if(!m_sessions[i].enabled) continue;

        int current_minutes = current.hour * 60 + current.min;
        int session_start = m_sessions[i].start_hour * 60 + m_sessions[i].start_minute;
        int session_end = m_sessions[i].end_hour * 60 + m_sessions[i].end_minute;

        if(current_minutes >= session_start && current_minutes < session_end)
        {
            CDateTime end_time = current;
            end_time.Hour(m_sessions[i].end_hour);
            end_time.Min(m_sessions[i].end_minute);
            end_time.Sec(0);
            return end_time.DateTime();
        }
    }

    return 0;
}

void CTradingTimeManager::PrintTradingSessions(void)
{
    Print("Trading Sessions:");
    for(int i = 0; i < m_session_count; i++)
    {
        if(m_sessions[i].enabled)
        {
            Print("  Session ", i+1, ": ",
                  StringFormat("%02d:%02d", m_sessions[i].start_hour, m_sessions[i].start_minute),
                  " - ",
                  StringFormat("%02d:%02d", m_sessions[i].end_hour, m_sessions[i].end_minute));
        }
    }
}
```

### Date/Time Validation Helper

```mql5
class CDateTimeValidator
{
public:
    static bool IsValidDate(const int year, const int month, const int day);
    static bool IsValidTime(const int hour, const int minute, const int second);
    static bool IsLeapYear(const int year);
    static int GetDaysInMonth(const int year, const int month);
    static bool IsValidDateTime(const CDateTime &dt);
    static string GetValidationError(const CDateTime &dt);
};

bool CDateTimeValidator::IsValidDate(const int year, const int month, const int day)
{
    if(year < 1970 || year > 3000) return false;
    if(month < 1 || month > 12) return false;
    if(day < 1 || day > GetDaysInMonth(year, month)) return false;
    return true;
}

bool CDateTimeValidator::IsValidTime(const int hour, const int minute, const int second)
{
    if(hour < 0 || hour > 23) return false;
    if(minute < 0 || minute > 59) return false;
    if(second < 0 || second > 59) return false;
    return true;
}

bool CDateTimeValidator::IsLeapYear(const int year)
{
    return (year % 4 == 0 && year % 100 != 0) || (year % 400 == 0);
}

int CDateTimeValidator::GetDaysInMonth(const int year, const int month)
{
    int days_in_month[] = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};

    if(month < 1 || month > 12) return 0;

    if(month == 2 && IsLeapYear(year))
        return 29;

    return days_in_month[month - 1];
}

bool CDateTimeValidator::IsValidDateTime(const CDateTime &dt)
{
    return IsValidDate(dt.year, dt.mon, dt.day) &&
           IsValidTime(dt.hour, dt.min, dt.sec);
}

string CDateTimeValidator::GetValidationError(const CDateTime &dt)
{
    if(!IsValidDate(dt.year, dt.mon, dt.day))
        return "Invalid date: " + IntegerToString(dt.year) + "/" +
               IntegerToString(dt.mon) + "/" + IntegerToString(dt.day);

    if(!IsValidTime(dt.hour, dt.min, dt.sec))
        return "Invalid time: " + IntegerToString(dt.hour) + ":" +
               IntegerToString(dt.min) + ":" + IntegerToString(dt.sec);

    return "";
}
```

## Practical Applications

### Event Scheduler
```mql5
void ScheduleTradingEvents()
{
    CTradingTimeManager trading_time;
    CDateTimeCalculator calculator;

    // Add trading sessions
    trading_time.AddTradingSession(9, 30, 16, 0);  // 9:30 AM - 4:00 PM
    trading_time.AddTradingSession(18, 0, 20, 0);  // 6:00 PM - 8:00 PM (evening session)

    // Check current trading status
    Print("Current status: ", trading_time.GetTradingStatus());

    // Schedule next analysis
    datetime next_analysis = calculator.AddTime(0, 0, 0, 1, 0, 0); // Add 1 hour
    Print("Next analysis scheduled for: ", calculator.FormatDateTime(next_analysis, "long"));

    // Find next business day
    datetime next_business = calculator.GetNextBusinessDay(TimeCurrent());
    Print("Next business day: ", calculator.FormatDateTime(next_business, "date_only"));
}
```

## Best Practices

1. **Date Validation**
   - Always validate date/time inputs
   - Handle leap years correctly
   - Consider time zone implications

2. **Performance**
   - Cache frequently used date calculations
   - Use appropriate date/time formats
   - Minimize conversions between formats

3. **Internationalization**
   - Use localized month and day names where appropriate
   - Consider different date formats for different regions
   - Handle different calendar systems if needed

4. **Trading Applications**
   - Account for market holidays
   - Handle trading session overlaps
   - Consider daylight saving time changes

## Common Use Cases

- **Trading Session Management** - Market hours and timing
- **Event Scheduling** - Automated task timing
- **Log Timestamps** - Recording event times
- **Performance Analysis** - Time-based calculations
- **Report Generation** - Date range filtering
- **Alarm Systems** - Time-based notifications

## See Also

- [CRect](./CRect.md) - Rectangle structure for spatial operations
- [Time Management Tutorial](../tutorials/time-management.md)
- [Trading Session Guide](../tutorials/trading-sessions.md)
- [Event Scheduling Patterns](../tutorials/event-scheduling.md)