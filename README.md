# Smart Cab Allocation - Frontend

A React web app for finding, booking and tracking nearby cabs on Google Maps. It also includes an admin dashboard for managing cabs and users.

![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)
![React Router](https://img.shields.io/badge/React%20Router-6-CA4245?logo=reactrouter&logoColor=white)
![Google Maps](https://img.shields.io/badge/Google%20Maps-JS%20API-4285F4?logo=googlemaps&logoColor=white)
![Vercel](https://img.shields.io/badge/deployed%20on-Vercel-000000?logo=vercel)

> **Related repositories**
> - Backend (Express + MongoDB): [smart-cab-allocation-backend](https://github.com/VamsiKrishnaThota03/smart-cab-allocation-backend)
> - Combined repo (frontend + `backend/` folder): [smart-cab-allocation](https://github.com/VamsiKrishnaThota03/smart-cab-allocation)

## Live demo

**https://smart-cab-allocation-frontend.vercel.app/login**

Admin demo account (from the original README):

| Email | Password |
| --- | --- |
| `admin@gmail.com` | `12345678` |

## Overview

The app is a Create React App frontend. It talks to the deployed backend at `https://smart-cab-allocation-backend.onrender.com` and loads the Google Maps JavaScript API directly in the browser. Riders see available cabs on a map, get a "nearest cabs" list ranked by distance, book one, and watch a simulated trip along the real driving route. Admins add cabs by picking a spot on the map and manage registered users.

## Features

**Riders**
- Sign up and log in with JWT auth. The token is kept in `localStorage`, and protected routes use a `PrivateRoute` guard.
- A Google Map shows your current location (browser Geolocation API) and every available cab as a taxi marker.
- Enter pickup ("From", filled in from your location) and drop-off ("To") coordinates to find cabs:
  - all available cabs are sorted by straight-line distance (Haversine formula), with an estimated time to reach you
  - the "nearest cabs" list keeps cabs within **5 km**, or in the same **town or pincode** (Google Geocoding API)
- Book a cab. It is marked unavailable for other users.
- **My Cab** page: the app draws a driving route from the cab to you (Google Directions API) and animates the cab marker along it, updating the remaining distance and ETA. When the cab arrives, **End Trip** unlocks and moves the cab to the drop-off point, making it available again.
- Profile page with the logged-in user's details

**Admins**
- Admin dashboard for adding a cab (name, phone, email, location). Set the location by clicking the map or typing coordinates; town and pincode are filled in by reverse geocoding.
- Manage users: list all users and delete non-admin users
- Admin-only routes check the `isAdmin` claim in the JWT

## Tech stack

| Area | Tools |
| --- | --- |
| UI | React 18, Create React App (`react-scripts`), React Router 6 |
| Maps | Google Maps JavaScript API (Maps, Markers, Directions, Geocoding), `@react-google-maps/api` |
| HTTP / auth | `axios`, `fetch`, `jwt-decode` |
| Icons | `react-icons`, Font Awesome |
| Hosting | Vercel |

## Project structure

```
smart-cab-allocation-frontend/
├── public/
└── src/
    ├── App.js                  # routes
    └── components/
        ├── Login.js, SignUp.js
        ├── Home.js             # map, nearest-cab search, booking
        ├── MyCab.js            # route + simulated cab movement, end trip
        ├── UserDetails.js      # profile
        ├── AdminDashboard.js   # add cab via map
        ├── AdminUsers.js       # list / delete users
        ├── Navbar.js, AdminNavbar.js
        └── PrivateRoute.js     # auth + admin route guard
```

### Routes

| Path | Access | Page |
| --- | --- | --- |
| `/login`, `/signup` | Public | Authentication |
| `/` | User | Map, cab search and booking |
| `/myCab` | User | Track the booked cab and end the trip |
| `/profile` | User | Profile |
| `/admin-dashboard` | Admin | Add cabs |
| `/admin/users` | Admin | Manage users |

## Getting started

### Prerequisites

- Node.js and npm
- A browser with location access enabled
- A running backend ([smart-cab-allocation-backend](https://github.com/VamsiKrishnaThota03/smart-cab-allocation-backend)); the code points at the deployed Render instance by default

### Install and run

```bash
git clone https://github.com/VamsiKrishnaThota03/smart-cab-allocation-frontend.git
cd smart-cab-allocation-frontend
npm install
npm start          # http://localhost:3000
```

| Command | Description |
| --- | --- |
| `npm start` | Start the dev server |
| `npm run build` | Production build into `build/` |
| `npm test` | Run the CRA test runner |

### Configuration

There are no environment variables yet. These values are hard-coded in `src/components/*.js`:

| Setting | Where | Replace with |
| --- | --- | --- |
| Backend base URL | `https://smart-cab-allocation-backend.onrender.com` in `Home.js`, `MyCab.js`, `Login.js`, `SignUp.js`, `UserDetails.js`, `AdminDashboard.js`, `AdminUsers.js` | `<your-backend-url>` (for example `http://localhost:5001`) |
| Google Maps API key | `Home.js`, `MyCab.js`, `AdminDashboard.js` | `<your-google-maps-api-key>` with the Maps JavaScript, Directions and Geocoding APIs enabled |

To make this configurable, move the values into `REACT_APP_API_URL` and `REACT_APP_GOOGLE_MAPS_API_KEY` in a `.env` file and read them through `process.env`.
