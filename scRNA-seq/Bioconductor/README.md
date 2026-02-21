# 💻 Análisis de datos de scRNA-seq con Bioconductor en RStudio

Ahora, se llevará a cabo otro ejercicio práctico centrándose únicamente en las etapas de **preprocesamiento y exploración inicial de datos** de scRNA-seq utilizando herramientas del proyecto **Bioconductor** en el entorno de **RStudio**. 

Al igual que el ejercicio anterior, esta guía es una adaptación educativa del material original [*Single Cell RNA-seq Analysis with Bioconductor*](https://www.singlecellcourse.org/introduction-to-rbioconductor.html)*, realizado por Alexander Predeus, Hugo Tavares, Vladimir Kiselev, y colaboradores asociados con el Instituto Sanger y la Universidad de Cambridge. El contenido ha sido ajustado con fines didácticos para facilitar la comprensión de este tipo de análisis bioinformático para estudiantes principiantes.

Es esencial aclarar que este ejercicio no abarca todo el flujo de trabajo, ya que su propósito es entender cómo se preparan y exploran los datos con la paquetería *Bionconductor*.

###  ¿Qué datos se van a estudiar?

El conjunto de datos que se utilizarán provienen de células madre pluripotentes inducidas (iPSC) generadas a partir de tres individuos diferentes realizado por [Tung et al. (2017)](https://www.nature.com/articles/srep39921) en la Universidad de Chicago. En este caso, los datos ya se encuentran procesados (ya pasaron por las fases de alineamiento y cuantificación) y consisten en dos archivos principales que se explicarán más adelante.

## 1. Preparación del entorno y carga del conjunto de datos *Tung*

### 1.1 Antes de empezar

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

### 1.2 Leer los datos en R

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

### 1.3 Crear el objetivo `SingleCellExperiment`

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


### Algunos comados para explorar el objeto:

```r
dim(assay(tung))   # Muestra las dimensiones de la matriz
assay(tung)[1:5, 1:5] # Muestra una submatriz
assayNames(tung)  # Muestra qué matrices contiene el objeto
table(tung$batch)  # Muestra el número de células por lote experimental
colData(tung)      # Muestra los metadatos de las células
rowData(tung)     # Muestra los metadatos de los genes
```

## 2. Transformación logarítmica

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

## 3. Visualización exploratoria

Una vez que se han importado y almacenado los datos de expresión y metadatos en el objeto `SingleCellExperiment`, es relevante explorar las características del *dataset*. La visualización inicial facilita evaluar la calidad de los datos, comprender patrones biológicos y tomar decisiones informadas para los estudios posteriores.

Para crear estos gráficos se utiliza principalmente la librería `ggplot2`, complementada por funciones auxiliares específicas de *Bioconductor*, como las del paquete `scater`.

Un gráfico `ggplot2` se construye a partir de:
1. Un data.frame que contiene los datos a representar.
2. Estética: asignación de las variables del data.frame a los ejes, colores, formas, etc (con la función `aes()`).
3. Geometrías (`geom_`) que definen el tipo de representación, por ejemplo puntos (`geom_point()`), violines (`geom_violin()`), líneas, etc.

### Ejemplos

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


## ▶ Formas de manipular los datos de un objeto `SingleCellExperiment`

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
| **Visualización** | Permite generar gráficos para represtar los datos | `ggcells()`, `ggplot` |


## 📝 Para cerrar
Este ejercicio se enfoca en la fase de preparación y exploración inicial de los datos. Las etapas que se describen en este ejercicio son cruciales dado que si no se realiza un preprocesamiento adecuado, los análisis posteriores pueden dar lugar a resultados engañosos.

