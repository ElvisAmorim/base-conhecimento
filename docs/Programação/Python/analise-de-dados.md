???+ info "Finalidade"

    Esta pagina é para ferramentas/ecossistemas Python

## Pandas

### Ler aquivo csv

```py
import pandas as pd

df = pd.read_csv("arquivo.csv")
df.head()
```

### Remover duplicadas
```
df.drop_duplicates()
```

### Remover valores vazios
Cuidado: remove a linha inteira
```
df.dropna()
```
