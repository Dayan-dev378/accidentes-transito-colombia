# Análisis de Accidentes de Tránsito en Colombia

**Autor:** Dayan Orley Murillo Quiceno
**Institución:** Corporación Universitaria Uniremington
**Curso:** Big Data & Machine Learning

---

## Descripción del proyecto

Este repositorio contiene el análisis exploratorio de datos (EDA) y un flujo completo de Machine Learning sobre el dataset de **Lesiones en Accidentes de Tránsito** publicado en la plataforma de **Datos Abiertos del Gobierno colombiano** ([datos.gov.co](https://www.datos.gov.co)), administrada por el Ministerio TIC y alimentada por la **Policía Nacional de Colombia – Dirección de Tránsito y Transporte (DITRA)**.

El trabajo replica la metodología del [Diplomado Big Data & Machine Learning – UdeA](https://github.com/diplomado-bigdata-machinelearning-udea), aplicada a un caso de estudio de movilidad y seguridad vial en Colombia.

### Detalle del cohorte y período de análisis

| Aspecto | Detalle |
|---------|---------|
| **Entidad responsable** | Policía Nacional de Colombia – DITRA |
| **Plataforma de publicación** | Datos Abiertos Colombia (Socrata Open Data API) |
| **Tipo de cohorte** | Cohorte abierta de registros administrativos de siniestros viales reportados a la Policía Nacional |
| **Cobertura geográfica** | Nacional – 32 departamentos + Bogotá D.C. y más de 1.100 municipios |
| **Período histórico** | Registros desde **enero de 2010** hasta la actualización más reciente del recurso |
| **Frecuencia de actualización** | Mensual / trimestral (según publicación de DITRA) |
| **Granularidad** | Un registro por víctima lesionada en siniestro de tránsito |
| **Volumen estimado** | Más de 5.000 registros descargados vía API (`$limit=50000`) |
| **Última consulta** | API Socrata en tiempo real al ejecutar el notebook |

### Pregunta de investigación
> ¿Qué factores (tipo de accidente, actor vial, ubicación geográfica, temporalidad) influyen en la ocurrencia y gravedad de los accidentes de tránsito en Colombia, y es posible predecir la gravedad de un siniestro a partir de estas variables?

### Objetivo de Machine Learning
Construir un modelo de **clasificación binaria** que prediga si un siniestro será **grave** (número de lesionados por encima de la mediana) o **no grave**, evaluando distintos algoritmos (Regresión Logística, Árbol de Decisión, Random Forest) con métricas robustas (F1-Score, AUC-ROC, matriz de confusión).

---

## Dataset

| Campo | Detalle |
|-------|---------|
| **Nombre oficial** | LESIONES ACCIDENTES DE TRÁNSITO |
| **Identificador (SODA)** | `ntej-qq7v` |
| **Fuente** | Datos Abiertos Colombia |
| **URL del recurso** | https://www.datos.gov.co/Seguridad-y-Defensa/LESIONES-ACCIDENTES-DE-TR-NSITO/ntej-qq7v/about_data |
| **Endpoint API (CSV)** | `https://www.datos.gov.co/resource/ntej-qq7v.csv?$limit=50000` |
| **Categoría temática** | Seguridad y Defensa |
| **Licencia** | Datos abiertos – uso libre con citación de la fuente |
| **Formato** | CSV / JSON vía API Socrata (SODA 2.1) |

### Variables principales

A continuación se describen las variables utilizadas en el análisis, su tipo, su rol dentro del proyecto y, en el caso de variables categóricas (selección única), las **opciones posibles** que toman.

#### Variables temporales

| Variable | Tipo | Descripción | Ejemplo / rango |
|----------|------|-------------|------------------|
| `fecha` | Fecha (datetime) | Fecha exacta en la que se registró el siniestro vial. Se descompone en año, mes, día de la semana y hora para análisis temporal. | `2010-01-01` → presente |
| `año` (derivada) | Entero | Año de ocurrencia, extraído de `fecha`. | 2010 – 2025 |
| `mes` (derivada) | Entero | Mes del año (1–12). Usado para detectar estacionalidad. | 1 – 12 |
| `dia_sem` (derivada) | Entero | Día de la semana derivado de `fecha` (0 = Lunes, 6 = Domingo). | 0 – 6 |
| `hora` (derivada) | Entero | Hora del día en que ocurrió el siniestro, extraída de `fecha`. | 0 – 23 |

#### Variables categóricas

| Variable | Tipo | Descripción | Opciones (selección única) |
|----------|------|-------------|----------------------------|
| `tipo_accidente` | Categórica nominal | Clasificación del siniestro según la primera maniobra o el modo en que se produjo, conforme a la tipificación que usa la Policía Nacional. | • **Choque** (encuentro violento entre dos o más vehículos en movimiento)<br>• **Atropello** (vehículo contra peatón o ciclista)<br>• **Volcamiento** (el vehículo gira sobre su eje)<br>• **Caída de ocupante** (caída del conductor o pasajero del vehículo en marcha)<br>• **Incendio** (siniestro con combustión)<br>• **Otro** (casos no clasificables en las categorías anteriores) |
| `actor_vial` | Categórica nominal | Tipo de víctima lesionada en el siniestro según su rol en la vía. | • **Peatón**<br>• **Conductor**<br>• **Pasajero**<br>• **Ciclista**<br>• **Motociclista**<br>• **Acompañante** |
| `departamento` | Categórica nominal | Departamento (división político-administrativa nivel 1) donde ocurrió el siniestro. | 32 departamentos de Colombia + **Bogotá D.C.** (ej. Antioquia, Cundinamarca, Valle del Cauca, Atlántico, Santander, Bolívar, …) |
| `municipio` | Categórica nominal | Municipio donde ocurrió el siniestro. | Más de **1.100 municipios** del DANE (ej. Medellín, Bogotá, Cali, Barranquilla, Cartagena, …) |
| `genero` | Categórica nominal | Género de la víctima lesionada en el siniestro. | • **Masculino**<br>• **Femenino**<br>• **No reportado** |
| `rango_edad` | Categórica ordinal | Rango etario de la víctima, agrupado en intervalos definidos por la Policía Nacional. | Intervalos de edad (ej. 0–14, 15–24, 25–34, 35–44, 45–54, 55–64, 65+) |
| `clase_vehiculo` | Categórica nominal | Tipo de vehículo involucrado en el siniestro como causante o parte afectada. | • **Motocicleta**<br>• **Automóvil**<br>• **Bus / Buseta**<br>• **Camión**<br>• **Bicicleta**<br>• **Otro** |
| `estado_victima` | Categórica nominal | Condición física de la víctima al momento del registro del siniestro. | • **Lesionado**<br>• **Muerto**<br>• **Ileso** |
| `zona` | Categórica nominal | Clasificación del área geográfica donde ocurrió el siniestro. | • **Urbana**<br>• **Rural** |
| `gravedad` (derivada) | Categórica binaria (objetivo) | Variable objetivo del modelo de ML. Se construye comparando `numero_lesionados` con la mediana global. | • **0 – No grave** (lesionados ≤ mediana)<br>• **1 – Grave** (lesionados > mediana) |

#### Variables numéricas

| Variable | Tipo | Descripción | Rango típico |
|----------|------|-------------|--------------|
| `cantidad_victimas` | Entero (discreto) | Total de personas afectadas en el siniestro (lesionadas y/o fallecidas). | 1 – 20+ |
| `numero_lesionados` | Entero (discreto) | Número de personas con lesiones reportadas en el siniestro. Distribución tipo Poisson con λ ≈ 2,3. | 0 – 15+ |

---

## Visualización dashboard

El repositorio incluye dos formatos de dashboard que resumen los hallazgos del análisis exploratorio:

### `dashboard.html` — Dashboard interactivo (navegador)
Inspirado en Power BI, se abre directamente en cualquier navegador sin instalación:

- KPIs principales (total de siniestros, lesionados, gravedad promedio, departamentos cubiertos)
- Evolución temporal de siniestros por mes
- Distribución por tipo de accidente y actor vial
- Top 10 departamentos con más accidentes
- Matriz de gravedad por tipo de accidente

> Para abrirlo: descarga `dashboard.html` y ábrelo directamente en cualquier navegador. No requiere instalación.

### `dashboard_powerbi_accidentes.png` — Dashboard estático (generado por el notebook)
El notebook genera automáticamente esta imagen al ejecutar la sección de visualización tipo Power BI. Contiene:

- Encabezado ejecutivo con título del proyecto
- 4 KPIs: total de registros, número de variables, valores nulos y cobertura geográfica
- Gráfico de barras horizontales del top 10 por categoría principal
- Gráfico de torta con participación porcentual
- Histograma con KDE de la variable numérica principal
- Heatmap de correlación entre variables numéricas
- Tabla resumen del dataset

---

## Estructura del repositorio

```
accidentes-transito-colombia/
│
├── accidentes_transito_colombia.ipynb      ← Notebook principal (s01–s09 + dashboard)
├── dashboard.html                          ← Dashboard interactivo tipo Power BI
├── dashboard_powerbi_accidentes.png        ← Dashboard estático generado por el notebook
├── README.md                               ← Este archivo
└── data/                                   ← (opcional) muestra del dataset
```

---

## Contenido del notebook

| Sesión | Tema | Contenido principal |
|--------|------|---------------------|
| **s01** | Entorno Python / Colab | Configuración del entorno; clasificación de variables en temporales, categóricas y numéricas |
| **s02** | NumPy | Simulación Poisson (λ=2.3) de lesionados; estadísticas vectorizadas (media, mediana, std, percentiles); segmentación por gravedad; matriz de correlación simulada |
| **s03** | Pandas | Carga del dataset vía API Socrata (`$limit=50000`); exploración de shape, dtypes, `.describe()`, nulos y cardinalidad de categóricas |
| **s04** | Limpieza & GroupBy | Normalización de columnas; eliminación de duplicados; conversión de fechas y extracción de `anio`, `mes`, `dia_sem`, `hora`; imputación de nulos; Label Encoding + One-Hot Encoding; GroupBy por tipo, geografía y mes |
| **s05** | Visualización | **5.1** Histogramas de variables numéricas · **5.2** Barras top 10 tipo de accidente · **5.3** Tendencia temporal por año y mes · **5.4** Distribución geográfica (departamento/municipio) · **5.5** Heatmap de correlación · **5.6** Pie de actor vial · **5.7** Boxplots para detección de outliers |
| **s06** | Introducción a ML | Construcción de variable objetivo binaria `gravedad` (umbral = mediana); selección de features; split 80/20 estratificado |
| **s07** | Pipeline & PCA | Pipeline `SimpleImputer + StandardScaler`; PCA completo con gráficos de varianza explicada y acumulada; proyección 2D del conjunto de entrenamiento |
| **s08** | Clasificación | Entrenamiento de 4 modelos: Dummy baseline, Logistic Regression, Decision Tree (`max_depth=5`), Random Forest (`n_estimators=100`); comparación de accuracy train vs. test; reglas del árbol; importancia de features |
| **s09** | Evaluación | `classification_report` completo; matrices de confusión; curvas ROC con AUC para todos los modelos; tabla resumen (Accuracy, Precision, Recall, F1, AUC-ROC); selección del modelo final |
| **Dashboard** | Visualización tipo Power BI | KPIs ejecutivos; barras, torta, histograma KDE, heatmap de correlación, tabla resumen; exportación a `dashboard_powerbi_accidentes.png` |


---

## Bibliotecas requeridas

| Biblioteca | Versión mínima recomendada | Uso en el proyecto |
|------------|----------------------------|--------------------|
| `numpy` | ≥ 1.24 | Operaciones vectorizadas, simulación Poisson, PCA |
| `pandas` | ≥ 2.0 | Carga de datos, limpieza, GroupBy, encoding |
| `matplotlib` | ≥ 3.7 | Histogramas, barras, tendencias temporales, boxplots, dashboard PNG |
| `seaborn` | ≥ 0.12 | Heatmaps, distribuciones, gráficos de correlación |
| `scikit-learn` | ≥ 1.3 | Pipeline, PCA, modelos de clasificación, métricas de evaluación |

> Instalación rápida: `pip install numpy pandas matplotlib seaborn scikit-learn`

---

## Cómo ejecutar el notebook

1. **Clonar el repositorio**
   ```bash
   git clone <url-del-repositorio>
   cd accidentes-transito-colombia
   ```

2. **Instalar dependencias**
   ```bash
   pip install numpy pandas matplotlib seaborn scikit-learn
   ```

3. **Abrir el notebook**
   - En **Google Colab**: subir `accidentes_transito_colombia.ipynb` o abrirlo desde GitHub.
   - En **Jupyter local**: ejecutar `jupyter notebook accidentes_transito_colombia.ipynb`.

4. **Ejecutar todas las celdas** en orden (`Kernel → Restart & Run All`).
   - La celda s03 descarga automáticamente los datos desde la API Socrata. Se requiere conexión a internet.
   - La sección de Dashboard genera y guarda `dashboard_powerbi_accidentes.png` en el directorio actual.

---

## Conclusiones

| Sesión | Hallazgo principal |
|--------|-------------------|
| **s01** | Entorno configurado; variables del problema clasificadas en temporales, categóricas y numéricas |
| **s02** | Distribución Poisson del nº de lesionados (λ ≈ 2,3); ~15–20% de accidentes con más de 3 lesionados (graves) |
| **s03** | Dataset con más de 5.000 registros, varias columnas con nulos (<5%), sin estructuras irregulares graves |
| **s04** | Limpieza exitosa (0 nulos finales); OHE para tipo_accidente y actor_vial; GroupBy revela concentración geográfica en departamentos capitales |
| **s05** | Accidentalidad se concentra en ciertos meses del año; el choque es el tipo más frecuente; departamentos con capital grande lideran los registros |
| **s06** | Problema binario definido: **grave (> mediana de lesionados) vs. no grave**; split 80/20 estratificado garantiza representatividad |
| **s07** | Pipeline `Imputer + StandardScaler` normaliza correctamente los datos; ~4–5 componentes principales explican más del 90% de la varianza |
| **s08** | **Random Forest** supera a Logistic Regression y Decision Tree en accuracy; la variable más importante es el tipo de accidente |
| **s09** | La accuracy sola es engañosa con clases desbalanceadas; **Random Forest** obtiene el mejor F1-Score y AUC-ROC → modelo seleccionado para producción |

---

## Referencias

- Datos Abiertos Colombia. (s.f.). *Lesiones en accidentes de tránsito* [Conjunto de datos]. https://www.datos.gov.co/Seguridad-y-Defensa/LESIONES-ACCIDENTES-DE-TR-NSITO/ntej-qq7v/about_data
- Policía Nacional de Colombia. (s.f.). *Lesiones en accidentes de tránsito*. https://www.policia.gov.co/delitos-de-impacto/lesiones-en-accidentes-de-tránsito
- Agencia Nacional de Seguridad Vial (ANSV). (s.f.). *Observatorio Nacional de Seguridad Vial*. https://ansv.gov.co/observatorio/estadísticas
- Diplomado Big Data & Machine Learning – UdeA. https://github.com/diplomado-bigdata-machinelearning-udea