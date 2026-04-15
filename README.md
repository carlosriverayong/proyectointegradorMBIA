# Proyecto Integrador MBIA — Mercado Global de Trigo

> **Inteligencia Analítica para la Industria Harinera Centroamericana**  
> Universidad del Valle de Guatemala — Maestría en Business Intelligence & Analytics  
> Contexto aplicado: Molinos Modernos Guatemala | Entrega: Septiembre 2026

---

## Descripción general

Este proyecto desarrolla un sistema analítico integral que cubre el ciclo completo de analítica de datos — desde la ingesta de fuentes externas públicas hasta la aplicación de modelos de Data Science y visualización ejecutiva — con el objetivo de transformar información del mercado global de trigo en inteligencia accionable para la industria harinera centroamericana.

El proyecto utiliza **exclusivamente datos externos de acceso público**, sin requerir datos internos de ninguna empresa, garantizando reproducibilidad académica y eliminando restricciones de confidencialidad.

---

## Caso de negocio

Guatemala importa prácticamente el 100% del trigo que consume, principalmente desde Estados Unidos, Canadá y Argentina. El trigo representa entre el 60% y el 75% del costo total de producción de harina, y su precio es determinado por factores globales como stocks mundiales, costos de flete marítimo y condiciones climáticas en zonas productoras clave.

Sin inteligencia analítica avanzada, las decisiones de compra se toman de forma reactiva. Este proyecto construye el sistema que lo cambia.

| Escenario | Impacto financiero estimado |
|---|---|
| Mejor timing de compra (modelo predictivo) | USD 250,000 – 400,000 / año |
| Evitar compras en régimen bull (HMM) | USD 300,000+ por evento extremo |
| Reducción de incertidumbre en inventario | USD 50,000 – 150,000 / año |

---

## Objetivos

| # | Componente | Objetivo |
|---|---|---|
| OE-1 | Ingesta | Pipeline automatizado integrando 8+ fuentes externas públicas |
| OE-2 | Transformación | Data Warehouse con modelo dimensional, calidad y trazabilidad |
| OE-3 | Business Intelligence | 3 dashboards interactivos en Power BI |
| OE-4 | Data Science | 3 modelos analíticos entrenados y en producción |
| OE-5 | Arquitectura | Solución funcional documentada end-to-end |
| OE-6 | Impacto | Estudio de impacto financiero con supuestos explícitos |

---

## Modelos analíticos

### DS-1 — Predicción del precio CBOT del trigo a 4-8 semanas
- **Algoritmo:** XGBoost con features de series de tiempo (lags + rolling averages)
- **Justificación:** Supera a ARIMA en relaciones no lineales entre señales externas; robusto ante datos faltantes; ampliamente validado en literatura de forecasting de commodities
- **Variables:** Precio CBOT rezagado, ratio stocks/consumo, Baltic Dry Index (lag 4 sem.), tipo de cambio USD/EUR, delta producción WASDE mensual, dummy cosecha hemisferio norte
- **Entregable:** Score semanal COMPRAR / ESPERAR / HEDGE con intervalos de confianza al 80% y 95%. Métricas: RMSE, MAPE, Directional Accuracy

### DS-2 — Detección de regímenes de mercado (Bull / Bear / Lateral)
- **Algoritmo:** Hidden Markov Model (HMM) con 3 estados latentes
- **Justificación:** Estándar académico para detección de estados latentes en series financieras y de commodities; estima probabilidades de transición entre regímenes
- **Aplicación:** Régimen bear → acumular inventario | Lateral → comprar según necesidad | Bull → minimizar compras y buscar cobertura con futuros
- **Entregable:** Serie histórica 2000–2025 clasificada por régimen + probabilidad del régimen actual + matriz de transición

