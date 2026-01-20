## 🧪 Análisis de Dominios Conservados (Conserved Domains)

### 📍 Introducción

El análisis de dominios conservados es una estrategia que contribuye a predecir la función, estructura y evolución de una proteína desconocida. Esta herramienta se centra en detectar regiones específicas que se han conservado a lo largo del tiempo, gracias a su relevancia funcional o estructural.

El **Conserved Domain Database (CDD)**, desarrollado y mantenido por el National Center for Biotechnology Information (NCBI), permite identificar estos dominios dentro de una secuencia proteica mediante comparaciones con dominios previamente caracterizados.

>¿Qué es un dominio conservado?
Un **dominio conservado** es una unidad estructural y funcional de una proteína, compuesta por una secuencia de aminoácidos (50 - 200 aa) que se pliega de manera independiente y cuya arquitectura se ha mantenido estable a través de la evolución porque desempeña una tarea biológica esencial para la supervivencia de diversas especies.

Tiene múltiples aplicaciones, entre las que destacan:

- **Anotación funcional de proteínas**: permite inferir la función de proteínas sin caracterización experimental previa.
- **Identificación de familias proteicas**: facilita la clasificación de proteínas dentro de grupos funcionales o evolutivos.
- **Estudio de evolución molecular**: permite analizar la conservación y divergencia de dominios a lo largo del tiempo.
- **Identificación de regiones funcionales relevantes**: como sitios activos, regiones de unión a ligandos o interfaces de interacción.

👩🏻‍🔬 Si un investigador secuencia un nuevo gen y descubre que la proteína resultante contiene un "dominio cinasa", puede inferir inmediatamente que esa proteína tiene la capacidad de añadir grupos fosfato a otras moléculas, incluso si el resto de la secuencia es totalmente nueva.

### 🗂️ ¿Qué es el CDD?

El **CDD** es un recurso de anotación de proteínas que consiste en una colección de modelos de alineamiento de secuencias múltiples. El contenido incluye dominios seleccionados por el NCBI, que utilizan información de estructura 3D para definir explícitamente los límites de los dominios y proporcionar información sobre las relaciones secuencia/estructura/función, así como modelos de dominio importados de diversas bases de datos externas (Pfam, SMART, COG, PRK, TIGRFAM).

### 👩🏻‍💻 Acceso y uso de la interfaz del CDD

El análisis de dominios conservados puede realizarse utilizando la herramienta **CD Search** del NCBI.

A continuación se describe cómo se puede realizar un análisis de CD, retomando una de las secuencias de proteína empleadas previamente en los alineamientos pareados.

**🌐 Página web:** https://www.ncbi.nlm.nih.gov/Structure/cdd/wrpsb.cgi

#### 🧩 Secuencia de ejemplo

>Protein_1
MVLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHFDLSHGSAQVKGHGKKVADALTNAVAHVDDMPNALSALSDLHAHKLRVDPVNFKLLSHCLLVTLAAHLPAEFTPAVHASLDKFLASVSTVLTSKYR

#### Pasos a seguir

1. Acceder al sitio web del CDD.
<img width="921" height="583" alt="image" src="https://github.com/user-attachments/assets/f591b39c-5113-404b-a6cb-cae1627d2394" />

2. En el campo **Enter Query Sequence**, copiar y pegar la secuencia de proteína en formato FASTA.
<img width="393" height="472" alt="image" src="https://github.com/user-attachments/assets/4d71b687-4d70-4fcd-99d9-11f773938afe" />

3. Mantener los parámetros por defecto, los cuales son adecuados para análisis exploratorios.
<img width="921" height="522" alt="image" src="https://github.com/user-attachments/assets/d2fb975a-8764-4464-b2ee-4001c864d089" />

4. Ejecutar el análisis haciendo clic en el botón **Submit**.
<img width="921" height="439" alt="image" src="https://github.com/user-attachments/assets/ead1bae0-62a3-4a07-b4f6-1a466c1583d7" />

### 👀 Interpretación de los resultados

Al aparecer el resultado de CDD se observan varias secciones importantes:

#### 1. Encabezado del análisis

En la parte superior se muestra:

- Nombre de la proteína evaluada
- Identificador de referencia (ej. NP_000508)
- Base de datos utilizada: CDSEARCH / cdd
- Versión de CDD
- E-value cutoff

