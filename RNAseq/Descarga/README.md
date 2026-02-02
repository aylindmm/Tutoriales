# 🧬Descarga de datos de secuenciación usando Galaxy

## 🎯 Objetivo
Aprender a descargar archivos de datos de secuenciación a partir de estudios públicos disponibles en la base de datos **GEO** del **NCBI**, utilizando la plataforma **Galaxy**, una herramienta en línea gratuita y accesible para análisis bioinformáticos.

## 🧠 1. ¿Qué es GEO y por qué lo usamos?

[**GEO (Gene Expression Omnibus)**](https://www.ncbi.nlm.nih.gov/geo/) es una base de datos pública del NCBI (National Center for Biotechnology Information) que almacena resultados de experimentos genómicos realizados por científicos de todo el mundo.

![GEO](/Datos_FASTQ/Imágenes/GEO.png)

En GEO se pueden encontrar estudios que analizan:
- Expresión génica (RNA-seq, microarreglos)
- Variaciones genéticas (GWAS)
- Secuenciación de genomas o exomas (WGS, WES)

Cada estudio tiene un código llamado *GSE* (por ejemplo, `GSE219205`), y dentro de él, cada muestra tiene su propio identificador *SRR* (por ejemplo, `SRR22493369`).  

Estos códigos permiten acceder a los archivos crudos (en formato FASTQ), que son los datos directos generados por el secuenciador.

## 🧬 2. ¿Qué es un archivo FASTQ?

Un archivo *FASTQ* es un formato estándar para almacenar lecturas de secuenciación (reads) junto con su calidad.

Cada secuencia ocupa 4 líneas consecutivas.

Ejemplo de cómo luce una lectura en formato FASTQ:

<pre>
@SRR22493369.1
GATCGTAGTTCCAGT...
+
!''*((((***+))%%%++)(%%%%).1***-+*''
</pre>

- `@` → identificador de la lectura  
- Segunda línea → secuencia de nucleótidos (contiene la secuencia de bases de ADN o ARN)
- `+` → separador  
- Cuarta línea → calidad de cada base (Es una cadena de símbolos ASCII que codifican la calidad de cada nucleótido)

Estos archivos son el punto de partida para todos los análisis bioinformáticos (control de calidad, alineamiento, expresión, etc.).

💡 Datos útiles
- Extensión: .fastq o .fq
- Puede estar comprimido: .fastq.gz
- Cada archivo puede contener millones de lecturas

## ☁️ 3. ¿Qué es Galaxy?
[**Galaxy**](https://galaxy-main.usegalaxy.org/) es una plataforma web gratuita para análisis bioinformáticos.  
Permite realizar tareas complejas sin necesidad de programar, simplemente combinando herramientas mediante una interfaz gráfica.  

Ventajas:
- Gratuita y basada en la nube (no necesitas instalar nada)
- Permite guardar tu trabajo y resultados
- Ideal para principiantes

![Galaxy](/Datos_FASTQ/Imágenes/Galaxy.png)

Puedes acceder a Galaxy en distintos servidores:
- 🌎 [https://usegalaxy.org](https://usegalaxy.org) → Servidor de Estados Unidos (recomendado)
- 🇪🇺 [https://usegalaxy.eu](https://usegalaxy.eu) → Servidor de Europa
- 🇦🇺 [https://usegalaxy.org.au](https://usegalaxy.org.au) → Servidor de Australia

> 🟢 Para este tutorial usaremos el servidor de Estados Unidos.

## 🔐 4. Crear una cuenta en Galaxy

1. Entra a [https://usegalaxy.org](https://usegalaxy.org)  
2. En la esquina superior derecha, haz clic en **Login or Register**.  
3. Selecciona **Register here**.  
4. Completa el formulario con tu correo personal.  
5. Abre tu correo y confirma tu registro.  

Una vez que inicies sesión, verás tres zonas en la interfaz:
- **Izquierda:** Herramientas disponibles  
- **Centro:** Área de trabajo (donde se configuran los análisis)  
- **Derecha:** Historial de tus resultados  

Ahora crea una nueva historia en Galaxy
   1. En la esquina superior derecha de Galaxy, haz clic en **Histories**.
   2. Selecciona *Create New History* ✚ .
![newhistory](/Descarga/Imágenes/History.png)
   3. Ponle un nombre descriptivo, por ejemplo: FASTQC. 
   Esto te ayudará a mantener organizados todos los archivos dentro de la interfaz.

## 🔎 5. Buscar el estudio en GEO

Ahora buscaremos el estudio cuyos datos descargaremos.

1. Entra al sitio: [https://www.ncbi.nlm.nih.gov/geo/](https://www.ncbi.nlm.nih.gov/geo/)
2. En la barra de búsqueda escribe el código del estudio: **GSE230372**
3. Presiona **Search**.
![busqueda](/Datos_FASTQ/Imágenes/busqueda.png)

Esto te llevará a la página del estudio.  

En ella verás información como:
- Título del experimento  
- Autores  
- Fecha de publicación  
- Tipo de tecnología utilizada (por ejemplo, *RNA-seq*)  
- Descripción de las muestras  


> 💡 Cada estudio GSE contiene varias muestras, y cada muestra tiene un identificador *SRR* asociado al repositorio SRA.

## 🧩 6. Identificar los SRR en GEO

1. En la parte inferior de la página del estudio, busca el enlace **SRA Run Selector**.  

![SRA](/Datos_FASTQ/Imágenes/SRA.png)

2. Al abrirlo, verás una tabla con todos los **SRR** del estudio, por ejemplo:
   - SRR24282443 a SRR24282451 (para GSE230372)

Cada SRR corresponde a una muestra individual.

![SRR](/Datos_FASTQ/Imágenes/SRR.png)

## 💾 7. Descargar las lecturas desde Galaxy

Ahora vamos a usar Galaxy para obtener los archivos *FASTQ* de esos SRR.

### 1. Buscar la herramienta adecuada
  - En Galaxy, ve al **panel izquierdo** y en la barra de búsqueda escribe: **NCBI SRA**.
  - Elige la herramienta: **Download and Extract Reads in FASTQ format from NCBI SRA**.

### 2. Ingresar los códigos SRR

  - En el campo **Accession(s)** escribe los identificadores **SRR**, uno por línea:


#### Estudio — GSE230372

SRR24282443,
SRR24282444,
SRR24282445,
SRR24282449,
SRR24282450,
SRR24282451.

  - Haz clic en **Run** para comenzar.

![SRRA](/Datos_FASTQ/Imágenes/SRRA.png)

### 3. Revisar el progreso

En el **panel derecho (Historial)** verás las tareas en ejecución.  
Los colores indican el estado:
- 🟢 Verde → completado correctamente  
- 🟡 Amarillo → en proceso  
- 🔴 Rojo → error

> Puede tardar varios minutos, dependiendo del tamaño de las muestras.

### 4. Verificar los resultados

Cuando termine la ejecución, verás nuevos elementos en tu historial.
Los archivos FASTQ no aparecerán uno por uno, sino agrupados en colecciones, es decir, conjuntos de archivos relacionados.

Esto es normal, Galaxy organiza los resultados automáticamente según el tipo de datos.

#### ¿Qué significa cada colección?

Al finalizar, deberías ver hasta tres colecciones diferentes:
| **Colección** | **Contenido** | **Cuándo aparece** |
|------------------|------------------|------------------------|
| **Paired-end data** | Lecturas en pares (`_1.fastq` y `_2.fastq`) | Si las muestras fueron secuenciadas en modo paired-end |
| **Single-end data** | Lecturas individuales (`.fastq`) | Si alguna muestra fue secuenciada en modo single-end |
| **Other data** | Lecturas que no pudieron clasificarse (por ejemplo, una lectura de un par sin su compañera) | Aparece ocasionalmente |

💡Nota: Es posible que algunas colecciones estén vacías si las muestras que descargaste no contienen ese tipo de datos.

## ✅ 8. Resultado final esperado
Después de correr la herramienta correctamente, deberás tener en tu historial:
- 1 colección con las lecturas paired-end
- 1 colección con lecturas single-end


Y dentro de cada colección, los archivos .fastq listos para analizar con **FastQC**.

## 📚 Recursos y lecturas recomendadas

- 🌎 [Galaxy Project (EE. UU.)](https://usegalaxy.org)  
- 🧬 [NCBI GEO Database](https://www.ncbi.nlm.nih.gov/geo/)  
- 💾 [NCBI SRA — Sequence Read Archive](https://www.ncbi.nlm.nih.gov/sra)  
- 📘 [Tutorial oficial de introducción a Galaxy](https://training.galaxyproject.org/training-material/topics/introduction/)





