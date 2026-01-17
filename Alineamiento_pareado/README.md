# 🔀 Alineamiento pareado de secuencias

## 📝 ¿Qué es?
El alineamiento de secuencias por pares (*pairwise alignment*) es una técnica que se utiliza para identificar regiones de similitud que podrían indicar relaciones funcionales, estructurales o evolutivas entre dos secuencias biológicas (proteínas o ácidos nucleicos). Dos algoritmos de alineación de secuencias mayormente utilizados son:

- **Alineación global**: Intenta alinear las secuencias completas de principio a fin. Este método se utiliza al comparar secuencias de la misma longitud. 

- **Alineación local**: Alinea las regiones de mayor densidad de coincidencias dentro de las secuencias a alinear. Es muy útil para identificar motivos conservados.

<img width="921" height="408" alt="image" src="https://github.com/user-attachments/assets/af60323d-9aa1-405d-9c13-d3d9575cdeae" />

**Figura 1.** Alineamiento global y local de dos secuencias (Mount, 2001).

El objetivo principal es encontrar la mejor coincidencia entre los residuos, ya sean nucleótidos o aminoácidos, de ambas secuencias. Para lograr esto, se emplean tres elementos fundamentales:
- **Coincidencias (Matches)**: Caracteres idénticos en la misma posición.
- **Sustituciones (Mismatches)**: Caracteres diferentes que ocupan la misma posición.
- **Huecos (*Gaps*)**: Espacios introducidos en una secuencia para compensar inserciones o deleciones (indels) en la otra.

Para encontrar el alineamiento más adecuado, se emplean sistemas de puntuación (*Scoring*):

- **Matrices de Sustitución**: Asignan valores numéricos a los intercambios de residuos.
  - En el caso del material genético, se asigna un valor positivo para una coincidencia y un valor negativo para una sustitución.
  - En el de las proteínas, se recurren a matrices de puntuación más complejas como PAM o BLOSUM, que reflejan la probabilidad de que un aminoácido sea sustituido por otro sin comprometer la función de la proteína.
- **Penalización por *gaps***: Abrir un hueco "cuesta" más puntos lo que ayuda a evitar que el algoritmo llene las secuencias con espacios.

## ⚙️ Tipos de alineamiento pareado

### 💡 Algoritmo de Needleman-Wunsch

### 📝 ¿Qué es?
El algoritmo de Needleman-Wunsch fue propuesto por Saul B. Needleman y Christian D. Wunschen en 1970. Es un método de alineamiento **global** cuyo objetivo es encontrar el mejor emparejamiento posible considerando la longitud completa de ambas secuencias.

Este algoritmo se basa en programación dinámica y sigue tres pasos principales:

1. **Inicialización de la matriz de puntuación**: Se construye una matriz donde las filas y columnas representan las secuencias a comparar. La primera fila y columna se inicializan con penalizaciones por *gaps*.
2. **Relleno de la matriz**: Cada celda se calcula evaluando tres posibles movimientos (diagonal, arriba o izquierda), considerando coincidencias, sustituciones y penalizaciones por *gaps*.
3. **Trazado para identificar la alineación óptima**: Una vez llena la matriz, se comienza desde la esquina inferior derecha y se sigue el camino de regreso hasta la esquina superior izquierda para reconstruir el alineamiento final.

<img width="921" height="620" alt="image" src="https://github.com/user-attachments/assets/03f5eee2-14c8-419d-be2c-06f4ab7684a0" />

**Figura 2.** Representación del algoritmo de Needleman-Wunsch (Gauthier *et al*., 2018).

### 🛠️ ¿Cómo se realiza un alineamiento global?

**EMBOSS Needle** es una herramienta web que implementa el algoritmo de Needleman-Wunsch. A continuación, se presenta un ejemplo de cómo se puede llevar a cabo un alineamiento empleando este método.

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
   Para este ejemplo, elegir el formato *Pair*...
<img width="515" height="126" alt="image" src="https://github.com/user-attachments/assets/a10cbfb4-51ec-4002-a5c1-efb6b12a350d" />

6. En el campo **`Title`**, asignar un nombre descriptivo al ejercicio (por ejemplo: *Alineamiento global Protein_1 vs Protein_2*). Este título aparecerá en los resultados y ayuda a identificar el análisis realizado.
<img width="921" height="191" alt="image" src="https://github.com/user-attachments/assets/ed1e1730-2e79-4e73-8d55-b2f55151f675" />

