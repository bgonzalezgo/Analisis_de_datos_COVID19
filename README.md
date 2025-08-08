# Analisis_de_datos_COVID19
1. Escenario del Problema 🏥
Formas parte del equipo de analistas de datos del Ministerio de Salud. Tu misión es consolidar
los datos sobre la pandemia de COVID-19 que se reportan diariamente desde diferentes
ciudades. La información está fragmentada: los casos diarios, los datos de vacunación y las
características demográficas de cada ciudad se encuentran en archivos separados.
Para poder analizar el impacto de la pandemia y la efectividad de las campañas de
vacunación, es indispensable crear un dataset maestro unificado. Tu objetivo no es solo
realizar este análisis una vez, sino construir un proceso automatizado y robusto que permita
al ministerio integrar nuevos datos de manera rápida y fiable en el futuro.
2. Objetivos de Aprendizaje 🎯
Al completar este taller, serás capaz de:
● Combinar reportes de datos de diferentes regiones en un único dataset (pd.concat).
● Enriquecer datos de salud con información complementaria (vacunación, demografía)
usando pd.merge con claves simples y compuestas.
● Aplicar diferentes tipos de JOIN y justificar su uso en un contexto de salud pública.
● Crear variables calculadas (KPIs) relevantes para el análisis epidemiológico.
● Reestructurar y ordenar datos para analizar la evolución de la pandemia por ciudad y
fecha (sort_values, pivot_table).
● Automatizar un pipeline de procesamiento de datos de salud encapsulando la lógica
en una función reutilizable.
● Validar la consistencia e integridad de un dataset integrado.
● Exportar los resultados para la creación de reportes y análisis posteriores.
3. Materiales Requeridos 📁
Asegúrate de tener en tu directorio de trabajo los siguientes 5 archivos CSV:
● casos_bogota.csv
● casos_medellin.csv
● casos_cali.csv
● vacunacion_nacional.csv
● demografia_ciudades.csv
4. Instrucciones del Taller (Paso a Paso)
Desarrolla un Jupyter Notebook que siga la siguiente secuencia lógica. Sé claro con tus
comentarios y explica el "porqué" de tus decisiones.
Paso 1: Configuración y Carga de Datos
1. Importa las librerías necesarias: pandas, numpy, y os.
2. Carga los 5 archivos CSV en DataFrames separados (ej. df_casos_bog,
df_vacunacion).
3. Inspección Inicial: Para cada uno de los 5 DataFrames, muestra sus primeras 5 filas
(.head()), sus dimensiones (.shape) y la información general (.info()). Presta especial
atención a los nombres y tipos de las columnas que servirán como claves.
Paso 2: Combinación Vertical de Reportes de Casos (pd.concat)
1. Consolida los casos por ciudad: Combina los tres DataFrames de casos
(casos_bogota.csv, casos_medellin.csv, casos_cali.csv) en un único DataFrame llamado
casos_consolidados.
2. Asegura la integridad del índice: Usa el parámetro ignore_index=True para generar un
nuevo índice limpio y secuencial.
3. Verificación: Imprime las dimensiones del DataFrame casos_consolidados. Deberías
tener 2,520 filas. Confirma que la columna Ciudad está presente y contiene los
nombres de las tres ciudades.
Paso 3: Enriquecimiento con Datos Nacionales (pd.merge)
1. Integrar Datos de Vacunación:
○ Toma casos_consolidados y únelo con df_vacunacion.
○ La unión requiere una clave compuesta. Utiliza las columnas ['Fecha', 'Ciudad']
para asegurar que cada registro diario por ciudad se corresponda correctamente.
○ Usa un left join para asegurar que se conserven todos los reportes de casos,
incluso si para un día en particular no hubiera datos de vacunación.
○ Almacena el resultado en un nuevo DataFrame llamado salud_con_vacunas.
2. Integrar Datos Demográficos:
○ Ahora, toma salud_con_vacunas y únelo con df_demografia.
○ La clave de unión es Ciudad.
○ Almacena el resultado en un DataFrame final llamado df_final.
○ Pregunta de análisis (responde en una celda de Markdown): Si hubieras
hecho un inner join para unir los datos de vacunación, ¿qué días o registros se
habrían perdido y por qué? (Pista: la vacunación no comenzó el mismo día que la
pandemia).
Paso 4: Ingeniería de Indicadores de Salud (KPIs)
1. Crear indicadores calculados: En df_final, crea las siguientes columnas:
○ Tasa_Mortalidad_Diaria: Fallecidos / Casos_Confirmados. (Maneja los casos de
división por cero rellenando los resultados NaN o inf con 0).
○ Casos_Por_100k_Hab: (Casos_Confirmados / Poblacion_Total) * 100000. Este es
un indicador clave para comparar el impacto relativo entre ciudades de diferente
tamaño.
○ Porcentaje_Pauta_Completa: (Pauta_Completa / Poblacion_Total) * 100.
Paso 5: Reordenamiento y Análisis Agregado
1. Ordenamiento por Ciudad y Fecha: Ordena df_final usando las columnas ['Ciudad',
'Fecha'] para poder analizar la evolución temporal de la pandemia en cada localidad.
Resetea el índice.
2. Identificación de Picos de la Pandemia:
○ Crea un nuevo DataFrame ordenando df_final por Casos_Confirmados de forma
descendente para identificar los días de mayor impacto. Muestra los 10 peores
días registrados en el consolidado.
3. Vista Agregada (Tabla Pivote):
○ Para crear una columna de agregación temporal, primero extrae el mes de la
columna Fecha: df_final['Mes'] =
pd.to_datetime(df_final['Fecha']).dt.to_period('M').
○ Crea una tabla pivote que muestre la suma de Casos_Confirmados para cada
Ciudad (filas) a través de cada Mes (columnas).
○ Pregunta de análisis (responde en una celda de Markdown): ¿Qué ciudad
parece haber tenido el pico de casos más temprano? ¿Se observa un patrón
similar de "olas" en las tres ciudades?
Paso 6: Automatización del Pipeline
1. Crea una función procesar_pipeline_covid(ruta_archivos):
○ La función debe recibir como parámetro la ruta al directorio donde están los
CSVs.
○ Dentro de la función, debe realizar dinámicamente todos los pasos del 2 al 4:
cargar y concatenar los archivos de casos, hacer los merges con vacunación y
demografía, y crear los KPIs.
○ La función debe retornar el DataFrame final, procesado y listo para el
análisis.
2. Prueba tu función: Llama a la función y verifica que el DataFrame que retorna es
idéntico al que creaste manualmente en los pasos anteriores.
Paso 7: Validación y Exportación de Reportes
1. Validación de Calidad: Realiza al menos dos verificaciones sobre el DataFrame final:
○ Comprueba que no hay valores nulos en columnas clave como Poblacion_Total
después de los merges.
○ Verifica que el rango de fechas es el esperado.
2. Exporta tus Resultados:
○ Guarda el DataFrame final y completo como reporte_covid_integrado.csv.
○ Crea un reporte en Excel (reporte_analisis_covid.xlsx) que contenga dos hojas:
■ Una hoja con los datos completos.
■ Otra hoja con la tabla pivote de casos por ciudad y mes.
