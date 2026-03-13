# Modelado de Complejos Proteicos con Herramientas basadas en la nube ☁️

Comunmente, el análisis bioinformático de alta complejidad (como lo es la predicción de estructuras proteicas o el ensamblaje de genomas) requería de servidores locales costosos y conocimientos avanzados en Linux.

Hoy en día, las herramientas basadas en la nube han democratizado el acceso a la ciencia de datos biológicos. Estas plataformas permiten ejecutar algoritmos pesados en servidores remotos (principalmente de Google), eliminando la necesidad de hardware potente en propio. Sus principales ventajas son:

- Accesibilidad: Solo necesitas un navegador y una conexión a internet.

- Poder de Cómputo: Uso gratuito (o bajo demanda) de GPUs y TPUs de alto rendimiento.

- Reproducibilidad: Los entornos suelen estar preconfigurados, evitando errores de instalación de dependencias.

Para modelar el complejo de PRC2, utilizaremos dos de las implementaciones más potentes del algoritmo de DeepMind. Es importante señalar que, aunque ambas comparten el motor de AlphaFold, su enfoque es distinto.

#### 1. AlphaFold Server (AlphaFold 3)
Es la plataforma oficial y más reciente de Google DeepMind. Su principal fuerte es la simplicidad y la precisión multimer.

 * Enfoque: Ideal para usuarios que buscan resultados rápidos y precisos de complejos proteína-proteína, ácidos nucleicos o ligandos sin tocar una sola línea de código.

#### 2. ColabFold
Es una versión optimizada por la comunidad científica que corre sobre Google Colab.

  * Enfoque: Su gran ventaja es la velocidad. Utiliza MMseqs2 para generar alineamientos múltiples de secuencias (MSAs) en segundos, en lugar de horas. Además, permite un mayor control sobre los parámetros de plegamiento (como el número de reciclos).

Principales diferencias:

| Característica | **AlphaFold Database** | **AlphaFold Server** | **ColabFold** (Este tutorial) |
| :--- | :--- | :--- | :--- |
| **Motor** | AlphaFold 2 (Pre-calculado) | AlphaFold 3 | AlphaFold 2 + MMseqs2 |
| **Velocidad** | Instantánea (si existe) | Lenta (Cola de espera) | Muy Rápida (GPU Gratuita) |
| **Complejos** | No (Solo monómeros) | Sí | Sí (Multimer) |
| **Personalización**| Nula | Media | Alta (Control total) |
| **Uso Ideal** | Consultas rápidas de proteínas individuales. | Modelar complejos con ADN/ARN/Ligandos. | Docking Proteína-Proteína rápido. |

---


## Caso de Estudio: El Complejo Represivo Polycomb 2 (PRC2)

### Introducción
La predicción de estructuras de proteínas ha experimentado una revolución gracias a la Inteligencia Artificial. En este tutorial, aprenderemos a utilizar **AlphaFold Server** para modelar un complejo multiproteico esencial para la regulación epigenética: **PRC2**.

A diferencia de los tutoriales anteriores donde usamos la terminal, aquí utilizaremos herramientas basadas en la nube, lo que facilita el acceso a modelos de alta precisión sin necesitar una supercomputadora propia.

---

## Contexto Biológico: ¿Qué es PRC2?
El **Polycomb Repressive Complex 2 (PRC2)** es una maquinaria molecular crucial para el desarrollo embrionario y la identidad celular. Su función principal es catalizar la metilación de la histona H3 en la lisina 27 (**H3K27me3**), una marca asociada con el silenciamiento génico (represión de la transcripción).

Aunque PRC2 puede tener varios componentes accesorios (como JARID2 o AEBP2), existe un **núcleo catalítico conservado** desde *Drosophila* hasta los mamíferos, compuesto por cuatro subunidades:

1.  **EZH2 (Enhancer of Zeste Homolog 2):** La subunidad catalítica que contiene el dominio SET.
2.  **EED (Embryonic Ectoderm Development):** Se une a la marca H3K27me3 y estimula la actividad de EZH2.
3.  **SUZ12 (Suppressor of Zeste 12):** Esencial para la integridad del complejo.
4.  **RbAp48 (RBBP4):** Una chaperona de histonas.

<img width="481" height="302" alt="Foto1" src="https://github.com/user-attachments/assets/d66d4598-e48a-4110-a317-ffbb3f9c71aa" />


En estos tutoriales, modelaremos la interacción de este núcleo central.

*Referencias* 

- *Margueron, R., & Reinberg, D. (2011). The Polycomb complex PRC2 and its mark in life. Nature, 469(7330), 343-349.*

- *Jumper, J., et al. (2021). Highly accurate protein structure prediction with AlphaFold. Nature.*

