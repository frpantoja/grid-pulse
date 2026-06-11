# ⚡ Grid Pulse

> Análisis predictivo de consumo energético en Smart Grids mediante Machine Learning

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)
![TRL](https://img.shields.io/badge/TRL-4%20%E2%80%94%20Validado%20en%20laboratorio-orange?style=flat)
![Metodología](https://img.shields.io/badge/Metodolog%C3%ADa-CRISP--DM-blue?style=flat)
![Dataset](https://img.shields.io/badge/Dataset-Smart%20Meters%20London-green?style=flat)
![Licencia Dataset](https://img.shields.io/badge/Licencia%20Dataset-CC0%20Public%20Domain-lightgrey?style=flat)
![Costo](https://img.shields.io/badge/Stack-100%25%20gratuito-brightgreen?style=flat)

---

## ¿Qué es Grid Pulse?

Grid Pulse es una solución de análisis predictivo de consumo energético en redes eléctricas inteligentes (Smart Grids). Aplica la metodología **CRISP-DM completa** sobre el dataset público *Smart Meters in London* para predecir demanda, detectar anomalías y visualizar patrones de consumo usando técnicas de Machine Learning.

El proyecto demuestra que es posible construir un pipeline de ciencia de datos completo —desde la comprensión del negocio hasta el despliegue— con stack 100% gratuito (Google Colab, Drive, GitHub).

---

## Resultados del Modelo

| Modelo | R² | MAPE | Uso principal |
|---|---|---|---|
| **Random Forest** | **0.66** | **79%** | Predicción de demanda (modelo principal) |
| Prophet | 0.45 | 271% | Captura de tendencias y estacionalidades |
| Isolation Forest | — | — | Detección de anomalías (277.903 detectadas, 5% del total) |

**Hallazgo clave:** el Isolation Forest detectó anomalías concentradas entre las 23:00 y las 04:00 horas, con consumo promedio **2.4× superior** al consumo normal en ese horario.

---

## Dataset

- **Fuente:** [Smart Meters in London — Kaggle](https://www.kaggle.com/datasets/jeanmidev/smart-meters-in-london) (Jean-Claude Cote)
- **Licencia:** CC0 Public Domain
- **Cobertura:** 5.567 hogares del área de Londres, diciembre 2011 – febrero 2014
- **Volumen:** ~1,5 millones de filas por archivo, lecturas cada 30 minutos
- **Nota:** el dataset no se incluye en este repositorio por su tamaño. Ver instrucciones en [`data/README.md`](data/README.md).

---

## Arquitectura del Proyecto

```
Fuente de datos (Kaggle)
        │
        ▼
Google Drive (Data Lake — almacenamiento raw)
        │
        ▼
Google Colab (procesamiento Python)
        │
   ┌────┴────┐
   │  CRISP-DM  │
   └────┬────┘
        │
   ┌────▼──────────────────────────────────┐
   │  1. Comprensión del Negocio           │
   │  2. Comprensión de los Datos (EDA)    │
   │  3. Preparación de Datos              │
   │  4. Modelado (RF + Prophet + IF)      │
   │  5. Evaluación                        │
   │  6. Despliegue (Dashboard Streamlit)  │
   └───────────────────────────────────────┘
        │
        ▼
Dashboard Streamlit (visualización interactiva)
```

---

## Stack Tecnológico

| Categoría | Herramienta |
|---|---|
| Entorno | Google Colab free tier |
| Almacenamiento | Google Drive + GitHub |
| Predicción | scikit-learn (Random Forest) |
| Series temporales | Prophet (Meta) |
| Anomalías | scikit-learn (Isolation Forest) |
| Visualización | matplotlib, seaborn, Streamlit |
| Subsampling | sklearn.utils.resample |

---

## Estructura del Repositorio

```
grid-pulse/
├── notebook/
│   └── grid_pulse_crisp_dm.ipynb   ← Pipeline CRISP-DM completo ejecutado
├── dashboard/
│   └── app.py                      ← Dashboard Streamlit
├── docs/
│   ├── Informe_Grid_Pulse.pdf
│   ├── TRL_Negocios_CLP.pdf
│   └── Gestion_Riesgos.pdf
├── data/
│   └── README.md                   ← Instrucciones para obtener el dataset
├── requirements.txt
└── README.md
```

---

## Cómo Ejecutar

### 1. Clonar el repositorio

```bash
git clone https://github.com/frpantoja/grid-pulse.git
cd grid-pulse
```

### 2. Instalar dependencias

```bash
pip install -r requirements.txt
```

### 3. Obtener el dataset

Seguir las instrucciones en [`data/README.md`](data/README.md) para descargar el archivo `halfhourly_dataset.zip` desde Kaggle y ubicarlo en Google Drive.

### 4. Ejecutar el notebook

Abrir `notebook/grid_pulse_crisp_dm.ipynb` en Google Colab y ejecutar todas las celdas en orden.

### 5. Ejecutar el dashboard

```bash
cd dashboard
streamlit run app.py
```

---

## Madurez Tecnológica (TRL)

Grid Pulse se encuentra actualmente en **TRL 4 — Validación en laboratorio**.

| TRL | Estado | Descripción |
|---|---|---|
| TRL 1 | ✅ Completado | Investigación de técnicas ML para predicción energética |
| TRL 2 | ✅ Completado | Arquitectura Data Lake + selección de modelos |
| TRL 3 | ✅ Completado | Prueba de concepto con datos reales |
| TRL 4 | ✅ Completado | Validación con métricas cuantitativas en laboratorio |
| TRL 5 | ⏳ Pendiente | Dataset completo en Apache Spark / GCP |
| TRL 6 | ⏳ Pendiente | Dashboard en Cloud Run con datos en tiempo real |
| TRL 7–9 | ⏳ Pendiente | Piloto con distribuidora eléctrica real → producción |

Para el roadmap completo con estimación de costos en CLP, ver [`docs/TRL_Negocios_CLP.pdf`](docs/TRL_Negocios_CLP.pdf).

---

## Decisiones Técnicas Clave

**¿Por qué subsampling a 500K registros?**
El dataset completo tiene ~167M registros. Google Colab free tier limita la RAM a ~13GB, lo que hace inviable entrenar Random Forest sobre el dataset completo. Se utilizó `sklearn.utils.resample` con stratify para generar una muestra representativa de 500K registros, manteniendo el conjunto de test completo.

**¿Por qué Prophet tiene MAPE=271%?**
Prophet está optimizado para series temporales con estacionalidades claras a nivel diario o semanal. El dataset contiene lecturas individuales por hogar con alta volatilidad, lo que eleva el error de predicción puntual. Prophet es útil en este proyecto para capturar tendencias y estacionalidades anuales, no para predicción precisa a escala semihourly.

---

## Limitaciones Conocidas

- El dataset de Londres no representa patrones de consumo chilenos (clima, hábitos, tarifas distintas).
- El subsampling introduce un sesgo temporal que puede afectar la generalización del modelo.
- R² = 0.66 del Random Forest no supera el umbral de 0.70 con el dataset parcial.
- No se incluyen variables climáticas externas como features del modelo (mejora propuesta para TRL 5).

---

## Sobre el Proyecto

Desarrollado por **Francisco Pantoja Loyola** como proyecto capstone en **Duoc UC**.

Aplica metodología CRISP-DM completa sobre datos reales del sector energético, demostrando el dominio del proceso end-to-end de ciencia de datos: desde la comprensión del negocio hasta el despliegue de un dashboard interactivo.

---

## Referencias

- Cote, J.-C. (2019). *Smart Meters in London*. Kaggle. https://www.kaggle.com/datasets/jeanmidev/smart-meters-in-london
- Taylor, S. J., & Letham, B. (2018). Forecasting at Scale. *The American Statistician*, 72(1), 37–45.
- Liu, F. T., Ting, K. M., & Zhou, Z. H. (2008). Isolation Forest. *Proceedings of ICDM 2008*. IEEE.
- Pedregosa, F. et al. (2011). Scikit-learn: Machine Learning in Python. *JMLR*, 12, 2825–2830.
- Shearer, C. (2000). The CRISP-DM Model. *Journal of Data Warehousing*, 5(4).
