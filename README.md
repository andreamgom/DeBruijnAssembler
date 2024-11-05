# Análisis y Reconstrucción de Secuencias de ADN usando Gráficos de De Bruijn

Este proyecto implementa un análisis y reconstrucción de secuencias de ADN utilizando gráficos de De Bruijn a partir de fragmentos `k-mers`. Está diseñado para ejecutarse en un entorno de Jupyter Notebook, permitiendo seleccionar diferentes conjuntos de `k-mers` y analizar la conectividad del grafo resultante.

## Descripción General

El proyecto utiliza gráficos de De Bruijn para representar y ensamblar fragmentos `k-mers` de una secuencia de ADN. La estructura permite verificar si existe un camino o ciclo Euleriano, lo que implica que es posible reconstruir la secuencia de ADN original. El proyecto también incluye herramientas para visualizar el grafo de De Bruijn y detectar posibles problemas de conectividad.

---

## Requisitos

Este proyecto requiere las siguientes librerías:
- **NetworkX** para la construcción y análisis de gráficos.
- **Matplotlib** para la visualización gráfica.
- **Ipywidgets** para la interacción en el entorno de Jupyter Notebook.

Instalación de dependencias:

```bash
pip install networkx matplotlib ipywidgets
```

---

## Funcionalidad del Proyecto

El código se organiza en pasos, permitiendo ejecutar cada etapa individualmente en un entorno de Notebook para facilitar la comprensión y el análisis.

### Paso 1: Selección del Conjunto de k-mers

En la primera celda, definimos diferentes conjuntos de `k-mers`. Usamos un widget interactivo de `ipywidgets` para seleccionar el conjunto de `k-mers` que se desea analizar.

### Paso 2: Construcción del Grafo de De Bruijn

La función `build_de_bruijn_graph()` crea un grafo de De Bruijn a partir de los `k-mers` seleccionados. La función toma cada `k-mer`, divide en prefijo y sufijo, y luego agrega un arco en el grafo dirigido de prefijo a sufijo.

```python
def build_de_bruijn_graph(kmers):
    G = nx.DiGraph()
    for kmer in kmers:
        prefix = kmer[:-1]
        suffix = kmer[1:]
        G.add_edge(prefix, suffix, label=kmer)
    return G
```

### Paso 3: Verificación de Camino o Ciclo Euleriano

La función `has_eulerian_path_or_cycle()` verifica si el grafo cumple con las condiciones para tener un camino o ciclo Euleriano. El camino Euleriano requiere un nodo de inicio y un nodo de fin con un desequilibrio de grado, mientras que el ciclo Euleriano requiere que todos los nodos tengan el mismo grado de entrada y salida.

```python
def has_eulerian_path_or_cycle(G):
    ...
    return graph_type, start_nodes, end_nodes
```

### Paso 4: Búsqueda del Camino o Ciclo Euleriano

En esta sección, `find_eulerian_path_or_cycle()` busca un camino o ciclo Euleriano usando funciones de NetworkX si el grafo cumple con las condiciones adecuadas. Si se encuentra, se almacena como una lista de nodos visitados en el camino.

```python
def find_eulerian_path_or_cycle(G):
    ...
    return path, graph_type
```

### Paso 5: Reconstrucción de la Secuencia de ADN

La función `reconstruct_sequence_from_path()` reconstruye la secuencia original tomando el primer nodo del camino y agregando los caracteres finales de los siguientes nodos en el camino o ciclo.

```python
def reconstruct_sequence_from_path(path):
    ...
    return sequence
```

### Paso 6: Análisis de Errores

Si el grafo no tiene un camino o ciclo Euleriano, `analyze_errors()` identifica los nodos no balanceados y sugiere ajustes en los `k-mers` para mejorar la conectividad. Esto es útil para detectar problemas como fragmentos de ADN incompletos o errores en las secuencias.

```python
def analyze_errors(G):
    ...
```

### Paso 7: Visualización del Grafo

La función `visualize_graph()` permite representar el grafo de De Bruijn utilizando `matplotlib`. Muestra los nodos y las aristas con etiquetas, resaltando el camino o ciclo Euleriano, si existe.

```python
def visualize_graph(G, title="Grafo de De Bruijn"):
    ...
    plt.show()
```

---

## Ejemplo de Uso

1. Seleccionar un conjunto de `k-mers` usando el widget interactivo en la primera celda.
2. Ejecutar cada celda sucesivamente para:
   - Construir el grafo.
   - Verificar la existencia de un camino o ciclo Euleriano.
   - Encontrar el camino o ciclo si es posible.
   - Reconstruir la secuencia de ADN.
   - Analizar errores si no se puede encontrar un camino o ciclo.
   - Visualizar el grafo final con el camino o ciclo resaltado.

---

## Resultados y Discusión

El análisis permite evaluar la conectividad de los fragmentos de ADN y reconstruir la secuencia siempre que los `k-mers` proporcionen un grafo de De Bruijn balanceado y conectado. En casos de fragmentos incompletos o errores en las secuencias, el análisis de errores proporciona información sobre los nodos no balanceados, lo que ayuda a identificar fragmentos problemáticos.

---

## Consideraciones y Limitaciones

Este enfoque es adecuado para conjuntos de `k-mers` pequeños o medianos. Sin embargo, en conjuntos de datos grandes, el tamaño del grafo crece significativamente, lo que puede hacer que el análisis y visualización sean más intensivos en memoria y procesamiento.

--- 

## Conclusión

El proyecto proporciona una implementación para la construcción de gráficos de De Bruijn a partir de fragmentos de ADN, permitiendo reconstruir secuencias completas cuando los `k-mers` cumplen con los requisitos de conectividad. La herramienta es especialmente útil para la visualización y análisis de errores en datos de secuencias.

