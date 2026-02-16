# GRUBBRR Kiosk Tizen V2 Replica

A production-quality replica of the GRUBBRR self-service kiosk interface, built for the Samsung Tizen platform. This application demonstrates the ordering flow with integrated bug visualization for development and testing purposes.

## 🎯 Features

### Core Kiosk Functionality
- **Category Navigation** - Left sidebar with icon-based category selection (Burgers, Sides, Drinks, Desserts)
- **Item Grid** - Scrollable grid of menu items with images, descriptions, and pricing
- **Shopping Cart** - Persistent cart with add/remove/quantity controls
- **Customization Modal** - Item customization with checkboxes for add-ons
- **Checkout Flow** - 3-step checkout: Review → Payment → Confirmation
- **Payment Methods** - Credit Card, Apple Pay, Google Pay, Cash

### Bug Visualization System
Toggle between **FIXED** and **BROKEN** modes to visualize critical bugs:

| Bug ID | Title | Description |
|--------|-------|-------------|
| **NGE-13870** | Card Type Shows "N/A" | Payment screen displays "N/A" instead of detected card type (Visa/MC/Amex) |
| **NGE-13857** | Refund Status Mismatch | Order shows "Processed" but refund shows "Pending" - state inconsistency |
| **NGE-13820** | Scanner Always Active | Barcode scanner remains active on payment/confirmation screens |
| **NGE-13897** | Quantity Slider Sync Issue | Cart quantity slider doesn't sync with actual quantity value |

## 🚀 Quick Start

### Local Development
```bash
# Serve the file locally
cd grubbrr-kiosk
python3 -m http.server 8080

# Or simply open index.html in a browser
open index.html
```

### Deployment
This is a single-file application that can be deployed to any static hosting:
- Netlify
- Vercel
- GitHub Pages
- AWS S3

## 🏗️ Architecture

### Tech Stack
- **Single HTML File** - No build process, no dependencies
- **Vanilla JavaScript** - No frameworks (kiosk-friendly)
- **CSS Grid/Flexbox** - Responsive kiosk layout
- **LocalStorage** - Cart persistence across sessions
- **Touch-friendly UI** - Large buttons, swipe gestures

### File Structure
```
grubbrr-kiosk/
├── index.html      # Complete application (HTML + CSS + JS)
└── README.md       # Documentation
```

### Component Architecture

```
┌─────────────────────────────────────────────────────────┐
│  HEADER (Logo, Call Staff, Language, Cancel)           │
├──────────┬──────────────────────────┬─────────────────┤
│          │                          │                 │
│ CATEGORY │      ITEM GRID           │     CART        │
│ SIDEBAR  │   (Scrollable)           │   SIDEBAR       │
│          │                          │                 │
│  🍔      │  ┌────┐ ┌────┐ ┌────┐   │  🛒 Item 1     │
│  🍟      │  │ 🍔 │ │ 🍟 │ │ 🥤 │   │  🛒 Item 2     │
│  🥤      │  └────┘ └────┘ └────┘   │                 │
│  🍦      │                          │  Subtotal: $0.00│
│          │                          │  Tax: $0.00     │
│          │                          │  Total: $0.00   │
│          │                          │  [Checkout]     │
└──────────┴──────────────────────────┴─────────────────┘
```

### State Management
```javascript
// Core state objects
currentCategory    // Active category ID
cart               // Array of cart items with quantities
currentItem        // Item being customized
currentCustomizations // Selected customizations
checkoutStep       // 1=Review, 2=Payment, 3=Confirm
bugMode            // false=FIXED, true=BROKEN
```

### Data Structure
```javascript
// Category
{ id: 'burgers', name: 'Burgers', icon: '🍔', desc: '...' }

// Menu Item
{
  id: 'b1',
  name: 'Classic Cheeseburger',
  description: 'Beef patty with cheddar...',
  price: 8.99,
  emoji: '🍔',
  badge: 'popular', // or 'new'
  customizations: [
    { name: 'Extra Cheese', price: 1.00 },
    { name: 'Bacon', price: 2.00 }
  ]
}

// Cart Item
{
  id: 'b1',
  name: 'Classic Cheeseburger',
  price: 8.99,
  emoji: '🍔',
  customizations: [...],
  quantity: 2
}
```

## 🐛 Bug Integration Details

### NGE-13870: Card Type Shows "N/A"
**Location:** Payment Screen → Credit Card Method

