# 👨🏻‍💻 Análisis de datos de secuenciación de ARN unicelular (scRNA-seq)

## 🎯 1. Introducción a scRNA-seq

La secuenciación de ARN de célula única (**scRNA-seq**) es una tecnología reciente que permite medir la expresión génica a nivel de cada célula individual. A diferencia del RNA-seq masivo (*bulk*), que mide el promedio de expresión génica en una población de células, el scRNA-seq permite capturar la heterogeneidad biológica, analizando la diversidad de tipos celulares en un tejido complejo e identificando estados celulares raros.

## 🕵 2. Flujo de trabajo general

La metodología de scRNA-seq puede dividirse en dos etapas principales complementarias e interdependientes:

**1. Fase experimental**: incluye todos los procedimientos que se llevan a cabo desde la obtención del material biológico hasta la generación de los datos de secuenciación. 
  
**1.1 Obtención y preparación de la muestra**: consiste en obtener una suspensión de células individuales viables a partir de un tejido o población celular. Para lograrlo, los tejidos suelen someterse a procesos de disociación mecánica y/o enzimática. Posteriormente, de forma opcional, se pueden seleccionar células (por ejemplo, basándose en ...)

   1.2 **Aislamiento de células individuales**: involucra asegurar que cada célula sea procesada de forma independiente. Este aislamiento puede realizarse mediante diversas tecnologías, como sistemas de nanopocillos, microgotas o microplacas, cada una con sus ventajas y limitaciones en cuanto al número de células que se pueden analizar, la profundidad de secuenciación y la resolución transcriptómica.

   1.3 **Captura del ARN y etiquetado molecular**: las células son lisadas y su ARN mensajero (ARNm) es capturado y marcado molecularmente. En este proceso, se añaden códigos de barras celulares (*bardcodes*) que permiten identificar de qué célula proviene cada transcrito, así como identificadores moleculares únicos (UMI, *Unique Molecular Identifier*), que tienen la función de diferenciar las moléculas originales de las copias que se generan durante la amplificación.

   1.4 **Retrotranscripción y amplificación**: El ARNm se convierte en ADN complementario (ADNc) mediante retrotranscripción, y luego se amplifica para asegurarse de tener suficiente material genético. Esta amplificación, se puede llevar a cabo mediante PCR o transcripción *in vitro*.

   1.5 **Construcción de librerías y secuenciación**: el ADNc amplificado se utiliza para construir librerías de secuenciación que son procesadas mediante plataformas de secuenciación masiva.

2. **Fase computacional**: comienza una vez que se han generado los datos de secuenciación, se busca transformar los datos crudos en información biólogica que se pueda analizar.

   2.1 **Preprocesamiento**: las lecturas pasan por un procesamiento primario que incluye asignar cada lectura a su célula de origen usando los bardcodes, el alineamiento o pseudoalineamiento a un genoma o transcriptoma de referencia, y el conteo de las moléculas con los UMIs. Al final de este proceso, se genera una matriz de expresión génica, donde las filas representan genes y las columnas representan células individuales.

   2.2 **Control de calidad**: tiene la finalidad de identificar y eliminar células dañadas, dobletes o multipletes, así como restos celulares. Se basa en métricas como el número de genes detectados por célula, el número total de transcritos y la proporción de ARN mitocondrial.

   2.3 **Normalización**: su propósito es hacer comparables las células entre sí, corrigiendo diferencias debidas a la profundidad de secuenciación u otras fuentes de variación técnica. También, puede incluir la corrección de efectos de lote (*batch effects*) cuando los datos provienen de múltiples experimentos o condiciones.

   2.4 **Selección de genes informativos**: se identifican aquellos genes que presentan una variabilidad significativa entre células y que son más útiles para distinguir diferentes tipos o estados celulares.

   2.5 **Reducción de dimensionalidad**: dado que la matriz de expresión génica tiene una alta dimensionalidad, se aplican técnicas de reducción para representar los datos en un espacio más simple. Técnicas como el análisis de componentes principales (PCA, *Principal Component Analyisis*), UMAP ayudan a capturar las principales fuentes de variación, lo que a su vez facilita la exploración visual de los datos.

   2.6 **Agrupamiento**: su objetivo es identificar conjuntos de células con perfiles transcriptómicos similares.

   2.7 **Identificación de genes marcadores**: los grupos celulares obtenidos se caracterizan por la identificación de genes distintivos, que permiten asignar identidades celulares o estados funcionales a cada grupo. Para ello, es necesario integrar el conocimiento previo que ya existe en la literatura y en bases de datos especializadas.
   
En resumen, la fase experimental establece la calidad y el tipo de información disponible, mientras que la fase computacional se ocupa de cómo se analiza e interpreta dicha información.

## 🔎 3. Aplicaciones, ventajas y desventajas

La scRNA-seq permite abordar preguntas biológicas que requieren resolución celular fina, pero también implica retos técnicos y analíticos. En la siguiente tabla se sintetizan sus principales aplicaciones, ventajas y limitaciones.

| Característica | Descripción |
| :--- | :--- |
| **Aplicaciones** |  Se utiliza para estudiar la heterogeneidad celular en tejidos complejos, permitiendo analizar procesos del desarrollo embrionario, respuestas inmunes, cáncer y organización del sistema nervioso.|
| **Ventajas** | Alta resolución, identificación de poblaciones celulares no descubiertas, estudio de trayectorias. |
| **Desventajas** | Alto costo, mayor "ruido" estadístico (*dropout events*), requiere procesamiento bioinformático complejo. |

## 4. Ejercicio práctico en R

A continuación, realizaremos un análisis básico utilizando el paquete `Seurat` y el dataset de 2,700 células mononucleares de sangre periférica (PBMC).
