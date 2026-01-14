# 🔀 Alineamiento pareado de secuencias

## 📝 ¿Qué es?
El alineamiento pareado de secuencias (*pairwise alignment*) es una técnica clave en bioinformática que se utiliza para comparar directamente dos secuencias biológicas (ADN, ARN o proteínas) para identificar similitudes, homologías (genes o proteínas que comparten un ancestro común) o diferencias (mutaciones, inserciones y deleciones). Este tipo de alineamiento puede clasificarse en:

- **Alineamiento global**: Intenta alinear las secuencias completas de principio a fin.

  👉 Este método se utiliza para comparar secuencias de longitud similar y cuando se espera que estén relacionadas a lo largo de toda su extensión.

- **Alineamiento local**: Identifica regiones de alta similitud dentro de secuencias más largas.

  👉 Es ideal cuando las secuencias presentan similitud solo en regiones específicas como dominios conservados.

## ⚙️ Algoritmo de Needleman-Wunsch

### 📝 ¿Qué es?
El algoritmo de **Needleman-Wunsch** fue propuesto por Saul B. Needleman y Christian D. Wunschen en 1970. Es un método de alineamiento **global** cuyo objetivo es encontrar el mejor emparejamiento posible considerando la longitud completa de ambas secuencias.

Este algoritmo se basa en programación dinámica y sigue tres pasos principales:

1. **Inicialización de la matriz**: se construye una matriz donde las filas y columnas representan las secuencias a comparar. La primera fila y columna se inicializan con penalizaciones por espacios (*gaps*).
2. **Relleno de la matriz**: cada celda se calcula evaluando tres posibles movimientos (diagonal, arriba o izquierda), considerando coincidencias, sustituciones y penalizaciones por *gaps*.
3. **Trazado (*traceback*)**: se reconstruye el alineamiento óptimo siguiendo el camino de mayor puntuación desde la última celda de la matriz.

<img width="480" height="480" alt="image" src="https://github.com/user-attachments/assets/f3a4ceed-313b-4fd6-9ac4-30ab777009f4" />

## 🛠️ ¿Cómo se realiza un alineamiento global?

**EMBOSS Needle** es una herramienta web que implementa el algoritmo de **Needleman-Wunsch**, el cual realiza un alineamiento global entre dos secuencias biológicas. Este tipo de alineamiento compara las secuencias completas de principio a fin, introduciendo espacios (*gaps*) cuando es necesario para maximizar la similitud total.

### 🧩 Secuencias de ejemplo

>Protein_1  
MVLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHFDLSHGSAQVKGHGKKVADALTNAVAHVDDMPNALSALSDLHAHKLRVDPVNFKLLSHCLLVTLAAHLPAEFTPAVHASLDKFLASVSTVLTSKYR

>Protein_2  
MVLSGEDKSNIKAAWGKIGGHGAEYGAEALERMFASFPTTKTYFPHFDVSHGSAQVKGHGKKVADALASAAGHLDDLPGALSALSDLHAHKLRVDPVNFKLLSHCLLVTLASHHPADFTPAVHASLDKFLASVSTVLTSKYR

**🌐 Página web:**  
https://www.ebi.ac.uk/jdispatcher/psa/emboss_needle

### 👩🏻‍💻 Uso de la interfaz

1. Acceder al enlace de **EMBOSS Needle** desde el navegador web.
<img width="921" height="393" alt="image" src="https://github.com/user-attachments/assets/4e96ef44-547c-45f5-bfe3-89326ba03422" />

2. En la sección **`Input sequence`**, indicar el tipo de secuencia que se va a analizar.  
   En este caso, seleccionar la opción correspondiente a *Protein*, ya que las secuencias están compuestas por aminoácidos.
<img width="713" height="123" alt="image" src="https://github.com/user-attachments/assets/c1fbdd89-6429-431c-b646-e6d4b337da94" />

3. En el campo **`First sequence`**, copiar y pegar la secuencia *Protein_1* completa, incluyendo la línea identificadora que inicia con el símbolo `>`.
<img width="921" height="146" alt="image" src="https://github.com/user-attachments/assets/3134eb4e-40f4-41f9-8761-1415f2ca02bc" />

