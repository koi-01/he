#### GBFS
```
# Step 1: We define the graph with heuristic values
class Graph:
    def __init__(self):
        self.edges = {}
        self.heuristics = {}

    def add_edge(self, from_node, to_node, cost):
        if from_node in self.edges:
            self.edges[from_node].append((to_node, cost))
        else:
            self.edges[from_node] = [(to_node, cost)]

    def set_heuristic(self, node, heuristic_value):
        self.heuristics[node] = heuristic_value

# Step 2: Greedy Best First Search function
def greedy_best_first_search(graph, start, goal):
    from queue import PriorityQueue

    visited = set()
    pq = PriorityQueue()
    pq.put((graph.heuristics[start], start))

    came_from = {}
    came_from[start] = None

    while not pq.empty():
        (heuristic, current_node) = pq.get()

        if current_node == goal:
            # Goal found, reconstruct the path
            path = []
            while current_node is not None:
                path.append(current_node)
                current_node = came_from[current_node]
            path.reverse()
            return path

        visited.add(current_node)

        for (neighbor, cost) in graph.edges.get(current_node, []):
            if neighbor not in visited:
                visited.add(neighbor)
                came_from[neighbor] = current_node
                pq.put((graph.heuristics[neighbor], neighbor))

    return None

# Step 3: User input section
graph = Graph()

print("Enter number of edges: ")
num_edges = int(input())

print("Enter edges in the format: from_node to_node cost")
for _ in range(num_edges):
    from_node, to_node, cost = input().split()
    graph.add_edge(from_node, to_node, int(cost))
    graph.add_edge(to_node, from_node, int(cost))  # assuming undirected graph

print("Enter number of nodes for heuristic values:")
num_nodes = int(input())

print("Enter node heuristic in the format: node heuristic_value")
for _ in range(num_nodes):
    node, heuristic = input().split()
    graph.set_heuristic(node, int(heuristic))

start = input("Enter the start node: ")
goal = input("Enter the goal node: ")

# Step 4: Run the algorithm and display result
path = greedy_best_first_search(graph, start, goal)
if path:
    print("Path found:", " -> ".join(path))
else:
    print("No path found.")
```

#### TICTACTOE
```
# Each tuple defines a win if all cells in tuple contain players mark.
winning_combos = (
    (0, 1, 2), (3, 4, 5), (6, 7, 8),
    (0, 3, 6), (1, 4, 7), (2, 5, 8),
    (0, 4, 8), (2, 4, 6)
)
def play(player):
    """Player selects cell.  Return winning combo if player won."""
    print("\n", " | ".join(grid[:3]))
    print("---+---+---")
    print("", " | ".join(grid[3:6]))
    print("---+---+---")
    print("", " | ".join(grid[6:]))

    # Loop until player enters number for empty cell.

    while True:
        try:
            cell = int(input(f"Enter cell for {player}: "))
            if str(cell) not in grid:
                raise ValueError
            grid[cell-1] = player
            break
        except ValueError:
            print("Enter number of open cell.")

    # Return winning combo if player wins, else None

    for combo in winning_combos:
        if all(grid[cell] == player for cell in combo):
            return combo
    return None


player1 = "X"+
player2 = "O"
player = player1
grid = list("123456789")

for i in range(9):
    won = play(player)
    if won:
        print(f"Player {player} won!")
        break
    player = player1 if player == player2 else player2
else:
    # 9 moves without a win is a draw.
    print("Game ends in a draw.")
```

#### A*
```
```

#### TSP
```
import heapq
from math import inf

N = 4

def find_min_edge(city, graph, visited):
    min_cost = inf
    for i in range(N):
        if not visited[i] and graph[city][i] and graph[city][i] < min_cost:
            min_cost = graph[city][i]
    return 0 if min_cost == inf else min_cost

def calculate_initial_bound(graph):
    bound = 0
    for i in range(N):
        bound += find_min_edge(i, graph, [False] * N)
    return (bound + 1) // 2

def tsp_branch_and_bound(graph):
    pq = []
    initial_path = [0]
    initial_bound = calculate_initial_bound(graph)

    # (bound, path)
    heapq.heappush(pq, (initial_bound, initial_path))

    min_cost = inf
    best_path = []

    while pq:
        bound, path = heapq.heappop(pq)

        if bound >= min_cost:
            continue

        last = path[-1]

        if len(path) == N:
            total_cost = bound + graph[last][0]
            if total_cost < min_cost:
                min_cost = total_cost
                best_path = path + [0]
            continue

        visited = [False] * N
        for city in path:
            visited[city] = True

        for next_city in range(N):
            if not visited[next_city] and graph[last][next_city]:
                new_path = path + [next_city]
                cost_to_next = graph[last][next_city]
                new_bound = bound + cost_to_next - find_min_edge(last, graph, visited)
                heapq.heappush(pq, (new_bound, new_path))

    print("Minimum TSP Cost:", min_cost)
    print("Path:", ' -> '.join(map(str, best_path)))

# Example graph
graph = [
    [0, 10, 15, 20],
    [10, 0, 35, 25],
    [15, 35, 0, 30],
    [20, 25, 30, 0]
]

tsp_branch_and_bound(graph)
```

