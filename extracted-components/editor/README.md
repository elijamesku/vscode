# Editor Components - Extracted from VS Code

This folder contains the **Code Editor** with **error checking**, **squiggly lines**, **syntax highlighting**, and all editing functionality from Visual Studio Code.

## Components Included

### 1. **Monaco Editor Core** (`monaco-editor/`)
The complete Monaco Editor - the code editor that powers VS Code.

**Key directories:**
- `browser/` - Browser-specific editor implementation
  - `widget/` - Editor widget and decorations
  - `controller/` - Mouse and keyboard handlers
  - `view/` - Rendering engine
  - `services/` - Editor services
- `common/` - Core editor logic
  - `model/` - Text model and editing operations
  - `languages/` - Language features
  - `services/` - Language services
  - `viewModel/` - View model and line rendering
  - `config/` - Editor configuration
- `contrib/` - Editor contributions (features)
  - `quickSuggestions/` - IntelliSense suggestions
  - `gotoError/` - Navigate between errors
  - `codelens/` - Code lens support
  - `inlayHints/` - Inline hints
  - `hover/` - Hover information
  - `snippet/` - Snippet support
  - And many more...

**Key features:**
- Syntax highlighting
- Error/warning squiggly lines (decorations)
- Code completion (IntelliSense)
- Go to definition
- Find all references
- Formatting
- Multiple cursors
- Minimap
- Line numbers
- Bracket matching
- Code folding

---

### 2. **Editor Part** (`editor/`)
The workbench integration of the Monaco Editor.

**Key files:**
- `editorPart.ts` - Main editor part (manages editor groups)
- `editorGroupView.ts` - Editor group view
- `textCodeEditor.ts` - Text code editor wrapper
- `textDiffEditor.ts` - Diff editor
- `editorAutoSave.ts` - Auto-save functionality
- `editorStatus.ts` - Status bar items
- `breadcrumbs.ts` - File breadcrumbs

**Features:**
- Split editor support (multiple editor groups)
- Tab management
- Editor state persistence
- Breadcrumbs navigation
- Status bar integration
- Diff view

---

### 3. **Markers/Diagnostics** (`platform/markers/` and `contrib/markers/`)
The system that shows errors, warnings, and info messages.

**Platform Markers** (`platform/markers/`):
- `markerService.ts` - Core marker service
- `markers.ts` - Marker interfaces and types

**Markers Contribution** (`contrib/markers/`):
- `markersView.ts` - Problems panel UI
- `markersModel.ts` - Marker data model
- `markersTreeViewer.ts` - Tree view for problems
- `media/markers.css` - Problems panel styling

**How squiggly lines work:**
1. **Language Server** sends diagnostics
2. **Marker Service** stores the diagnostics
3. **Editor** subscribes to marker changes
4. **Decorations** are created for each marker
5. **View Layer** renders the squiggly lines

**Marker types:**
- Error (red squiggly)
- Warning (yellow squiggly)
- Info (blue squiggly)
- Hint (dotted underline)

---

### 4. **Editor Services** (`services/editor/`)
Services that manage editor state and behavior.

**Key files:**
- `editorService.ts` - Main editor service
- `editorGroupsService.ts` - Editor group management
- Editor input/output management
- Editor history
- Editor reuse

---

## How Error Checking & Squiggly Lines Work

### Architecture Flow:

```
Language Server / Linter
        ↓
    Diagnostics
        ↓
  Marker Service
        ↓
 Editor Model Decorations
        ↓
   View Decorations
        ↓
  Rendered Squiggly Lines
```

### Detailed Process:

1. **Language Analysis**
   - TypeScript/JavaScript analyzer runs
   - ESLint/other linters run
   - Language servers provide diagnostics

2. **Marker Creation**
   ```typescript
   markerService.changeOne(resource, markers);
   ```
   Creates markers with:
   - Severity (Error, Warning, Info, Hint)
   - Range (start/end line and column)
   - Message
   - Source (e.g., "typescript", "eslint")
   - Code (error code)

3. **Model Decorations**
   ```typescript
   model.deltaDecorations(oldDecorations, newDecorations);
   ```
   Converts markers to decorations:
   ```typescript
   {
       range: marker.range,
       options: {
           className: 'squiggly-error',
           hoverMessage: marker.message,
           ...
       }
   }
   ```

4. **Rendering**
   - View layer renders decorations
   - CSS classes applied:
     - `.squiggly-error` - Red wavy underline
     - `.squiggly-warning` - Yellow wavy underline
     - `.squiggly-info` - Blue wavy underline

5. **Hover Support**
   - Mouse hover over squiggly
   - Hover widget shows error message
   - Can show multiple diagnostics

6. **Quick Fixes**
   - Light bulb appears next to errors
   - Code actions available
   - Can auto-fix issues

---

## CSS for Squiggly Lines

The squiggly underlines are rendered using CSS:

```css
/* Error squiggly - red wavy underline */
.squiggly-error {
    background: url("data:image/svg+xml,...") repeat-x bottom left;
}

/* Warning squiggly - yellow/orange wavy underline */
.squiggly-warning {
    background: url("data:image/svg+xml,...") repeat-x bottom left;
}

/* Info squiggly - blue wavy underline */
.squiggly-info {
    background: url("data:image/svg+xml,...") repeat-x bottom left;
}

/* Hint - dotted underline */
.squiggly-hint {
    border-bottom: 1px dotted;
}
```

