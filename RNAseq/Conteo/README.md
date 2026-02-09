# FeatureCounts en Galaxy: Guía Paso a Paso

Este repositorio contiene un tutorial práctico para utilizar FeatureCounts en Galaxy con el objetivo de obtener conteos de genes a partir de datos de RNA-seq alineados.

---

## Introducción a Galaxy

Galaxy es una plataforma web de código abierto para análisis bioinformáticos que permite ejecutar herramientas complejas mediante una interfaz gráfica, sin necesidad de programación. Es ampliamente utilizada en análisis de RNA-seq, ChIP-seq y genómica funcional.

Entre sus principales ventajas se encuentran:
- Reproducibilidad de los análisis
- Transparencia en los parámetros utilizados
- Accesibilidad para usuarios con distintos niveles técnicos

En análisis de RNA-seq, uno de los pasos fundamentales es el conteo de lecturas por gen, proceso que puede realizarse eficientemente con FeatureCounts.

---

## ¿Qué es FeatureCounts?

FeatureCounts es una herramienta del paquete Subread que asigna lecturas alineadas a features genómicas (genes, exones o CDS) utilizando un archivo de anotación en formato GTF o GFF.

Galaxy ofrece una implementación gráfica de FeatureCounts que conserva la funcionalidad y eficiencia de la versión por línea de comandos.

---

## Requisitos

Antes de ejecutar FeatureCounts en Galaxy, asegúrate de contar con los siguientes archivos:

- Archivo BAM alineado y ordenado (salida de HISAT2, STAR, Bowtie2, etc.)
- Archivo de anotación GTF o GFF correspondiente al mismo genoma de referencia
- (Opcional) Archivo de índice del BAM (.bai)

---

## Uso de FeatureCounts en Galaxy

### 1. Cargar los datos

Sube a Galaxy:
- El archivo BAM
- El archivo GTF o GFF

Verifica que Galaxy reconozca correctamente los formatos de los archivos.

---

### 2. Seleccionar la herramienta

En el panel izquierdo de Galaxy:
1. Busca "FeatureCounts"
2. Selecciona la herramienta "FeatureCounts – count reads to genomic features"

---

### 3. Configuración principal

#### Inputs

- Aligned reads (BAM): archivo BAM alineado
- Gene annotation file: archivo GTF o GFF

---

### 4. Parámetros importantes

#### Feature type

Para RNA-seq estándar se recomienda:
- exon

Otras opciones incluyen:
- gene
- CDS

El valor seleccionado debe coincidir con la tercera columna del archivo GTF.

---

#### Gene identifier

Comúnmente se utiliza:
- gene_id

En algunos casos puede utilizarse:
- gene_name

Es importante revisar el archivo GTF antes de seleccionar este parámetro.

---

#### Strand specificity

La opción correcta depende del protocolo de preparación de la librería:

- Unstranded: No
- Stranded (forward): Yes
- Stranded (reverse): Reverse

---

#### Lecturas multimapping

Por defecto, FeatureCounts no incluye lecturas multimapping. Incluirlas puede introducir sesgos en el análisis y generalmente no se recomienda.

---

#### Output

Se recomienda generar:
- Tabla de conteos por gen
- Archivo de resumen (summary)

---

### 5. Ejecutar

Haz clic en "Run Tool" y espera a que el análisis finalice correctamente.

---

## Resultados

### Tabla de conteos

El archivo principal de salida contiene:
- Filas: genes
- Columnas: muestras
- Valores: número de lecturas asignadas

Este archivo puede utilizarse directamente en herramientas de expresión diferencial como:
- DESeq2
- edgeR
- limma-voom

---

### Archivo summary

El archivo summary incluye estadísticas sobre:
- Lecturas asignadas
- Lecturas no asignadas (ambiguous, no features, etc.)

Un porcentaje elevado de lecturas no asignadas puede indicar problemas con:
- El genoma de referencia
- El archivo de anotación
- La orientación de la librería
- El tipo de feature seleccionado

---

## Errores comunes

- Utilizar un GTF que no corresponde a la versión del genoma
- Seleccionar un identificador de gen incorrecto
- Ignorar la especificidad de cadena
- Usar archivos BAM sin ordenar

---

## Conclusión

FeatureCounts en Galaxy es una herramienta robusta y accesible para generar matrices de conteo en análisis de RNA-seq. Una selección adecuada del archivo de anotación, el tipo de feature y la especificidad de cadena es clave para obtener resultados confiables y reproducibles.

Este tutorial puede utilizarse como guía práctica o material docente.

---
