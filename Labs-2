# Assignment: Graph Traversal using BFS and DFS

### 1. Objective
To implement Breadth First Search (BFS) and Depth First Search (DFS) and understand their applications in graph traversal.

### 2. Theory

*BFS:* BFS explores a graph level by level. It uses a Queue (FIFO). It always finds the shortest path in an unweighted graph.
*Uses:* Shortest path in unweighted graphs, network exploration, social network degrees of separation, web crawling.

*DFS:* DFS explores a graph by going as deep as possible along one path, then backtracks. It uses a Stack or Recursion. It does not guarantee the shortest path.
*Uses:* Maze traversal, cycle detection, topological sorting, backtracking search, file-system traversal.

Both BFS and DFS are uninformed search algorithms. They do not know which direction is closer to the goal.
Feature | BFS | DFS
Data Structure | Queue | Stack / Recursion
Traversal Order | Level by Level | Depth by Depth
Shortest Path | Yes | No
Time Complexity | $O(V + E)$ | $O(V + E)$
### 3. BFS Code
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

void BFS(int start, vector<vector<int>> &adj, int n) {
    vector<bool> visited(n, false);
    queue<int> q;
    q.push(start);
    visited[start] = true;
    cout << "BFS: ";
    while (!q.empty()) {
        int node = q.front();
        q.pop();
        cout << node << " ";
        for (int neighbour : adj[node]) {
            if (!visited[neighbour]) {
                visited[neighbour] = true;
                q.push(neighbour);
            }
        }
    }
}

int main() {
    int n = 7;
    vector<vector<int>> adj(n);
    adj[0] = {1, 2};
    adj[1] = {3, 4};
    adj[2] = {5};
    adj[3] = {6};

    BFS(0, adj, n);
    return 0;
}
*Output:*
BFS: 0 1 2 3 4 5 6
### 4. DFS Code
#include <iostream>
#include <vector>
using namespace std;

void DFS_Util(int node, vector<vector<int>> &adj, vector<bool> &visited) {
    visited[node] = true;
    cout << node << " ";
    for (int neighbour : adj[node]) {
        if (!visited[neighbour]) {
            DFS_Util(neighbour, adj, visited);
        }
    }
}

int main() {
    int n = 7;
    vector<vector<int>> adj(n);
    adj[0] = {1, 2};
    adj[1] = {3, 4};
    adj[2] = {5};
    adj[3] = {6};

    vector<bool> visited(n, false);
    cout << "DFS: ";
    DFS_Util(0, adj, visited);
    return 0;
}
*Output:*
DFS: 0 1 3 6 4 2 5
### 5. Conclusion
BFS is best for finding the shortest path. DFS is best for deep traversal, cycle detection and topological sorting. The next step beyond uninformed search is informed search such as A*, which uses a heuristic to search more efficiently.