---

## Key Classes and APIs

### IMarkerService
```typescript
interface IMarkerService {
    // Add/update markers
    changeOne(owner: string, resource: URI, markers: IMarker[]): void;

    // Get markers
    read(filter?: { resource?: URI }): IMarker[];

    // Listen to changes
    onMarkerChanged: Event<URI[]>;
}
```

### IMarker
```typescript
interface IMarker {
    owner: string;           // e.g., "typescript"
    resource: URI;           // File URI
    severity: MarkerSeverity; // Error/Warning/Info/Hint
    message: string;         // Error message
    startLineNumber: number;
    startColumn: number;
    endLineNumber: number;
    endColumn: number;
    code?: string;           // Error code
    relatedInformation?: IRelatedInformation[];
}
```

### IModelDecoration
```typescript
interface IModelDecoration {
    range: IRange;
    options: IModelDecorationOptions;
}

interface IModelDecorationOptions {
    className?: string;      // CSS class
    hoverMessage?: IMarkdownString;
    glyphMarginClassName?: string;
    minimap?: {
        color: string;
        position: MinimapPosition;
    };
}
```

---

## How to Use in Another Project

### Basic Editor Setup:

```typescript
import * as monaco from 'monaco-editor';

// 1. Create editor
const editor = monaco.editor.create(container, {
    value: 'function hello() {\n\tconsole.log("Hello");\n}',
    language: 'javascript',
    theme: 'vs-dark',
    minimap: { enabled: true },
    automaticLayout: true
});

// 2. Get model
const model = editor.getModel();

// 3. Add decorations (squiggly lines)
model.deltaDecorations([], [{
    range: new monaco.Range(1, 1, 1, 10),
    options: {
        isWholeLine: false,
        className: 'squiggly-error',
        hoverMessage: { value: 'Syntax error' }
    }
}]);
```

### Adding Markers:

```typescript
// Set markers for the model
monaco.editor.setModelMarkers(model, 'eslint', [
    {
        severity: monaco.MarkerSeverity.Error,
        startLineNumber: 1,
        startColumn: 1,
        endLineNumber: 1,
        endColumn: 10,
        message: 'Variable is not defined'
    },
    {
        severity: monaco.MarkerSeverity.Warning,
        startLineNumber: 2,
        startColumn: 5,
        endLineNumber: 2,
        endColumn: 15,
        message: 'Unused variable'
    }
]);
```

### Language Support:

```typescript
// Register a language
monaco.languages.register({ id: 'myLanguage' });

// Register completion provider
monaco.languages.registerCompletionItemProvider('myLanguage', {
    provideCompletionItems: (model, position) => {
        return {
            suggestions: [
                {
                    label: 'myFunction',
                    kind: monaco.languages.CompletionItemKind.Function,
                    insertText: 'myFunction()',
                }
            ]
        };
    }
});

// Register hover provider
monaco.languages.registerHoverProvider('myLanguage', {
    provideHover: (model, position) => {
        return {
            contents: [{ value: 'Hover information' }]
        };
    }
});
```

---

## Key Features Documentation

### 1. Syntax Highlighting
- Tokenization using Monarch (declarative)
- TextMate grammars (regex-based)
- Semantic highlighting (language server)

### 2. IntelliSense
- Completion providers
- Signature help
- Parameter hints
- Quick info

### 3. Navigation
- Go to definition
- Find references
- Go to symbol
- Breadcrumbs

### 4. Editing
- Multi-cursor editing
- Bracket matching
- Auto-closing pairs
- Format on type
- Format document

### 5. Error Display
- Inline squiggly lines
- Gutter markers
- Problems panel
- Minimap decorations

---

## Dependencies

The editor requires:
- **Base utilities** - Event system, lifecycle, DOM helpers
- **Platform services** - Configuration, themes, keybindings
- **Language services** - TypeScript, JSON, HTML, CSS parsers

---

## File Structure

```
editor/
├── monaco-editor/          # Complete Monaco Editor
│   ├── browser/           # Browser implementation
│   ├── common/            # Core logic
│   └── contrib/           # Features
├── editor/                # Workbench editor part
│   ├── editorPart.ts
│   ├── editorGroupView.ts
│   └── media/
├── platform/
│   └── markers/           # Marker service
│       ├── markerService.ts
│       └── markers.ts
├── contrib/
│   └── markers/           # Problems panel
│       ├── markersView.ts
│       └── media/
├── services/
│   └── editor/            # Editor services
└── README.md (this file)
```

---

## Performance Considerations

- **Large files**: Virtual scrolling for 1M+ lines
- **Markers**: Debounced updates to avoid thrashing
- **Decorations**: Batched updates with delta operations
- **Syntax highlighting**: On-demand tokenization
- **View rendering**: Incremental rendering of visible lines

---

## Theming

Editor supports full theming:
```typescript
monaco.editor.defineTheme('myTheme', {
    base: 'vs-dark',
    inherit: true,
    rules: [
        { token: 'comment', foreground: '6A9955' },
        { token: 'keyword', foreground: '569CD6' },
    ],
    colors: {
        'editor.background': '#1E1E1E',
        'editor.foreground': '#D4D4D4',
        'editorError.foreground': '#F48771',
        'editorWarning.foreground': '#CCA700',
    }
});
```

