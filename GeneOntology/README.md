#  Análisis de Enriquecimiento con Gene Ontology (GO)

Este tutorial describe cómo realizar un **análisis de enriquecimiento funcional** utilizando resultados del análisis diferencial de expresión de **DESeq2**, junto con la herramienta en línea **g:Profiler**.  
El objetivo es identificar **procesos biológicos, funciones moleculares y componentes celulares** sobre-representados en los genes diferencialmente expresados.

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

genes_difexp.txt

genes_up.txt

genes_down.txt

