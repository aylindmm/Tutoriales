# 🧫 Control de calidad de archivos FASTQ en Galaxy

## 🎯 Objetivo
Aprender a evaluar la calidad de los archivos FASTQ obtenidos mediante la herramienta **FastQC** en **Galaxy**, e interpretar los principales gráficos que genera.

## 🧠 1. ¿Qué es el control de calidad?
Antes de analizar datos de secuenciación, es indispensable revisar su **calidad**.  
Las máquinas de secuenciación pueden introducir errores:  
- Bases mal leídas,  
- Adaptadores no recortados,  
- Secuencias contaminantes,  
- O duplicaciones excesivas.  

El **control de calidad (Quality Control, QC)** nos permite detectar y corregir estos problemas **antes** de hacer análisis biológicos más complejos (como el alineamiento o la cuantificación de genes).

## 🔬 2. ¿Qué es FastQC?
**FastQC** es un programa que analiza los archivos **FASTQ** y genera un **reporte visual** sobre su calidad.  
Está integrado en Galaxy, por lo que no necesitas instalar nada.

Cada módulo del reporte muestra un aspecto distinto de los datos, y cada uno se marca con un color:
- 🟢 **PASS** — Todo bien  
- 🟡 **WARN** — Precaución (posible irregularidad)  
- 🔴 **FAIL** — Algo requiere revisión o filtrado  

> ⚠️ Importante: Un `WARN` o incluso un `FAIL` no significa necesariamente que tus datos sean inútiles; muchas veces se debe al tipo de experimento o secuenciador.

