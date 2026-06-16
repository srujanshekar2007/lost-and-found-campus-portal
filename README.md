# Findly Campus - Lost & Found Portal

Modern MERN stack Lost & Found Campus Portal built for hackathon judging. The app combines a premium SaaS-style UI with explicit Data Structure & Algorithm demonstrations using arrays and a hash map search engine.

## Features

- Report lost items and found items
- Instant keyword search powered by `Map<String, Item[]>`
- Dashboard tabs for lost, found, and claimed items
- Claim flow using `foundItems.splice(index, 1)` and `claimedItems.push(item)`
- Smart matching when a lost item is reported
- Recent item highlight for posts created within 24 hours
- Analytics cards and charts
- Dark/light mode toggle
- Responsive glassmorphism UI with Framer Motion animations
- Sonner toast notifications
- MongoDB Atlas integration with local in-memory fallback

## Tech Stack

Frontend: React, Vite, Tailwind CSS, ShadCN-style local UI components, Framer Motion, Lucide Icons, Sonner, Recharts.

Backend: Node.js, Express.js, MongoDB Atlas, Mongoose.

State: React Context API.

## Folder Structure

```txt
client/
 ├── components/
 ├── pages/
 ├── hooks/
 ├── context/
 ├── utils/
 ├── services/

server/
 ├── controllers/
 ├── routes/
 ├── models/
 ├── middleware/
 ├── utils/
```

## Run Locally

```bash
npm install
npm run dev
```

Client: `http://localhost:5173`

Server: `http://localhost:5001`

MongoDB is optional for demo mode. To use MongoDB Atlas:

```bash
cp server/.env.example server/.env
```

Then set `MONGODB_URI` in `server/.env`.

## DSA Implementation

### Arrays

The project explicitly uses three arrays:

```js
lostItems = []
foundItems = []
claimedItems = []
```

Implemented operations:

- `push()` stores new items in the backend memory store.
- `filter()` separates lost, found, claimed, and match results.
- `map()` renders cards and computes smart match scores.
- `find()` / `findIndex()` locates claimable found items.
- `splice()` removes a claimed item from `foundItems`.
- `sort()` orders items and smart matches.

Core claim logic:

```js
const index = foundItems.findIndex((item) => item.id === id);
const [item] = foundItems.splice(index, 1);
claimedItems.push({ ...item, status: "claimed" });
```

### Hash Map Search

The search engine builds:

```js
Map<String, Item[]>
```

When an item is added:

1. Convert the item name to lowercase.
2. Split the name into keywords.
3. Store each keyword as a hash map key.
4. Push the item into that keyword bucket.

Example:

```txt
Black Leather Wallet
```

Stored as:

```txt
black   -> [item]
leather -> [item]
wallet  -> [item]
```

Search lookup:

```js
const bucket = searchMap.get(keyword) || [];
```

This gives average `O(1)` lookup per keyword instead of scanning all items with `O(n)` search.

Relevant files:

- `client/src/utils/dsaStore.js`
- `server/utils/hashSearch.js`
- `server/utils/memoryStore.js`

## Smart Matching

When a lost item is posted, it is compared with active found items.

```txt
matchingScore = keywordMatches / totalKeywords
```

Example:

- Lost: `Black Wallet`
- Found: `Black Leather Wallet`
- Score: `2 / 2 = 100%`

## API Documentation

Base URL: `/api/items`

### Get Items

```http
GET /api/items
```

Response:

```json
{
  "lostItems": [],
  "foundItems": [],
  "claimedItems": []
}
```

### Create Item

```http
POST /api/items
```

Body:

```json
{
  "name": "Black Wallet",
  "description": "Student ID inside",
  "location": "Library",
  "date": "2026-06-16",
  "contact": "student@campus.edu",
  "imageUrl": "",
  "type": "lost"
}
```

### Search Items

```http
GET /api/items/search?q=wallet
```

Response includes `results`, `keywords`, and `complexity`.

### Claim Found Item

```http
PATCH /api/items/:id/claim
```

Updates the item status to `claimed`.

## Presentation Notes

For judges, demonstrate:

1. Add a found item named `Black Leather Wallet`.
2. Add a lost item named `Black Wallet`.
3. Show the smart match score.
4. Search `wallet` and open the hash map preview.
5. Claim the found item and show it moving from Found Items to Claimed Items.
