# Análisis y Reconstrucción de Secuencias de ADN usando Gráficos de De Bruijn

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

## Explicación del Proyecto

### Paso 1: Selección del Conjunto de k-mers

En la primera celda, definimos diferentes conjuntos de `k-mers`. Usamos un widget interactivo de `ipywidgets` para seleccionar el conjunto de `k-mers` que se desea analizar.

```python
import ipywidgets as widgets

kmers_options = [
    ["GACG", "GCTT", "TTAC", "ACTA", "TATG", "TGTG"],
    ["AGTC", "GTCA", "TCAG", "CAGT", "AGTT", "GTTG"],
    ["AGTT", "GTTG", "TTGA", "TGAC", "GACG", "ACGA", "CGAA", "GAAC", "AACG"]
]

kmer_selector = widgets.Dropdown(
    options=[(f"Conjunto {i+1}", kmer_set) for i, kmer_set in enumerate(kmers_options)],
    description='Seleccionar conjunto de k-mers:'
)
display(kmer_selector)
```

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

La función `has_eulerian_path_or_cycle()` verifica si el grafo cumple con las condiciones para tener un camino o ciclo Euleriano.

```python
def has_eulerian_path_or_cycle(G):
    in_degrees = Counter(G.in_degree(n) for n in G.nodes())
    out_degrees = Counter(G.out_degree(n) for n in G.nodes())

    start_nodes = [n for n in G.nodes() if G.out_degree(n) - G.in_degree(n) == 1]
    end_nodes = [n for n in G.nodes() if G.in_degree(n) - G.out_degree(n) == 1]
    balanced_nodes = [n for n in G.nodes() if G.in_degree(n) == G.out_degree(n)]

    if len(start_nodes) == 1 and len(end_nodes) == 1:
        return "path", start_nodes[0], end_nodes[0]
    elif not start_nodes and not end_nodes and len(balanced_nodes) == len(G.nodes()):
        return "cycle", None, None
    else:
        return None, start_nodes, end_nodes
```

### Paso 4: Búsqueda del Camino o Ciclo Euleriano

En esta sección, `find_eulerian_path_or_cycle()` busca un camino o ciclo Euleriano usando funciones de NetworkX si el grafo cumple con las condiciones adecuadas.

```python
def find_eulerian_path_or_cycle(G):
    graph_type, start_node, end_node = has_eulerian_path_or_cycle(G)
    
    if graph_type == "path":
        return list(nx.eulerian_path(G, source=start_node)), graph_type
    elif graph_type == "cycle":
        return list(nx.eulerian_circuit(G)), graph_type
    else:
        return None, None
```

### Paso 5: Reconstrucción de la Secuencia de ADN

La función `reconstruct_sequence_from_path()` reconstruye la secuencia original.

```python
def reconstruct_sequence_from_path(path):
    sequence = path[0][0]
    for _, suffix in path:
        sequence += suffix[-1]
    return sequence
```

### Paso 6: Análisis de Errores

Si el grafo no tiene un camino o ciclo Euleriano, `analyze_errors()` identifica los nodos no balanceados.

```python
def analyze_errors(G):
    in_degrees = {n: G.in_degree(n) for n in G.nodes()}
    out_degrees = {n: G.out_degree(n) for n in G.nodes()}

    unbalanced_nodes = [n for n in G.nodes() if in_degrees[n] != out_degrees[n]]
    if unbalanced_nodes:
        print("El grafo no tiene camino o ciclo Euleriano debido a nodos no balanceados.")
        print(f"Nodos no balanceados: {unbalanced_nodes}")
        print("Posibles problemas en la secuencia de entrada:")
        for node in unbalanced_nodes:
            print(f"Nodo {node} tiene {in_degrees[node]} aristas de entrada y {out_degrees[node]} aristas de salida.")
        print("Prueba revisar o ajustar los k-mers para asegurar conectividad completa.")
    else:
        print("El grafo es balanceado pero puede estar desconectado.")
```

### Paso 7: Visualización del Grafo

La función `visualize_graph()` permite representar el grafo de De Bruijn.

```python
def visualize_graph(G, title="Grafo de De Bruijn"):
    pos = nx.spring_layout(G, k=1.5, seed=42)
    plt.figure(figsize=(10, 6))
    nx.draw(G, pos, with_labels=True, node_color="lightgreen", node_size=2000, 
            font_size=10, font_weight="bold", edge_color="black", edgecolors='black', arrows=True)

    edge_labels = nx.get_edge_attributes(G, 'label')
    nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_labels, font_size=8, font_color="darkred")

    plt.title(title, fontsize=14, fontweight="bold", color="darkgreen")
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
