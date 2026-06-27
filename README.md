# Parte 1: Evaluación Conceptual y Buenas Prácticas

## Formatos de Intercambio

| Formato | Ventajas | Desventajas |
|----------|----------|-------------|
| **CSV** | Es extremadamente ligero y fácil de generar desde herramientas como Excel. | No soporta jerarquías complejas y únicamente maneja datos planos. |
| **XML** | Es un formato estructurado que soporta tipos de datos y jerarquías. | Es más verboso y genera archivos más pesados que JSON o CSV. |

---

## 1. Diferenciación de Procesos

**Serialización:** Es el proceso de convertir un objeto de C# a formato JSON utilizando la librería `System.Text.Json`.

**Deserialización:** Es el proceso inverso, donde un documento JSON se convierte nuevamente en un objeto de C# mediante `System.Text.Json`.

---

## 2. El Antipatrón del Rendimiento (N+1)

El problema **N+1** ocurre cuando se realiza una inserción en la base de datos por cada registro leído del archivo, ejecutando múltiples llamadas a `SaveChanges()`, lo que disminuye considerablemente el rendimiento.

La estrategia recomendada es utilizar **Batching (procesamiento por lotes)**, almacenando primero todos los registros en una colección y realizando una única inserción mediante `AddRange()` seguida de una sola llamada a `SaveChangesAsync()`. Esto reduce la cantidad de operaciones contra la base de datos y mejora significativamente el rendimiento.
