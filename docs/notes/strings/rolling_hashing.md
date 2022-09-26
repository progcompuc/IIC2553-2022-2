---
title: Rolling Hashing
---

# Rolling Hashing

## Introducción

Rolling Hashing o string hashing es una técnica ocupada para resolver problemas de búsqueda de substring. La idea es _encriptar_ la cadena de caracteres en un número, de tal forma que si dos cadenas son iguales, entonces su encriptación también lo es. Esto nos permite comparar dos cadenas en tiempo constante. De esta manera sólo comparamos el valor del hash, y no cada caracter de la cadena.

## Hasing

Para calcular el hash de una cadena ocupamos _polynomial hasing_. La idea es que cada caracter de la cadena se encripta en un número, y luego se suman todos los números. El problema es que si la cadena es muy larga, el número puede ser muy grande y no cabe en un tipo de dato. Para solucionar esto ocuparemos aritmetica modular.

Sea $S$ el string que queremos encriptar, y $A$ y $B$ dos primos. Entonces, el hash de $S$ es:

$$
\sum_{i=0}^{n-1} S[i] \cdot A^i \mod B
$$

Cada caracter se multiplica por una potencia de $A$ para minizar la probabilidad de colisiones. El modulo $B$ es para evitar que el número sea muy grande y tengamos problemas de overflow. El valor de cada caracter se puede obtener de la tabla ASCII o de alguna otra tabla que se nos ocurra.

### Ejemplo

Supongamos que queremos calcular el hash de la cadena `"PABLO"`. El valor ASCII de cada caracter es:

| P | A | B | L | O |
|---|---|---|---|---|
| 80 | 65 | 66 | 76 | 79 |

Ocupando los primos $A=31$ y $B=10^9 + 7$:

$$
\begin{aligned}
80 \cdot 31^0 + 65 \cdot 31^1 + 66 \cdot 31^2 + 76 \cdot 31^3 + 79 \cdot 31^4 \mod (10^9 + 7) = 75287796
\end{aligned}
$$

## Preprocesamiento

Recordemos que queremos ocupar rolling hashing para resolver problemas de búsqueda de substring. En cuyo caso no nos conviene estar calculando el hash de cada substring a comparar en cada consulta. En vez de eso, ocuparemos un arreglo de hashes, donde cada posición $i$ del arreglo contiene el hash de la cadena desde la posición $i$ hasta el final. De esta manera, si queremos comparar dos substrings, sólo tenemos que manipular los hashes de las posiciones $i$ y $j$.

Llamando $S$ al string que queremos preprocesar, entonces el arreglo $h$ con los hashes que comienzan en cada posición estaría definido por:

$$
\begin{aligned}
h[0] &= S[0] \\ 
h[i] &= S[i - 1] + S[0] \cdot A \\
\end{aligned}
$$

Además de esto, ocuparemos un arreglo $p$ que contiene las potencias de $A$:

$$
\begin{aligned}
p[0] &= 1 \\
p[i] &= p[i - 1] \cdot A \mod B \\
\end{aligned}
$$

Así podemos calcular el hash de un substring $S[i \dots j]$ como:

$$
\begin{aligned}
h[i] - h[j + 1] \cdot p[j - i + 1] \mod B \\
\end{aligned}
$$

### Implementación

#### Código

El siguiente código es la función `preprocess` que calcula los hashes de cada posición del string $S$:

```cpp  
const int MAXN = 1e5 + 5;
const int A = 31;
const int B = 1e9 + 7;

int n;
string s;
int h[MAXN], p[MAXN];

void preprocess() {
    p[0] = 1;
    for (int i = 1; i <= n; i++) {
        p[i] = (p[i - 1] * A) % B;
    }
    h[n] = s[n - 1];
    for (int i = n - 1; i >= 0; i--) {
        h[i] = (h[i + 1] * A + s[i]) % B;
    }
}

int get_hash(int i, int j) {
    return (h[i] - h[j + 1] * p[j - i + 1] + B) % B;
}
```

#### Uso

Supongamos que queremos encontrar el primer substring de $S$ que es igual a `"PABLO"`. Para esto ocuparemos la función `get_hash`:

```cpp
int main() {
    cin >> s;
    n = s.size();
    preprocess();
    string t = "PABLO";
    int m = t.size();
    int h = get_hash(0, m - 1);
    for (int i = 0; i + m - 1 < n; i++) {
        if (get_hash(i, i + m - 1) == h) {
            cout << i << endl;
            break;
        }
    }
}
```