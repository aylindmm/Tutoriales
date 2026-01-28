¿Qué herramienta debo usar?

Es común confundirse entre las distintas "versiones" de AlphaFold. Aquí tienes las principales diferencias:

| Característica | **AlphaFold Database** | **AlphaFold Server** | **ColabFold** (Este tutorial) |
| :--- | :--- | :--- | :--- |
| **Motor** | AlphaFold 2 (Pre-calculado) | AlphaFold 3 | **AlphaFold 2 + MMseqs2** |
| **Velocidad** | Instantánea (si existe) | Lenta (Cola de espera) | **Muy Rápida** (GPU Gratuita) |
| **Complejos** | No (Solo monómeros) | Sí | **Sí (Multimer)** |
| **Personalización**| Nula | Media | **Alta** (Control total) |
| **Uso Ideal** | Consultas rápidas de proteínas individuales. | Modelar complejos con ADN/ARN/Ligandos. | **Docking Proteína-Proteína rápido.** |

---
