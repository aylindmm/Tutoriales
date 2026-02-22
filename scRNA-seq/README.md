# 👨🏻‍💻 Secuenciación de ARN de célula única (scRNA-seq)

## 🔬 ¿Qué es scRNA-seq?

La secuenciación de ARN de célula única (scRNA-seq) es una tecnología reciente que permite medir la expresión génica a nivel de cada célula individual. A diferencia del RNA-seq masivo (*bulk*), que mide el promedio de expresión génica en una población de células, la scRNA-seq permite capturar la heterogeneidad biológica, analizando la diversidad de tipos celulares en un tejido complejo e identificando estados celulares nuevos.

## 📍 Flujo de trabajo general

La metodología de scRNA-seq puede dividirse en dos etapas principales complementarias e interdependientes:

1. <mark>**Fase experimental**</mark>

Incluye todos los procedimientos que se llevan a cabo desde la obtención de la muestra hasta la generación de los datos de secuenciación. 

   1.1 **Obtención y preparación de la muestra**

   Consiste en obtener una suspensión de células individuales viables a partir de un tejido o cultivo celular. Para conseguirlo, los tejidos suelen someterse a procesos de disociación mecánica y/o enzimática que permiten separar las células sin dañar su integridad estructural ni la calidad del ARN. Durante este procedimiento, es muy importante priorizar la viabilidad celular y reducir al mínimo el estrés o la degradación del material genético, ya que el éxito del scRNA-seq depende casi totalmente de la calidad inicial de la muestra.

   1.2 **Aislamiento de células individuales**
   
   Una vez que se ha obtenido la suspensión celular, es fundamental procesar cada célula de manera individual. Este aislamiento se puede llevar a cabo utilizando diversas tecnologías, como sistemas de microgotas, nanopocillos o microplacas, cada una con sus ventajas y limitaciones. La elección del método adecuado depende de cuántas células se necesiten analizar, la profundidad de secuenciación que se requiera y la resolución transcriptómica que se desee alcanzar.

   1.3 **Captura del ARN y etiquetado molecular**
   
   Tras el aislamiento, las células son lisadas y se captura su ARN mensajero (ARNm). Durante este proceso, se añaden códigos de barras celulares (*bardcodes*) que identifican de qué célula proviene cada transcrito, junto con identificadores moleculares únicos (UMI, *Unique Molecular Identifier*) que ayudan a diferenciar las moléculas originales de las copias que se generan durante la amplificación. 
   
   1.4 **Retrotranscripción y amplificación**
   
   El ARNm se convierte en ADN complementario (ADNc) mediante retrotranscripción. Luego, este ADNc se amplifica, generalmente mediante la reacción en cadena de la polimerasa (PCR) o transcripción *in vitro*, con el fin de obtener suficiente material para crear librerías de secuenciación.

   1.5 **Construcción de librerías y secuenciación**

   El ADNc amplificado se prepara añadiendo adaptadores que son compatibles con la plataforma de secuenciación. Antes de avanzar a la secuenciación, se lleva a cabo un control de calidad para verificar la concentración, el tamaño de los fragmentos y la integridad de las librerías. Esta revisión garantiza que el material cumpla con los requisitos técnicos necesarios.

   1.6 **Secuenciación**

   Las librerías pasan por un proceso de secuenciación masiva, lo que da lugar a archivos de lecturas crudas en formato FASTQ. Estos archivos son el punto de partida para el análisis computacional que se realizará más adelante.
   
2. <mark>**Fase computacional**</mark>

Comienza una vez que se han generado los datos de secuenciación, se busca transformar los datos crudos en información biólogica interpretable.

   2.1 **Preprocesamiento**

   El análisis computacional inicia con el procesamiento de las lecturas que se generaron a partir de la secuenciación. Este primer paso abarca la clasificación de las muestras, la corrección de posibles errores en los códigos de barras, el filtrado de lecturas de baja calidad y el alineamiento o pseudoalineamiento con un genoma o transcriptoma de referencia. Herramientas como *Cell Ranger* se encargan de automatizar gran parte de este proceso. Gracias al uso de los UMIs, se lleva a cabo el conteo de moléculas, lo que da lugar a una matriz de expresión génica donde las filas representan genes, las columnas son células individuales y los valores indican el número de transcritos detectados. Estos datos suelen mostrar una distribución muy dispersa y una alta proporción de ceros.

   2.2 **Control de calidad**

   Una vez obtenida la matriz de expresión, se realiza un filtrado tanto a nivel celular como génico. Se eliminan las células que tienen un número muy bajo de genes detectados, así como aquellas que presentan una alta proporción de ARN mitocondrial, ya que esto puede ser un signo de daño celular. También se descartan posibles dobletes o multipletes. Del mismo modo, se eliminan los genes que se expresan en un número muy reducido de células, ya que no aportan mucha información al análisis del conjunto de datos.

   2.3 **Normalización y transformación**

   El propósito de esta etapa es hacer que las células sean comparables entre sí, ajustando las diferencias que pueden surgir debido a la profundidad de secuenciación y otras variaciones técnicas. Este proceso puede incluir la normalización según el tamaño de la biblioteca, la transformación logarítmica de los datos, el escalado y, en algunos casos, la regresión de variables no deseadas, como el porcentaje de ARN mitocondrial o el estado del ciclo celular. Cuando se analizan varias muestras, también se puede aplicar una corrección por efectos de lote (*batch effects*).

   2.4 **Selección de genes altamente variables**
   
  En esta fase, se identifican los genes cuya variabilidad entre las células supera la variación técnica esperada. Estos genes que presentan alta variabilidad son muy informativos para entender la heterogeneidad biológica del sistema y son fundamentales para los análisis de reducción de dimensionalidad y agrupamiento.

   2.5 **Reducción de dimensionalidad**
   
   Dado que la matriz de expresión génica tiene una alta dimensionalidad, se utilizan métodos que ayudan a representar los datos en un espacio más simple. Primero, se lleva a cabo un análisis de componentes principales (PCA, *Principal Component Analyisis*), que resume las principales fuentes de variación. Luego, se emplean técnicas no lineales como UMAP (*Uniform Manifold Approximation and Projection*) o t-SNE (*t-Distributed Stochastic Neighbor Embedding*), que facilitan la visualización de las células en dos o tres dimensiones, permitiendo así explorar patrones y subpoblaciones celulares.

   2.6 **Construcción del grafo de vecinos y agrupamiento (*clustering*)**

   A partir de los componentes principales seleccionados, se crea un grafo de vecinos más cercanos que muestra la similitud transcriptómica entre las células. A partir de este grafo, se utilizan algoritmos de *clustering* para identificar grupos de células con perfiles similares. Estos grupos pueden representar diferentes tipos celulares o distintos estados funcionales dentro de una misma población.

   2.7 **Identificación de genes marcadores y anotación celular**

   Finalmente, se lleva a cabo un análisis de expresión diferencial (DE, *Differencial expression*) entre los grupos identificados para reconocer genes que son marcadores distintivos de cada clúster. Estos genes son clave para caracterizar funcionalmente las poblaciones celulares y sirven como base para asignar identidades biológicas, integrando conocimientos previos de la literatura o de bases de datos especializadas.

   2.8 **Análisis exploratorios**

   Dependiendo de la pregunta de investigación, se pueden llevar a cabo análisis más avanzados, como la inferencia de trayectorias celulares, la integración de múltiples *datasets*, el análisis de interacciones entre células o la estimación de dinámicas transcriptómicas.
   

