# CWndClient - Scrollable Client Area Control

The `CWndClient` class provides a scrollable client area with automatic scrollbar management, enabling the creation of controls that can display content larger than their visible area.

## Class Overview

```mql5
class CWndClient : public CWndContainer
{
protected:
    // Scrolling flags
    bool              m_v_scrolled;      // Vertical scrolling enabled
    bool              m_h_scrolled;      // Horizontal scrolling enabled

    // Dependent controls
    CPanel            m_background;      // Scrollbar background
    CScrollV          m_scroll_v;        // Vertical scrollbar
    CScrollH          m_scroll_h;        // Horizontal scrollbar

public:
    virtual bool      Create(const long chart, const string name, const int subwin,
                             const int x1, const int y1, const int x2, const int y2);

    // Visual properties
    virtual bool      ColorBackground(const color value);
    virtual bool      ColorBorder(const color value);
    virtual bool      BorderType(const ENUM_BORDER_TYPE flag);

    // Scrolling settings
    virtual bool      VScrolled(void);
    virtual bool      VScrolled(const bool flag);
    virtual bool      HScrolled(void);
    virtual bool      HScrolled(const bool flag);

    // State management
    virtual bool      Show(void);

protected:
    // Control creation
    virtual bool      CreateBack(void);
    virtual bool      CreateScrollV(void);
    virtual bool      CreateScrollH(void);

    // Event handlers
    virtual bool      OnVScrollShow(void);
    virtual bool      OnVScrollHide(void);
    virtual bool      OnHScrollShow(void);
    virtual bool      OnHScrollHide(void);
    virtual bool      OnScrollLineDown(void);
    virtual bool      OnScrollLineUp(void);
    virtual bool      OnScrollLineLeft(void);
    virtual bool      OnScrollLineRight(void);

    // Layout management
    virtual bool      Rebound(const CRect &rect);
};
```

## Key Features

### 1. **Automatic Scrollbar Management**
- Dynamically shows/hides scrollbars based on content size
- Independent vertical and horizontal scrolling
- Smooth scrolling operations

### 2. **Content Clipping**
- Displays only visible portion of content
- Automatic content boundaries
- Efficient rendering of large datasets

### 3. **Flexible Layout**
- Customizable background and borders
- Scrollbar positioning and styling
- Container for other controls

## Basic Usage

### Creating a Scrollable Text Display

```mql5
class CScrollableTextDisplay : public CWndClient
{
private:
    CArrayObj m_text_labels;  // Array of text labels
    int       m_line_height;  // Height of each text line
    int       m_content_height; // Total content height
    string    m_text_content[]; // Text lines

public:
    virtual bool Create(const long chart, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    // Content management
    bool AddTextLine(const string text);
    bool SetTextContent(const string &lines[]);
    void ClearContent();

protected:
    // Scrolling event handlers
    virtual bool OnScrollLineUp();
    virtual bool OnScrollLineDown();
    virtual bool OnScrollLineLeft();
    virtual bool OnScrollLineRight();

    // Layout management
    void UpdateContentLayout();
    void UpdateScrollbars();
    void CreateTextLabels();
};

bool CScrollableTextDisplay::Create(const long chart, const string name, const int subwin,
                                   const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndClient::Create(chart, name, subwin, x1, y1, x2, y2))
        return false;

    m_line_height = 18;
    m_content_height = 0;

    // Enable vertical scrolling
    VScrolled(true);
    HScrolled(false); // Disable horizontal scrolling for this example

    // Set background color
    ColorBackground(clrWhite);
    ColorBorder(C'0xB2,0xC3,0xCF');

    return true;
}

bool CScrollableTextDisplay::AddTextLine(const string text)
{
    // Add text to array
    int new_size = ArraySize(m_text_content) + 1;
    ArrayResize(m_text_content, new_size);
    m_text_content[new_size - 1] = text;

    // Update layout
    UpdateContentLayout();
    return true;
}

void CScrollableTextDisplay::UpdateContentLayout()
{
    // Calculate total content height
    m_content_height = ArraySize(m_text_content) * m_line_height;

    // Clear existing labels
    for(int i = m_text_labels.Total() - 1; i >= 0; i--)
    {
        CLabel* label = m_text_labels.At(i);
        if(label)
        {
            Delete(label);
            delete label;
        }
    }
    m_text_labels.Clear();

    // Create new labels for visible content
    CreateTextLabels();
    UpdateScrollbars();
}

void CScrollableTextDisplay::CreateTextLabels()
{
    int visible_height = Height();
    int visible_lines = visible_height / m_line_height + 2; // Extra lines for smooth scrolling

    int start_line = 0; // This would be calculated based on scroll position
    int end_line = MathMin(start_line + visible_lines, ArraySize(m_text_content));

    for(int i = start_line; i < end_line; i++)
    {
        CLabel* label = new CLabel();
        if(label)
        {
            int y_pos = i * m_line_height;
            if(label->Create(ChartID(), Name() + "Line" + IntegerToString(i), 0,
                            5, y_pos, Width() - 5, y_pos + m_line_height))
            {
                label->Text(m_text_content[i]);
                label->Font("Courier New");
                label->FontSize(9);
                Add(label);
                m_text_labels.Add(label);
            }
        }
    }
}

bool CScrollableTextDisplay::OnScrollLineUp()
{
    // Handle vertical scroll up
    Print("Scroll up");
    // Update visible content based on new scroll position
    CreateTextLabels();
    return true;
}

bool CScrollableTextDisplay::OnScrollLineDown()
{
    // Handle vertical scroll down
    Print("Scroll down");
    // Update visible content based on new scroll position
    CreateTextLabels();
    return true;
}
```

