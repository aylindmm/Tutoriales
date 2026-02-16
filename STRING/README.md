#  Uso de STRING para redes de interacción proteína–proteína (PPI)

##  Introducción

**STRING** (Search Tool for the Retrieval of Interacting Genes/Proteins) es una base de datos y plataforma web que permite explorar y analizar **redes de interacción proteína–proteína (PPI)**.

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

   <img width="956" height="453" alt="image" src="https://github.com/user-attachments/assets/33e10299-7cc4-4d89-b61e-a4f1ea35ee7a" />


---

### 🔹 Opción B: Múltiples proteínas (recomendado)

1. Selecciona **Multiple proteins**
2. Pega una lista de:
   - Símbolos génicos (TP53, BRCA1)
   - IDs UniProt
   - IDs Ensembl
3. Selecciona el organismo
4. Haz clic en **Search**

   <img width="955" height="568" alt="image" src="https://github.com/user-attachments/assets/0209ba96-f85e-46d5-a0f2-36962aadd85a" />


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


<img width="267" height="212" alt="image" src="https://github.com/user-attachments/assets/08d69dcc-4e50-4d87-ae82-4f4ada02a295" />

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


<img width="1002" height="595" alt="image" src="https://github.com/user-attachments/assets/182630a4-f9ce-40ff-8762-51b1016baab6" />

---

## 4️⃣ Control del número de interactores

Puedes mostrar:
- Solo las proteínas consultadas
- Interactores de primer nivel (proteínas que no están en la lista que ingresé pero que interactúan con las proteínas que sí)
- Interactores de segundo nivel

> ⚠️ Agregar demasiados interactores puede dificultar la interpretación biológica.

<img width="458" height="235" alt="image" src="https://github.com/user-attachments/assets/0336bf15-ce03-4127-a739-eee2fbf5cc43" />

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

<img width="1040" height="797" alt="image" src="https://github.com/user-attachments/assets/7207cdc2-1e3b-4e11-bff6-189ed563c40d" />


---

## 6️⃣ Tipo de interacción

STRING permite identificar **complejos de proteínas** al ajustar el tipo de red a "physical subnetwork"

Esto nos permite identificar grupos de proteínas que forman complejos dentro de la célula.

<img width="1125" height="398" alt="image" src="https://github.com/user-attachments/assets/b2df138d-4932-4b71-a191-30aa45948ce8" />

---

## 7️⃣ Exportación de resultados

STRING permite exportar:

- Imagen de la red (PNG, SVG)
- Tabla de interacciones
- Resultados de enriquecimiento funcional
- Archivos compatibles con **Cytoscape**

Usa el botón **Export** en la interfaz.

<img width="1035" height="768" alt="image" src="https://github.com/user-attachments/assets/85948c7d-52dc-4c04-9b9d-e684cb330a27" />


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
