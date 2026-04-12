# Cabárceno Routing Optimization: End-to-End TSP Solver

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![OR-Tools](https://img.shields.io/badge/Google_OR--Tools-Optimization-orange.svg)
![OSMnx](https://img.shields.io/badge/OSMnx-Graph_Theory-brightgreen.svg)
![Streamlit](https://img.shields.io/badge/Streamlit-Web_App-red.svg)

## The Problem

The [Parque de la Naturaleza de Cabárceno](https://parquedecabarceno.com/) (Cantabria, Spain) spans over 750 hectares and features more than 20 km of internal roads. Visitors drive their own vehicles between animal enclosures, often leading to inefficient routes, traffic jams, and wasted time.

This project solves the problem by building an **Optimal Route Recommender**. Given the animal enclosures a visitor wants to see, the system mathematically calculates the absolute shortest path while strictly respecting the park's actual road topology, one-way streets, and exact parking lot locations.

## Project Architecture (Data Lifecycle)

The project is designed as a complete data pipeline, dividing the problem into three key data disciplines:

### 1. Data Engineering (`01_extraction.ipynb`)

* **Topological Extraction:** Instead of using linear approximations, the real spatial graph of the park's drivable roads is extracted directly from OpenStreetMap using the Overpass API and `osmnx`.
* **Processing:** Cleaning isolated nodes, enforcing one-way street directions, and projecting the graph to a local Coordinate Reference System (CRS) (UTM) to measure real metric distances.

### 2. Data Science & Optimization (`02_optimization.ipynb`)

* **Vectorized Geospatial Joins:** We use `geopandas` spatial joins (`sjoin`, `buffer`) to map animal enclosures to all nearby candidate parking lots within 50 meters, eliminating slow Python loops.
* **Mathematical Modeling:** The challenge is modeled as a **Generalized Traveling Salesperson Problem (GTSP)** on an asymmetrical graph. Because visitors can park in multiple spots for the same enclosure, the C++ solver dynamically picks the single best parking lot per animal using OR-Tools' `AddDisjunction` penalty constraint.
* **Dijkstra's Algorithm:** Pre-computing the exact distance matrix between all target parking candidates using the underlying road graph.
* **MILP Solver:** The Mixed Integer Linear Programming system is solved using the Guided Local Search metaheuristic from **Google OR-Tools**.

### 3. Analysis & Benchmarking (`03_analysis.ipynb`)

* **Dynamic Programming (Viterbi Algorithm):** A critical benchmarking comparison between our algorithmic Optimal Route and the park's official Recommended Route (from their paper map). By structurally mapping the official sequence (1 to 32) and applying Viterbi to select the cheapest parking combinations for that rigid chronological layout, we accurately demonstrate the exact driving distance saved by using our OR-Tools solution vs. the manual brochure.

### 4. Data Visualization

* **Spatial Mapping:** Dynamic rendering of the calculated optimal route on an interactive map using `Folium` and `Leaflet.js`. This allows end-users to follow precise visual directions, and automatically groups and clusters enclosures to prevent marker overlap on the UI.
* *(Upcoming/Planned)* **Frontend:** Deployment of an interactive UI with `Streamlit`.

## Tech Stack

* **Core Math/Data:** `numpy`, `networkx`, `geopandas`, `shapely`
* **Optimization:** `ortools` (Google OR-Tools)
* **Geospatial & Scraped Data:** `osmnx`, `pyproj`
* **Visualization & UI:** `folium`, `streamlit`# Cabárceno Routing Optimization: End-to-End TSP Solver