4. En el campo **`Second sequence`**, copiar y pegar la secuencia *Protein_2*, también incluyendo la línea identificadora.
<img width="1443" height="231" alt="image" src="https://github.com/user-attachments/assets/ad1a1ce0-e119-4360-825f-dc78ce418778" />

5. En la sección **`Parameters`**, seleccionar el formato de salida del alineamiento.  
   Para este ejemplo, elegir el formato *Pair*, ya que facilita la visualización del alineamiento y la identificación de regiones conservadas.
<img width="515" height="126" alt="image" src="https://github.com/user-attachments/assets/a10cbfb4-51ec-4002-a5c1-efb6b12a350d" />

6. En el campo **`Title`**, asignar un nombre descriptivo al ejercicio (por ejemplo: *Alineamiento global Protein_1 vs Protein_2*). Este título aparecerá en los resultados y ayuda a identificar el análisis realizado.
<img width="921" height="191" alt="image" src="https://github.com/user-attachments/assets/ed1e1730-2e79-4e73-8d55-b2f55151f675" />

9. Una vez verificados todos los campos, ejecutar el alineamiento haciendo clic en el botón **`Submit`**.

### 👀 Interpretación de los resultados

Una vez ejecutado el alineamiento, EMBOSS Needle genera un reporte estructurado que permite evaluar la calidad y las características del alineamiento global. A continuación, se describen los principales elementos que aparecen en la salida y cómo deben interpretarse.

#### <mark>Información general</mark>
En la parte superior del reporte se indica el programa utilizado (**needle**), la matriz de sustitución empleada (**EBLOSUM62**) y las penalizaciones por apertura y extensión de espacios (*gap penalty* y *extend penalty*). Estos parámetros determinan cómo se puntúan las coincidencias, sustituciones y la introducción de *gaps* durante el alineamiento.

<img width="561" height="507" alt="Alineamientopareado1" src="https://github.com/user-attachments/assets/20c1e50c-25a8-42e0-a618-3d8e7bd71d4d" />

#### <mark>Estadísticas principales</mark>
Este bloque resume cuantitativamente el resultado del alineamiento:

- **Length**: indica la longitud total del alineamiento. En un alineamiento global, este valor suele coincidir con la longitud completa de las secuencias comparadas.
  - Length = 142. El alineamiento cubre toda la longitud de ambas secuencias.
- **Identity**: representa el porcentaje de posiciones con aminoácidos idénticos entre ambas secuencias.
  - 122/142 (85.9%). Indica que el 85.9% de los aminoácidos son idénticos en ambas secuencias. Este es un valor muy alto, lo que sugiere que las proteínas están estrechamente relacionadas.
- **Similarity**: incluye tanto identidades como sustituciones conservadas, de acuerdo con la matriz de sustitución utilizada.
  - 131/142 (92.3%). Incluye aminoácidos idénticos y sustituciones conservadas. 
- **Gaps**: muestra el número y porcentaje de espacios introducidos para optimizar el alineamiento.
  - 0/142 (0.0%) No fue necesario introducir espacios para alinear las secuencias. Esto indica que ambas proteínas tienen la misma longitud y una estructura muy similar.
- **Score**: es el puntaje total del alineamiento; valores más altos indican una mejor correspondencia global entre las secuencias.
  - 648.0. Un puntaje alto refleja un alineamiento de muy buena calidad, coherente con los altos porcentajes de identidad y similitud.  

<img width="406" height="163" alt="image" src="https://github.com/user-attachments/assets/70e43482-90e7-40a7-910c-8a4c027d984a" />

#### <mark>Representación visual</mark>
El alineamiento se presenta de forma textual, línea por línea. Entre las dos secuencias aparece una línea de símbolos que facilita la interpretación:

- `|` indica coincidencias exactas entre aminoácidos.
- `:` representa sustituciones conservadas.
- `.` señala sustituciones menos conservadas.
- `-` corresponde a espacios (*gaps*) introducidos por el algoritmo.

Esta representación visual permite identificar rápidamente regiones altamente conservadas y posibles diferencias entre las secuencias.

<img width="921" height="354" alt="image" src="https://github.com/user-attachments/assets/d8cdaa20-d25e-40eb-98de-e4d1103f55fd" />

👉 La abundancia de | y : confirma una alta conservación entre ambas secuencias.

## ⚙️ Algoritmo de Smith-Waterman

