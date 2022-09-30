---
title: Trie String
description: Explicación del algoritmo de Trie String y su implementación en C++.
---

# Trie String

Trie string es una forma de guardar un conjunto de strings. La idea es guardar los strings en un árbol, donde cada nodo es un carácter. De forma que si seguimos cada camino desde la raíz hasta alguna de sus hoja podamos reconstruir cada string del conjunto. 

Por ejemplo, si tenemos los strings `tree`, `trie`, `algo`, `assoc`, `all` y `also` podemos guardarlos en un árbol de la siguiente forma:

## Creación del árbol paso por paso
Empezamos con el árbol vacío solo con la raíz.

<center>

``` mermaid
graph TD
    A[root]
```

</center>

Luego anexamos el primer string `tree` carácter por carácter.

<center>

``` mermaid
graph TD
    A[root] --> B((t))
    B --> C((r))
    C --> D((e))
    D --> E((e))
    E --> F[leaf]
    F:::leaf

style A fill:#6b9cd5,stroke:#333
classDef leaf fill:#f96;
```

</center>

Se añade el nodo final `leaf` para indicar que el string termina ahí (asi podemos distinguir strings que son prefijos de otros).

Veamos el siguiente string, `trie`. Revisamos si el nodo `root` tiene un hijo cuyo carácter es igual a `t`. Como si existe, se revisa si el nodo `t` tiene un hijo cuyo carácter es igual a `r`. Si tiene, asi que revisamos si este tiene un hijo igual `i`. No tiene! Asi que añadidos el string restante `ie` como un camino debajo del nodo `r`. 

<center>

``` mermaid
graph TD
    A[root] --> B((t))
    B --> C((r))
    C --> D((e))
    D --> E((e))
    E --> F[leaf]
    F:::leaf
    C --> G((i))
    G --> H((e))
    H --> I[leaf]
    I:::leaf

style A fill:#6b9cd5,stroke:#333
classDef leaf fill:#f96;
```

</center>

Procedemos de forma análoga con los otros strings. Entonces nos quedaría el árbol:

<center>

``` mermaid
graph TD
    A[root] --> B((t))
    B --> C((r))
    C --> D((e))
    D --> E((e))
    E --> F[leaf]
    F:::leaf
    C --> G((i))
    G --> H((e))
    H --> I[leaf]
    I:::leaf
    A[root] --> J((a))
    J --> K((l))
    K --> L((g))
    L --> M((o))
    M --> N[leaf]
    N:::leaf
    K --> O((s))
    O --> P((o))
    P --> Q[leaf]
    Q:::leaf
    R --> S((l))
    S --> T[leaf]
    T:::leaf

style A fill:#6b9cd5,stroke:#333
classDef leaf fill:#f96;
```

</center>

## Implementación en C++

``` cpp
struct Trie {
    vector<vector<int>> g;
    vector<int> count;
    int vocab;
    Trie(int vocab, int maxdepth = 10000) : vocab(vocab) {
        g.reserve(maxdepth);
        g.emplace_back(vocab, -1);
        count.reserve(maxdepth);
        count.push_back(0);
    }
    int move_to(int u, int c) {
        // assert (0 <= c and c < vocab); // debugging
        int& v = g[u][c];
        if (v == -1) {
            v = g.size();
            g.emplace_back(vocab, -1);
            count.push_back(0);
        }
        count[v]++;
        return v;
    }
    void insert(const string& s, char ref = 'a') {  // insert string
        int u = 0; count[0]++; for (char c : s) u = move_to(u, c - ref);
    }    
    void insert(vector<int>& s) { // insert vector<int>
        int u = 0; count[0]++; for (int c : s) u = move_to(u, c);
    }
    int size() { return g.size(); }
};
```