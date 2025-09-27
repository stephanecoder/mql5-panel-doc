# CRect - Rectangle Structure

The `CRect` class provides functionality for managing rectangular areas on charts, including coordinate handling, movement operations, and area calculations.

## Overview

- **Header File**: `<Controls\Rect.mqh>`
- **Purpose**: Rectangle area management and coordinate operations
- **Key Features**: Coordinate storage, movement operations, containment testing
- **Usage**: UI layout, collision detection, area calculations

## Properties

### Coordinate Properties
```mql5
int Left;       // X coordinate of upper-left corner
int Top;        // Y coordinate of upper-left corner
int Right;      // X coordinate of lower-right corner
int Bottom;     // Y coordinate of lower-right corner
```

### Calculated Properties
```mql5
int Width();    // Rectangle width (Right - Left)
int Height();   // Rectangle height (Bottom - Top)
```

## Core Methods

### Coordinate Management

#### SetBound()
Sets new area coordinates for the rectangle.

```mql5
void SetBound(const int left, const int top, const int right, const int bottom);
```

**Parameters:**
- `left` - X coordinate of upper-left corner
- `top` - Y coordinate of upper-left corner
- `right` - X coordinate of lower-right corner
- `bottom` - Y coordinate of lower-right corner

#### Move()
Performs absolute displacement of area coordinates.

```mql5
void Move(const int x, const int y);
```

**Parameters:**
- `x` - New X coordinate for the upper-left corner
- `y` - New Y coordinate for the upper-left corner

#### Shift()
Performs relative displacement of area coordinates.

```mql5
void Shift(const int dx, const int dy);
```

**Parameters:**
- `dx` - Horizontal displacement
- `dy` - Vertical displacement

### Area Testing

#### Contains()
Checks if a point is inside the rectangular area.

```mql5
bool Contains(const int x, const int y) const;
```

**Parameters:**
- `x` - X coordinate of the point to test
- `y` - Y coordinate of the point to test

**Returns:** `true` if the point is inside the rectangle, `false` otherwise

### Utility Methods

#### Format()
Converts area coordinates to a string representation.

```mql5
string Format() const;
```

**Returns:** String representation of the rectangle coordinates

## Usage Examples

### Basic Rectangle Operations

```mql5
#include <Controls\Rect.mqh>

void ExampleBasicRectangle()
{
    CRect rect;

    // Set initial bounds
    rect.SetBound(10, 20, 110, 80);

    Print("Rectangle created:");
    Print("  Left: ", rect.Left, ", Top: ", rect.Top);
    Print("  Right: ", rect.Right, ", Bottom: ", rect.Bottom);
    Print("  Width: ", rect.Width(), ", Height: ", rect.Height());
    Print("  Format: ", rect.Format());

    // Test point containment
    bool contains_point1 = rect.Contains(50, 50);  // Should be true
    bool contains_point2 = rect.Contains(150, 50); // Should be false

    Print("Point (50,50) is inside: ", contains_point1);
    Print("Point (150,50) is inside: ", contains_point2);

    // Move rectangle to new position
    rect.Move(100, 100);
    Print("After move to (100,100): ", rect.Format());

    // Shift rectangle by offset
    rect.Shift(20, -10);
    Print("After shift by (20,-10): ", rect.Format());
}
```

### Rectangle Layout Manager

