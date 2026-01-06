# 🧬 Alineamiento de lecturas al genoma de referencia

El alineamiento de lecturas al genoma de referencia es uno de los primeros y más importantes pasos en el análisis de datos genómicos. Este proceso consiste en ubicar secuencias cortas de ADN o RNA, llamadas lecturas, dentro de un genoma de referencia previamente conocido.

A través del alineamiento es posible estudiar la expresión génica, identificar regiones de interés y sentar las bases para análisis bioinformáticos más avanzados.

## 🎯 Objetivo

Generar y analizar un alineamiento entre lecturas de RNA-seq y un genoma de referencia.

## 🧰 ¿Qué necesitas antes de empezar?

Para realizar el alineamiento se requiere:

- Lecturas de RNA-seq  
- Un genoma de referencia  
- Un archivo de anotación génica  
- Un alineador bioinformático  

En este tutorial se utilizará el alineador **RNA-STAR** dentro de la plataforma **Galaxy**.

![STAR](https://github.com/user-attachments/assets/fab75cd4-0bec-45c2-9013-892f4cdd4221)

## 📂 Archivos necesarios

Para este análisis se requieren tres archivos:

1. **Lecturas de RNA-seq**  
   - Pueden ser *single-end* o *paired-end*

2. **Genoma de referencia**  
   - En este tutorial se utilizará el genoma humano **hg19**

3. **Archivo de anotación génica**  
   - Formato **GTF**
   - Contiene la información sobre la posición de los genes en el genoma

## 🧪 Metodología

### 1. Identificar el tipo de lecturas

Primero, es importante saber qué tipo de datos se tienen:

- **Single-end**: una lectura por fragmento
- **Paired-end**: dos lecturas por fragmento

Esta información es necesaria para que el alineador procese correctamente los datos.


> 📌 **Recuerda**  
> Elegir mal el tipo de lectura puede afectar la calidad del alineamiento.


<img width="1046" height="429" alt="image" src="https://github.com/user-attachments/assets/5eb6181d-307e-417c-93d1-84f29fa21ea3" />

### 2. Seleccionar las lecturas de entrada

Selecciona la carpeta o los archivos que contienen las lecturas de RNA-seq que se van a alinear.

Asegúrate de que los archivos correspondan al mismo experimento.

### 3. Seleccionar el genoma de referencia

A continuación, se selecciona el genoma de referencia.  
En este caso se utilizará el genoma humano **hg19**.

Se indica que se trabajará con un genoma precargado, el cual ya cuenta con un índice disponible.

<img width="975" height="155" alt="image" src="https://github.com/user-attachments/assets/eb45ec5e-0dd6-4b18-a536-da9149a76d9f" />


#### ¿Qué es un índice?

Un índice es una estructura de datos preprocesada que representa el genoma de referencia. RNA-STAR utiliza este índice para acelerar el proceso de alineamiento, permitiendo encontrar rápidamente las posibles ubicaciones de cada lectura.

> 📝 **Nota**  
> Sin un índice, el alineamiento sería mucho más lento y computacionalmente costoso.

### 4. Seleccionar el archivo de anotación génica

El siguiente paso es seleccionar el archivo que contiene la información sobre la posición de los genes en el genoma. Este archivo debe estar en formato **GTF**.

El archivo GTF puede descargarse desde el siguiente enlace:  
https://usegalaxy.org/api/datasets/f9cad7b01a4721358306b8d463f168f9/display?to_ext=gtf  

Una vez descargado, debe subirse al historial de Galaxy y seleccionarse para el análisis.

> 💡 **Tip**  
> El archivo GTF ayuda al alineador a reconocer regiones génicas y sitios de empalme, lo cual es especialmente importante en datos de RNA-seq.

### 5. Revisar los parámetros

Antes de ejecutar el análisis, revisa que:

- Las lecturas estén correctamente seleccionadas  
- El genoma de referencia sea hg19  
- El archivo GTF sea el correcto  

No es necesario modificar parámetros avanzados para este ejercicio.

> 📌 **Recuerda**  
> Los valores por defecto de RNA-STAR son adecuados para la mayoría de los análisis básicos.

### 6. Ejecutar el alineamiento

Cuando todos los parámetros estén correctamente configurados, inicia el análisis haciendo clic en el botón **“Run tool”**.

RNA-STAR comenzará a alinear las lecturas contra el genoma de referencia.

## 📊 Evaluación de la calidad del alineamiento

### 7. Generar el reporte de calidad

Una vez finalizado el alineamiento, se utiliza la herramienta **MultiQC** para generar un reporte de calidad.

MultiQC reúne y resume la información generada durante el alineamiento, facilitando la interpretación de los resultados.

### 8. Analizar los resultados

Al analizar los gráficos generados por MultiQC, se deben considerar aspectos como:

- El porcentaje de lecturas alineadas
- La calidad general del alineamiento
- La distribución de las lecturas

> 📝 **Nota**  
> Un buen alineamiento suele mostrar un alto porcentaje de lecturas correctamente mapeadas.

## ✅ Conclusión

El alineamiento de lecturas al genoma de referencia es un paso fundamental en el análisis de datos de RNA-seq. Utilizar herramientas como RNA-STAR permite realizar alineamientos eficientes, mientras que MultiQC facilita la evaluación de la calidad del proceso.

Un alineamiento exitoso es la base para análisis posteriores, como la cuantificación de expresión génica y otros estudios bioinformáticos.

## 📌 Recuerda

- Verificar siempre el tipo de lecturas
- Usar el genoma de referencia correcto
- Revisar la calidad del alineamiento antes de continuar con otros análisis

## 📚 Referencias

- STAR Aligner: https://github.com/alexdobin/STAR
- Galaxy Project: https://usegalaxy.org
- MultiQC: https://multiqc.info
