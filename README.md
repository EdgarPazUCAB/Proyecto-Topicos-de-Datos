# Proyecto 01 — Retail Intelligence (RetailVenture C.A.)

Análisis exploratorio para encontrar oportunidades de rentabilidad y fugas de ingresos de una cadena de retail, a partir de 6 datasets operativos (24 meses: 2023-01-01 a 2024-12-31). El enunciado completo está en `Proyecto_01_Retail_Intelligence.pdf`.

**Estado actual:** A1, A2 y A3 terminadas. Faltan A4 (marketing) y A5 (storytelling, dashboard y presentación).

---

## 1. Estructura de la carpeta

```
Proyecto 01 Analitica de Retail/
├── Proyecto_01_Retail_Intelligence.pdf   ← enunciado
├── *.csv                                 ← 6 datasets ORIGINALES (no se modifican)
├── notebooks/
│   ├── 01_auditoria_calidad.ipynb        ← A1: calidad, limpieza y modelo dimensional
│   ├── 02_rentabilidad.ipynb             ← A2: margen, CLV, estacionalidad y descuentos
│   └── 03_fugas_ingresos.ipynb           ← A3: devoluciones, CRM, churn e impacto a 6 meses
├── data/clean/                           ← datos limpios (los genera el notebook 01)
│   ├── dim_cliente.csv  dim_producto.csv  dim_fecha.csv
│   └── fact_ventas.csv  fact_devoluciones.csv  fact_interacciones.csv  fact_campanas.csv
└── outputs/
    ├── figuras/   ← gráficos PNG para el informe y la presentación (prefijo a1_, a2_, a3_)
    └── tablas/    ← tablas CSV de soporte (mismo prefijo)
```

## 2. Cómo ejecutarlo

**Requisitos:** Python 3.12 o superior y las siguientes librerías (versiones usadas: pandas 3.0, numpy 2.5, matplotlib 3.11, scipy 1.18, statsmodels 0.15, scikit-learn 1.9).

```bash
py -m pip install pandas numpy matplotlib seaborn scipy statsmodels scikit-learn jupyter
```

**Orden de ejecución (importante):** los notebooks dependen uno del otro.

1. `01_auditoria_calidad.ipynb` lee los CSV originales y **genera `data/clean/`**.
2. `02_rentabilidad.ipynb` lee `data/clean/` y genera, entre otras cosas, `outputs/tablas/a2_clv_segmento.csv`.
3. `03_fugas_ingresos.ipynb` lee `data/clean/` y el CLV de A2.

Para abrirlos en el navegador:

```bash
py -m jupyter notebook notebooks
```

Para volver a ejecutar uno completo desde la terminal (por ejemplo, después de cambiar algo en A1):

```bash
cd notebooks && py -m jupyter nbconvert --to notebook --execute --inplace 01_auditoria_calidad.ipynb
```

> Si se cambia el notebook 01, hay que volver a ejecutar el 02 y el 03, porque usan los datos limpios que genera.

Cada notebook sigue la misma lógica: **pregunta del enunciado → código → tabla o gráfico → celda de "Lectura"** con la interpretación y los números clave. Cada notebook termina con un **resumen ejecutivo**.

---

## 3. Qué hace cada notebook

### A1 · Auditoría de calidad (`01_auditoria_calidad.ipynb`)

| Paso | Qué se hace |
|---|---|
| Perfilado | Tipos, nulos explícitos y "disfrazados" (`No especificado`), cardinalidad y dominios |
| Duplicados | Por clave primaria, filas idénticas y duplicados lógicos |
| Outliers | IQR y z-score, revisando si son errores o datos reales |
| Inconsistencias | Integridad referencial (FK) y 20 reglas de negocio (R01–R20), por ejemplo: devolución anterior a la compra, `monto_neto` que no cuadra, conversión sin compra |
| Métricas de calidad | **Completitud, unicidad, consistencia y oportunidad** por dataset |
| Limpieza | Tabla de decisiones justificadas; no se borra ningún registro, los casos dudosos se marcan con columnas `flag_*` |
| Modelo dimensional | **Constelación de 4 hechos** (ventas, devoluciones, interacciones y campañas) con dimensiones conformadas de cliente y fecha |

**Decisiones clave de limpieza:**
- `clientes.fecha_registro` **no es confiable**: el 50% de las compras ocurre antes del registro. Se crea `fecha_alta_efectiva`, la fecha más temprana entre el registro y el primer evento del cliente.
- `campanas.convertido` **no se refleja en las ventas**: el 77,5% de las conversiones no tiene compra en los 30 días siguientes. Se agrega `flag_conversion_verificada` y `monto_compra_30d`.
- Las 233 felicitaciones con estado de resolución pasan a `resolucion = "no_aplica"`.
- **Margen neto (escenario base):** margen bruto − monto devuelto + costo recuperado. Se asume que el producto devuelto vuelve al inventario, salvo que el motivo sea *defectuoso* o *fraude*. También se guarda `margen_neto_pesimista` (no se recupera nada) para el análisis de sensibilidad.

### A2 · Rentabilidad (`02_rentabilidad.ipynb`)

