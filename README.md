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
| `fecha` | Fecha (datetime) | Fecha exacta en la que se registró el siniestro vial. Se descompone en año, mes, día de la semana y trimestre para análisis temporal. | `2010-01-01` → presente |
| `año` (derivada) | Entero | Año de ocurrencia, extraído de `fecha`. | 2010 – 2025 |
| `mes` (derivada) | Entero | Mes del año (1–12). Usado para detectar estacionalidad. | 1 – 12 |
| `dia_semana` (derivada) | Categórica | Día de la semana derivado de `fecha`. | Lunes, Martes, …, Domingo |

#### Variables categóricas

| Variable | Tipo | Descripción | Opciones (selección única) |
|----------|------|-------------|----------------------------|
| `tipo_accidente` | Categórica nominal | Clasificación del siniestro según la primera maniobra o el modo en que se produjo, conforme a la tipificación que usa la Policía Nacional. | • **Choque** (encuentro violento entre dos o más vehículos en movimiento)<br>• **Atropello** (vehículo contra peatón o ciclista)<br>• **Volcamiento** (el vehículo gira sobre su eje)<br>• **Caída de ocupante** (caída del conductor o pasajero del vehículo en marcha)<br>• **Incendio** (siniestro con combustión)<br>• **Otro** (casos no clasificables en las categorías anteriores) |
| `actor_vial` | Categórica nominal | Tipo de víctima lesionada en el siniestro según su rol en la vía. | • **Peatón**<br>• **Conductor**<br>• **Pasajero**<br>• **Ciclista**<br>• **Motociclista**<br>• **Acompañante** |
| `departamento` | Categórica nominal | Departamento (división político-administrativa nivel 1) donde ocurrió el siniestro. | 32 departamentos de Colombia + **Bogotá D.C.** (ej. Antioquia, Cundinamarca, Valle del Cauca, Atlántico, Santander, Bolívar, …) |
| `municipio` | Categórica nominal | Municipio donde ocurrió el siniestro. | Más de **1.100 municipios** del DANE (ej. Medellín, Bogotá, Cali, Barranquilla, Cartagena, …) |
| `gravedad` (derivada) | Categórica binaria (objetivo) | Variable objetivo del modelo de ML. Se construye comparando `numero_lesionados` con la mediana global. | • **0 – No grave** (lesionados ≤ mediana)<br>• **1 – Grave** (lesionados > mediana) |

#### Variables numéricas

| Variable | Tipo | Descripción | Rango típico |
|----------|------|-------------|--------------|
| `cantidad_victimas` | Entero (discreto) | Total de personas afectadas en el siniestro (lesionadas y/o fallecidas). | 1 – 20+ |
| `numero_lesionados` | Entero (discreto) | Número de personas con lesiones reportadas en el siniestro. Distribución tipo Poisson con λ ≈ 2,3. | 0 – 15+ |

---

## Visualización dashboard

El repositorio incluye un **dashboard interactivo** (`dashboard.html`) inspirado en Power BI, que resume los hallazgos principales del análisis exploratorio en una sola vista:

- KPIs principales (total de siniestros, lesionados, gravedad promedio, departamentos cubiertos)
- Evolución temporal de siniestros por mes
- Distribución por tipo de accidente y actor vial
- Top 10 departamentos con más accidentes
- Matriz de gravedad por tipo de accidente

> Para abrirlo: descarga `dashboard.html` y ábrelo directamente en cualquier navegador. No requiere instalación.

---

## Estructura del repositorio

```
accidentes-transito-colombia/
│
├── accidentes_transito_colombia.ipynb   ← Notebook principal (s01–s09)
├── dashboard.html                       ← Dashboard interactivo tipo Power BI
├── README.md                            ← Este archivo
└── data/                                ← (opcional) muestra del dataset
```

---

## Contenido del notebook

| Sesión | Tema | Contenido principal |
|--------|------|---------------------|
| **s01** | Entorno Python / Colab | Configuración, clasificación de variables |
| **s02** | NumPy | Simulación Poisson de lesionados, estadísticas vectorizadas |
| **s03** | Pandas | Carga vía API Socrata, exploración inicial, dtypes |
| **s04** | Limpieza & GroupBy | Tratamiento de nulos, One-Hot Encoding, agregaciones |
| **s05** | Visualización | Matplotlib / Seaborn — histogramas, barras, heatmaps, boxplots |
| **s06** | Introducción a ML | Definición del problema, split estratificado 80/20 |
| **s07** | Pipeline & PCA | Imputer + StandardScaler + PCA (~4-5 PCs explican >90% varianza) |
| **s08** | Clasificación | Logistic Regression, Decision Tree, Random Forest |
| **s09** | Evaluación | Accuracy, Precision, Recall, F1, AUC-ROC, matrices de confusión |


---

## Referencias

- Datos Abiertos Colombia. (s.f.). *Lesiones en accidentes de tránsito* [Conjunto de datos]. https://www.datos.gov.co/Seguridad-y-Defensa/LESIONES-ACCIDENTES-DE-TR-NSITO/ntej-qq7v/about_data
- Policía Nacional de Colombia. (s.f.). *Lesiones en accidentes de tránsito*. https://www.policia.gov.co/delitos-de-impacto/lesiones-en-accidentes-de-tránsito
- Agencia Nacional de Seguridad Vial (ANSV). (s.f.). *Observatorio Nacional de Seguridad Vial*. https://ansv.gov.co/observatorio/estadísticas
- Diplomado Big Data & Machine Learning – UdeA. https://github.com/diplomado-bigdata-machinelearning-udea