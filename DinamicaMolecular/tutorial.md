# 🧬 Aprende Dinámica Molecular con Gromacs
## Introducción
La **dinámica molecular (MD)** es una técnica computacional que permite simular el movimiento de átomos y moléculas a lo largo del tiempo, resolviendo las leyes de Newton para cada partícula del sistema.  

Piensa en cada átomo como una pequeña bolita unida a otras por “resortes invisibles”. La dinámica molecular nos permite calcular cómo estas bolitas se mueven, chocan y se reorganizan, simulando lo que ocurre en la naturaleza, pero en una computadora.

## 🖥️ ¿Qué es GROMACS?
**GROMACS** (acrónimo original de Groningen Machine for Chemical Simulations) es un paquete de software de DM, de alto rendimiento, libre y de código abierto, diseñado principalmente para realizar simulaciones de sistemas biomoleculares como proteínas, lípidos y ácidos nucleicos. Realiza cálculos complejos automáticamente, como fuerzas entre átomos y movimientos en el tiempo. Es rápido, confiable y puede integrarse con **Galaxy**, un entorno gráfico que facilita el uso para principiantes.  

## 🎯 Qué aprenderás en este tutorial
1. Familiarizarse con el funcionamiento de GROMACS disponible en la plataforma Galaxy. 
2. Comprender el flujo de trabajo empleado en una simulación de dinámica molecular.
3. Realizar una simulación corta (1 ns) de una proteína.

Aunque no tengas experiencia previa con GROMACS ni con simulación molecular, aprenderás paso a paso.

El flujo general de la simulación incluye:

![Flujo general de simulación][workflow.png] <!-- No se ve -->


