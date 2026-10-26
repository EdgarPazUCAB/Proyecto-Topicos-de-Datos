# Proyecto 01 — Retail Intelligence (RetailVenture C.A.)

Análisis exploratorio para encontrar oportunidades de rentabilidad y fugas de ingresos de una cadena de retail, a partir de 6 datasets operativos.

---

## 1. Estructura de la carpeta

```
Proyecto-Topicos-de-Datos/
├── Proyecto_01_Retail_Intelligence.pdf   ← enunciado
├── *.csv                                 ← 6 datasets ORIGINALES (no se modifican)
├── notebooks/
│   ├── 01_auditoria_calidad.ipynb        ← A1: auditoría de calidad (nulos, duplicados, outliers, inconsistencias), métricas y limpieza
│   ├── 02_rentabilidad.ipynb             ← A2: margen, CLV, estacionalidad y descuentos
│   ├── 03_fugas_ingresos.ipynb           ← A3: devoluciones, CRM, churn e impacto a 6 meses
│   ├── 04_marketing.ipynb                ← A4: conversión, efecto del descuento, segmento que no convierte y ROI
│   └── 05_recomendaciones.ipynb          ← A5: recomendaciones cuantificadas y figuras nuevas de la presentación
├── data/clean/                           ← datos limpios (los genera el notebook 01)
│   ├── dim_cliente.csv  dim_producto.csv  dim_fecha.csv
│   └── fact_ventas.csv  fact_devoluciones.csv  fact_interacciones.csv  fact_campanas.csv
└── outputs/
    ├── figuras/   ← gráficos PNG para el informe y la presentación (prefijo a1_ … a5_)
    └── tablas/    ← tablas CSV de soporte (mismo prefijo)
```

## 2. Cómo ejecutarlo

**Requisitos:** Python 3.12 o superior. Versiones con las que se validaron los notebooks (Python 3.13.5 y 3.14): pandas 2.3.3, numpy 2.3.3, matplotlib 3.10.8, seaborn 0.13.2, scipy 1.18.1, statsmodels 0.15.0, scikit-learn 1.9.1. Están fijadas en `requirements.txt`.

```bash
py -m pip install -r requirements.txt
```

> En macOS/Linux se usa `python3` en lugar de `py` en todos los comandos. Opcionalmente, para no tocar el Python del sistema, se puede crear antes un entorno virtual: `py -m venv .venv` y activarlo (`.venv\Scripts\activate` en Windows, `source .venv/bin/activate` en macOS/Linux).

**Orden de ejecución (importante):** los notebooks dependen uno del otro. Para regenerar todo desde los CSV originales, en orden (desde la carpeta del proyecto):

```bash
cd notebooks
py -m nbconvert --to notebook --execute --inplace 01_auditoria_calidad.ipynb 02_rentabilidad.ipynb 03_fugas_ingresos.ipynb 04_marketing.ipynb 05_recomendaciones.ipynb
```

`nbconvert` ejecuta los archivos en el orden en que se escriben. Las carpetas `data/clean/` y `outputs/` se crean solas si no existen.

1. `01_auditoria_calidad.ipynb` lee los CSV originales y **genera `data/clean/`**.
2. `02_rentabilidad.ipynb` lee `data/clean/` y genera, entre otras cosas, `outputs/tablas/a2_clv_segmento.csv`.
3. `03_fugas_ingresos.ipynb` lee `data/clean/` y el CLV de A2.
4. `04_marketing.ipynb` lee `data/clean/` (no depende de A2 ni de A3).
5. `05_recomendaciones.ipynb` lee `data/clean/` y el escenario de tope de descuento de A4 (`outputs/tablas/a4_escenario_tope_descuento.csv`).

Para abrirlos en el navegador (requiere además `py -m pip install notebook`) o directamente en VS Code:

```bash
py -m notebook notebooks
```

Para volver a ejecutar uno completo desde la terminal (por ejemplo, después de cambiar algo en A1):

```bash
cd notebooks && py -m nbconvert --to notebook --execute --inplace 01_auditoria_calidad.ipynb
```

> Si se cambia el notebook 01, hay que volver a ejecutar del 02 al 05, porque usan los datos limpios que genera. Si se cambia el 04, hay que volver a ejecutar el 05.

Cada notebook sigue la misma lógica: **pregunta del enunciado → código → tabla o gráfico → celda de "Lectura"** con la interpretación y los números clave. El código va **comentado paso a paso** (qué se mide y por qué) y **no imprime resultados por consola**: la evidencia se emite como tabla o figura, y las cifras se leen en el texto de las celdas de "Lectura", de modo que la afirmación y el número quedan siempre juntos. A1 cierra con la bitácora de IA, los supuestos y las limitaciones; A2, A3 y A4, con un resumen ejecutivo; A5, con el consolidado de recomendaciones.

---
