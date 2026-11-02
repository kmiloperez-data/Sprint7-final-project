# Sprint7-final-project

# 📞 ConnectaTel: Análisis Exploratorio de Datos y Segmentación de Clientes

Este repositorio contiene el proyecto completo de análisis de datos, saneamiento de calidad de datos y segmentación estratégica para la empresa de telecomunicaciones **ConnectaTel**. A través de la auditoría de calidad de datos, tratamiento estadístico de inconsistencias y unificación transaccional, se identifican los patrones de consumo de los usuarios y se formulan recomendaciones comerciales accionables para optimizar la oferta de planes y maximizar la retención de clientes.

---

## 🎯 Objetivo del Proyecto

- **Analizar el comportamiento de consumo:** Evaluar los patrones de uso de voz (llamadas/minutos) y mensajería de texto por parte de los clientes.
- **Auditar y sanear datos:** Detectar y corregir incoherencias, valores centinela (`-999`, `?`), datos faltantes y fechas fuera de rango cronológico (`2026`).
- **Segmentar la cartera de clientes:** Clasificar a los usuarios según sus niveles de actividad operativa (*Bajo uso*, *Uso medio*, *Alto uso*) y sus rangos demográficos (*Joven*, *Adulto*, *Adulto Mayor*).
- **Generar Insights Ejecutivos (CFI):** Proveer recomendaciones orientadas a stakeholders para optimizar las estrategias de *upselling* e incentivos comercialmente rentables.

---

## 📂 Datasets Utilizados

El análisis procesa tres conjuntos de datos interrelacionados ubicados en la carpeta `/datasets/`:

| Dataset | Descripción | Registros / Columnas Clave |
| :--- | :--- | :--- |
| `plans.csv` | Tarifas y cuotas contractuales de los planes comerciales (*Básico* y *Premium*). | 2 filas × 8 columnas (`plan_name`, `messages_included`, `gb_per_month`, `minutes_included`, cuotas y tarifas adicionales). |
| `users_latam.csv` | Información demográfica y estado contractual de la base de clientes. | 4,000 usuarios × 8 columnas (`user_id`, `age`, `city`, `reg_date`, `plan`, `churn_date`). |
| `usage.csv` | Registros transaccionales de eventos de consumo de la red (llamadas y mensajes). | 40,000 eventos × 6 columnas (`id`, `user_id`, `type`, `date`, `duration`, `length`). |

---

## 📊 Etapas del Análisis

### 1. Carga y Exploración Inicial
- Importación de librerías base de análisis de datos (`pandas`, `numpy`, `matplotlib`, `seaborn`).
- Inspección de estructura mediante `.info()`, `.head()` y `.shape` para diagnosticar volúmenes y tipos de datos preliminares.

### 2. Identificación de Problemas de Calidad de Datos
- **Valores centinela:** Identificación del valor `-999` en `age` (55 registros, 1.38%) y el carácter `?` en `city` (96 registros, 2.4%).
- **Fechas anómalas:** Detección de 40 registros (1.0%) con fechas futuras erróneas correspondientes al año `2026` en la columna `reg_date`.
- **Análisis de nulos (MAR):** Confirmación de que los nulos en `duration` (55.19%) y `length` (44.74%) de la tabla de consumo obedecen a un patrón de ausencia aleatoria (*Missing At Random*), derivado de la exclusión mutua entre eventos de voz y texto.

### 3. Limpieza Básica de Datos
- Imputación de la mediana en la variable `age` para corregir los valores centinela `-999`.
- Sustitución de valores centinela de texto (`?`) por nulos explícitos (`pd.NA`) y estandarización de fechas futuras a `pd.NaT`.
- Preservación de los nulos estructurales (MAR) en llamadas y mensajes para no sesgar los promedios reales de uso.

### 4. Resumen Estadístico por Usuario
- Agregación transaccional agrupada por `user_id` para construir las métricas acumuladas de consumo: `cant_mensajes`, `cant_llamadas` y `cant_minutos_llamada`.
- Unificación con la tabla de usuarios mediante un `left join` e imputación con `.fillna(0)` para caracterizar a los clientes registrados sin actividad transaccional durante el periodo.

