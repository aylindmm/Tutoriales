# Modelado de Complejos Proteicos con AlphaFold Server
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


En este tutorial, modelaremos la interacción de este núcleo central.

---

## ⚠️ Concepto Clave: AlphaFold Database vs. AlphaFold Server

Es común confundir estas dos herramientas, pero sirven para propósitos muy diferentes.

| Característica | **AlphaFold Database (DB)** | **AlphaFold Server** |
| :--- | :--- | :--- |
| **¿Qué es?** | Un repositorio estático de estructuras *pre-calculadas*. | Una herramienta en la nube para generar *nuevas* predicciones. |
| **Contenido** | Contiene el proteoma humano y de otros organismos. | Lo que tú le pidas modelar. |
| **Complejos** | Principalmente monómeros (cadenas individuales). | Especializado en **multímeros** (complejos proteína-proteína), ADN, ARN y ligandos. |
| **Versión** | Generalmente basada en AlphaFold 2. | Utiliza **AlphaFold 3** (la versión más avanzada). |
| **Uso ideal** | Buscar rápido la estructura de una sola proteína conocida. | Modelar interacciones, proteínas mutadas o complejos novedosos. |

**En resumen:** Si buscas una proteína sola, ve a la Database. Si quieres ver cómo interactúan varias proteínas (como haremos hoy con PRC2), usa el AlphaFold Server.

---

## Procedimiento

### Paso 1: Obtención de Secuencias (UniProt)
Primero necesitamos la secuencia de aminoácidos de cada componente humano de PRC2.

1. Ve a [UniProt](https://www.uniprot.org/).
2. Busca y copia la secuencia **FASTA** de las siguientes proteínas humanas (Verifica que el *Organism* sea *Homo sapiens*):
    * **EZH2:**
    * **EED:**
    * **SUZ12:**
    * **RbAp48:**

>  En UniProt, selecciona el botón ``Download``, luego en ``Format`` selecciona "FASTA". Copia todo el texto, incluyendo la primera línea que empieza con `>`.
<img width="1142" height="344" alt="Captura de pantalla 2026-01-27 a la(s) 10 29 20 p m" src="https://github.com/user-attachments/assets/90166054-fba5-4451-a6ba-dae3ce9c9d09" />
<img width="859" height="605" alt="Captura de pantalla 2026-01-28 a la(s) 1 13 27 a m" src="https://github.com/user-attachments/assets/548a6ff8-51b0-4508-8854-4b3c67f1b620" />

---

### Paso 2: Configuración en AlphaFold Server
AlphaFold 3 permite predecir complejos macromoleculares con alta precisión.

1. Accede a [AlphaFold Server](https://alphafoldserver.com/) e inicia sesión con tu cuenta de Google.
2. Haz clic en ``Create new job``.
3. En la sección de entrada ("Input"), verás opciones para añadir entidades. Vamos a añadir nuestras proteínas:
    * **Molecula 1:** Selecciona ``Protein``. Pega la secuencia FASTA de **EZH2**.

    * Haz clic en **"Add entity"**.
    * **Molecula 2:** Selecciona ``Protein``. Pega la secuencia FASTA de **EED**.
    * > Y asi con las 2 moleculas restantes...
4. Asigna un nombre al trabajo, por ejemplo: `PRC2_Core_Complex`.
5. Haz clic en **"Continue"** y luego en **"Confirm and submit"**.

*El proceso puede tardar desde unos minutos hasta una hora, dependiendo de la carga del servidor en ese momento.*

---

## 📊 Paso 3: Análisis de Resultados (Métricas de Confianza)

Una vez que AlphaFold termina, no basta con ver la estructura; debemos saber si la predicción es científicamente válida. AlphaFold nos da dos métricas para esto:

### 1. pLDDT (Confianza Local)
Mide la confianza en la estructura de cada residuo individual (0 a 100).
* **Azul oscuro (>90):** Muy alta confianza. Equivalente a un experimento cristalográfico.
* **Azul claro (70-90):** Confianza alta. El "backbone" es fiable.
* **Amarillo/Naranja (<50):** Muy baja confianza. A menudo indica regiones **intrínsecamente desordenadas**. *Nota: PRC2 tiene varias regiones desordenadas funcionales, así que es normal ver naranja en los extremos.*

### 2. PAE (Predicted Aligned Error - Confianza Global)
Esta es la métrica más importante para complejos. Es una matriz 2D que nos dice: *"Si alineo la proteína A, ¿qué tanto error espero en la posición de la proteína B?"*.
* **Interpretación:** Buscamos cuadros **azules oscuros** en la intersección entre dos proteínas diferentes.
* **En PRC2:** Deberías ver regiones azules fuertes entre EZH2 y EED, confirmando que AlphaFold predice una interacción rígida y específica entre ellas. Si la matriz es verde/amarilla entre las proteínas, AlphaFold no está seguro de que interactúen.

---

## Paso 4 (Ópcional): Visualización
AlphaFold Server tiene un visor integrado, pero para imágenes más detalladas podemos descargar los resultados.

1. Haz clic en **"Download"** (descargarás un archivo `.zip`).
2. Descomprime el archivo y busca el modelo con el mejor ranking (generalmente `seed_000` o `rank_0`).
3. Abre el archivo `.cif` (o `.pdb`) en **PyMOL**.

**Visualización:**
Intenta colorear cada cadena de un color distinto en PyMOL para identificar cómo EED "abraza" a EZH2, regulando su actividad metiltransferasa.

```python
# Comandos útiles en PyMOL
color blue, chain A  # EZH2
color red, chain B   # EED
color green, chain C # SUZ12
show surface         # Para ver el complejo como un volumen
```
*Referencias* 

- *Margueron, R., & Reinberg, D. (2011). The Polycomb complex PRC2 and its mark in life. Nature, 469(7330), 343-349.*

- *Jumper, J., et al. (2021). Highly accurate protein structure prediction with AlphaFold. Nature.*
