# 👨🏻‍💻 Análisis de datos de secuenciación de ARN de célula única (scRNA-seq)

## 🔬 1. ¿Qué es scRNA-seq?

La secuenciación de ARN de célula única (**scRNA-seq**) es una tecnología reciente que permite medir la expresión génica a nivel de cada célula individual. A diferencia del RNA-seq masivo (*bulk*), que mide el promedio de expresión génica en una población de células, el scRNA-seq permite capturar la heterogeneidad biológica, analizando la diversidad de tipos celulares en un tejido complejo e identificando estados celulares nuevos.

## 📍 2. Flujo de trabajo general

La metodología de scRNA-seq puede dividirse en dos etapas principales complementarias e interdependientes:

1. <mark>**Fase experimental**</mark>

Incluye todos los procedimientos que se llevan a cabo desde la obtención de la muestra hasta la generación de los datos de secuenciación. 

   1.1 **Obtención y preparación de la muestra**

   Consiste en obtener una suspensión de células individuales viables a partir de un tejido o cultivo celular. Para conseguirlo, los tejidos suelen someterse a procesos de disociación mecánica y/o enzimática que permiten separar las células sin dañar su integridad estructural ni la calidad del ARN. Durante este procedimiento, es muy importante priroziar la viabilidad celular y reducir al mínimo el estrés o la degradación del material genético, ya que el éxito del scRNA-seq depende casi totalmente de la calidad inicial de la muestra.

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

   El análisis computacional comienza con el procesamiento inicial de las lecturas que se generan a partir de la secuenciación. Este primer paso abarca el demultiplexado de las muestras, la corrección de posibles errores en los códigos de barras, el filtrado de lecturas de baja calidad y el alineamiento o pseudoalineamiento con un genoma o transcriptoma de referencia. Herramientas como *Cell Ranger* se encargan de automatizar gran parte de este proceso. Gracias al uso de los UMIs, se lleva a cabo el conteo de moléculas, lo que da lugar a una matriz de expresión génica donde las filas representan genes, las columnas son células individuales y los valores indican el número de transcritos detectados. Estos datos suelen mostrar una distribución muy dispersa y una alta proporción de ceros.

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

## 🔎 3. Aplicaciones, ventajas y desventajas

La scRNA-seq permite abordar preguntas biológicas que requieren una resolución detallada, aunque también implica desafíos tanto técnicos como analíticos. En la tabla siguiente se resumen sus principales aplicaciones, ventajas y limitaciones.

| Aplicaciones | Ventajas | Desventajas |
|--------------|----------|-------------|
| Identificación de tipos celulares | Resolución a nivel celular | Costo elevado |
| Estudio de heterogeneidad tumoral | Detección de poblaciones raras | Complejidad técnica y computacional |
| Análisis de diferenciación y desarrollo | Estudio de heterogeneidad biológica | Alta proporción de ceros (*dropouts*) |
| Análisis de interacción célula–célula | Alto rendimiento | Posibles sesgos técnicos y efectos de lote |

## 📦 4. Paquetes para análisis de scRNA-seq en R

Para realizar un análisis de scRNA-seq en R la elección de las librerías es fundamental. Existen varias herramientas, sin embargo en este tutorial abordaremos las siguientes dos debido a que ambas permiten hacer todo el flujo, desde el control de calidad hasta la identificación de tipos celulares.

