# 🔎BLAST 

En muchos análisis bioinformáticos es común enfrentarse a secuencias cuya identidad o función es desconocida. En estos casos, resulta necesario comparar una secuencia problema contra grandes bases de datos de secuencias biológicas para identificar posibles homologías. Para este propósito se utiliza **BLAST (Basic Local Alignment Search Tool)**, una de las herramientas más empleadas para la búsqueda de similitud entre secuencias.

## 📝 ¿Qué es?

**BLAST** es un conjunto de algoritmos diseñados para identificar similitudes locales entre secuencias biológicas. Su función principal es comparar una secuencia de entrada contra bases de datos extensas, con el fin de localizar secuencias que presenten un grado de similitud estadísticamente significativo. Para ello, utiliza un **enfoque heurístico**, lo que quiere decir que inicialmente encuentra coincidencias cortas entre dos secuencias y luego las extiende.

<img width="1205" height="631" alt="image" src="https://github.com/user-attachments/assets/45b24b38-b4cf-436a-b5c1-962c98916121" />

**Figura 4.** BLAST (Basic Local Alignment Search Tool) (NCBI).

## 📌 ¿Cómo funciona BLAST? 

El procedimiento consiste en:

1. **Fragmentación (*Seeding*):** BLAST fracciona la secuencia problema en partes muy pequeñas llamados "palabras" (por lo general en 3 aminoácidos para proteínas o 11 nucleótidos para ADN).
2. **Búsqueda de coincidencias:** Busca estas palabras en la base de datos.
3. **Extensión (*Extension*)**: Una vez que encuentra una coincidencia, intenta ampliarla en ambas direcciones para ver si la similitud encaja.
4. **Evaluación (*Scoring*)**: Determina si el alineamiento es estadísticamente significativo.

<img width="1158" height="443" alt="image" src="https://github.com/user-attachments/assets/5aebebe5-79ac-46e5-be2c-b4ef81fa7b26" />
<img width="1058" height="207" alt="image" src="https://github.com/user-attachments/assets/b366f343-e903-4187-8ca5-aed66f33f1c0" />

**Figura 5**. ¿Cómo funciona BLAST? (NLM, 2022).

## 🗂️ Tipos principales

Dependiendo de qué tipo de secuencia de entrada tengas y qué quieras buscar, existen diferentes variantes:

- **blastp**: compara una secuencia de proteína contra una base de datos de proteínas.
- **blastn**: compara una secuencia de nucleótidos contra una base de datos de nucleótidos.
- **blastx**: traduce una secuencia de nucleótidos y la compara contra proteínas.
- **tblastn**: compara una proteína contra una base de datos de nucleótidos traducidos.
- **tblastx**: compara traducciones de nucleótidos contra traducciones de nucleótidos.

## 🤔 ¿Para qué se utiliza?

Esta herramienta se emplea para:
- **Identificar especies**: Si cuentas con una secuencia biólogica desconocida, BLAST te puede ayudar a indagar a qué especie pertenece.
- **Anotación genómica**: Contribuye a predecir la función de un gen recién descubierto comparándolo con genes ya conocidos.
- **Filogenia**: Detecta homologías evolutivas entre diversos organismos.
- **Mapeo de dominios**: Localiza regiones conservadas entre proteínas.

## 🛠️ ¿Cómo se realiza un alineamiento con BLAST?

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

## 👀 Interpretación de los resultados

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

## Bibliografía

National Library of Medicine. (2021). How BLAST works. https://www.nlm.nih.gov/ncbi/workshops/2022-10_Basic-Web-BLAST/how-blast-works.html
