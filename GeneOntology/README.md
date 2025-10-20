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


## 3. Interpretación de resultados

Una vez que el análisis termine, g:Profiler mostrará los términos de GO enriquecidos organizados en categorías:
<img width="1344" height="750" alt="image" src="https://github.com/user-attachments/assets/938ad686-91e5-4f4f-8e16-078e979384a3" />

Las pertenecientes al repositorio de GeneOntology son las siguientes:
| Categoría                   | Descripción                                          |
| --------------------------- | ---------------------------------------------------- |
| **BP (Biological Process)** | Procesos biológicos en los que participan los genes  |
| **MF (Molecular Function)** | Actividades moleculares o funciones de las proteínas |
| **CC (Cellular Component)** | Partes de la célula donde actúan los genes/proteínas |








