# 🚦 Análisis de Accidentes de Tránsito en Colombia

**Autor:** Dayan Orley Murillo Quiceno  
**Institución:** Corporación Universitaria Uniremington  
**Curso:** Big Data & Machine Learning  

---

## 📋 Descripción del proyecto

Este repositorio contiene el análisis exploratorio de datos (EDA) sobre el dataset de **Lesiones en Accidentes de Tránsito** publicado en la plataforma de Datos Abiertos del Gobierno colombiano.

El trabajo replica la metodología del [Diplomado Big Data & Machine Learning – UdeA](https://github.com/diplomado-bigdata-machinelearning-udea), aplicada a un caso de estudio de movilidad y seguridad vial en Colombia.

### Pregunta de investigación
> ¿Qué factores influyen en la ocurrencia y gravedad de los accidentes de tránsito en Colombia?

---

## 📦 Dataset

| Campo | Detalle |
|-------|---------|
| **Nombre** | Lesiones en Accidentes de Tránsito |
| **Fuente** | Datos Abiertos Colombia |
| **URL** | https://www.datos.gov.co/Seguridad-y-Defensa/LESIONES-ACCIDENTES-DE-TR-NSITO/ntej-qq7v/about_data |
| **Registros** | > 5.000 |
| **Formato** | CSV (API Socrata) |

### Variables principales
- `tipo_accidente` – Clase de siniestro vial
- `fecha` – Fecha del evento
- `departamento` / `municipio` – Ubicación
- `actor_vial` – Tipo de víctima
- `numero_lesionados` – Variable objetivo

---

## 📁 Estructura del repositorio

```
📂 accidentes-transito-colombia/
│
├── 📓 accidentes_transito_colombia.ipynb   ← Notebook principal (EDA completo)
├── 📄 README.md                            ← Este archivo
└── 📂 data/                                ← (opcional) muestra del dataset
```

---

## 🚀 Cómo ejecutar en Google Colab

1. Abre Google Colab: https://colab.research.google.com  
2. Ve a **Archivo → Abrir cuaderno → GitHub**  
3. Pega la URL de este repositorio  
4. Selecciona `accidentes_transito_colombia.ipynb`  
5. Ejecuta todas las celdas con **Runtime → Run all**

O usa este botón directo (reemplaza `TU_USUARIO` con tu usuario de GitHub):

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/TU_USUARIO/accidentes-transito-colombia/blob/main/accidentes_transito_colombia.ipynb)

---

## 📊 Contenido del notebook

| Sección | Descripción |
|---------|-------------|
| **1. Librerías** | pandas, numpy, matplotlib, seaborn |
| **2. Carga de datos** | API directa o Google Drive |
| **3. Exploración inicial** | Shape, dtypes, estadísticas descriptivas |
| **4. Limpieza** | Nulos, duplicados, tipos de datos, fechas |
| **5. EDA y visualizaciones** | Histogramas, barras, líneas, pie, heatmap, boxplots |
| **6. Conclusiones** | Hallazgos y próximos pasos |

---

## 🛠️ Tecnologías

- Python 3.10+
- Jupyter Notebook / Google Colab
- pandas · numpy · matplotlib · seaborn

---

## 📚 Referencias

- Datos Abiertos Colombia. (s.f.). *Lesiones en accidentes de tránsito*. https://www.datos.gov.co
- Diplomado Big Data & Machine Learning – UdeA. https://github.com/diplomado-bigdata-machinelearning-udea