### 📝 ¿Qué es?
El algoritmo de **Smith-Waterman** fue introducido por Temple Smith y Michael Waterman en 1981. Es un método de alineamiento **local** diseñado para identificar regiones de alta similitud entre dos secuencias, sin forzar el alineamiento completo.

Al igual que Needleman-Wunsch, utiliza programación dinámica, pero con diferencias clave:

- La matriz se inicializa con ceros.
- No se permiten valores negativos; cualquier puntuación negativa se reemplaza por cero.
- El alineamiento comienza en la celda con la puntuación máxima y finaliza cuando se alcanza un valor cero.

<img width="765" height="370" alt="image" src="https://github.com/user-attachments/assets/7bb9bea1-7545-4925-a075-8586e3acbd27" />

## 🛠️ ¿Cómo se realiza un alineamiento local?

**EMBOSS Water** es una herramienta web que implementa el algoritmo de **Smith-Waterman**, un método de alineamiento local ampliamente utilizado para detectar regiones conservadas dentro de dos secuencias, incluso cuando estas difieren en longitud o presentan regiones no relacionadas.

### 🧩 Secuencias de ejemplo

>Protein_1  
MVLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHFDLSHGSAQVKGHGKKVADALTNAVAHVDDMPNALSALSDLHAHKLRVDPVNFKLLSHCLLVTLAAHLPAEFTPAVHASLDKFLASVSTVLTSKYR

>Protein_2  
MVLSGEDKSNIKAAWGKIGGHGAEYGAEALERMFASFPTTKTYFPHFDVSHGSAQVKGHGKKVADALASAAGHLDDLPGALSALSDLHAHKLRVDPVNFKLLSHCLLVTLASHHPADFTPAVHASLDKFLASVSTVLTSKYR

**🌐 Página web:**  
https://www.ebi.ac.uk/jdispatcher/psa/emboss_water

### 👩🏻‍💻 Uso de la interfaz
1. Acceder al enlace de **EMBOSS Water** desde el navegador web.
<img width="921" height="408" alt="image" src="https://github.com/user-attachments/assets/ce5f9f8a-6381-4ab3-af57-96288af36856" />

2. En la sección **`Input sequence`**, indicar el tipo de secuencia a analizar.  
   En este caso, seleccionar la opción correspondiente a *Protein*, ya que las secuencias están formadas por aminoácidos.
<img width="706" height="109" alt="image" src="https://github.com/user-attachments/assets/750143b6-b507-4035-a2ed-03bb1035221e" />

3. En el campo **`First sequence`**, copiar y pegar la secuencia *Protein_1* completa, incluyendo la línea identificadora que inicia con el símbolo `>`.
<img width="921" height="149" alt="image" src="https://github.com/user-attachments/assets/c9f64284-733b-43c9-9298-2dec866977bf" />

4. En el campo **`Second sequence`**, copiar y pegar la secuencia *Protein_2*, también incluyendo la línea identificadora.
<img width="921" height="147" alt="image" src="https://github.com/user-attachments/assets/0cc2f78a-5cf2-4372-8d49-2f3454539916" />

5. En la sección **`Parameters`**, seleccionar el formato de salida del alineamiento.  
   Para este ejemplo, elegir el formato *Pair*, ya que facilita la visualización de las regiones alineadas.
<img width="515" height="126" alt="image" src="https://github.com/user-attachments/assets/065a3b40-5edd-4756-a964-9aae904a3423" />

6. En el campo **`Title`**, asignar un nombre descriptivo al ejercicio (por ejemplo: *Alineamiento local Protein_1 vs Protein_2*).
<img width="764" height="164" alt="image" src="https://github.com/user-attachments/assets/20c53abc-87ef-4d90-ad75-5fcbf2230a39" />

7. Ejecutar el alineamiento haciendo clic en el botón **`Submit`**.

### 👀 Interpretación de los resultados
Una vez que se completa el alineamiento, EMBOSS Water produce un informe que se asemeja al de EMBOSS Needle; sin embargo, hay diferencias clave que se deben tener en cuenta debido a la naturaleza local del algoritmo.

#### <mark>Información general</mark>
En la parte superior del reporte se indica que el programa ejecutado es **water**, confirmando el uso del algoritmo de Smith-Waterman. Asimismo, se reportan los parámetros empleados durante el alineamiento, entre los que destacan:

