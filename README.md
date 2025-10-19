#include <iostream>
#include <vector>
#include <chrono>
#include <cstdlib>
#include <climits>

int NUM_VERTICES = 99000;  // Variable, not const, smaller for online compilers

int getMinVertex(std::vector<int>& distances, std::vector<bool>& visited) {
    int minDist = INT_MAX;
    int minIndex = -1;

    for (int i = 0; i < NUM_VERTICES; ++i) {
        if (!visited[i] && distances[i] <= minDist) {
            minDist = distances[i];
            minIndex = i;
        }
    }
    return minIndex;
}

void runDijkstra(std::vector<std::vector<int>>& graph, int source) {
    std::vector<int> distances(NUM_VERTICES, INT_MAX);
    std::vector<bool> visited(NUM_VERTICES, false);

    distances[source] = 0;
    double timeGetMin = 0.0, timeUpdate = 0.0;

    auto startTotal = std::chrono::high_resolution_clock::now();

    for (int count = 0; count < NUM_VERTICES - 1; ++count) {
        auto startGetMin = std::chrono::high_resolution_clock::now();
        int u = getMinVertex(distances, visited);
        auto endGetMin = std::chrono::high_resolution_clock::now();
        timeGetMin += std::chrono::duration<double>(endGetMin - startGetMin).count();

        visited[u] = true;

        auto startUpdate = std::chrono::high_resolution_clock::now();
        for (int v = 0; v < NUM_VERTICES; ++v) {
            if (!visited[v] && graph[u][v] != 0 && distances[u] != INT_MAX &&
                distances[u] + graph[u][v] < distances[v]) {
                distances[v] = distances[u] + graph[u][v];
            }
        }
        auto endUpdate = std::chrono::high_resolution_clock::now();
        timeUpdate += std::chrono::duration<double>(endUpdate - startUpdate).count();
    }

    auto endTotal = std::chrono::high_resolution_clock::now();

    std::cout << "GetMin Vertex Total Time: " << timeGetMin << " seconds\n";
    std::cout << "Distance Update Total Time: " << timeUpdate << " seconds\n";
    std::cout << "Total Execution Time: " << std::chrono::duration<double>(endTotal - startTotal).count() << " seconds\n";
}

int main() {
    std::vector<std::vector<int>> graph(NUM_VERTICES, std::vector<int>(NUM_VERTICES, 0));

    for (int i = 0; i < NUM_VERTICES; ++i) {
        for (int j = 0; j < NUM_VERTICES; ++j) {
            if (i != j) {
                graph[i][j] = rand() % 50 + 1;
            }
        }
    }

    runDijkstra(graph, 0);
    return 0;
}
