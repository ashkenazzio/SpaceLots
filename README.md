# SpaceLots: Buy Your Plot Among the Stars

A tongue-in-cheek React storefront for buying real estate on other planets. Browse a handful of "space lots" (the Moon, Mars, Venus, Jupiter), pick a quantity, drop them in a cart, and watch your interplanetary property portfolio add up. It's a single-page app built with Create React App and deployed to GitHub Pages.

> **Why it exists.** This was a deliberate practice project: I wanted to get hands-on with the core React patterns (components, props, hooks, context, and a reducer-backed store) and I figured I might as well make it fun while I was at it. The premise is a joke (the hero copy opens with "Can't find an apartment in Tel Aviv?"), but the cart logic underneath is the real exercise. It's small on purpose, and I'd rather it be honestly small than dressed up as more than it is.

---

## What It Does

- **Browse lots:** a gallery of planetary plots, each with its own image, description, and price
- **Add to cart with quantity:** each lot has a small form to choose an amount (1 to 9) with inline validation before anything is added
- **Live cart:** a modal cart that lists everything you've selected, lets you bump quantities up or down per item, and recalculates the running total on the fly
- **Cart badge with feedback:** the header button shows a live item count and gives a little "bump" animation each time the cart changes, so the action registers visually
- **Merge on re-add:** adding a lot that's already in the cart increases its quantity instead of creating a duplicate line

## What It Showcases

A focused tour of foundational React, without reaching for a UI library or extra state tooling:

- **Global state with Context + `useReducer`:** the cart lives in a [`CartProvider`](src/Store/CartProvider.js) backed by a reducer that handles `ADD` and `REMOVE` actions, including the merge-quantities and remove-or-decrement branches; any component reads or mutates it through [`cart-context`](src/Store/cart-context.js) without prop drilling
- **Hooks across the board:** `useReducer` for the store, `useContext` for consumers, `useState` for local UI (cart visibility, validation), `useRef` for uncontrolled form input, and a `useEffect` cleanup in [`HeaderCartButton`](src/components/Layout/HeaderCartButton.js) that drives the bump animation with a timer that clears itself
- **React Portals:** the cart [`Modal`](src/components/UI/Modal.js) renders its backdrop and overlay into a dedicated `#overlays` DOM node via `ReactDOM.createPortal`, keeping overlay markup out of the normal component tree
- **Reusable, forwarded-ref components:** a generic [`Input`](src/components/UI/Input.js) built with `React.forwardRef` so a parent form can read its value imperatively
- **Component-scoped styling:** CSS Modules throughout, so every component owns its own styles with no global class collisions
- **Form validation:** the [`LotItemForm`](src/components/Lots/LotItemForm.js) validates the entered amount before dispatching, and surfaces an inline error message when it's out of range

## Architecture

A flat single-page app. State flows down through context; events flow back up through handlers the provider exposes. The cart reducer is the single source of truth for what's in the cart and what it costs.

```
                         ┌──────────────────────┐
                         │     CartProvider     │
                         │  useReducer(cart)    │  state: items, totalAmount
                         │  addItem / removeItem │  actions exposed via context
                         └──────────┬───────────┘
                                    │  CartContext
        ┌───────────────────────────┼───────────────────────────┐
        ▼                           ▼                           ▼
┌───────────────┐          ┌────────────────┐          ┌────────────────┐
│    Header     │          │    LotsList    │          │      Cart      │
│ HeaderCart-   │          │   → Lot (×4)   │          │  (Modal portal)│
│ Button (count │          │   → LotItemForm│          │  → CartItem    │
│  + bump fx)   │          │     (validate, │          │    (+/- qty)   │
│               │          │      addItem)  │          │                │
└───────────────┘          └────────────────┘          └────────────────┘
        │                                                       │
        └───────────────── reads/dispatches ────────────────────┘
                          through CartContext
```

The lot catalog itself is a static array in [`LotsList`](src/components/Lots/LotsList.js): there's no backend, no fetch, and no database. Lot images are pulled in dynamically with `require(`../../assets/${id}.jpg`)` so a lot's `id` maps straight to its image file.

## Tech Stack

| Layer        | Technology                                      |
| ------------ | ----------------------------------------------- |
| Library      | React 18                                        |
| Scaffolding  | Create React App (`react-scripts`)              |
| State        | Context API + `useReducer` (no Redux)           |
| Styling      | CSS Modules                                     |
| Overlays     | `ReactDOM.createPortal`                         |
| Deployment   | GitHub Pages (`gh-pages`)                        |

## Getting Started

### Requirements

- [Node.js](https://nodejs.org/) (LTS recommended)
- npm

### Installation

```bash
# Clone the repository
git clone https://github.com/ashkenazzio/SpaceLots.git
cd SpaceLots

# Install dependencies
npm install

# Start the dev server
npm start
```

The app runs at **http://localhost:3000**.

To create a production build, run `npm run build`. Deployment to GitHub Pages is wired up through `npm run deploy` (which builds first via the `predeploy` script).

## Project Structure

```
SpaceLots/
├── public/
│   └── index.html              # Hosts #root and a separate #overlays node for portals
└── src/
    ├── App.js                  # Top-level layout + cart-visibility state
    ├── index.js                # React entry point
    ├── Store/
    │   ├── CartProvider.js     # Cart reducer (ADD/REMOVE) + context provider
    │   └── cart-context.js     # Context shape + default value
    ├── components/
    │   ├── Layout/             # Header, Hero, Footer, HeaderCartButton (count + bump)
    │   ├── Lots/               # LotsList (catalog), Lot (card), LotItemForm (qty + validation)
    │   ├── Cart/               # Cart, CartItem, CartIcon
    │   └── UI/                 # Reusable Modal (portal) and forwardRef Input
    └── assets/                 # Lot images (l1–l4) and planet artwork
```

## Limitations / What I'd Do Differently Today

This is a learning-scale project, and a few things are intentionally left simple:

- **No checkout:** the "Order" button is a placeholder; there's no order submission, payment, or backend
- **No persistence:** the cart lives in memory, so a refresh empties it; `localStorage` or a backend would fix that
- **Static catalog:** the lots are hard-coded in the component rather than fetched from an API
- **No tests:** the CRA test tooling is installed but I never wrote a suite; the reducer logic in particular would be a natural, easy thing to unit-test
- **Class names over `clsx`:** conditional classes are string-concatenated by hand, which is fine at this size but wouldn't scale

If I were rebuilding it now I'd likely reach for a current toolchain (Vite over CRA) and extract the catalog behind a real data source, but the point of this one was the React fundamentals, and those are all here.

---

_Real estate has never been this far away._ 🪐
</content>
</invoke>
