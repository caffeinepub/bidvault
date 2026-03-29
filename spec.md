# BidNova

## Current State
LiveAuction.tsx has a full live bidding page with a countdown timer (CountdownTimer component), bot simulation, bid history, anti-sniping, and a static "Auction Completed" banner shown when `auction.status === 'completed'`. No modal/popup for winner declaration exists yet.

## Requested Changes (Diff)

### Add
- `WinnerDeclarationModal` component (new file: `src/frontend/src/components/WinnerDeclarationModal.tsx`)
  - Centered modal overlay shown when auction timer hits zero
  - Phase 1 (3-5s): animated "counting down" / suspense animation before revealing result
  - Phase 2: winner card OR no-winner card based on `hasBids` flag
  - Dummy data: `{ item: "Camera", highestBid: 15000, winner: "John Doe" }`
  - Winner message: "🎉 Congratulations! You won this auction."
  - No-winner message: "No winner for this auction"
  - Others message: "Auction ended. Better luck next time!"
  - Trophy icon, bold text, smooth entrance animation
  - Toggle button inside modal to switch between "With Bids" and "No Bids" scenarios
  - Card-style popup with shadow, gradient background, fully responsive

- Integration in `LiveAuction.tsx`:
  - Track `auctionEnded` state: set to `true` when `timeRemaining <= 0` and auction was active
  - Show `WinnerDeclarationModal` when `auctionEnded === true`
  - Pass dummy winner data and `hasBids` toggle state into modal

### Modify
- `LiveAuction.tsx`: add `auctionEnded` state + effect watching `timeRemaining`, render `WinnerDeclarationModal`

### Remove
- Nothing removed

## Implementation Plan
1. Create `WinnerDeclarationModal.tsx` with:
   - Props: `isOpen`, `onClose`, `hasBids`, `onToggleBids`, `winnerData`
   - Phase state: `'suspense' | 'result'`
   - On open: start 4s suspense animation (pulsing trophy, dots, countdown feel), then transition to result
   - Result card: trophy icon, "Auction Ended", "Winner Declared", item/winner/bid info with animations
   - No-bids card: "No winner for this auction" message
   - Toggle button: "Switch to No Bids" / "Switch to With Bids"
   - Close button
2. In `LiveAuction.tsx`:
   - Add `showWinnerModal` and `hasBids` states
   - useEffect: when `timeRemaining <= 0 && isActive`, set `showWinnerModal = true`
   - Render `<WinnerDeclarationModal>` at the end of the JSX
