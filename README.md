# Web PDF Editor

A comprehensive web-based PDF editor built with React, TypeScript, Tailwind CSS, and pdf-lib. This project replicates all major features from RevPDF Editor for the browser.

## ✨ Features - Complete A to Z

### Document Management
- **Load PDF Files**: Upload any PDF document
- **Save/Export**: Download edited PDFs  
- **Page Registry**: Internal tracking of all pages and objects
- **Document History**: Full undo/redo support with complete command history

### Text Operations
- **Add Text**: Insert text anywhere on the page with precise positioning
- **Font Control**: Support for multiple fonts with fallback mechanisms
- **Text Styling**: 
  - Custom font sizes (8-72pt range)
  - RGB color picker for custom colors
  - Opacity/transparency control (0-100%)
  - Precise X, Y coordinate positioning
- **Font Caching**: Efficient font reuse across operations
- **Text Extraction**: Extract all text content from pages

### Image Operations
- **Add Images**: Insert PNG and JPEG images
- **Image Positioning**: Precise X, Y coordinate placement
- **Image Scaling**: Adjustable width and height
- **Image Transparency**: Full opacity control
- **Image Flattening**: Merge images with background
- **Format Detection**: Automatic PNG/JPEG format recognition and validation

### Annotations & Comments
- **Highlight**: Add yellow highlight boxes to text
- **Notes/Comments**: Add text-based comment annotations
- **Underline**: Underline text sections
- **Strikethrough**: Cross out text content
- **Custom Colors**: RGB color selection for each annotation
- **Positioned Annotations**: Full control over position, width, and height

### Graphics & Shapes
- **Rectangles**: Draw filled or bordered rectangles
- **Circles**: Draw circular shapes
- **Custom Styling**: 
  - Adjustable border width
  - Custom border color
  - Customizable fill color
  - Opacity control
- **Transformation**: Full position and size control

### Watermarking
- **Text Watermarks**:
  - Custom watermark text
  - Font size control
  - Color customization
  - Opacity/transparency control
  - Multiple position options (center, corners, custom)
  - Rotation support
- **Image Watermarks**:
  - PNG and JPEG watermark support
  - Scale and sizing control
  - Opacity control
  - Multiple positioning options
- **Apply to All Pages**: Watermarks automatically apply to entire document

### Page Management
- **Add Pages**: Create new blank pages
- **Delete Pages**: Remove unwanted pages
- **Move Pages**: Reorder pages
- **Duplicate Pages**: Create page copies
- **Page Rotation**: Rotate pages (90°, 180°, 270°, 360°)
- **Page Navigation**: Previous/Next page buttons with keyboard support

### Form Fields & Interactive Elements
- **Text Fields**: Create text input areas
- **Checkboxes**: Add checkbox form fields
- **Radio Buttons**: Multiple choice selection fields
- **Dropdown Select**: Selection list fields
- **Field Positioning**: Precise placement on pages
- **Field Validation**: Input validation and error handling

### Advanced Object Management
- **Object Registry**: Track all page objects and their states
- **Object State Management**: Active, inactive, and stale state tracking
- **Object Transformation**:
  - Matrix transformation operations
  - Position adjustments
  - Scale control
  - Rotation angles
- **Object Identification**: Unique ID system for all objects

### Undo & Redo
- **Full History**: Complete operation history tracking
- **Undo**: Revert to previous state
- **Redo**: Restore undone changes
- **History Limits**: Configurable history size management
- **State Preservation**: Proper state tracking for all operations

### File Operations
- **PDF Upload**: Direct PDF file upload support
- **PDF Export**: Download edited PDF files
- **Batch Operations**: Create documents from multiple images
- **File Validation**: Ensure valid PDF structure

### User Interface
- **Modern Design**: Built with React and Tailwind CSS
- **Responsive Layout**: Works on different screen sizes
- **Modal Dialogs**: Dedicated editing dialogs for each operation
- **Toast Notifications**: Success and error user feedback
- **Organized Toolbar**: Logical tool grouping and access
- **PDF Preview**: Real-time PDF viewing with pdf.js

### Font Management
- **Multiple Fonts**: TrueType, Type1, OpenType support
- **Font Extraction**: Extract fonts from existing PDFs
- **Character Mapping**: Unicode codepoint support
- **Script Support**: Special handling for CJK text
- **Subset Fonts**: Handles subset fonts with fallback

