# 👨🏻‍💻 Análisis de datos de secuenciación de ARN unicelular (scRNA-seq)

## 🧪 1. ¿Qué es scRNA-seq?

La secuenciación de ARN de célula única (**scRNA-seq**) es una tecnología reciente que permite medir la expresión génica a nivel de cada célula individual. A diferencia del RNA-seq masivo (*bulk*), que mide el promedio de expresión génica en una población de células, el scRNA-seq permite capturar la heterogeneidad biológica, analizando la diversidad de tipos celulares en un tejido complejo e identificando estados celulares nuevos.

## 🕵 2. Flujo de trabajo general

La metodología de scRNA-seq puede dividirse en dos etapas principales complementarias e interdependientes:

1. <mark>**Fase experimental**</mark>: incluye todos los procedimientos que se llevan a cabo desde la obtención del material biológico hasta la generación de los datos de secuenciación. 

   1.1 **Obtención y preparación de la muestra**: consiste en obtener una suspensión de células individuales viables a partir de un tejido o población celular. Para lograrlo, los tejidos suelen someterse a procesos de disociación mecánica y/o enzimática. Posteriormente, de forma opcional, se pueden seleccionar células (por ejemplo, basándose en ...)

   1.2 **Aislamiento de células individuales**: involucra asegurar que cada célula sea procesada de forma independiente. Este aislamiento puede realizarse mediante diversas tecnologías, como sistemas de nanopocillos, microgotas o microplacas, cada una con sus ventajas y limitaciones en cuanto al número de células que se pueden analizar, la profundidad de secuenciación y la resolución transcriptómica.

   1.3 **Captura del ARN y etiquetado molecular**: las células son lisadas y su ARN mensajero (ARNm) es capturado y marcado molecularmente. En este proceso, se añaden códigos de barras celulares (*bardcodes*) que permiten identificar de qué célula proviene cada transcrito, así como identificadores moleculares únicos (UMI, *Unique Molecular Identifier*), que tienen la función de diferenciar las moléculas originales de las copias que se generan durante la amplificación.

   1.4 **Retrotranscripción y amplificación**: El ARNm se convierte en ADN complementario (ADNc) mediante retrotranscripción, y luego se amplifica para asegurarse de tener suficiente material genético. Esta amplificación, se puede llevar a cabo mediante PCR o transcripción *in vitro*.

   1.5 **Construcción de librerías y secuenciación**: el ADNc amplificado se utiliza para construir librerías de secuenciación que son procesadas mediante plataformas de secuenciación masiva.

2. <mark>**Fase computacional**</mark>: comienza una vez que se han generado los datos de secuenciación, se busca transformar los datos crudos en información biólogica que se pueda analizar.

   2.1 **Preprocesamiento**: las lecturas pasan por un procesamiento primario que incluye asignar cada lectura a su célula de origen usando los bardcodes, el alineamiento o pseudoalineamiento a un genoma o transcriptoma de referencia, y el conteo de las moléculas con los UMIs. Al final de este proceso, se genera una matriz de expresión génica, donde las filas representan genes y las columnas representan células individuales.

   2.2 **Control de calidad**: tiene la finalidad de identificar y eliminar células dañadas, dobletes o multipletes, así como restos celulares. Se basa en métricas como el número de genes detectados por célula, el número total de transcritos y la proporción de ARN mitocondrial.

   2.3 **Normalización**: su propósito es hacer comparables las células entre sí, corrigiendo diferencias debidas a la profundidad de secuenciación u otras fuentes de variación técnica. También, puede incluir la corrección de efectos de lote (*batch effects*) cuando los datos provienen de múltiples experimentos o condiciones.

   2.4 **Selección de genes variables**: se identifican aquellos genes que presentan una variabilidad significativa entre células y que son más útiles para distinguir diferentes tipos o estados celulares.

   2.5 **Reducción de dimensionalidad**: dado que la matriz de expresión génica tiene una alta dimensionalidad, se aplican técnicas de reducción para representar los datos en un espacio más simple. Técnicas como el análisis de componentes principales (PCA, *Principal Component Analyisis*), UMAP ayudan a capturar las principales fuentes de variación, lo que a su vez facilita la exploración visual de los datos.

   2.6 **Agrupamiento**: su objetivo es identificar conjuntos de células con perfiles transcriptómicos similares.

   2.7 **Identificación de genes marcadores**: los grupos celulares obtenidos se caracterizan por la identificación de genes distintivos, que permiten asignar identidades celulares o estados funcionales a cada grupo. Para ello, es necesario integrar el conocimiento previo que ya existe en la literatura y en bases de datos especializadas.
   
