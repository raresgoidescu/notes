# Grafuri

## Cuprins

- [Tipuri](#tipuri)
- [Notații](#notații)
- [Parcurgeri](#parcurgeri)
    - [DFS (Stivă - LIFO)](#dfs-stivă---lifo)
    - [BFS (Coadă - FIFO)](#bfs-coadă---fifo)
    - [DFS Iterativ (IDDFS - Iterative Deepening DFS)](#dfs-iterativ-iddfs---iterative-deepening-dfs)
    - [După "optim"](#după-optimizare)
    - [Probabilistice (Monte Carlo)](#probabilistice-monte-carlo)
- [Sortare topologică](#sortare-topologică)
    - [Kahn](#kahn)
- [Componente tare conexe](#componente-tare-conexe)
    - [Algoritmul lui Kosaraju](#algoritmul-lui-kosaraju)
- [Puncte de articulație](#puncte-de-articulație)
    - [Algoritmul lui Tarjan](#algoritmul-lui-tarjan)
- [Punți](#punți)
- [Drumuri de cost minim (cu sursa unică)](#drumuri-de-cost-minim-cu-sursa-unică)
    - [Dijkstra](#dijkstra)
        - [Heap binar](#heap-binar)
        - [Heap Fibonacci](#heap-fibonacci)
        - [Algoritmul lui Dial (costuri < n)](#algoritmul-lui-dial-costuri--n)
        - [Aplicații Dijkstra](#aplicații-dijkstra)
    - [Bellman-Ford](#bellman-ford)
        - [Optimizări Bellman-Ford (1)](#optimizări-bellman-ford-1)
        - [Optimizări Bellman-Ford (2)](#optimizări-bellman-ford-2)
- [Drumuri de cost minim (multipunct - multipunct)](#drumuri-de-cost-minim-multipunct---multipunct)
    - [Floyd-Warshall / Roy-Floyd](#floyd-warshall--roy-floyd)
    - [Johnson](#johnson)
- [Arbori minimi de acoperire](#arbori-minimi-de-acoperire)
    - [Algoritmul lui Prim](#algoritmul-lui-prim)
    - [Algoritmul lui Kruskal](#algoritmul-lui-kruskal)
    - [Multimi disjuncte](#multimi-disjuncte)
- [Fluxuri maxime](#fluxuri-maxime)
    - [Ford-Fulkerson](#ford-fulkerson)
    - [Edmonds-Karp](#edmonds-karp)
    - [(Pompare) Preflux](#pompare-preflux)
- [TIPS & TRICKS](#tips--tricks)

## Tipuri

- DAG (aciclice, orientate)
- Cu ponderi pe arce (e.g.: lungime, cost, etc.)
- Cu ponderi pe noduri (e.g.: importanță, rang - Page Rank Google)
- Ordonate
- Multigraf
- Arbori, păduri, liste
- Bipartite:
    - Nodurile sunt colorate folosind 2 culori.
    - Nu există vecini de aceeași culoare.

## Notații

- G = (V, E)
- V – mulțimea de noduri
- E – mulțimea de muchii / arce
    - (u, v) – arcul / muchia u, v
    - u..v – drum de la u la v
        - dacă există mai multe variante notăm u..x..v, u..y..v;
- `R(u)` - `reachable(u)` = mulțimea nodurilor ce pot fi atinse pe căi ce pleacă din 𝑢
- `succs(u)`/`neighs(u)`
    - mulțimea succesorilor lui u (graf orientat)
    - mulțimea nodurilor adiacente lui u (graf neorientat)
- `c(u)` – culoarea nodului (starea nodului la un moment de timp):
    - Alb – nedescoperit
    - Gri – descoperit, în curs de prelucrare
    - Negru – descoperit și terminat (cu semnificații diferite pentru BFS și DFS)
- `p(u)` – părintele lui u
    - nodul din care s-a ajuns în nodul u prima oară
- tipuri de muchii:
    - directe: (u, v), c[u] = gri, c[v] = alb
    - inverse: (u, v), c[u] = gri, c[v] = gri
    - back-edge: (u, v), c[u] = gri, c[v] = gri, d[v] < d[u]
    - inainte: (u, v), c[u] = gri, c[v] = negru, d[u] < d[v]
    - transversal: (u, v), c[u] = gri, c[v] = negru, d[u] > d[v]

## Parcurgeri

### DFS (Stivă - LIFO)

- Nu exista nod de start fixat, toate nodurile sunt parcurse.
- `d[u]` - momentul descoperirii nodului u
- `f[u]` - momentul finalizarii nodului u
- timestamp global
- `p[u]`, `c[u]`.

```python
def DFS(G):
    parent = {u: None for u in G}
    color = {u: 'white' for u in G}
    discovery = {}
    finish = {}
    time = [0]
    has_cycle = [False]
    topo_sort = []

    def DFS_explore(u):
        color[u] = 'gray'
        time[0] += 1
        discovery[u] = time[0]

        for v in G[u]:
            if color[v] == 'white':
                parent[v] = u
                DFS_explore(v)
            elif color[v] == 'gray':
                has_cycle[0] = True

        color[u] = 'black'
        time[0] += 1
        finish[u] = time[0]
        topo_sort.append(u)

    for u in G:
        if color[u] == 'white':
            DFS_explore(u)

    topo_sort.reverse()

    return parent, discovery, finish, has_cycle[0], topo_sort
```

- Graf orientat: daca ajung dintr-un nod gri in alt nod gri, am ciclu
- Daca generează o singură componentă conexă, graf conex

- Complexitate: O(n+m); n - nr. noduri, m - nr. muchii
- Optimalitate: NU
- Parcurge tot graful? DA

---

### BFS (Coadă - FIFO)

[?] Inducție optimalitate (C5, Slide 25)

- Caracterizare grafuri
- Nod de start `s`; foarte important
- Determină numărul minim de arce / muchii între `s` și orice alt nod din graf
- Cel mai scurt drum când nu există o funcție de cost asociată grafului
- `p[u]`, `c[u]`, `dist[u]`.
- Complexitate: O(n+m); n - nr. noduri, m - nr. muchii
- Optimalitate: DA
- Parcurge tot graful? NU

```python
def BFS(source, G):
    parent = {u: None for u in G}
    color = {u: 'white' for u in G}
    dist = {u: float('inf') for u in G}
    queue = []

    color[source] = 'gray'
    dist[source] = 0
    queue.append(source)

    while queue:
        u = queue.pop(0)
        for v in G[u]:
            if color[v] == 'white':
                color[v] = 'gray'
                parent[v] = u
                dist[v] = dist[u] + 1
                queue.append(v)
        color[u] = 'black'

    return parent, dist
```

- Aplicații:
    - Căutare într-un graf (câteodată cu mai multe surse)
    - Când se iese din BFS, se începe altul dintr-un nod care a rămas alb

- Lungime minimă: **Dijkstra**
- Arbori minimi de acoperire: **Prim**


- Bipartite:
    - Colorezi alternativ:
        - c[src] = 0,
        - c[neigh[src]] = 1,
        - c[neigh[neigh[src]]] = 0, ...

---

### DFS Iterativ (IDDFS - Iterative Deepening DFS)
- Explorăm iterativ graful în adâncime
- Combină beneficii BFS & DFS
- Aplicare DFS cu adâncime limitată, care crește iterativ (evită cazuri în care o cale poate fi foarte lungă)

---

### După "optim"
- Coadă de priorități

---

### Probabiliste (Monte Carlo)

---

## Sortare topologică

### Kahn

- [YouTube](https://www.youtube.com/watch?v=cIBFEhD77b4)

```python
def kahn(G):
    in_degree = {u: 0 for u in G}
    for u in G:
        for v in G[u]:
            in_degree[v] += 1

    queue = [u for u in G if in_degree[u] == 0]
    topo_order = []

    while queue:
        u = queue.pop(0)
        topo_order.append(u)

        for v in G[u]:
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    if len(topo_order) != len(G):
        raise ValueError("Graful are ciclu")

    return topo_order
```

**Aplicații**:

- Cum putem calcula numarul de drumuri intre două noduri într-un graf orientat aciclic? **Sortare topologică + DP**:
    - Sortăm topologic graful
    - Initializăm `dp[u] = 0` pentru toate nodurile, cu excepția sursei `dp[source] = 1`
    - Parcurgem nodurile în ordine topologică:
        - Pentru fiecare nod `u`, pentru fiecare vecin `v` al lui `u`, adunăm numărul de drumuri:
        - `dp[v] += dp[u]`
- Cum identificam cel mai lung drum intr-un graf orientat aciclic? **Sortare topologică + DP**:
    - Sortăm topologic graful
    - Initializăm `dp[u] = -1` pentru toate nodurile, cu exceptia celor cu in-degree 0, care vor fi `dp[idz] = 0`
    - Parcurgem nodurile în ordine topologică:
        - Pentru fiecare nod `u`, pentru fiecare vecin `v` al lui `u`, actualizăm lungimea drumului:
        - `dp[v] = max(dp[v], dp[u] + 1)`
        - retinem parintii pentru fiecare nod (dp[u] + 1 > dp[v] => p[v] = u)
    - Pentru a gasi cel mai lung drum din nodul x, cu dp[x] = maxim, si urmărim părinții până ajungem la un nod cu in-degree 0.

## Componente tare conexe

- Un graf tare conex este un graf pentru care oricare doua noduri (`a`, `b`) sunt conectate printr-un drum atat de la `a` la `b` cat si de la `b` la `a`.
- Un graf care nu este tare conex poate fi împărțit în componente tare conexe, fiecare componentă fiind un subgraf tare conex.

### Algoritmul lui Kosaraju

- Sortăm topologic graful folosind DFS
- Inversăm direcția arcelor (transpunem graful)
- Aplicăm DFS pe graful transpus, în ordinea inversă a sortării topologice
- Fiecare apel DFS va descoperi o componentă tare conexă
- Complexitate: O(n + m), unde n este numărul de noduri și m este numărul de arce/muchii.

```python
# G - graful de intrare, reprezentat ca un dicționar de liste de adiacență (echivalent in c++ cu vectori de vectori)
def kosaraju(G):
    visited = set()   # Mulțime pentru a urmări nodurile vizitate
    stack = []        # Stivă pentru a păstra ordinea nodurilor în timpul DFS

    def dfs_first(u):
        visited.add(u)
        for v in G[u]:
            if v not in visited:
                dfs_first(v)
        # Adăugăm nodul la stivă după ce am terminat de vizitat toți vecinii
        stack.append(u)

    for u in G:
        if u not in visited:
            dfs_first(u)

    # Transpunem graful (inversăm direcția arcelor)
    transposed = {u: [] for u in G}
    for u in G:
        for v in G[u]:
            transposed[v].append(u)

    visited.clear()
    components = []

    def dfs_second(u, component):
        visited.add(u)
        component.append(u) # Adăugăm nodul la componenta curentă
        for v in transposed[u]:
            if v not in visited:
                dfs_second(v, component)

    while stack:
        u = stack.pop()
        if u not in visited:
            component = []
            dfs_second(u, component)
            components.append(component)

    return components
```

## Puncte de articulație

- Punctele de articulatie sunt noduri care, daca sunt eliminate, alaturi de toate arcele incidente, cresc numarul de componente conexe ale grafului.
- G = graf **neorientat**

### Idee algoritm
- Folosim DFS pentru a parcurge graful, tinem minte pentru fiecare nod:
    - `d[u]` - timpul de descoperire al nodului u
    - `low[u]` - cel mai mic timp de descoperire al unui nod accesibil din subarborele lui u
        - `low[u] = min(d[u], low[v], d[w])`, unde `v` este un vecin al lui `u` si `(u, w)` este o muchie inapoi.
- Calculam numarul de subarbori dominati ai lui `u`:
    - Daca `u` este radacina, numarul de subarbori este numarul de copii ai lui `u`.
    - Daca `u` nu este radacina, numarul de subarbori este numarul de copii ai lui `u` care au un timp de descoperire mai mare decat `d[u]`.
- Daca numărul de subarbori este mai mare sau egal cu 2, atunci `u` este un punct de articulație.

### Algoritmul lui Tarjan

```python
def find_articulation_points(G):
    visited = set()
    d = {}
    low = {}
    parent = {}
    articulation_points = set()
    time = 0

    def dfs(u):
        visited.add(u)
        d[u] = low[u] = time
        time += 1
        children = 0

        for v in G[u]:
            if v not in visited:  # Nodul v nu a fost vizitat
                parent[v] = u     # Setăm părintele lui v ca u
                children += 1     # Incrementăm numărul de copii al lui u
                dfs(v)            # Apelăm DFS recursiv pentru v

                # Actualizăm low[u]
                low[u] = min(low[u], low[v])

                # Condiția pentru punct de articulație
                if parent.get(u) is None and children > 1:
                    # Dacă u este rădăcina și are mai mult de un copil
                    articulation_points.add(u)
                elif parent.get(u) is not None and low[v] >= d[u]:
                    # Dacă u nu este rădăcina și low[v] >= d[u], atunci u este un punct de articulație
                    articulation_points.add(u)

            elif v != parent.get(u):
                # Nodul v este un vecin deja vizitat și nu este părintele lui u
                low[u] = min(low[u], d[v])

    for u in G:
        if u not in visited:
            dfs(u)

    return articulation_points
```

Q: Cum putem calcula numarul de componente tare conexe folosind Tarjan?
A: Algoritmul lui Tarjan pentru găsirea componentelor tare conexe folosește o abordare similară cu cea a punctelor de articulație, dar se concentrează pe identificarea componentelor tare conexe prin utilizarea unei stive pentru a urmări nodurile descoperite și a verifica dacă un nod este rădăcina unei componente tare conexe.

```python
def tarjan_scc(G):
    visited = set()
    stack = []
    low = {}
    d = {}
    on_stack = set()
    sccs = []
    time = 0

    def dfs(u):
        visited.add(u)
        low[u] = d[u] = time
        time += 1
        stack.append(u)
        on_stack.add(u)

        for v in G[u]:
            if v not in visited:  # Nodul v nu a fost vizitat
                dfs(v)
                low[u] = min(low[u], low[v])
            elif v in on_stack:  # Nodul v este pe stivă, deci parte din SCC curent
                low[u] = min(low[u], d[v])

        # Dacă u este rădăcina unei SCC
        if low[u] == d[u]:
            current_scc = []
            while True:
                v = stack.pop()
                on_stack.remove(v)
                current_scc.append(v)
                if v == u:
                    break
            sccs.append(current_scc)

    for u in G:
        if u not in visited:
            dfs(u)

    return sccs
```

## Punți

- Puntile sunt muchii care, daca sunt eliminate, cresc numarul de componente conexe ale grafului.
- G = graf **neorientat**

```python
def find_bridges(G):
    visited = set()
    d = {}
    low = {}
    bridges = []
    time = 0

    def dfs(u, parent=None):
        visited.add(u)
        d[u] = low[u] = time
        time += 1

        for v in G[u]:
            if v not in visited:  # Nodul v nu a fost vizitat
                dfs(v, u)
                low[u] = min(low[u], low[v])

                # Condiția pentru punte
                if low[v] > d[u]:
                    bridges.append((u, v))

            elif v != parent:  # Nodul v este un vecin deja vizitat și nu este părintele lui u
                low[u] = min(low[u], d[v])

    for u in G:
        if u not in visited:
            dfs(u)

    return bridges
```

## Drumuri de cost minim (cu sursa unică)

- BFS nu poate fi aplicat
- Drumuri punct – multipunct
    - Fie un nod s ∊ V, să se găsească un drum de cost minim de la s la oricare u ∊ V
    - Aplicam Dijkstra sau Bellman-Ford (1)
- Drumuri multipunct – punct
    - Fie un nod e ∊ V, să se găsească un drum de cost minim de la oricare u ∊ V la e
    - transpose(G) și apoi (1)
- Drumuri punct – punct:
    - Fie două noduri u și v ∊ V, să se găsească un drum u..v de cost minim
    - Abordarea de la (1)
- Drumuri multipunct – multipunct:
    - oricare u, v ∊ V, să se găsească un drum u..v de cost minim.
    - Aplicam Floyd-Warshall (2)
- Drumuri de cost maxim?
    - DAG => sortare topologică + DP
    - BF pe costuri negative (c -> -c)


- Algoritmii sunt conceputi pentru grafuri orientate:
    - Dijkstra (Greedy)
    - Bellman-Ford (DP)

### Dijkstra

- Foloseste o coada de prioritati in care sunt adaugate nodurile in functie de distanta minima de la sursa.
- Utilizat pentru grafuri cu ponderi pozitive.

```python
def Relaxare(u, v):
    if dist[v] > dist[u] + cost(u, v):
        dist[v] = dist[u] + cost(u, v)
        parent[v] = u

def Dijkstra(source, G):
    V = len(G)

    dist = {u: float('inf') for u in G}
    parent = {u: None for u in G}

    dist[source] = 0
    queue = [(0, source)]  # (cost, nod)
    while queue:
        current_dist, u = heapq.heappop(queue) # Extraem nodul cu distanta minima

        for v in G[u]:
            Relaxare(u, v)  # Relaxăm muchia (u, v)

            if dist[v] < float('inf'):
                heapq.heappush(queue, (dist[v], v))  # Adăugăm nodul în coada de priorități

    return dist, parent
```

- Complexitate daca folosim coada de priorități (min-heap): O((n + m) * log n), unde n este numărul de noduri și m este numărul de arce/muchii.
- Daca graful are multe arce, putem folosi vectori, reducând complexitatea la O(n^2).
- Daca graful are mult mai putine arce decat noduri, putem folosi un heap binar pentru a reduce complexitatea la O(m * log n).
- Daca folosim un heap Fibonacci, complexitatea devine O(n log n + m).
- **Complexitate generala**: *O(n * (T(findMin) + T(removeMin)) + m * T(updateKey))*

#### Heap binar

- Structura de date de tip arbore binar, cu 2 constrangeri:
    - Fiecare nivel este complet umplut, cu excepția ultimului nivel.
    - Ultimul nivel este umplut de la stânga la dreapta.
- Heap binar minimizant:
    - Rădăcina are valoarea minimă.
    - Pentru fiecare nod, valoarea sa este mai mică sau egală cu valorile copiilor săi.

#### Heap Fibonacci

- Colectie de arbori, fiecare fiind un heap minimizant.
- Nu este binar, nici neapărat complet.

#### Algoritmul lui Dial (costuri < n)

- Se folosesc n * w bin-uri, unde w este valoarea maximă a costurilor.
- Daca distanta(src, u) este egala cu k, atunci u este adaugat in bin-ul k.
- src este adaugat in bin-ul 0.
- Parcurgem bin-urile pana cand gasim un bin nenul
    - Extragem nodurile u din bin
    - Pentru fiecare nod v din vecinii lui u, il adaugam in bin-ul c = bin[u] + cost(u, v)
    - In caz ca gasim un nod v care e deja prezent in alt bin, il mutam in bin-ul cu cost minim.
- Complexitate: O(n*w + m)

#### Aplicații Dijkstra

- OSPF (Open Shortest Path First) - protocol de rutare pentru rețele IP.
- Identificarea celui mai scurt ciclu care trece prin nodul v:
    - ```python
        def shortest_cycle(G, v):
            Dijkstra(v, G) # Calculăm distanțele de la v
            min_cycle = float('inf')
            for u in len(G):
                for w in G[u]:
                    if w == v and min_cycle < dist[u] + cost(u, v):
                        min_cycle = dist[u] + cost(u, v)
            return min_cycle if min_cycle != float('inf') else -1
- Ciclul de cost minim: `shortest_cycle(G, v)` pe fiecare nod v din graf.

### Bellman-Ford

- Crapa daca graful are ciclu de cost negativ.

```python
def BellmanFord((V, E), source):
    dist = {u: float('inf') for u in V}
    parent = {u: None for u in V}

    dist[source] = 0

    for _ in range(len(V) - 1):
        for u, v in E:
            Relaxare(u, v)

    for u, v in E:
        if dist[v] > dist[u] + cost(u, v):
            raise ValueError("Graful are ciclu de cost negativ")
```

- Complexitate: O(n * m)
- Optimizari (C7, slide 55)

#### Optimizare Bellman-Ford (1)

```python
def BF1(G, s):
    dist = {u: float('inf') for u in G}
    parent = {u: None for u in G}

    dist[s] = 0

    for _ in range(len(G) - 1):
        if !modified(dist):
            break  # Nu s-au făcut modificări, deci nu mai este nevoie de iterații suplimentare
        for u in V:
            if modified(dist[u]):
                for v in G[u]:
                    Relaxare(u, v)

    for u, v in E:
        if dist[v] > dist[u] + cost(u, v):
            raise ValueError("Graful are ciclu de cost negativ")
```

####  Optimizare Bellman-Ford (2)

```python
def RelaxareOpt(u, v):
    if (d[v] > d[u] + cost(u, v)):
        d[v] = d[u] + cost(u, v)
        p[v] = u
        if !inQueue(v):
            queue.append(v)  # Adăugăm v în coada de noduri de relaxat
            inQueue[v] = True  # Marcăm v ca fiind în coadă

def BellmanFordOpt(G, s):
    dist = {u: float('inf') for u in G}
    parent = {u: None for u in G}
    inQueue = {u: False for u in G}  # Marcăm dacă un nod este în coada de relaxare

    queue = []

    dist[s] = 0
    queue.append(s)
    inQueue[s] = True

    while queue:
        u = queue.pop(0)
        inQueue[u] = False  # Scoatem u din coada

        for v in G[u]:
            RelaxareOpt(u, v)

    # Detectarea ciclurilor de cost negativ este dificila (**WHY??**)

    return dist, parent
```

## Drumuri de cost minim (multipunct - multipunct)

- Calculam distantele minime intre toate nodurile din graf.
- Exemplu clasic de programare dinamica.

### Floyd-Warshall / Roy-Floyd

- La pasul k, se calculeaza distanta minima intre u si v, folosind cel mai bun cost u..k si k..v calculat anterior.
- Graful nu trebuie sa contina cicluri de cost negativ.

#### Teoremă

- Considerand d0(u, v) = cost(u, v) pentru toate u, v ∈ V
- Considerand dk(u, v) = min(dk-1(u, v), dk-1(u, k) + dk-1(k, v)) pentru toate u, v ∈ V
- Atunci, dn(u, v) = distanta minima intre u si v in graful G = d(u, v).

- Complexitate temporala: O(n^3), unde n este numărul de noduri din graf
- Complexitate spațială: O(n^2) pentru matricea de distanțe și matricea de părinți

```python
def FloydWarshall(G):
    n = len(G)

    for i in range(n):
        for j in range(n):
            d[i][j] = cost(i, j)  # Inițializăm distanțele cu costurile directe
            if cost(i, j) == float('inf'):
                p[i][j] = None  # Nu există drum direct între i și j
            else:
                p[i][j] = i  # Părintele lui j este i

    for k in range(1, n + 1):
        for i in range(n):
            for j in range(n):
                if d[i][j] > d[i][k] + d[k][j]:
                    d[i][j] = d[i][k] + d[k][j]  # Actualizăm distanța minimă
                    p[i][j] = p[k][j]  # Actualizăm părintele lui j
                else:
                    d[i][j] = d[i][j]  # Păstrăm distanța anterioară
                    p[i][j] = p[i][j]  # Păstrăm părintele anterior

    return d, p  # Returnăm matricea de distanțe și matricea de părinți
```

#### Inchidere tranzitivă

- Se poate calcula folosind Floyd-Warshall, unde `d[i][j]` devine `True` dacă există un drum de la `i` la `j`, și `False` în caz contrar.

```python
def transitive_closure(G):
    n = len(G)
    closure = [[False] * n for _ in range(n)]

    for i in range(n):
        closure[i][i] = True  # Fiecare nod este accesibil de la sine

    for u in G:
        for v in G[u]:
            closure[u][v] = True  # Există un drum direct de la u la v

    for k in range(n):
        for i in range(n):
            for j in range(n):
                closure[i][j] = closure[i][j] or (closure[i][k] and closure[k][j])

    return closure
```

### Johnson

- Distante minime in grafuri **rare**
- Liste de adiacență
- Bazat (pe Bellman-Ford + Dijkstra)
- Complexitate: O(n*m*log(n))

- Daca graful are doar costuri pozitive, aplicam Dijkstra pe fiecare nod.

```python
def Johnson(G):
    # Pasul 1: Adăugăm un nod sursă fictivă s
    s = 's'
    G[s] = []
    for u in G:
        G[s].append(u)  # Adăugăm arce de la s la toate celelalte noduri

    # Pasul 2: Aplicăm Bellman-Ford pentru a calcula h(u) pentru fiecare nod u
    h = BellmanFord(G, s)

    # Pasul 3: Recalculăm costurile pentru a face toate ponderile pozitive
    for u in G:
        for v in G[u]:
            cost[u][v] += h[u] - h[v]

    # Pasul 4: Aplicăm Dijkstra pe fiecare nod
    dist = {}
    for u in G:
        dist[u] = Dijkstra(u, G)

    # Pasul 5: Recalculăm distanțele originale folosind h(u)
    for u in G:
        for v in G[u]:
            dist[u][v] += h[v] - h[u]

    return dist
```

### Aplicații

- Accesibilitatea intre orase:
    - n orase, d[u][v] = d[v][u] = distanta intre orasele u si v
    - maxDist = distanta maxima accesibila de un autovehicul
    - OUT: orasul cel mai izolat (numar minim de alte orase accesibile avand distanta <= maxDist)
    - Solutie:
        - FW pentru a calcula distantele intre orase
        - Pentru fiecare oras u, numaram orasele v pentru care d[u][v] <= maxDist
        - Selectie minim
- Dependente intre cursuri:
    - n cursuri, prereq[i][j] = cursul i trebuie sa fie finalizat inainte de cursul j (relatia e tranzitivă)
    - Q - vector de perechi (i, j)
    - OUT: Vector cu True (cursul i este necesar pentru a face cursul j) / False
    - Solutie:
        - FW: Inchidere tranzitiva (graf dens)
        - BFS intre toate nodurile (graf rar)

## Arbori minimi de acoperire

- G = graf neorientat, conex, ponderat
- Arb(G) = arbore de acoperire al lui G (graf neorientat, conex, aciclic), unde oricare nod din G este accesibil din oricare alt nod.
- ARB(G) = padurea tuturor arborilor de acoperire ai lui G.
- AMA(G) = arborele minim de acoperire al lui G

### Algoritmul lui Prim

- Se porneste cu un nod si se extinde pe rand cu muchiile cele mai ieftine care au un singur capat in multimea de muchii deja formate.
- Algoritmul este asemanator cu Dijkstra.

```python
def Prim(G, start):
    for u in G:
        d[u] = float('inf')  # Inițializăm distanțele la infinit
        parent[u] = None  # Părintele inițial este None
        inAMA[u] = False  # Nodul nu este în arborele minim de acoperire

    d[start] = 0  # Distanța de la nodul de start la sine este 0

    Q = [(0, start)]  # Coada de priorități pentru noduri (cost, nod)

    while Q:
        current_cost, u = heapq.heappop(Q)  # Extragem nodul cu costul minim

        if inAMA[u]:
            continue  # Dacă nodul este deja în arborele minim de acoperire, îl sărim

        inAMA[u] = True  # Adăugăm nodul în arborele minim de acoperire
        for v in G[u]:
            if d[v] > w[u][v] and not inAMA[v]:
                d[v] = w[u][v]  # Actualizăm distanța pentru nodul v
                parent[v] = u  # Setăm părintele lui v ca u
                heapq.heappush(Q, (d[v], v))  # Adăugăm nodul v în coada de priorități

    return parent  # Returnăm părinții nodurilor din arborele minim de acoperire
```

- Complexitate: asemanatoare cu Dijkstra:
    - O(n^2) - Vectori
    - O(m * log n) - Binary heap
    - O(n * log n + m) - Fibonacci heap

- Vectori pentru grafuri dense, heap pentru grafuri rare.

### Algoritmul lui Kruskal

- Initial toate nodurile formeaza cate o multime si la fiecare pas se reunesc 2 multimi printr-o muchie.
- Muchiile sunt considerate in ordinea costulurilor si sunt adaugate in arbore doar daca nu formeaza un ciclu.

### Multimi disjuncte

## Fluxuri maxime

### Ford-Fulkerson

### Edmonds-Karp

### Aplicații

### (Pompare) Preflux

## TIPS & TRICKS

- *Diametrul arborelui*:
    - BFS din orice nod, salveaza cel mai departat nod (`u`).
    - BFS din acel nod si gaseste cel mai departat nod (`v`). `u..v` - cel mai lung drum intr-un graf aciclic neorientat.
- *Cum putem calcula numarul de drumuri intre două noduri într-un graf orientat aciclic?* **Sortare topologică + DP**:
    - Sortăm topologic graful
    - Initializăm `dp[u] = 0` pentru toate nodurile, cu excepția sursei `dp[source] = 1`
    - Parcurgem nodurile în ordine topologică:
        - Pentru fiecare nod `u`, pentru fiecare vecin `v` al lui `u`, adunăm numărul de drumuri:
        - `dp[v] += dp[u]`
- *Cum identificam cel mai lung drum intr-un graf orientat aciclic?* **Sortare topologică + DP**:
    - Sortăm topologic graful
    - Initializăm `dp[u] = -1` pentru toate nodurile, cu exceptia celor cu in-degree 0, care vor fi `dp[idz] = 0`
    - Parcurgem nodurile în ordine topologică:
        - Pentru fiecare nod `u`, pentru fiecare vecin `v` al lui `u`, actualizăm lungimea drumului:
        - `dp[v] = max(dp[v], dp[u] + 1)`
        - retinem parintii pentru fiecare nod (dp[u] + 1 > dp[v] => p[v] = u)
    - Pentru a gasi cel mai lung drum din nodul x, cu dp[x] = maxim, si urmărim părinții până ajungem la un nod cu in-degree 0.