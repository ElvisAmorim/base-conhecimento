# Guia de uso Material MkDocs

## Referenciar outra pagina:
```md
[nomedapagina](/mkdocs/mkdocs-guia/)
```

## Blocos de codigo
``` py
# Function to add two numbers
def add_two_numbers(num1, num2):
    return mum1 + num2
```
Bloco com titulo
``` py title="Codigo com titulo"
# Function to add two numbers
def add_two_numbers(num1, num2):
    return mum1 + num2
```

``` sql
select * from map_produto p
where 1=1
and p.seqproduto = 6044
```

[botao](#caixas){ .md-button .md-button--primary }

---

Sanfona

!!! note "Sanfona"
    teste sanfona descricao completa

??? note "Sanfona"
    teste sanfona descricao completa

---
## Caixas
```
!!! info "Info"
    caixa de informacao
```

!!! info "Info"
    caixa de informacao

```
!!! question "FAQ"
    caixa de questao
```

!!! question "FAQ"
    Caixa de questao

```
!!! warning "Atencao"
    caixa de atencao
```

!!! warning "Atencao"
    Caixa de atencao

```
!!! failure "Error"
    Caixa de erro ou falha
```

!!! failure "Error"
    Caixa de erro ou falha

```
!!! quote "Frase"
    Caixa de frase
```

!!! quote "Frase"
    Caixa de frase


---
## Diagramas

``` mermaid
stateDiagram-v2
  state fork_state <<fork>>
    [*] --> fork_state
    fork_state --> State2
    fork_state --> State3

    state join_state <<join>>
    State2 --> join_state
    State3 --> join_state
    join_state --> State4
    State4 --> [*]
```
``` mermaid
classDiagram
  Person <|-- Student
  Person <|-- Professor
  Person : +String name
  Person : +String phoneNumber
  Person : +String emailAddress
  Person: +purchaseParkingPass()
  Address "1" <-- "0..1" Person:lives at
  class Student{
    +int studentNumber
    +int averageMark
    +isEligibleToEnrol()
    +getSeminarsTaken()
  }
  class Professor{
    +int salary
  }
  class Address{
    +String street
    +String city
    +String state
    +int postalCode
    +String country
    -validate()
    +outputAsLabel()
  }
```

``` mermaid
erDiagram
  CUSTOMER ||--o{ ORDER : places
  ORDER ||--|{ LINE-ITEM : contains
  LINE-ITEM {
    string name
    int pricePerUnit
  }
  CUSTOMER }|..|{ DELIVERY-ADDRESS : uses
```


``` mermaid
graph LR
  A[Start] --> B{Error?};
  B -->|Yes| C[Hmm...];
  C --> D[Debug];
  D --> B;
  B ---->|No| E[Yay!];
```

``` mermaid
sequenceDiagram
  autonumber
  Alice->>John: Hello John, how are you?
  loop Healthcheck
      John->>John: Fight against hypochondria
  end
  Note right of John: Rational thoughts!
  John-->>Alice: Great!
  John->>Bob: How about you?
  Bob-->>John: Jolly good!
```