En resumen, la fase experimental establece la calidad y el tipo de información disponible, mientras que la fase computacional se ocupa de cómo se analiza e interpreta dicha información.

## 🔎 3. Aplicaciones, ventajas y desventajas

La scRNA-seq permite abordar preguntas biológicas que requieren una resolución detallada, aunque también implica desafíos tanto técnicos como analíticos. En la tabla siguiente, se resumen sus principales aplicaciones, ventajas y limitaciones.

| Característica | Descripción |
| :--- | :--- |
| **Aplicaciones** |  Se utiliza para estudiar la heterogeneidad celular en tejidos complejos, permitiendo analizar procesos del desarrollo embrionario, respuesta inmune, cáncer y del sistema nervioso.|
| **Ventajas** | Alta resolución, identificación de poblaciones celulares nuevas, estudio de trayectorias. |
| **Desventajas** | Alto costo, mayor "ruido" estadístico, requiere procesamiento bioinformático complejo. |

## 📦 4. Paqueterías para análisis de scRNA-seq 

Para realizar un análisis de scRNA-seq en R la elección de las librerías es fundamental. Existen varias herramientas, sin embargo en este tutorial abordaremos las siguientes dos debido a que ambas permiten hacer todo el flujo, desde el control de calidad hasta la identificación de tipos celulares.

- **Seurat**: Es la más utilizada ya que agrupa todas las herramientas necesarias para procesar y visualizar los datos en un solo lugar. Utiliza un objeto `Seurat` que organiza los datos de conteo, los metadatos y el análisis dimensional. Es excelente gracias a su amplia documentación y versatilidad.

- **SingleCellExperiment (Bioconductor)**: Es un conjunto de librerías especializadas y rigurosas que se pueden combinar libremente para realizar análisis estadísticos más personalizados y profundos. Utiliza una estructura común llamada `SingleCellExperiment` (SCE).

## 💻 4. Análisis de scRNA-seq con Seurat en R

A continuación, se llevará a cabo un ejercicio práctico para aprender a realizar un análisis de un conjunto de datos reales de células individuales usando el paquete **Seurat** en RStudio.

