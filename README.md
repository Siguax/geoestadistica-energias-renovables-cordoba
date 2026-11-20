# 🌱 Análisis Geoestadístico de Energías Renovables y Comunidades Indígenas en Córdoba, Argentina
> Análisis de patrones espaciales aplicado a la planificación territorial y la evaluación de impacto socioambiental

## 📌 Problema de negocio
La provincia de Córdoba concentra una cantidad creciente de proyectos de energía renovable (solar, eólica, biogás, biomasa, hidrógeno verde). Al mismo tiempo, en su territorio habitan comunidades indígenas registradas oficialmente. La pregunta que guía este análisis es concreta: ¿la distribución de los proyectos energéticos es aleatoria o responde a un patrón espacial? Y, más importante aún, ¿existe una asociación espacial significativa entre esos proyectos y las comunidades indígenas?

Responder estas preguntas permite a organismos de planificación territorial, autoridades ambientales y comunidades identificar si la infraestructura energética se está desarrollando de forma concentrada en territorios con presencia indígena, insumo directo para evaluaciones de impacto socioambiental, procesos de consulta previa y políticas de ordenamiento territorial.

## 🎯 Objetivos

1. Caracterizar la configuración espacial de los proyectos y obras de energía renovable en Córdoba.
2. Evaluar si su distribución responde a un proceso de **Aleatoriedad Espacial Completa (CSR)** o presenta agrupamiento/dispersión a distintas escalas.
3. Analizar la correlación espacial entre los proyectos energéticos y las comunidades indígenas, identificando si existe asociación significativa.

## 🗂️ Fuentes de datos

