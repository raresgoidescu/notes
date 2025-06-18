# Notițe Curs Grafuri - End

## Cuprins

- [Programare Dinamica](#programare-dinamică)
- [Concepte de Bază](#concepte-de-bază)
    - [Tipuri de Grafuri](#tipuri-de-grafuri)
    - [Notații și Terminologie](#notații-și-terminologie)
- [Parcurgeri Fundamentale](#parcurgeri-fundamentale)
    - [DFS (Depth-First Search)](#dfs-depth-first-search)
    - [BFS (Breadth-First Search)](#bfs-breadth-first-search)
    - [IDDFS (Iterative Deepening DFS)](#iddfs-iterative-deepening-dfs)
- [Sortare Topologică](#sortare-topologică)
    - [Algoritmul lui Kahn](#algoritmul-lui-kahn)
    - [Aplicații (DP pe DAG)](#aplicații-dp-pe-dag)
- [Componente Conexe](#componente-conexe)
    - [Componente Tare Conexe (Grafuri Orientate)](#componente-tare-conexe-grafuri-orientate)
        - [Algoritmul lui Kosaraju](#algoritmul-lui-kosaraju)
        - [Algoritmul lui Tarjan pentru CTC](#algoritmul-lui-tarjan-pentru-ctc)
    - [Puncte de Articulație și Punți (Grafuri Neorientate)](#puncte-de-articulație-și-punți-grafuri-neorientate)
        - [Puncte de Articulație](#puncte-de-articulație)
        - [Punți (Bridges)](#punți-bridges)
- [Drumuri de Cost Minim](#drumuri-de-cost-minim)
    - [Sursă Unică (Single-Source Shortest Path)](#sursă-unică-single-source-shortest-path)
        - [Algoritmul lui Dijkstra](#algoritmul-lui-dijkstra)
        - [Algoritmul Bellman-Ford](#algoritmul-bellman-ford)
    - [Drumuri de cost minim (multipunct - multipunct)](#drumuri-de-cost-minim-multipunct---multipunct)
        - [Algoritmul Floyd-Warshall](#algoritmul-floyd-warshall)
        - [Algoritmul Johnson](#algoritmul-lui-johnson)
- [Arbori Minimi de Acoperire (MST)](#arbori-minimi-de-acoperire-mst)
    - [Algoritmul lui Prim](#algoritmul-lui-prim)
    - [Algoritmul lui Kruskal](#algoritmul-lui-kruskal)
- [Flux Maxim în Rețele](#flux-maxim-în-rețele)
    - [Algoritmul lui Ford-Fulkerson](#metoda-ford-fulkerson)
    - [Algoritmul Edmonds-Karp](#algoritmul-edmonds-karp)
    - [Algoritmul Push-Relabel (Pompare-Preflux)](#algoritmul-push-relabel-pompare-preflux)
- [Tips & Tricks](#tips--tricks)

## Programare Dinamică

**Recurente comune**:
- Urcatul scarilor: In cate feluri se poate urca o scara care are N trepte, daca la fiecare pas se pot face 1, 2 sau 3 pași?
    - `ways(n) = ways(n-1) + ways(n-2) + ways(n-3)`
    - `ways(0) = 1` (o singură modalitate de a nu face nimic)
    - `ways(1) = 1` (o singură modalitate de a urca o treaptă)
    - `ways(2) = 2` (două modalități: 1+1 sau 2)
    - `ways(3) = 4` (patru modalități: 1+1+1, 1+2, 2+1, 3)
- Problema rucsacului: Avem un rucsac cu o capacitate maximă și o listă de obiecte, fiecare cu o greutate și o valoare. Cum putem maximiza valoarea totală a obiectelor din rucsac? Nu se pot împărți obiectele.
    - `dp[i][j]` = valoarea maximă pentru primele `i` obiecte și capacitatea `j`.
    - `dp[i][j] = dp[i-1][j]` (nu luăm obiectul i) sau `dp[i][j] = dp[i-1][j-w[i]] + v[i]` (luăm obiectul i, dacă se încadrează).
- Rod Cutting: Care este cea mai eficientă metodă de a secționa o sfoară de dimensiunea N, pentru a maximiza prețul total al secțiunilor vândute?
    - `dp[0] = 0` (nu putem vinde nimic dintr-o sfoară de lungime 0)
    - `dp[i] = max(v[j] + dp[i-j])` pentru toate `j` posibile, unde `v[j]` este prețul pentru o secțiune de lungime `j`.
- Subsir crescător maxim: Avem un șir de numere și vrem să găsim lungimea celui mai lung subsir crescător.
    - `dp[i]` = lungimea celui mai lung subsir crescător care se termină la poziția `i`.
    - `dp[i] = max(dp[j] + 1)` pentru toate `j < i` unde `arr[j] < arr[i]`.
- Cel mai lung subșir comun: Avem două șiruri și vrem să găsim lungimea celui mai lung subșir comun.
    - `dp[i][j]` = lungimea celui mai lung subșir comun pentru primele `i` caractere din primul șir și primele `j` caractere din al doilea șir.
    - `dp[i][j] = dp[i-1][j-1] + 1` dacă `s1[i-1] == s2[j-1]`, altfel `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`.
- Distanta Levenshtein: Măsoară diferența dintre două șiruri de caractere, numărând numărul minim de operații necesare pentru a transforma un șir în altul (inserare, ștergere, substituție).
    - `dp[i][j]` = distanța Levenshtein între primele `i` caractere din primul șir și primele `j` caractere din al doilea șir.
    - `dp[i][j] = dp[i-1][j] + 1` (ștergere), `dp[i][j-1] + 1` (inserare), sau `dp[i-1][j-1] + cost` (substituție, unde `cost = 0` dacă caracterele sunt egale, altfel `cost = 1`).
- Parantezare optima de matrice: Avem o secvență de matrice și vrem să găsim ordinea optimă de înmulțire pentru a minimiza numărul total de operații.
    - `dp[i][j]` = costul minim pentru a înmulți matricele de la `i` la `j`.
    - `dp[i][j] = min(dp[i][k] + dp[k+1][j] + cost(i, k, j))` pentru toate `k` între `i` și `j-1`, unde `cost(i, k, j)` este costul de înmulțire a matricelor.

## Concepte de Bază

### Tipuri de Grafuri

- **Graf Orientat / Neorientat**: Muchiile au o direcție (arce) sau nu.
- **Graf Ponderat / Neponderat**: Muchiile au asociat un cost/pondere (e.g., distanță, timp) sau nu.
- **Graf Aciclic / Ciclic**: Conține sau nu cicluri.
    - **DAG (Directed Acyclic Graph)**: Un graf orientat și aciclic. Foarte important pentru modelarea dependențelor.
- **Graf Simplu / Multigraf**: Un graf simplu are cel mult o muchie între oricare două noduri. Un multigraf permite multiple muchii între aceleași noduri.
- **Graf Bipartit**: O clasă specială de grafuri ale căror noduri pot fi împărțite în două mulțimi disjuncte, `L` și `R`, astfel încât fiecare muchie conectează un nod din `L` cu un nod din `R`. Nu există muchii între noduri din aceeași mulțime.
- **Arbore**: Un graf neorientat, conex și aciclic.
- **Pădure**: O colecție de arbori (un graf neorientat, aciclic, nu neapărat conex).

### Notații și Terminologie

- **G = (V, E)**: Un graf definit de o mulțime de noduri (vârfuri) `V` și o mulțime de muchii `E`.
- **(u, v) ∈ E**: O muchie (neorientata) sau un arc (orientat) de la nodul `u` la `v`.
- **`adj(u)` sau `succs(u)`**: Mulțimea nodurilor adiacente cu `u` (vecini).
- **`grad(u)`**: Gradul unui nod (numărul de muchii incidente).
    - **`grad_in(u)` / `grad_out(u)`**: Gradul intern/extern într-un graf orientat.
- **Drum**: O secvență de noduri `v_1, v_2, ..., v_k` unde `(v_i, v_{i+1})` este o muchie pentru `i=1..k-1`.
- **`p[u]` (părinte)**: Nodul din care s-a ajuns pentru prima dată în `u` în timpul unei parcurgeri.
- **`c[u]` (culoare)**: Starea unui nod în timpul unei parcurgeri:
    - **Alb**: Nedescoperit.
    - **Gri**: Descoperit, dar vecinii săi nu au fost încă toți explorați (în curs de procesare).
    - **Negru**: Descoperit și finalizat (toți vecinii săi au fost explorați).

## Parcurgeri Fundamentale

### DFS (Depth-First Search)

Parcurgerea în adâncime explorează cât de mult posibil pe o ramură înainte de a se întoarce (backtracking). Folosește o stivă (LIFO), implementată recursiv în mod natural.

**Condiții**: Graf orientat sau neorientat.

**Descriere**:
1. Se alege un nod de start și se marchează ca vizitat (gri).
2. Pentru nodul curent, se alege un vecin nevizitat, se vizitează recursiv.
3. Se repetă pasul 2 până se ajunge la un nod fără vecini nevizitați.
4. Se face backtracking la nodul anterior și se explorează alte ramuri nevizitate.
5. Nodul este marcat ca finalizat (negru) după ce toate ramurile sale au fost explorate.
6. Algoritmul se repetă pentru toate nodurile nevizitate pentru a acoperi întregul graf (toate componentele conexe).

**Aplicații**: Detectarea ciclurilor, sortare topologică, găsirea componentelor tare conexe.

**Tipuri de muchii în DFS**:
- **Muchie de arbore (directă)**: `(u, v)` unde `v` este descoperit prima dată din `u`.
- **Muchie de întoarcere (back-edge)**: `(u, v)` unde `v` este un strămoș al lui `u` în arborele DFS (indică un ciclu în grafuri orientate).
- **Muchie de înaintare (forward-edge)**: `(u, v)` unde `v` este un descendent al lui `u`, dar nu un copil direct.
- **Muchie transversală (cross-edge)**: `(u, v)` unde `u` și `v` nu au o relație de strămoș-descendent.

**Complexitate**: `O(|V| + |E|)`

```python
# G: graf reprezentat prin liste de adiacență
# V: mulțimea nodurilor
def DFS(G, V):
    parent = {u: None for u in V}
    color = {u: 'white' for u in V}
    discovery_time = {}
    finish_time = {}
    time = 0

    def DFS_visit(u):
        time += 1
        discovery_time[u] = time
        color[u] = 'gray'

        for v in G[u]:  # Explorează vecinii
            if color[v] == 'white':
                parent[v] = u
                DFS_visit(v)
            # elif color[v] == 'gray':
                # S-a găsit o muchie de întoarcere -> ciclu

        color[u] = 'black'
        time += 1
        finish_time[u] = time

    # Se parcurg toate nodurile pentru a acoperi și grafurile neconexe
    for u in V:
        if color[u] == 'white':
            DFS_visit(u)

    return parent, discovery_time, finish_time
```

### BFS (Breadth-First Search)

Parcurgerea în lățime explorează toți vecinii unui nod înainte de a trece la nivelul următor. Folosește o coadă (FIFO).

**Condiții**: Graf orientat sau neorientat. Ideal pentru **grafuri neponderate**.

**Descriere**:
- Se adaugă un nod sursă `s` într-o coadă și se marchează ca vizitat (gri).
- Cât timp coada nu este goală:
    - Se extrage un nod `u` din coadă.
    - Pentru fiecare vecin `v` al lui `u` care nu a fost vizitat:
        - Se marchează `v` ca vizitat (gri) și i se setează părintele și distanța.
        - Se adaugă `v` în coadă.
    - Se marchează `u` ca finalizat (negru).

**Aplicații**:
- Găsirea celui mai scurt drum (în număr de muchii) de la o sursă la toate celelalte noduri.
- Verificarea dacă un graf este bipartit:
    - Se colorează nodurile în două culori, alternand culorile pentru fiecare nivel de adâncime. Dacă se întâlnește un nod deja colorat cu aceeași culoare ca și cel curent, graf nu este bipartit.

**Complexitate**: `O(|V| + |E|)`

```python
# G: graf reprezentat prin liste de adiacență
# V: mulțimea nodurilor
# s: nodul sursă
def BFS(G, V, s):
    parent = {u: None for u in V}
    color = {u: 'white' for u in V}
    distance = {u: float('inf') for u in V}

    queue = []

    color[s] = 'gray'
    distance[s] = 0
    queue.append(s)

    while queue:
        u = queue.pop(0)  # Extrage primul element din coadă
        for v in G[u]:
            if color[v] == 'white':
                color[v] = 'gray'
                parent[v] = u
                distance[v] = distance[u] + 1
                queue.append(v)
        color[u] = 'black'

    return parent, distance
```

### IDDFS (Iterative Deepening DFS)

Combină avantajele DFS (memorie redusă) cu cele ale BFS (găsește cel mai scurt drum).

**Condiții**: Graf orientat sau neorientat.

**Descriere**:
Rulează DFS în mod repetat cu o limită de adâncime care crește la fiecare iterație (`limit = 0, 1, 2, ...`). Se oprește când găsește soluția sau atinge adâncimea maximă. Este util în grafuri cu ramificare mare sau adâncime infinită, unde un DFS simplu ar putea să nu se termine.

**Complexitate**: `O(|V| + |E|)`

## Sortare Topologică

O ordonare liniară a nodurilor unui graf astfel încât pentru fiecare arc `(u, v)`, nodul `u` apare înaintea nodului `v` în ordonare.

**Condiții**: **Graf Orientat Aciclic (DAG)**. Dacă graful conține un ciclu, sortarea topologică nu este posibilă.

### Algoritmul lui Kahn

- [YouTube](https://www.youtube.com/watch?v=cIBFEhD77b4)

**Descriere**:
- Calculează gradul intern (`in-degree`) pentru fiecare nod.
- Adaugă toate nodurile cu grad intern 0 într-o coadă.
- Cât timp coada nu este goală:
    - Extrage un nod `u` din coadă și adaugă-l la lista sortată topologic.
    - Pentru fiecare vecin `v` al lui `u`:
        - Decrementează gradul intern al lui `v`.
        - Dacă gradul intern al lui `v` devine 0, adaugă-l în coadă.
- Dacă lista sortată conține mai puține noduri decât `|V|`, graful are un ciclu.

**Complexitate**: `O(|V| + |E|)`

```python
# G: graf reprezentat prin liste de adiacență
# V: mulțimea nodurilor
def kahn_topological_sort(G, V):
    in_degree = {u: 0 for u in V}
    for u in V:
        for v in G[u]:
            in_degree[v] += 1

    queue = [u for u in V if in_degree[u] == 0]
    topo_order = []

    while queue:
        u = queue.pop(0)
        topo_order.append(u)

        for v in G[u]:
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    if len(topo_order) != len(V):
        raise ValueError("Graful conține cel puțin un ciclu.")

    return topo_order
```

### Aplicații (DP pe DAG)

Multe probleme de programare dinamică pot fi rezolvate pe un DAG prin procesarea nodurilor în ordine topologică.

- **Numărul de drumuri între două noduri `s` și `t`**:
    1. Sortează topologic graful.
    2. Inițializează `dp[u] = 0` pentru toate nodurile, `dp[s] = 1`.
    3. Parcurge nodurile `u` în ordine topologică:
        - Pentru fiecare vecin `v` al lui `u`, `dp[v] += dp[u]`.
    4. Rezultatul este `dp[t]`.

- **Cel mai lung drum într-un DAG**:
    1. Sortează topologic graful.
    2. Initializăm `dp[u] = -1` pentru toate nodurile, cu exceptia celor cu in-degree 0, care vor fi `dp[idz] = 0`
    3. Parcurgem nodurile în ordine topologică:
        - Pentru fiecare nod `u`, pentru fiecare vecin `v` al lui `u`, actualizăm lungimea drumului:
        - `dp[v] = max(dp[v], dp[u] + 1)`
        - retinem parintii pentru fiecare nod (dp[u] + 1 > dp[v] => p[v] = u)
    - Pentru a gasi cel mai lung drum din nodul x, cu dp[x] = maxim, si urmărim părinții până ajungem la un nod cu in-degree 0.

## Componente Conexe

### Componente Tare Conexe (Grafuri Orientate)

O componentă tare conexă (CTC) este un subgraf maximal în care pentru oricare două noduri `u` și `v` există un drum de la `u` la `v` și un drum de la `v` la `u`.

**Condiții**: **Graf orientat**.

#### Algoritmul lui Kosaraju

**Descriere**:
1. Rulează un DFS pe graful original `G` pentru a calcula timpii de finalizare `f[u]` pentru fiecare nod.
2. Calculează graful transpus `G_T` (inversând toate arcele din `G`).
3. Rulează un DFS pe `G_T`, procesând nodurile în ordinea descrescătoare a timpilor de finalizare calculați la pasul 1.
4. Fiecare arbore generat în pădurea DFS de la pasul 3 reprezintă o componentă tare conexă.

**Complexitate**: `O(|V| + |E|)` (două parcurgeri DFS).

```python
# G: graf, G_T: graf transpus
def kosaraju(G, V):
    # Pas 1: DFS pe G pentru a obține ordinea de finalizare
    visited = set()
    finish_order_stack = []
    def dfs1(u):
        visited.add(u)
        for v in G[u]:
            if v not in visited:
                dfs1(v)
        finish_order_stack.append(u)

    for u in V:
        if u not in visited:
            dfs1(u)

    # Pas 2: Calculează G_T
    G_T = {u: [] for u in V}
    for u in V:
        for v in G[u]:
            G_T[v].append(u)

    # Pas 3: DFS pe G_T în ordinea dată de stivă
    visited.clear()
    scc_list = []
    def dfs2(u, current_scc):
        visited.add(u)
        current_scc.append(u)
        for v in G_T[u]:
            if v not in visited:
                dfs2(v, current_scc)

    while finish_order_stack:
        u = finish_order_stack.pop()
        if u not in visited:
            current_scc = []
            dfs2(u, current_scc)
            scc_list.append(current_scc)

    return scc_list
```

#### Algoritmul lui Tarjan pentru CTC

**Descriere**:
Folosește o singură parcurgere DFS. Menține o stivă cu nodurile vizitate și calculează pentru fiecare nod `u` o valoare `low_link[u]`, care este cel mai mic timp de descoperire accesibil din `u` (inclusiv prin el însuși) printr-o muchie de întoarcere. Un nod `u` este rădăcina unei CTC dacă `discovery_time[u] == low_link[u]`. Când o astfel de rădăcină este găsită, toate nodurile de pe stivă până la `u` (inclusiv) formează o CTC.

**Complexitate**: `O(|V| + |E|)`

### Puncte de Articulație și Punți (Grafuri Neorientate)

**Condiții**: **Graf neorientat**.

#### Puncte de Articulație

Un nod este un punct de articulație dacă eliminarea sa (și a muchiilor incidente) crește numărul de componente conexe ale grafului.

**Algoritm (bazat pe Tarjan)**:
Se folosește DFS. Pentru fiecare nod `u`, se calculează `discovery_time[u]` și `low_link[u]` (cel mai mic timp de descoperire accesibil din `u` sau descendenții săi). Un nod `u` este punct de articulație dacă:
1. `u` este rădăcina arborelui DFS și are mai mult de un copil.
2. `u` nu este rădăcină și are un copil `v` pentru care `low_link[v] >= discovery_time[u]`.

**Note**: `low[u] = min(d[u], low[v], d[w])`, unde `v` este un vecin al lui `u` si `(u, w)` este un back-edge.

**Complexitate**: `O(|V| + |E|)`

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

#### Punți (Bridges)

O muchie este o punte dacă eliminarea sa crește numărul de componente conexe.

**Algoritm (bazat pe Tarjan)**:
Similar cu punctele de articulație. O muchie `(u, v)` (unde `v` este copilul lui `u` în arborele DFS) este o punte dacă `low_link[v] > discovery_time[u]`.

**Complexitate**: `O(|V| + |E|)`

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

## Drumuri de Cost Minim

### Sursă Unică (Single-Source Shortest Path)

Găsirea drumului de cost minim de la un nod sursă `s` la toate celelalte noduri din graf.

#### Algoritmul lui Dijkstra

**Condiții**: Graf orientat sau neorientat. **Toate ponderile muchiilor trebuie să fie non-negative.**

**Descriere (Greedy)**:
1. Inițializează distanța de la sursă `s` la toate celelalte noduri ca infinit (`dist[u] = inf`), iar `dist[s] = 0`.
2. Folosește o coadă de priorități pentru a stoca nodurile nevizitate, ordonate crescător după distanță. Inițial, coada conține doar `(0, s)`.
3. Cât timp coada de priorități nu este goală:
    - Extrage nodul `u` cu cea mai mică distanță.
    - Pentru fiecare vecin `v` al lui `u`, se aplică operația de **relaxare**:
        - `if dist[u] + cost(u, v) < dist[v]:`
            - `dist[v] = dist[u] + cost(u, v)`
            - Se actualizează `v` în coada de priorități cu noua distanță.

**Complexitate**:
- `O(|V|^2)` cu o listă simplă (bun pentru grafuri dense).
- `O((|V| + |E|) log |V|)` cu heap binar (standard, bun pentru grafuri rare).
- `O(|E| + |V| log |V|)` cu heap Fibonacci (teoretic mai bun, dar complex în practică).

**Complexitate generala**:
- `O(n * (T(findMin) + T(removeMin)) + m * T(updateKey))`

```python
import heapq # Modul pentru cozi de priorități (min-heap)

# G: graf ponderat, s: nodul sursă
def dijkstra(G, V, s):
    dist = {u: float('inf') for u in V}
    parent = {u: None for u in V}
    dist[s] = 0

    pq = [(0, s)]  # Coadă de priorități: (distanță, nod)

    while pq:
        d, u = heapq.heappop(pq)

        # Optimizare: dacă am găsit deja un drum mai scurt, ignorăm
        if d > dist[u]:
            continue

        for v, weight in G[u]: # G[u] este o listă de perechi (vecin, cost)
            # Operația de relaxare
            if dist[u] + weight < dist[v]:
                dist[v] = dist[u] + weight
                parent[v] = u
                heapq.heappush(pq, (dist[v], v))

    return dist, parent
```

##### Heap binar

- Structura de date de tip arbore binar, cu 2 constrangeri:
    - Fiecare nivel este complet umplut, cu excepția ultimului nivel.
    - Ultimul nivel este umplut de la stânga la dreapta.
- Heap binar minimizant:
    - Rădăcina are valoarea minimă.
    - Pentru fiecare nod, valoarea sa este mai mică sau egală cu valorile copiilor săi.

##### Heap Fibonacci

- Colectie de arbori, fiecare fiind un heap minimizant.
- Nu este binar, nici neapărat complet.

##### Algoritmul lui Dial (costuri << n)

- Se folosesc `n * w` bin-uri, unde `w` este valoarea maximă a costurilor.
- Daca `dist(src, u)` este egala cu `k`, atunci `u` este adaugat in bin-ul `k`.
- `src` este adaugat in bin-ul `0`.
- Parcurgem bin-urile pana cand gasim un bin nenul
    - Extragem nodurile `u` din bin
    - Pentru fiecare nod `v` din vecinii lui `u`, il adaugam in bin-ul `c = bin[u] + cost(u, v)`
    - In caz ca gasim un nod `v` care e deja prezent in alt bin, il mutam in bin-ul cu cost minim.
- Complexitate: `O(n*w + m)`

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

#### Algoritmul Bellman-Ford

**Condiții**: Graf orientat sau neorientat. **Permite ponderi negative**, dar **nu poate avea cicluri de cost negativ**.

**Descriere (Programare Dinamică)**:
1. Inițializează `dist[u] = inf` pentru toți `u`, și `dist[s] = 0`.
2. Repetă de `|V| - 1` ori:
    - Pentru fiecare muchie `(u, v)` din graf, aplică operația de relaxare.
3. (Opțional) Detectarea ciclurilor de cost negativ:
    - După cele `|V| - 1` iterații, mai parcurge o dată toate muchiile. Dacă o distanță poate fi încă îmbunătățită, atunci există un ciclu de cost negativ accesibil din sursă.

**Complexitate**: `O(|V| * |E|)`

```python
# G_edges: lista de muchii (u, v, cost)
def bellman_ford(G_edges, V, s):
    dist = {u: float('inf') for u in V}
    parent = {u: None for u in V}
    dist[s] = 0

    # Pasul 2: Relaxează muchiile de |V| - 1 ori
    for _ in range(len(V) - 1):
        for u, v, weight in G_edges:
            if dist[u] != float('inf') and dist[u] + weight < dist[v]:
                dist[v] = dist[u] + weight
                parent[v] = u

    # Pasul 3: Detectează cicluri de cost negativ
    for u, v, weight in G_edges:
        if dist[u] != float('inf') and dist[u] + weight < dist[v]:
            raise ValueError("Graful conține un ciclu de cost negativ.")

    return dist, parent
```

##### Optimizare Bellman-Ford (1)

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

#####  Optimizare Bellman-Ford (2)

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

### Drumuri de cost minim (multipunct - multipunct)

Găsirea drumului de cost minim între oricare două noduri din graf.

#### Algoritmul Floyd-Warshall

**Condiții**: Graf orientat sau neorientat, ponderat. Permite ponderi negative, dar **fără cicluri de cost negativ**.

**Descriere (Programare Dinamică)**:
- Utilizează o matrice `dist[i][j]` pentru a stoca distanța minimă de la `i` la `j`.
- Inițial, `dist[i][j]` este costul direct al muchiei `(i, j)` sau infinit dacă nu există.
- Iterează prin toate nodurile `k` și le consideră ca posibile noduri intermediare în drumuri:
    - `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`

**Complexitate**: `O(|V|^3)`

**Teoremă**:
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

**Inchidere tranzitivă**:
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

#### Algoritmul lui Johnson

**Condiții**: Graf orientat sau neorientat, ponderat, **fără cicluri de cost negativ**. **Eficient pentru grafuri rare**.

**Descriere**:
1. Creează un nou graf `G'` adăugând un nod sursă fictiv `s` și arce de la `s` la toate celelalte noduri cu pondere 0.
2. Rulează Bellman-Ford pe `G'` din sursa `s` pentru a calcula `h(u)`, distanța de la `s` la fiecare nod `u`.
3. Reponderează fiecare muchie `(u, v)` din graful original: `w_nou(u, v) = w_vechi(u, v) + h(u) - h(v)`. Noile ponderi vor fi non-negative.
4. Rulează Dijkstra din fiecare nod pe graful reponderat pentru a găsi toate drumurile minime.
5. Convertește distanțele înapoi la ponderile originale.

**Complexitate**: `O(|V| * |E| + |V|^2 * log|V|)` (cu Bellman-Ford și Dijkstra pe heap).

```python
def johnson(G):
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

**Aplicații**:
- **Accesibilitatea intre orase**:
    - `n` orase, `d[u][v] = d[v][u]` = distanta intre orasele `u` si `v`
    - `maxDist` = distanta maxima accesibila de un autovehicul
    - OUT: orasul cel mai izolat (numar minim de alte orase accesibile avand distanta <= `maxDist`)
    - Solutie:
        - F-W pentru a calcula distantele intre orase
        - Pentru fiecare oras `u`, numaram orasele `v` pentru care `d[u][v] <= maxDist`
        - Selectie minim
- **Dependente intre cursuri**:
    - `n` cursuri, `prereq[i][j]` = cursul `i` trebuie sa fie finalizat inainte de cursul `j` (relatia e tranzitivă)
    - `Q` - vector de perechi `(i, j)`
    - OUT: Vector cu `True` (cursul `i` este necesar pentru a face cursul `j`) / `False`
    - Solutie:
        - F-W: Inchidere tranzitiva (graf dens)
        - BFS intre toate nodurile (graf rar)

## Arbori Minimi de Acoperire (MST)

Un subgraf care conectează toate nodurile, este aciclic (un arbore) și are suma totală a ponderilor muchiilor minimă.

**Condiții**: **Graf neorientat, conex, ponderat**.

### Algoritmul lui Prim

**Descriere (Greedy)**:
- Similar cu Dijkstra.
- Începe de la un nod arbitrar și "crește" arborele adăugând în mod repetat cea mai ieftină muchie care conectează un nod din arborele curent cu un nod din afara acestuia.
- Folosește o coadă de priorități pentru a alege eficient următoarea muchie de adăugat.

**Complexitate**: Identică cu Dijkstra (`O(|E| log |V|)` cu heap binar).

```python
def prim(G, start):
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

### Algoritmul lui Kruskal

**Descriere (Greedy)**:
1. Sortează toate muchiile din graf în ordine crescătoare a ponderilor.
2. Inițializează MST-ul ca o mulțime goală.
3. Parcurge muchiile sortate. Pentru fiecare muchie `(u, v)`:
    - Dacă adăugarea ei în MST nu formează un ciclu, adaug-o.
4. Pentru a verifica eficient ciclurile, se folosește structura de date **Mulțimi Disjuncte (Union-Find)**.

**Complexitate**: `O(|E| log |E|)` sau `O(|E| log |V|)` (dominată de sortare).

```python
def kruskal(G):
    A = []  # Arborele minim de acoperire
    for u in G:
        make_set(u)  # Creăm mulțimi disjuncte pentru fiecare nod
    edges = sorted(G.edges(), key=lambda e: e[2])  # Sortăm muchiile după cost
    for u, v, cost in edges:
        if find_set(u) != find_set(v):  # Verificăm dacă u și v sunt în mulțimi diferite
            union(u, v)  # Unim cele două mulțimi
            A.append((u, v, cost))  # Adăugăm muchia în arborele minim de acoperire
    return A  # Returnăm arborele minim de acoperire
```

**Aplicatii**:
- K-clustering: impartirea nodurilor in k grupuri a.i. obiectele din cadrul unui grup sa fie "apropiate" considerand o "distanta" data.
- Utilizat in clasificare si cautari
- Dandu-se un intreg K, si un grup de obiecte, se cere sa se imparta grupul de obiecte in k grupuri a.i. distanta dintre grupuri sa fie maxima.

### Structura de Date: Mulțimi Disjuncte (Union-Find)

O structură de date care gestionează o partiție a unei mulțimi în submulțimi disjuncte.
- `make_set(x)`: Creează o nouă mulțime care conține doar elementul `x`.
- `find(x)`: Returnează reprezentantul (rădăcina) mulțimii care conține `x`.
- `union(x, y)`: Unește cele două mulțimi care conțin `x` și `y`.

**Optimizări**:
- **Union by rank/size**: La unire, arborele mai mic este atașat la rădăcina celui mai mare.
- **Path compression**: La o operație `find(x)`, toate nodurile de pe drumul de la `x` la rădăcină sunt legate direct de rădăcină.

Cu aceste optimizări, complexitatea amortizată pe operație este aproape constantă, `O(α(|V|))`, unde `α` este funcția Ackermann inversă, care crește extrem de lent.

## Flux Maxim în Rețele

**Condiții**: **Graf orientat, ponderat (capacități)**, cu un nod **sursă (s)** și un nod **destinație (t)**.

**Terminologie**:
- **Rețea de flux**: Un graf orientat unde fiecare arc `(u, v)` are o capacitate `c(u, v) >= 0`.
- **Flux**: O funcție `f(u, v)` care respectă:
    1. **Constrângerea capacității**: `0 <= f(u, v) <= c(u, v)`.
    2. **Conservarea fluxului**: Pentru orice nod `u` (cu excepția `s` și `t`), fluxul total care intră este egal cu fluxul total care iese: `f(u, v) = f(v, u)`.
    3. Pentru X, Y mulțimi de noduri:
        - `f(X, X) = 0` (nu există flux in bucle).
        - `f(X, Y) = -f(Y, X)` (conservarea fluxului).
        - `f(X\Y, Z) = f(X, Z) - f(Y, Z)`
        - `f(X, Y\Z) = f(X, Y) - f(Z, Y)`
- **Rețea reziduală `G_f`**: O rețea care indică cât flux suplimentar poate fi trimis.
- **Drum de ameliorare**: Un drum de la `s` la `t` în rețeaua reziduală.
- **Notatie per arc**: `f/c`

### Metoda Ford-Fulkerson

**Descriere (Greedy)**:
1. Inițializează fluxul la 0.
2. Cât timp există un drum de ameliorare de la `s` la `t` în rețeaua reziduală:
    - Găsește un astfel de drum `p` (de ex., cu DFS sau BFS).
    - Calculează capacitatea reziduală a drumului `p` (minimul capacităților reziduale de pe arcele drumului).
    - Mărește fluxul de-a lungul drumului `p` cu această valoare.
3. Când nu mai există drumuri de ameliorare, fluxul este maxim.

**Complexitate**: `O(F * |E|)`, unde `F` este valoarea fluxului maxim. Poate fi ineficient dacă capacitățile sunt mari.

```python
def ford_fulkerson(G(V, E), s, t):
    # Inițializăm fluxul la 0 pentru toate arcele
    for u, v in E:
        f[u][v] = 0
        f[v][u] = 0

    while drum_de_ameliorare(s, t, p): # O(fmax)
        cap = min(c[u][v] - f[u][v] for u, v in p)  # Capacitatea reziduală minimă pe drumul de ameliorare
        for u, v in p:
            f[u][v] += cap  # Mărim fluxul pe arcul (u, v)
            f[v][u] -= cap  # Mărim fluxul invers pe arcul (v, u)

    f = {(u, v): f[u][v] for u, v in E}  # Reținem fluxul final

    return f
```

**Probleme**:
- Daca apar capacitati rationale, se pot scala la intregi.
- Daca apar capacitati irationale, s-ar putea sa nu se termine niciodata.
- Timpul este nepolinomial fata de marimea intrarii.
- Alte probleme:
    - Se folosesc cai cu capacitate mica
    - Se pun fluxuri pe mai multe arce decat este nevoie
- Imbunatatiri:
    - Se aleg caile reziduale cu capacitate maxima - complexitatea va depinde in continuare de fmax si de valoare capacitatilor.
    - Se aleg caile reziduale cele mai scurte - in acest caz complexitatea nu mai depinde de fmax, ci doar de numarul de arce (e.g.: [Edmonds-Karp](#algoritmul-edmonds-karp): identificarea cailor reziduale minime prin aplicarea unui BFS).

### Algoritmul Edmonds-Karp

O implementare specifică a metodei Ford-Fulkerson unde drumul de ameliorare este **cel mai scurt drum** (în număr de arce) din rețeaua reziduală, găsit cu **BFS**.

**Complexitate**: `O(|V| * |E|^2)`. Nu depinde de valoarea fluxului.

```python
def Edmonds_Karp(G(V, E), s, t):
    for u, v in E:
        f[u][v] = 0
        f[v][u] = 0

    while True:
        path = bfs(G, s, t)  # Găsim o cale de ameliorare folosind BFS

        if not path:  # Dacă nu mai există cale de ameliorare, ieșim
            break

        cap = min(c[u][v] - f[u][v] for u, v in path)  # Capacitatea reziduală minimă pe drumul de ameliorare

        for u, v in path:
            f[u][v] += cap  # Mărim fluxul pe arcul (u, v)
            f[v][u] -= cap  # Mărim fluxul invers pe arcul (v, u)

    # Encoding-ul fluxului final este arbitrar ales (poate fi si de la sursa la toti vecinii săi, de exemplu)
    f = {(u, v): f[u][v] for u, v in E}  # Reținem fluxul final

    return f

def bfs(G, s, t):
    visited = set()
    queue = [s]
    parent = {s: None}  # Păstrăm părinții pentru a reconstrui calea

    while queue:
        u = queue.pop(0)
        if u == t:  # Am ajuns la destinație
            break
        for v in G[u]:  # Ne uităm la toți vecinii lui u
            if v not in visited and c[u][v] > f[u][v]:
                # Dacă v nu a fost vizitat și dacă există capacitate reziduală
                visited.add(v)      # Vizităm nodul v
                parent[v] = u       # Setăm părintele lui v ca u
                queue.append(v)     # Adăugăm v în coada de procesat

    if t not in parent:
        # Dacă nu am găsit o cale până la t
        return None

    # Reconstruim calea de la s la t
    path = []
    v = t

    while v is not None:
        path.append((parent[v], v))
        v = parent[v]

    path.reverse()  # Inversăm calea pentru a fi de la s la t

    return path
```

### Algoritmul Push-Relabel (Pompare-Preflux)

**Descriere**:
- O abordare diferită care simulează curgerea apei.
- Nodurile au o "înălțime" și pot avea un "exces de flux".
- Algoritmul "pompează" flux de la nodurile cu înălțime mai mare la cele cu înălțime mai mică și "ridică" înălțimea nodurilor când fluxul este blocat.
- Se termină când tot excesul de flux (cu excepția sursei și destinației) a fost eliminat.

**Complexitate**: `O(|V|^3)` în implementări simple, dar poate ajunge la `O(|V|^2 * |E|)` sau chiar mai bine.

**Altele**:
- Analogie: simularea curgerii lichidului intr-un sistem de conducte ce leaga noduri aflate la diverse inaltimi.
- Sursa = inaltime = |V|
- Calea cea mai lunga = |V| - 1
- Initial toate nodurile au inaltime 0, cu exceptia sursei care are inaltime |V|.
- Destinatia are inaltime 0, intotdeauna.
- Diferenta fata de flux maxim:
    - nu se mai conserva fluxul (datorita inaltimilor asociate nodurilor).
    - un nod poate fi supraîncărcat cu flux (exces de flux).
- Pentru ca `(u, v)` sa fie arc rezidual, `h(u) < h(v) + 1`.
- Exista un preflux initial in retea obtinut prin incarcarea la capacitate maxima a tuturor conductelor care ies din sursa.
- Este permisa acumularea de exces de flux - `e(u)` - la noduri intermediare (adica in afara de sursa si destinatie).
    - Excesul de flux poate fi stocat intr-un 'rezervor' al nodului.
- Cand un nod `u` are flux disponibil in rezervor si o conducta spre un alt nod `v`, care nu este incarcata complet (are capacitate reziduala pozitiva), se poate pompa fluxul in conducta respectiva.
    - Deci inaltimea nodului `u` trebuie sa fie mai mare decat inaltimea nodului `v` (pentru a permite curgerea).
- [cp-algorithms.com](https://cp-algorithms.com/graph/push-relabel.html)

```python
def push_relabel(G, s, t):
    n = len(G)
    height = {u: 0 for u in G}  # Inălțimea fiecărui nod
    excess = {u: 0 for u in G}  # Excesul de flux la fiecare nod
    flow = {u: {v: 0 for v in G[u]} for u in G}  # Fluxul pe fiecare arc

    height[s] = n  # Sursa are înălțimea maximă
    for v in G[s]:
        flow[s][v] = G[s][v]  # Încărcăm conductele de la sursă la capacitate maximă
        excess[v] += G[s][v]  # Creștem excesul de flux la nodurile adiacente sursei

    def push(u, v):  # Pomparea fluxului de la nodul u la nodul v
        if excess[u] > 0 and height[u] > height[v]:
            # Dacă există exces și înălțimea permite curgerea
            flow[u][v] += excess[u]
            flow[v][u] -= excess[u]
            excess[v] += excess[u]
            excess[u] -= excess[u]

    def relabel(u):  # Ridicare înălțimea nodului u
        min_height = float('inf')
        for v in G[u]:
            if G[u][v] - flow[u][v] > 0:  # Dacă există capacitate reziduală
                min_height = min(min_height, height[v])
        if min_height < float('inf'):
            height[u] = min_height + 1

    while True:
        # Găsim nodurile active (cu exces de flux) excluzând sursa și destinația
        active_nodes = [u for u in G if excess[u] > 0 and u != s and u != t]

        # Dacă nu mai există noduri active, am terminat
        # (adică nu mai există flux de pompat)
        if not active_nodes:
            break

        u = active_nodes[0]
        pushed = False

        for v in G[u]:
            if flow[u][v] < G[u][v]:  # Dacă conducta nu este încărcată complet
                push(u, v)
                pushed = True
                break

        if not pushed:
            relabel(u)

    return flow
```

### Aplicații ale Fluxului Maxim

- **Cuplaj maxim în grafuri bipartite**:
    - Se creează o rețea de flux: se adaugă o sursă `s` și o destinație `t`. Se adaugă arce de la `s` la toate nodurile din partiția `L` și de la toate nodurile din `R` la `t`. Toate arcele (inclusiv cele originale din graf) au capacitate 1. Valoarea fluxului maxim este egală cu dimensiunea cuplajului maxim.
- **Drumuri disjuncte pe muchii**:
    - Numărul maxim de drumuri disjuncte (fără muchii comune) de la `s` la `t` este egal cu fluxul maxim într-o rețea unde fiecare muchie are capacitate 1.
- **Conectivitatea muchiilor**:
    - Numărul minim de muchii ce trebuie eliminate pentru a deconecta `s` de `t` este egal cu fluxul maxim de la `s` la `t` (Teorema Menger).

## Tips & Tricks

- **Diametrul unui arbore** (cel mai lung drum într-un arbore):
    1. Alege un nod arbitrar `x` și găsește cel mai îndepărtat nod de el, `u`, folosind BFS.
    2. Pornește un BFS din `u` și găsește cel mai îndepărtat nod, `v`.
    3. Drumul `u...v` este diametrul arborelui.

- **Numărul de drumuri într-un DAG**: Sortare topologică + DP (vezi secțiunea [Sortare Topologică](#aplicații-dp-pe-dag)).

- **Cel mai lung drum într-un DAG**: Sortare topologică + DP (vezi secțiunea [Sortare Topologică](#aplicații-dp-pe-dag)). Pentru cel mai lung drum în grafuri generale cu cicluri, problema este NP-hard. Dacă se permit ponderi negative, se poate transforma problema într-una de drum minim negândând ponderile, dar doar dacă nu se creează cicluri de cost negativ.