- **Matriz de sustitución (EBLOSUM62)**: utilizada para asignar puntajes a coincidencias y sustituciones conservadas entre aminoácidos.
- **Gap penalty (10.0)** y **extend penalty (0.5)**: penalizaciones aplicadas a la apertura y extensión de espacios (*gaps*).

<img width="587" height="507" alt="Alineamiento pareado2" src="https://github.com/user-attachments/assets/a01dbd7a-9aee-4601-82c6-f0cd14d434b4" />

#### <mark>Estadísticas principales</mark>

El bloque de estadísticas resume cuantitativamente el alineamiento identificado como el más significativo:

- **Length (142)**: indica la longitud del fragmento alineado, no necesariamente a la longitud completa de las secuencias. En este ejemplo, el alineamiento local abarca toda la longitud de las secuencias, lo cual puede ocurrir cuando las proteínas son altamente similares.
- **Identity (122/142; 85.9%)**: representa el porcentaje de aminoácidos idénticos dentro de la región alineada.
- **Similarity (131/142; 92.3%)**: incluye coincidencias exactas y sustituciones conservadas, de acuerdo con la matriz EBLOSUM62.
- **Gaps (0/142; 0.0%)**: indica que no fue necesario introducir espacios para optimizar el alineamiento.
- **Score (648.0)**: corresponde al puntaje del alineamiento local de mayor calidad identificado por el algoritmo.

<img width="398" height="167" alt="image" src="https://github.com/user-attachments/assets/becdc0fa-c906-4c10-a905-5892787b6049" />


#### <mark>Representación visual</mark>
El alineamiento textual muestra únicamente la región con mayor similitud entre las secuencias. Al igual que en EMBOSS Needle:

- `|` indica coincidencias exactas.
- `:` representa sustituciones conservadas.
- `.` señala sustituciones menos conservadas.
- `-` indica *gaps*.

<img width="632" height="233" alt="image" src="https://github.com/user-attachments/assets/a39d5f40-012f-4218-ab41-51bf4b96b564" />

👉 Cuando dos secuencias son altamente similares y de la misma longitud, tanto el alineamiento global como el local pueden producir resultados equivalentes.

### Tabla comparativa entre ambos métodos de alineamiento
| Característica | Needleman-Wunsch | Smith-Waterman |
|---------------|--------------|--------------|
| Tipo de alineamiento | Global | Local |
| Región alineada | Alinea la secuencia completa, de extremo a extremo | Alinea únicamente la región con mayor similitud |
| Tratamiento de regiones no conservadas | Se incluyen en el alineamiento, aun si presentan baja similitud | Se excluyen del alineamiento |
| Uso de *gaps* | Introduce *gaps* para forzar el alineamiento completo | Introduce *gaps* solo dentro de la región local alineada |
| Aplicación principal | Comparación global de secuencias de longitud similar | Detección de regiones conservadas o dominios funcionales |


## 🔎BLAST 

Los alineamientos pareados son un método útil para comparar directamente dos secuencias previamente seleccionadas. Sin emabrgo, en muchos análisis bioinformáticos es común enfrentarse a secuencias cuya identidad o función es desconocida. En estos casos, resulta necesario comparar una secuencia problema contra grandes bases de datos de secuencias biológicas para identificar posibles homologías.

Para este propósito se utiliza **BLAST (Basic Local Alignment Search Tool)**, una de las herramientas más empleadas para la búsqueda de similitud entre secuencias.

### 📝 ¿Qué es?

**BLAST** es un conjunto de algoritmos diseñados para identificar similitudes locales entre secuencias biológicas. Su función principal es comparar una secuencia de entrada contra bases de datos extensas, con el fin de localizar secuencias que presenten un grado de similitud estadísticamente significativo.











## Dominios Conservados

### ¿Qué son?


## Literatura complementaria

Needleman, S. B., & Wunsch, C. D. (1970). A general method applicable to the search for similarities in the amino acid sequence of two proteins. *Journal of Molecular Biology, 48*(3), 443–453. https://doi.org/10.1016/0022-2836(70)90057-4

Smith, T. F., & Waterman, M. S. (1981). Identification of common molecular subsequences. *Journal of Molecular Biology, 147*(1), 195–197. https://doi.org/10.1016/0022-2836(81)90087-5



