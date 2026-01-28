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