| Dataset | Fuente | Registros | Variables de interés |
|---|---|---|---|
| Centrales y proyectos de energías renovables | [Secretaría de Energía](http://datos.energia.gob.ar/dataset/centrales-energia-renovables) (actualización 17/04/2023) | 180 | Proyecto, Provincia, Tecnología, Latitud, Longitud, Geometría |
| Comunidades indígenas | INAI — Ministerio de Justicia y DDHH (2021) | 12 | Nombre, Provincia, Latitud (DMS), Longitud (DMS) |

- **Sistema de referencia:** POSGAR 2007 / Argentina zona 4 → reproyectado a **EPSG:5347**.
- La base de comunidades se encontraba en formato **DMS** y fue convertida a grados decimales mediante una función personalizada.

Tecnologías consideradas: Biogás, Biomasa, Biomasa de Residuos Sólidos (BRS), Eólica, Eólica con Almacenamiento, Hidrógeno Verde (HI), Pequeño Aprovechamiento Hidroeléctrico (PAH), Solar y Solar con Almacenamiento.

## 🛠️ Stack tecnológico
- R · sf · spatstat · spdep · tidyverse · dplyr · tmap · nngeo · ggmap · ggplot2 · osmdata · leaflet · units · R Markdown
- Análisis espacial vectorial: sf para manipulación de geometrías, reproyección y recorte espacial.
- Geoestadística de patrones de puntos: spatstat para análisis de primer y segundo orden (K, L, G, F, J, K cruzada, K multimarca).
- Dependencias espaciales: spdep para autocorrelación espacial; nngeo para vecinos más cercanos.
- Visualización: tmap y ggplot2 para mapas estáticos; leaflet para mapas interactivos.
- Reproducibilidad: R Markdown con salida en HTML, análisis documentado de punta a punta.

## 💡 El reporte incluye mapas interactivos y gráficos de funciones K, L, G, F y J con envelopes de simulación de Monte Carlo para evaluar la significancia estadística.

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

## 📊 Principales hallazgos

- **Intensidad global:** ~0.000139 proyectos/km² → patrón **homogéneo**.
- **Test de cuadrantes (Monte Carlo):** p-value > 0.05 → **no se rechaza CSR** (Proceso de Poisson homogéneo).
- **Función K / L:** agrupamiento a **distancias cortas** (recursos e infraestructura compartida) y **dispersión a distancias largas** (evitar saturación regional).
- **Función G:** confirma agregación significativa (vecinos más cercanos de lo esperado por azar).
- **Función K cruzada (comunidades ↔ energía):** la curva observada (Kiso) se ubica **por encima de la línea de Poisson** → **asociación espacial significativa** entre comunidades y proyectos energéticos.

## 💡 Impacto y recomendaciones

- Evaluación de impacto socioambiental: la asociación espacial significativa entre proyectos energéticos y comunidades indígenas es un insumo directo para procesos de consulta previa, evaluación de impacto ambiental y diseño de medidas de mitigación.
- Ordenamiento territorial: los patrones de agrupamiento a corta distancia y dispersión a larga distancia sugieren una lógica de localización que puede orientar políticas de planificación energética.
- Replicabilidad: la metodología es replicable a otras provincias, otras tecnologías y otros tipos de infraestructura (minería, transporte, residuos).
- Transparencia metodológica: el análisis incluye tests estadísticos formales (Monte Carlo, envelopes) que permiten distinguir patrones reales de aleatoriedad, evitando conclusiones espurias.

## 🧠 Competencias demostradas

- Integración de datos geoespaciales heterogéneos: Secretaría de Energía (shapefile), INAI (CSV en DMS), reproyección y recorte espacial.
- Geoestadística avanzada: análisis de primer y segundo orden, funciones K/L/G/F/J, K cruzada, K multimarca, simulación de Monte Carlo.
- Interpretación estadística: evaluación de CSR, identificación de agrupamiento/dispersión, asociación espacial entre patrones.
- Visualización: mapas estáticos e interactivos, gráficos de funciones con envelopes.
- Reproducibilidad: análisis documentado en R Markdown, con código y datos disponibles.
- Traducción de resultados a recomendaciones: de hallazgos estadísticos a insumos para planificación territorial y evaluación de impacto.

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
> ⚠️ spatstat requiere además spatstat.data, spatstat.geom, spatstat.explore, etc. (se instalan automáticamente como dependencias).

## 📁 Estructura del repositorio

```
├── index.html                        # Redirige al reporte (GitHub Pages)
├── Trabajo-Final-Modulo-5.html       # Reporte HTML
├── Trabajo-Final-Modulo-5.rmd        # Documento fuente R Markdown
├── README.md
├── .gitignore
├── LICENSE
├── data/
│   ├── centrales-... .shp / .dbf / .shx / .prj
│   └── Comunidades indígenas, 202102 - Córdoba.csv
└── img/                              # Gráficos exportados (opcional)
```
## ▶️ Ver el análisis completo

👉 [https://siguax.github.io/geoestadistica-energias-renovables-cordoba/](https://siguax.github.io/geoestadistica-energias-renovables-cordoba/)

## ▶️ Cómo reproducir el análisis

1. Descargá o cloná este repositorio:
   ```bash
   git clone https://github.com/Siguax/geoestadistica-energias-renovables-cordoba.git
   ```
2. Para ver los resultados: abrí  **`index.html`** (o `Trabajo-Final-Modulo-5.html`) en cualquier navegador web (Chrome, Firefox, Edge).
   No necesitás instalar R.
3. Para reproducir el análisis, abrí **`Trabajo-Final-Modulo-5.rmd`** en RStudio y ejecutá **Knit**.

## 📝 Notas metodológicas

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
Distribuido bajo la licencia **MIT**. Consultá [`LICENSE`](LICENSE) para más detalles.

## ✉️ Autora
**Silvia Prevettoni**

- 🐙 GitHub: [@Siguax](https://github.com/Siguax)
- 💼 LinkedIn: [silvia-prevettoni](https://www.linkedin.com/in/silvia-prevettoni)
- 📧 Email: [silvia.prevettoni@gmail.com](mailto:silvia.prevettoni@gmail.com)
