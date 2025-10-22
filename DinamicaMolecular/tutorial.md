# 🧬 Aprende Dinámica Molecular con Gromacs
## Introducción
La **dinámica molecular (MD)** es una técnica computacional que permite simular el movimiento de átomos y moléculas a lo largo del tiempo, resolviendo las **leyes de Newton** para cada partícula del sistema.  

Piensa en cada átomo como una pequeña bolita unida a otras por “resortes invisibles”. La dinámica molecular nos permite calcular cómo estas bolitas se mueven, chocan y se reorganizan, **simulando lo que ocurre en la naturaleza**, pero en una computadora.

## 🖥️ ¿Qué es GROMACS?
**GROMACS** (acrónimo original de GROningen MAchine for Chemical Simulations) es un paquete de software de DM, de alto rendimiento, libre y de código abierto, diseñado principalmente para realizar simulaciones de sistemas biomoleculares como proteínas, lípidos y ácidos nucleicos. Realiza **cálculos complejos automáticamente**, como fuerzas entre átomos y movimientos en el tiempo. Es rápido, confiable y puede integrarse con **Galaxy**, un entorno gráfico que facilita el uso para principiantes.  

## 🎯 Qué aprenderás en este tutorial
Realizaremos una **simulación de la lisozima de huevo de gallina (PDB ID: 1AKI)**.  
Aunque no tengas experiencia previa con GROMACS ni con simulación molecular, aprenderás paso a paso:

El flujo general de la simulación incluye:

1. Preparación de la proteína (limpieza, generación de topología).  
2. Definición de la caja y solvatar el sistema con moléculas de agua.  
3. Adición de iones para neutralizar el sistema.  
4. Minimización de energía para relajar el sistema.  
5. Equilibración (NVT y NPT) y simulación de producción.  
6. Análisis de resultados (energía potencial, RMSD, temperatura, presión).


💡 **Consejo:**  
Antes de iniciar, asegúrate de tener tu cuenta activa en [Galaxy Europe](https://usegalaxy.eu/) y haber descargado los archivos de entrada necesarios.

## Requisitos previos






Esta versión está basada en el tutorial original del [Galaxy Training Network](https://training.galaxyproject.org).