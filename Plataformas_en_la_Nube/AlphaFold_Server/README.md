# Modelado de Complejos Proteicos con AlphaFold Server

AlphaFold Server es la plataforma gratuita de Google DeepMind que permite utilizar AlphaFold 3, la versión más avanzada de su modelo de Inteligencia Artificial. A diferencia de las versiones anteriores que se centraban casi exclusivamente en proteínas, este servidor permite modelar una amplia gama de biomoléculas con una gran precisión. 

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
    - **Molecula 1:** Selecciona ``Protein``. Pega la secuencia FASTA de **EZH2**.
<img width="700" height="300" alt="Captura de pantalla 2026-01-28 a la(s) 1 22 55 a m" src="https://github.com/user-attachments/assets/b2c177c4-67a3-4321-867b-462c676a4f6b" />

   - Haz clic en **``Add entity``**.
   - **Molecula 2:** Selecciona ``Protein``. Pega la secuencia FASTA de **EED**.
   -  > Y asi con las 2 moleculas restantes...
      
4. Haz clic en **``Continue and preview job``**

   4.1. Asigna un nombre al trabajo, por ejemplo: `PRC2_Core_Complex`.

      4.2. Luego selecciona **``Confirm and submit job``**.

*El proceso puede tardar desde unos minutos hasta una hora, dependiendo de la carga del servidor en ese momento.*

---

## 📊 Paso 3: Análisis de Resultados (Métricas de Confianza)

Una vez que AlphaFold termina, nos dara dos métricas para esto, incluida la estructura 3D:

### 1. pLDDT (Confianza Local)
Mide la confianza en la estructura de cada residuo individual (0 a 100).
* **Azul oscuro (>90):** Muy alta confianza. Equivalente a un experimento cristalográfico.
* **Azul claro (70-90):** Confianza alta. El "backbone" es fiable.
* **Amarillo/Naranja (<50):** Muy baja confianza. A menudo indica regiones **intrínsecamente desordenadas**. 

### 2. PAE (Predicted Aligned Error - Confianza Global)
Esta es la métrica más importante para complejos. Es una matriz 2D que nos dice: *"Si alineo la proteína A, ¿qué tanto error espero en la posición de la proteína B?"*.
* **Interpretación:** Buscamos cuadros **verdes oscuros** en la intersección entre dos proteínas diferentes.
<img width="1230" height="756" alt="Captura de pantalla 2026-01-28 a la(s) 10 59 58 a m" src="https://github.com/user-attachments/assets/bda75aa0-f6af-462c-874e-9f7d07b350ec" />

---