#### A*
```
def astar_algorithm(start_node, goal_node):
    open_set = {start_node}
    closed_set = set()
    cost = {start_node: 0}
    parent = {start_node: None}

    while open_set:
        # Find node in open_set with the lowest f(n) = g(n) + h(n)
        curr = min(open_set, key=lambda node: cost.get(node, float('inf')) + heuristic(node))

        if curr == goal_node:
            return reconstruct_path(parent, start_node, goal_node, cost)

        open_set.remove(curr)
        closed_set.add(curr)

        neighbors = get_neighbors(curr)
        if neighbors is None:
            continue

        for neighbor, weight in neighbors:
            if neighbor in closed_set:
                continue

            total_cost = cost[curr] + weight

            if neighbor not in open_set:
                open_set.add(neighbor)

            if total_cost < cost.get(neighbor, float('inf')):
                parent[neighbor] = curr
                cost[neighbor] = total_cost

    print("Path does not exist!")
    return None

def reconstruct_path(parent, start, goal, cost):
    path = []
    node = goal
    while node is not None:
        path.append(node)
        node = parent[node]
    path.reverse()

    print(f"Path found: {path}")
    print(f"Total total_cost: {cost[goal]}")
    return path

def get_neighbors(node):
    return Graph_nodes.get(node, None)

def heuristic(node):
    H_dist = {
        'A': 11,
        'B': 6,
        'C': 99,
        'D': 1,
        'E': 5,
        'G': 0,
    }
    return H_dist.get(node, float('inf'))

Graph_nodes = {
    'A': [('B', 2), ('E', 3)],
    'B': [('C', 1), ('G', 9)],
    'C': None,
    'E': [('D', 6)],
    'D': [('G', 1)],
}

astar_algorithm('A', 'G')
```

#### N-QUEENS
```
def printSolution(board, N):
    for i in range(N):
        for j in range(N):
            if board[i][j] == 1:
                print("Q", end=" ")
            else:
                print(".", end=" ")
        print()

def isSafe(board, row, col, N):
    for i in range(col):
        if board[row][i] == 1:
            return False
    for i, j in zip(range(row, -1, -1), range(col, -1, -1)):
        if board[i][j] == 1:
            return False
    for i, j in zip(range(row, N, 1), range(col, -1, -1)):
        if board[i][j] == 1:
            return False
    return True

def solveNQUtil(board, col, N):
    if col >= N:
        return True
    for i in range(N):
        if isSafe(board, i, col, N):
            board[i][col] = 1
            if solveNQUtil(board, col + 1, N):
                return True
            board[i][col] = 0
    return False

def solveNQ(N):
    board = [[0 for _ in range(N)] for _ in range(N)]
    if not solveNQUtil(board, 0, N):
        print("Solution does not exist")
        return False
    printSolution(board, N)
    return True

if __name__ == "__main__":
    N = 4
    solveNQ(N)
graph = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F'],
    'D': [],
    'E': ['F'],
    'F': []
}

visited = []  # List to keep track of visited nodes
queue = []    # Initialize a queue

def bfs(visited, graph, node):
    visited.append(node)
    queue.append(node)

    while queue:
        s = queue.pop(0)
        print(s, end=" ")

        for neighbour in graph[s]:
            if neighbour not in visited:
                visited.append(neighbour)
                queue.append(neighbour)

# Calling the bfs function
bfs(visited, graph, 'A')
```

#### BFS
```
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    order = []  # To collect the traversal order

    while queue:
        node = queue.popleft()
        if node not in visited:
            order.append(node)
            visited.add(node)
            for neighbor in graph[node]:
                if neighbor not in visited:
                    queue.append(neighbor)

    print(" → ".join(order))  # Nicely formatted output

# Custom graph to produce A → B → E → D
graph = {
    'A': ['B', 'E'],
    'B': ['D'],
    'E': [],
    'D': []
}

print("BFS traversal starting from 'A':")
bfs(graph, 'A')
```

#### DFS
```
def dfs(graph, start, visited=None, order=None):
    if visited is None:
        visited = set()
    if order is None:
        order = []

    visited.add(start)
    order.append(start)

    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited, order)

    return order

# Custom graph to produce A → B → D → E
graph = {
    'A': ['B', 'E'],
    'B': ['D'],
    'E': [],
    'D': []
}

print("DFS traversal starting from 'A':")
traversal = dfs(graph, 'A')
print(" → ".join(traversal))
```
