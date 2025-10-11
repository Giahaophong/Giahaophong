#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <limits.h>

typedef struct {
    int **adj_matrix;
    char *vertex_data;
    int size;
} Graph;

Graph* create_graph(int size) {
    Graph *g = malloc(sizeof(Graph));
    g->size = size;
    g->adj_matrix = malloc(size * sizeof(int *));
    for (int i = 0; i < size; i++) {
        g->adj_matrix[i] = calloc(size, sizeof(int));
    }
    g->vertex_data = malloc(size * sizeof(char));
    return g;
}

void add_edge(Graph *g, int u, int v, int w) {
    if (u >= 0 && u < g->size && v >= 0 && v < g->size) {
        g->adj_matrix[u][v] = w;
        g->adj_matrix[v][u] = w;  // For undirected graph
    }
}

void add_vertex_data(Graph *g, int vertex, char data) {
    if (vertex >= 0 && vertex < g->size) {
        g->vertex_data[vertex] = data;
    }
}

int min_distance(int distances[], bool visited[], int size) {
    int min = INT_MAX, min_index = -1;
    for (int v = 0; v < size; v++) {
        if (!visited[v] && distances[v] <= min) {
            min = distances[v];
            min_index = v;
        }
    }
    return min_index;
}

void dijkstra(Graph *g, char start_vertex_data) {
    int start_vertex = -1;
    for (int i = 0; i < g->size; i++) {
        if (g->vertex_data[i] == start_vertex_data) {
            start_vertex = i;
            break;
        }
    }

    int distances[*g->size];
    bool visited[*g->size];
    for (int i = 0; i < g->size; i++) {
        distances[i] = INT_MAX;
        visited[i] = false;
    }
    distances[start_vertex] = 0;

    for (int i = 0; i < g->size - 1; i++) {
        int u = min_distance(distances, visited, g->size);
        visited[u] = true;

        for (int v = 0; v < g->size; v++) {
            if (!visited[v] && g->adj_matrix[u][v] && distances[u] != INT_MAX && distances[u] + g->adj_matrix[u][v] < distances[v]) {
                distances[v] = distances[u] + g->adj_matrix[u][v];
            }
        }
    }

    printf("Dijkstra's Algorithm starting from vertex %c:\n\n", start_vertex_data);
    for (int i = 0; i < g->size; i++) {
        printf("Shortest distance from %c to %c: %d\n", start_vertex_data, g->vertex_data[i], distances[i]);
    }
}

void free_graph(Graph *g) {
    for (int i = 0; i < g->size; i++) {
        free(g->adj_matrix[i]);
    }
    free(g->adj_matrix);
    free(g->vertex_data);
    free(g);
}

int main() {
    Graph *g = create_graph(6);

    add_vertex_data(g, 0, 'A');
    add_vertex_data(g, 1, 'B');
    add_vertex_data(g, 2, 'C');
    add_vertex_data(g, 3, 'D');
    add_vertex_data(g, 4, 'E');
    add_vertex_data(g, 5, 'G');

    add_edge(g, 0, 1, 1); 
    add_edge(g, 1, 2, 2); 
    add_edge(g, 0, 5, 10); 
    add_edge(g, 2, 3, 20); 
    add_edge(g, 2, 5, 3); 
    add_edge(g, 4, 3, 5); 
    add_edge(g, 5, 4, 4); 
    
    
    dijkstra(g, 'A');

    free_graph(g);
    return 0;
}

