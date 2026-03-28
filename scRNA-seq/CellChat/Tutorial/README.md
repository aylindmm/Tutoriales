# 💻 Análisis de comunicación celular con CellChat en R

En este tutorial aplicaremos **CellChat** paso a paso para analizar la comunicación célula-célula a partir de datos de scRNA-seq, siguiendo un flujo de trabajo completo en **R**. Aquí trabajaremos directamente con las funciones de la herramienta para **construir e interpretar redes de comunicación intercelular**.

Este tutorial está basado en el material original desarrollado por los autores de CellChat y disponible en su [repositorio oficial de GitHub](https://github.com/sqjin/CellChat
). Nuestro objetivo reemplazarlo, sino **adaptarlo, traducirlo y explicarlo** de una manera más accesible. Buscamos que comprendas **qué está pasando en cada etapa del análisis**, para que después puedas aplicar estos conocimientos en tus propios proyectos y no solo reproducir un flujo de trabajo.

## 🔧 Antes de empezar
Antes de comenzar con el análisis en CellChat, es importante contar con algunos requisitos básicos para asegurar que el flujo de trabajo funcione correctamente.

1. Disponer de R y un entorno de trabajo como RStudio.
2. Contar con conocimientos básicos de R y análisis de scRNA-seq.
3. Se requiere un objeto de scRNA-seq previamente procesado. Es vital tener la anotación de tipos celulares, puesto que CellChat utiliza estas etiquetas para inferir la comunicación entre grupos celulares.

## ⚙️ Parte 0. Instalación de CellChat
Para comenzar, es primordial instalar CellChat y algunas dependencias en R. Dado que CellChat no siempre está disponible directamente en CRAN, se instala desde GitHub.

Primero, asegúrate de tener instalado el paquete devtools (o remotes), que permite instalar paquetes desde GitHub:
```
install.packages("devtools")
library(devtools)
```
Luego, puedes instalar CellChat con el siguiente comando:
```
devtools::install_github("jinworks/CellChat")
```
Durante la instalación, es posible que se instalen automáticamente varias dependencias. Si ocurre algún error, verifica que tengas actualizadas tus versiones de R y de los paquetes requeridos.

### 0.1  Cargar librerías
Una vez instalado, carga CellChat junto con otros paquetes necesarios:

 - `patchwork`: se emplea para combinar gráficos en una sola figura.
 - `Cellchat`: permite usar todas sus funciones para explorar la comunicación celular en datos de scRNA-seq.
 - `stringsAsFactors = FALSE`: Evita que los strings se conviertan en factores al crear dataframes.

```
library(CellChat)
library(patchwork)
options(stringsAsFactors = FALSE)
```
## 📂 Parte 1. Input y preparación de los datos

Para este tutorial utilizaremos un conjunto de datos de scRNA-seq que ya ha sido procesado y anotado. Este *dataset* combina información sobre piel humana en 2 condiciones biológicas: LS (piel lesionada) y NL (piel normal).

En el tutorial original, estos datos se proporcionan como un objeto de Seurat llamado `data_humanSkin`, el cual contiene la matriz de expresión génica y la anotación de tipos celulares, que son los 2 archivos que Cellchat necesita para trabajar.

### 1.1 Cargar los datos 

Comenzamos cargando el objeto `data_humanSkin`en R. Para ello:

