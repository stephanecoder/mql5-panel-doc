# CPicture - Bitmap Image Control

The `CPicture` class creates image display controls based on the "Bitmap Label" chart object for showing bitmap images in MQL5 applications.

## Class Hierarchy

```
CObject
└── CWnd
    └── CWndObj
        └── CPicture
```

## Overview

- **Header File**: `<Controls\Picture.mqh>`
- **Base Object**: Bitmap Label chart object
- **Purpose**: Display bitmap images and graphics
- **Key Features**: Image loading, scaling, positioning, event handling

## Core Methods

### Creation

#### Create()
Creates the picture control with specified parameters.

```mql5
virtual bool Create(const long chart_id, const string name, const int subwin,
                   const int x1, const int y1, const int x2, const int y2);
```

#### CreatePicture()
Specialized creation method for picture-specific initialization.

```mql5
bool CreatePicture(const long chart_id, const string name, const int subwin,
                  const int x1, const int y1, const int x2, const int y2);
```

### Image Management

#### BmpName()
Gets or sets the bitmap file name for the image.

```mql5
string BmpName() const;                     // Get bitmap filename
virtual bool BmpName(const string name);   // Set bitmap filename
```

**File Path Notes:**
- Use relative paths from the MQL5 directory
- Common location: `\\Images\\filename.bmp`
- Supported formats: BMP files

## Event Handling

### Lifecycle Events

#### OnCreate()
Called when the picture control is created.

```mql5
virtual bool OnCreate(void);
```

#### OnShow/OnHide()
Called when picture visibility changes.

```mql5
virtual bool OnShow(void);  // Picture becomes visible
virtual bool OnHide(void);  // Picture becomes hidden
```

### Layout Events

#### OnMove()
Called when the picture is moved.

```mql5
virtual bool OnMove(void);
```

#### OnChange()
Called when picture properties change.

```mql5
virtual bool OnChange(void);
```

## Usage Examples

### Basic Image Display

```mql5
#include <Controls\Picture.mqh>
#include <Controls\Dialog.mqh>

class CImageDialog : public CAppDialog
{
private:
    CPicture m_logo;
    CPicture m_status_icon;
    CPicture m_chart_image;

public:
    virtual bool Create(const long chart_id, const string name, const int subwin,
                       const int x1, const int y1, const int x2, const int y2);

private:
    bool CreateImages(void);
    void SetStatusIcon(const string status);
};

bool CImageDialog::Create(const long chart_id, const string name, const int subwin,
                         const int x1, const int y1, const int x2, const int y2)
{
    if(!CAppDialog::Create(chart_id, name, subwin, x1, y1, x2, y2))
        return false;

    return CreateImages();
}

bool CImageDialog::CreateImages(void)
{
    // Company logo
    if(!m_logo.Create(m_chart_id, m_name+"Logo", m_subwin, 10, 10, 110, 60))
        return false;
    m_logo.BmpName("\\Images\\company_logo.bmp");
    Add(m_logo);

    // Status icon
    if(!m_status_icon.Create(m_chart_id, m_name+"StatusIcon", m_subwin, 250, 10, 280, 40))
        return false;
    SetStatusIcon("normal");
    Add(m_status_icon);

    // Chart thumbnail
    if(!m_chart_image.Create(m_chart_id, m_name+"ChartImage", m_subwin, 10, 70, 210, 170))
        return false;
    m_chart_image.BmpName("\\Images\\chart_preview.bmp");
    Add(m_chart_image);

    return true;
}

void CImageDialog::SetStatusIcon(const string status)
{
    if(status == "normal")
        m_status_icon.BmpName("\\Images\\icon_green.bmp");
    else if(status == "warning")
        m_status_icon.BmpName("\\Images\\icon_yellow.bmp");
    else if(status == "error")
        m_status_icon.BmpName("\\Images\\icon_red.bmp");
    else
        m_status_icon.BmpName("\\Images\\icon_gray.bmp");
}
```

### Dynamic Image Loader