```mql5
class CRectangleLayoutManager
{
private:
    CRect m_container;
    CRect m_controls[50];
    int   m_control_count;

public:
    CRectangleLayoutManager();
    void SetContainer(const int left, const int top, const int right, const int bottom);
    bool AddControl(const int width, const int height);
    void ArrangeGrid(const int columns, const int margin = 5);
    void ArrangeVertical(const int margin = 5);
    void ArrangeHorizontal(const int margin = 5);
    CRect GetControlRect(const int index) const;
    void PrintLayout(void);
};

CRectangleLayoutManager::CRectangleLayoutManager(void) : m_control_count(0)
{
}

void CRectangleLayoutManager::SetContainer(const int left, const int top,
                                          const int right, const int bottom)
{
    m_container.SetBound(left, top, right, bottom);
    Print("Container set: ", m_container.Format());
}

bool CRectangleLayoutManager::AddControl(const int width, const int height)
{
    if(m_control_count >= 50) return false;

    // Initially position at origin, will be arranged later
    m_controls[m_control_count].SetBound(0, 0, width, height);
    m_control_count++;

    return true;
}

void CRectangleLayoutManager::ArrangeGrid(const int columns, const int margin = 5)
{
    if(columns <= 0 || m_control_count == 0) return;

    int available_width = m_container.Width() - (2 * margin);
    int available_height = m_container.Height() - (2 * margin);

    int rows = (m_control_count + columns - 1) / columns;
    int cell_width = available_width / columns;
    int cell_height = available_height / rows;

    for(int i = 0; i < m_control_count; i++)
    {
        int row = i / columns;
        int col = i % columns;

        int control_width = m_controls[i].Width();
        int control_height = m_controls[i].Height();

        // Center control within cell
        int x = m_container.Left + margin + (col * cell_width) + (cell_width - control_width) / 2;
        int y = m_container.Top + margin + (row * cell_height) + (cell_height - control_height) / 2;

        m_controls[i].Move(x, y);
    }

    Print("Arranged ", m_control_count, " controls in ", columns, " columns grid");
}

void CRectangleLayoutManager::ArrangeVertical(const int margin = 5)
{
    if(m_control_count == 0) return;

    int y_pos = m_container.Top + margin;
    int available_width = m_container.Width() - (2 * margin);

    for(int i = 0; i < m_control_count; i++)
    {
        int control_width = m_controls[i].Width();
        int control_height = m_controls[i].Height();

        // Center horizontally
        int x = m_container.Left + margin + (available_width - control_width) / 2;

        m_controls[i].Move(x, y_pos);
        y_pos += control_height + margin;
    }

    Print("Arranged ", m_control_count, " controls vertically");
}

void CRectangleLayoutManager::ArrangeHorizontal(const int margin = 5)
{
    if(m_control_count == 0) return;

    int x_pos = m_container.Left + margin;
    int available_height = m_container.Height() - (2 * margin);

    for(int i = 0; i < m_control_count; i++)
    {
        int control_width = m_controls[i].Width();
        int control_height = m_controls[i].Height();

        // Center vertically
        int y = m_container.Top + margin + (available_height - control_height) / 2;

        m_controls[i].Move(x_pos, y);
        x_pos += control_width + margin;
    }

    Print("Arranged ", m_control_count, " controls horizontally");
}

CRect CRectangleLayoutManager::GetControlRect(const int index) const
{
    CRect empty_rect;
    if(index >= 0 && index < m_control_count)
        return m_controls[index];
    return empty_rect;
}

void CRectangleLayoutManager::PrintLayout(void)
{
    Print("Layout Manager State:");
    Print("  Container: ", m_container.Format());
    Print("  Controls: ", m_control_count);

    for(int i = 0; i < m_control_count; i++)
    {
        Print("    Control ", i, ": ", m_controls[i].Format());
    }
}
```

### Rectangle Collision Detection

```mql5
class CRectangleCollisionDetector
{
public:
    static bool Intersects(const CRect &rect1, const CRect &rect2);
    static bool Contains(const CRect &container, const CRect &contained);
    static CRect GetIntersection(const CRect &rect1, const CRect &rect2);
    static CRect GetUnion(const CRect &rect1, const CRect &rect2);
    static int GetDistance(const CRect &rect1, const CRect &rect2);
};

bool CRectangleCollisionDetector::Intersects(const CRect &rect1, const CRect &rect2)
{
    return !(rect1.Right <= rect2.Left ||
             rect2.Right <= rect1.Left ||
             rect1.Bottom <= rect2.Top ||
             rect2.Bottom <= rect1.Top);
}

bool CRectangleCollisionDetector::Contains(const CRect &container, const CRect &contained)
{
    return (contained.Left >= container.Left &&
            contained.Top >= container.Top &&
            contained.Right <= container.Right &&
            contained.Bottom <= container.Bottom);
}

CRect CRectangleCollisionDetector::GetIntersection(const CRect &rect1, const CRect &rect2)
{
    CRect intersection;

    if(!Intersects(rect1, rect2))
    {
        // No intersection - return empty rectangle
        intersection.SetBound(0, 0, 0, 0);
        return intersection;
    }

    int left = MathMax(rect1.Left, rect2.Left);
    int top = MathMax(rect1.Top, rect2.Top);
    int right = MathMin(rect1.Right, rect2.Right);
    int bottom = MathMin(rect1.Bottom, rect2.Bottom);

    intersection.SetBound(left, top, right, bottom);
    return intersection;
}

CRect CRectangleCollisionDetector::GetUnion(const CRect &rect1, const CRect &rect2)
{
    CRect union_rect;

    int left = MathMin(rect1.Left, rect2.Left);
    int top = MathMin(rect1.Top, rect2.Top);
    int right = MathMax(rect1.Right, rect2.Right);
    int bottom = MathMax(rect1.Bottom, rect2.Bottom);

    union_rect.SetBound(left, top, right, bottom);
    return union_rect;
}

int CRectangleCollisionDetector::GetDistance(const CRect &rect1, const CRect &rect2)
{
    if(Intersects(rect1, rect2))
        return 0; // Rectangles overlap

    // Calculate minimum distance between rectangles
    int dx = 0;
    int dy = 0;

    if(rect1.Right < rect2.Left)
        dx = rect2.Left - rect1.Right;
    else if(rect2.Right < rect1.Left)
        dx = rect1.Left - rect2.Right;

    if(rect1.Bottom < rect2.Top)
        dy = rect2.Top - rect1.Bottom;
    else if(rect2.Bottom < rect1.Top)
        dy = rect1.Top - rect2.Bottom;

    return (int)MathSqrt(dx * dx + dy * dy);
}
```

