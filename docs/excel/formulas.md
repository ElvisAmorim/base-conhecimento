# **Formulas de Excel**
Aqui estao as formulas que aprendi a usar e que foram necessarias para alguma tarefa

## Dia da semana
```
=Texto(Dia.da.semana(A1);"dddd")

```

## Descobrir proxima segunda-feira
```
=SE(DIA.DA.SEMANA(A1)>5;A1-(DIA.DA.SEMANA(A1)-2)+7;A1-(DIA.DA.SEMANA(A1)-2)) 

```
## Tratar erro procv
```
=seerro(formula_procv);"" ou uma mensagem)
```

## Separa algarismo ou letra
Selecione a quant de celulas para cada digito.
Na primeira coloque a formula
```
={ext.texto(A1;lin(1:12);1 quantos digitos ficaram em cada celula)}

```

## Juntar duas ou mais celulas em uma unica
```
=concatenar(A1;” “;A2)

```

## Converter horas em minutos
```
=(A1*24)
```

## Contar periodo de dias
```
=DATADIF(data_inicial e data_final;”y”)
```
d= dia, m= mes, y=ano

## Contar entre periodo
* dia
    * data_inicial e data_final
* meses
    * ```
    =(ano(data final)-ano(data inicial))*12+mês(data final)-mês(data inicial))
    ```
* ano
    * ```
    =(ano(data final)-ano(data inicial))
    ```

## Procv duplo
```
={procv(A1&A2;A2:C7&2;3;0)}
```

## Contar caracter
```
=NÚM.CARACT(A1)
```

## Calcular horas trabalhadas
```
07:00  11:46  04:46  R$ 2,50  =(04:46*2,5)*24  =  R$ 11,92

```

## Numero da semana do mes
```
=INT(((A1)-DATA(ANO(A1);MÊS(A1);1))/7)+1&"ª Semana de "&PRI.MAIÚSCULA(TEXTO(A1;"mmmm"))
```

## Numero da semana do ano
```
=NÚMSEMANA(A1;1)& “ª semana do ano”

```

## Tranformar data em dia, mes e ano
ddd - para dia / mmm - para mes / yyy - para ano
```
=(TEXTO(HOJE();"mmm"))
```
```
=(TEXTO(HOJE();"mmmm")) 
```

## Dia da semana
```
=TEXTO(DIA.DA.SEMANA(b2;1);"ddd")
```

## Conserter medidas
```
=converter(A1; “m” para metro;”cm” para centímetro)
```

## Contar parte de valor
```
=CONT.SE(B2:B4;"*"&D2&"*")
```

## Identificar valores duplicados
```
=Se(cont.se(célula fixada:mesma célula sem fixada;mesma célula)>1;”dupli”;”OK”)
```

## Procv parcial
O valor procurado será a célula concatenando com * para procurar parcialmente.
```
=PROCV(A1&”*”;MATRIZ;COLUNA;0)
```

## Verificar ultimo dia do mes
```
=FIMMES(A1;0)
```

## Classificacao
```
=REPT(CARACTUNICODE(10025);A1)
```

## Formula
```

```