1. Descarga el archivo [data_humanSkin_CellChat.rda](https://ndownloader.figshare.com/files/25950872).
2. Ve al panel **Files**.
![alt text](image-1.png)
3. Haz clic en **Upload**.
![alt text](image-2.png)
4. Selecciona el archivo previamente descargado y haz clic en **OK**
![alt text](image.png).
5. Cuando lo encuentres en el panel Files, haz clic en el archivo.
6. Luego te aparecerá la siguiente ventana, haz clic en **Yes**.
![alt text](image-3.png)

Otra opción es utilizar la ruta de acceso al arhivo:

```
load("/ruta/completa/a/data_humanSkin_CellChat.rda")
```
📤 **Salida esperada:** El objeto `data_humanSkin` se encuentra disponible en el entorno de trabajo. El cual incluye: 
- Una matriz de expresión génica (`data`).
- Un data frame con metadatos (`meta`).

#### 1.1.1 Extraer lamatriz de expresión y los metadatos

Aquí se separan los dos componentes principales:
- `data.input`: matriz de expresión.
- `meta`: información de cada célula (tipo celular, condición, etc.)

```
data.input = data_humanSkin$data
meta = data_humanSkin$meta
```

📤 **Salida esperada:**: dos objetos listos para manipulación.

#### 1.1.2 Seleccionar las células de interés

En este paso se filtran las células que pertenecen a la condición "LS" (en este caso, enfermedad).
```
cell.use = rownames(meta)[meta$condition == "LS"]
```
📤 **Salida esperada:**: `cell.use`: vector con los nombres (IDs) de las células seleccionadas.

#### 1.1.3 Filtrar la matriz de expresión

Se conservan únicamente las columnas (células) que cumplen la condición seleccionada.

```
data.input = data.input[, cell.use]
```
📤 **Salida esperada:**: Matriz de expresión reducida (solo células LS).

#### 1.1.4 Filtrar los metadatos

Se ajusta el data frame de metadatos para que coincida con las células filtradas.

```
meta = meta[cell.use, ]
```
📤 **Salida esperada:**: `meta` contiene solo la información de las células seleccionadas.

#### 1.1.5 Verificar los tipos celulares

Este comando permite visualizar los tipos celulares presentes en los datos.

```
unique(meta$labels)
```
📤 **Salida esperada:**: Un vector con las etiquetas únicas.


### 1.2 Crear el objeto CellChat
Una vez cargados los datos, el siguiente paso es convertirlos en un **objeto CellChat**, que será la estructura donde se almacenará todo el análisis.
```
cellchat <- createCellChat(object = data.input, meta = meta, group.by = "labels")
```
- `object = data.input` es el objeto Seurat.
- `group.by = "labels` es la columna que contiene los tipos celulares.

📤 **Salida esperada:**: Crea un objeto llamado `Cellchat` que contiene los datos de expresión organizados por grupo celular y los metadatos, por ahora.

### 1.3 Añadir la base de datos

Ahora se selecciona la base de datos para humano de interacciones ligando-receptor. Si fuera para ratón sería `CellChatDB.mouse`
```
CellChatDB <- CellChatDB.human
```
📤 **Salida esperada:**: No verás un cambio visual, pero ahora el objeto contiene una inmesa listas de ligandos, receptores, complejos y cofactores.

Puedes verificarlo con:
```
showDatabaseCategory(CellChatDB)
```
📤 **Salida esperada:**:

### 1.3 Filtrar genes relevantes

Ahora vamos a filtrar genes que no participan en señalización, lo que reduce el ruido en el análisis.

```
cellchat <- subsetData(cellchat)
```
📤 **Salida esperada:**: Internamente disminuye el número de genes analizados y se optimiza el rendimiento.

## 🗪 Parte 2. Inferencia de la comunicación celular
El objetivo de esta sección es transformar los datos de expresión génica en una representación de cómo se relacionan las células entre sí. Para ello, **calcula una probablidad de interacción para cada par ligando-receptor** entre los distintos grupos celulares.

**¿Cómo calcula esa probabilidad?** Utiliza la expresión de los genes (qué tanto se expresa el ligando en un grupo celular y el receptor en otro). Así como CellChatDB que indica qué ligandos interactúan con cuáles receptores.

El modelo matemático que implementa es la **Ley de acción de masas**, en términos sencillos significa que la probabilidad de comunicación aumenta cuando tanto el ligando como el receptor están altamente expresados. Después, CellChat aplica un *permutation test* (una prueba estadística basada en permutaciones) para evaluar si la interacción observada es significativa o podría explicarse por azar. Solo se conservan interacciones significativas.

Otro punto importante es **cómo se calcula la expresión promedio de los genes en cada grupo celular**, ya que esto afecta directamente a cuántas interacciones se detectan. Por defecto, CellChat utiliza un método llamado *trimean*, que es una forma robusta de promedio menos sensible a valores extremos. Este método tiende a ser más estricto, produciendo menos interacciones, pero con mayor confiabilidad. De hecho, si menos del 25% de las células en un grupo expresan un gen, el *trimean* lo considera prácticamente como no expresado, lo que reduce la detección de señales débiles o provenientes de poblaciones pequeñas.

Sin embargo, existen métodos alternativos, como el *truncated mean*, donde solo se eliminan los valores más extremos (por ejemplo, el 10%). Este enfoque es más permisivo y puede detectar más interacciones, incluyendo señales más sútiles. Además, CellChat proporciona funciones como `computeAveExpr` para explorar directamente la expresión promedio de genes específicos de interés.

Por otro lado, cuando se analizan datos de células individuales no separadas previamente (*unsorted*), CellChat también puede considerar el tamaño de cada población celular. Esto se basa en la idea de que poblaciones más abundantes pueden generar señales colectivamente más fuertes que poblaciones raras. Al activar esta opción `(population.size = TRUE)`, el modelo ajusta las probabilidades de interacción tomando en cuenta la proporción de células en cada grupo, lo cual puede hacer el análisis más realista, aunque también puede disminuir la visibilidad de interacciones provenientes de poblaciones pequeñas.

### 2.1 Cálculo de la probabilidad de comunicación

CellChat calcula la probabilidad de comunicación entre todos los grupos celulares usando la función `computeCommunProb`. Esto significa que, para cada par de tipos celulares, el programa evalúa qué tan probable es que exista señalización a través de todos los pares ligando-receptor conocidos.

Usa por defecto el método `triMean` para resumir la expresión génica por grupo.

```
cellchat <- computeCommunProb(cellchat)
```

📤 **Salida esperada:**: El resultado de esta función se guarda en el interior del objeto `CellChat`. Dentro de `object@options$parameter` se almacenan las probabilidades de comunicación y los parámetros usados en el cálculo.

>Nota: Si hay vías de señalización conocidas que esperas ver pero no aparecen, puedes cambiar el método de promedio a uno menos estricto, como *truncated mean*.

### 2.1.2 Filtrado de interacciones poco confiables

Después se aplica un paso de control de calidad con la función `filterCommunication` al eliminar interacciones que involucran grupos celulares con pocas células.
```
cellchat <- filterCommunication(cellchat, min.cells = 10)
```
### 2.2 Extraer la red de comunicación celular inferida

La función `subsetCommunication` se utiliza para extraer la red de comunicación celular en forma de tabla (*data frame*). Hasta ahora, toda la información está guardada dentro del objeto `CellChat`, pero no es fácil de ver directamente. Esta función hace posible convertir esas interacciones en algo más manejable, donde puedes ver claramente: qué célula envía la señal, cuál la recibe, qué ligando-receptor está involucrado y qué tan fuerte es la interacción.

Al usar:

```
df.net <- subsetCommunication(cellchat)
```
obtienes todas las interacciones inferidas a nivel de ligando-receptor. Es decir, cada fila representa una interacción específica. Si en lugar de eso usas `slot.name = "netP"`, entonces accedes a las interacciones a nivel de vías de señalización completas, no de pares individuales. Esto es útil porque muchas veces lo que te interesa no es un gen en particular, sino rutas.

También puedes filtrar la información según lo que te interese. 

Si especificas `sources.use` y `targets.use`: 
```
df.net <- subsetCommunication(cellchat, sources.use = c(1,2), targets.use = c(4,5))
```
Estás seleccionando qué tipos celulares envían y cuáles reciben señales, lo cual es útil si quieres enfocarte en interacciones específicas.

De forma similar, puedes filtrar por tipo de señalización usando el argumento `signaling`:
```
df.net <- subsetCommunication(cellchat, signaling = c("WNT", "TGFb"))
```
Lo que te permite quedarte solo con interacciones asociadas a vías específicas que te llamen la atención.

### 2.3 Inferir la comunicación celular a nivel de vía de señalización

Mediante la función `computeCommunProbPathway`, CellChat pasa de interacciones individuales a una visión más general. Lo que hace es agrupar todas las interacciones ligando-receptor que pertenecen a una misma vía de señalización y calcular una probabilidad global para esa vía. Es decir, en lugar de ver interacciones aisladas, puedes ver qué tan activa está una ruta completa de comunicación entre células.
```
cellchat <- computeCommunProbPathway(cellchat)
```
Es esencial entender cómo se organiza esta información dentro del objeto `CellChat`. Las interacciones a nivel de ligando-receptor se guardan en `net`, mientras que las interacciones a nivel de vías de señalización se guardan en `netP`.

### 2.4 Calcular la red de comunicación agregada entre celdas

Después de haber calculado todas las interacciones célula-célula, la función `aggregateNet` se utiliza para resumir esa información en una red más simple. En lugar de ver miles de pares ligando-receptor individuales, aquí CellChat agrupa todo y calcula, para cada par de tipos celulares, dos cosas principales:

1. El número de interacciones que existen entre ellos, y
2. La fuerza total de comunicación (sumando las probabilidades de todas las interacciones).

Esto es muy útil porque te permite responder preguntas más globales, como: ¿qué tipo celular es el que más se comunica? o ¿qué interacción entre células es más fuerte en general? Además, si te interesa solo un subconjunto de células, puedes usar `sources.use` y `targets.use` para enfocarte en interacciones específicas.

## Parte 3. Visualización y análisis