Esta sección indica cómo se ejecutó la búsqueda y con qué parámetros. Sirve para verificar bajo qué condiciones se realizó el análisis.

<img width="921" height="112" alt="image" src="https://github.com/user-attachments/assets/29d4909a-813b-478d-ae7e-ad7760521fda" />

#### 2. Clasificación de la proteína

Debajo del encabezado aparece la sección: *Protein classification*.

Aquí se muestra:
- A qué familia proteínica pertenece la secuencia
- Un resumen general sobre su función

CDD asigna automáticamente una familia, esta atribución se basa en los dominios detectados.

<img width="921" height="152" alt="image" src="https://github.com/user-attachments/assets/e1a20d11-abe6-40ac-a380-fd04caaae726" />

#### 3. Barra de la secuencia (Query)

En este apartado se muestra:

- Una barra horizontal que representa toda la longitud de la proteína
- La numeración de los aminoácidos (de principio a fin)
- Regiones funcionales detectadas dentro del dominio, tales como:
  - *Sitios de unión al grupo hemo (heme binding site)*: responsables de la interacción con el grupo hemo
  - *Interfaces de tetramerización (tetramer interface)*: implicadas en la formación de la estructura funcional de la hemoglobina.

<img width="921" height="61" alt="image" src="https://github.com/user-attachments/assets/1bd1df00-532a-4298-b1e8-8566c83d2ad6" />

#### 4. Opciones de visualización

La interfaz permite cambiar entre diferentes tipos de vista, desde una forma simple a una más detallada, así como ajustar el zoom y ocultar o mostrar características. Estas opciones sirven para visualizar regiones específicas con más claridad.

<img width="921" height="62" alt="image" src="https://github.com/user-attachments/assets/504d2ddd-3b22-42ed-b415-e1b98348531f" />

#### 5. Dominios específicos (Specific hits)

Un **specific hit** es un dominio conservado bien definido que coincide de forma significativa con la secuencia analizada.

Aquí se muestra:
- Nombre del dominio
- Accession
- Intervalo (posición en la proteína)
- E-value

Si el dominio cubre gran parte de la proteína, suele ser el dominio principal. Un E-value muy bajo indica una coincidencia confiable.

<img width="921" height="32" alt="image" src="https://github.com/user-attachments/assets/0e4b7cd5-3235-40a6-aa1c-5c7c3e81304f" />

#### 6. Superfamily architecture

Debajo de los specific hits aparece la superfamilia **globin-like**, correspondiente a la superfamilia de las globinas. Este dominio abarca la mayor parte de la secuencia.

Aquí se organizan los dominios de forma jerárquica:
- Specific hit: dominio concreto
- Superfamily: grupo más amplio de dominios relacionados

La detección de estas regiones confirma la identidad funcional de la proteína analizada.

<img width="921" height="27" alt="image" src="https://github.com/user-attachments/assets/eb23ede5-d2cc-40a5-ad7e-5f0ad70c347e" />

#### 7. Tabla de dominios 

En la parte inferior aparece una tabla con columnas como:
- Name
- Accession
- Description
- Interval
- E-value

Este apartado ayuda a confirmar cuántos dominios fueron detectados, ver qué parte de la secuencia cubren y comparar E-values si hay más de un dominio.

<img width="921" height="71" alt="image" src="https://github.com/user-attachments/assets/8b8adfb7-0408-45d8-8097-308378a96b36" />

## ⚡ Conclusión
La herramienta Conserved Domain Database permite identificar dominios conservados y clasificar proteínas a partir de regiones funcionales bien caracterizadas. En este tutorial se mostró cómo utilizar su interfaz web e interpretar sus resultados básicos, lo que facilita la anotación funcional y complementa otros análisis bioinformáticos.

## 📚 Bibliografía

CDD. (s. f.). Database Commons. https://ngdc.cncb.ac.cn/databasecommons/database/

NCBI Conserved Domain Database (CDD) help. (s. f.). https://www.ncbi.nlm.nih.gov/Structure/cdd/cdd_help.shtml

Yang, M., Derbyshire, M. K., Yamashita, R. A., & Marchler-Bauer, A. (2020). NCBI's Conserved Domain Database and Tools for Protein Domain Analysis. Current protocols in bioinformatics, 69(1), e90. https://doi.org/10.1002/cpbi.90