**Broken Behavior:**
```javascript
function detectCardType() {
  if (bugMode) {
    display.textContent = 'N/A 🐛';  // Always N/A in bug mode
    display.classList.add('na-bug');
    return;
  }
  // Normal detection logic...
}
```

**Visual Indicator:** Red "N/A 🐛" text below payment method

### NGE-13857: Refund Status Mismatch
**Location:** Order Confirmation Screen

**Broken Behavior:**
- Order Status: "Processed" (green)
- Refund Status: "Pending" (orange) - MISMATCH!

**Fixed Behavior:**
- Both Order and Refund Status: "Processed" (green)

### NGE-13820: Scanner Always Active
**Location:** Top center indicator

**Broken Behavior:**
- Scanner indicator pulses orange on ALL screens
- Text shows "Scanner Active (BUG)"
- Should be OFF during payment/confirmation

**Fixed Behavior:**
- Scanner green/active only during menu browsing
- Scanner off (gray) during payment/confirmation

### NGE-13897: Quantity Slider Sync Issue
**Location:** Cart sidebar items

**Broken Behavior:**
- Slider appears with random value (not synced to quantity)
- Moving slider changes color but not actual quantity
- Shows "Unsynced!" label

## 🎨 UI Design System

### Color Palette
```css
--primary: #FF6B35        /* Orange - CTAs, active states */
--primary-dark: #E55A2B   /* Dark orange - hover states */
--success: #27AE60        /* Green - checkout, success */
--danger: #E74C3C         /* Red - remove, cancel */
--warning: #F39C12        /* Orange - alerts, bugs */
--bg-dark: #1A1A2E        /* Main background */
--bg-card: #16213E        /* Card backgrounds */
--bg-sidebar: #0F3460     /* Sidebar backgrounds */
--text-primary: #FFFFFF   /* Primary text */
--text-secondary: #B8C5D6 /* Secondary text */
--bug-indicator: #FF0055  /* Hot pink - bug visualization */
```

### Typography
- **Font Family:** 'Segoe UI', 'Roboto', sans-serif
- **Header:** 22px, weight 800
- **Category Names:** 14px, weight 600
- **Item Names:** 15px, weight 600
- **Prices:** 18px, weight 700, orange
- **Body:** 14px, weight 400

### Touch Targets
- **Buttons:** Minimum 44px height
- **Cards:** Large hit areas with hover feedback
- **Quantity Controls:** 28px × 28px buttons

## 🔧 Development

### Adding New Categories
```javascript
const categories = [
  { id: 'newcat', name: 'New Category', icon: '🆕', desc: 'Description' }
];

const menuItems = {
  newcat: [
    { id: 'n1', name: 'New Item', description: '...', price: 9.99, emoji: '🆕', customizations: [] }
  ]
};
```

### Adding New Bugs
1. Define bug in `bugs` object
2. Add visual indicator with `bug-indicator-icon` class
3. Add conditional logic in relevant functions
4. Add entry to bug panel

### Responsive Breakpoints
- **Desktop:** 1024px+ (3-column layout)
- **Tablet:** 768px-1024px (narrower sidebars)
- **Mobile:** <768px (stacked layout, bottom cart bar)

## 📱 Kiosk UX Best Practices Implemented

1. **Large Touch Targets** - All buttons >= 44px
2. **Visual Feedback** - Hover states, transitions
3. **Progress Indicators** - Checkout steps clearly shown
4. **Error Prevention** - Confirm on cancel, validation
5. **Accessibility** - High contrast, clear labels
6. **Performance** - No external dependencies, instant load
7. **State Persistence** - Cart saved to LocalStorage

## 🚀 Deployment Checklist

- [ ] Test on target kiosk resolution (typically 1920×1080)
- [ ] Verify touch response on actual hardware
- [ ] Test all payment flows
- [ ] Verify bug mode toggle works
- [ ] Check LocalStorage persistence
- [ ] Validate printer integration (if applicable)
- [ ] Test scanner integration (if applicable)

## 📄 License

Internal use only - GRUBBRR Kiosk Replica for development and testing purposes.

## 🤝 Contributing

When adding features:
1. Maintain single-file architecture
2. Use vanilla JavaScript only
3. Follow existing CSS custom properties
4. Test in both FIXED and BROKEN modes
5. Update this README with changes