9. Una vez verificados todos los campos, ejecutar el alineamiento haciendo clic en el botón **`Submit`**.

### 👀 Interpretación de los resultados

Una vez ejecutado el alineamiento, se genera un reporte estructurado que permite evaluar la calidad y las características del alineamiento global. A continuación, se describen los principales elementos que aparecen en la salida y cómo deben interpretarse.

#### <mark>Información general</mark>
En la parte superior del reporte se indica el programa utilizado (**needle**), la matriz de sustitución empleada (**EBLOSUM62**) y las penalizaciones por apertura y extensión de espacios (*gap penalty* y *extend penalty*). Estos parámetros determinan cómo se puntúan las coincidencias, sustituciones y la introducción de *gaps* durante el alineamiento.

<img width="561" height="507" alt="Alineamientopareado1" src="https://github.com/user-attachments/assets/20c1e50c-25a8-42e0-a618-3d8e7bd71d4d" />

#### <mark>Estadísticas principales</mark>
Este bloque resume cuantitativamente el resultado del alineamiento:

- **Length**: indica la longitud total del alineamiento. En un alineamiento global, este valor suele coincidir con la longitud completa de las secuencias comparadas.
  - Length = 142. El alineamiento cubre toda la longitud de ambas secuencias.
- **Identity**: representa el porcentaje de posiciones con aminoácidos idénticos entre ambas secuencias.
  - 122/142 (85.9%). Indica que el 85.9% de los aminoácidos son idénticos en ambas secuencias. Este es un valor muy alto, lo que sugiere que las proteínas están muy relacionadas.
- **Similarity**: incluye tanto coincidencias como sustituciones conservadas, de acuerdo con la matriz utilizada.
  - 131/142 (92.3%).
- ***Gaps***: muestra el número y porcentaje de espacios introducidos para optimizar el alineamiento.
  - 0/142 (0.0%) No fue necesario introducir espacios para alinear las secuencias. Esto indica que ambas proteínas tienen la misma longitud y una estructura muy similar.
- **Score**: es el puntaje total del alineamiento; valores más altos indican una mejor correspondencia global entre las secuencias.
  - 648.0. Un puntaje alto refleja un alineamiento de muy buena calidad, acorde con los altos porcentajes de identidad y similitud.  

#### <mark>Representación visual</mark>
El alineamiento se presenta de forma textual, línea por línea. Entre las dos secuencias aparece una línea de símbolos que facilita la interpretación:

- `|` indica coincidencias exactas entre aminoácidos.
- `:` representa sustituciones conservadas.
- `.` señala sustituciones menos conservadas.
- `-` corresponde a los *gaps* introducidos por el algoritmo.

Esta representación visual permite identificar rápidamente regiones altamente conservadas y posibles diferencias entre las secuencias.

<img width="921" height="354" alt="image" src="https://github.com/user-attachments/assets/d8cdaa20-d25e-40eb-98de-e4d1103f55fd" />

### 💡 Algoritmo de Smith-Waterman

### 📝 ¿Qué es?
El algoritmo de Smith-Waterman fue introducido por Temple Smith y Michael Waterman en 1981. Es un método de alineamiento **local** modificado del anterior diseñado para identificar regiones de alta similitud entre dos secuencias, sin forzar el alineamiento completo.

Al igual que Needleman-Wunsch, utiliza programación dinámica, pero con diferencias clave:

- La matriz se inicializa con ceros.
- No se permiten valores negativos; cualquier puntuación negativa se reemplaza por cero.
- El alineamiento comienza en la celda con la puntuación máxima y finaliza cuando se alcanza un valor cero.

<img width="608" height="378" alt="image" src="https://github.com/user-attachments/assets/a786f7b3-3ab6-4c73-bc83-ddf3af450f13" />

**Figura 3.** Representación del algoritmo de Needleman-Wunsch (Liao *et al*., 2018).

## 🛠️ ¿Cómo se realiza un alineamiento local?

**EMBOSS Water** es una herramienta web que implementa el algoritmo de Smith-Waterman. A continuación también se presenta un ejemplo de cómo se puede llevar a cabo un alineamiento empleando este método.

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
   Para este ejemplo, elegir el formato *Pair* ...
<img width="515" height="126" alt="image" src="https://github.com/user-attachments/assets/065a3b40-5edd-4756-a964-9aae904a3423" />

