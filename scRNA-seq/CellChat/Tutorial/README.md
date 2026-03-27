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
Una vez cargados los datos, el siguiente paso es crear un **objeto de CellChat**, que será la base donde se almacenará todo el análisis.
```
cellchat <- createCellChat(object = data.input, meta = meta, group.by = "labels")
```
- `object = data.input` es el objeto Seurat.
- `group.by = "labels` es la columna que contiene los tipos celulares.

📤 **Salida esperada:**: Crea un objeto llamado `Cellchat` que contienee los datos de expresión organizados por grupo celular y los metadatos, por ahora.

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
📤 **Salida esperada:**: No imprime mucho en consola, pero internamente disminuye el número de genes analizados y optimiza el rendimiento.

## Parte 2. Inferencia de la comunicación celular


## Parte 3. Visualización y análisis