💡**Antes de iniciar:**  
- Asegúrate de tener tu cuenta activa en [Galaxy](https://usegalaxy.org/)
- Haber realizado previamente el curso: [Introducción a Galaxy](https://training.galaxyproject.org/training-material/topics/introduction/)

## Guía del Tutorial

- [Pasos previos](#pasos-previos)
- [Obtención de datos](#obtención-de-datos)
  - [Lisozima](#lisozima)
- [Configuración](#configuración)
- [Solvatación](#solvatación)
- [Minimización de energía](#minimización-de-energía)
- [Equilibro](#equilibro)
-  [Equilibrio NVT](#equilibrio-NVT)
-  [Equilibrio NPT](#equilibrio-NPT)
- [Simulación de producción](#simulación-de-producción)
- [Análisis de resultados](#análisis-de-resultados)
- [Conclusión](#conclusión)

## 🧩Pasos previos
Antes de iniciar una simulación de DM, es necesario preparar el sistema siguiendo una serie de pasos. Este procedimiento se puede organizar en varias fases:
1. **Preparación del sistema**: se cargan los datos de la proteína y se rodea con moléculas de agua, agregando también iones para neutralizar la carga del sistema.
2. **Minimización de energía**: se ajustan las posiciones atómicas para eliminar tensiones o solapamientos no físicos en la estructura inicial.
3. **Equilibración del solvente**: se estabiliza el sistema respecto a la temperatura y la presión, realizando primero una fase a volumen constante (NVT) y luego otra a presión constante (NPT).
4. **Simulación de producción**: en esta etapa se generan los datos de la trayectoria, que es un archivo que registra las posiciones de todos los átomos a lo largo del tiempo, mostrando cómo se mueve la molécula. 

Con programas de visualización molecular, estas trayectorias pueden reproducirse como una especie de “película”, permitiendo observar el comportamiento dinámico de la proteína. Más adelante, se explicará cada una de estas fases con mayor detalle.


## 📁 Obtención de datos
Para llevar a cabo las simulaciones, es fundamental disponer de los datos adecuados. Primero, se requiere la estructura tridimensional de la proteína, normalmente obtenida de bases de datos como el [PDB](https://www.rcsb.org/) (Protein Data Bank). Esta estructura sirve como punto de partida para la simulación.

<pre><span style="background-color:yellow">Protein Data Bank</span>
El PDB almacena las coordenadas atómicas de biomoléculas y ofrece una forma estandarizada de presentar la información estructural obtenida a partir de estudios de difracción de rayos X y resonancia magnética nuclear (RMN). Cada una de las estructuras incluidas en esta base de datos se identifica mediante un código único de cuatro letras. Por ejemplo, el archivo PDB que utilizaremos en este tutorial corresponde al código <a href="https://www.rcsb.org/structure/1AKI" target="_blank">1AKI</a>
</pre> 

Durante y después de la simulación, se generan archivos de trayectoria y energía que contienen información detallada sobre el movimiento de los átomos y las interacciones en el sistema. Estos datos permiten analizar cómo se comporta la proteína en su entorno, identificar cambios conformacionales y estudiar propiedades dinámicas como la estabilidad y la flexibilidad de la molécula.

Para cargar la estructura inicial: 
1. **Crea un nuevo historial en Galaxy**
   1. En la esquina superior derecha de Galaxy, haz clic en *Histories*.
   2. Selecciona *Create New History*. <!-- Falta añadir imagen -->
   3. Ponle un nombre descriptivo, por ejemplo: Simulación_1AKI. 
   Esto te ayudará a mantener organizados todos los archivos de esta simulación.
2. **Descargar el archivo PDB**
   1. Busca la herramienta *Get PDB* en Galaxy.
   2. Ingresa el código de acceso PDB: 1AKI.
   3. Ejecuta la herramienta y se decargará el archivo PDB de la proteína a tu historial.
3. **Limpiar el archivo PDB (eliminar átomos que no son de la proteína)**   
   1. Abre la herramienta *Search in textfiles (grep)* en Galaxy.
   2. En el campo *Select lines from*, selecciona el archivo PDB que acabas de descargar (1AKI).
   3. Configura los siguientes parámetros:

      -**that**: `Don´t Match`.

      -**Regular Expressión**: `HETATM`.

    4. Ejecuta la herramienta y obtendrás un nuevo archivo PDB limpio para la simulación.

🔍 Explicación:
Las líneas de un archivo PDB que comienzan con *HETATM* describen átomos que no pertenecen a la cadena principal de la proteína (como iones, ligandos o moléculas de agua). Al usar la opción *Don’t Match* con la expresión regular *HETATM*, el resultado conservará únicamente las líneas correspondientes a los átomos de proteína (ATOM), eliminando las demás.

<pre><span style="background-color:yellow">Lisozima</span>
En este tutorial analizaremos la lisozima de la clara de huevo de gallina, una enzima ampliamente estudiada por su capacidad para degradar los polisacáridos presentes en las paredes celulares de numerosas bacterias.
Se trata de una proteína globular pequeña, compuesta por 129 residuos, caracterizada por su gran estabilidad, cualidades que la convierten en un modelo ideal para nuestros objetivos de estudio. <!-- Falta añadir imagen -->
</pre>
    
## 🧩 Configuración
En este paso se utiliza la herramienta **GROMACS initial setup**, que toma como entrada un archivo PDB y genera tres archivos esenciales para la simulación de MD: 

<u>1. Archivo de topología (.top)</u>
  
Describe las propiedades químicas y físicas de la molécula, como masas, tipos de átomos, enlaces y cargas. Esta información define cómo interactúan los átomos durante la simulación. Para su generación, es necesario seleccionar un campo de fuerza y un modelo de agua compatibles. En este tutorial usaremos el campo de fuerza *OPLS/AA*, adecuado para proteínas, junto con el modelo de agua *SPC/E*, que ofrece un buen equilibrio entre precisión y eficiencia.

<u>2. Archivo de estructura (.gro)</u>
   
Contiene las coordenadas tridimensionales de los átomos en el formato nativo de GROMACS. Este formato es más eficiente que el PDB y permite centrar la proteína dentro de la caja de simulación.

<u>3. Archivo de restricciones de posición (.itp)</u>

Especifica qué átomos permanecerán fijos durante las etapas de equilibración (NVT y NPT). Esto permite que el disolvente se acomode alrededor de la proteína sin distorsionar su estructura inicial.

En resumen, la herramienta crea la topología del sistema, convierte la estructura de PDB a GRO y genera las restricciones necesarias para equilibrar la simulación.

### 📦 Definición de la caja de simulación
Una vez generados estos archivos, es necesario definir la caja de simulación o celda unitaria, que representa el espacio donde se desarrollará la simulación. Esto se realiza mediante la herramienta **GROMACS structure configuration**, que permite establecer el tamaño y la forma de la caja.

La caja debe ser lo suficientemente grande para evitar que la proteína interactúe con sus propias imágenes bajo condiciones periódicas. Aunque una caja cúbica puede parecer intuitiva, en la práctica un dodecaedro rómbico es más eficiente, ya que ocupa menos volumen y reduce el número de moléculas de agua necesarias, optimizando los recursos computacionales

### 🧪 Ejecución práctica
Primero, ejecuta la herramienta *GROMACS initial setup* con los siguientes parámetros:
- **PDB input file**: `1AKI_clean.pdb` (estructura sin moléculas no proteicas)
- **Water model**: `SPC/E`
- **Force field**: `OPLS/AA`
- **Ignore hydrogens**: `No`
- **Generate detailed log**: `Yes`

Es importante que el archivo PDB de entrada contenga solo la proteína, ya que los campos de fuerza están parametrizados para aminoácidos estándar; incluir ligandos u otras moléculas podría generar errores en la topología.

A continuación, ejecuta la herramienta *GROMACS structure configuration* para definir la caja de simulación. Utiliza como entrada el archivo GRO generado en el paso anterior y selecciona:

 - **Output formal**: `GRO file`
 - **Configure box**: `Yes`
 - **Box dimensions (nm**): `1.0`
 - **Box type**: `Rectangular box with all sides equal`
 - **Generate detailed log**: `Yes`

Esta configuración define una caja simétrica y con un espacio adecuado alrededor de la proteína, garantizando que el sistema esté listo para los siguientes pasos de la simulación.

## Solvatación

## Minimización de energía

## Equilibrio

## Simulación de producción

## Análisis de resultados

## Conclusión



Esta versión está basada en el tutorial original de [Galaxy Training!](https://training.galaxyproject.org/training-material/topics/computational-chemistry/tutorials/md-simulation-gromacs/tutorial.html).