### Rectangle Animation Helper

```mql5
class CRectangleAnimator
{
private:
    CRect m_start_rect;
    CRect m_end_rect;
    CRect m_current_rect;
    int   m_total_steps;
    int   m_current_step;
    bool  m_animation_active;

public:
    CRectangleAnimator();
    void StartAnimation(const CRect &start, const CRect &end, const int steps);
    bool UpdateAnimation(void);
    CRect GetCurrentRect(void) const { return m_current_rect; }
    bool IsAnimating(void) const { return m_animation_active; }
    double GetProgress(void) const;
};

CRectangleAnimator::CRectangleAnimator(void) : m_total_steps(0),
                                               m_current_step(0),
                                               m_animation_active(false)
{
}

void CRectangleAnimator::StartAnimation(const CRect &start, const CRect &end, const int steps)
{
    m_start_rect = start;
    m_end_rect = end;
    m_current_rect = start;
    m_total_steps = MathMax(1, steps);
    m_current_step = 0;
    m_animation_active = true;

    Print("Animation started from ", start.Format(), " to ", end.Format(), " in ", steps, " steps");
}

bool CRectangleAnimator::UpdateAnimation(void)
{
    if(!m_animation_active) return false;

    m_current_step++;

    if(m_current_step >= m_total_steps)
    {
        // Animation complete
        m_current_rect = m_end_rect;
        m_animation_active = false;
        Print("Animation completed");
        return false;
    }

    // Calculate interpolated position
    double progress = (double)m_current_step / m_total_steps;

    int left = (int)(m_start_rect.Left + (m_end_rect.Left - m_start_rect.Left) * progress);
    int top = (int)(m_start_rect.Top + (m_end_rect.Top - m_start_rect.Top) * progress);
    int right = (int)(m_start_rect.Right + (m_end_rect.Right - m_start_rect.Right) * progress);
    int bottom = (int)(m_start_rect.Bottom + (m_end_rect.Bottom - m_start_rect.Bottom) * progress);

    m_current_rect.SetBound(left, top, right, bottom);

    return true;
}

double CRectangleAnimator::GetProgress(void) const
{
    if(m_total_steps == 0) return 1.0;
    return (double)m_current_step / m_total_steps;
}
```

## Practical Applications

### UI Layout System
```mql5
void CreateButtonLayout()
{
    CRectangleLayoutManager layout;

    // Set container area
    layout.SetContainer(10, 10, 300, 200);

    // Add buttons of different sizes
    layout.AddControl(80, 30);  // Button 1
    layout.AddControl(80, 30);  // Button 2
    layout.AddControl(80, 30);  // Button 3
    layout.AddControl(80, 30);  // Button 4

    // Arrange in 2-column grid
    layout.ArrangeGrid(2, 10);

    // Get positions for actual button creation
    for(int i = 0; i < 4; i++)
    {
        CRect button_rect = layout.GetControlRect(i);
        Print("Button ", i, " position: ", button_rect.Format());
        // Create actual button at this position
    }
}
```

### Collision Detection Example
```mql5
void TestCollisionDetection()
{
    CRect rect1, rect2;

    rect1.SetBound(10, 10, 50, 50);
    rect2.SetBound(30, 30, 70, 70);

    bool intersects = CRectangleCollisionDetector::Intersects(rect1, rect2);
    Print("Rectangles intersect: ", intersects);

    if(intersects)
    {
        CRect intersection = CRectangleCollisionDetector::GetIntersection(rect1, rect2);
        Print("Intersection area: ", intersection.Format());
    }

    CRect union_rect = CRectangleCollisionDetector::GetUnion(rect1, rect2);
    Print("Union area: ", union_rect.Format());
}
```

## Best Practices

1. **Coordinate Validation**
   - Ensure Left ≤ Right and Top ≤ Bottom
   - Validate coordinates before operations
   - Handle negative dimensions appropriately

2. **Performance**
   - Use rectangle operations for layout calculations
   - Cache frequently accessed rectangle properties
   - Minimize rectangle creation in loops

3. **Layout Design**
   - Use consistent margins and spacing
   - Plan for responsive layouts
   - Consider different screen resolutions

4. **Collision Detection**
   - Implement efficient spatial partitioning for many rectangles
   - Use bounding box tests before detailed collision checks
   - Cache collision results when possible

## Common Use Cases

- **UI Layout Management** - Positioning and sizing controls
- **Collision Detection** - Testing overlaps and intersections
- **Animation Systems** - Moving and resizing elements
- **Selection Areas** - Defining selectable regions
- **Clipping Regions** - Restricting drawing areas
- **Hit Testing** - Determining click targets

## See Also

- [CWnd](../foundation/CWnd.md) - Base window class using rectangles
- [Layout Management Tutorial](../tutorials/layout-management.md)
- [Animation Techniques](../tutorials/animation-techniques.md)
- [Collision Detection Guide](../tutorials/collision-detection.md)