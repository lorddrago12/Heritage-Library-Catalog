# 📚 Heritage Library Catalog

A lightweight, dependency-free JavaScript library for parsing, searching, grouping, validating, and exporting vintage-style library catalog cards.

---

## Overview

**Heritage Library Catalog** turns raw pipe-delimited catalog card strings into a structured, queryable in-memory catalog. It gracefully handles incomplete or legacy records — missing authors, unknown years, empty shelf locations — making it ideal for digitizing historical collections.

```
"The Shining | King, Stephen | 1977 | Shelf K1"
        ↓
{ title: "The Shining", author: "King, Stephen", year: 1977, location: "Shelf K1" }
```

---

## Features

- **Parse** pipe-delimited catalog card strings into structured objects
- **Search** by author name (case-insensitive, partial match)
- **Group** books by decade (e.g. `1970s`, `1980s`, `Unknown`)
- **Validate** entries for completeness
- **Export** the catalog to JSON or CSV
- **Render** individual entries as formatted text blocks
- Graceful fallback to `"Unknown"` for any missing field

---

## Catalog Format

Each raw catalog card is a pipe-delimited string with four fields:

```
"Title | Author (Last, First) | Year | Shelf Location"
```

| Field    | Example            | Notes                          |
|----------|--------------------|--------------------------------|
| Title    | `The Shining`      | Required for valid entry       |
| Author   | `King, Stephen`    | Last name first                |
| Year     | `1977`             | Parsed as integer              |
| Location | `Shelf K1`         | Physical shelf identifier      |

Missing fields are replaced with `"Unknown"` automatically.

---

## Usage

### 1. Define your raw catalog cards

```js
const rawCatalogCards = [
  "The Shining | King, Stephen | 1977 | Shelf K1",
  "Dune | Herbert, Frank | 1965 | Shelf H3",
  "Mysterious Manuscript | Unknown Author |  | Shelf Z9",
];
```

### 2. Parse into a catalog

```js
const catalog = parseCatalog(rawCatalogCards);
// Returns an array of { title, author, year, location } objects
```

### 3. Search by author

```js
const kingBooks = findByAuthor(catalog, "king");
// Case-insensitive, partial match
```

### 4. Group by decade

```js
const byDecade = groupByDecade(catalog);
// { "1960s": [...], "1970s": [...], "Unknown": [...] }
```

### 5. Validate an entry

```js
const isValid = validateEntry(catalog[0]);
// Returns false if any field is missing or "Unknown"
```

### 6. Export

```js
const json = exportToJSON(catalog);  // Pretty-printed JSON string
const csv  = exportToCSV(catalog);   // CSV with header row
```

### 7. Render a single entry

```js
console.log(renderEntry(catalog[0]));
// -------------------------
// Title: The Shining
// Author: King, Stephen
// Year: 1977
// Location: Shelf K1
// -------------------------
```

---

## API Reference

### `parseCard(rawString) → Object`
Parses a single pipe-delimited card string. Returns `{ title, author, year, location }`.

### `parseCatalog(rawCards) → Object[]`
Parses an array of raw card strings into a full catalog array.

### `findByAuthor(catalog, author) → Object[]`
Returns all entries whose `author` field contains the search term (case-insensitive).

### `groupByDecade(catalog) → Object`
Returns an object keyed by decade string (e.g. `"1980s"`). Entries with no year are grouped under `"Unknown"`.

### `validateEntry(entry) → boolean`
Returns `true` only if all four fields are present and not `"Unknown"`.

### `renderEntry(entry) → string`
Returns a formatted multi-line string representation of a catalog entry.

### `exportToJSON(catalog) → string`
Serializes the full catalog to a pretty-printed JSON string.

### `exportToCSV(catalog) → string`
Serializes the full catalog to a CSV string with a `Title,Author,Year,Location` header.

---

## Sample Catalog

The included dataset spans from **850** to **2011** and covers 19 entries across authors including Stephen King, Isaac Asimov, Philip K. Dick, Frank Herbert, and more.

| Decade  | Titles |
|---------|--------|
| 850s    | Ancient Scroll |
| 1950s   | I, Robot · Foundation |
| 1960s   | Do Androids Dream of Electric Sheep? · Dune |
| 1970s   | The Shining · The Stand · The Hitchhiker's Guide to the Galaxy |
| 1980s   | It · Misery · Neuromancer · Ender's Game · The Dark Tower: The Gunslinger |
| 1990s   | Snow Crash |
| 2000s   | From a Buick 8 |
| 2010s   | The Martian · Ready Player One |
| Unknown | Mysterious Manuscript |

---

## Handling Incomplete Records

Heritage Library Catalog is designed to survive messy legacy data:

```js
"Unknown Title |  | 1975 | Shelf X1"
// → { title: "Unknown Title", author: "Unknown", year: 1975, location: "Shelf X1" }

"Mysterious Manuscript | Unknown Author |  | Shelf Z9"
// → { title: "Mysterious Manuscript", author: "Unknown Author", year: "Unknown", location: "Shelf Z9" }

"Ancient Scroll | Anonymous | 850 | "
// → { title: "Ancient Scroll", author: "Anonymous", year: 850, location: "Unknown" }
```

`validateEntry()` will flag these entries so you can audit them separately.

---