6. En el campo **`Title`**, asignar un nombre descriptivo al ejercicio (por ejemplo: *Alineamiento local Protein_1 vs Protein_2*).
<img width="764" height="164" alt="image" src="https://github.com/user-attachments/assets/20c53abc-87ef-4d90-ad75-5fcbf2230a39" />

7. Ejecutar el alineamiento haciendo clic en el botón **`Submit`**.

### 👀 Interpretación de los resultados
Una vez que se completa el alineamiento, igual se produce un informe que se asemeja al de EMBOSS Needle; sin embargo, hay diferencias clave que se deben tener en cuenta debido a la naturaleza local del algoritmo.

#### <mark>Información general</mark>
En la parte superior del reporte se indica que el programa ejecutado es **water**, confirmando el uso del algoritmo de Smith-Waterman. Asimismo, se reportan los parámetros empleados durante el alineamiento, entre los que destacan:

- **Matriz de sustitución (EBLOSUM62)**: utilizada para asignar puntajes a coincidencias y sustituciones conservadas entre aminoácidos.
- **Gap penalty (10.0)** y **extend penalty (0.5)**: penalizaciones aplicadas a la apertura y extensión de *gaps*.

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

>También se puede llevar a cabo una **alineación de secuencias múltiples**, que implica alinear tres o más secuencias biológicas que tienen longitudes similares. A partir de los resultados obtenidos, se puede deducir la homología y explorar la relación evolutiva entre las secuencias.

### ¿Cuál elegir?

- **Needleman-Wunsch:** Escógelo si estás comparando dos variantes del mismo gen o dos proteínas de la misma familia que sospechas que son casi idénticas de inicio a fin.
- **Smith-Waterman:** Elígelo si tienes una secuencia larga y quieres ver si contiene un fragmento (como un sitio de unión a un ligando o un dominio funcional) que ya conoces.

### Resumen de ambos métodos de alineamiento
| Característica | Needleman-Wunsch | Smith-Waterman |
|---------------|--------------|--------------|
| Tipo de alineamiento | Global| Local|
| Región alineada | Alinea la secuencia completa, de extremo a extremo | Alinea únicamente la región con mayor similitud |
| Aplicación principal | Comparación global de secuencias de longitud similar | Detección de regiones conservadas o dominios funcionales |
| Ventaja| Garantiza el mejor alineamiento total posible | Sensible para encontrar similitudes pequeñas en secuencias muy largas |


## 🔎BLAST 

En muchos análisis bioinformáticos es común enfrentarse a secuencias cuya identidad o función es desconocida. En estos casos, resulta necesario comparar una secuencia problema contra grandes bases de datos de secuencias biológicas para identificar posibles homologías. Para este propósito se utiliza **BLAST (Basic Local Alignment Search Tool)**, una de las herramientas más empleadas para la búsqueda de similitud entre secuencias.

### 📝 ¿Qué es?

**BLAST** es un conjunto de algoritmos diseñados para identificar similitudes locales entre secuencias biológicas. Su función principal es comparar una secuencia de entrada contra bases de datos extensas, con el fin de localizar secuencias que presenten un grado de similitud estadísticamente significativo. Para ello, utiliza un **enfoque heurístico**, lo que quiere decir que inicialmente encuentra coincidencias cortas entre dos secuencias y luego las extiende.

<img width="1205" height="631" alt="image" src="https://github.com/user-attachments/assets/45b24b38-b4cf-436a-b5c1-962c98916121" />

**Figura 4.** BLAST (Basic Local Alignment Search Tool) (NCBI).

### 📌 ¿Cómo funciona BLAST? 

El procedimiento consiste en:

1. **Fragmentación (*Seeding*):** BLAST fracciona la secuencia problema en partes muy pequeñas llamados "palabras" (por lo general en 3 aminoácidos para proteínas o 11 nucleótidos para ADN).
2. **Búsqueda de coincidencias:** Busca estas palabras en la base de datos.
3. **Extensión (*Extension*)**: Una vez que encuentra una coincidencia, intenta ampliarla en ambas direcciones para ver si la similitud encaja.
4. **Evaluación (*Scoring*)**: Determina si el alineamiento es estadísticamente significativo.

<img width="1158" height="443" alt="image" src="https://github.com/user-attachments/assets/5aebebe5-79ac-46e5-be2c-b4ef81fa7b26" />
<img width="1058" height="207" alt="image" src="https://github.com/user-attachments/assets/b366f343-e903-4187-8ca5-aed66f33f1c0" />

