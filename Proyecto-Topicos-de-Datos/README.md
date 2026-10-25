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
│   ├── 01_auditoria_calidad.ipynb        ← A1: auditoría de calidad (nulos, duplicados, outliers, inconsistencias), métricas y limpieza
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

**Requisitos:** Python 3.12 o superior. Versiones con las que se validaron los tres notebooks (Python 3.13.5): pandas 2.3.3, numpy 2.3.3, matplotlib 3.10.8, seaborn 0.13.2, scipy 1.18.1, statsmodels 0.15.0, scikit-learn 1.9.1. Están fijadas en `requirements.txt`.

```bash
py -m pip install -r requirements.txt
```

**Orden de ejecución (importante):** los notebooks dependen uno del otro.

1. `01_auditoria_calidad.ipynb` lee los CSV originales y **genera `data/clean/`**.
2. `02_rentabilidad.ipynb` lee `data/clean/` y genera, entre otras cosas, `outputs/tablas/a2_clv_segmento.csv`.
3. `03_fugas_ingresos.ipynb` lee `data/clean/` y el CLV de A2.

Para abrirlos en el navegador (requiere además `py -m pip install notebook`) o directamente en VS Code:

```bash
py -m notebook notebooks
```

Para volver a ejecutar uno completo desde la terminal (por ejemplo, después de cambiar algo en A1):

```bash
cd notebooks && py -m nbconvert --to notebook --execute --inplace 01_auditoria_calidad.ipynb
```

> Si se cambia el notebook 01, hay que volver a ejecutar el 02 y el 03, porque usan los datos limpios que genera.

Cada notebook sigue la misma lógica: **pregunta del enunciado → código → tabla o gráfico → celda de "Lectura"** con la interpretación y los números clave. El código va **comentado paso a paso** (qué se mide y por qué) y **no imprime resultados por consola**: la evidencia se emite como tabla o figura, y las cifras se leen en el texto de las celdas de "Lectura", de modo que la afirmación y el número quedan siempre juntos. A1 cierra con la bitácora de IA, los supuestos y las limitaciones; A2 y A3, con un resumen ejecutivo.

---

## 3. Qué hace cada notebook

### A1 · Auditoría de calidad (`01_auditoria_calidad.ipynb`)

| Paso | Qué se hace |
|---|---|
| Parámetros | Umbrales, SLA y tolerancias centralizados en una sola celda; los CSV originales se leen y **nunca se modifican** |
| §1 Perfilado | Volumen, tipos, nulos explícitos y "disfrazados" (`No especificado`), cardinalidad y **dominios** de las 14 categóricas (0 valores fuera de dominio) |
| §2 Duplicados | Clave primaria, filas idénticas y duplicados lógicos: 0 PK repetidas, 0 filas idénticas y **1 duplicado lógico** (doble envío de campaña) |
| §3 Outliers | Solo en las 7 variables **continuas** (IQR y z-score, un boxplot múltiple); en las acotadas se valida el rango y en conteo/ordinales no aplica. Los 4.138 outliers de monto **cuadran** con precio × cantidad × (1 − descuento) y pesan mucho más en el ingreso que en el número de ventas: se conservan y se marcan |
| §4 Inconsistencias | Integridad referencial (6 FK, 0 huérfanos) y **26 verificaciones de consistencia (R01–R26)**: devoluciones sin transacción, compras anteriores al alta, conversión sin compra, descuento ofrecido vs. aplicado, plazos y dominios. El notebook las clasifica por origen (13 estructurales, 4 temporales, 9 con decisión de negocio) porque el enunciado solo da dos ejemplos, no un catálogo de reglas |
| §5 Métricas de calidad | **Completitud, unicidad, consistencia y oportunidad** (cobertura, latencia y frescura) por dataset, en una sola tabla sobre el dato crudo |
| §6 Limpieza | Tabla de 12 decisiones con su justificación y la verificación que la respalda; no se borra ningún registro (los casos dudosos se marcan con `flag_*`) y se mide el **antes/después** con las mismas verificaciones |
| §7 Exportación | 7 tablas en `data/clean/` y `assert` de volumen, claves, integridad referencial y reconciliación de montos, releídos desde disco |
| §8–§9 Bitácora y límites | Uso de IA (12 consultas: aceptadas, ajustadas y rechazadas con su validación), supuestos y limitaciones |

