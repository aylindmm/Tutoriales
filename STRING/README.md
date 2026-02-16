#  Tutorial: Uso de STRING para redes de interacción proteína–proteína (PPI)

##  Introducción

**:contentReference[oaicite:0]{index=0}** (Search Tool for the Retrieval of Interacting Genes/Proteins) es una base de datos y plataforma web que permite explorar y analizar **redes de interacción proteína–proteína (PPI)**.

STRING integra múltiples fuentes de evidencia:
- Datos experimentales
- Bases de datos curadas
- Coexpresión génica
- Vecindad genómica
- Minería de texto
- Predicciones computacionales

Es ampliamente utilizada en **biología molecular, bioinformática, proteómica y análisis de RNA-seq**. Nos da información sobre las proteínas y cómo interactúan unas con otras.

---

## 🌐 Acceso a STRING

Accede a la plataforma en:

👉 https://string-db.org

No es necesario crear una cuenta para el uso básico.

---

## 1️⃣ Ingreso de proteínas

### 🔹 Opción A: Una proteína

1. Selecciona **Protein by name**
2. Ingresa el nombre de la proteína (ej. `TP53`)
3. Selecciona el organismo correcto (ej. *Homo sapiens*)
4. Haz clic en **Search**

---

### 🔹 Opción B: Múltiples proteínas (recomendado)

1. Selecciona **Multiple proteins**
2. Pega una lista de:
   - Símbolos génicos (TP53, BRCA1)
   - IDs UniProt
   - IDs Ensembl
3. Selecciona el organismo
4. Haz clic en **Search**

> ⚠️ STRING valida automáticamente la nomenclatura y solicita confirmación si hay ambigüedad.

---

## 2️⃣ Interpretación de la red

### 🟢 Nodos
- Representan proteínas
- El color puede indicar **clusters funcionales**

###  Aristas
- Representan interacciones
- El grosor indica **nivel de confianza**
- El color indica el tipo de evidencia:
  - Experimental
  - Base de datos
  - Coexpresión
  - Minería de texto
  - Predicción

---

## 3️⃣ Ajuste del nivel de confianza

En el panel izquierdo:

- **Confidence score**
  - Bajo: 0.15
  - Medio: 0.4
  - Alto: 0.7
  - Muy alto: 0.9

📌 Recomendación:
- Exploración inicial: ≥ 0.4  
- Análisis para publicación: ≥ 0.7

---

## 4️⃣ Control del número de interactores

Puedes mostrar:
- Solo las proteínas consultadas
- Interactores de primer nivel
- Interactores de segundo nivel

> ⚠️ Agregar demasiados interactores puede dificultar la interpretación biológica.

---

## 5️⃣ Análisis funcional (Functional Enrichment)

En la pestaña **Analysis** puedes obtener:

- **Gene Ontology (GO)**
  - Procesos biológicos
  - Función molecular
  - Componente celular
- **Vías metabólicas**
  - KEGG
  - Reactome
- **Dominios proteicos**
- Valores de **p-value** y **FDR**

Este análisis permite identificar procesos biológicos significativamente enriquecidos en la red.

---

## 6️⃣ Tipo de interacción

STRING permite identificar **complejos de proteínas** al ajustar el tipo de red a "physical subnetwork"

Esto nos permite identificar grupos de proteínas que forman complejos dentro de la célula.

---

## 7️⃣ Exportación de resultados

STRING permite exportar:

- Imagen de la red (PNG, SVG)
- Tabla de interacciones
- Resultados de enriquecimiento funcional
- Archivos compatibles con **Cytoscape**

Usa el botón **Export** en la interfaz.

---

## 🧪 Ejemplo de aplicación

1. Obtener genes diferencialmente expresados (RNA-seq)
2. Importar la lista en STRING
3. Identificar clusters funcionales
4. Analizar enriquecimiento biológico
5. Generar figuras para reportes, tesis o artículos

---

## ⚠️ Buenas prácticas

- STRING **no garantiza interacción física directa**
- Reportar siempre:
  - Nivel de confianza usado
  - Fuente de evidencia
- Verificar correctamente el organismo seleccionado

---

##  Conclusión

STRING es una herramienta esencial para transformar listas de genes o proteínas en **interpretaciones biológicas funcionales**, integrando múltiples fuentes de evidencia de manera rápida e intuitiva.

---

## 📚 Referencia

Szklarczyk et al. STRING v11: protein–protein association networks with increased coverage. *Nucleic Acids Research*.
