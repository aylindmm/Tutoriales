#  Análisis de Enriquecimiento con Gene Ontology (GO)

Aylin del Moral
---

Este tutorial describe cómo realizar un análisis de enriquecimiento funcional utilizando resultados del análisis diferencial de expresión de **DESeq2**, junto con la herramienta en línea **g:Profiler**.  
El objetivo es identificar procesos biológicos, funciones moleculares y componentes celulares sobrerrepresentados en los genes diferencialmente expresados.

---

## Requisitos

- R y Bioconductor instalados
- Paquete [`DESeq2`](https://bioconductor.org/packages/release/bioc/html/DESeq2.html)
- Resultados de análisis diferencial (`DESeqDataSet`)

---

##  1. Preparar las listas de genes desde DESeq2

Después de ejecutar tu análisis con `DESeq(dds)`, genera tres listas de genes:

1. **Genes diferencialmente expresados** (`padj < 0.05` y `|log2FoldChange| > 1`)
2. **Genes sobreexpresados** (`log2FoldChange > 1`)
3. **Genes subexpresados** (`log2FoldChange < -1`)

Ejemplo de código en R:

```r
# Cargar resultados del análisis
res <- results(dds)

# Filtrar genes significativamente expresados
res_filtered <- res[which(res$padj < 0.05 & abs(res$log2FoldChange) > 1), ]

# Eliminar filas con valores NA
res_filtered <- na.omit(res_filtered)

# Lista general de genes diferencialmente expresados
gene_list <- rownames(res_filtered)
write.table(gene_list, "genes_difexp.txt", quote = FALSE, row.names = FALSE, col.names = FALSE)

# Lista de genes sobreexpresados
up_genes <- rownames(res_filtered[res_filtered$log2FoldChange > 1, ])
write.table(up_genes, "genes_up.txt", quote = FALSE, row.names = FALSE, col.names = FALSE)

# Lista de genes subexpresados
down_genes <- rownames(res_filtered[res_filtered$log2FoldChange < -1, ])
write.table(down_genes, "genes_down.txt", quote = FALSE, row.names = FALSE, col.names = FALSE)
```

Esto generará tres archivos de texto:

`genes_difexp.txt`
`genes_up.txt`
`genes_down.txt`

--- 
##  2. Ejecutar el análisis en g:Profiler

1. Abre [`g:Profiler`](https://biit.cs.ut.ee/gprofiler/gost)
2. Carga una de tus listas. Copia y pega los nombres de los genes, o sube el archivo .txt
3. Selecciona el organismo correcto (por ejemplo, Homo sapiens, Mus musculus, Danio rerio, etc.)
4. Haz clic en "Run" para iniciar el análisis
   
<img width="1351" height="647" alt="image" src="https://github.com/user-attachments/assets/e83487ce-d724-4ba4-b67a-ba02ea7e6ae4" />

---

## 3. Interpretación de resultados

Una vez que el análisis termine, g:Profiler mostrará los términos de GO enriquecidos organizados en categorías:


📈 Bubble plot (gráfico de burbujas)

<img width="1344" height="750" alt="image" src="https://github.com/user-attachments/assets/938ad686-91e5-4f4f-8e16-078e979384a3" />

El bubble plot resume visualmente los términos de GO más significativos:

**Eje Y**: representa la magnitud del enriquecimiento. A mayor valor, mayor significancia.

**Eje X**: muestra los términos de GO (procesos, funciones o componentes).

**Tamaño de la burbuja**: indica cuántos genes de tu lista pertenecen a ese término.

**Color de la burbuja**: representa el nivel de significancia estadística (por ejemplo, el valor p-ajustado).
  Colores más intensos = términos más significativos.
  Colores más claros = menor significancia.

*En resumen*: busca burbujas grandes y de color intenso, ya que indican procesos altamente enriquecidos con varios genes implicados.

📊 Tabla de resultados

<img width="936" height="403" alt="image" src="https://github.com/user-attachments/assets/92f5eea3-c493-4bf4-bb96-eff9d1116957" />

| Columna               | Significado                                                           |
| --------------------- | --------------------------------------------------------------------- |
| **Term ID**           | Identificador del término GO (ej. GO:0008150)                         |
| **Term name**         | Nombre descriptivo del proceso o función (ej. *cell differentiation*) |
| **Source**            | Base de datos de origen (GO:BP, KEGG, Reactome, etc.)                 |
| **Adjusted p-value**  | Significancia corregida (cuanto menor, más confiable el resultado)    |



Las categorías pertenecientes al repositorio de GeneOntology son las siguientes:

| Categoría                   | Descripción                                          |
| --------------------------- | ---------------------------------------------------- |
| **BP (Biological Process)** | Procesos biológicos en los que participan los genes  |
| **MF (Molecular Function)** | Actividades moleculares o funciones de las proteínas |
| **CC (Cellular Component)** | Partes de la célula donde actúan los genes/proteínas |

💡 Consejo: Compara los resultados entre tus tres listas (diferencial, sobreexpresada y subexpresada).
Observa qué tipos de procesos dominan en cada una; por ejemplo, los genes sobreexpresados podrían estar asociados con proliferación celular, mientras que los subexpresados con señalización o apoptosis.

El resto de categorías pertenecen a distintos repositorios:

<img width="1248" height="751" alt="image" src="https://github.com/user-attachments/assets/ec7d93b4-e040-4627-9dc1-12c89d252739" />

##  4. Explorar un término de GO de interés

1. Elige un término GO que te parezca relevante en tus resultados.

2. Busca su definición en la página oficial de [`Gene Ontology`](https://geneontology.org/)
3. En g:Profiler, descarga los resultados en formato csv.
   
   <img width="1350" height="739" alt="image" src="https://github.com/user-attachments/assets/c287c412-96c8-45d5-bb6a-bb84f538068f" />

5. Abre el archivo en Excel. Busca la columna "intersections"; en esta se muestran los genes de tu lista asociados a cada término enriquecido.
   
   <img width="1365" height="751" alt="image" src="https://github.com/user-attachments/assets/611eda33-aca2-4db2-8526-b592c1dd93fc" />

6. Si necesitas convertir identificadores (por ejemplo, de ENSG a nombres de genes), utiliza la herramienta [g:Convert](https://biit.cs.ut.ee/gprofiler/convert)

## 5. Reporte de resultados (plantilla sugerida)

Puedes usar esta tabla para documentar tus hallazgos:

| Lista analizada  | Término GO | Categoría | p-ajustada | Genes en intersección | Descripción           |
| ---------------- | ---------- | --------- | ---------- | --------------------- | --------------------- |
| genes_up.txt     | GO:0008283 | BP        | 1.2e-5     | MYC, CCND1, CDK4      | Proliferación celular |
| genes_down.txt   | GO:0006915 | BP        | 3.4e-4     | BAX, CASP3            | Apoptosis             |
| genes_difexp.txt | GO:0003723 | MF        | 2.1e-3     | RPL3, RPS9            | Unión a ARN           |