>En resumen, la fase experimental establece la calidad y el tipo de información disponible, mientras que la fase computacional se ocupa de cómo se analiza e interpreta dicha información.

## 🔎 Aplicaciones, ventajas y desventajas

La scRNA-seq permite abordar preguntas biológicas que requieren una resolución detallada, aunque también implica desafíos tanto técnicos como analíticos. En la tabla siguiente se resumen sus principales aplicaciones, ventajas y limitaciones.

| Aplicaciones | Ventajas | Desventajas |
|--------------|----------|-------------|
| Identificación de tipos celulares | Resolución a nivel celular | Costo elevado |
| Estudio de heterogeneidad tumoral | Detección de poblaciones raras | Complejidad técnica y computacional |
| Análisis de diferenciación y desarrollo | Estudio de heterogeneidad biológica | Alta proporción de ceros (*dropouts*) |
| Análisis de interacción célula–célula | Alto rendimiento | Posibles sesgos técnicos y efectos de lote |

## 📦 Paquetes para análisis de datos de scRNA-seq

Para analizar datos de scRNA-seq, existen varias herramientas dependiendo del lenguaje de programación que se desee utilizar: R o Python. 

Aquí se presentan tres de  los paquetes líderes en la industria:

### Ecosistema en R

R continúa siendo el referente principal debido a la vasta cantidad de paquetes estadísticos que ofrece.

- [**Seurat**](https://satijalab.org/seurat/): Es la más utilizada ya que agrupa todas las herramientas necesarias para procesar y visualizar los datos en un solo lugar. Es excelente gracias a su amplia documentación y versatilidad. Usa un objeto `Seurat`.

- [**SingleCellExperiment (Bioconductor)**](https://www.bioconductor.org/about/): Es un conjunto de librerías especializadas y rigurosas que se pueden combinar libremente para realizar análisis estadísticos más personalizados y profundos. Utiliza una estructura común llamada `SingleCellExperiment` (SCE).

### Ecosistema en Python

Python ha ganado mucha popularidad, especialmente para trabajar con grandes volúmenes de datos, gracias a su gran eficiencia en el uso de memoria.

- [**Scanpy**](https://scanpy.readthedocs.io/en/stable/): Es una librería que resalta por su gran escalabilidad, capaz de procesar millones de células rápidamente. Es la opción preferida en entornos de ciencia de datos por su integración con algoritmos de aprendizaje automático. Emplea la estructura `AnnData`.

La decisión entre usar R o Python para el análisis de scRNA-seq depende del equilibrio entre rigor estadístico y capacidad de cómputo. Ambos ecosistemas son complementarios y definen el estándar actual para desglosar la complejidad celular con alta precisión.

## ✍️ Conclusión

La scRNA-seq integra una fase experimental de alta precisión con un análisis computacional avanzado, lo que ayuda a revelar la heterogeneidad celular que los métodos tradicionales suelen pasar por alto. Gracias a la potencia de los algoritmos en R y Python, esta tecnología transforma datos crudos en mapas biológicos detallados, lo que la convierte en una herramienta indispensable para afrontar los retos que la ciencia enfrenta actualmente.

## 📖 Bibliografía

*Haque, A., Engel, J., Teichmann, S. A., & Lönnberg, T. (2017). A practical guide to single-cell RNA-sequencing for biomedical research and clinical applications. Genome Medicine, 9(1), 75. https://doi.org/10.1186/s13073-017-0467-4*

*Jovic, D., Liang, X., Zeng, H., Lin, L., Xu, F., & Luo, Y. (2022). Single‐cell RNA sequencing technologies and applications: A brief overview. Clinical And Translational Medicine, 12(3), e694. https://doi.org/10.1002/ctm2.694*







