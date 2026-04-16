# Web PDF Editor - Complete Architecture & Feature Documentation

## Table of Contents
1. [System Architecture](#system-architecture)
2. [Complete Feature List](#complete-feature-list)
3. [Technical Implementation](#technical-implementation)
4. [API Reference](#api-reference)
5. [Design Patterns](#design-patterns)

## System Architecture

### High-Level Overview

```
┌────────────────────────────────────────────────────────────┐
│                    React UI Layer                          │
│  ┌──────────────────────────────────────────────────────┐ │
│  │ ToolBar (15+ editing buttons)                        │ │
│  ├──────────────────────────────────────────────────────┤ │
│  │ Modal Dialogs:                                       │ │
│  │ • TextModal - Add/edit text                         │ │
│  │ • ImageModal - Insert images                        │ │
│  │ • WatermarkModal - Create watermarks                │ │
│  │ • AnnotationModal - Add annotations                 │ │
│  │ • GraphicModal - Draw shapes                        │ │
│  │ • FormModal - Add form fields                       │ │
│  ├──────────────────────────────────────────────────────┤ │
│  │ PDFViewer - Canvas rendering with pdf.js            │ │
│  ├──────────────────────────────────────────────────────┤ │
│  │ Toast Notifications - User feedback                 │ │
│  └──────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘
                           ↓
┌────────────────────────────────────────────────────────────┐
│          PDF Editor Engine (pdfEditor.ts)                  │
├────────────────────────────────────────────────────────────┤
│ Core Classes & Interfaces:                                 │
│ • PDFEditorEngine - Main editor singleton                 │
│ • TextObject - Text element representation                │
│ • ImageObject - Image element representation              │
│ • AnnotationObject - Annotation representation            │
│ • GraphicObject - Shape representation                    │
│ • FormField - Form field representation                   │
│ • WatermarkOptions - Watermark configuration              │
│ • PageObject - Page representation                        │
└────────────────────────────────────────────────────────────┘
                           ↓
┌────────────────────────────────────────────────────────────┐
│              pdf-lib Library (PDF Operations)              │
│ • Document creation and manipulation                      │
│ • Page operations                                         │
│ • Text rendering                                          │
│ • Image embedding                                         │
│ • Content stream generation                               │
│ • PDF serialization                                       │
└────────────────────────────────────────────────────────────┘
```

### Component Interaction Model

```
User Action in UI
    ↓
Modal Dialog Captures Parameters
    ↓
PDFEditorEngine.method(parameters)
    ↓
pdf-lib API Calls
    ↓
Internal Object Registry Updated
    ↓
History State Saved (for undo/redo)
    ↓
PDF Document Regenerated
    ↓
pdf.js Renders Preview
    ↓
UI Updated with Preview
    ↓
Toast Notification Shown
```

## Complete Feature List

### 1. Document Management (7 operations)

#### Load PDF
- Accept PDF file uploads
- Parse and load into internal registry
- Track all pages and existing objects
- Initialize empty history

#### Save PDF
- Serialize current document state to PDF
- Apply all pending modifications
- Maintain PDF structure integrity

#### Export to File
- Generate downloadable PDF
- Trigger browser download
- Default filename with timestamp

#### Get Page Count
- Return total number of pages
- Update UI page indicator

#### Get Page Dimensions
- Return current page width/height
- Used for positioning calculations

#### Create from Images
- Batch insert multiple images
- Create new page per image
- Configurable page size

#### Get/Set Current Page
- Track active page for editing
- Used by all page-specific operations

### 2. Text Operations (4 operations)

#### Add Text
```typescript
interface TextOptions {
  text: string
  x: number
  y: number
  fontSize: number
  color: { r: number; g: number; b: number }
  opacity: number
  fontName?: string
}
```
- Insert text at X, Y coordinates
- Support custom fonts
- Apply RGB color
- Set opacity (0-1 range)
- Cache font data
- Store in object registry
- Return unique object ID

#### Update Text Object
- Modify existing text properties
- Update font, color, opacity
- Reposition on page
- Regenerate content

#### Extract Text from Page
- Read all text from current page
- Return concatenated string
- Maintain reading order

#### Delete Text Object
- Remove text by object ID
- Update internal registry
- Trigger re-render

### 3. Image Operations (4 operations)

#### Add Image
```typescript
interface ImageOptions {
  x: number
  y: number
  width: number
  height: number
  opacity: number
}
```
- Accept image buffer (ArrayBuffer)
- Auto-detect PNG vs JPEG
- Embed in PDF with scaling
- Set opacity
- Track in object registry
- Return object ID

#### Detect Image Format
- Identify PNG by magic bytes (89 50 4E 47)
- Identify JPEG by magic bytes (FF D8 FF)
- Return structured format info

#### Flatten Image Object
- Merge image with background
- Remove transparency channel
- Convert to RGB color space

#### Delete Image Object
- Remove by object ID
- Clean up registry

### 4. Annotation Operations (4 types)

#### Type: Highlight
- Yellow highlight box
- Covers text area
- No rotation or complex styling

#### Type: Note/Comment
- Text annotation box
- Custom position and size
- Stores text content

#### Type: Underline
- Line under text
- Specified position and width

#### Type: Strikethrough
- Line through text
- Specified position and width

#### Operations
```typescript
addAnnotation(options: AnnotationObject): Promise<string>
```
- Create annotation with type, text, position, color
- Store in object registry
- Return unique ID

### 5. Graphics & Shapes (3 types)

#### Rectangle
- Filled or bordered rectangle
- Custom border width
- Border and fill colors
- Opacity support
- Position and size control

#### Circle
- Filled or bordered circle
- Custom border width
- Border and fill colors
- Opacity support

#### Line
- Straight line
- Configurable width
- Start and end coordinates

### 6. Watermarking (2 types + configuration)

#### Text Watermark
- Custom text on all pages
- Font size (typically 48pt+)
- Semi-transparent (opacity 0.3 typical)
- Position options:
  - Center
  - Top-left corner
  - Top-right corner
  - Bottom-left corner
  - Bottom-right corner
  - Custom X, Y

#### Image Watermark
- PNG/JPEG image on all pages
- Scale control
- Opacity control
- Same position options as text

#### Configuration
- Apply to all pages automatically
- Optional background behind watermark
- Rotation support
- Metadata embedding

### 7. Page Management (5 operations)

#### Add Page
```typescript
addPage(insertAtIndex?: number): Promise<void>
```
- Create blank page
- Insert at specific index or end
- Default size: 612x792 (US Letter)
- Initialize empty object registry

#### Delete Page
```typescript
deletePage(pageIndex: number): Promise<void>
```
- Remove page by index
- Update all page references
- Adjust page count

#### Move Page
```typescript
movePage(fromIndex: number, toIndex: number): Promise<void>
```
- Reorder pages
- Update internal references
- Maintain object associations

#### Duplicate Page
```typescript
duplicatePage(pageIndex: number): Promise<void>
```
- Create exact copy
- Preserve all objects
- Insert after source page

#### Rotate Page
```typescript
rotatePage(pageIndex: number, degrees: number): Promise<void>
```
- Rotate 90, 180, 270 degrees
- Update page dimensions
- Reposition all objects accordingly

### 8. Form Fields (4 types)

#### Text Field
```typescript
interface FormField {
  type: 'text'
  name: string
  x: number
  y: number
  width: number
  height: number
  fontSize: number
  defaultValue?: string
}
```
- Text input field
- Configurable size
- Optional default value

#### Checkbox
```typescript
interface FormField {
  type: 'checkbox'
  name: string
  x: number
  y: number
  width: number
  height: number
  checked?: boolean
}
```
- Boolean checkbox
- Named field
- Optional default state

#### Radio Button
```typescript
interface FormField {
  type: 'radio'
  name: string
  value: string
  x: number
  y: number
  width: number
  height: number
  checked?: boolean
}
```
- Single radio button
- Part of radio group (same name)
- Text value for selection

#### Dropdown Select
```typescript
interface FormField {
  type: 'select'
  name: string
  options: Array<{ label: string; value: string }>
  x: number
  y: number
  width: number
  height: number
  defaultValue?: string
}
```
- Dropdown selection
- Multiple options
- Optional default selection

### 9. Object Management (5 operations)

#### Get Page Objects
```typescript
getPageObjects(pageIndex: number): Array<PageObject>
```
- Return all objects on page
- Includes type, position, properties
- Used for display and updates

#### Get Object by ID
```typescript
getObjectById(objectId: string): PageObject | null
```
- Lookup specific object
- Return full object data

#### Update Object Matrix
```typescript
updateObjectMatrix(objectId: string, transform: Transform): Promise<void>
```
- Apply transformation matrix
- Position adjustments
- Scale changes
- Rotation angles

#### Delete Object
```typescript
deleteObject(objectId: string): Promise<void>
```
- Remove by object ID
- Remove from registry
- Trigger render update

#### Get Object Type
```typescript
getObjectType(objectId: string): string | null
```
- Return object type string
- Types: 'text', 'image', 'annotation', 'graphic', 'form'

### 10. History Management (4 operations)

#### Undo
```typescript
undo(): Promise<void>
```
- Revert to previous state
- Load previous PDF from history
- Update UI
- History moves back one position

#### Redo
```typescript
redo(): Promise<void>
```
- Restore undone changes
- Load next PDF from history
- Update UI
- History moves forward one position

#### Can Undo
```typescript
canUndo(): boolean
```
- Return true if history has previous state
- Used to disable undo button

#### Can Redo
```typescript
canRedo(): boolean
```
- Return true if history has next state
- Used to disable redo button

#### Save History State
- Automatic after every modification
- Store PDF buffer in history array
- Maintain current index
- Limit history to 50 states

### 11. Color Management (2 operations)

#### RGB Color Validation
```typescript
interface Color {
  r: number  // 0-255
  g: number  // 0-255
  b: number  // 0-255
}
```
- Validate RGB values range
- Convert to proper format for pdf-lib
- Support opacity/alpha

#### Parse Color
- Accept hex color strings (#RRGGBB)
- Accept RGB tuples (r, g, b)
- Convert to internal format

## Technical Implementation

### Technology Choices

#### React 18
- Component-based UI
- Hooks for state management
- Functional components
- Performance optimization with React.memo

#### TypeScript
- Full type safety
- Interfaces for all data structures
- Better IDE support
- Compile-time error detection

#### Vite
- Ultra-fast build (< 1s)
- Instant HMR (hot module reload)
- ES module based
- Optimized production build

#### pdf-lib
- Pure JavaScript PDF manipulation
- No native dependencies
- Fast performance
- Complete PDF 1.7 support

#### pdf.js
- Mozilla's battle-tested PDF renderer
- Canvas-based rendering
- Excellent text extraction
- Mobile support

#### Tailwind CSS
- Utility-first styling
- Responsive design
- Dark mode support
- Fast development

### Data Structures

#### TextObject
```typescript
interface TextObject {
  id: string
  type: 'text'
  text: string
  x: number
  y: number
  fontSize: number
  color: { r: number; g: number; b: number }
  opacity: number
  fontName: string
  width: number
  height: number
}
```

#### ImageObject
```typescript
interface ImageObject {
  id: string
  type: 'image'
  x: number
  y: number
  width: number
  height: number
  opacity: number
  format: 'png' | 'jpeg'
  data: ArrayBuffer
}
```

#### AnnotationObject
```typescript
interface AnnotationObject {
  id: string
  type: 'highlight' | 'note' | 'underline' | 'strikethrough'
  x: number
  y: number
  width: number
  height: number
  text?: string
  color: { r: number; g: number; b: number }
  opacity: number
}
```

#### GraphicObject
```typescript
interface GraphicObject {
  id: string
  type: 'rectangle' | 'circle' | 'line'
  x: number
  y: number
  width: number
  height: number
  borderWidth: number
  borderColor: { r: number; g: number; b: number }
  fillColor: { r: number; g: number; b: number }
  opacity: number
}
```

#### PageObject
```typescript
interface PageObject {
  index: number
  width: number
  height: number
  rotation: number  // 0, 90, 180, 270
  objects: Array<TextObject | ImageObject | AnnotationObject | GraphicObject | FormField>
}
```

### Performance Optimizations

1. **Font Caching**
   - Cache loaded fonts in memory
   - Reuse across pages and documents
   - Key: fontName
   - Value: font data

2. **Object Registry**
   - O(1) lookup by object ID
   - Efficient updates without full document re-parse
   - Memory-efficient sparse representation

3. **Lazy Rendering**
   - Only render visible pages
   - pdf.js handles off-screen optimization
   - Virtualization for large documents

4. **History Optimization**
   - Store only PDF buffers
   - Limit history size to 50 states
   - Compress on demand

5. **Image Format Detection**
   - Quick magic byte check
   - Avoid full image parse
   - O(1) format detection

## API Reference

### PDFEditorEngine Class

Primary class for all PDF operations.

#### Constructor
```typescript
constructor()
```
- Initialize empty document
- Setup empty history
- Initialize object registry

#### Document Methods

##### loadPDF
```typescript
async loadPDF(buffer: ArrayBuffer): Promise<void>
```
- Load PDF from ArrayBuffer
- Parse all pages and objects
- Initialize registry

##### loadPDFFromFile
```typescript
async loadPDFFromFile(file: File): Promise<void>
```
- Load PDF from File input
- Convert File to ArrayBuffer
- Call loadPDF

##### savePDF
```typescript
async savePDF(): Promise<ArrayBuffer>
```
- Serialize current state to PDF
- Return ArrayBuffer of PDF bytes
- Ready for download or storage

##### exportToFile
```typescript
async exportToFile(filename: string): Promise<void>
```
- Trigger browser download
- Use filename parameter
- Call savePDF internally

##### getPageCount
```typescript
getPageCount(): number
```
- Return total pages
- Access pdfDoc.getPageCount()

##### getPageDimensions
```typescript
getPageDimensions(pageIndex?: number): 
  { width: number; height: number } | null
```
- Return width/height of page
- Use current page if not specified
- Return null if no current page

#### Text Methods

##### addText
```typescript
async addText(options: TextOptions): Promise<string>
```
- Insert text at position
- Apply style and color
- Return unique object ID
- Store in registry

##### updateTextObject
```typescript
async updateTextObject(
  objectId: string, 
  updates: Partial<TextOptions>
): Promise<void>
```
- Modify existing text object
- Partial updates (only changed props)
- Regenerate page content

##### extractTextFromPage
```typescript
async extractTextFromPage(pageIndex?: number): Promise<string>
```
- Extract all text from page
- Concatenate with newlines
- Return text string

##### deleteTextObject
```typescript
async deleteTextObject(objectId: string): Promise<void>
```
- Remove text by ID
- Update registry
- Trigger render

#### Image Methods

##### addImage
```typescript
async addImage(
  buffer: ArrayBuffer, 
  options: ImageOptions
): Promise<string>
```
- Embed image in PDF
- Auto-detect PNG/JPEG
- Apply positioning and opacity
- Return object ID

##### flattenImageObject
```typescript
async flattenImageObject(objectId: string): Promise<void>
```
- Merge image with background
- Remove transparency
- Convert to RGB

##### deleteImageObject
```typescript
async deleteImageObject(objectId: string): Promise<void>
```
- Remove image by ID
- Clean up buffer

#### Annotation Methods

##### addAnnotation
```typescript
async addAnnotation(
  options: AnnotationObject
): Promise<string>
```
- Create annotation (4 types)
- Store text and styling
- Return object ID

##### getAnnotations
```typescript
getAnnotations(pageIndex?: number): AnnotationObject[]
```
- Get all annotations on page
- Return array of annotation objects

##### deleteAnnotation
```typescript
async deleteAnnotation(objectId: string): Promise<void>
```
- Remove annotation by ID
- Update registry

#### Graphics Methods

##### addGraphic
```typescript
async addGraphic(
  options: GraphicObject
): Promise<string>
```
- Add shape (rectangle, circle, line)
- Apply styling
- Return object ID

##### deleteGraphic
```typescript
async deleteGraphic(objectId: string): Promise<void>
```
- Remove shape by ID

#### Watermark Methods

##### addWatermark
```typescript
async addWatermark(
  options: WatermarkOptions
): Promise<void>
```
- Add text or image watermark
- Apply to all pages
- Handle positioning and opacity

##### removeWatermark
```typescript
async removeWatermark(): Promise<void>
```
- Remove all watermarks
- Clean up watermark objects

#### Page Methods

##### addPage
```typescript
async addPage(insertAtIndex?: number): Promise<void>
```
- Create new blank page
- Insert at index or end
- Initialize object registry

##### deletePage
```typescript
async deletePage(pageIndex: number): Promise<void>
```
- Remove page by index
- Update page count

##### movePage
```typescript
async movePage(fromIndex: number, toIndex: number): Promise<void>
```
- Reorder pages
- Update all references

##### rotatePage
```typescript
async rotatePage(pageIndex: number, degrees: number): Promise<void>
```
- Rotate page 90/180/270
- Update page dimensions

##### setCurrentPage
```typescript
setCurrentPage(index: number): void
```
- Set active page for operations
- Used by page-specific methods

##### getCurrentPageIndex
```typescript
getCurrentPageIndex(): number
```
- Return current page index

#### Form Methods

##### addFormField
```typescript
async addFormField(
  field: FormField
): Promise<string>
```
- Add form field to page
- Support 4 field types
- Return field ID

#### History Methods

##### undo
```typescript
async undo(): Promise<void>
```
- Revert to previous state
- If canUndo() is true

##### redo
```typescript
async redo(): Promise<void>
```
- Restore next state
- If canRedo() is true

##### canUndo
```typescript
canUndo(): boolean
```
- Return true if previous state exists

##### canRedo
```typescript
canRedo(): boolean
```
- Return true if next state exists

#### Object Methods

##### getPageObjects
```typescript
getPageObjects(pageIndex?: number): PageObject[]
```
- Get all objects on page
- Includes all types

##### getObjectById
```typescript
getObjectById(objectId: string): PageObject | null
```
- Lookup specific object
- Return full object data

##### updateObjectMatrix
```typescript
async updateObjectMatrix(
  objectId: string, 
  transform: Transform
): Promise<void>
```
- Apply transformation
- Position, scale, rotation

##### deleteObject
```typescript
async deleteObject(objectId: string): Promise<void>
```
- Universal delete by ID
- Handle all object types

## Design Patterns

### 1. Singleton Pattern
- PDFEditorEngine is instantiated once
- Global state management
- Single source of truth

### 2. Registry Pattern
- Object registry for tracking
- O(1) lookups by ID
- Efficient updates

### 3. Command Pattern
- Each operation is a method call
- Undo/redo stores commands
- History preserves state

### 4. Observer Pattern
- React hooks observe editor state
- Automatic re-rendering
- Reactive UI updates

### 5. Factory Pattern
- Object creation methods (addText, addImage, etc.)
- Encapsulated creation logic
- Consistent initialization

### 6. Strategy Pattern
- Different operations have different handlers
- Pluggable functionality
- Easy to extend

### 7. Repository Pattern
- Object registry acts as repository
- Abstraction over storage
- Consistent access patterns

## Build & Deployment

### Development
```bash
npm run dev
```
- Start Vite dev server
- HMR for instant updates
- Port 5173

### Production
```bash
npm run build
```
- Optimize and minify
- Create dist/ folder
- Ready for deployment

### Deployment Target
- Static web server (nginx, Apache)
- S3 bucket with CloudFront
- Vercel, Netlify, GitHub Pages
- Any static hosting

## Future Enhancements

1. **OCR Integration**
   - Tesseract.js for text recognition
   - Extract text from scanned PDFs

2. **Cloud Storage**
   - AWS S3 integration
   - Google Drive support
   - Auto-save functionality

3. **Collaborative Editing**
   - WebSocket for live collaboration
   - Real-time cursor tracking
   - Conflict resolution

4. **PDF Security**
   - Password protection
   - Digital signatures
   - Encryption support

5. **Advanced Features**
   - PDF compression
   - Bookmarks/outline editing
   - Metadata editing
   - Link support

6. **Mobile App**
   - React Native version
   - iOS/Android apps
   - Touch-optimized UI

7. **Desktop App**
   - Electron wrapper
   - Native file system access
   - System integration

---

**Complete documentation of Web PDF Editor - Feature parity with RevPDF Desktop Editor**
