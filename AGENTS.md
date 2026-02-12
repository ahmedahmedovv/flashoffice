# Flashcards - AI Agent Guide

## Project Overview

Flashcards is a **single-page web application** for vocabulary learning using the Spaced Repetition System (SRS). The entire application is contained in one HTML file (`index.html`) with embedded CSS and JavaScript.

### Core Features
- **Study Mode**: Fill-in-the-blank style flashcards where users type the missing word
- **Spaced Repetition**: Automatically schedules cards based on performance using 10 intervals (1min → 10min → 1hr → 6hr → 1day → 3days → 7days → 14days → 30days → 90days)
- **Dashboard**: Side panel with statistics, card management, and data import/export
- **Card Management**: Add, edit, delete cards with validation
- **Data Persistence**: Uses browser localStorage
- **Practice Mode**: Review all cards regardless of due date

### Data Model
Each card has the following structure:
```javascript
{
  w: string,      // word (the answer)
  d: string,      // definition (hint)
  s: string,      // sentence with the word (must include the word)
  level: number,  // mastery level (0-9+)
  due: timestamp  // when card is due for review
}
```

---

## Technology Stack

| Component | Technology |
|-----------|------------|
| Language | HTML5, CSS3, Vanilla JavaScript (ES6+) |
| Storage | Browser localStorage |
| Build Process | None - static HTML file |
| Dependencies | None |
| Architecture | Modular IIFE pattern with no external libraries |

---

## Project Structure

```
flashoffice/
├── index.html       # Complete application (HTML + CSS + JS)
├── README.md        # Project name only
└── AGENTS.md        # This file
```

### Code Organization

The JavaScript is organized into 15 modules within a single IIFE:

| Module | Purpose |
|--------|---------|
| 1. Constants | Configuration values, intervals, seed cards |
| 2. Utilities | Helper functions (escapeHTML) |
| 3. DOM Cache | Centralized DOM element caching |
| 4. Storage | localStorage read/write operations |
| 5. SRS | Spaced repetition algorithm |
| 6. CardManager | CRUD operations for cards |
| 7. Toast | Notification system |
| 8. ConfirmDialog | Reusable confirmation dialogs |
| 9. UI | Statistics display and list rendering |
| 10. Panel | Side panel toggle/open/close |
| 11. Modal | Add/Edit card modal |
| 12. Study | Study session logic |
| 13. DataIO | Import/Export JSON data |
| 14. Event Binding | All event listeners |
| 15. App Init | Initialization and public API |

---

## Key Configuration

### Storage Keys
- `flashcards` - Main card data (JSON array)
- `flashcards_cleared` - Flag indicating if user cleared all cards (prevents seed cards from reappearing)

### SRS Constants
```javascript
INTERVALS_MINUTES = [1, 10, 60, 360, 1440, 4320, 10080, 20160, 43200, 129600]
MASTERY_THRESHOLD = 4  // Level 4+ considered "mastered"
```

### Seed Cards
Three default cards are loaded on first run:
- apple, ocean, brilliant

---

## Development Guidelines

### Code Style
- **Modular Pattern**: Use the existing module structure
- **DOM Access**: Cache elements in the `DOM` object, never query directly in loops
- **String Escaping**: Always use `escapeHTML()` for user-generated content in innerHTML
- **Event Handling**: Use `bindEvents()` for all listeners, prefer event delegation

### Adding New Features
1. Add constants to Module 1 if needed
2. Create new module or extend existing one
3. Update DOM cache in Module 3
4. Add event bindings in Module 14
5. Expose public methods in the return object (Module 15)

### CSS Guidelines
- Uses CSS custom properties (variables) for theming
- Mobile-responsive with media queries at 640px breakpoint
- BEM-like naming for component classes

---

## Testing

### Manual Testing Checklist
1. **Card Creation**: Add card with word, definition, sentence
2. **Validation**: Sentence must contain the word (case-insensitive)
3. **Study Flow**: Answer correctly → level up, incorrectly → level down
4. **Due Cards**: Cards with `due <= now()` appear in study queue
5. **Import/Export**: JSON export/import preserves all card data
6. **Edge Cases**: Delete card during study, clear all data, practice mode

### Data Validation
When importing, cards must have `w`, `d`, and `s` properties. Duplicates (by lowercase word) are skipped.

---

## Deployment

This is a **static HTML application** with no build step:

1. Serve `index.html` with any static file server
2. No server-side processing required
3. All data stored in browser localStorage

Example deployment options:
- GitHub Pages
- Netlify Drop
- Vercel
- Any web server (nginx, Apache, etc.)

---

## Security Considerations

1. **XSS Prevention**: User input is escaped via `escapeHTML()` before rendering
2. **No Server**: No authentication, sessions, or server-side vulnerabilities
3. **Data Privacy**: All data stays in browser localStorage
4. **Import Validation**: Imported JSON is validated for required fields

---

## Browser Compatibility

- Modern browsers with ES6+ support
- Uses: localStorage, template literals, arrow functions, const/let, classList API
- Tested on: Chrome, Firefox, Safari, Edge (latest versions)

---

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| Enter | Submit answer / Save card / Confirm dialog |
| Escape | Close modal, panel, or dialog |

---

## State Management

The application uses a simple state pattern:
- `cards[]` - Array of all flashcards (Module 6)
- `currentCard` - Card currently being studied (Module 12)
- `editingIndex` - Index of card being edited (-1 for new card)
- `practiceMode` - Boolean for practice vs. SRS mode

State changes trigger UI updates via `UI.refresh()`.