> El **modelo relacional / dimensional** se entrega como diagrama aparte (hecho a mano): no se genera en el notebook.

**Decisiones clave de limpieza:**
- `clientes.fecha_registro` **no es confiable**: el 50% de las compras ocurre antes del registro. Se crea `fecha_alta_efectiva`, la fecha más temprana entre el registro y el primer evento del cliente.
- `campanas.convertido` **no se refleja en las ventas**: el 77,5% de las conversiones no tiene compra en los 30 días siguientes. Se agrega `flag_conversion_verificada` y `monto_compra_30d`. Además (verificación R21), el descuento ofrecido por la campaña coincide con el aplicado en solo el 7,3 % de las conversiones verificables, por lo que el costo del descuento en el ROI de A4 debe calcularse con el descuento *ofrecido* y declararse como supuesto.
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
| ¿Las quejas reducen la compra? | **Diferencias en diferencias** con **dos controles** (clientes sin CRM y clientes que solo hicieron una **consulta o soporte**), ANCOVA y ventanas de 180 y 90 días |
| Churn | Backtest de 4 métricas candidatas; se adopta **PI6** (probabilidad de no comprar en 6 meses), validada fuera de tiempo |
| Impacto a 6 meses | Proyección de margen perdido por fuga, separando la parte **evitable** identificada |

---

## 4. Hallazgos hasta ahora

**Premisas del caso (verificadas sobre `data/clean/`; se retoman en A4):**
- La caída de la conversión a la mitad **sí se confirma** (11% → 5,6%; o 30% → 15% sobre campañas abiertas).
- La caída del ticket del 8% **no se confirma**: baja solo 1,9%.
- Las devoluciones (~13,5%) son altas pero **estables** desde 2023: no aumentaron recientemente.

**Rentabilidad (A2):**
- **Electrónica** es el 44% de las ventas, pero solo el 16% del margen neto (6,3% de margen, 17,7% de devoluciones).
- El canal **online descuenta el doble** que la tienda (7,7% vs. 3,7%). Por eso tiene 3,2 puntos menos de margen, con la misma mezcla de productos.
- **Los descuentos no generan volumen** (efecto de +0,2%, no significativo, con 4 pruebas distintas). Costaron $968K, el 22,5% del margen bruto; por encima del 20% de descuento Electrónica vende a pérdida en promedio y 633 de las 711 transacciones bajo costo son suyas.
- **CLV:** Premium $1.670, Frecuente $840, Esporádico $236, Nuevo $109 e Inactivo $53. Premium y Frecuente son el 80,5% del valor de la cartera.
- **No hay estacionalidad ni tendencia**: las ventas son planas en ~$725K por mes.

**Fugas (A3):**
- Las **devoluciones las explica la categoría** (Ropa 21,8%, Electrónica 17,7%, resto ~10%), no el producto, el canal ni el cliente. **No hay devolvedores seriales.**
- **Una queja reduce el gasto ~10%** en los 6 meses siguientes, se resuelva o no, pero **no la frecuencia** de compra. El **control de contacto** muestra que las **consultas** caen igual: el efecto se asocia a *contactar* (selección), no a la queja ni a la resolución.
- **Los clientes de alto valor no se están yendo.** La recencia no predice la fuga, porque los clientes compran a un ritmo constante y aleatorio. La fuga real ($36K en 6 meses) está en clientes de 1–2 compras: el 61% de los Nuevos nunca repite.
- **En 6 meses se perderían ~$554K de margen** (71% del margen neto proyectado). El **98%** se debe a **devoluciones ($300K) y descuentos ($243K)**, y ya hay **$153K evitables** identificados.

**Hilo del relato para A5:** la gerencia cree que el problema es la pérdida de clientes, pero los clientes son leales. El margen se pierde por **qué** se vende (Electrónica y Ropa, con muchas devoluciones), por **cómo** se vende (descuentos sin retorno, sobre todo online) y por clientes nuevos que **no llegan a una segunda compra**.

---

## 5. Supuestos y limitaciones (para la defensa)

- **Calidad antes/después:** la limpieza lleva la consistencia de clientes, transacciones y CRM al 100 % porque las verificaciones de fecha se evalúan contra `fecha_alta_efectiva`. En campañas **no** llega al 100 % (91,1 %): R19 y R21 siguen fallando por diseño, ya que `convertido` y `descuento_ofrecido` no se alteran (limitación del dato, no se maquilla).
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