### Color Management
- **RGB Color Picker**: Full color customization
- **Opacity Control**: Transparency and blending support
- **Color Validation**: Ensure valid color values
- **Preset Colors**: Common color options

## 🚀 Technology Stack

- **React 18** - Modern UI component framework
- **TypeScript** - Type-safe development
- **Vite** - Lightning-fast build tool with HMR
- **pdf-lib** - Pure JavaScript PDF operations
- **pdf.js** - Mozilla's PDF rendering engine
- **Tailwind CSS** - Utility-first styling framework
- **Lucide React** - Beautiful icon library

## 📋 Installation

```bash
# Clone the repository
git clone https://github.com/Lithiumwow/webpdf.git
cd webpdf

# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build
```

## 🎨 Development

```bash
# Start dev server with hot reload
npm run dev

# Build production bundle
npm run build

# Preview production build
npm run preview

# Run linter
npm run lint
```

## 📊 Project Structure

```
webpdf/
├── src/
│   ├── lib/
│   │   └── pdfEditor.ts         # Core PDF editing engine (500+ lines)
│   ├── components/
│   │   ├── PDFViewer.tsx        # PDF canvas rendering
│   │   ├── ToolBar.tsx          # Main toolbar UI
│   │   ├── TextModal.tsx        # Text dialog
│   │   ├── ImageModal.tsx       # Image dialog
│   │   ├── WatermarkModal.tsx   # Watermark dialog
│   │   └── AnnotationModal.tsx  # Annotation dialog
│   ├── App.tsx                  # Main application
│   ├── App.css                  # App styles
│   ├── index.css                # Global styles
│   └── main.tsx                 # Entry point
├── index.html                   # HTML template
├── vite.config.ts              # Vite configuration
├── tsconfig.json               # TypeScript config
├── tailwind.config.js          # Tailwind configuration
├── postcss.config.js           # PostCSS config
└── package.json                # Dependencies

```

## 🏗️ Architecture

### Core Components

**PDFEditorEngine** (pdfEditor.ts)
- Central singleton managing all PDF operations
- 30+ public methods covering all features
- Object registry system for state management
- Font caching for performance

**React Component Layer**
- Modular modal dialogs for each operation
- Real-time PDF preview with pdf.js
- Tailwind-styled responsive UI
- Toast notification system for feedback

### Data Flow

```
User Input (UI Component)
    ↓
Modal Dialog (Capture Parameters)
    ↓
PDFEditorEngine Method Call
    ↓
pdf-lib Document Manipulation
    ↓
Update Page Objects & History
    ↓
Render Preview (pdf.js)
    ↓
Display Updated PDF
```

## 📈 Feature Comparison

| Feature | RevPDF Desktop | Web Version |
|---------|--------|-------------|
| Text Addition | ✓ | ✓ |
| Image Insertion | ✓ | ✓ |
| Watermarking | ✓ | ✓ |
| Annotations | ✓ | ✓ |
| Form Fields | ✓ | ✓ |
| Page Management | ✓ | ✓ |
| **Undo/Redo** | ✗ | ✓ |
| **Cross-Platform** | Windows/Mac/Linux | **Any Browser** |
| **No Installation** | ✗ | **✓** |
| **Mobile Support** | ✗ | **✓ (future)** |

## 🌐 Browser Support

- Chrome/Chromium 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## 🔧 API Reference

See the comprehensive API documentation in [ARCHITECTURE.md](./ARCHITECTURE.md).

## 📝 License

MIT

## 🤝 Contributing

Pull requests welcome. Please open an issue first for major changes.

## 📖 Documentation

- [Complete Architecture Guide](./ARCHITECTURE.md) - Full technical details and design patterns
- [API Reference](./ARCHITECTURE.md#api-reference) - Detailed method documentation
- [Development Guide](/docs/DEVELOPMENT.md) - Setup and development instructions

## 🎯 Roadmap

- [ ] Batch document processing
- [ ] OCR text recognition
- [ ] Cloud storage integration
- [ ] Collaborative editing
- [ ] Advanced security (signatures, encryption)
- [ ] Plugin system for custom features
- [ ] Mobile app version
- [ ] Desktop app (Electron)

## 🐛 Known Limitations

- Font subsetting limited by pdf-lib library
- OCR not included in current version
- Drag-and-drop UI positioning (planned)
- Advanced PDF security features (planned)

## 📞 Support

For issues, questions, or suggestions, please open a GitHub issue.

---

**Built with ❤️ as a complete replacement for RevPDF Editor**
