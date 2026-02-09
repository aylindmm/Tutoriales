# DESeq2 en Galaxy: Análisis de Expresión Diferencial

## Introducción

DESeq2 es uno de los métodos más utilizados para el análisis de expresión diferencial en datos de RNA-seq. Modela los conteos de lecturas mediante una distribución binomial negativa, realiza normalización por tamaño de biblioteca y controla el error por pruebas múltiples.

Galaxy ofrece una implementación gráfica de DESeq2 que permite ejecutar este análisis de manera reproducible sin necesidad de programar en R.

<img width="1177" height="600" alt="image" src="https://github.com/user-attachments/assets/5a3d6ccc-9013-4a73-a0bd-a3834e0d8cdc" />


---

## Requisitos

Antes de comenzar, asegúrate de contar con:

- Matriz de conteos por gen
  - Salida típica de FeatureCounts
  - Filas: genes
  - Columnas: muestras
- Tabla de factores (metadata)
  - Debes saber cuáles son las muestras control y cuáles son el tratamiento

---
### 1. Seleccionar la herramienta

<img width="397" height="255" alt="image" src="https://github.com/user-attachments/assets/3469d0e2-a827-4a66-a4ad-9ec177c44574" />

---

### 2. Configuración de inputs

- Count matrix: matriz de conteos por gen

Seleciona la opción **Select datasets per level**

<img width="699" height="218" alt="image" src="https://github.com/user-attachments/assets/13b593cc-6e9a-4d7e-b583-c509a2cb17a4" />


---

### 3. Diseño experimental

La fórmula de diseño más común es: tratamiento vs control

Esto indica que se evaluará el efecto de la variable **tratamiento** sobre la expresión génica.

Galaxy suele inferir automáticamente esta fórmula a partir de la tabla de factores, pero debe verificarse.

---

### 4. Definición del contraste

Ejemplo:
- Factor: Tratamiento
- Nivel 1: siKDM4A
- Nivel 2: control

La comparación responde a la pregunta:
¿Qué genes cambian su expresión en siKDM4A respecto a control?

Debes ingresar las muestras que corresponden a cada grupo de la siguiente manera:

<img width="693" height="882" alt="image" src="https://github.com/user-attachments/assets/d696dd12-4d1f-498a-87a2-4cf4544b3599" />


El resto de opciones pueden quedarse como están.

---

### 6. Ejecutar el análisis

Hacer clic en "Run Tool" y esperar a que el análisis finalice correctamente.

---

## Output de DESeq2 en Galaxy

DESeq2 genera varios archivos de salida que permiten evaluar tanto los resultados estadísticos como la calidad del análisis.

---

### 1. Tabla de expresión diferencial

Este es el archivo principal del análisis. Cada fila corresponde a un gen e incluye, entre otras, las siguientes columnas:

- **baseMean**  
  Promedio de la expresión normalizada del gen considerando todas las muestras.

- **log2FoldChange**  
  Cambio de expresión entre las dos condiciones comparadas, expresado en log2.  
  Valores positivos indican sobreexpresión en la condición tratada; valores negativos indican subexpresión.

- **lfcSE**  
  Error estándar asociado al log2FoldChange.

- **stat**  
  Estadístico de prueba utilizado por el modelo.

- **pvalue**  
  Valor p sin corregir.

- **padj**  
  Valor p ajustado por múltiples pruebas (False Discovery Rate, FDR).  
  Este es el valor recomendado para determinar significancia estadística.

Para interpretación biológica se recomienda usar genes con:
- padj < 0.05
- y un |log2FoldChange| > 1

**NOTA:** Puedes usar Excel para encontrar los genes que cumplen con esas características.

Para descargar la tabla debes dar click en el ícono de guardar:

<img width="414" height="427" alt="image" src="https://github.com/user-attachments/assets/a41abc9f-05e7-4d25-88a5-39dbf7d67219" />


---

El segundo archivo contiene una serie de gráficos en PDF:

<img width="419" height="305" alt="image" src="https://github.com/user-attachments/assets/dc2bb0bf-d147-4350-9656-492ba4a05268" />

Algunos gráficos en los que debes prestar atención son:

### PCA (Principal Component Analysis)

El PCA muestra la relación global entre las muestras:
- Permite evaluar si las réplicas biológicas agrupan correctamente
- Ayuda a detectar outliers o problemas experimentales

<img width="658" height="513" alt="image" src="https://github.com/user-attachments/assets/3cea34f7-0e6f-4cea-bc2c-b5fa1a0f2f6f" />


---

### MA Plot

Gráfica que muestra:
- Eje X: expresión promedio (baseMean)
- Eje Y: log2FoldChange

Permite identificar genes diferencialmente expresados y evaluar sesgos dependientes de la expresión.

<img width="650" height="640" alt="image" src="https://github.com/user-attachments/assets/9319c426-f478-45a3-b83a-60f37b578ddc" />

Usualmente, genes que se expresan poco tienden a presentar valores de log2FoldChange altos pero menos confiables.

---
## Errores comunes

- Usar TPM, FPKM o CPM (lecturas normalizadas) como entrada
- Nombres de muestras inconsistentes entre archivos
- Falta de réplicas biológicas
- Diseños experimentales mal definidos

---

## Buenas prácticas

- Utilizar al menos tres réplicas biológicas por condición
- Filtrar genes con baja expresión
- Revisar el PCA antes de interpretar resultados
- Justificar claramente el diseño experimental

---

## Conclusión

DESeq2 en Galaxy permite realizar análisis de expresión diferencial robustos y reproducibles sin necesidad de programación. El uso de conteos crudos, un diseño experimental adecuado y la interpretación basada en FDR garantizan resultados estadísticamente sólidos y biológicamente interpretables.

Este análisis representa un paso central entre la cuantificación de lecturas y la interpretación funcional de un experimento de RNA-seq.

---


> ## 🧭 **Siguiente paso:** continúa con el análisis biológico de tus resultados [Ontología Génica](/GeneOntology/README.md)

