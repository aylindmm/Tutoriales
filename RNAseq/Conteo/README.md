# Conteo


## ¿Qué es FeatureCounts?

FeatureCounts es una herramienta que asigna lecturas alineadas a anotaciones genómicas (genes o exones) utilizando un archivo de anotación en formato GTF o GFF.

Galaxy ofrece una implementación gráfica de FeatureCounts que conserva la funcionalidad y eficiencia de la versión por línea de comandos.

---

## Requisitos

Antes de ejecutar FeatureCounts en Galaxy, asegúrate de contar con los siguientes archivos:

- Archivo BAM alineado y ordenado (salida de HISAT2, STAR, Bowtie2, etc.)
- Archivo de anotación GTF o GFF correspondiente al mismo genoma de referencia

---

## Uso de FeatureCounts en Galaxy

### 1. Seleccionar la herramienta

En el panel izquierdo de Galaxy:
1. Busca "FeatureCounts"
2. Selecciona la herramienta "featureCounts"
3. <img width="540" height="562" alt="image" src="https://github.com/user-attachments/assets/94dc975e-2f5b-4043-bac2-0a6e8a15e1a6" />

---

### 2. Configuración principal

#### Inputs

- Aligned reads (BAM): archivos BAM alineados (es la carpeta que obtuviste en el paso anterior de alineamiento con STAR)
- Gene annotation file: archivo GTF o GFF (mismo archivo que usaste en el paso anterior)

  <img width="1053" height="508" alt="image" src="https://github.com/user-attachments/assets/d60ecfd9-3d67-43db-8b19-0015ab5c8c7c" />

---

### 3. Parámetros importantes

#### Strand specificity

La opción correcta depende del protocolo de preparación de la librería:

- Unstranded: No
- Stranded (forward): Yes
- Stranded (reverse): Reverse

  **En este caso las muestras son unstranded**

  <img width="699" height="126" alt="image" src="https://github.com/user-attachments/assets/e81a2658-fa88-4e12-85a2-d029efbfe71f" />

NOTA: si no conoces el tipo de librería que usaste en tu muestra, puedes usar la herramienta *Infer Experiment* de Galaxy.

#### Feature type

Para RNA-seq estándar se recomienda:
- exon

Otras opciones incluyen:
- gene
- CDS

El valor seleccionado debe coincidir con la tercera columna del archivo GTF.

<img width="672" height="117" alt="image" src="https://github.com/user-attachments/assets/7fefb57d-5d12-42f5-8b58-76f336e47ffb" />

---

#### Gene identifier

Es la columna del archivo GTF que se usará para asignar nombre a los genes. En este caso queremos que nos dé el nombre del gen.

- gene_name <-- debe estar escrito tal cual aparece aquí

<img width="685" height="198" alt="image" src="https://github.com/user-attachments/assets/f1242282-3fff-4900-a838-d956eba09221" />

---

#### Lecturas pareadas

Si nuestras muestras son **paired end** debemos seleccionar la opción *Yes, paired end and count them as a single fragment*

<img width="693" height="345" alt="image" src="https://github.com/user-attachments/assets/da61dfbe-55c5-4a68-be5b-234251df2816" />


---

### 4. Ejecutar

Haz clic en **Run Tool** y espera a que el análisis finalice correctamente.

---

## Resultados

### Tabla de conteos

Si todo sale bien, obtendrás 2 carpetas.
<img width="422" height="142" alt="image" src="https://github.com/user-attachments/assets/88592b15-e9a4-418f-aa1b-f43ae897a133" />

La primera, llamada **Summary**, contiene:
  - Lecturas asignadas
  - Lecturas no asignadas (ambiguous, no features, etc.)
  
  Un porcentaje elevado de lecturas no asignadas puede indicar problemas con:
  - El genoma de referencia
  - El archivo de anotación
  - La orientación de la librería
  - El tipo de feature seleccionado

La segunda, llamada **Counts**, contiene un archivo por muestra. La tabla está organizada de la siguiente manera:
  - Filas: genes
  - Columnas: muestras
  - Valores: número de lecturas asignadas

<img width="673" height="500" alt="image" src="https://github.com/user-attachments/assets/d15f30e9-bd7c-4c1c-abaa-ff8cb394580d" />

Este archivo puede utilizarse directamente en herramientas de expresión diferencial como:
- DESeq2
- edgeR
- limma-voom


---

## Errores comunes

- Utilizar un GTF que no corresponde a la versión del genoma
- Seleccionar un identificador de gen incorrecto
- Ignorar la especificidad de cadena
- Usar archivos BAM sin ordenar

---

## Conclusión

FeatureCounts en Galaxy es una herramienta robusta y accesible para generar matrices de conteo en análisis de RNA-seq. Una selección adecuada del archivo de anotación, el tipo de feature y la especificidad de cadena es clave para obtener resultados confiables y reproducibles.

---

 > ## 🧭 **Siguiente paso:** continúa con el tutorial [Expresión diferencial](/RNAseq/Expresion_Diferencial/README.md)