```mql5
class CImageLoader : public CPicture
{
private:
    string m_image_path;
    string m_fallback_image;
    bool   m_auto_resize;

public:
    CImageLoader();
    bool LoadImage(const string path);
    void SetFallbackImage(const string path);
    void SetAutoResize(const bool auto_resize);
    virtual bool OnCreate(void);

private:
    bool ValidateImageFile(const string path);
    void ResizeToFitImage(void);
};

CImageLoader::CImageLoader(void) : m_image_path(""),
                                   m_fallback_image("\\Images\\no_image.bmp"),
                                   m_auto_resize(true)
{
}

bool CImageLoader::LoadImage(const string path)
{
    if(!ValidateImageFile(path))
    {
        // Load fallback image
        if(StringLen(m_fallback_image) > 0)
        {
            Print("Failed to load image: ", path, ". Using fallback.");
            return BmpName(m_fallback_image);
        }
        return false;
    }

    m_image_path = path;
    bool result = BmpName(path);

    if(result && m_auto_resize)
        ResizeToFitImage();

    return result;
}

void CImageLoader::SetFallbackImage(const string path)
{
    m_fallback_image = path;
}

void CImageLoader::SetAutoResize(const bool auto_resize)
{
    m_auto_resize = auto_resize;
}

bool CImageLoader::OnCreate(void)
{
    // Load default image if specified
    if(StringLen(m_image_path) > 0)
        LoadImage(m_image_path);
    else if(StringLen(m_fallback_image) > 0)
        BmpName(m_fallback_image);

    return CPicture::OnCreate();
}

bool CImageLoader::ValidateImageFile(const string path)
{
    // Check if file exists and is a valid BMP file
    // This is a simplified check - in practice, you might want more validation
    if(StringLen(path) == 0) return false;
    if(StringFind(path, ".bmp") == -1) return false;

    // Additional file existence checks could be added here
    return true;
}

void CImageLoader::ResizeToFitImage(void)
{
    // This would require additional image information
    // For now, use standard sizing
    int width = Right() - Left();
    int height = Bottom() - Top();

    // Maintain aspect ratio if needed
    // Implementation depends on specific requirements
}
```

### Image Gallery Control

```mql5
class CImageGallery : public CWndContainer
{
private:
    struct SImageItem
    {
        CPicture picture;
        string   file_path;
        string   caption;
    };

    SImageItem m_images[20];
    int        m_image_count;
    int        m_selected_index;
    int        m_images_per_row;

public:
    CImageGallery();
    bool CreateGallery(const long chart_id, const string name, const int subwin,
                      const int x, const int y, const int images_per_row = 4);
    bool AddImage(const string file_path, const string caption = "");
    void SetSelected(const int index);
    int  GetSelected(void) const { return m_selected_index; }
    virtual bool OnEvent(const int id, const long &lparam,
                        const double &dparam, const string &sparam);

private:
    void CalculateImagePosition(const int index, int &x, int &y, int &w, int &h);
    void UpdateSelection(void);
};

CImageGallery::CImageGallery(void) : m_image_count(0),
                                     m_selected_index(-1),
                                     m_images_per_row(4)
{
}

bool CImageGallery::CreateGallery(const long chart_id, const string name, const int subwin,
                                 const int x, const int y, const int images_per_row = 4)
{
    m_images_per_row = images_per_row;
    int gallery_width = images_per_row * 80 + (images_per_row - 1) * 5;
    int gallery_height = 200; // Will expand as needed

    if(!Create(chart_id, name, subwin, x, y, x + gallery_width, y + gallery_height))
        return false;

    return true;
}

bool CImageGallery::AddImage(const string file_path, const string caption = "")
{
    if(m_image_count >= 20) return false;

    int img_x, img_y, img_w, img_h;
    CalculateImagePosition(m_image_count, img_x, img_y, img_w, img_h);

    SImageItem &item = m_images[m_image_count];
    item.file_path = file_path;
    item.caption = caption;

    // Create picture control
    string pic_name = Name() + "Img" + IntegerToString(m_image_count);
    if(!item.picture.Create(Chart(), pic_name, Subwin(),
                           img_x, img_y, img_x + img_w, img_y + img_h))
        return false;

    item.picture.BmpName(file_path);
    Add(item.picture);

    m_image_count++;

    // Expand container if needed
    int rows = (m_image_count + m_images_per_row - 1) / m_images_per_row;
    int new_height = rows * (img_h + 5) - 5;
    if(new_height > Height())
        Resize(Width(), new_height);

    return true;
}

void CImageGallery::SetSelected(const int index)
{
    if(index >= 0 && index < m_image_count)
    {
        m_selected_index = index;
        UpdateSelection();
    }
}

bool CImageGallery::OnEvent(const int id, const long &lparam,
                           const double &dparam, const string &sparam)
{
    if(id == CHARTEVENT_OBJECT_CLICK)
    {
        // Check which image was clicked
        for(int i = 0; i < m_image_count; i++)
        {
            if(sparam == m_images[i].picture.Name())
            {
                SetSelected(i);
                Print("Image selected: ", m_images[i].caption, " (", m_images[i].file_path, ")");
                return true;
            }
        }
    }

    return CWndContainer::OnEvent(id, lparam, dparam, sparam);
}

void CImageGallery::CalculateImagePosition(const int index, int &x, int &y, int &w, int &h)
{
    w = 75;
    h = 60;
    int spacing = 5;

    int row = index / m_images_per_row;
    int col = index % m_images_per_row;

    x = col * (w + spacing);
    y = row * (h + spacing);
}

void CImageGallery::UpdateSelection(void)
{
    // Update visual selection indicators
    for(int i = 0; i < m_image_count; i++)
    {
        if(i == m_selected_index)
        {
            // Highlight selected image
            m_images[i].picture.ColorBorder(clrRed);
            // Could also resize or add border effects
        }
        else
        {
            // Normal appearance
            m_images[i].picture.ColorBorder(clrGray);
        }
    }
}
```