## ☁️ 3. Abrir FastQC en Galaxy
1. Entra a [https://usegalaxy.org](https://usegalaxy.org) e inicia sesión.  
2. En el **panel izquierdo**, busca la herramienta: **FastQC**.
3. Haz clic en **FastQC Read Quality reports**.

## ⚙️ 4. Configurar FastQC
1. En el campo **“Short read data from your history”**, selecciona los archivos FASTQ que descargaste (o la colección completa).  
2. Deja las demás opciones con valores predeterminados.  
3. Haz clic en **Run**.

Galaxy generará, para cada archivo:
- Un **archivo HTML** (reporte visual)
- Un **archivo ZIP** (con los datos numéricos del análisis)

## 🔎 5. Ver los resultados

1. En el **panel derecho (Historial)**, espera a que los procesos terminen (🟢).  
2. Haz clic en el ícono 👁️ **(View data)** sobre el archivo `.html` para abrir el reporte de FastQC.  
3. Aparecerá una interfaz con módulos (panel izquierdo) y gráficos (panel central).


## 📊 6. Interpretación de los resultados de FastQC

A continuación, se explican los principales **módulos del reporte** y cómo interpretarlos.

### 🧩 6.1. *Per base sequence quality*
**Qué muestra:**  
La calidad promedio de las bases en todas las lecturas, a lo largo de su posición.

- **Eje X:** posición en la lectura.  
- **Eje Y:** puntuación de calidad Phred (0–40).

**Interpretación:**
- 🟢 Las cajas verdes indican buena calidad (Phred > 30).
- 🟡 Amarillas: zonas donde la calidad comienza a bajar.
- 🔴 Rojas: mala calidad.

**Problemas comunes:** la calidad suele disminuir al final de las lecturas (bases 3’).  
**Acción recomendada:** recortar los extremos con baja calidad usando *TrimGalore!* o *Trimmomatic*.


### 🧬 6.2. *Per sequence quality scores*
**Qué muestra:**  Distribución de las calidades promedio por lectura.

**Interpretación:**
- La mayoría de lecturas deberían tener una calidad promedio alta (pico en valores > 30).  
- Si hay un segundo pico bajo, puede indicar errores sistemáticos o muestras degradadas.  

**Acción:** Filtrar lecturas con baja calidad promedio.


### 🧪 6.3. *Per base sequence content*
**Qué muestra:**  
El porcentaje de cada base (A, T, G, C) en cada posición a lo largo de las lecturas.

**Interpretación:**
- En secuenciación genómica aleatoria (shotgun), las líneas deberían mantenerse estables y paralelas.  
- En RNA-seq, puede haber variaciones al inicio por sesgos de transcripción.

**Acción:**  
Si hay fuertes desviaciones al inicio, recortar las primeras bases (p. ej. 5–10 bases) con *TrimGalore!*.


### 🧫 6.4. *Per sequence GC content*
**Qué muestra:**  
Distribución del contenido GC de todas las lecturas.

**Interpretación:**
- Se espera una **distribución normal** centrada en el GC medio del organismo.
- Picos adicionales o desplazados indican contaminación o regiones repetitivas.

**Acción:**  
Verificar si la desviación es biológica (por ejemplo, genes muy ricos en GC) o contaminación técnica.

---

### ⚙️ 6.5. *Per base N content*
**Qué muestra:**  
Porcentaje de bases “N” (no identificadas) por posición.

**Interpretación:**
- Debe estar cerca de **0%**.
- Un aumento de “N” indica problemas de lectura o secuenciador.

**Acción:**  
Descartar lecturas con muchos “N” o reportar al proveedor.

---

### 🧯 6.6. *Sequence Duplication Levels*
**Qué muestra:**  
Porcentaje de lecturas duplicadas (idénticas).

**Interpretación:**
- Duplicación alta puede deberse a:
- 🔸 **Sesgo de PCR** (mala preparación de biblioteca)
- 🔸 **Genes altamente expresados** (en RNA-seq, esto es normal)

**Acción:**  
Si se sospecha duplicación por PCR, usar herramientas para marcar o eliminar duplicados tras el alineamiento.

---

### 🧩 6.7. *Overrepresented sequences*
**Qué muestra:**  
Secuencias que aparecen con mayor frecuencia de la esperada (>0.1% del total).

**Interpretación:**
- Si coinciden con adaptadores o primers → contaminación técnica.
- Si son genes o transcritos reales → puede ser normal en RNA-seq.

**Acción:**  
Eliminar adaptadores con *TrimGalore!* o *Cutadapt*.

---

### 🧷 6.8. *Adapter content*
**Qué muestra:**  
Porcentaje de lecturas que contienen secuencias de adaptadores (por ejemplo, Illumina).

**Interpretación:**
- En datos ideales, el gráfico debe estar cercano a 0%.  
- Si hay un aumento al final de las lecturas (3’), indica adaptadores no recortados.

**Acción:**  
Recortar adaptadores con *TrimGalore!* o *Trimmomatic*, y luego volver a correr FastQC.

---

## 📋 7. Tabla resumen de interpretación

| 🔍 **Módulo** | 📈 **Qué indica** | ⚠️ **Qué significa un WARN o FAIL** | 🛠️ **Acción recomendada** |
|----------------|------------------|------------------------------------|---------------------------|
| Per base sequence quality | Calidad por posición | Caída de calidad al final | Recorte 3’ (Q20–30) |
| Per sequence quality | Calidad promedio por lectura | Pico bajo o doble distribución | Filtrar lecturas malas |
| Per base sequence content | Proporción A/T/G/C | Desbalance al inicio | Recortar primeras bases |
| GC content | Contenido GC | Distribución anormal | Verificar contaminación |
| N content | Bases no identificadas | Aumento de N | Filtrar lecturas |
| Duplication levels | Lecturas repetidas | Exceso de duplicación | Marcar/eliminar duplicados |
| Overrepresented seq. | Secuencias muy frecuentes | Adaptadores o contaminación | Recortar adaptadores |
| Adapter content | Secuencias Illumina detectadas | Picos altos al final | Recortar adaptadores |

---

## 🔧 8. Herramientas para corrección

| Herramienta | Función | Disponible en Galaxy |
|----------------|-----------|------------------------|
| **TrimGalore!** | Recorta adaptadores y bases de baja calidad | ✅ Sí |
| **Trimmomatic** | Recorte y filtrado avanzado (paired-end) | ✅ Sí |
| **Cutadapt** | Recorte de adaptadores personalizado | ✅ Sí |

> Después de corregir los problemas, vuelve a ejecutar **FastQC** sobre los archivos corregidos para confirmar la mejora.


## 🧠 9. Discusión sugerida

Reflexiona y responde:
- ¿Qué diferencias observas entre los proyectos GSE219205 y GSE230372?  
- ¿Qué tipo de errores son más frecuentes?  
- ¿Qué ajustes realizarías antes de continuar con el análisis?

Esto ayuda a conectar la interpretación con el contexto experimental.


## ✅ 10. Resultado final esperado

- Reportes HTML de **FastQC** con los módulos interpretados.  
- Identificación de posibles adaptadores o lecturas de baja calidad.  
- Decisión informada sobre si se necesita recorte o filtrado.  
- Datos listos para el siguiente paso del análisis (alineamiento o cuantificación).

---

## 📚 Referencias y recursos

- [FastQC Official Documentation](https://rtsf.natsci.msu.edu/genomics/technical-documents/fastqc-tutorial-and-faq.aspx)  
- [Galaxy Project: FastQC tool help](https://usegalaxy.org)  
- [TrimGalore! Documentation](https://www.bioinformatics.babraham.ac.uk/projects/trim_galore/)  
- [Trimmomatic Manual](http://www.usadellab.org/cms/?page=trimmomatic)
