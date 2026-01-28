# Modelado de Complejos con ColabFold
## El Complejo PRC2 (EZH2:EED)

### Introducción
En este tutorial utilizaremos **ColabFold**, una versión optimizada y accesible de AlphaFold2. A diferencia de la versión original de DeepMind que requiere terabytes de bases de datos locales, ColabFold corre en la nube de Google Colab y utiliza **MMseqs2** para realizar alineamientos de secuencia a gran velocidad.

Es una buena herramienta cuando necesitas modelar interacciones proteína-proteína de forma rápida y gratuita.

---

## Procedimiento

### Paso 1: Preparar las Secuencias
Necesitamos las secuencias en formato FASTA lineal para ColabFold.
Ve a [UniProt](https://www.uniprot.org/) y obtén las secuencias de: 
- EZH2 (Q15910)
- EED (O75530)

#### Ejemplo de como obtener las secuencias en Uniprot:
1. Verifica que el organismos sea "Homo sapiens (Human)"
2. Ve a ``Dowload``
<img width="1142" height="344" alt="Captura de pantalla 2026-01-27 a la(s) 10 29 20 p m" src="https://github.com/user-attachments/assets/268677e7-02da-4250-a9ae-27b0f0bebbe0" />

3. En ``Format`` selecciona "FASTA (canonical)"
4. Puedes seleccionar la opción ``Preview`` o ``Download`` para poder visualizar la secuencia.
5. Para ColabFold copía la secuencia a partir de donde esta la flecha roja en la imagen.
<img width="869" height="600" alt="Captura de pantalla 2026-01-27 a la(s) 10 44 45 p m" src="https://github.com/user-attachments/assets/db468119-cf11-4423-82ec-fd9c1088864b" />

Para indicar que es un **complejo**, unimos las secuencias usando dos puntos (`:`) como separador.
### Paso 2: Ejecutar en Google Colab 
1. Abre el notebook de [ColabFold](https://colab.research.google.com/github/sokrypton/ColabFold/blob/main/AlphaFold2.ipynb)
2. Inicia sesión con tu cuenta de Google. ⚠️ No uses tu cuenta institucional. Solo cuentas de Google personales.

### Paso 3: Configuración 
- ``query_sequence``: pega tus 2 secuencias y une ambas usando dos puntos (`:`) como separador.
<img width="1285" height="73" alt="Captura de pantalla 2026-01-27 a la(s) 11 52 44 p m" src="https://github.com/user-attachments/assets/90634fc2-5309-476c-9244-d0b7bd15242d" />
- ``jobname``: PRC2
- ``num_relax``: 0
- `template_mode: ``none``
- ``num_recycles``: Dejarlo en 3 es lo ideal pero puedes bajarlo a 1 si tienes prisa (se predera precisión)

### Paso 4: Ejecutar
- Ve al menú superior y selecciona ``Ejecutar todo``
- *Acepta la advertencia de que el notebook no es de Google*

### Paso 5: Esperar jaja
- Veras como se instalan las dependencias.
- Luego, MMseqs2 buscará secuencias evolutivas.
- Finalmente, AlphaFold predecirá 5 modelos.

### Resultados
Al finalizar la ejecución, ColabFold te entregará varios resultados. 

#### 1. Los Gráficos de Diagnóstico.
- **MSA Coverage**: muestra cuántas secuencias evolutivas homólogas encontro el algoritmo MMseqs2.
<img width="681" height="470" alt="image" src="https://github.com/user-attachments/assets/223b7024-f45f-48ce-a304-7bf5187c7338" />
---
- **Predicciones**: se generaran 5 predicciones alternativos de la misma estructura, ordenadas automáticamente del mejor al peor según su puntuación de confianza (pLDDT y PAE). 
<img width="840" height="413" alt="image" src="https://github.com/user-attachments/assets/df7ca86c-433e-40fe-beaa-bd32b21fa834" /> **Rank 1**
---
- **Estructura 3D del complejo**: también se generan las estructuras 3D para cada uno de las predicciones, en donde veras cada cadena según el nivel de confianza que le asigno el algoritmo.  
<img width="939" height="446" alt="Captura de pantalla 2026-01-28 a la(s) 12 46 15 a m" src="https://github.com/user-attachments/assets/6fdd088c-3858-4302-afc6-1c357c034d49" /> **Rank 1**
---
- **Gráficos PAE**: se genera gráfico de PAE que es una validación matemática para cada una de las predicciones.
<img width="298" height="288" alt="Captura de pantalla 2026-01-28 a la(s) 12 53 51 a m" src="https://github.com/user-attachments/assets/7034ef22-b192-4b5c-b991-93824353e1f8" /> **Rank 1**
--- 
- **pLDDT**: es una métrica de confianza sobre las estructuras predichas para cada residuo (aminoácido) de la proteína. 
<img width="1391" height="939" alt="image" src="https://github.com/user-attachments/assets/619b3434-082d-477d-80e0-8faea3d8b772" />



