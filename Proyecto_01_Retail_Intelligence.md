Tópicos Especiales para la Gestión de Datos Prof. Armen Djenanian 



<!-- Start of picture text -->
INFORMATICA 0}<br><!-- End of picture text -->

# **Proyecto 01: Retail Intelligence — Storytelling con Datos para Maximizar la Rentabilidad y Prevenir Fugas de Ingresos** 

## **Objetivo:** 

Desarrollar un análisis exploratorio completo sobre un conjunto de datos reales del sector retail, identificando patrones de comportamiento del consumidor, oportunidades de incremento de rentabilidad y señales tempranas de fuga de ingresos, para construir un storytelling de datos coherente, visualmente impactante y orientado a la toma de decisiones gerenciales. 

## **Alcance:** 

El equipo tiene libertad de elegir las herramientas de análisis y visualización (Python, R, Power BI, Tableau, Excel avanzado, etc.) y el enfoque narrativo del storytelling. Se valorará la profundidad del análisis sobre la cantidad de gráficos, la coherencia del relato de datos y la capacidad de traducir hallazgos técnicos en recomendaciones ejecutivas concretas y cuantificables. 

## **Contexto del Caso:** 

**RetailVenture C.A.** es una cadena de retail con presencia en canales físicos y digitales que opera en Venezuela desde hace 12 años. La empresa enfrenta una presión creciente en sus márgenes: el ticket promedio ha caído un 8% interanual, la tasa de devoluciones aumentó al 14% en el último trimestre y la efectividad de las campañas de marketing ha disminuido de un 22% a un 11% de conversión en 18 meses. La gerencia general ha contratado al equipo para que, a partir de los datos operativos disponibles, construya un relato basado en evidencia que explique qué está ocurriendo, por qué está ocurriendo y qué acciones concretas se deben tomar. 

## **Set de Datos Provistos:** 

El equipo recibirá seis archivos en formato CSV que simulan el entorno de datos de RetailVenture C.A. con 24 meses de historia operativa. Los datos son sintéticos pero semánticamente coherentes con la realidad del sector. El volumen aproximado es de 50,000 transacciones, 8,000 clientes únicos y 320 productos activos. 

**Archivo** ~~<mark>a</mark>~~ 

|**Archivo**<br>|**Descripción**|**Campos clave**|
|---|---|---|
|**`clientes.csv`**<br>|Perfil demográfico y de segmentación de los<br>clientes registrados en la plataforma.|_cliente_id, edad, genero, ciudad,_<br>_segmento, fecha_registro,_<br>_canal_adquisicion_|
|**`transacciones.csv`**|Historial completo de compras: productos,<br>montos, descuentos aplicados y canal de<br>venta.|_transaccion_id, cliente_id, fecha,_<br>_producto_id, cantidad,_|



1 

Tópicos Especiales para la Gestión de Datos Prof. Armen Djenanian 



<!-- Start of picture text -->
INFORMATICA 0}<br><!-- End of picture text -->

|**Archivo**<br>~~a~~|**Descripción**|**Campos clave**|
|---|---|---|
|||_precio_unitario, descuento, canal_<br>_(online/tienda), metodo_pago_|
|**`productos.csv`**|Catálogo de productos con categoría,<br>subcategoría, costo y margen bruto.|_producto_id, nombre, categoria,_<br>_subcategoria, costo, precio_lista,_<br>_margen_bruto_pct_|
|**`devoluciones.csv`**|Registro de devoluciones y motivos<br>reportados por los clientes.|_devolucion_id, transaccion_id,_<br>_cliente_id, motivo, monto_devuelto,_<br>_fecha_devolucion_|
|**`interacciones_crm.csv`**|Eventos de contacto del cliente con la<br>empresa: quejas, consultas y resolución.|_interaccion_id, cliente_id, tipo_<br>_(queja/consulta/soporte), fecha, canal,_<br>_resolucion, tiempo_respuesta_hrs_|
|**`campanas_marketing.csv`**|Campañas enviadas a clientes y si resultaron<br>en una compra (conversión).|_campana_id, cliente_id,_<br>_tipo_campana, fecha_envio,_<br>_canal_campana, abierto, convertido,_<br>_descuento_ofrecido_|