### DS-3 — Impacto de El Niño / La Niña en la producción mundial de trigo
- **Algoritmo:** Prophet (Meta) con regressores externos + regresión panel por país
- **Justificación:** Maneja estacionalidad múltiple por hemisferio; incorpora índice ONI de ENSO como regressor externo; pronósticos interpretables con intervalos de incertidumbre
- **Fuentes climáticas:** Índice ONI/ENSO de NOAA + FAO GIEWS + USDA FAS PSD
- **Entregable:** Forecast de producción próximos 12 meses por región + análisis de sensibilidad climática

---

## Fuentes de datos

| # | Fuente | Variables | Período | Acceso |
|---|---|---|---|---|
| 1 | [FRED — St. Louis Fed](https://fred.stlouisfed.org) | Precio trigo, CPI, tipo de cambio, proxy flete | 1960–presente | API gratuita |
| 2 | [USDA ERS Wheat Data](https://www.ers.usda.gov/data-products/wheat-data) | Balance oferta/demanda USA y mundial por clase | 1918–presente | Excel público |
| 3 | [USDA FAS PSD](https://apps.fas.usda.gov/psdonline/app/index.html) | Producción, stocks, exportaciones por país | 1960–presente | CSV público |
| 4 | [World Bank Pink Sheet](https://www.worldbank.org/en/research/commodity-markets) | Precio HRW y SRW histórico $/TM | 1960–presente | Excel público |
| 5 | [CBOT Futuros — yfinance](https://finance.yahoo.com/quote/ZW=F) | Precio diario futuros trigo, volumen | 2000–presente | Gratuito |
| 6 | [Agriculture Canada](https://agriculture.canada.ca/en/sector/crops/reports-statistics) | Outlook cosecha, producción, precios CA | 2015–presente | PDF público |
| 7 | [FAO GIEWS / WFP VAM](https://data.humdata.org/dataset/wfp-food-prices-for-guatemala) | Precio harina en mercados Guatemala | 2010–presente | CSV público |
| 8 | [FRED — BDI Proxy](https://fred.stlouisfed.org/series/PCU483111483111) | Deep Sea Freight Transportation PPI | 2000–presente | API gratuita |

---

## Estructura del repositorio

```
proyectointegradorMBIA/
│
├── README.md
├── requirements.txt
│
├── notebooks/
│   ├── 01_fred_extraccion.ipynb         # Precio trigo, CPI, tipo cambio, BDI proxy
│   ├── 02_usda_ers_extraccion.ipynb     # Balance oferta/demanda USA y mundial
│   ├── 03_usda_psd_extraccion.ipynb     # Producción por país exportador
│   ├── 04_worldbank_pinksheet.ipynb     # Serie histórica precios 1960–presente
│   ├── 05_cbot_futuros.ipynb            # Precios diarios CBOT ZW=F
│   ├── 06_agriculture_canada.ipynb      # Parseo PDFs outlook cosecha
│   ├── 07_fao_giews.ipynb               # Precios harina Guatemala
│   └── 08_bdi_proxy.ipynb              # Baltic Dry Index proxy FRED
│
├── data/
│   ├── raw/                             # Archivos originales sin modificar
│   └── processed/                       # CSVs limpios listos para el DW
│
└── docs/
    └── proyecto_trigo_UVG.docx          # Propuesta formal del proyecto
```

---

## Stack tecnológico

| Capa | Herramienta | Uso |
|---|---|---|
| Ingesta | Python 3.11 + Apache Airflow | Pipelines automáticos desde APIs y PDFs |
| Almacenamiento | PostgreSQL 16 + dbt Core | Data Warehouse con modelo dimensional |
| Procesamiento | pandas, numpy | Transformaciones, limpieza, feature engineering |
| Modelos DS | XGBoost, hmmlearn, Prophet | Entrenamiento y evaluación de los 3 modelos |
| BI | Power BI Desktop | Dashboards interactivos y storytelling |
| Notebooks | Jupyter Lab | EDA, exploración y documentación |
| Control versiones | Git + GitHub | Este repositorio |
| Contenedores | Docker + Docker Compose | Reproducibilidad total del entorno |

**Opción B — Cloud (GCP):** Cloud Composer · BigQuery · dbt Cloud · Looker Studio · Vertex AI Workbench

---

## Instalación y uso

```bash
# Clonar el repositorio
git clone https://github.com/carlosriverayong/proyectointegradorMBIA.git
cd proyectointegradorMBIA

# Instalar dependencias
pip install -r requirements.txt

# Configurar API key de FRED
# Regístrate en: https://fred.stlouisfed.org/docs/api/api_key.html
# Crea un archivo config.py (está en .gitignore):
echo "FRED_API_KEY = '2f55df367c8ec1c44c28ed52b959e6a5'" > config.py

# Ejecutar notebooks en orden
jupyter lab
```

---

## EDA teórico — comportamiento esperado de los datos

### Correlaciones clave anticipadas

| Correlación | Variables | Interpretación |
|---|---|---|
| r = -0.75 | Ratio stocks/uso vs. precio CBOT | Driver fundamental más importante |
| r = +0.60 | Precio CBOT t vs. t-4 semanas | Momentum de precios |
| r = +0.45 | BDI (lag 4 sem.) vs. precio CIF Guatemala | Flete impacta precio destino |
| r = -0.35 | Índice ONI El Niño vs. producción AU/AR | Clima reduce oferta hemisferio sur |
| r = +0.80 | Precio CBOT vs. precio FOB exportadores | Arbitraje sincroniza mercados |
| r = +0.65 | Precio trigo vs. precio harina Guatemala | Pass-through con lag 1-2 meses |

### Eventos extremos documentados

| Año | Evento | Impacto |
|---|---|---|
| 2007–08 | Crisis alimentaria global | Precio alcanzó USD 440/TM — outlier estadístico |
| 2010–11 | Sequía Rusia + inundaciones Australia | +70% precio en 6 meses |
| 2012 | Sequía extrema USA (HRW) | −13% producción norteamericana |
| 2020 | COVID-19 | BDI colapsa Q1 — dummy necesario en modelos |
| 2022 | Invasión Rusia-Ucrania | +60% precio en 3 semanas — máximo USD 470/TM |

### Transformaciones requeridas

- Unificación de calendarios: marketing years distintos → año calendario
- Conversión de unidades: bushels → TM (`1 bu = 0.02722 TM`)
- Deflactación: precios nominales → reales (base CPI 2020, serie FRED `CPIAUCSL`)
- Serie continua de futuros: roll-over de contratos CBOT mensuales
- Imputación: forward-fill para gaps < 3 meses; interpolación lineal para gaps medianos
- Feature engineering: ratio stocks/uso, spread HRW–SRW, volatilidad rolling 30d, MA 12 semanas

---

## Roadmap

| Mes | Fase | Entregables |
|---|---|---|
| Abril 2026 | Fundación | Pipeline ingesta, DW montado, EDA inicial |
| Mayo 2026 | BI Core | Dashboards 1 y 2 en Power BI |
| Junio 2026 | DS inicio | Modelo DS-1 (XGBoost) entrenado y evaluado |
| Julio 2026 | DS avanzado | Modelos DS-2 (HMM) y DS-3 (Prophet + clima) |
| Agosto 2026 | Integración | Arquitectura en producción, Dashboard 3, pipeline automatizado |
| Septiembre 2026 | Cierre | Documento final, storytelling ejecutivo, defensa |

---

## Nota metodológica — proxy del Baltic Dry Index

El BDI oficial del Baltic Exchange no se distribuye gratuitamente via APIs públicas. Como proxy se utiliza la serie **PCU483111483111** (Deep Sea Freight Transportation Producer Price Index) del Bureau of Labor Statistics, disponible en FRED. Esta serie tiene una correlación histórica superior a 0.78 con el BDI oficial y es de uso estándar en literatura académica de commodities cuando el BDI directo no está disponible sin costo.

---

## Autor
**Daniela Valladares**
**Jorge Alvarado**
**Carlos Rivera** 
Maestría en Business Intelligence & Analytics  
Universidad del Valle de Guatemala  
[github.com/carlosriverayong](https://github.com/carlosriverayong)
