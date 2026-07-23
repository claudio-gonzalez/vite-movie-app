# Movie App

## Overview

A movie discovery web application built with React and Vite. Users can browse popular movies, search the TMDB catalog in real time, and view posters, ratings, language, and release year. Search activity is logged to Appwrite so the most frequently searched titles can be surfaced as a "Trending" list.

This project started as a guided React learning exercise. It has since been revisited to remove leftover security issues from that original tutorial and to bring the codebase closer to what a small, honest client-side app should look like.

## Features

- Browse popular movies on load, sorted by popularity
- Debounced search-as-you-type against the TMDB API
- Movie cards with poster, rating, original language, and release year
- Fallback poster image for movies with no artwork
- Trending list based on search counts stored in Appwrite
- Loading and error states for the movie list

## Technology Stack

| Technology   | Purpose                                             |
| ------------ | ---------------------------------------------------- |
| React        | UI components and state management                  |
| Vite         | Development server and production build tooling     |
| JavaScript   | Application logic                                    |
| Tailwind CSS | Styling                                              |
| Appwrite     | Backend-as-a-service for storing search metrics      |
| TMDB API     | Source of movie data (search, discovery, posters)    |
| react-use    | `useDebounce` hook for throttling search requests    |

## Project Structure

```
src/
  App.jsx           # Top-level component: fetches movies, renders layout
  appwrite.js        # Appwrite client + search-count read/write helpers
  components/
    Search.jsx        # Search input
    MovieCard.jsx      # Movie poster/rating card
    Spinner.jsx        # Loading indicator
  index.css / App.css  # Tailwind and component styling
public/                # Static assets (images, icons)
```

## Getting Started

### Prerequisites

- Node.js 18+
- An [Appwrite Cloud](https://cloud.appwrite.io) project with a database and collection for search metrics
- A [TMDB](https://www.themoviedb.org/documentation/api) account with a v3 API key

### Installation

```bash
npm install
```

### Environment Variables

Create a `.env.local` file in the project root (this file is git-ignored) with the following keys:

```
VITE_TMDB_V3_KEY=
VITE_APPWRITE_PROJECT_ID=
VITE_APPWRITE_DATABASE_ID=
VITE_APPWRITE_COLLECTION_ID=
```

No values are committed to the repository. Populate them with your own TMDB and Appwrite credentials.

### Running Locally

```bash
npm run dev
```

### Building

```bash
npm run build
```

Output is written to `dist/`.

## Deployment

The app is a static, single-page build with no server-side component, so it is deployed as a static site on GitHub Pages. Running `npm run build` produces the `dist/` folder, which is what gets published.

## Architecture

This is a purely client-side application: the browser calls the TMDB and Appwrite APIs directly, with no backend of its own. That fits the project's origin as a React learning exercise, where the goal was to practice component design, state, and API integration rather than backend engineering.

The TMDB v3 key is used directly from the browser. This is acceptable here because it only grants access to public movie metadata — there is no sensitive data or write access behind it. It would not be an acceptable pattern for an API key that carried more privilege.

## Security Notes

This repository originally followed a beginner tutorial, which left a few practices in place that don't hold up outside a learning context. As part of a later review, the following was addressed:

- Removed a TMDB v4 read access token that had been exposed in the frontend, and rotated it
- Switched the app to a TMDB v3 API key, which is scoped for this kind of client-side use
- Confirmed `.env.local` is git-ignored so local credentials are never committed
- Deleted an obsolete `gh-pages` deployment branch left over from the tutorial's setup
- Updated dependencies and resolved all `npm audit` findings
- Tightened Appwrite collection permissions to the minimum needed for the search-count feature
- Reviewed the repository and deployment history for other leftover exposure

The trade-off called out above still stands: calling TMDB directly from the browser is fine for public, read-only movie data, but it's not a pattern to carry into an application with real credentials or user data.

## Lessons Learned

This project was a practical introduction to a handful of things that are easy to read about but only really click by building something:

- **React**: component composition, props flow, and conditional rendering for loading/error/empty states
- **State management**: keeping `useState` scoped to what each component actually needs, and lifting state only where it's shared (e.g., `searchTerm` between `App` and `Search`)
- **API integration**: debouncing user input before hitting an external API, and handling failure states instead of assuming the network always succeeds
- **Frontend security**: the difference between an API key that's safe to ship in a browser bundle and one that isn't, and why "it works" isn't the same as "it's safe to expose"
- **Environment variables**: using `.env.local` and Vite's `import.meta.env` to keep configuration out of source control
- **Production architecture**: recognizing where a client-only architecture is a reasonable choice versus where it's a shortcut that would need to be revisited before shipping something real

## Future Improvements

- Route TMDB requests through a backend or serverless proxy to keep the API key off the client, add rate limiting, and centralize logging
- User accounts, so trending/search data and favorites are tied to a person rather than global
- Favorites/watchlist functionality
- Pagination for search and discovery results
- Richer search (filter by genre, year, rating)
- Client-side caching of movie data to reduce redundant TMDB calls

---

**A note on Appwrite Cloud**: this project uses the Appwrite Cloud Free tier, which can enter a sleep state after periods of inactivity — the first request after idle time may be noticeably slower. That's an acceptable trade-off for a learning project, but it's not something you'd want in a production system, where you'd either pay for a warm instance or self-host.