**Nota:** Los datasets estarán disponibles en el aula virtual. 

**Actividades:** 

## **A1. Preparación y Auditoría de Calidad de Datos:** 

- Cargar los seis datasets e identificar valores nulos, duplicados, outliers y registros inconsistentes (ej: devoluciones sin transacción de origen, campañas con fechas posteriores a la compra). 

- Documentar las decisiones de limpieza y transformación tomadas, justificando cada una técnicamente. 

- Construir un modelo relacional conceptual que muestre cómo se relacionan los seis datasets entre sí (diagrama dimensional o esquema estrella). 

- Calcular y reportar las métricas de calidad del dato: completitud, consistencia, unicidad y oportunidad por cada dataset. 

## **A2. Análisis de Rentabilidad y Comportamiento de Compra:** 

- Identificar los segmentos de clientes, categorías de producto y canales de venta con mayor y menor contribución al margen bruto total. 

- Calcular el Customer Lifetime Value (CLV) estimado por segmento y detectar cuáles segmentos están en declive. 

- Analizar la estacionalidad de las ventas: identificar picos, valles y tendencias por categoría de producto y canal. 

2 

Tópicos Especiales para la Gestión de Datos Prof. Armen Djenanian 



<!-- Start of picture text -->
INFORMATICA 0}<br><!-- End of picture text -->

- Evaluar el efecto real de los descuentos sobre el margen: ¿los descuentos generan volumen suficiente para compensar la pérdida de margen? 

## **A3. Detección de Fugas de Ingresos:** 

- Analizar el patrón de devoluciones: ¿existen productos, categorías o segmentos con tasas anómalas? ¿Hay clientes recurrentes en devoluciones? 

- Cruzar los datos de interacciones del CRM con el comportamiento de compra posterior: ¿los clientes que tuvieron quejas no resueltas disminuyeron su frecuencia de compra? 

- Identificar clientes en riesgo de churn (fuga): definir una métrica de inactividad y encontrar el segmento de mayor valor en riesgo. 

- Cuantificar el impacto económico estimado de las fugas identificadas: expresarlo en términos de ingresos perdidos proyectados a 6 meses. 

## **A4. Evaluación de la Efectividad del Marketing:** 

- Calcular la tasa de conversión por tipo de campaña, canal de envío y segmento de cliente para identificar qué combinaciones generan el mayor retorno. 

- Determinar si existe un efecto del descuento ofrecido en la campaña sobre la conversión, controlando por segmento. 

- Identificar el segmento de clientes que recibe campañas pero no convierte, y proponer hipótesis sobre las causas basadas en los datos de CRM y devoluciones. 

- Calcular el ROI estimado de cada tipo de campaña usando el monto de compra convertida versus el costo implícito del descuento ofrecido. 

## **A5. Storytelling Ejecutivo y Recomendaciones:** 

- Construir un relato de datos cohesionado que conecte los hallazgos de las actividades anteriores en una narrativa con inicio (situación actual), nudo (hallazgos clave y patrones detectados) y desenlace (recomendaciones priorizadas). 

- Diseñar un dashboard o presentación ejecutiva con máximo 10 visualizaciones, seleccionadas por su poder explicativo, no por su cantidad. Cada visualización debe tener un título en forma de insight, no de descripción (ej: 'El canal digital genera el 40% del ingreso pero solo el 12% del margen neto'). 

- Cuantificar el impacto potencial de al menos tres recomendaciones: expresar cada una como 'Si hacemos X, esperamos un incremento/reducción de Y% en Z métrica, equivalente a [monto] en ingresos'. 

- Preparar una presentación de 15 minutos ante la gerencia (simulada en el aula) donde el equipo defienda sus hallazgos y recomendaciones ante preguntas de escrutinio técnico y de negocio. 

3 

Tópicos Especiales para la Gestión de Datos Prof. Armen Djenanian 



<!-- Start of picture text -->
INFORMATICA 0}<br><!-- End of picture text -->

**Evaluación:** 

