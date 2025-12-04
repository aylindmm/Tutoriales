# 🧪 Docking Molecular y Virtual Screening en Terminal
## Un estudio comparativo: Capsaicina vs NorA

Bienvenido a este repositorio de tutoriales avanzados de bioinformática estructural. Aquí encontrarás una guía paso a paso para realizar experimentos de acoplamiento molecular (*Molecular Docking*) y cribado virtual (*Virtual Screening*) utilizando herramientas de línea de comandos en un servidor Linux.

El objetivo central de este proyecto es evaluar el potencial de la **Capsaicina** y otros compuestos similares como inhibidores de la bomba de expulsión **NorA** de *Staphylococcus aureus*, un mecanismo clave en la resistencia a antibióticos.

---

## 🎯 Objetivo del Proyecto
Evaluar la capacidad de unión de la **Capsaicina** al sitio activo de la bomba NorA y realizar un cribado virtual (Virtual Screening) sobre la base de datos DrugBank para identificar fármacos con mayor afinidad teórica, utilizando tres enfoques computacionales distintos:
1.  **AutoDock Vina:** El estándar de oro en docking académico (basado en campos de fuerza).
2.  **PLANTS:** Un algoritmo basado en inteligencia de enjambre (optimización por colonia de hormigas).
3.  **DiffDock:** La vanguardia en Inteligencia Artificial (Deep Learning generativo).

---

## 📚 Contenido de los Tutoriales

Este repositorio está dividido en tres módulos independientes pero complementarios. Te recomendamos seguirlos en orden para comprender la progresión desde el docking básico hasta la validación con IA.

### 1. [Tutorial: AutoDock Vina](Docking_AutodockVina/README.md)
* **Enfoque:** Docking clásico y Virtual Screening masivo.
* **Aprenderás:** Preparación de archivos PDBQT, definición de cajas de búsqueda, scripting en Bash para automatizar el docking de cientos de ligandos y análisis de energías de afinidad (kcal/mol).

### 2. [Tutorial: PLANTS](Docking_PLANTS/README.md)
* **Enfoque:** Algoritmos estocásticos y consenso.
* **Aprenderás:** Manejo de archivos MOL2, configuración de archivos `.conf` para screening nativo y uso de la función de puntuación ChemPLP.

### 3. [Tutorial: DiffDock (IA)](Docking_DiffDock/README.md)
* **Enfoque:** Validación mediante Deep Learning.
* **Aprenderás:** Uso de modelos de difusión generativa para docking ciego (*blind docking*), interpretación de Scores de Confianza y validación cruzada de resultados.

### 4. [Análisis de Consenso (ECR)](Consenso Exponencial (ECR)/README.md)
* **Enfoque:** Estadística avanzada.
* **Aprenderás:** Cómo integrar los resultados de las tres herramientas anteriores para calcular un "Ranking de Consenso Exponencial" y encontrar el candidato más robusto.

---

## 🛠️ Comparativa de Herramientas

En este proyecto utilizamos tres motores de docking fundamentalmente diferentes. Aquí te explicamos sus características clave para que entiendas cuándo usar cada uno.

| Característica | **AutoDock Vina** | **PLANTS** | **DiffDock** |
| :--- | :--- | :--- | :--- |
| **Algoritmo** | Búsqueda Local Iterada (Gradiente + Estocástico) | Optimización por Colonia de Hormigas (ACO) | Modelo de Difusión Generativa (Deep Learning) |
| **Función de Puntuación** | Empírica / Campos de Fuerza (Vina Score) | Empírica (ChemPLP) | Modelo de Confianza (Red Neuronal) |
| **Métrica de Salida** | Afinidad ($\Delta G$) en `kcal/mol` | Score adimensional (ChemPLP) | Score de Confianza (Log-likelihood) |
| **Velocidad** | Muy Rápido (Segundos/ligando) ⚡ | Rápido (Segundos/ligando) ⚡ | Lento (Minutos/ligando - Requiere GPU) 🐢 |
| **Tipo de Docking** | Sitio dirigido (Caja definida) | Sitio dirigido (Radio definido) | Docking Ciego (Busca en toda la proteína) |
| **Input de Ligando** | Archivos `.pdbqt` individuales | Archivo `.mol2` (multi-molécula) | Archivos `.sdf` o `.pdb` originales |
| **Mejor uso** | Screening masivo de librerías grandes. | Screening flexible y optimización de leads. | Validación de candidatos y sitios desconocidos. |

---

## 🧬 Contexto Biológico: El Problema de NorA

La resistencia a los antibióticos es una crisis de salud global. *Staphylococcus aureus* resistente a meticilina (MRSA) utiliza la bomba de eflujo **NorA** para expulsar antibióticos (como ciprofloxacina) fuera de la célula bacteriana, haciéndolos ineficaces.

* **La Hipótesis:** Si encontramos una molécula que se una fuertemente al canal de NorA y lo bloquee, podríamos restaurar la sensibilidad de la bacteria a los antibióticos.
* **El Candidato:** La **Capsaicina** (el compuesto picante del chile) ha mostrado actividad inhibitoria en estudios previos.
* **El Reto:** ¿Podemos encontrar en DrugBank un fármaco ya aprobado que sea estructuralmente similar a la capsaicina pero que se una aún mejor a NorA?

---

## 💻 Requisitos Previos

Para realizar estos tutoriales, necesitarás acceso a un entorno tipo Unix (Linux o macOS) y tener instalados los siguientes gestores de paquetes:

* **Conda (Miniconda o Anaconda):** Para gestionar entornos virtuales y dependencias de Python/RDKit.
* **Herramientas de sistema:** `wget`, `nano`, `awk`, `grep`, `scp`.

¡Empecemos a dockear! Dirígete al [Primer Tutorial](https://github.com/aylindmm/Tutoriales/tree/main/Docking/DockingTerminal/Docking_AutodockVina) para comenzar.
