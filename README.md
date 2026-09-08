# GreenCorridor

**GreenCorridor** is a real-time emergency ambulance navigation and traffic signal preemption system. It simulates how an ambulance dispatched from any location can have all traffic signals on its route turned green automatically, minimizing delays and saving lives.

##  Live Demo
 [**Launch Dynamic Green Corridor**] : ( https://greencorridor.vercel.app )

 
## Core Features
- **Intelligent Route Selection:** Fetches optimal driving paths using OSRM and lets the user or system select the most direct route.
- **3-Ring Signal Preemption:** A dynamic geofence system (2 km, 1 km, 500 m rings) triggers green lights well ahead of the ambulance's arrival.
- **Multi-Ambulance Fleet & Arbitration:** Supports up to 4 concurrent ambulances with intelligent conflict resolution when paths intersect.
- **Dynamic Rerouting:** Automatically detects blocked roads and recalculates the best alternative route in real-time.
- **Voice Guidance:** Multilingual speech alerts powered by the Web Speech API (English, Hindi, Kannada, Tamil, Telugu).
- **Live Database Simulation:** Syncs data via Firebase Realtime Database for cross-session fleet tracking.

## Technology Stack
- **Frontend Framework:** Vanilla HTML5, CSS3, JavaScript (No heavy frameworks)
- **Map & Geospatial Mathematics:** Leaflet.js, CartoDB Tiles, Turf.js
- **Routing & Geocoding APIs:** OSRM (Open Source Routing Machine), Nominatim / OpenStreetMap
- **Cloud Database:** Firebase Realtime Database
- **Build Tool:** Webpack

## Key Algorithms Used

1. **A* Deviation Scoring (Route Efficiency)**
   - When fetching alternative routes from OSRM, the engine calculates an A* deviation score to measure how much the road network deviates from the optimal straight-line "as the crow flies" path.
   - **Mechanism:** `deviation = Σ (distance of each waypoint from the straight line)`. A lower deviation equates to a more direct, efficient emergency path, which is used as a tie-breaker for routes with similar distances.

2. **Haversine Formula (Geofencing)**
   - The system utilizes Turf.js (which implements the Haversine formula) to calculate accurate spherical distances between the ambulance's live GPS coordinates and upcoming traffic signals. 
   - This continuously drives the state machine for the 3-ring preemption system (Outer Awareness, Mid Approach, Inner Preemption).

3. **Priority Arbitration Algorithm (Conflict Resolution)**
   - If two or more ambulances cross paths at the same physical traffic signal simultaneously, the system uses a priority scoring formula to determine right-of-way.
   - **Formula:** `priorityScore = (severity × 1000) − etaToConflictPoint (seconds)`
   - Severity is ranked from 1 (low) to 3 (critical). The unit with the highest score is granted the green light, while the losing unit is held in a red state until the intersection clears.

4. **Load-Balancing Algorithm (Multi-Fleet Navigation)**
   - When dispatching secondary ambulances while a corridor is already active, the engine fetches multiple route alternatives and selects the one with the *minimum physical overlap* with existing corridors.
   - **Mechanism:** It compares waypoints against active routes and chooses the path with the fewest points within a 100-meter radius of active ambulances, distributing traffic intelligently.

## Getting Started

Because GreenCorridor is a statically bundled application, it is exceptionally easy to build and run.

### Local Development
1. Install dependencies:
   ```bash
   npm install
   ```
2. Start the development server:
   ```bash
   npm start
   ```

### Production Build & Deployment
To build the application for production:
```bash
npm run build
```
This will compile the application into the `dist/` directory. The contents of the `dist/` folder can be deployed to any static site hosting service such as **Vercel**, **Netlify**, or **GitHub Pages**.