|**Criterio de Evaluación**<br>~~P~~|**Descripción y Alcance**<br>|**%**<br><br>|**Niveles de Logro (0–**<br>**20)**<br><br>~~|~~|
|---|---|---|---|
|**1. Calidad y Auditoría de**<br>**Datos**<br>|Completitud del perfil de calidad por dataset<br>(nulos, duplicados, outliers), justificación<br>técnica de las decisiones de limpieza y<br>construcción del modelo relacional<br>conceptual (ER o esquema estrella).<br>|**20%**<br><br>|**Exc:**Perfil completo,<br>decisiones documentadas,<br>modelo relacional sin<br>errores.<br>**Bien:**Cumple los elementos<br>clave con algún detalle<br>menor.<br>**Reg:**Auditoría parcial o<br>modelo relacional<br>incorrecto.<br>**Def:**Ausencia de auditoría o<br>modelo que no refleja las<br>relaciones reales.<br><br>|
|**2. Profundidad Analítica**|Rigor en el análisis de rentabilidad (CLV,<br>margen por canal/segmento), detección de<br>fugas (devoluciones, churn, impacto CRM) y<br>evaluación de marketing (conversión, ROI).<br>Se penaliza la presentación de correlaciones<br>sin investigar causalidad.|**30%**|**Exc:**Cubre las 5 áreas,<br>cuantifica impactos,<br>distingue correlación de<br>causalidad.<br>**Bien:**Cubre al menos 3<br>áreas con análisis sólido.<br>**Reg:**Análisis superficial o<br>limitado a una sola área.<br>**Def:**No evidencia análisis<br>real; solo estadísticos básicos<br>sin interpretación.|
|**3. Calidad del Storytelling**|Coherencia de la narrativa (inicio-nudo-<br>desenlace), poder explicativo de las<br>visualizaciones (máx. 10), calidad de los<br>títulos como insights accionables y claridad<br>en la cuantificación de recomendaciones ('Si<br>X, entonces Y% en Z').|**25%**|**Exc:**Narrativa fluida,<br>visualizaciones con títulos<br>de insight, 3+<br>recomendaciones<br>cuantificadas.<br>**Bien:**Narración clara con<br>alguna visualización<br>genérica o recomendación<br>sin cuantificar.<br>**Reg:**Historia fragmentada,<br>gráficos sin títulos de<br>insight.<br>**Def:**Sin narrativa coherente;<br>colección de gráficos sin hilo<br>conductor.|
|**4. Defensa Oral y**<br>**Escrutinio**|Capacidad de todos los integrantes de<br>responder preguntas técnicas y de negocio<br>sin depender de las diapositivas. Se evaluará<br>conpreguntas deprofundización sobre|**15%**|**Exc:**Todo el equipo<br>responde con precisión;<br>justifican metodología al ser<br>cuestionados.|



4 

Tópicos Especiales para la Gestión de Datos Prof. Armen Djenanian 



<!-- Start of picture text -->
INFORMATICA 0}<br><!-- End of picture text -->

||decisiones metodológicas y validez de los<br>hallazgos.||**Bien:**Mayoría del equipo<br>responde; algún integrante<br>depende de las diapositivas.<br>**Reg:**Respuestas<br>incompletas o solo 1-2<br>integrantes dominan el<br>material.<br>**Def:**El equipo no puede<br>defender los hallazgos ni<br>explicar las decisiones<br>tomadas.|
|---|---|---|---|
|**5. Bitácora de IA y Criterio**<br>**Técnico**<br>|Si se usaron herramientas de IA generativa:<br>registro de prompts con validación técnica de<br>resultados, indicando qué fue aceptado,<br>ajustado o rechazado y por qué. Se valora el<br>juicio crítico sobre la IA, no la aceptación<br>ciega.<br>|**10%**<br><br>|**Exc:**Bitácora completa con<br>validación técnica de cada<br>sugerencia de IA aceptada o<br>rechazada.<br>**Bien:**Bitácora presente con<br>validación parcial.<br>**Reg:**Lista de prompts sin<br>evidencia de validación<br>crítica.<br>**Def:**Sin bitácora o evidencia<br>de vibe coding sin<br>comprensión del código<br>generado.<br><br>|
|**TOTAL**<br>~~P~~|Suma de todos los criterios ponderados. Nota<br>mínima aprobatoria: 10/20.<br>|**100%**<br><br>|Fecha de entrega y<br>defensa: Semana 15.<br><br>~~|~~|



5 