**Figura 5**. ¿Cómo funciona BLAST? (NLM, 2022).

### 🗂️ Tipos principales

Dependiendo de qué tipo de secuencia de entrada tengas y qué quieras buscar, existen diferentes variantes:

- **blastp**: compara una secuencia de proteína contra una base de datos de proteínas.
- **blastn**: compara una secuencia de nucleótidos contra una base de datos de nucleótidos.
- **blastx**: traduce una secuencia de nucleótidos y la compara contra proteínas.
- **tblastn**: compara una proteína contra una base de datos de nucleótidos traducidos.
- **tblastx**: compara traducciones de nucleótidos contra traducciones de nucleótidos.

### 🤔 ¿Para qué se utiliza?

Esta herramienta se emplea para:
- **Identificar especies**: Si cuentas con una secuencia biólogica desconocida, BLAST te puede ayudar a indagar a qué especie pertenece.
- **Anotación genómica**: Contribuye a predecir la función de un gen recién descubierto comparándolo con genes ya conocidos.
- **Filogenia**: Detecta homologías evolutivas entre diversos organismos.
- **Mapeo de dominios**: Localiza regiones conservadas entre proteínas.

### 🛠️ ¿Cómo se realiza un alineamiento con BLAST?

La forma más común de usarlo es a través del portal del **NCBI (National Center for Biotechnology Information)**, que ofrece una versión web conectada a bases de datos masivas. A continuación se describe cómo se puede manejar, retomando una de las secuencias de proteína empleadas previamente en los alineamientos pareados.

### 🧩 Secuencia de ejemplo

>Protein_1  
MVLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHFDLSHGSAQVKGHGKKVADALTNAVAHVDDMPNALSALSDLHAHKLRVDPVNFKLLSHCLLVTLAAHLPAEFTPAVHASLDKFLASVSTVLTSKYR

**🌐 Página web:** 
https://blast.ncbi.nlm.nih.gov/Blast.cgi

### 👩🏻‍💻 Uso de la interfaz
1. Acceder al sitio web.
<img width="921" height="449" alt="image" src="https://github.com/user-attachments/assets/05a00bed-a85d-45d6-a1d0-e430e272f519" />

2. En la página principal, seleccionar la opción `Protein BLAST (blastp)`, ya que la secuencia de entrada corresponde a una secuencia de aminoácidos. Sin embargo, esta selección depende de la secuencia que se desee alinear.
<img width="1664" height="811" alt="DM (1)" src="https://github.com/user-attachments/assets/bc682b7e-1b09-431e-9734-51128241a94c" />

3. En el campo `Enter Query Sequence`, copiar y pegar la secuencia *Protein_1* completa, incluyendo la línea identificadora que inicia con el símbolo `>`.
<img width="921" height="340" alt="image" src="https://github.com/user-attachments/assets/04461953-c3ab-4ffc-b9af-ab7902082e8e" />

4. En la sección `Choose Search Set`, seleccionar la base de datos contra la cual se realizará la búsqueda.  
   Para este ejercicio, elegir la base de datos **nr (non-redundant protein sequences)**, la cual abarca una gran colección de proteínas de diferentes organismos.
<img width="921" height="196" alt="image" src="https://github.com/user-attachments/assets/3970af8f-970d-48b8-be47-8796a39cf3d6" />

5. En el apartado `Program Selection`, mantener la configuración por defecto (**blastp – protein-protein BLAST**).
<img width="921" height="265" alt="image" src="https://github.com/user-attachments/assets/bfaa7c2a-e04b-492a-9d1b-53a00870647d" />

6. En la sección `Algorithm parameters`, se pueden dejar los valores predeterminados.
<img width="921" height="820" alt="image" src="https://github.com/user-attachments/assets/a2e85bf8-21c0-4671-8f75-b877c92e26e7" />

7. Una vez configurados los parámetros, ejecutar la búsqueda haciendo clic en el botón **BLAST**.
<img width="921" height="138" alt="image" src="https://github.com/user-attachments/assets/71b9a0f7-22c3-460d-a4dd-956a66a92739" />

8. Esperar a que el servidor procese la consulta.
<img width="921" height="349" alt="image" src="https://github.com/user-attachments/assets/df2d186c-1110-4992-9061-85a23e25e2bb" />

### 👀 Interpretación de los resultados

