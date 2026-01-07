# 🧬 Alineamiento

El alineamiento de lecturas al genoma de referencia es uno de los primeros y más importantes pasos en el análisis de datos genómicos. Este proceso consiste en ubicar secuencias cortas de ADN, llamadas lecturas, dentro de un genoma de referencia previamente conocido.

A través del alineamiento es posible estudiar la expresión génica, identificar variantes de interés y sentar las bases para análisis bioinformáticos más avanzados.

## 🎯 Objetivo

Generar y analizar un alineamiento entre lecturas de RNA-seq y un genoma de referencia, utilizando el alineador RNA-STAR en la plataforma Galaxy.

## 📂 Herramientas y archivos necesarios

Antes de comenzar, asegúrate de contar con:
- **Lecturas de RNA-seq**
  - Pueden ser *single-end* o *paired-end*
- **Genoma de referencia**
  - En este tutorial se utilizará el genoma humano hg19
- **Archivo de anotación génica en formato GTF**
  - Contiene la información sobre la posición de los genes en el genoma
- Alineador: **RNA-STAR**
- Herramienta de control de calidad: **MultiQC**
- Plataforma de análisis: **Galaxy**

![STAR](https://github.com/user-attachments/assets/fab75cd4-0bec-45c2-9013-892f4cdd4221)

## 🖥️ Metodología

### 1.  Identificar los datos de entrada

Se elige la herramienta **RNA-STAR**, la cual está diseñada para llevar a cabo el alineamiento rápido y eficiente de lecturas de RNA-seq. Este alineador es especialmente útil para manejar grandes volúmenes de datos y para determinar correctamente los sitios de empalme característicos de este tipo de secuencias.

Posteriormente, se identifican y cargan los archivos de lecturas que se utilizarán como datos de entrada. Estas lecturas provienen de experimentos de secuenciación masiva y se presentan en formato FASTQ.

Es importante verificar si los datos corresponden a:
- **Single-end** (un archivo de lecturas), o
- **Paired-end** (dos archivos de lecturas).

Esta información es necesaria para configurar correctamente el alineador y asegurar un alineamiento adecuado.

<img width="1046" height="429" alt="image" src="https://github.com/user-attachments/assets/5eb6181d-307e-417c-93d1-84f29fa21ea3" />

### 2. Elegir del genoma de referencia

A continuación, se selecciona el genoma de referencia.  
En este caso se utilizará el genoma humano **hg19**.

Se indica que se trabajará con un genoma precargado, el cual ya cuenta con un índice disponible.

<img width="975" height="155" alt="image" src="https://github.com/user-attachments/assets/eb45ec5e-0dd6-4b18-a536-da9149a76d9f" />

#### 📝 ¿Qué es un índice?
Un índice es una estructura de datos preprocesada que representa el genoma de referencia. RNA-STAR utiliza este índice para acelerar el proceso de alineamiento, permitiendo encontrar rápidamente las posibles ubicaciones de cada lectura. 

### 3. Incorporar el archivo de anotación génica

El siguiente paso es escoger el archivo que contiene la información sobre la posición de los genes en el genoma. Este archivo debe estar en formato **GTF**.

El archivo GTF puede descargarse desde el siguiente enlace:  
https://usegalaxy.org/api/datasets/f9cad7b01a4721358306b8d463f168f9/display?to_ext=gtf  

Una vez descargado, debe subirse al historial de Galaxy y seleccionarse para el análisis.

<img width="975" height="675" alt="image" src="https://github.com/user-attachments/assets/9b22b6b6-d554-4656-a733-106be557dac5" />

### 4. Revisar los parámetros y efectuar el alineamiento

Antes de ejecutar el análisis, revisa que:

- Las lecturas estén correctamente seleccionadas  
- El genoma de referencia sea hg19  
- El archivo GTF sea el correcto  

Cuando todos los parámetros estén correctamente configurados, inicia el análisis haciendo clic en el botón **“Run tool”**.

RNA-STAR comenzará a alinear las lecturas contra el genoma de referencia.

## 📊 Evaluación de la calidad del alineamiento

### 5. Generar el reporte de calidad

Una vez finalizado el alineamiento, se utiliza la herramienta **MultiQC** para generar un reporte de calidad. 

MultiQC reúne y resume la información generada durante el alineamiento, facilitando la interpretación de los resultados.

<img width="550" height="713" alt="image" src="https://github.com/user-attachments/assets/63256493-27fb-42ce-9e9c-1862b4b5e694" />

### 6. Analizar los resultados

Al analizar los gráficos generados por MultiQC, se deben considerar aspectos como:

- El porcentaje de lecturas alineadas
- La calidad general del alineamiento
- La distribución de las lecturas

> 📝 Un buen alineamiento suele mostrar un alto porcentaje de lecturas correctamente mapeadas.

## 📌 Recuerda

- Verificar siempre el tipo de lecturas
- Usar el genoma de referencia correcto
- Revisar la calidad del alineamiento antes de continuar con otros análisis

## ✅ Conclusión

El alineamiento de lecturas al genoma de referencia es un paso fundamental en el análisis de datos de RNA-seq. Utilizar herramientas como RNA-STAR permite realizar alineamientos eficientes, mientras que MultiQC facilita la evaluación de la calidad del proceso.

Un alineamiento exitoso es la base para análisis posteriores, como la cuantificación de expresión génica y otros estudios bioinformáticos.


