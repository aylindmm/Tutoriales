# 🧬 Flujo de trabajo general de un análisis RNA-seq

## 🎯 Objetivo
Comprender las etapas principales de un análisis de RNA-seq, desde la obtención de los datos crudos hasta la interpretación de los resultados, y conocer las herramientas que se utilizan en cada paso.

## 🧠 ¿Qué es RNA-seq?
El **RNA-seq (secuenciación del transcriptoma)** es una técnica que permite medir la expresión génica de un organismo.  

Funciona secuenciando los fragmentos de ARN mensajero (ARNm) convertidos a ADN complementario (cDNA), y luego contando cuántas veces aparece cada gen.

El objetivo principal es comparar la expresión génica entre diferentes condiciones biológicas, por ejemplo: Tratamiento vs. control.

## 🧩 Fases

El análisis de RNA-seq consta de siete etapas principales, que se ejecutan una tras otra.

A continuación se muestra el flujo completo con su propósito, herramientas comunes y salidas generadas.

| **Etapa** | **Descripción** | **Herramientas comunes** | **Salida generada** |
|---------------|--------------------|-----------------------------|-------------------------|
| **1. Obtención de datos** | Descarga de datos crudos | Galaxy, SRA Toolkit | Archivos `.fastq` |
| **2. Control de calidad** | Evaluar la calidad de las lecturas crudas | FastQC | Reportes `.html` |
| **3. Filtrado y recorte** | Eliminar adaptadores y bases de baja calidad | TrimGalore!, Trimmomatic | Archivos `.fastq` limpios |
| **4. Alineamiento** | Mapear las lecturas contra un genoma de referencia | STAR, HISAT2 | Archivos `.bam` |
| **5. Cuantificación** | Contar lecturas asignadas a cada gen | featureCounts, HTSeq-count | Tabla de conteos |
| **6. Análisis diferencial** | Identificar genes con expresión distinta entre condiciones | DESeq2, EdgeR | Tabla de genes con valores p |
| **7. Interpretación biológica** | Analizar funciones, rutas y procesos enriquecidos | DAVID, GO, KEGG | Gráficos, listas de genes |


Cada etapa depende de la anterior.  

Si los datos crudos son de mala calidad o tienen adaptadores, los errores se propagarán hacia los siguientes pasos.

![workflow](/RNAseq/Imagenes/workflow2.png)

## ☁️ Galaxy: una plataforma para RNA-seq
**Galaxy** es una plataforma web gratuita que permite realizar análisis bioinformáticos sin usar la línea de comandos.  
Todo se hace mediante una interfaz gráfica, ideal para principiantes.

Ventajas:
- Accesible desde cualquier navegador  
- Guarda tu historial de análisis  
- Incluye cientos de herramientas preinstaladas  

## 🧰 Herramientas principales utilizadas en Galaxy para RNA-seq

| **Etapa** | **Herramienta Galaxy** | **Propósito** |
|---------------|---------------------------|------------------|
| Control de calidad | **FastQC** | Evalúa la calidad de las lecturas crudas |
| Recorte y limpieza | **TrimGalore!** / **Trimmomatic** | Elimina adaptadores y bases de baja calidad |
| Alineamiento | **HISAT2** / **STAR** | Mapea las lecturas al genoma de referencia |
| Cuantificación | **featureCounts** / **HTSeq-count** | Cuenta lecturas por gen |
| Análisis diferencial | **DESeq2** | Compara condiciones para identificar genes diferencialmente expresados |
| Visualización e interpretación | **Volcano Plot**, **GO/KEGG Analysis** | Representa resultados y funciones biológicas |

## 🧠 Conceptos clave a recordar

| **Concepto** | **Descripción** |
|----------------|------------------|
| **Lectura (read)** | Fragmento de secuencia de ADNc generado por el secuenciador. |
| **FASTQ** | Archivo que contiene las lecturas y su calidad asociada. |
| **Adaptador** | Secuencia técnica añadida durante la preparación; debe eliminarse. |
| **Alineamiento (mapping)** | Proceso de colocar cada lectura en su posición en el genoma. |
| **Expresión diferencial** | Diferencia estadísticamente significativa en la expresión de un gen entre condiciones. |


## 📚 Recursos y lecturas recomendadas

- [Galaxy Training — Introduction to RNA-seq](https://training.galaxyproject.org/training-material/topics/transcriptomics/tutorials/ref-based/tutorial.html)   
- [FastQC Manual](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)   

----

> ## 🧭 **Siguiente paso:** continúa con el tutorial [Descarga de datos de secuenciación usando Galaxy](/RNAseq/Descarga/README.md)