Una vez que se termina la búsqueda con BLAST, la plataforma presenta una página de resultados que está organizada en varias secciones. Cada sección ofrece información única y complementaria que te ayuda a evaluar cuán similar es la secuencia que consultaste con las secuencias que existentes en la base de datos.

### <mark>Información general de la búsqueda</mark>

En la parte superior del reporte se muestra un resumen de la búsqueda realizada, el cual incluye:

- **Program**: indica el tipo de BLAST utilizado. En este ejemplo se empleó **BLASTP**, ya que la secuencia consulta corresponde a una proteína.
- **Database**: señala la base de datos utilizada. En este caso, se utilizó **nr (non-redundant protein sequences)**.
- **Query Length**: longitud de la secuencia consulta, expresada en número de aminoácidos.
- **RID**: identificador único de la búsqueda, que permite recuperar los resultados posteriormente.

<img width="921" height="714" alt="image" src="https://github.com/user-attachments/assets/ba99937f-51fb-43db-83b4-813c25b2ecae" />

### <mark>Lista de secuencias con alineamientos significativos (Descriptions)</mark>

Esta sección presenta una tabla con las secuencias de la base de datos que muestran similitud significativa con la secuencia de entrada. Cada fila corresponde a un *hit* o coincidencia.

Las columnas más relevantes son:

- **Description**: descripción de la proteína encontrada.
- **Scientific Name**: organismo del cual proviene la secuencia.
- **Max Score** y **Total Score**: puntajes que reflejan la calidad del alineamiento; valores más altos indican mayor similitud.
- **Query Cover**: porcentaje de la secuencia consulta que forma parte en el alineamiento.
- **E-value**: probabilidad de que el alineamiento ocurra por azar. Valores muy cercanos a cero indican alta significancia estadística.
- **Per. Ident**: porcentaje de aminoácidos idénticos entre la secuencia consulta y la secuencia encontrada.
- **Accession**: identificador único de la secuencia en la base de datos.

En este ejemplo, los primeros resultados se refieren a proteínas identificadas como **hemoglobina subunidad alfa**, principalmente de *Homo sapiens*, con valores de identidad cercanos o iguales al 100% y *E-values* muy bajos, lo que indica una coincidencia altamente significativa.

<img width="921" height="491" alt="image" src="https://github.com/user-attachments/assets/2cb5b127-2851-40aa-8f15-66e73410c266" />

### <mark>Resumen gráfico de los alineamientos (Graphic Summary)</mark>

Esta pestaña enseña una representación visual de la distribución de los alineamientos a lo largo de la secuencia problema. Cada barra horizontal representa un alineamiento entre la secuencia problema y una secuencia de la base de datos. El color de las barras indica la calidad del alineamiento, según la escala de puntajes mostrada en la parte superior. En este ejemplo, las barras cubren prácticamente toda la longitud de la secuencia problema, lo que indica una alta cobertura y una fuerte similitud global entre la secuencia analizada y las proteínas encontradas.

Dentro del resumen gráfico, también se pueden ver los **dominios conservados** que se han detectado automáticamente. En este ejemplo, se identifica un dominio del tipo **globin-like**, que está relacionado con las proteínas de la familia de las globinas. La identificación de estos dominios conservados brinda información funcional adicional, ya que ayuda a inferir la posible función de la proteína basándonos en regiones que son estructural y funcionalmente conservadas.

<img width="921" height="505" alt="image" src="https://github.com/user-attachments/assets/65e22e2a-016c-4780-9b65-ca0633d7fac1" />

### <mark>Alineamientos (Alignments)</mark>

Esta pestaña presenta el alineamiento detallado entre la secuencia problema y cada una de las secuencias encontradas.

En esta sección se observan:

- El **Score** del alineamiento expresado en bits.
- El **E-value**, que confirma la significancia estadística.
- Los valores de **Identities**, **Positives** y **Gaps**.
- El alineamiento aminoácido por aminoácido entre la secuencia consulta (*Query*) y la secuencia de la base de datos (*Sbjct*).

En el alineamiento mostrado, se observa una identidad del 100% y ausencia de *gaps*, lo cual indica que la secuencia consulta es idéntica o prácticamente idéntica a las proteínas encontradas en la base de datos.

<img width="921" height="418" alt="image" src="https://github.com/user-attachments/assets/a8364d88-65d8-49ce-b3d6-0d83cb4ee7f9" />

### <mark>Interpretación general en el contexto del tutorial</mark>

