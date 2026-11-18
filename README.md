# Ai-codes-
Ai codes 
# ===== Program 1: Simple Reflex Agent (Vacuum Cleaner World) =====
def agent(loc, status):
    if status == "Dirty": return "Suck"
    return "Right" if loc == "A" else "Left"

env = {"A": "Dirty", "B": "Dirty"}
loc = "A"
for _ in range(4):
    act = agent(loc, env[loc])
    print(loc, env[loc], "->", act)
    if act == "Suck": env[loc] = "Clean"
    else: loc = "B" if act == "Right" else "A"


# ===== Program 2: Model-Based Agent (Grid Navigation) =====
grid = ["S.#.",
        ".##.",
        "...G"]
pos, goal = (0, 0), (2, 3)
seen, path = {pos}, [pos]          # seen = internal model (memory)
while pos != goal:
    for dr, dc in [(0,1),(1,0),(0,-1),(-1,0)]:
        r, c = pos[0]+dr, pos[1]+dc
        if 0<=r<3 and 0<=c<4 and grid[r][c] != "#" and (r,c) not in seen:
            pos = (r, c); seen.add(pos); path.append(pos); break
    else:
        path.pop(); pos = path[-1]  # dead end -> backtrack
print("Path:", path)


# ===== Program 3: A* Search (8-Puzzle) =====
import heapq
goal = (1,2,3,4,5,6,7,8,0)
def h(s):  # Manhattan distance
    return sum(abs(i//3-s.index(v)//3)+abs(i%3-s.index(v)%3)
               for i, v in enumerate(goal) if v)
def nbrs(s):
    i = s.index(0)
    for d in (-3, 3, -1, 1):
        j = i + d
        if 0<=j<9 and not (d==-1 and i%3==0) and not (d==1 and i%3==2):
            l = list(s); l[i], l[j] = l[j], l[i]; yield tuple(l)
def astar(start):
    pq, seen = [(h(start), 0, start, [start])], set()
    while pq:
        f, g, s, p = heapq.heappop(pq)
        if s == goal: return p
        if s in seen: continue
        seen.add(s)
        for n in nbrs(s): heapq.heappush(pq, (g+1+h(n), g+1, n, p+[n]))
p = astar((1,2,3,4,0,6,7,5,8))
print(len(p)-1, "moves")
for s in p: print(s[:3], s[3:6], s[6:])


# ===== Program 4: Alpha-Beta Pruning =====
import math
def ab(node, depth, a, b, maxp):
    if depth == 0 or not isinstance(node, list): return node
    v = -math.inf if maxp else math.inf
    for c in node:
        r = ab(c, depth-1, a, b, not maxp)
        if maxp: v = max(v, r); a = max(a, v)
        else:    v = min(v, r); b = min(b, v)
        if b <= a: break            # prune
    return v
tree = [[[3,5],[6,9]], [[1,2],[0,-1]]]
print("Best value:", ab(tree, 3, -math.inf, math.inf, True))


# ===== Program 5: Sudoku Solver (Backtracking + Constraints) =====
def cand(b, r, c):  # constraint: row, column, box
    used = set(b[r]) | {b[i][c] for i in range(9)} | \
           {b[r//3*3+i][c//3*3+j] for i in range(3) for j in range(3)}
    return [n for n in range(1, 10) if n not in used]
def solve(b):
    cells = [(len(cand(b,r,c)), r, c) for r in range(9) for c in range(9) if b[r][c]==0]
    if not cells: return True
    _, r, c = min(cells)            # fewest options first
    for n in cand(b, r, c):
        b[r][c] = n
        if solve(b): return True
        b[r][c] = 0                 # backtrack
    return False
s = ("53..7....6..195....98....6.8...6...34..8.3..17...2...6"
     ".6....28....419..5....8..79")
b = [[int(ch) if ch.isdigit() else 0 for ch in s[i:i+9]] for i in range(0, 81, 9)]
if solve(b):
    for row in b: print(row)


# ===== Program 6: Planning using BFS (Robot Path Planning) =====
from collections import deque
grid = ["S.#.",
        "..#.",
        "...G"]
def bfs(s, g):
    q, seen = deque([(s, [s])]), {s}
    while q:
        (r, c), p = q.popleft()
        if (r, c) == g: return p
        for dr, dc in ((0,1),(1,0),(0,-1),(-1,0)):
            n = (r+dr, c+dc)
            if 0<=n[0]<3 and 0<=n[1]<4 and grid[n[0]][n[1]] != "#" and n not in seen:
                seen.add(n); q.append((n, p+[n]))
print(bfs((0,0), (2,3)))


# ===== Program 7: Problem Solving using DFS (Water Jug 4L & 3L) =====
def dfs(s, seen, path):
    if s[0] == 2: return path        # goal: 2L in 4L jug
    seen.add(s)
    a, b = s
    x, y = min(a, 3-b), min(b, 4-a)
    for n in [(4,b), (a,3), (0,b), (a,0), (a-x,b+x), (a+y,b-y)]:
        if n not in seen:
            r = dfs(n, seen, path+[n])
            if r: return r
print(dfs((0,0), set(), [(0,0)]))


# ===== Program 8: 8-Queens using Backtracking =====
def solve(row=0, cols=[]):
    if row == 8: return cols
    for c in range(8):
        if all(c != x and abs(c-x) != row-r for r, x in enumerate(cols)):
            res = solve(row+1, cols+[c])
            if res: return res
sol = solve()
for c in sol: print("." * c + "Q" + "." * (7-c))


# ===== Program 9: Rule-Based Chatbot =====
import re
rules = {r"\b(hi|hello|hey)\b": "Hello! How can I help?",
         r"your name": "I am a simple chatbot.",
         r"how are you": "I'm fine, thanks!",
         r"\b(bye|exit)\b": "Goodbye!"}
while True:
    msg = input("You: ").lower()
    print("Bot:", next((v for k, v in rules.items() if re.search(k, msg)),
                       "Sorry, I don't understand."))
    if re.search(r"\b(bye|exit)\b", msg): break


# ===== Program 10: Game Agent using Minimax (Tic-Tac-Toe) =====
W = [(0,1,2),(3,4,5),(6,7,8),(0,3,6),(1,4,7),(2,5,8),(0,4,8),(2,4,6)]
def win(b):
    for x, y, z in W:
        if b[x] != " " and b[x] == b[y] == b[z]: return b[x]
def minimax(b, p):                   # X = human (max), O = AI (min)
    w = win(b)
    if w: return (1 if w == "X" else -1), None
    if " " not in b: return 0, None
    best = None
    for i in range(9):
        if b[i] == " ":
            b[i] = p
            s = minimax(b, "O" if p == "X" else "X")[0]
            b[i] = " "
            if best is None or (p=="X" and s>best[0]) or (p=="O" and s<best[0]):
                best = (s, i)
    return best
b = [" "] * 9
while not win(b) and " " in b:
    b[int(input("Your move X (0-8): "))] = "X"
    if not win(b) and " " in b: b[minimax(b, "O")[1]] = "O"
    print(b[:3], b[3:6], b[6:], sep="\n")
print("Winner:", win(b) or "Draw")