## Image Management Patterns

### Lazy Loading
```mql5
class CLazyImageLoader : public CPicture
{
private:
    string m_image_path;
    bool   m_loaded;

public:
    void SetImagePath(const string path) { m_image_path = path; m_loaded = false; }
    virtual bool OnShow(void);
};

bool CLazyImageLoader::OnShow(void)
{
    if(!m_loaded && StringLen(m_image_path) > 0)
    {
        BmpName(m_image_path);
        m_loaded = true;
    }
    return CPicture::OnShow();
}
```

### Image Validation
```mql5
bool ValidateImagePath(const string path)
{
    // Check file extension
    if(StringFind(path, ".bmp") == -1)
    {
        Print("Error: Only BMP files are supported");
        return false;
    }

    // Check path format
    if(StringFind(path, "\\") != 0)
    {
        Print("Error: Path must start with \\");
        return false;
    }

    return true;
}
```

## Best Practices

1. **File Organization**
   - Store images in the `\\Images\\` directory
   - Use descriptive filenames
   - Organize images in subdirectories by category

2. **Image Formats**
   - Use BMP format for compatibility
   - Optimize image size for display requirements
   - Consider file size for performance

3. **Error Handling**
   - Always provide fallback images
   - Validate file paths before loading
   - Handle missing image files gracefully

4. **Performance**
   - Use lazy loading for large image sets
   - Cache frequently used images
   - Optimize image dimensions for display size

5. **User Experience**
   - Provide loading indicators for large images
   - Use consistent image sizing in galleries
   - Implement proper selection feedback

## Common Use Cases

- **Logos and Branding** - Company logos and brand elements
- **Status Icons** - Visual indicators for states and conditions
- **Chart Previews** - Thumbnail images of charts and graphs
- **Image Galleries** - Collections of related images
- **Icon Toolbars** - Tool and action icons
- **Data Visualization** - Custom graphics and charts

## Image Resources

### Standard Icon Locations
```mql5
// Common paths for standard icons
#define ICON_SUCCESS    "\\Images\\icons\\success.bmp"
#define ICON_WARNING    "\\Images\\icons\\warning.bmp"
#define ICON_ERROR      "\\Images\\icons\\error.bmp"
#define ICON_INFO       "\\Images\\icons\\info.bmp"

// Trading-specific icons
#define ICON_BUY        "\\Images\\trading\\buy.bmp"
#define ICON_SELL       "\\Images\\trading\\sell.bmp"
#define ICON_CLOSE      "\\Images\\trading\\close.bmp"
```

## See Also

- [CWndObj](../foundation/CWndObj.md) - Base class for simple controls
- [CPanel](./CPanel.md) - Rectangle panel control
- [CLabel](./CLabel.md) - Text display control
- [Image Management Tutorial](../tutorials/image-management.md)
- [Resource Organization Guide](../tutorials/resource-organization.md)