# 🧬 Aprende Dinámica Molecular con Gromacs
## 📘 Introducción
La **dinámica molecular (MD)** es una técnica computacional que permite simular el movimiento de átomos y moléculas a lo largo del tiempo, resolviendo las leyes de Newton para cada partícula del sistema.  

Piensa en cada átomo como una pequeña bolita unida a otras por “resortes invisibles”. La dinámica molecular nos permite calcular cómo estas bolitas se mueven, chocan y se reorganizan, simulando lo que ocurre en la naturaleza, pero en una computadora.

Existen diversos paquetes para realizar simulaciones de MD. Uno de los más utilizados es GROMACS, un software libre y de código abierto, que será el enfoque de este tutorial. También están disponibles en Galaxy otros programas como [NAMD](https://training.galaxyproject.org/training-material/topics/computational-chemistry/tutorials/md-simulation-namd/tutorial.html) y [CHARMM](https://academiccharmm.org/), accesibles en [Biomolecular Reaction & Interaction Dynamics Global Environment](https://github.com/scientificomputing/BRIDGE).


## 🖥️ ¿Qué es GROMACS?
[GROMACS](https://www.gromacs.org/) (acrónimo de Groningen Machine for Chemical Simulations) es un software de alto rendimiento diseñado para simular el comportamiento de biomoléculas [(Abraham, 2015)](https://www.sciencedirect.com/science/article/pii/S2352711015000059?via%3Dihub). Automatiza cálculos complejos, como las fuerzas entre átomos y su movimiento a lo largo del tiempo. Es rápido, confiable y puede integrarse fácilmente con Galaxy, un entorno gráfico que simplifica su uso, especialmente para principiantes. 

## 🎯 Qué aprenderás en este tutorial
1. Familiarizarte con GROMACS en Galaxy.
2. Comprender el flujo de trabajo de una simulación MD.
3. Realizar una simulación corta (1 ns) de una proteína.

Aunque no tengas experiencia previa con GROMACS ni con DM, aprenderás paso a paso.

🔁 El flujo general de la simulación incluye:
<img width="1920" height="1080" alt="workflow" src="https://github.com/user-attachments/assets/3f4e6359-0fb2-44f7-aee8-964dbc6c381a" />

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
- [Equilibro del sistema](#equilibro-del-sistema)
   - [Equilibrio NVT](#equilibrio-NVT)
   - [Equilibrio NPT](#equilibrio-NPT)
- [Simulación de producción](#simulación-de-producción)
- [Análisis de resultados](#análisis-de-resultados)
- [Conclusión](#conclusión)

## 🧩Pasos previos
Antes de iniciar una simulación de DM, es necesario preparar el sistema siguiendo una serie de pasos. Este procedimiento se puede organizar en varias fases:
1. **Preparación del sistema**: se cargan los datos de la proteína y se rodea con moléculas de agua, agregando también iones para neutralizar la carga del sistema.
2. **Minimización de energía**: se ajustan las posiciones atómicas para eliminar tensiones o solapamientos no físicos en la estructura inicial.
3. **Equilibración del solvente**: se estabiliza el sistema respecto a la temperatura y la presión, realizando primero una fase a volumen constante (NVT) y luego otra a presión constante (NPT).
4. **Simulación de producción**: en esta etapa se generan los datos de la trayectoria, que es un archivo que registra las posiciones de todos los átomos a lo largo del tiempo, mostrando cómo se mueve la molécula. 

Más adelante, se explicará cada una de estas fases con mayor detalle.

La trayectoria es un archivo binario que almacena las coordenadas atómicas del sistema en diferentes intervalos de tiempo, lo que permite representar el movimiento dinámico de la molécula durante la simulación. Con programas de visualización molecular, estas trayectorias pueden reproducirse como una especie de “película”, mostrando de forma intuitiva cómo la proteína cambia de conformación y se mueve a lo largo del tiempo. 

## 📁 Obtención de datos
Para llevar a cabo las simulaciones, es fundamental disponer de los datos adecuados. Primero, se requiere la estructura tridimensional de la proteína, normalmente obtenida de bases de datos como el [PDB](https://www.rcsb.org/) (Protein Data Bank). Esta estructura sirve como punto de partida para la simulación, debe estar libre de disolvente y cualquier otro átomo no proteico. El disolvente se volverá a añadir más adelante.

> 🟡 **Protein Data Bank:**
 El PDB almacena las coordenadas atómicas de biomoléculas y ofrece una forma estandarizada de presentar la información estructural obtenida a partir de estudios de difracción de rayos X y resonancia magnética nuclear (RMN). Cada una de las estructuras incluidas en esta base de datos se identifica mediante un código único de cuatro letras. Por ejemplo, el archivo PDB que utilizaremos en este tutorial corresponde al código <a href="https://www.rcsb.org/structure/1AKI" target="_blank">1AKI</a>


Durante y después de la simulación, se generan archivos de trayectoria y energía que contienen información detallada sobre el movimiento de los átomos y las interacciones en el sistema. Estos datos permiten analizar cómo se comporta la proteína en su entorno, identificar cambios conformacionales y estudiar propiedades dinámicas como la estabilidad y la flexibilidad de la molécula.

Para cargar la estructura inicial: 
1. **Crea un nuevo historial en Galaxy**
   1. En la esquina superior derecha de Galaxy, haz clic en *Histories*.
   2. Selecciona *Create New History* ✚ .

<img width="1749" height="831" alt="newhistory" src="https://github.com/user-attachments/assets/20d90099-5f5d-43b2-bc3b-4424fe48655e" />

   3. Ponle un nombre descriptivo, por ejemplo: Simulación_1AKI. 
   Esto te ayudará a mantener organizados todos los archivos de esta simulación.

3. **Descargar el archivo PDB**
   1. Debes ir a la página de PDB y buscar el ID: 1AKI.
   2. Desgarga el archivo *Legacy PDB* y súbelo a una historia nueva de Galaxy.
4. **Limpiar el archivo PDB (eliminar átomos que no son de la proteína)**   
   1. Busca la herramienta **Search in textfiles (grep)** en Galaxy.
   2. En el campo *Select lines from*, selecciona el archivo PDB que acabas de descargar (1AKI).
   3. Configura los siguientes parámetros:
      - **that**: `Don´t Match`.
      - **Regular Expressión**: `HETATM`.
    4. Ejecuta la herramienta y obtendrás un nuevo archivo PDB limpio para la simulación.

🔍 Explicación:
Las líneas de un archivo PDB que comienzan con *HETATM* describen átomos que no pertenecen a la cadena principal de la proteína (como iones, ligandos o moléculas de agua). Al usar la opción *Don’t Match* con la expresión regular *HETATM*, el resultado conservará únicamente las líneas correspondientes a los átomos de proteína (ATOM), eliminando las demás.

> 🟡 **Lisozima:**
En este tutorial analizaremos la lisozima de la clara de huevo de gallina, una enzima ampliamente estudiada por su capacidad para degradar los polisacáridos presentes en las paredes celulares de numerosas bacterias.
Se trata de una proteína globular pequeña, compuesta por 129 residuos, caracterizada por su gran estabilidad, cualidades que la convierten en un modelo ideal para nuestros objetivos de estudio.


![1aki](https://github.com/user-attachments/assets/22a83e0b-7915-4fc2-ad00-89db903b41ff)

## 🧩 Configuración
En este paso se utiliza la herramienta **GROMACS initial setup**, que toma como entrada un archivo PDB y genera tres archivos esenciales para la simulación de MD: 

**1. Archivo de topología (.top)**
  
Describe las propiedades químicas y físicas de la molécula, como masas, tipos de átomos, enlaces y cargas. Esta información define cómo interactúan los átomos durante la simulación. Para su generación, es necesario seleccionar un campo de fuerza y un modelo de agua compatibles. En este tutorial usaremos el campo de fuerza *OPLS/AA*, junto con el modelo de agua *SPC/E*.

**2. Archivo de estructura (.gro)**
   
Contiene las coordenadas tridimensionales de los átomos en el formato nativo de GROMACS. Este formato es más eficiente que el PDB y permite centrar la proteína dentro de la caja de simulación.

**3. Archivo de restricciones de posición (.itp)**

Especifica qué átomos permanecerán fijos durante las etapas de equilibración (NVT y NPT). Esto permite que el disolvente se acomode alrededor de la proteína sin distorsionar su estructura inicial.

En resumen, la herramienta crea la topología del sistema, convierte la estructura de PDB a GRO con la estructura centrada en una caja de simulación (celda unitaria) y genera las restricciones necesarias para equilibrar la simulación.

### 📦 Definición de la caja de simulación o celda unitaria
Una vez generados estos archivos, es necesario definir la caja de simulación o celda unitaria, que representa el espacio donde se desarrollará la simulación. Esto se realiza mediante la herramienta **GROMACS structure configuration**, que permite establecer el tamaño y la forma de la caja.

La caja debe ser lo suficientemente grande para evitar que la proteína interactúe con sus propias imágenes bajo condiciones periódicas. Aunque una caja cúbica puede parecer intuitiva, en la práctica un dodecaedro rómbico es más eficiente, ya que ocupa menos volumen y reduce el número de moléculas de agua necesarias, optimizando los recursos de simulación dedicados al disolvente.

### 💻 Práctica
Primero, ejecuta la herramienta **GROMACS initial setup** con los siguientes parámetros:
- **📄 PDB input file**: `1AKI_clean.pdb` (estructura sin moléculas no proteicas)
- **Water model**: `SPC/E`
- **Force field**: `OPLS/AA`
- **Ignore hydrogens**: `No`

- **Generate detailed log**: `Yes`

🚨 Es importante que el archivo PDB de entrada contenga solo la proteína, ya que los campos de fuerza están parametrizados para aminoácidos estándar; incluir ligandos u otras moléculas podría generar errores en la topología.

A continuación, ejecuta la herramienta **GROMACS structure configuration** para definir la caja de simulación. Utiliza como entrada el archivo GRO generado en el paso anterior y selecciona:
 - **Input structure**: `GRO file de la herramienta de configuración inicial`
 - **Output formal**: `GRO file`
 - **Configure box**: `Yes`
 - **Box dimensions (nm**): `1.0`
 - **Box type**: `Rectangular box with all sides equal`

 - **Generate detailed log**: `Yes`

Esta configuración define una caja simétrica y con un espacio adecuado alrededor de la proteína, garantizando que el sistema esté listo para los siguientes pasos de la simulación.

## 💧 Solvatación
Una vez definida la caja de simulación, el siguiente paso es solvatar y neutralizar el sistema utilizando la herramienta *GROMACS solvation and adding ions*. En esta etapa se añaden moléculas de agua al archivo de estructura y se actualiza la topología para llenar completamente la celda unitaria, de modo que la proteína quede inmersa en un entorno acuoso similar al fisiológico.

Además, la herramienta incorpora automáticamente iones de sodio (Na⁺) y cloruro (Cl⁻) para neutralizar la carga total del sistema, garantizando su estabilidad electrostática durante la simulación. Este equilibrio es esencial, ya que un sistema cargado podría generar fuerzas artificiales e inestables que alterarían los resultados de la dinámica molecular.

En el caso de la lisozima, cuya carga neta es +8, se añaden 8 iones de cloruro (Cl⁻) para compensar la carga positiva y mantener el sistema eléctricamente neutro. El archivo resultante incluye la proteína, el disolvente y los iones necesarios para continuar con las siguientes etapas.

### 💻 Práctica
Ejecuta la herramienta **GROMACS solvation and adding ions** con los siguientes parámetros:

 - **📄 GRO structure file**: `archivo GRO generado en el paso anterior`
 - **📄 Topology (TOP) file**: `GRO file produced by the structure configuration tool`
 - **Input structure**: `Topology produced by setup`
 - **Water model for solvatation**: `SPC`
 - **Add ions to neutralise system**: `Yes, add ions`
 - **Specify salt concentration (sodium chloride) to add, in mol/liter**: `0`

 - **Generate detailed log**: `Yes`


El resultado es un archivo **GRO** que contiene la proteína completamente rodeada por moléculas de agua, listo para el siguiente paso.

## ⚡ Minimización de energía
Antes de iniciar la simulación de dinámica molecular, es necesario relajar la estructura del sistema mediante una minimización de energía (EM). Este paso corrige posibles choques entre átomos o geometrías poco realistas que podrían incrementar la energía potencial y generar inestabilidades durante la simulación. El objetivo es obtener una conformación inicial energéticamente estable.

La herramienta GROMACS energy minimization permite dos modos de configuración en la sección “Entrada de parámetros”. En este tutorial se empleará la configuración predeterminada de Galaxy, que define automáticamente los parámetros necesarios a través de la interfaz y resulta adecuada para la mayoría de los casos.

Como alternativa, es posible cargar un archivo MDP (Molecular Dynamics Parameters) personalizado, lo que ofrece un mayor control sobre las condiciones de simulación. Esta opción es útil para usuarios con experiencia en GROMACS, ya que permite ajustar parámetros no disponibles aún en la interfaz de Galaxy. La descripción completa de estos parámetros se encuentra en la documentación oficial de GROMACS.

### 💻 Práctica
Ejecuta la herramienta **GROMACS energy minimization** con los siguientes parámetros:

 - **📄 GRO structure file**: `archivo GRO generado en el paso de solvatación`
 - **📄 Topology (TOP) file**: `Topology`
 - **Parameter input**: `Use default (partially customisable) setting`
 - **Choice of integrator**: `Generate a pair list with buffering` (most common choice for EM)
 - **Electrostatics**: `Fast smooth Particle-Mesh Ewald (SPME) electrostatics`
 - **Cut-off distance for the short-range neighbor list**: `1.0` (but irrelevant as we are using the Verlet scheme)
 - **Short range van der Waals cutoff**: `1.0`
 - **Number of steps for the MD simulation**: `50000`
 - **EM tolerance**: `1000`
 - **Maximum step size**: `0.01`

 - **Generate detailed log**: `Yes`

## ⚖️ Equilibrio del sistema
Tras la minimización de energía, el siguiente paso es equilibrar el sistema para asegurar que el solvente y los iones se distribuyan correctamente alrededor de la proteína y que las condiciones termodinámicas sean estables antes de la simulación de producción.

El equilibrio se realiza en dos etapas sucesivas:

1. **Fase NVT (isotérmico-isocórico)**: Mantiene constantes el número de partículas, el volumen y la temperatura. En esta primera fase, se mantiene fijo el volumen mientras la temperatura del sistema se estabiliza mediante un termostato. La proteína se mantiene casi inmóvil utilizando el archivo de restricciones de posición generado durante la configuración inicial. Esto permite que el solvente se reorganice y se adapte al entorno de la proteína sin alterar su estructura principal.

2. **Fase NPT (isotérmico-isobárico)**: Se mantiene constante el número de partículas, la presión y la temperatura. Una vez alcanzada la temperatura deseada, se procede a equilibrar la presión del sistema utilizando un barostato. En esta segunda fase se eliminan las restricciones de posición, permitiendo que tanto la proteína como el solvente se ajusten completamente a las condiciones de presión y densidad del sistema.

Después de completar ambas etapas, el sistema se encuentra preparado y en equilibrio, listo para iniciar la simulación de dinámica molecular bajo condiciones controladas y estables.

### 💻 Práctica

### Equilibrio NVT 
En esta primera etapa de equilibración, el sistema se mantiene a volumen constante, permitiendo que la temperatura se estabilice alrededor del valor deseado. La proteína permanece restringida para evitar movimientos grandes, mientras el solvente y los iones se adaptan libremente a su entorno.

Ejecuta la herramienta **GROMACS simulation** con los siguientes parámetros:

 - 📄**GRO structure file**: `archivo GRO generado en el paso anterior`
 - 📄**Topology (TOP) file**: `Topology`

- **📄Inputs:**

   - **Use a checkpoint (CPT) file**: `No CPT input`
   - 📄**Position restraint file**: `Position restraint file produced by ‘Setup’ tool`

- **📄Outputs:**

   - **Trajectory output**: `Return no trajectory output`
   - **Structure output**: `Return .gro file`
   - **Produce a checkpoint (CPT) file**: `Produce CPT output`

- **📄Settings:**

   - **Ensemble**: `Isothermal-isochoric ensemble (NVT)`
   - **📄Parameter input:** `Use default (partially customisable) setting`
   - **Choice of integrator**: `A leap-frog algorithm for integrating Newton’s equations of motion`
   - **Bond constraints**: `Bonds with H-atoms`
   - **Neighbor searching**: `Generate a pair list with buffering`
   - **Electrostatics**: `Fast smooth Particle-Mesh Ewald (SPME) electrostatics`
   - **Temperature**: `300`
   - **Step length in ps**: `0.002`
   - **Number of steps that elapse between saving data points (velocities, forces, energies)**: `5000`
   - **Distance for the Coulomb cut-off**: `1.0`
   - **Cut-off distance for the short-range neighbor list**: `1.0`
   - **Short range van der Waals cutoff**: `1.0`
   - **Number of steps for the NVT simulation**: `50000`

- **Generate detailed log**: `Yes`

### Equilibrio NPT
Una vez estabilizada la temperatura del sistema durante la fase NVT, el siguiente paso es equilibrar la presión. Para ello, se realiza una nueva simulación bajo el ensamble NPT (Número de partículas, Presión y Temperatura constantes).

En esta etapa, el volumen del sistema puede variar hasta alcanzar la presión y densidad adecuadas, representando condiciones más realistas del entorno físico. Al mismo tiempo, la proteína y el solvente pueden moverse libremente, permitiendo que el sistema se adapte de forma completa y estable.

💡Nota:
Se puede continuar la simulación justo donde finalizó la fase NVT utilizando el archivo de punto de control (CPT) generado al final de esa ejecución. Este archivo guarda el estado del sistema (velocidades, posiciones y parámetros), lo que permite retomar el proceso sin reiniciar desde cero.

Ejecuta la herramienta **GROMACS simulation** con los siguientes parámetros:

 - 📄**GRO structure file**: `archivo GRO generado en el paso anterior`
 - 📄**Topology (TOP) file**: `Topology`

- **📄Inputs:**

   - **Use a checkpoint (CPT) file**: `Continue simulation from a CPT file` Archivo generado por la equilibración NVT
   - 📄**Position restraint file**: `No position restraints`

- **📄Outputs:**

   - **Trajectory output**: `Return no trajectory output`
   - **Structure output**: `Return .gro file`
   - **Produce a checkpoint (CPT) file**: `Produce CPT output`

- **📄Settings:**

   - **Ensemble**: `Isothermal-isobaric ensemble (NPT)`
   - **📄Parameter input:** `Use default (partially customisable) setting`
   - **Choice of integrator**: `A leap-frog algorithm for integrating Newton’s equations of motion`
   - **Bond constraints**: `Bonds with H-atoms`
   - **Neighbor searching**: `Generate a pair list with buffering`
   - **Electrostatics**: `Fast smooth Particle-Mesh Ewald (SPME) electrostatics`
   - **Temperature**: `300`
   - **Step length in ps**: `0.002`
   - **Number of steps that elapse between saving data points (velocities, forces, energies)**: `5000`
   - **Distance for the Coulomb cut-off**: `1.0`
   - **Cut-off distance for the short-range neighbor list**: `1.0`
   - **Short range van der Waals cutoff**: `1.0`
   - **Number of steps for the NVT simulation**: `50000`

- **Generate detailed log**: `Yes`

> 🟡 **¿Por qué es importante mantener la proteína restringida durante el equilibrio NVT?**
Durante el equilibrio NVT, es importante mantener la proteína restringida para evitar movimientos o deformaciones bruscas mientras el solvente y los iones se ajustan a su entorno. En esta fase, el sistema aún no está completamente equilibrado, por lo que liberar la proteína podría provocar desplazamientos no naturales debido a fuerzas iniciales desequilibradas.
Al aplicar restricciones de posición, la estructura principal de la proteína se mantiene estable mientras el agua y los iones alcanzan una distribución más realista y uniforme alrededor de ella.

## ▶️ Simulación de producción
La simulación de producción es la etapa principal de la dinámica molecular: una vez que el sistema está minimizado y equilibrado (NVT → NPT), se ejecuta una simulación prolongada para muestrear el comportamiento dinámico de la proteína bajo condiciones termodinámicas estables. El objetivo es obtener trayectorias físicas (posiciones y velocidades a lo largo del tiempo) que permitan analizar propiedades como fluctuaciones conformacionales, interacciones ligando-proteína, estabilidad estructural, y otras magnitudes termodinámicas y cinéticas.

### 💻 Práctica
Ejecuta la herramienta **GROMACS simulation** con los siguientes parámetros:
 - 📄**GRO structure file**: `archivo GRO generado en el paso anterior`
 - 📄**Topology (TOP) file**: `Topology`

- **📄Inputs:**

   - **Use a checkpoint (CPT) file**: `Checkpoint file produced by NPT equilibration`
   - **Apply position restraints**: `No position restraints`

- **📄Outputs:**

   - **Trajectory output**: `Return .xtc file (reduced precision)`
   - **Structure output**: `Return .gro file`
   - **Produce a checkpoint (CPT) file**: `Produce CPT output`
   - **Produce an energy (EDR) file**: `No EDR output`
   - **Produce XVG output**: `Produce XVG output`
   - **Produce TPR output**: `Produce TPR output`   

- **📄Settings**

   - **Ensemble**: `Isothermal-isobaric ensemble (NPT)`

- **📄Parameter input**: `Use default (partially customisable) setting`

  - **Choice of integrator**: `A leap-frog algorithm for integrating Newton’s equations of motion`
  - **Bond constraints**: `Bonds with H-atoms` 
  - **Neighbor searching**: `Generate a pair list with buffering`
  - **Electrostatics**: `Fast smooth Particle-Mesh Ewald (SPME) electrostatics`
  - **Temperature**: `300`
  - **Step length in ps**: `0.002`
  - **Number of steps that elapse between saving data points (velocities, forces, energies)**: `5000`
  - **Distance for the Coulomb cut-off**: `1.0`
  - **Cut-off distance for the short-range neighbor list**: `1.0` 
  - **Short range van der Waals cutoff**: `1.0`
  - **Number of steps for the NVT simulation**: `500000`

- **Generate detailed log**: `Yes`

---

### Visualización de la simulación en PyMOL
Una vez completada la simulación y obtenidos los archivos de salida, podemos visualizar el movimiento atómico de la proteína utilizando [PyMOL](https://www.pymol.org/), un software de visualización molecular ampliamente usado en bioinformática.

**Archivos necesarios**
- Archivo de estructura: `.gro`
- Archivo de trayectoria: `.xtc`

Estos dos archivos permiten reproducir la dinámica del sistema.


---

### 🎉 ¡Lo lograste!
Has completado con éxito una simulación de dinámica molecular completa con GROMACS en Galaxy, desde la preparación del sistema hasta la generación de la trayectoria final (archivo `.xtc`).

A partir de aquí, puedes continuar con el análisis y la visualización de la trayectoria para explorar el movimiento de la proteína, su estabilidad estructural y las interacciones con el solvente. Te recomendamos seguir los tutoriales complementarios de Galaxy dedicados a visualización y análisis de trayectorias de GROMACS para profundizar en estos aspectos.

## 🧭 Conclusión
En este tutorial hemos recorrido paso a paso el flujo de trabajo completo para realizar una simulación de dinámica molecular con GROMACS en Galaxy.

Comenzamos con la preparación de la proteína, eliminando átomos no proteicos y generando los archivos básicos del sistema (topología, estructura y restricciones). Posteriormente, realizamos la solvatación e introducción de iones para neutralizar la carga total. Posteriormente, se ejecutaron las etapas de minimización de energía y equilibración (NVT y NPT) para obtener un sistema estable. Finalmente, se llevó a cabo la simulación de producción, donde el sistema evoluciona en el tiempo bajo condiciones controladas.

Este flujo permite observar el comportamiento dinámico de la proteína en un entorno simulado y constituye la base para análisis posteriores.

En resumen, Galaxy ofrece una forma accesible, reproducible y transparente de realizar simulaciones de dinámica molecular sin necesidad de ejecutar comandos directamente, facilitando el aprendizaje y la exploración de biomoléculas complejas mediante GROMACS.

## Referencias
Este tutorial se basa en el material original del proyecto [Galaxy Training!](https://training.galaxyproject.org/training-material/topics/computational-chemistry/tutorials/md-simulation-gromacs/tutorial.html), adaptado para ofrecer una guía clara paso a paso. 

Además, el tutorial de Galaxy se basa en el tutorial de GROMACS proporcionado por Justin Lemkul [(aquí)](http://www.mdtutorials.com/gmx/lysozyme/index.html) consúltelo si está interesado en una guía técnica más detallada de GROMACS.
