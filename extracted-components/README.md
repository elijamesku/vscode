# VS Code Extracted Components

This directory contains extracted components from Visual Studio Code, organized for reuse in other projects.

## 📁 Directory Structure

```
extracted-components/
├── ui-bars/              # Top bar, Sidebar, Activity Bar
│   ├── titlebar/         # Window titlebar with menus
│   ├── sidebar/          # Left/right sidebar panel
│   ├── activitybar/      # Icon bar for view switching
│   └── README.md         # Detailed UI bars documentation
│
├── editor/               # Code editor with error checking
│   ├── monaco-editor/    # Complete Monaco Editor core
│   ├── editor/           # Workbench editor integration
│   ├── platform/markers/ # Error/warning marker system
│   ├── contrib/markers/  # Problems panel UI
│   ├── services/editor/  # Editor services
│   └── README.md         # Detailed editor documentation
│
├── shared/               # Shared dependencies
│   ├── base/             # Base utilities (DOM, events, lifecycle)
│   └── platform/         # Platform services (config, themes, etc.)
│
└── README.md             # This file
```

---

## 🎯 What's Included

### 1. **UI Bars** (`ui-bars/`)
The visual chrome of VS Code:
- **Titlebar** - Top window bar with menus and window controls
- **Sidebar** - Collapsible panel for views (File Explorer, Search, etc.)
- **Activity Bar** - Icon bar for quick view switching

**Use this for:** Creating a professional IDE-like interface with navigation panels.

[→ Read UI Bars Documentation](./ui-bars/README.md)

---

### 2. **Editor** (`editor/`)
The complete code editing experience:
- **Monaco Editor** - Full-featured code editor
- **Syntax highlighting** - Color coding for 100+ languages
- **Error checking** - Red/yellow squiggly lines for errors and warnings
- **IntelliSense** - Code completion and suggestions
- **Markers/Diagnostics** - Error tracking and display system
- **Problems Panel** - List view of all errors and warnings

**Use this for:** Adding professional code editing with error checking to your application.

[→ Read Editor Documentation](./editor/README.md)

---

### 3. **Shared** (`shared/`)
Common utilities and services that both UI and editor components depend on:
- Base DOM utilities
- Event system
- Lifecycle management
- Theming system
- Configuration service
- And more...

---

## 🚀 Quick Start

### Option 1: Use Just the Editor

```typescript
import * as monaco from './extracted-components/editor/monaco-editor';

// Create a code editor with error checking
const editor = monaco.editor.create(document.getElementById('container'), {
    value: 'const x = 10;',
    language: 'typescript',
    theme: 'vs-dark'
});

// Add error squiggly lines
monaco.editor.setModelMarkers(editor.getModel(), 'owner', [
    {
        severity: monaco.MarkerSeverity.Error,
        startLineNumber: 1,
        startColumn: 1,
        endLineNumber: 1,
        endColumn: 10,
        message: 'Example error'
    }
]);
```

### Option 2: Use Just the UI Bars

```typescript
import { TitlebarPart } from './extracted-components/ui-bars/titlebar/titlebarPart';
import { SidebarPart } from './extracted-components/ui-bars/sidebar/sidebarPart';
import { ActivityBarPart } from './extracted-components/ui-bars/activitybar/activitybarPart';

// Create titlebar
const titlebar = new TitlebarPart(/* services */);
titlebar.create(container);
titlebar.layout(width, height);

// Similar for sidebar and activity bar
```

### Option 3: Copy to Another Repository

```bash
# Copy just the editor
cp -r extracted-components/editor /path/to/your/repo/
cp -r extracted-components/shared /path/to/your/repo/

# Copy just the UI bars
cp -r extracted-components/ui-bars /path/to/your/repo/
cp -r extracted-components/shared /path/to/your/repo/

# Copy everything
cp -r extracted-components /path/to/your/repo/
```

---

## 🎨 Key Features

### Editor Features
- ✅ Syntax highlighting for 100+ languages
- ✅ Error/warning squiggly lines (like in VS Code)
- ✅ IntelliSense code completion
- ✅ Multi-cursor editing
- ✅ Minimap
- ✅ Code folding
- ✅ Find and replace
- ✅ Bracket matching
- ✅ Format document
- ✅ Problems panel showing all errors

### UI Features
- ✅ Professional titlebar with menus
- ✅ Collapsible sidebar
- ✅ Activity bar with icons
- ✅ Resizable panels
- ✅ Drag and drop support
- ✅ Context menus
- ✅ Keyboard shortcuts
- ✅ Theming support (dark/light modes)

---

## 🔧 How Error Checking Works