### 5. Visualización de Distribuciones y Outliers
- Evaluación de forma y sesgo en las distribuciones mediante histogramas (`sns.histplot`) comparando por tipo de plan (*Básico* vs. *Premium*).
- Inspección de dispersión con boxplots (`sns.boxplot`) e identificación de límites teóricos mediante el Rango Intercuartílico (IQR).
- **Decisión de Negocio:** Retención total de los outliers detectados en consumo, al validar que representan a los usuarios de mayor valor (*heavy users*) y no errores de captura.

### 6. Segmentación de Clientes
- Categorización de uso en la columna `grupo_uso`: *Bajo uso* (<5 llamadas y <5 mensajes), *Uso medio* (<10 llamadas y <10 mensajes) y *Alto uso* (resto de casos).
- Clasificación etaria en la columna `grupo_edad`: *Joven* (<30 años), *Adulto* (<60 años) y *Adulto Mayor* (≥60 años).

### 7. Formulación de Insights Ejecutivos (CFI)
- Síntesis analítica enfocada en stakeholders, destacando oportunidades de *upselling* dirigidas al 74% de clientes en *Uso medio* y programas de retención para el 7% de *Alto uso*.

---

## 📈 Resumen de Hallazgos y Recomendaciones

- **Saneamiento de Base:** Se corrigió un total de 191 registros anómalos entre edades, ciudades y fechas futuras, garantizando la integridad matemática del dataset final.
- **Núcleo Demográfico:** El 50% de los usuarios de ConnectaTel son **Adultos** (30–59 años) y el 30% son **Adultos Mayores** (≥60 años), consolidando una masa cliente madura y estable.
- **Concentración de Uso:** El 74% de la cartera opera en el segmento de **Uso medio** y el 64.9% está suscrito al plan **Básico**, evidenciando un espacio clave para el empaquetamiento e incentivos de actualización de plan.
- **Heavy Users:** El 7% de los usuarios clasificados en **Alto uso** absorbe los picos de minutos y mensajes. Su retención representa una prioridad estratégica mediante ofertas diferenciales en el plan **Premium**.

---

## 🚀 Cómo Ejecutar el Notebook

### Opción A: Abrir directamente en Google Colab
1. Entra a [Google Colab](https://colab.research.google.com/).
2. Ve a la pestaña **GitHub** en la ventana flotante.
3. Ingresa la URL de este repositorio de GitHub.
4. Asegúrate de cargar los archivos CSV dentro de la estructura de carpetas `/datasets/` en el entorno de ejecución o vincular tu Google Drive.

### Opción B: Ejecución Local en Jupyter Notebook
1. Clonar el repositorio localmente:
   ```bash
   git clone https://github.com/tu-usuario/ConnectaTel-Telecom-Analysis.git
   cd ConnectaTel-Telecom-Analysis
   ```
2. Crear y activar un entorno virtual:
   ```bash
   python -m venv venv
   source venv/bin/activate  # En Linux/macOS
   # venv\Scripts\activate   # En Windows
   ```
3. Instalar las dependencias de Python requeridas:
   ```bash
   pip install pandas numpy matplotlib seaborn jupyter
   ```
4. Iniciar el entorno de Jupyter:
   ```bash
   jupyter notebook
   ```

---

## 📋 Guía de Reproducción

Para garantizar la reproducibilidad exacta de los resultados e indicadores:

1. **Estructura de Directorios:** Conserva exactamente el orden de carpetas y archivos:
   ```text
   ConnectaTel-Telecom-Analysis/
   ├── README.md
   ├── notebooks/
   │   └── S7_Proyecto_ConnectaTel.ipynb
   └── datasets/
       ├── plans.csv
       ├── users_latam.csv
       └── usage.csv
   ```
2. **Ejecución Lineal:** Ejecuta las celdas del notebook en orden secuencial (`Run All`) para mantener la trazabilidad de los DataFrames limpios (`users_limpio`, `usage_limpio`, `user_profile`).
3. **Verificación de Resultados:** Compara los conteos de frecuencias, nulos y las distribuciones gráficas con las secciones explicativas descritas en el notebook.
