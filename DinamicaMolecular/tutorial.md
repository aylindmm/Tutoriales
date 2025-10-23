# 🧬 Aprende Dinámica Molecular con Gromacs
## Introducción
La **dinámica molecular (MD)** es una técnica computacional que permite simular el movimiento de átomos y moléculas a lo largo del tiempo, resolviendo las **leyes de Newton** para cada partícula del sistema.  

Piensa en cada átomo como una pequeña bolita unida a otras por “resortes invisibles”. La dinámica molecular nos permite calcular cómo estas bolitas se mueven, chocan y se reorganizan, simulando lo que ocurre en la naturaleza, pero en una computadora.

## 🖥️ ¿Qué es GROMACS?
**GROMACS** (acrónimo original de GROningen MAchine for Chemical Simulations) es un paquete de software de DM, de alto rendimiento, libre y de código abierto, diseñado principalmente para realizar simulaciones de sistemas biomoleculares como proteínas, lípidos y ácidos nucleicos. Realiza **cálculos complejos automáticamente**, como fuerzas entre átomos y movimientos en el tiempo. Es rápido, confiable y puede integrarse con **Galaxy**, un entorno gráfico que facilita el uso para principiantes.  

## 🎯 Qué aprenderás en este tutorial
1. Familiarizarse con el funcionamiento de GROMACS disponible en la plataforma Galaxy. 
2. Comprender el flujo de trabajo empleado en una simulación de dinámica molecular.
3. Realizar una simulación corta (1 ns) de una proteína.

Aunque no tengas experiencia previa con GROMACS ni con simulación molecular, aprenderás paso a paso.

El flujo general de la simulación incluye:

![Flujo general de simulación][workflow.png]


💡**Antes de iniciar:**  
- Asegúrate de tener tu cuenta activa en [Galaxy](https://usegalaxy.org/)
- Haber realizado previamente este curso [Introducción a Galaxy](https://training.galaxyproject.org/training-material/topics/introduction/)

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

## Pasos previos
Antes de iniciar una simulación de dinámica molecular, es necesario preparar el sistema siguiendo una serie de pasos. Este procedimiento se puede organizar en varias fases:
1. **Preparación del sistema**: se cargan los datos de la proteína y se rodea con moléculas de agua, agregando también iones para neutralizar la carga del sistema.
2. **Minimización de energía**: se ajustan las posiciones atómicas para eliminar tensiones o solapamientos no físicos en la estructura inicial.
3. **Equilibración del solvente**: se estabiliza el sistema respecto a la temperatura y la presión, realizando primero una fase a volumen constante (NVT) y luego otra a presión constante (NPT).
4. **Simulación de producción**: en esta etapa se generan los datos de la trayectoria, que es un archivo que registra las posiciones de todos los átomos a lo largo del tiempo, mostrando cómo se mueve la molécula. 

Con programas de visualización molecular, estas trayectorias pueden reproducirse como una especie de “película”, permitiendo observar el comportamiento dinámico de la proteína. Más adelante, se explicará cada una de estas fases con mayor detalle.


## Obtención de datos
Para llevar a cabo las simulaciones, es fundamental disponer de los datos adecuados. Primero, se requiere la estructura tridimensional de la proteína, normalmente obtenida de bases de datos como el PDB (Protein Data Bank). Esta estructura sirve como punto de partida para la simulación.

Durante y después de la simulación, se generan archivos de trayectoria y energía que contienen información detallada sobre el movimiento de los átomos y las interacciones en el sistema. Estos datos permiten analizar cómo se comporta la proteína en su entorno, identificar cambios conformacionales y estudiar propiedades dinámicas como la estabilidad y la flexibilidad de la molécula.

1. **Crea un nuevo historial en Galaxy**
   1. En la esquina superior derecha de Galaxy, haz clic en "Histories".
   2. Selecciona "Create New History".
   3. Ponle un nombre descriptivo, por ejemplo: Simulación_1AKI.
   4. Esto te ayudará a mantener organizados todos los archivos de esta simulación.
2. **Descargar el archivo PDB**
   1. Busca la herramienta "Get PDB" en Galaxy.
   2. Ingresa el código de acceso PDB: 1AKI.
   3. Ejecuta la herramienta y se decargará el archivo PDB de la proteína a tu historial.
3. **Limpiar el archivo PDB (eliminar átomos que no son de la proteína)**   
   1. Busca la herramienta "Search in textfiles (grep)" en Galaxy.
   2. Selecciona el archivo PDB que acabas de descargar como "Selec lines from".
   3. Configura los siguientes parámetros:
      -"that": Don´t Match.
      -"Regular Expressión": HETATM.
   4. Esto eliminará todas las líneas que correspondan a átomos no proteicos (como ligandos o iones) dejando solo los átomos de la proteína.
   5. Ejecuta la herramienta y obtendrás un nuveo archivo PDB limpio para la simulación

 
     

## Configuración

## Solvatación

## Minimización de energía

## Equilibrio

## Simulación de producción

## Análisis de resultados

## Conclusión



Esta versión está basada en el tutorial original de [Galaxy Training!](https://training.galaxyproject.org/training-material/topics/computational-chemistry/tutorials/md-simulation-gromacs/tutorial.html).

