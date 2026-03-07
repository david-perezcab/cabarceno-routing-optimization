# Cabárceno Routing Optimization: End-to-End TSP Solver

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![OR-Tools](https://img.shields.io/badge/Google_OR--Tools-Optimization-orange.svg)
![OSMnx](https://img.shields.io/badge/OSMnx-Graph_Theory-brightgreen.svg)
![Streamlit](https://img.shields.io/badge/Streamlit-Web_App-red.svg)

## El Problema

El [Parque de la Naturaleza de Cabárceno](https://parquedecabarceno.com/) (Cantabria, España) abarca 750 hectáreas y cuenta con más de 20 km de carreteras internas. Los visitantes se desplazan en sus propios vehículos entre los recintos de los animales, lo que genera rutas ineficientes, atascos y pérdida de tiempo. 

Este proyecto resuelve el problema construyendo un **Recomendador de Rutas Óptimas**. Dado un conjunto de animales que un usuario desea visitar, el sistema calcula matemáticamente el recorrido más rápido respetando la topología real de las carreteras del parque y sus sentidos de circulación.



## Arquitectura del Proyecto (Data Lifecycle)

El proyecto está diseñado como un *pipeline* completo de datos, dividiendo el problema en las tres disciplinas clave del ecosistema Data:

### 1. Ingeniería de Datos
* **Extracción Topológica:** En lugar de usar aproximaciones lineales, se extrae el grafo espacial real de las vías transitables del parque directamente desde OpenStreetMap usando la API de Overpass y `osmnx`.
* **Procesamiento:** Limpieza de nodos aislados, corrección de sentidos únicos (One-Way streets) y proyección del grafo a un Sistema de Referencia de Coordenadas (CRS) local para medir distancias métricas reales.

### 2. Ciencia de Datos
* **Matemáticas:** El reto se modela como una variante del **Problema del Viajante (TSP - Traveling Salesperson Problem)** sobre un grafo asimétrico. 
* Se pre-calcula una matriz de distancias exactas entre todos los recintos objetivo utilizando el **Algoritmo de Dijkstra** para encontrar los caminos mínimos en el grafo subyacente.
* **Optimización:** La función objetivo busca minimizar el coste total (distancia/tiempo) del recorrido $x_{ij}$ entre los nodos $i$ y $j$, sujetos a restricciones de ruteo:

$$\min \sum_{i \in V} \sum_{j \in V} c_{ij} x_{ij}$$

* **Solver:** Resolución del sistema de programación lineal entera mixta (MILP) mediante heurísticas de **Google OR-Tools**.

### 3. Análisis de datos y Visualización
* **Frontend:** Despliegue de una interfaz interactiva con `Streamlit`.
* **Mapeo Espacial:** Renderizado dinámico de la ruta óptima calculada sobre un mapa interactivo usando `Folium` y `Leaflet.js`, permitiendo al usuario final (el visitante) seguir las indicaciones visuales precisas.

## ⚙️ Stack Tecnológico

* **Core Matemático/Datos:** `numpy`, `scipy`, `networkx`, `geopandas`
* **Optimización:** `ortools` (Google OR-Tools)
* **Geospatial & Scraped Data:** `osmnx`
* **Visualización & UI:** `streamlit`, `folium`, `streamlit-folium`