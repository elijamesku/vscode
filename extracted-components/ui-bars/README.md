# UI Bars Components - Extracted from VS Code

This folder contains the **Titlebar**, **Sidebar**, and **Activity Bar** components from Visual Studio Code.

## Components Included

### 1. **Titlebar** (`titlebar/`)
The top bar of VS Code that displays:
- Window title
- Menu bar (File, Edit, View, etc.)
- Command center (search box)
- Window controls (minimize, maximize, close)

**Key files:**
- `titlebarPart.ts` - Main titlebar implementation
- `menubarControl.ts` - Menu bar functionality
- `commandCenterControl.ts` - Command center/search box
- `windowTitle.ts` - Window title management
- `media/titlebarpart.css` - Styling

**Features:**
- Custom titlebar rendering
- Menu bar with dropdown menus
- Window control buttons
- Native vs custom titlebar support
- Drag region for window movement

---

### 2. **Sidebar** (`sidebar/`)
The left/right panel that contains views like:
- File Explorer
- Search
- Source Control
- Extensions
- Other custom views

**Key files:**
- `sidebarPart.ts` - Main sidebar implementation
- `sidebarActions.ts` - Sidebar toggle actions
- `media/sidebarpart.css` - Styling

**Features:**
- Collapsible sidebar
- Can be positioned left or right
- Hosts multiple view containers
- Resizable width
- View switching and management

---

### 3. **Activity Bar** (`activitybar/`)
The narrow icon bar on the far left/right that shows:
- View icons (Files, Search, Git, etc.)
- Accounts/settings icons at bottom
- Quick access to different views

**Key files:**
- `activitybarPart.ts` - Main activity bar implementation
- `media/activitybarpart.css` - Styling
- `media/activityaction.css` - Action button styling

**Features:**
- Vertical icon bar
- View activation/switching
- Badge support (showing counts)
- Drag and drop reordering
- Global actions (settings, accounts)

---

## Shared Dependencies

The UI bars also depend on:
- `part.ts` - Base class for all workbench parts
- `composite.ts` - Base class for composite views
- `compositeBar.ts` - Generic composite bar implementation

---

## Architecture

All UI bar components follow this pattern:

1. **Extend from `Part` class** - Base class for workbench parts
2. **Implement lifecycle methods**:
   - `create()` - Initialize the DOM structure
   - `layout()` - Handle resizing
   - `toJSON()` - Serialize state
3. **Use dependency injection** - Services injected via constructor
4. **CSS Grid Layout** - Integrated into workbench grid layout

---

## How to Use in Another Project

### Basic Integration Steps:

1. **Copy the components** you need from this folder
2. **Copy dependencies** from `extracted-components/shared/`
3. **Set up services** - The components rely on VS Code's service architecture:
   - Configuration service
   - Theme service
   - Context menu service
   - Layout service

4. **Initialize in order**:
   ```typescript
   // 1. Create the part
   const titlebar = new TitlebarPart(...services);

   // 2. Create DOM container
   const container = document.createElement('div');

   // 3. Initialize
   titlebar.create(container);

   // 4. Add to DOM
   document.body.appendChild(container);

   // 5. Layout
   titlebar.layout(width, height);
   ```

5. **Handle styling** - Include the CSS files from `media/` folders

---

## Key Concepts

### Service Injection
Components use dependency injection for services:
```typescript
constructor(
    @IConfigurationService private readonly configurationService: IConfigurationService,
    @IThemeService themeService: IThemeService,
    // ... more services
)
```

### Grid Layout Integration
Parts are integrated into a SerializableGrid for flexible layout:
- Parts can be resized
- Parts can be moved
- Parts can be hidden/shown
- Layout is saved and restored

### Part Lifecycle
1. **Construction** - Services injected
2. **Creation** - DOM structure built
3. **Layout** - Size calculated and applied
4. **Update** - Content refreshed
5. **Disposal** - Cleanup

---

## Styling

The components use CSS variables for theming:
- Color themes (dark, light, high contrast)
- Font sizes and families
- Spacing and padding
- Border colors and sizes

Example theme variables:
```css
--vscode-titleBar-activeBackground
--vscode-titleBar-activeForeground
--vscode-sideBar-background
--vscode-activityBar-background
```

---

## Dependencies Summary

The UI bars require these from VS Code's architecture:
- **Base utilities** (`vs/base/`) - DOM helpers, events, lifecycle
- **Platform services** (`vs/platform/`) - Configuration, themes, menus
- **Workbench services** - Layout, views, editors

---

## File Structure

```
ui-bars/
├── titlebar/
│   ├── titlebarPart.ts
│   ├── menubarControl.ts
│   ├── commandCenterControl.ts
│   ├── windowTitle.ts
│   └── media/
│       ├── titlebarpart.css
│       └── menubarControl.css
├── sidebar/
│   ├── sidebarPart.ts
│   ├── sidebarActions.ts
│   └── media/
│       └── sidebarpart.css
├── activitybar/
│   ├── activitybarPart.ts
│   └── media/
│       ├── activitybarpart.css
│       └── activityaction.css
├── part.ts
├── composite.ts
├── compositeBar.ts
└── README.md (this file)
```