The squiggly lines (error indicators) work through this system:

1. **Marker Service** - Stores diagnostic information (errors, warnings)
2. **Model Decorations** - Converts markers to visual decorations
3. **View Rendering** - Renders decorations as colored underlines
4. **Hover Widget** - Shows error messages on hover
5. **Problems Panel** - Lists all errors in a dedicated panel

```
Linter/Language Server
        ↓
   Marker Service
        ↓
 Model Decorations
        ↓
  View Rendering
        ↓
  Squiggly Lines ~~~~
```

---

## 📦 Dependencies

These components depend on VS Code's architecture:

### Required
- **Base utilities** (`shared/base/`) - DOM helpers, events, lifecycle
- **Platform services** (`shared/platform/`) - Configuration, themes, etc.

### For Full Integration
- Dependency injection system
- Service architecture
- Configuration providers
- Theme providers

### Standalone Usage
The Monaco Editor can be used standalone without the full VS Code architecture:
```html
<script src="monaco-editor/min/vs/loader.js"></script>
<script>
    require.config({ paths: { vs: 'monaco-editor/min/vs' } });
    require(['vs/editor/editor.main'], function() {
        monaco.editor.create(document.getElementById('container'), {
            value: 'console.log("Hello");',
            language: 'javascript'
        });
    });
</script>
```

---

## 🎯 Use Cases

### For Building IDEs
- Code playgrounds (like CodePen, JSFiddle)
- Online coding platforms
- Educational coding tools
- Configuration editors
- Database query editors

### For Adding VS Code Features
- Professional navigation bars
- File explorer panels
- Error checking in web apps
- Code documentation tools
- API testing interfaces

---

## 📝 Customization

### Styling
All components support theming via CSS variables:
```css
:root {
    --vscode-editor-background: #1e1e1e;
    --vscode-editor-foreground: #d4d4d4;
    --vscode-titleBar-activeBackground: #3c3c3c;
    --vscode-sideBar-background: #252526;
    --vscode-activityBar-background: #333333;
}
```

### Configuration
Components can be configured through the configuration service:
```typescript
configurationService.updateValue('editor.fontSize', 14);
configurationService.updateValue('workbench.colorTheme', 'vs-dark');
```

---

## 🏗️ Architecture

### Component Pattern
All parts follow this pattern:
1. **Extend base `Part` class**
2. **Implement lifecycle methods** (create, layout, dispose)
3. **Use dependency injection** for services
4. **Emit events** for communication
5. **Support serialization** for state persistence

### Service Architecture
```
IConfigurationService
IThemeService
ILayoutService
IMarkerService
IEditorService
...
```

Services are injected via decorators:
```typescript
constructor(
    @IConfigurationService private config: IConfigurationService,
    @IThemeService private theme: IThemeService
) { }
```

---

## 📚 Additional Resources

- [Monaco Editor Playground](https://microsoft.github.io/monaco-editor/playground.html)
- [VS Code Source Code](https://github.com/microsoft/vscode)
- [Monaco Editor Documentation](https://microsoft.github.io/monaco-editor/)
- [UI Bars README](./ui-bars/README.md) - Detailed UI documentation
- [Editor README](./editor/README.md) - Detailed editor documentation

---

## 📄 License

These components are extracted from VS Code, which is licensed under the MIT License.

---

## 🤝 Contributing

When adding components to another repository:

1. **Preserve the folder structure** for easier updates
2. **Keep the README files** for documentation
3. **Include shared dependencies** that components need
4. **Test thoroughly** in your target environment
5. **Check licenses** for any additional dependencies

---

## 💡 Tips

### Performance
- Monaco Editor handles files up to 1M+ lines efficiently
- Use virtual scrolling for large lists
- Debounce marker updates for better performance

### Integration
- Start with standalone Monaco Editor for simpler integration
- Add workbench parts gradually as needed
- Use VS Code's service architecture for full features

### Debugging
- Enable source maps for TypeScript debugging
- Use browser DevTools for layout debugging
- Check console for service initialization errors

---

## 📞 Support

For questions about:
- **Monaco Editor**: Check Monaco Editor docs
- **VS Code Architecture**: Check VS Code wiki
- **These Extractions**: See component-specific READMEs

---

## 🔄 Updates

To update these extractions from a newer VS Code version:
```bash
# Re-run the extraction from VS Code source
cp -r /path/to/vscode/src/vs/workbench/browser/parts/titlebar extracted-components/ui-bars/
cp -r /path/to/vscode/src/vs/workbench/browser/parts/sidebar extracted-components/ui-bars/
# ... etc
```

---

Happy coding! 🚀

