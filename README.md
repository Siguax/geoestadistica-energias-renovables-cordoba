# 🌱 Análisis Geoestadístico de Energías Renovables y Comunidades Indígenas en Córdoba, Argentina

**Trabajo Final — Módulo 5: Geoestadística**  
Autora: Silvia Prevettoni · Año: 2025

## 📋 Descripción

Este proyecto analiza el **patrón espacial de los centros y proyectos de generación de energía renovable** en la provincia de Córdoba (Argentina), y evalúa su **interacción espacial con las comunidades indígenas** registradas oficialmente.

El análisis se aborda desde la perspectiva de **patrones de puntos**, combinando:

- Propiedades de **primer orden**: intensidad, densidad (kernel) y homogeneidad espacial.
- Propiedades de **segundo orden**: funciones K, L, G, F y J de Ripley, y funciones K cruzadas entre patrones marcados.

## 🎯 Objetivos

1. Caracterizar la configuración espacial de los proyectos y obras de energía renovable en Córdoba.
2. Evaluar si su distribución responde a un proceso de **Aleatoriedad Espacial Completa (CSR)** o presenta agrupamiento/dispersión a distintas escalas.
3. Analizar la **correlación espacial** entre los proyectos energéticos y las comunidades indígenas.

## 🗂️ Fuentes de datos

| Dataset | Fuente | Registros | Variables de interés |
|---|---|---|---|
| Centrales y proyectos de energías renovables | [Secretaría de Energía](http://datos.energia.gob.ar/dataset/centrales-energia-renovables) (actualización 17/04/2023) | 180 | Proyecto, Provincia, Tecnología, Latitud, Longitud, Geometría |
| Comunidades indígenas | INAI — Ministerio de Justicia y DDHH (2021) | 12 | Nombre, Provincia, Latitud (DMS), Longitud (DMS) |

- **Sistema de referencia:** POSGAR 2007 / Argentina zona 4 → reproyectado a **EPSG:5347**.
- La base de comunidades se encontraba en formato **DMS** y fue convertida a grados decimales mediante una función personalizada.

Tecnologías consideradas: Biogás, Biomasa, Biomasa de Residuos Sólidos (BRS), Eólica, Eólica con Almacenamiento, Hidrógeno Verde (HI), Pequeño Aprovechamiento Hidroeléctrico (PAH), Solar y Solar con Almacenamiento.

## 🧪 Enfoque metodológico

### 1. Exploración y preparación
- Filtrado de la base nacional a la provincia de Córdoba.
- Georreferenciación de comunidades indígenas (conversión DMS → decimal).
- Recorte espacial al polígono provincial y transformación a EPSG:5347.
- Eliminación de puntos duplicados mediante `rjitter()`.

### 2. Análisis de primer orden
- **Intensidad** global (puntos/km²).
- **Teselación por cuadrantes** (5×5) y **test de Monte Carlo** para evaluar homogeneidad.
- **Estimación Kernel** de densidad (con correcciones de Diggle y bw.ppl).

### 3. Análisis de segundo orden
- **Función K de Ripley** y su versión centrada **L de Besag**.
- **Función G** (distancia al vecino más cercano).
- **Función F** (espacio vacío).
- **Función J** (combinación de G y F).
- **Envelopes** por simulación de Monte Carlo (nsim = 20).

### 4. Interacción entre patrones
- **Función K cruzada** entre comunidades indígenas y proyectos energéticos.
- **K multimarca** (`alltypes`) para explorar todas las combinaciones.

## 📊 Resultados principales

- **Intensidad global:** ~0.000139 proyectos/km² → patrón **homogéneo**.
- **Test de cuadrantes (Monte Carlo):** p-value > 0.05 → **no se rechaza CSR** (Proceso de Poisson homogéneo).
- **Función K / L:** agrupamiento a **distancias cortas** (recursos e infraestructura compartida) y **dispersión a distancias largas** (evitar saturación regional).
- **Función G:** confirma agregación significativa (vecinos más cercanos de lo esperado por azar).
- **Función K cruzada (comunidades ↔ energía):** la curva observada (Kiso) se ubica **por encima de la línea de Poisson** → **asociación espacial significativa** entre comunidades y proyectos energéticos.

## 🧰 Requisitos

- **R** ≥ 4.1
- **RStudio** (recomendado)

### Paquetes utilizados

```r
install.packages(c(
  "sf", "dplyr", "tidyverse", "spdep", "tmap", "nngeo",
  "jsonlite", "ggmap", "ggplot2", "osmdata", "units",
  "spatstat", "ggpubr", "leaflet", "knitr", "rmarkdown"
))
```

## 📁 Estructura del repositorio

```
├── Trabajo-Final-Modulo-5.html                      # Reporte HTML (GitHub Pages)
├── trabajo Final Modulo 5.Rmd       # Documento fuente R Markdown
├── README.md
├── .gitignore
├── LICENSE
├── data/
│   ├── centrales-... .shp / .dbf / .shx / .prj
│   └── Comunidades indígenas, 202102 - Córdoba.csv
└── img/                            # Gráficos exportados (opcional)
```

## ▶️ Cómo visualizar el proyecto

1. Descargá o cloná este repositorio:
   ```bash
   git clone https://github.com/Siguax/geoestadistica-energias-renovables-cordoba.git
   ```
2. Abrí el archivo **`Trabajo-Final-Modulo-5.html`** en cualquier navegador web (Chrome, Firefox, Edge).
   - No necesitás instalar R para ver los resultados.
3. Si querés reproducir el análisis, abrí el archivo `.Rmd` en RStudio y ejecutá **Knit**.

## 🌐 Ver el reporte en línea

👉 [https://siguax.github.io/geoestadistica-energias-renovables-cordoba/](https://siguax.github.io/geoestadistica-energias-renovables-cordoba/)

## 📝 Notas

- Los puntos duplicados se resolvieron con `rjitter()` (radio = 2 m, semilla 200) para garantizar la validez del patrón de puntos.
- La ventana de observación (`owin`) se construyó a partir del polígono provincial reproyectado, con unidades en metros.
- Los resultados de las funciones K, L, G, F y J se basan en envelopes de 20 simulaciones; para mayor robustez se recomienda aumentarlas a ≥ 99.

## 📚 Referencias

- Baddeley, A., Rubak, E., & Turner, R. (2015). *Spatial Point Patterns: Methodology and Applications with R*. CRC Press.
- Ripley, B. D. (1976). The second-order analysis of stationary point processes. *Journal of Applied Probability*.
- Diggle, P. J. (2013). *Statistical Analysis of Spatial and Spatio-Temporal Point Patterns*. CRC Press.
- Secretaría de Energía de la Nación (2023). *Centrales y proyectos de energías renovables*. [http://datos.energia.gob.ar](http://datos.energia.gob.ar)
- INAI (2021). *Listado oficial de comunidades indígenas*. Ministerio de Justicia y DDHH.

## 📄 Licencia
Este proyecto se distribuye bajo la licencia MIT. Ver LICENSE para más detalles.

## ✉️ Contacto
**Silvia Prevettoni**

- 🐙 GitHub: [@Siguax](https://github.com/Siguax)
- 💼 LinkedIn: [silvia-prevettoni](https://www.linkedin.com/in/silvia-prevettoni)
- 📧 Email: [silvia.prevettoni@gmail.com](mailto:silvia.prevettoni@gmail.com)