### Scrollable Image Gallery

```mql5
class CScrollableImageGallery : public CWndClient
{
private:
    CArrayObj m_image_controls; // Array of CPicture controls
    int       m_images_per_row;  // Number of images per row
    int       m_image_width;     // Width of each image
    int       m_image_height;    // Height of each image
    int       m_image_spacing;   // Spacing between images
    string    m_image_files[];   // Array of image file paths

public:
    virtual bool Create(const long chart, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    // Gallery management
    bool LoadImagesFromFolder(const string folder_path);
    bool AddImage(const string image_path);
    void SetImagesPerRow(int count);

protected:
    virtual bool OnScrollLineUp();
    virtual bool OnScrollLineDown();
    virtual bool OnScrollLineLeft();
    virtual bool OnScrollLineRight();

    void UpdateGalleryLayout();
    void CreateImageControls();
    void CalculateContentDimensions();
};

bool CScrollableImageGallery::Create(const long chart, const string name, const int subwin,
                                    const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndClient::Create(chart, name, subwin, x1, y1, x2, y2))
        return false;

    m_images_per_row = 4;
    m_image_width = 80;
    m_image_height = 60;
    m_image_spacing = 5;

    // Enable both scrollbars
    VScrolled(true);
    HScrolled(true);

    // Set gallery background
    ColorBackground(C'0xF5,0xF5,0xF5');
    ColorBorder(C'0xA0,0xA0,0xA0');

    return true;
}

bool CScrollableImageGallery::AddImage(const string image_path)
{
    int new_size = ArraySize(m_image_files) + 1;
    ArrayResize(m_image_files, new_size);
    m_image_files[new_size - 1] = image_path;

    UpdateGalleryLayout();
    return true;
}

void CScrollableImageGallery::UpdateGalleryLayout()
{
    CalculateContentDimensions();
    CreateImageControls();
}

void CScrollableImageGallery::CalculateContentDimensions()
{
    int total_images = ArraySize(m_image_files);
    if(total_images == 0) return;

    int rows = (total_images + m_images_per_row - 1) / m_images_per_row;
    int content_width = m_images_per_row * (m_image_width + m_image_spacing) - m_image_spacing;
    int content_height = rows * (m_image_height + m_image_spacing) - m_image_spacing;

    // Update scrollbar ranges based on content dimensions
    // This would require access to scrollbar position methods
}

void CScrollableImageGallery::CreateImageControls()
{
    // Clear existing image controls
    for(int i = m_image_controls.Total() - 1; i >= 0; i--)
    {
        CPicture* picture = m_image_controls.At(i);
        if(picture)
        {
            Delete(picture);
            delete picture;
        }
    }
    m_image_controls.Clear();

    // Calculate visible range based on scroll position
    int visible_start = 0; // Calculate based on scroll position
    int visible_end = MathMin(visible_start + 20, ArraySize(m_image_files)); // Show max 20 images

    for(int i = visible_start; i < visible_end; i++)
    {
        int row = i / m_images_per_row;
        int col = i % m_images_per_row;

        int x_pos = col * (m_image_width + m_image_spacing);
        int y_pos = row * (m_image_height + m_image_spacing);

        CPicture* picture = new CPicture();
        if(picture)
        {
            if(picture->Create(ChartID(), Name() + "Image" + IntegerToString(i), 0,
                              x_pos, y_pos, x_pos + m_image_width, y_pos + m_image_height))
            {
                picture->BmpName(m_image_files[i]);
                Add(picture);
                m_image_controls.Add(picture);
            }
        }
    }
}
```