- [**Seurat**](https://satijalab.org/seurat/): Es la más utilizada ya que agrupa todas las herramientas necesarias para procesar y visualizar los datos en un solo lugar. Utiliza un objeto `Seurat` que organiza los datos de conteo, los metadatos y el análisis dimensional. Es excelente gracias a su amplia documentación y versatilidad.

- [**SingleCellExperiment (Bioconductor)**](https://www.bioconductor.org/about/): Es un conjunto de librerías especializadas y rigurosas que se pueden combinar libremente para realizar análisis estadísticos más personalizados y profundos. Utiliza una estructura común llamada `SingleCellExperiment` (SCE).

## 💻 4. Análisis de datos de scRNA-seq con Seurat en RStudio

A continuación, se llevará a cabo un ejercicio práctico para aprender a realizar un **análisis completo** de un conjunto de datos reales de células individuales usando el paquete **Seurat** en el entorno de **RStudio**. 

Más allá de simplemente aprender a ejecutar comandos en R, el *objetivo principal* es que comprendan la lógica biológica y computacional que hay detrás de cada paso, y que sean capaces de interpretar de manera crítica los resultados que obtienen.

Esta guía es una adaptación educativa del tutorial oficial de [*Seurat Guided Clustering Tutorial*](https://satijalab.org/seurat/articles/pbmc3k_tutorial), desarrollado por Rahul Satija y colaboradores. El contenido ha sido ajustado con fines didácticos para facilitar la comprensión de este tipo de análisis bioinformático para estudiantes principiantes.

####  ¿Qué datos se van a estudiar?

Los datos que se utilizarán provienen del conjunto [PBMCs](https://cf.10xgenomics.com/samples/cell/pbmc3k/pbmc3k_filtered_gene_bc_matrices.tar.gz) que incluye 2 700 células mononucleares de sangre periférica humana secuenciadas utilizando la tecnología de 10x Genomics. 

### 1. Preparación del entorno y carga del conjunto de datos *PBMC*

#### 1.1 Antes de empezar

Se requiere descargar el archivo del *dataset* y descomprimirlo.

>Tip: Se sugiere crear un proyecto específico y organizar los archivos en una carpeta bien estructurada (por ejemplo, una carpeta llamada “scRNA-seq_ej1”) ya que ayuda a mantener la reproducibilidad y el orden.

Es necesario instalar y cargar las siguientes librerías:

```r
install.packages(Seurat)
install.packages(dplyr)
install.packages(patchwork)

library(Seurat)
library(dplyr)
library(patchwork)
```
**¿Para qué sirve cada librería?**
- `Seurat`: librería principal que procesa los datos biológicos.
- `dplyr`: organiza la información textual de las células.
- `patchwork`: combina múltiples gráficas en una sola imagen de forma sencilla.
  
**Resultado esperado:**
- Si aparece el nombre de las librerías en la consola quiere decir que todo está correcto.
- Si hay error significa que las librerías no se instalaron adecuadamente.

#### 1.2 Leer los datos desde 10x Genomics

Para trabajar con los datos en el entorno de R, primero necesitas leer los archivos que generó 10x Genomics. Seurat tiene una función llamada `Read10X` que se encarga de leer automáticamente los archivos que contienen la matriz de conteos, los nombres de los genes y los identificadores de las células, y los combina en una sola matriz manipulable en R.

```r
pbmc.data <- Read10X(data.dir = "ruta/a/tus/datos/")
```

**Resultado esperado:**

El objeto resultante `pbmc.data` se guarda en el *Environment*.

Se carga la **matriz de conteos cruda**, en donde:
- Filas = genes (32 738).
- Columnas = células (2 700).

<img width="921" height="325" alt="image" src="https://github.com/user-attachments/assets/4493eae4-2bae-43eb-9ffb-5b908056d939" />

#### 1.3 Crear el objetivo `Seurat`

El siguiente paso es crear un objeto `Seurat` que es una estructura especializada diseñada para almacenar tanto los datos de expresión génica como la información adicional necesaria para el análisis. Es un contenedor que guarda todo el análisis en un solo objeto.

Para crear un objeto Seurat se utiliza la función `CreateSeuratObject`. El parámetro `projet` menciona el nombre del proyecto y `min.cells` asegura que solo se mantendrán aquellos genes que estén presentes en al menos tres células, lo que ayuda a eliminar genes que probablemente sean ruido. Por otro lado, el parámetro `min.features` determina que solo se incluirán células que tengan al menos 200 genes detectados, descartando aquellas con muy poca información transcriptómica.

```r
pbmc <- CreateSeuratObject(counts = pbmc.data, project = "pbmc3k", min.cells = 3, min.features = 200)
```

**Resultado esperado:**

El objeto Seurat resultante `pbmc` se almacena en el *Environment*. 

Se filtra la **matriz de conteos cruda**, y ahora se cuenta con 13 714 genes y 2 700 células.

<img width="921" height="547" alt="image" src="https://github.com/user-attachments/assets/87fab070-524c-4c31-9913-fb814c0f1e40" />


#### Para explorar el objeto, puedes utilizar las siguientes funciones:

```r
dim(pbmc)          # Permite saber cuántos genes (filas) y cuántas células (columnas) contiene el experimento
rownames(pbmc)[1:5] # Ver los primeros 5 nombres de genes
colnames(pbmc)[1:5] # Ver los primeros 5 nombres de células 
pbmc[1:5, 1:5]     # Visualizar un subconjunto pequeño de la matriz
pbmc["MS4A1", ]    # Consultar la expresión de un gen específico en todas las células
```

### 2. Control de calidad

Primero se debe evaluar la calidad de las células incluidas en el conjunto de datos, ya que es común encontrar células dañadas o muertas, dobletes o multipletes, o que tienen ARN degradado, lo cual pueden afectar la interpretación de los resultados si no se eliminan adecuadamente.

**Métricas más usadas**
- `nFeature_RNA`: número total de genes detectados por célula.
- `nCount_RNA`: número total de moléculas (UMIs) por célula.
- `percent.mt`: porcentaje de genes mitocondriales (genes MT-) por célula.

Un alto porcentaje de ARN mitocondrial suele ser un signo de células que están bajo estrés o en proceso de morir. Para calcular esta métrica, Seurat utiliza la función `PercentageFeatureSet()` e identifica los genes mitocondriales buscando un patrón en sus nombres, que en humanos generalmente comienza con “MT-”, y luego calcula qué porcentaje representan en relación al total de genes expresados por célula.

Para calcular los genes mitocondriales:

```r
pbmc[["percent.mt"]] <- PercentageFeatureSet(pbmc, pattern = "^MT-")
```

Puedes visualizar estas métricas mediante gráficos de violín:

```r
VlnPlot(pbmc, features = c("nFeature_RNA","nCount_RNA","percent.mt"))
```
**Resultado esperado:**

Cómo interpretar las gráficas:
- Valores muy bajos quiere decir que son células de baja calidad.
- Valores muy altos significa posibles dobletes.
- Un porcentaje mitocondrial alto pueden ser células dañadas.

<img width="1178" height="683" alt="Control_calidad" src="https://github.com/user-attachments/assets/39bb3c20-2c59-45c8-93f5-98c068fca85d" />


Una vez evaluadas las métricas de calidad y visualizadas sus distribuciones, el siguiente paso es eliminar aquellas células que no cumplen con los criterios mínimos para un análisis confiable. Este proceso, conocido como **filtrado**, tiene como objetivo conservar solo las células que presentan perfiles de expresión representativos. Se lleva a cabo utilizando la función `subset` seleccionando únicamente las células que cumplen con los criterios establecidos.

En este ejercicio se eliminan células:
- Que expresan menos de 200 genes, ya que suelen ser células muertas, fragmentos celulares o resultado de errores técnicos.
- Que expresan más de 2,500 genes, ya que podrían ser dobletes (dos células que se capturaron como una sola durante la secuenciación).
- Cuyo porcentaje de genes mitocondriales supera el 5%, ya que un valor alto suele estar asociado con estrés celular o degradación del ARN.

```r
pbmc <- subset(pbmc, subset = nFeature_RNA > 200 & nFeature_RNA < 2500 & percent.mt < 5)
```

**Resultado esperado:**

El objeto `Seurat` se actualiza de forma automática, eliminando las células que no cumplen con los filtros establecidos. 

El número total de células que se almacenan en el objeto disminuyó. Ahora se cuenta con 2 638 células.

<img width="221" height="57" alt="image" src="https://github.com/user-attachments/assets/48cf5eaa-0a15-41b5-8fd2-c0a8f969cc08" />


### 3. Normalización de los datos

Después de filtrar los datos, el siguiente paso es normalizarlos. En el caso del scRNA-seq, diferentes células pueden tener distintas profundidades de secuenciación; es decir, algunas pueden tener más lecturas que otras, y esto puede deberse a razones técnicas. La normalización ayuda a ajustar estas diferencias, asegurando que las comparaciones entre células sean válidas.

Seurat realiza este proceso mediante la función `NormalizeData`, utiliando el método *LogNormalize* que:
1. Divide los conteos por el total de cada célula.
2. Multiplica el resultado por 10 000.
3. Aplica logaritmo para que sea más comparable entre células.

```r
pbmc <- NormalizeData(pbmc, normalization.method = "LogNormalize", scale.factor = 10000)
```

**Resultado esperado:**

Los datos normalizados se guardan internamente. Los datos crudos no se eliminan, sino que se conservan dentro del objeto `Seurat` por si se requieren después.

<img width="921" height="211" alt="image" src="https://github.com/user-attachments/assets/793bd134-b1c2-47d3-bdee-29a54ed2a619" />


### 4. Detección de genes altamente variables

En un experimento de scRNA-seq, no todos los genes son igualmente útiles para diferenciar entre los distintos tipos de células. Muchos de ellos tienen niveles de expresión que son bastante similares en todas las células, lo que los hace poco útiles para analizar la variación celular. Por eso, Seurat se encarga de identificar un aquellos genes cuya expresión varía significativamente entre las células.

Este proceso se lleva a cabo mediante la función `FindVariableFeatures`, que examina la relación entre la media y la varianza de la expresión de cada gen, seleccionando aquellos que muestran una variabilidad mayor de lo que se esperaría.

En este caso, se seleccionan los 2,000 genes más variables del conjunto de datos.

```r
pbmc <- FindVariableFeatures(pbmc, selection.method = "vst", nfeatures = 2000)
```

**Resultado esperado:**

Se guarda internamente la lista de genes variables. 

<img width="921" height="282" alt="image" src="https://github.com/user-attachments/assets/beb6e431-b74c-4c9b-ae4d-3cc72132b976" />


### 5. Escalado de los datos
El escalado implica centrar y estandarizar los valores de expresión génica, asegurando que cada gen tenga una media igual a cero y una varianza igual a 1. Este proceso evita que los genes con valores de expresión muy altos dominen el análisis. Se realiza mediante la función `ScaleData`, la cual trabaja sobre los genes previamente identificados como altamente variables.

```r
all.genes <- rownames(pbmc)
pbmc <- ScaleData(pbmc, features = all.genes)
```
**Resultado esperado:**

Los resultados se almacenan en `all.genes` en el *Environment*.

<img width="921" height="77" alt="image" src="https://github.com/user-attachments/assets/668a89ba-e3a0-4c69-9376-db76e074a703" />


### 6. Análisis de componentes principales (PCA)
El análisis de componentes principales (*PCA*, por sus siglas en inglés) es una técnica estadística que ayuda a simplificar la complejidad de los datos puesto que convierte la expresión de muchos genes en un conjunto más pequeño de componentes que logran capturar la mayor parte de la variación entre las células.

Para realizar el PCA, se utiliza la función `RunPCA`, que requiere como entrada los datos escalados. 

```r
pbmc <- RunPCA(pbmc, features = VariableFeatures(object = pbmc))
```
**Resultado esperado:**

Los resultados se almacenan dentro del objeto `Seurat`.

En la consola se muestran los primeros componentes y los genes que más contribuyen positiva y negativamente a cada componente principal.

<img width="921" height="339" alt="image" src="https://github.com/user-attachments/assets/fed91dce-7e39-43b9-816b-56a30a0a54c1" />

Existen diversas funciones para interpretar tanto las células como las características que conforman el PCA, como:

1. `VizDimReduction()`: se utiliza para identificar y visualizar los genes que más contribuyen a cada componente principal, lo que permite interpretar qué procesos biológicos están representados en cada eje de variación.

```r
VizDimLoadings(pbmc, dims = 1:2, reduction = "pca")
```
Se observa:

- Un gráfico de barras con los genes que más contribuyen a PC1 y PC2.
- Se separan en contribuciones positivas y negativas.
- Son los genes que más explican la variación capturada por esos componentes.

<img width="1043" height="708" alt="PCA1" src="https://github.com/user-attachments/assets/900d51dd-106a-4d2b-a4fd-082b547f6f9e" />

2. `DimPlot()`: se emplea para visualizar cómo se distribuyen las células en el espacio de los componentes principales y evaluar si existe separación o agrupamiento entre ellas.

```r
DimPlot(pbmc, reduction = "pca") + NoLegend()
```
Se observa:

- Cada punto representa una célula.
- Los ejes son los PC1 y PC2.
- Si hay separación visible, significa que hay variación biológica importante. 

<img width="1043" height="708" alt="PCA2" src="https://github.com/user-attachments/assets/636715d6-2369-487f-b3c1-e75fac2158a8" />

3. `DimHeatmap()`: se usa para observar los patrones de expresión de los genes más influyentes en cada componente principal a través de un mapa de calor (*heatmap*), facilitando la interpretación biológica de los PCs y ayudando a decidir cuántos componentes son relevantes para análisis posteriores.

```r
DimHeatmap(pbmc, dims = 1, cells = 500, balanced = TRUE)
```
Se observa:

- Las filas son genes con mayor relevancia en PC1.
- Las columnas son las 500 células seleccionadas.
- Las células están ordenadas según su valor en PC1.

<img width="1043" height="708" alt="PCA3" src="https://github.com/user-attachments/assets/d8bd23da-e2fa-40fb-bf46-d095012e8433" />

```r
DimHeatmap(pbmc, dims = 1:15, cells = 500, balanced = TRUE)
```

<img width="1043" height="708" alt="PCA4" src="https://github.com/user-attachments/assets/3e98d50f-21aa-4204-8117-d7cab14a6fc8" />

Se observa:

- Un *heatmap* por cada PC.
- Permite evaluar qué tan estructurado está cada componente.
- Si un PC no muestra patrón claro, probablemente no aporte información biológica importante.

Para decidir cuántos componentes principales incluir, a menudo se recurre a un gráfico conocido como `Elbow Plot`. Este gráfico ilustra la cantidad de varianza que cada componente principal explica. El punto en el que la curva comienza a aplanarse, conocido como “codo”, señala un número razonable de componentes a considerar, ya que a partir de ahí, la ganancia de información se vuelve mínima.

```r
ElbowPlot(pbmc)
```

En este ejemplo, se observa una inclinación o "codo" alrededor de los componentes principales 9 a 10, lo que indica que la mayor parte de la varianza se encuentra en los primeros 10 PC. 

<img width="1043" height="708" alt="Elbowplot" src="https://github.com/user-attachments/assets/27b4c056-7fdf-4a18-a3bd-e5ecefe3e2aa" />

### 7. Agrupar las células (clustering)
Antes de agrupar las células, es necesario identificar qué células son similares entre sí. Esta similitud se determina por la distancia entre las células en el espacio de los componentes principales, ya que estos componentes capturan la información más relevante sobre la expresión génica. Para lograr esto, Seurat crea un grafo de vecinos más cercanos, donde cada célula se conecta con aquellas que tienen perfiles de expresión similares. Este grafo es la base para el posterior agrupamiento de las células en clústeres. 

La función `FindNeighbors` se encarga de calcular estas relaciones utilizando los componentes principales seleccionados previamente.

```r
pbmc <- FindNeighbors(pbmc, dims = 1:10)
```
En este comando, el argumento `dims = 1:10` indica que se utilizarán los primeros diez componentes principales para calcular la similitud entre células.

**Resultado esperado:**

<img width="691" height="106" alt="image" src="https://github.com/user-attachments/assets/bf6136e4-004f-4074-a5cc-01270af2ee0d" />

En la consola se muestran mensajes que indican que el grafo está siendo construido correctamente.

Ahora sí, sigue el **clustering* que es una técnica que ayuda a identificar grupos de células que tienen perfiles de expresión similares, lo que generalmente se relaciona con diferentes tipos o estados celulares. En Seurat, este proceso se lleva a cabo a través de algoritmos de detección de comunidades, como el método de Louvain cuyo objetivo es agrupar nodos de tal manera que los miembros de un mismo grupo estén fuertemente conectados entre sí, mientras que las conexiones entre diferentes grupos sean mínimas. 

Para realizar el agrupamiento de células, se utiliza la función `FindClusters`, que asigna a cada célula una etiqueta de clúster. Un aspecto clave de esta función es el parámetro `resolution`, que determina el nivel de detalle en el agrupamiento. Si se utilizan valores bajos, se obtienen pocos clústeres grandes, mientras que valores más altos generan un mayor número de clústeres más pequeños. Los clústeres se pueden encontrar utilizando la función `Idents()`.

```r
pbmc <- FindClusters(pbmc, resolution = 0.5)
```

**Resultado esperado:**

Se muestra información en la consola que detalla el análisis de 2638 nodos (células) conectados por 95,927 aristas (relaciones de vecindad). Después de ejecutar el algoritmo, se logró una modularidad máxima de 0.8723, lo que indica una separación clara y bien definida entre los grupos. El algoritmo identificó 9 comunidades, es decir, 9 clústeres celulares que comparten perfiles de expresión génica similares.

<img width="921" height="318" alt="image" src="https://github.com/user-attachments/assets/874b950d-f278-4edc-b83b-aa4df5878301" />

Seurat agrega esta información al objeto, asignando a cada célula un identificador (numérico) de clúster. Para mirar los identificadores de los grupos de las primeras 5 células:

```r
head(Idents(pbmc), 5)
```
**Resultado esperado:**

Cada nombre largo (por ejemplo, AAACATACAACCAC-1) es el *barcode* de una célula individual, y el número debajo (por ejemplo, 2, 3, 1, 6) indica el clúster al que fue asignada esa célula. La línea *levels* indica que existen 9 clústeres en total, numerados del 0 al 8.

<img width="921" height="139" alt="image" src="https://github.com/user-attachments/assets/f10c3018-c11b-483e-ab88-875e55089b34" />

### 8. Reducción dimensional no lineal (UMAP/t-SNE)
Existen métodos adicionales de reducción de dimensionalidad que son algoritmos diseñados específicamente para mostrar las relaciones complejas entre las células en un mapa visual de dos dimensiones. Uno de los métodos más populares es UMAP (*Uniform Manifold Approximation and Projection*), que se basa en la topología (el estudio de las formas geométricas) para crear un mapa que logra mantener tanto la estructura local como la global de los datos, y otro es tSNE (*t-Distributed Stochastic Neighbor Embedding*) que se basa en probabilidades y estadística, centrándose exclusivamente en mantener juntos a los puntos que son casi idénticos.

Para ejecutar UMAP se utiliza la función `RunUMAP`, la cual emplea los mismos componentes principales usados para el *clustering*.

```r
pbmc <- RunUMAP(pbmc, dims = 1:10)
```

<img width="706" height="124" alt="image" src="https://github.com/user-attachments/assets/e1363deb-acd6-4ee1-b3cc-b36c07d104a0" />


Una vez calculadas las coordenadas UMAP, es posible visualizar los resultados mediante la función `DimPlot`.

```r
DimPlot(pbmc, reduction = "umap"`)
```

> Si añades el argumento `label = TRUE`, se muestran los números de clúster sobre cada grupo.
> La distancia entre los puntos refleja similitudes transcriptómicas, no distancias físicas entre células.

**Resultado esperado:**

La gráfica resultante presenta cada célula como un punto en un espacio 2D. 
Los puntos se colorean según el clúster al que pertenecen.

<img width="1047" height="708" alt="umap" src="https://github.com/user-attachments/assets/164d9ff6-5cb3-47e8-8c2b-84d7d280146e" />

### 9. Identificación de genes marcadores de cada cluster
Una vez que tenemos los clústeres, el siguiente paso es entender qué genes definen a cada grupo. Para ello, es necesario identificar aquellos genes que se expresen de manera preferencial en cada grupo. Estos genes, conocidos como **genes marcadores**, permiten distinguir entre distintos tipos celulares. 

Seurat identifica estos genes comparando la expresión génica de un clúster contra todos los demás,  pero también puede comparar grupos de clústeres entre sí. Este análisis se realiza mediante la función `FindMarkers`.

Para encontrar todos los marcadores del grupo 2:

```r
cluster2.markers <- FindMarkers(pbmc, ident.1 = 2)
head(cluster2.markers, n = 5)
```

**Resultado esperado:**

Una tabla en donde cada fila representa un gen marcador, mientras que las columnas muestran: 
- p_val: el valor p sin ajustar.
- avg_log2FC: el cambio promedio de expresión en escala log2 entre el clúster de interés y el grupo de comparación (los valores positivos significan que hay mayor expresión en el clúster analizado).
- pct.1: el porcentaje de células del clúster que expresan el gen.
- pct.2: el porcentaje de células en el grupo de referencia que también lo expresan.
- p_val_adj: el valor p ajustado.

En resumen, los valores p muy bajos y los log2FC positivos sugieren que estos genes están significativamente sobreexpresados en ese clúster, lo que implica que podrían actuar como marcadores distintivos de ese tipo celular.

<img width="921" height="214" alt="image" src="https://github.com/user-attachments/assets/34e90155-4f83-482d-b002-c98453e43a63" />



En grandes conjuntos de datos, calcular genes marcadores puede resultar bastante costoso computacionalmente. Para hacer este proceso más eficiente, se puede integrar el paquete **Presto**, que ofrece versiones muy optimizadas de pruebas estadísticas. Una vez que el paquete Presto está instalado y cargado en el entorno de R, Seurat lo utiliza automáticamente para acelerar los cálculos de expresión diferencial.

```r
install.packages('devtools')
devtools::install_github('immunogenomics/presto')
```

Seurat ofrece varias pruebas de DE, las cuales se aplican mediante el parámetro `test.use` dentro de la función `FindMarkers`. Este parámetro define el método estadístico que se utilizará para evaluar la expresión diferencial. Entre las opciones más comunes se encuentran la prueba de Wilcoxon y la curva ROC, entre otros. Cada uno de estos métodos tiene supuestos y aplicaciones distintas, por lo que es importante comprender que la elección del test puede influir en los resultados obtenidos.

```r
cluster0.markers <- FindMarkers(pbmc, ident.1 = 0, logfc.threshold = 0.25, test.use = "roc", only.pos = TRUE)
```

Después de haber identificado los genes marcadores, es importante visualizar su expresión para confirmar que efectivamente distinguen a los clústeres.

Algunas de las funciones más comúnes son:
- `VlnPlot`: genera gráficos de violín que muestran la distribución de la expresión de un gen en cada clúster. Este tipo de gráfica permite observar tanto el nivel promedio de expresión como la variabilidad dentro de cada grupo celular.

```r
VlnPlot(pbmc, features = c("MS4A1", "CD79A"))
```

**Resultado esperado:**

Un gráfico en el que cada violín representa un clúster, y la forma del violín muestra cómo se distribuyen los niveles de expresión del gen. Un gen marcador tendrá una expresión alta en uno o en unos pocos clústeres, mientras que en el resto mostrará niveles bajos.

<img width="797" height="473" alt="DE1" src="https://github.com/user-attachments/assets/6a72d56e-a15a-4182-adbf-4520d4cf6f9a" />

- `FeaturePlot`: proyecta la expresión de un gen directamente sobre la representación UMAP/tSNE o PCA. Las células se colorean de acuerdo con su nivel de expresión, lo que permite identificar visualmente en qué regiones del mapa se expresa un gen específico. 

```r
FeaturePlot(pbmc, features = c("MS4A1", "GNLY", "CD3E", "CD14", "FCER1A", "FCGR3A", "LYZ", "PPBP",
    "CD8A"))
```
**Resultado esperado:**

Un mapa UMAP en el que uno o más clústeres muestran una coloración intensa, indicando alta expresión del gen, mientras que el resto de las células aparecen con colores más tenues.

<img width="1095" height="708" alt="genesmarc1" src="https://github.com/user-attachments/assets/8a012745-8774-4af3-b875-76bc067aa0d4" />


>Otras herramientas adicionales que permiten explorar la expresión génica desde diferentes perspectivas son: `RidgePlot`, muestra la distribución de la expresión de un gen en forma de curvas de densidad para cada clúster; `CellScatter()`, compara la expresión de dos genes entre células individuales); y `DotPlot()`, resume la expresión de múltiples genes en múltiples clústeres.

Para tener una visión completa de los genes más relevantes en cada clúster, Seurat ofrece la opción de crear mapas de calor a través de la función `DoHeatmap`. Esta herramienta ilustra la expresión relativa de un grupo seleccionado de genes en todas las células, organizadas por clúster.

```r
pbmc.markers %>%
    group_by(cluster) %>%
    dplyr::filter(avg_log2FC > 1) %>%
    slice_head(n = 10) %>%
    ungroup() -> top10
DoHeatmap(pbmc, features = top10$gene) + NoLegend()
```

**Resultado esperado:**

Un mapa de calor donde cada fila representa un gen específico y cada columna se refiere a un clúster. Los colores muestran los niveles relativos de expresión. Este tipo de visualización permite observar claramente qué genes distinguen a cada grupo celular, facilitando la interpretación biológica de las identidades celulares detectadas.

<img width="1079" height="683" alt="heatmap" src="https://github.com/user-attachments/assets/322f48dc-7080-4dc0-99bf-a1406378349e" />

### 10. Anotación de los clústeres con identidades celulares
Finalmente, es posible asignar un significado biológico a cada clúster. Este proceso, conocido como **anotación**, se basa en el conocimiento previo de genes marcadores característicos de distintos tipos celulares. Por ejemplo, la expresión de genes como *GNLY* y *NKG7* suele relacionarse con células *Natural Killer*, mientras que el gen *MS4A1* es un marcador representativo de los linfocitos B.

Al definir la identidad de cada clúster, se puede renombrarlos con la función `RenameIdents`.

```r
new.cluster.ids <- c("Naive CD4 T", "CD14+ Mono", "Memory CD4 T", "B", "CD8 T", "FCGR3A+ Mono",
    "NK", "DC", "Platelet")
names(new.cluster.ids) <- levels(pbmc)
pbmc <- RenameIdents(pbmc, new.cluster.ids)
DimPlot(pbmc, reduction = "umap", label = TRUE, pt.size = 0.5) + NoLegend()
```

**Resultado esperado:**

Al volver a generar el gráfico UMAP, los clústeres aparecen ahora etiquetados con los nombres biológicos. Esto representa uno de los principales objetivos del análisis de scRNA-seq que es identificar y caracterizar poblaciones celulares a partir de datos de expresión génica.

<img width="830" height="683" alt="identity" src="https://github.com/user-attachments/assets/73d5eed8-7efd-4d97-8bef-fb7db817935f" />

### 📝 Para cerrar
Al finalizar este ejercicio, habrás pasado por todas las etapas del flujo general de un análisis de scRNA-seq utilizando Seurat, desde la carga de datos crudos hasta la identificación y anotación de tipos celulares. Este enfoque paso a paso establece una base sólida para realizar análisis más complejos y promueve una comprensión profunda del potencial del scRNA-seq en el estudio de la heterogeneidad celular.

## 💻 5. Análisis de datos de scRNA-seq con Bioconductor en RStudio

Ahora, se llevará a cabo otro ejercicio práctico centrándose únicamente en las etapas de **preprocesamiento y exploración inicial de datos** de scRNA-seq utilizando herramientas del proyecto **Bioconductor** en el entorno de **RStudio**. 

Al igual que el ejercicio anterior, esta guía es una adaptación educativa del material original [*Single Cell RNA-seq Analysis with Bioconductor*](https://www.singlecellcourse.org/introduction-to-rbioconductor.html)*, realizado por Alexander Predeus, Hugo Tavares, Vladimir Kiselev, y colaboradores asociados con el Instituto Sanger y la Universidad de Cambridge. El contenido ha sido ajustado con fines didácticos para facilitar la comprensión de este tipo de análisis bioinformático para estudiantes principiantes.

Es esencial aclarar que este ejercicio no abarca todo el flujo de trabajo, ya que su propósito es entender cómo se preparan y exploran los datos con la paquetería *Bionconductor*.

####  ¿Qué datos se van a estudiar?

El conjunto de datos que se utilizarán provienen de células madre pluripotentes inducidas (iPSC) generadas a partir de tres individuos diferentes realizado por [Tung et al. (2017)](https://www.nature.com/articles/srep39921) en la Universidad de Chicago. En este caso, los datos ya se encuentran procesados (ya pasaron por las fases de alineamiento y cuantificación) y consisten en dos archivos principales que se explicarán más adelante.

### 1. Preparación del entorno y carga del conjunto de datos *Tung*

#### 1.1 Antes de empezar

Es necesario importar los datos, para ello:

1. Abre este enlace del curso: [scRNA.seq.course](https://github.com/flying-sheep/scRNA.seq.course/tree/master/tung)
2. Busca la carpeta **tung**, ahí encontrarás dos archivos:
- `molecules.txt`: la matriz de recuentos (genes × células).
- `annotation.txt`: metadatos sobre cada célula.
3. Descarga ambos archivos.
>Tip: Se sugiere crear un proyecto específico y organizar los archivos en una carpeta bien estructurada (por ejemplo, una carpeta llamada “scRNA-seq_ej2”) ya que ayuda a mantener la reproducibilidad y el orden.


Lo siguiente es instalar y cargar las librerías necesarias:

```r
install.packages("BiocManager")

BiocManager::install(c(
  "SingleCellExperiment",
  "scater",
  "scran",
  "igraph"
))

library(SingleCellExperiment)
library(scater)
library(scran)
library(igraph)
```

**¿Para qué sirve cada librería?**

- `SingleCellExperiment`: contenedor de los datos.
- `scater`: control de calidad y la visualización.
- `scran`: es la biblioteca para el análisis estadístico.
- `igraph`: librería general de teoría de redes y grafos.
  
**Resultado esperado:**
- Si aparece el nombre de las librerías en la consola quiere decir que todo está correcto.
- Si hay error significa que las librerías no se instalaron adecuadamente.

#### 1.2 Leer los datos en R

Para importar los dos archivos descargados anteriormente al entorno de R, se utiliza la función `read.table()` que se encarga de leer archivos de texto estructurados en formato tabular. 

Cuando se ejecuta:

```r
tung_counts <- read.table("ruta/a/tus/datos/", sep = "\t", header = TRUE, row.names = 1)
tung_annotation <- read.table("ruta/a/tus/datos/", sep = "\t", header = TRUE)
```

Se le dice a R que lea un archivo cuyos valores están separados por tabuladores `sep = "\t"`. El argumento `header = TRUE` revela que la primera fila del archivo contiene los nombres de las variables descriptivas asociadas a cada célula.

**Resultado esperado:**

Se crean dos objetos en el *Environment*: 

- `tung_counts`: data frame que contiene la matriz de conteos.

<img width="921" height="412" alt="image" src="https://github.com/user-attachments/assets/fd02633a-cf6d-49b3-adb2-3ee87ee02e97" />

- `tung_annotation`: data frame que contiene la información sobre cada célula (por ejemplo, individuo, lote, id de la muestra, etc.).

<img width="755" height="597" alt="image" src="https://github.com/user-attachments/assets/95596572-dc24-46f4-b3c3-2423d6996800" />

#### 1.3 Crear el objetivo `SingleCellExperiment`

El siguiente paso es crear el objeto estándar de *Bioconductor* `SingleCellExperiment`, en donde se almacena en un solo lugar las matrices de conteo, los metadatos de las células (columnas) y los metadatos de los genes (filas). Esta estructura garantiza que cada columna de la matriz de expresión esté correctamente asociada con su información descriptiva. Además, permite almacenar múltiples versiones de los datos (por ejemplo, conteos crudos y datos transformados).

```r
tung <- SingleCellExperiment(
  assays = list(counts = as.matrix(tung_counts)),
  colData = tung_annotation)
```

El componente `assays` guarda una o más matrices de cuantificación de expresión y el argumento `colData` se encarga de reunir la información relacionada con cada célula. Es primordial verificar que cada fila del `colData` debe coincidir exactamente con una columna de la matriz de conteos; de lo contrario, el objeto no tendrá coherencia.

Para eliminar las tablas iniciales debido a que ya no son necesarias:

```r
rm(tung_counts, tung_annotation)
```

Para visualizar el contenido del objeto `tung`:

```r
tung
```

<img width="956" height="318" alt="image" src="https://github.com/user-attachments/assets/79badac9-9d26-4774-a0fd-6083b1fc11a5" />

Aparece un resumen que muestra:
- Clase del objeto: `SingleCellExperiment`.
- Dimensiones: 19,027 genes (filas) y 864 células (columnas).
- assays(1): counts, contiene una sola matriz de expresión llamada *counts*.
- rownames(19027): identificadores de los genes (IDs Ensembl como ENSG...).
- colnames(864): cada columna representa una célula individual. Los nombres codifican individuo, réplica y pozo.
- colData names(5): hay 5 variables asociadas a cada célula: individuo, réplica, pozo, lote y id.
- metadata(0): no hay metadatos adicionales.
- reducedDimNames(0): no hay reducciones de dimensionalidad calculadas (sin PCA, UMAP o t-SNE).

**Resultado esperado:**

El objeto SingleCellExperiment resultante `tung` se almacena en el *Environment*. 

<img width="921" height="388" alt="image" src="https://github.com/user-attachments/assets/a341a819-ca6e-4056-bf80-a51fbc551bcc" />


#### Algunos comados para explorar el objeto:

```r
dim(assay(tung))   # Muestra las dimensiones de la matriz
assay(tung)[1:5, 1:5] # Muestra una submatriz
assayNames(tung)  # Muestra qué matrices contiene el objeto
table(tung$batch)  # Muestra el número de células por lote experimental
colData(tung)      # Muestra los metadatos de las células
rowData(tung)     # Muestra los metadatos de los genes
```

### 2. Transformación logarítmica

Los datos de conteo no se distribuyen de manera normal. Muestran una gran variabilidad y una gran cantidad de ceros. Para facilitar los análisis posteriores, se emplea una transformación logarítmica. La función `counts(tung)` extrae la matriz original, el +1 evita problemas con el logaritmo de cero, y `log2()` aplica la transformación en base 2. Después de hacer esta transformación, los valores extremos se reducen y la distribución se vuelve mucho más fácil de manejar.

```r
assay(tung, "logcounts") <- log2(counts(tung) + 1)
```

Para visualizar las primeras 10 filas y 4 columnas de la nueva matriz:

```r
logcounts(tung)[1:10, 1:4]
```

**Resultado esperado:**

El objeto `tung` ahora tiene dos formas diferentes de representar los datos: la matriz de expresión cruda y la matriz transformada, lo que permite comparar ambas representaciones.

<img width="976" height="287" alt="image" src="https://github.com/user-attachments/assets/a75697eb-3665-45df-a55b-860ca4327a42" />

### 3. Visualización exploratoria

Una vez que se han importado y almacenado los datos de expresión y metadatos en el objeto `SingleCellExperiment`, es relevante explorar las características del *dataset*. La visualización inicial facilita evaluar la calidad de los datos, comprender patrones biológicos y tomar decisiones informadas para los estudios posteriores.

Para crear estos gráficos se utiliza principalmente la librería `ggplot2`, complementada por funciones auxiliares específicas de *Bioconductor*, como las del paquete `scater`.

Un gráfico `ggplot2` se construye a partir de:
1. Un data.frame que contiene los datos a representar.
2. Estética: asignación de las variables del data.frame a los ejes, colores, formas, etc (con la función `aes()`).
3. Geometrías (`geom_`) que definen el tipo de representación, por ejemplo puntos (`geom_point()`), violines (`geom_violin()`), líneas, etc.

#### Ejemplos

Para ver cómo se distribuyen los conteos totales por célula según el lote de procesamiento, primero se obtienen los recuentos totales por celda con la función `colSums()`, y luego se extrae la información del objeto `SingleCellExperiment` y se convierte en un data.frame. Después, se puede utilizar un gráfico de violines para ilustrar las variaciones entre los diferentes grupos.

```r
colData(tung)$total_counts <- colSums(counts(tung))

cell_info <- as.data.frame(colData(tung))

ggplot(data = cell_info, aes(x = batch, y = total_counts)) +
  geom_violin(fill = 'brown') + theme_bw() + 
  theme(axis.text.x = element_text(angle = 45, vjust = 1, hjust=1))
```

**Resultado esperado:**

Cada violín representa la distribución de conteos en un lote. En el eje x se encuentran los distintos grupos de células, y en el eje y el número total de conteos por célula. La altura del violín indica el rango de valores (desde los más bajos hasta los más altos). El ancho del violín en cada punto refleja la densidad de datos:
- Zonas más anchas sugieren que hay más células con ese número de conteos.
- Zonas más estrechas sugieren que hay menos células con esos valores.

<img width="1233" height="708" alt="totalcounts" src="https://github.com/user-attachments/assets/e3269fd7-d727-4bf4-aaf2-494e7a104429" />


También se puede evitar la manipulación manual de los datos utilizando la función `ggcells()` de *scater*, que se encarga de extraer automáticamente la información necesaria del objeto `SingleCellExperiment`.

```r
ggcells(tung, aes(x = batch, y = total_counts)) + 
  geom_violin(fill = 'orange') + theme_bw() + 
  theme(axis.text.x = element_text(angle = 45, vjust = 1, hjust=1))
```

Si deseas visualizar la expresión de un gen en específico entre condiciones o grupos. Con `scater` y `ggcells()` se puede realizar especificando qué matriz de expresión usar (por ejemplo *logcounts*):

```r
ggcells(tung, aes(x = batch, y = ENSG00000198938), exprs_values = "logcounts") + 
  geom_violin(fill = 'coral2') + theme_bw() + 
  theme(axis.text.x = element_text(angle = 45, vjust = 1, hjust=1))
```
**Resultado esperado:**

Se muestra un diagrama de violín que representa la distribución de la expresión del gen *ENSG00000198938* (en valores transformados) en los distintos grupos. En el eje x se observan los grupos y en el eje y los niveles de expresión del gen. La forma de cada violín indica cómo se distribuyen los valores dentro de cada *batch*: las zonas más anchas representan mayor concentración de células con ese nivel de expresión, mientras que las zonas estrechas indican menor frecuencia.

<img width="1292" height="708" alt="Rplot" src="https://github.com/user-attachments/assets/9e4d8355-0fc3-41be-8ec2-e05d7f2ff207" />


### ▶ Formas de manipular datos en un objeto `SingleCellExperiment`

Por último, se presenta una tabla en donde se resume los principales operadores o funciones para explorar y manejar los datos que integran un objeto `SingleCellExperiment`.

| Elemento / Acción | Descripción | Ejemplo |
|-------------------|-------------------|----------|
| **assay** | Contiene una o más matrices de expresión | `assay(sce, "counts")` |
| **rowData** | Información sobre los genes (filas) | `rowData(sce)` |
| **colData** | Información sobre las células (columnas) | `colData(sce)` |
| **reducedDim** | Representaciones en dimensiones reducidas (PCA, UMAP, etc.) | `reducedDim(sce, "PCA")` |
| **Acceso a componentes** | Se entra usando funciones con el mismo nombre del componente | `assay()`, `rowData()`, `colData()` |
| **Añadir o modificar datos** | Se usa el operador `<-` para agregar nuevas matrices o metadatos | `assay(sce, "logcounts") <- log2(counts(sce) + 1)` |
| **Resúmenes de matrices** | Permiten explorar propiedades globales de los datos | `rowSums()`, `colSums()`, `rowMeans()`, `colMeans()` |
| **Subconjunto condicional** | Se pueden combinar métricas con operadores lógicos para filtrar datos | `sce[, colSums(counts(sce)) > 1000]` |
| **Visualización** | Permite generar gráficos para representar los datos | `ggcells()`, `ggplot` |


### 📝 Para cerrar
Este ejercicio se enfoca en la fase de preparación y exploración inicial de los datos. Las etapas que se describen en este ejercicio son cruciales dado que si no se realiza un preprocesamiento adecuado, los análisis posteriores pueden dar lugar a resultados engañosos.

## 📖 Bibliografía

*Haque, A., Engel, J., Teichmann, S. A., & Lönnberg, T. (2017). A practical guide to single-cell RNA-sequencing for biomedical research and clinical applications. Genome Medicine, 9(1), 75. https://doi.org/10.1186/s13073-017-0467-4*

*Jovic, D., Liang, X., Zeng, H., Lin, L., Xu, F., & Luo, Y. (2022). Single‐cell RNA sequencing technologies and applications: A brief overview. Clinical And Translational Medicine, 12(3), e694. https://doi.org/10.1002/ctm2.694*