Esta guía es una adaptación educativa del tutorial oficial de [Seurat “Guided Clustering Tutorial – PBMC 3K Dataset”](https://satijalab.org/seurat/articles/pbmc3k_tutorial), desarrollado por el Satija Lab. El contenido ha sido simplifaco con fines didácticos para facilitar la comprensión de este tipo de análisis bioinformático para estudiantes principiantes.

####  ¿De dónde provienen los datos?

Los datos que se utilizarán este tutorial provienen del conjunto PBMC3K, que incluye 2,700 células mononucleares de sangre periférica humana, secuenciadas utilizando la tecnología de 10x Genomics. 

### 1. Preparación del entorno y carga del conjunto de datos PBMC

#### 1.1 Antes de empezar

Es necesario instalar las siguientes librerías:

```r
library(Seurat)
library(dplyr)
library(patchwork)
```
**¿Qué hace cada librería?**
- `Seurat`: análisis de scRNA-seq
- `dplyr`: manipulación de datos
- `patchwork`: combinar gráficas

**Resultado esperado:**
- ✅ No aparece nada significa que todo está correcto
- ❌ Si hay error significa la librería no está instalada

#### 1.2 Leer los datos desde 10x Genomics

Para trabajar con los datos en R, primero necesitas leer los archivos que genera 10x Genomics. Seurat tiene una función llamada `Read10X` que se encarga de leer automáticamente los archivos que contienen la matriz de conteos, los nombres de los genes y los identificadores de las células, y los combina en una sola matriz que puedes manipular en R.

```r
pbmc.data <- Read10X(data.dir = "ruta/a/tus/datos/")
```
Esto carga la **matriz de conteos**, en donde:
- Filas = genes
- Columnas = células

**Resultado esperado:**
- ✅ No muestra ningún resultado en consola
- ✅ El objeto resultante `pbmc.data` se guarda en el entorno de trabajo

Una vez que hayas cargado los datos, puedes echar un vistazo a sus dimensiones para ver cuántos genes y cuántas células hay en el conjunto. Al usar la función `dim`, R te devuelve dos valores: el número de filas, que corresponde a los genes detectados, y el número de columnas, que representa el total de células analizadas. Esta información es útil para asegurarte de que los datos se hayan cargado correctamente antes de seguir con el análisis.

```r
dim(pbmc.data)
```
#### 1.3 Crear el objetivo `Seurat`

El siguiente paso es crear un objeto de tipo `Seurat`, que es una estructura especializada diseñada para almacenar tanto los datos de expresión génica como la información adicional necesaria para el análisis.

Para crear un objeto Seurat se utiliza la función `CreateSeuratObject`. El parámetro `projet` menciona el nombre del proyecto y `min.cells` asegura que solo se mantendrán aquellos genes que estén presentes en al menos tres células, lo que ayuda a eliminar genes que probablemente sean ruido técnico. Por otro lado, el parámetro `min.features` determina que solo se incluirán células que tengan al menos 200 genes detectados, descartando aquellas con muy poca información transcriptómica.

```r
pbmc <- CreateSeuratObject(counts = pbmc.data, project = "pbmc3k", min.cells = 3, min.features = 200)
```

**Resultado esperado:**
- ✅ RStudio muestra un mensaje indicando que se ha creado un objeto `Seurat`, junto con el número total de genes y células que cumplen con los criterios establecidos. Esto confirma que el objeto fue creado correctamente.

### 2. Control de calidad

Antes de analizar, debemos evaluar la calidad de las células incluidas en el conjunto de datos, ya que es común encontrar células dañadas o muertas, dobletes o multipletes, o tienen ARN degradado, las cuales pueden afectar la interpretación de los resultados si no se eliminan adecuadamente.

**Métricas más usadas**
- `nFeature_RNA`: Número total de genes detectados por célula
- `nCount_RNA`: Numero total de moléculas (UMIs) por célula
- `percent.mt`: Porcentaje de genes mitocondriales (genes MT-) por célula

Un alto porcentaje de ARN mitocondrial suele ser un signo de células que están bajo estrés o en proceso de morir. Para calcular esta métrica, Seurat utiliza la función `PercentageFeatureSet()` e identifica los genes mitocondriales buscando un patrón en sus nombres, que en humanos generalmente comienza con “MT-”, y luego calcula qué porcentaje representan en relación al total de genes expresados por célula.

Para calcular los genes mitocondriales:

```r
pbmc[["percent.mt"]] <- PercentageFeatureSet(pbmc, pattern = "^MT-")
```

Puedes visualizar estas métricas mediante gráficos de violín:

```r
VlnPlot(pbmc, features = c("nFeature_RNA","nCount_RNA","percent.mt"))
```

📊 Cómo interpretar las gráficas:
- Valores muy bajos quiere decir que son células de baja calidad
- Valores muy altos significa posibles dobletes
- Un % mitocondrial alto pueden ser células dañadas

Una vez evaluadas las métricas de calidad y visualizadas sus distribuciones, el siguiente paso es eliminar aquellas células que no cumplen con los criterios mínimos para un análisis confiable. Este proceso, conocido como **filtrado**, tiene como objetivo conservar solo las células que presentan perfiles de expresión representativos. Se lleva a cabo utilizando la función `subset` seleccionando únicamente las células que cumplen con los criterios establecidos.

En este ejercicio se eliminan células:
- Que expresan menos de 200 genes, ya que suelen ser células muertas, fragmentos celulares o resultado de errores técnicos.
- Que expresan más de 2,500 genes, ya que podrían ser dobletes (dos células que se capturaron como una sola durante la secuenciación).
- Cuyo porcentaje de genes mitocondriales supera el 5%, ya que un valor alto suele estar asociado con estrés celular o degradación del ARN.

```r
pbmc <- subset(pbmc, subset = nFeature_RNA > 200 & nFeature_RNA < 2500 & percent.mt < 5)
```

**Resultado esperado:**
- ✅ El objeto `Seurat` se actualiza de forma automática, eliminando las células que no cumplen con los filtros establecidos. 
- ✅ No se muestra ninguna salida en la consola, el número total de células que se almacenan en el objeto disminuye. 

### 3. Normalización de los datos

Después de filtrar los datos, el siguiente paso es normalizarlos. En el caso del scRNA-seq, diferentes células pueden tener distintas profundidades de secuenciación; es decir, algunas pueden tener más lecturas que otras, y esto puede deberse a razones técnicas. La normalización ayuda a ajustar estas diferencias, asegurando que las comparaciones entre células sean válidas.

Seurat realiza este proceso mediante la función `NormalizeData`, utiliando el método *LogNormalize* que:
1. Divide los conteos por el total de cada célula
2. Multiplica el resultado por 10,00
3. Aplica logaritmo para que sea más comparable entre células

**Resultado esperado:**
- ✅ No se muestra ninguna salida en la consola, sin embargo, los datos normalizados se guardan internamente.

Es importante mencionar que los datos crudos no se eliminan, sino que se conservan dentro del objeto `Seurat` por si se requieren después.

### 4. Detección de genes altamente variables

En un experimento de scRNA-seq, no todos los genes son igualmente útiles para diferenciar entre los distintos tipos de células. Muchos de ellos tienen niveles de expresión que son bastante similares en todas las células, lo que los hace poco útiles para analizar la variación celular. Por eso, Seurat se encarga de identificar un aquellos genes cuya expresión varía significativamente entre las células.

Este proceso se lleva a cabo mediante la función `FindVariableFeatures`, que examina la relación entre la media y la varianza de la expresión de cada gen, seleccionando aquellos que muestran una variabilidad mayor de lo que se esperaría.

En este caso, se seleccionan los 2,000 genes más variables del conjunto de datos.

```r
pbmc <- FindVariableFeatures(pbmc, selection.method = "vst", nfeatures = 2000)

# Identificar los 10 genes más variables
top10 <- head(VariableFeatures(pbmc), 10)

# Trazar características variables con y sin etiquetas
plot1 <- VariableFeaturePlot(pbmc)
plot2 <- LabelPoints(plot = plot1, points = top10, repel = TRUE)
plot1 + plot2
```

**Resultado esperado:**
- ✅ No imprime resultados directamente, sin embargo, se guarda internamente la lista de genes variables. 

### 5. Escalado de los datos
El escalado implica centrar y estandarizar los valores de expresión génica, asegurando que cada gen tenga una media igual a cero y una varianza igual a 1. Este proceso es fundamental para evitar que los genes con valores de expresión muy altos dominen el análisis. Se realiza mediante la función `ScaleData`, la cual trabaja sobre los genes previamente identificados como altamente variables.

```r
all.genes <- rownames(pbmc)
pbmc <- ScaleData(pbmc, features = all.genes)
```
**Resultado esperado:**
- ✅ No se genera una salida visible en la consola, sin embargo, los resultados se almacenan en `pbmc[["RNA"]]$scale.data`.

### 6. Análisis de componentes principales (PCA)
El análisis de componentes principales (PCA, por sus siglas en inglés) es una técnica estadística que ayuda a simplificar la complejidad de los datos puesto que convierte la expresión de muchos genes en un conjunto más pequeño de componentes que logran capturar la mayor parte de la variación entre las células.

Para llevar a cabo el PCA, se utiliza la función `RunPCA`, que requiere como entrada los datos escalados.

```r
pbmc <- RunPCA(pbmc, features = VariableFeatures(object = pbmc))
```
Existen diversas funciones para analizar y visualizar tanto las células como las características que conforman el PCA, como: `VizDimReduction()`, `DimPlot()` o `DimHeatmap()`.

```r
VizDimLoadings(pbmc, dims = 1:2, reduction = "pca")
```

```r
VizDimLoadings(pbmc, dims = 1:2, reduction = "pca")
```

```r
DimPlot(pbmc, reduction = "pca") + NoLegend()
```

```r
DimHeatmap(pbmc, dims = 1, cells = 500, balanced = TRUE)
```

```r
DimHeatmap(pbmc, dims = 1:15, cells = 500, balanced = TRUE)
```

Para decidir cuántos componentes principales incluir, a menudo se recurre a un gráfico conocido como `Elbow Plot`. Este gráfico ilustra la cantidad de varianza que cada componente principal explica. El punto en el que la curva comienza a aplanarse, conocido como “codo”, señala un número razonable de componentes a considerar, ya que a partir de ahí, la ganancia de información se vuelve mínima.

```r
ElbowPlot(pbmc)
```

En este ejemplo, se observa una inclinación o "codo" alrededor de los componentes principales 9 a 10, lo que indica que la mayor parte de la varianza se encuentra en los primeros 10. 

### 7. Agrupar las células (clustering)
Antes de agrupar las células, es necesario identificar qué células son similares entre sí. Esta similitud se determina por la distancia entre las células en el espacio de los componentes principales, ya que estos componentes capturan la información más relevante sobre la expresión génica. Para lograr esto, Seurat crea un grafo de vecinos más cercanos, donde cada célula se conecta con aquellas que tienen perfiles de expresión similares. Este grafo sirve es la base para el posterior agrupamiento de las células en clústeres. 

La función `FindNeighbors` se encarga de calcular estas relaciones utilizando los componentes principales seleccionados previamente (10 PC).

```r
pbmc <- FindNeighbors(pbmc, dims = 1:10)
```
En este comando, el argumento `dims = 1:10` indica que se utilizarán los primeros diez componentes principales para calcular la similitud entre células.

**Resultado esperado:**
- En la consola se muestran mensajes que indican que el grafo está siendo construido correctamente.

Ahora sí, sigue el **clustering* que es una técnica que ayuda a identificar grupos de células que tienen perfiles de expresión similares, lo que generalmente se relaciona con diferentes tipos o estados celulares. En Seurat, este proceso se lleva a cabo a través de algoritmos de detección de comunidades, como el método de Louvain.

Para realizar el agrupamiento de células, se utiliza la función `FindClusters`, que asigna a cada célula una etiqueta de clúster. Un aspecto clave de esta función es el parámetro `resolution`, que determina el nivel de detalle en el agrupamiento. Si se utilizan valores bajos, se obtienen pocos clústeres grandes, mientras que valores más altos generan un mayor número de clústeres más pequeños. Los clústeres se pueden encontrar utilizando la funcipon `Idents()`.

```r
pbmc <- FindClusters(pbmc, resolution = 0.5)
```
Seurat agrega esta información al objeto, asignando a cada célula un identificador (numérico) de clúster. Para mirar los identificadores de los grupos de las primeras 5 celdas:

```r
head(Idents(pbmc), 5)
```

**Resultado esperado:**
- ✅ RStudio imprime información indicando el número de clústeres identificados

### 8. Reducción dimensional no visual (UMAP/t-SNE)


### 9. Identificación de marcadores de cada cluster

### 10. Anotación biológica


## 💻 4. Análisis de scRNA-seq con Bioconductor en R

A continuación vamos a aprender a analizar datos de scRNA-seq utilizando **R** y **Bioconductor**. Este tutorial está directamente basado en el material original:

*Lun ATL et al. [*Single Cell RNA-seq Analysis with Bioconductor*](https://www.singlecellcourse.org/introduction-to-rbioconductor.html)*

### ¿Qué datos vamos a analizar?
Utilizaremos un conjunto de datos de células madre pluripotentes inducidas (iPSC) generado por [Tung et al. (2017)](https://www.nature.com/articles/srep39921) en la Universidad de Chicago.