## Advanced Usage Patterns

### 1. **Data Grid with Scrolling**

```mql5
class CScrollableDataGrid : public CWndClient
{
private:
    struct SGridCell
    {
        string text;
        color  background_color;
        color  text_color;
        int    column_width;
    };

    CArrayObj m_cell_controls;   // Grid cell controls
    SGridCell m_grid_data[][];   // 2D array of grid data
    int       m_row_height;      // Height of each row
    int       m_column_widths[]; // Width of each column
    int       m_visible_rows;    // Number of visible rows
    int       m_visible_cols;    // Number of visible columns
    int       m_scroll_row;      // Current scroll row position
    int       m_scroll_col;      // Current scroll column position

public:
    virtual bool Create(const long chart, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    // Data management
    bool SetGridData(const string &data[][], const int &column_widths[]);
    bool SetCellValue(int row, int col, const string value);
    string GetCellValue(int row, int col);

    // Grid configuration
    void SetRowHeight(int height);
    void SetColumnWidth(int column, int width);

protected:
    virtual bool OnScrollLineUp();
    virtual bool OnScrollLineDown();
    virtual bool OnScrollLineLeft();
    virtual bool OnScrollLineRight();

    void UpdateGridLayout();
    void CreateVisibleCells();
    void UpdateScrollPosition(int delta_row, int delta_col);
};

bool CScrollableDataGrid::Create(const long chart, const string name, const int subwin,
                                const int x1, const int y1, const int x2, const int y2)
{
    if(!CWndClient::Create(chart, name, subwin, x1, y1, x2, y2))
        return false;

    m_row_height = 20;
    m_scroll_row = 0;
    m_scroll_col = 0;

    // Enable both scrollbars
    VScrolled(true);
    HScrolled(true);

    // Set grid appearance
    ColorBackground(clrWhite);
    ColorBorder(C'0x80,0x80,0x80');
    BorderType(BORDER_FLAT);

    return true;
}

bool CScrollableDataGrid::SetGridData(const string &data[][], const int &column_widths[])
{
    // Copy data
    int rows = ArrayRange(data, 0);
    int cols = ArrayRange(data, 1);

    ArrayResize(m_grid_data, rows, cols);
    ArrayCopy(m_column_widths, column_widths);

    for(int r = 0; r < rows; r++)
    {
        for(int c = 0; c < cols; c++)
        {
            m_grid_data[r][c].text = data[r][c];
            m_grid_data[r][c].background_color = (r % 2 == 0) ? clrWhite : C'0xF8,0xF8,0xF8';
            m_grid_data[r][c].text_color = clrBlack;
        }
    }

    UpdateGridLayout();
    return true;
}

void CScrollableDataGrid::UpdateGridLayout()
{
    m_visible_rows = Height() / m_row_height;

    // Calculate visible columns based on width
    int total_width = 0;
    m_visible_cols = 0;
    for(int i = 0; i < ArraySize(m_column_widths); i++)
    {
        total_width += m_column_widths[i];
        if(total_width <= Width())
            m_visible_cols++;
        else
            break;
    }

    CreateVisibleCells();
}

void CScrollableDataGrid::CreateVisibleCells()
{
    // Clear existing cells
    for(int i = m_cell_controls.Total() - 1; i >= 0; i--)
    {
        CLabel* cell = m_cell_controls.At(i);
        if(cell)
        {
            Delete(cell);
            delete cell;
        }
    }
    m_cell_controls.Clear();

    // Create visible cells
    int rows = ArrayRange(m_grid_data, 0);
    int cols = ArrayRange(m_grid_data, 1);

    for(int r = 0; r < m_visible_rows && (r + m_scroll_row) < rows; r++)
    {
        int x_pos = 0;
        for(int c = 0; c < m_visible_cols && (c + m_scroll_col) < cols; c++)
        {
            int data_row = r + m_scroll_row;
            int data_col = c + m_scroll_col;

            CLabel* cell = new CLabel();
            if(cell)
            {
                int y_pos = r * m_row_height;
                int cell_width = m_column_widths[data_col];

                if(cell->Create(ChartID(), Name() + "Cell" + IntegerToString(data_row) + "_" + IntegerToString(data_col), 0,
                               x_pos, y_pos, x_pos + cell_width, y_pos + m_row_height))
                {
                    cell->Text(m_grid_data[data_row][data_col].text);
                    cell->ColorBackground(m_grid_data[data_row][data_col].background_color);
                    cell->Color(m_grid_data[data_row][data_col].text_color);
                    cell->Font("Arial");
                    cell->FontSize(8);

                    Add(cell);
                    m_cell_controls.Add(cell);
                }
            }

            x_pos += cell_width;
        }
    }
}

bool CScrollableDataGrid::OnScrollLineUp()
{
    if(m_scroll_row > 0)
    {
        UpdateScrollPosition(-1, 0);
        return true;
    }
    return false;
}

bool CScrollableDataGrid::OnScrollLineDown()
{
    int total_rows = ArrayRange(m_grid_data, 0);
    if(m_scroll_row + m_visible_rows < total_rows)
    {
        UpdateScrollPosition(1, 0);
        return true;
    }
    return false;
}

bool CScrollableDataGrid::OnScrollLineLeft()
{
    if(m_scroll_col > 0)
    {
        UpdateScrollPosition(0, -1);
        return true;
    }
    return false;
}

bool CScrollableDataGrid::OnScrollLineRight()
{
    int total_cols = ArrayRange(m_grid_data, 1);
    if(m_scroll_col + m_visible_cols < total_cols)
    {
        UpdateScrollPosition(0, 1);
        return true;
    }
    return false;
}

void CScrollableDataGrid::UpdateScrollPosition(int delta_row, int delta_col)
{
    m_scroll_row += delta_row;
    m_scroll_col += delta_col;

    // Ensure bounds
    m_scroll_row = MathMax(0, m_scroll_row);
    m_scroll_col = MathMax(0, m_scroll_col);

    CreateVisibleCells();
}
```