| Pregunta | Método |
|---|---|
| ¿Quién aporta más y menos margen? | Contribución al margen bruto y neto por segmento, categoría, canal, ciudad, edad y subcategoría |
| CLV por segmento | CLV a 3 años en margen neto, con retención 2023→2024 y tasa de descuento del 12%; sensibilidad a la tasa y al horizonte |
| ¿Qué segmentos están en declive? | Descomposición clientes × frecuencia × margen por compra, tendencia mensual (OLS) y cohortes de recompra |
| Estacionalidad | Índices sobre venta **diaria**, Kruskal-Wallis con corrección de Bonferroni, repetibilidad del patrón entre 2023 y 2024, y tendencia |
| ¿Los descuentos compensan? | Tramos de descuento, regresión con efectos fijos de producto y de cliente, margen extensivo y volumen de equilibrio |

### A3 · Fugas de ingresos (`03_fugas_ingresos.ipynb`)

| Pregunta | Método |
|---|---|
| Devoluciones anómalas | χ², regresión logística, prueba binomial por producto con corrección FDR (contra el promedio global y el de su categoría) |
| Devolvedores recurrentes | Prueba binomial por cliente y **simulación de 500 escenarios al azar** |
| ¿Las quejas reducen la compra? | **Diferencias en diferencias** con grupo de control (clientes sin CRM), ANCOVA y ventanas de 180 y 90 días |
| Churn | Backtest de 4 métricas candidatas; se adopta **PI6** (probabilidad de no comprar en 6 meses), validada fuera de tiempo |
| Impacto a 6 meses | Proyección de margen perdido por fuga, separando la parte **evitable** identificada |

---

## 4. Hallazgos hasta ahora

**Contraste con las premisas del caso (A1):**
- La caída de la conversión a la mitad **sí se confirma** (11% → 5,6%; o 30% → 15% sobre campañas abiertas).
- La caída del ticket del 8% **no se confirma**: baja solo 1,9%.
- Las devoluciones (~13,5%) son altas pero **estables** desde 2023: no aumentaron recientemente.

**Rentabilidad (A2):**
- **Electrónica** es el 44% de las ventas, pero solo el 16% del margen neto (6,3% de margen, 17,7% de devoluciones).
- El canal **online descuenta el doble** que la tienda (7,7% vs. 3,7%). Por eso tiene 3,2 puntos menos de margen, con la misma mezcla de productos.
- **Los descuentos no generan volumen** (efecto de +0,2%, no significativo, con 4 pruebas distintas). Costaron $968K, el 22,5% del margen bruto.
- **CLV:** Premium $1.670, Frecuente $840, Esporádico $236, Nuevo $109 e Inactivo $53. Premium y Frecuente son el 81% del valor de la cartera.
- **No hay estacionalidad ni tendencia**: las ventas son planas en ~$725K por mes.

**Fugas (A3):**
- Las **devoluciones las explica la categoría** (Ropa 21,8%, Electrónica 17,7%, resto ~10%), no el producto, el canal ni el cliente. **No hay devolvedores seriales.**
- **Una queja reduce el gasto ~10%** en los 6 meses siguientes, se resuelva o no.
- **Los clientes de alto valor no se están yendo.** La recencia no predice la fuga, porque los clientes compran a un ritmo constante y aleatorio. La fuga real ($36K en 6 meses) está en clientes de 1–2 compras: el 61% de los Nuevos nunca repite.
- **En 6 meses se perderían ~$554K de margen** (71% del margen neto proyectado). El **98%** se debe a **devoluciones ($300K) y descuentos ($243K)**, y ya hay **$153K evitables** identificados.

**Hilo del relato para A5:** la gerencia cree que el problema es la pérdida de clientes, pero los clientes son leales. El margen se pierde por **qué** se vende (Electrónica y Ropa, con muchas devoluciones), por **cómo** se vende (descuentos sin retorno, sobre todo online) y por clientes nuevos que **no llegan a una segunda compra**.

---

## 5. Supuestos y limitaciones (para la defensa)

- Los datos son **sintéticos**. No reproducen ciclos típicos del retail venezolano (diciembre, regreso a clases), y los clientes compran con un patrón "sin memoria".
- **Margen neto:** depende del supuesto de recuperación del producto devuelto. Se reportan los escenarios pesimista, base y optimista, y las conclusiones no cambian.
- **CLV:** horizonte de 3 años y tasa del 12%. El ranking de segmentos es el mismo con 8–20% y 2–5 años.
- **Causalidad:** los análisis de descuentos y de quejas son **observacionales**, aunque con controles (efectos fijos, grupo de control, ajuste por historia previa). Para confirmar el efecto de una política nueva se recomienda un experimento A/B.
- **Churn:** el ritmo de compra se mide desde 2023-01-01, que es la opción conservadora, y la fuga estructural se calcula por segmento para no confundir regresión a la media con fuga.
- **Proyección a 6 meses:** supone que nada cambia, lo que se justifica porque ventas, devoluciones y descuentos son estables. Los componentes pueden solaparse, así que el total es una cota superior.

## 6. Próximos pasos

- **A4 · Marketing:** conversión por tipo de campaña, canal y segmento; efecto del descuento controlando por segmento; segmento que recibe campañas y no convierte; ROI por tipo de campaña.
- **A5 · Storytelling:** relato con inicio, nudo y desenlace; dashboard o presentación con máximo 10 visualizaciones y títulos tipo insight; 3 o más recomendaciones cuantificadas ("Si hacemos X → Y% en Z = $ monto"); presentación de 15 minutos.
- **Entregables:** informe técnico (PDF), dashboard o storytelling ejecutivo, y presentación para la defensa (PowerPoint).