En el contexto de este tutorial, los resultados de BLAST permiten identificar de manera clara que la secuencia *Protein_1* corresponde a una proteína de la familia de las globinas, específicamente a la **hemoglobina subunidad alfa**. La alta identidad, la cobertura completa de la secuencia y los valores extremadamente bajos de *E-value* respaldan esta identificación.

Este ejercicio ilustra cómo BLAST complementa a los alineamientos pareados, permitiendo no solo comparar secuencias específicas, sino también contextualizar una secuencia dentro de grandes bases de datos biológicas y asociarla con información funcional y evolutiva.

### <mark>Filtrado de resultados (Filter Results)</mark>

La sección **Filter Results** permite afinar los resultados obtenidos con BLAST, haciendo más fácil la selección de alineamientos que realmente importan según criterios específicos. Esta herramienta es especialmente valiosa cuando se obtienen un montón de *hits* y quieres centrarte en subconjuntos particulares.

Los criterios principales para filtrar son:

- **Organism**: permite limitar los resultados a secuencias de un organismo específico o grupo taxonómico. Simplemente ingresa un nombre común, nombre científico o identificador taxonómico, y BLAST mostrará solo las coincidencias relevantes. También es posible excluir ciertos organismos marcando la opción *exclude*.
- **Percent Identity**: filtra los resultados según el porcentaje de aminoácidos idénticos entre la secuencia de problema y las secuencias encontradas. Un valor alto indica una mayor similitud.
- **E-value**: permite restringir los resultados con base en su significancia estadística. Al establecer un rango bajo de *E-value*, se excluyen alineamientos que podrían haberse producido por azar.
- **Query Coverage**: limita los resultados según el porcentaje de la secuencia problema que participa en el alineamiento. Una alta cobertura significa que una mayor parte de la secuencia fue alineada.

Una vez definidos los criterios deseados, el botón **Filter** aplica los filtros seleccionados, mientras que **Reset** restaura la visualización original de los resultados.

<img width="921" height="438" alt="image" src="https://github.com/user-attachments/assets/4c023ea0-c0c1-4e48-b341-b9a7b7dd30e8" />

### <mark>Clasificación taxonómica de los resultados (Taxonomy)</mark>

Esta pestaña ofrece una visualización de los resultados de BLAST organizada de acuerdo con la clasificación taxonómica de los organismos a los que pertenecen las secuencias encontradas.

En esta sección se presentan:

- Grupos taxonómicos jerárquicos.
- El número de secuencias alineadas que corresponden a cada grupo.
- Una representación gráfica que permite identificar rápidamente la distribución taxonómica de los *hits*.

En este ejemplo, la pestaña **Taxonomy** muestra una predominancia de secuencias pertenecientes a *Homo sapiens* y a otros primates. Esta visualización permite evaluar la diversidad taxonómica de los resultados y proporciona información adicional sobre la conservación evolutiva de la proteína analizada.

<img width="1574" height="901" alt="image" src="https://github.com/user-attachments/assets/baf89f77-654f-4b1e-931f-17ff2c92808c" />

## Dominios Conservados


## Bibliografía

Embl-Ebi. (s. f.). Job Dispatcher homepage | EMBL-EBI. https://www.ebi.ac.uk/jdispatcher/psa

Gauthier, J., Vincent, A. T., Charette, S. J., & Derome, N. (2018). A brief history of bioinformatics. Briefings In Bioinformatics, 20(6), 1981-1996. https://doi.org/10.1093/bib/bby063

Liao, Y., Li, Y., Chen, N., & Lu, Y. (2018). Adaptively Banded Smith-Waterman Algorithm for Long Reads and Its Hardware Accelerator. 2018 IEEE 29th International Conference on Application-specific Systems, Architectures and Processors (ASAP), 1-9.

Mount, D. W. (2001) Bioinformatics: sequence and genome analysis. Cold Spring Harbor Laboratory Press.

National Library of Medicine. (2021). How BLAST works. https://www.nlm.nih.gov/ncbi/workshops/2022-10_Basic-Web-BLAST/how-blast-works.html

Needleman, S. B., & Wunsch, C. D. (1970). A general method applicable to the search for similarities in the amino acid sequence of two proteins. *Journal of Molecular Biology, 48*(3), 443–453. https://doi.org/10.1016/0022-2836(70)90057-4

Smith, T. F., & Waterman, M. S. (1981). Identification of common molecular subsequences. *Journal of Molecular Biology, 147*(1), 195–197. https://doi.org/10.1016/0022-2836(81)90087-5