### 2. **Custom Scrollable Control**

```mql5
class CScrollableControlContainer : public CWndClient
{
private:
    CArrayObj m_controls;        // Array of child controls
    int       m_content_width;   // Total content width
    int       m_content_height;  // Total content height
    int       m_scroll_x;        // Current horizontal scroll position
    int       m_scroll_y;        // Current vertical scroll position

public:
    virtual bool Create(const long chart, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    // Control management
    bool AddControl(CWnd* control, int x, int y);
    bool RemoveControl(CWnd* control);
    void UpdateContentSize();

protected:
    virtual bool OnScrollLineUp();
    virtual bool OnScrollLineDown();
    virtual bool OnScrollLineLeft();
    virtual bool OnScrollLineRight();

    void UpdateControlPositions();
    void CalculateContentBounds();
};

bool CScrollableControlContainer::AddControl(CWnd* control, int x, int y)
{
    if(!control) return false;

    // Store original position for scrolling calculations
    control->Move(x - m_scroll_x, y - m_scroll_y);

    if(Add(control))
    {
        m_controls.Add(control);
        UpdateContentSize();
        return true;
    }

    return false;
}

void CScrollableControlContainer::UpdateContentSize()
{
    CalculateContentBounds();

    // Enable scrollbars if content exceeds visible area
    VScrolled(m_content_height > Height());
    HScrolled(m_content_width > Width());
}

void CScrollableControlContainer::CalculateContentBounds()
{
    m_content_width = 0;
    m_content_height = 0;

    for(int i = 0; i < m_controls.Total(); i++)
    {
        CWnd* control = m_controls.At(i);
        if(control)
        {
            int right = control->Left() + control->Width() + m_scroll_x;
            int bottom = control->Top() + control->Height() + m_scroll_y;

            m_content_width = MathMax(m_content_width, right);
            m_content_height = MathMax(m_content_height, bottom);
        }
    }
}

bool CScrollableControlContainer::OnScrollLineDown()
{
    const int scroll_step = 20;
    if(m_scroll_y + Height() < m_content_height)
    {
        m_scroll_y += scroll_step;
        UpdateControlPositions();
        return true;
    }
    return false;
}

void CScrollableControlContainer::UpdateControlPositions()
{
    for(int i = 0; i < m_controls.Total(); i++)
    {
        CWnd* control = m_controls.At(i);
        if(control)
        {
            // Update position based on scroll offset
            int new_x = control->Left() + m_scroll_x;
            int new_y = control->Top() + m_scroll_y;
            control->Move(new_x, new_y);
        }
    }
}
```

## Integration with Other Controls

### Using CWndClient as ListView Base

```mql5
class CCustomListView : public CWndClient
{
private:
    CArrayObj m_list_items;      // List item controls
    int       m_item_height;     // Height of each item
    int       m_selected_index;  // Currently selected item
    color     m_selection_color; // Selection highlight color

public:
    virtual bool Create(const long chart, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

    // List management
    int  AddItem(const string text, const long value = 0);
    bool DeleteItem(int index);
    void ClearItems();

    // Selection
    int  SelectedIndex() const { return m_selected_index; }
    bool SelectItem(int index);

protected:
    virtual bool OnScrollLineUp();
    virtual bool OnScrollLineDown();

    void UpdateItemLayout();
    void CreateVisibleItems();
    void OnItemClick(int item_index);
};

// Implementation would follow similar patterns to the data grid example
```

## Performance Optimization

### Efficient Content Management

```mql5
class COptimizedScrollClient : public CWndClient
{
private:
    // Virtual rendering for large datasets
    int m_total_items;           // Total number of items
    int m_visible_start;         // First visible item index
    int m_visible_count;         // Number of visible items
    int m_buffer_size;           // Number of buffered items

public:
    void SetTotalItems(int count);
    void SetBufferSize(int buffer_count);

protected:
    // Virtual methods for data access
    virtual string GetItemText(int index) = 0;
    virtual void   RenderItem(int index, int visible_position) = 0;

    void UpdateVisibleRange();
    void OptimizedScroll(int delta);
};

void COptimizedScrollClient::OptimizedScroll(int delta)
{
    int new_start = m_visible_start + delta;
    new_start = MathMax(0, MathMin(new_start, m_total_items - m_visible_count));

    if(new_start != m_visible_start)
    {
        m_visible_start = new_start;
        // Only update items that changed
        UpdateVisibleRange();
    }
}
```

## Best Practices

### 1. **Memory Management**
- Use virtual rendering for large datasets
- Implement object pooling for frequently created/destroyed items
- Clean up controls properly when scrolling

### 2. **Performance**
- Limit the number of simultaneously visible controls
- Use buffering for smooth scrolling
- Implement lazy loading for content

### 3. **User Experience**
- Provide visual feedback during scrolling
- Implement smooth scrolling animations when possible
- Maintain scroll position during content updates

### 4. **Layout Management**
- Calculate content dimensions accurately
- Handle dynamic content size changes
- Implement proper boundary checking

The CWndClient control provides the foundation for creating sophisticated scrollable interfaces that can handle large amounts of content efficiently while maintaining smooth user interaction.

## See Also

- [Complex Controls Overview](./README.md) - Overview of all complex controls
- [CListView Documentation](./CListView.md) - List control implementation using CWndClient
- [ScrollBars Documentation](./ScrollBars.md) - Scrollbar controls and management
- [Advanced Patterns Guide](../tutorials/advanced-patterns.md) - Dynamic control techniques