---
encryptcontent: true
password: suasenha123
---


## Conceitos
### Formatar campos
[Link](HTTP://PGDOCPTBR.SOURCEFORGE.NET/PG80/FUNCTIONS-FORMATTING.HTML)


## Consultas

#### Consultar tabelas
``` sql
SELECT * FROM ALL_TAB_COLUMNS 
WHERE TABLE_NAME LIKE '%NomeDaTabela%'
AND OWNER IN 'owner';
```

### Consultar colunas das tabelas
``` sql
SELECT * FROM ALL_TAB_COLUMNS 
WHERE COLUMN_NAME LIKE '%SAZ%'
AND OWNER = 'owner';
```

### Consultar jobs
```sql
SELECT A.JOB, A.NEXT_DATE, A.WHAT, A.* FROM ALL_JOBS A
WHERE 1=1
AND A.NEXT_DATE <= '21-nov-2021';
```

### Contacter / Juntar valores
```sql
SELECT PC.SEQPRODUTO,
       LISTAGG(PC.CODACESSO, ',') WITHIN GROUP(ORDER BY PC.CODACESSO) CODIGOS
  FROM MAP_PRODCODIGO PC
 WHERE 1 = 1
   AND PC.QTDEMBALAGEM = 1
   AND PC.TIPCODIGO = 'E'
   AND PC.SEQPRODUTO = 82
 GROUP BY PC.SEQPRODUTO;
```
Resultado
=== "Antes"

    |SEQPRODUTO|CODACESSO|
    |----------|---------|
    |82|7898591450020|
    |82|7898279792534|

=== "Depois"

    |SEQPRODUTO|CODIGOS|
    |----------|-------|
    |82|7898279792534,7898591450020|



### Conta-se com criterios
```sql
SELECT B.SEQFAMILIA, B.FAMILIA
  FROM MAD_LISTAITEM A, MAP_FAMILIA B, MAD_LISTA D
 WHERE 1 = 1
   AND A.SEQFAMILIA = B.SEQFAMILIA
   AND A.NROLISTA = D.NROLISTA
   AND A.SEQFAMILIA IN ( SELECT SEQFAMILIA
                          FROM MAD_LISTAITEM
                           WHERE 1 = 1
                           AND STATUS = 'I'
                         GROUP BY SEQFAMILIA
                         HAVING COUNT(*) = 3)
  GROUP BY B.SEQFAMILIA, B.FAMILIA;
```

### Valor exclusivo
```sql
SELECT COUNT(A.SEQPESSOA), A.NROSEGMENTO
  FROM MRL_CLIENTESEG A
 WHERE 1 = 1
   AND A.NROSEGMENTO = 7
   AND A.SEQPESSOA NOT IN (
                           SELECT B.SEQPESSOA
                              FROM MRL_CLIENTESEG B
                             WHERE B.NROSEGMENTO IN (1, 11, 6, 2, 5, 4, 8, 3, 9)
                          )
 GROUP BY A.NROSEGMENTO;
```

### Transformar linhas em colunas
```sql
SELECT *
       FROM (SELECT A.SEQPRODUTO, A.NROEMPRESA, A.STATUSCOMPRA
             FROM CONSINCO.MRL_PRODUTOEMPRESA A
             WHERE 1=1
             AND A.NROEMPRESA IN (2,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20)
             AND A.SEQPRODUTO = 1)
       PIVOT (MAX(STATUSCOMPRA) FOR NROEMPRESA IN (2,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20))
ORDER BY 2;

```
### Formacao de data como texto e ano
```sql
SELECT TRIM(TO_CHAR(EP.DATA, 'MONTH', 'NLS_DATE_LANGUAGE=PORTUGUESE')) || '/' ||
       TO_CHAR(EP.DATA, 'YYYY') MES,
       EP.DESCRICAO,
       EP.PROCESSO,
       UPPER(EP.ETAPA),
       C.DESCRICAO_ITENS,
       UPPER(EP.SOLICITANTE),
       EP.DATA,
       EP.DATA_ENCERRAMENTO,
       UPPER(EP.USUARIO)
  FROM VITRUVIO.VI_ETAPAS_PROCESSO EP, VITRUVIO.GO_COTACAO C
 WHERE 1 = 1
   AND EP.PROCESSO = C.PROCESSO_ID
   AND EP.DATA >= '01-JAN-2024'

```
### Aggrupar valores como meses e ano
```sql
SELECT TO_CHAR(FV.DTAOPERACAO, 'MM/YY') AS MÊS,
       FV.SEQPRODUTO,
       P.PRODUTO,
       SUM(FV.QTDOPERACAO) AS QUANTIDADE
  FROM FATO_VENDA FV
       JOIN DIM_PRODUTO P ON FV.SEQPRODUTO = P.SEQPRODUTO
 WHERE FV.NROEMPRESA = 215
   AND FV.SEQPRODUTO IN (6044, 17915, 18864, 26275, 87005)
   AND FV.DTAOPERACAO >= TO_DATE('29-sep-2025', 'DD-MON-YYYY')
   and fv.seqproduto = 6044
GROUP BY TO_CHAR(FV.DTAOPERACAO, 'MM/YY'), FV.SEQPRODUTO, P.PRODUTO
ORDER BY TO_CHAR(FV.DTAOPERACAO, 'MM/YY'), FV.SEQPRODUTO;
```

### Ano atual vs ano anterior
Mes corrido
```sql
SELECT DISTINCT FV.SEQPRODUTO, FV.DTAOPERACAO
FROM FATO_VENDA FV
WHERE 1=1
AND FV.NROEMPRESA IN (111,224)
AND FV.SEQPRODUTO = 6044
AND FV.DTAOPERACAO BETWEEN ADD_MONTHS(SYSDATE - 90, -12) AND ADD_MONTHS(SYSDATE, -12)
;
```
ou
``` sql
SELECT FV.DTAOPERACAO,
       FV.NROEMPRESA,
       FV.SEQCLIENTE,
       --E.EMPRESA,
       ROUND(SUM(FV.VVLROPERACAOBRUTO),0) AS VLRTOTAL,
       ROUND(SUM(FV.VVLRCTOLIQUIDO),0) AS VLRCUSTOLIQ,
       ROUND(SUM(FV.VLRICMS),0) AS VLRICMS,
       ROUND(SUM(FV.VLRFCPICMS),0) AS VLRFCPICMS,
       ROUND(SUM(FV.VLRPIS),0) AS VLRPIS,
       ROUND(SUM(FV.VLRCOFINS),0) AS VLRCOFINS,
       ROUND(SUM(FV.VLRCUSTOFISCAL),0) AS VLRCUSTOFIS

  FROM CONSINCODW.FATO_VENDA            FV,
       CONSINCODW.BART_CGO_INTERCOMPANY C
 WHERE 1 = 1
   AND FV.CODGERALOPER = C.CODGERALOPER
   AND FV.DTAOPERACAO BETWEEN TO_DATE('01/01' || (EXTRACT(YEAR FROM SYSDATE) - 1), 'DD/MM/YYYY') -- ANO ANTERIOR COMPLETO
   AND SYSDATE
   AND FV.NROEMPRESA in (1, 25, 211)
   AND FV.NROSEGMENTO in (2, 3, 11)
   AND FV.SEQCLIENTE IN (111,224)
 GROUP BY FV.NROEMPRESA, SEQCLIENTE, FV.DTAOPERACAO;
```
Mes ano fechado
``` sql
SELECT DISTINCT FV.SEQPRODUTO, FV.DTAOPERACAO
FROM FATO_VENDA FV
WHERE 1=1
AND FV.NROEMPRESA IN (111,224)
AND FV.SEQPRODUTO = 6044
AND FV.DTAOPERACAO BETWEEN ADD_MONTHS(TRUNC(SYSDATE, 'YEAR') + INTERVAL '7' MONTH, -24) 
AND ADD_MONTHS(SYSDATE, -12)
```

### From dual mes
```sql
SELECT TO_DATE('01/01/' || (EXTRACT(YEAR FROM SYSDATE) - 1), 'DD/MM/YYYY') AS data_inicial,
       SYSDATE AS data_final
FROM DUAL;

```

```sql
SELECT 
       TO_DATE('01/01' || (EXTRACT(YEAR FROM SYSDATE) - 1), 'DD/MM/YYYY')
       --AND SYSDATE
FROM DUAL;
```

```sql
SELECT 
    TRUNC(ADD_MONTHS(SYSDATE, -1), 'MM') AS start_date,
    SYSDATE AS end_date
FROM 
    DUAL;
```

```sql
SELECT SYSDATE - 30 /*- (365)*/
FROM DUAL;
```

### Inner join com parte do valor
```sql
SELECT A.nome AS nome_a, B.nome AS nome_b
FROM tabela_A A
INNER JOIN tabela_B B ON A.nome LIKE B.nome || '%';
```
!!! info "Info"
    A.nome é o valor completo (ex: abacaxi).
    B.nome é o valor parcial (ex: abac).
    O LIKE compara A.nome com B.nome concatenado com %, que permite vincular os valores que começam com B.nome.
    Esse tipo de junção faz com que a coluna A.nome seja vinculada à coluna B.nome onde o valor da tabela A comece com o valor da tabela B.

### Metodo para otimizar script em casos especificos
```sql
SELECT /*+ RULE */ tr.seqproduto
FROM macv_psitemreceber tr
WHERE 1=1
AND tr.dtaemissao >= TO_DATE('01/JAN/2024', 'DD/MON/YYYY');
```

### Exibir duas colunas em uma unica de tabelas diferentes
```sql
SELECT CODIGOBARRA, 
       DESCRICAO, 
       CATEGORIA_NIVEL_1, 
       CATEGORIA_NIVEL_2, 
       CATEGORIA_NIVEL_3, 
       CATEGORIA_NIVEL_4, 
       CATEGORIA_NIVEL_5, 
       MARCA, 
       NOM_FORNECEDOR
       FROM (

              SELECT MP.CODACESSO AS CODACESSO_MP,
                     CV.CODACESSO AS CODACESSO_CV,
                     P.DESCCOMPLETA AS DESCRICAO,
                     P.DEPARTAMENTO AS CATEGORIA_NIVEL_1,
                     P.LINHA AS CATEGORIA_NIVEL_2,
                     P.FAMILIA AS CATEGORIA_NIVEL_3,
                     P.GRUPO AS CATEGORIA_NIVEL_4,
                     P.SUBGRUPO AS CATEGORIA_NIVEL_5,
                     P.MARCA AS MARCA,
                     P.FANTASIA AS NOM_FORNECEDOR
                 FROM CONSINCO.BARV_LISTA_PRODUTOS P,
                      CONSINCO.MAP_PRODCODIGO MP,
                      CONSINCO.MAP_PRODCODIGOVENCIMENTO CV
                 WHERE 1=1
                 AND P.SEQPRODUTO = MP.SEQPRODUTO
                 AND MP.SEQPRODUTO = CV.SEQPRODUTO(+)
                 AND MP.TIPCODIGO IN ('E', 'I')
                 AND MP.INDUTILVENDA = 'S'
                 AND CV.QTDEMBALAGEM = 1
                 AND P.DEPARTAMENTO NOT LIKE '%VENDA'
                 AND P.SEQPRODUTO = 6044
   )
   
   UNPIVOT (
             CODIGOBARRA FOR CODIGO_TIPO IN (CODACESSO_MP AS 'MP', CODACESSO_CV AS 'CV')         
   )
   WHERE CODIGOBARRA IS NOT NULL
   ;
```

### Extrair palavra da string
```sql
SELECT E.EMPRESA, SUBSTR(E.EMPRESA, INSTR(E.EMPRESA, ' ')+1) AS EMPRESA
FROM CONSINCODW.DIM_EMPRESA E
;
```
### Pegar ultimo dia de cada mes
```sql
SELECT LAST_DAY(ADD_MONTHS(DATE '2024-01-01', LEVEL - 1)) AS ULTIMO_DIA
  FROM DUAL
CONNECT BY ADD_MONTHS(DATE '2024-01-01', LEVEL - 1) <= TRUNC(SYSDATE, 'MM')
;
```

### Separar conteudo na mesma coluna (texto para coluna excel)
```sql
SELECT 
    TRIM(SUBSTR(cliente, 1, INSTR(cliente, '-') - 1)) AS CODIGO,
    TRIM(SUBSTR(cliente, INSTR(cliente, '-') + 1))   AS NOME
FROM (
    SELECT '264081 - PATRICIA SANTOS SOUZA' AS cliente FROM dual
    UNION ALL
    SELECT '425234 - KAROLINE CARREGAL RANGEL' AS cliente FROM dual
);
```

### Contagem caracter com filtro
```sql
SELECT pc.SEQPRODUTO,
       pc.CODACESSO,
       pc.TIPCODIGO
FROM MAP_PRODCODIGO pc
WHERE pc.TIPCODIGO = 'I'
  AND LENGTH(pc.CODACESSO) BETWEEN 8 AND 13;
```


## Gestao de banco

### Tamanho das tabelas
```sql
SELECT * FROM
  (SELECT TRUNC(SUM(BYTES)/1024/*/1024/1024,2*/) "SIZE GB"
      FROM DBA_SEGMENTS
      WHERE SEGMENT_TYPE = 'MAP_PRODUTO'
      GROUP BY SEGMENT_NAME, OWNER
      )
      ;
```

### Permicoes objetos
```sql
SELECT 
    GRANTEE, 
    TABLE_NAME,
    MAX(CASE WHEN PRIVILEGE = 'SELECT' THEN 'S' ELSE 'N' END) AS SELECT_,
    MAX(CASE WHEN PRIVILEGE = 'INSERT' THEN 'S' ELSE 'N' END) AS INSERT_,
    MAX(CASE WHEN PRIVILEGE = 'UPDATE' THEN 'S' ELSE 'N' END) AS UPDATE_,
    MAX(CASE WHEN PRIVILEGE = 'DELETE' THEN 'S' ELSE 'N' END) AS DELETE_
    
FROM DBA_TAB_PRIVS
WHERE 1=1
      AND GRANTEE IN ('CONSINCODW', 'BARCELOSTIDW') 
      AND TABLE_NAME = 'GE_PESSOACADASTRO'
GROUP BY GRANTEE, TABLE_NAME;
```

### Conceder permissao/grant
```sql
GRANT SELECT ON CONSINCO.MAP_PRODCODIGOVENCIMENTO TO BARCELOSTIDW;
```

### Procedure vs tabela
```sql
SELECT *
FROM ALL_SOURCE
WHERE UPPER(TEXT) LIKE '%BART_ATAC_VDA_ANTERIOR%'
AND OWNER = 'CONSINCO';
```

### Jobs vs procedures vs tabela
```sql
SELECT *
FROM DBA_SCHEDULER_JOBS
WHERE JOB_ACTION LIKE '%BART_ATAC_VDA_ATUAL%';
```

### Historico de execuçoes de jobs
```sql
SELECT J.JOB_NAME, TO_CHAR(ACTUAL_START_DATE, 'DD/MM/YY HH24:MI:SS') AS DTHREXEC, J.RUN_DURATION AS DURACAO, J.STATUS
FROM DBA_SCHEDULER_JOB_RUN_DETAILS J
WHERE 1=1
AND J.JOB_NAME LIKE 'BAR_ATUALIZADW_BACKOFFICE'
ORDER BY J.ACTUAL_START_DATE DESC
FETCH FIRST 1 ROW ONLY;
```

### Ultimas execucoes das jobs
```sql
SELECT * FROM DBA_SCHEDULER_JOB_RUN_DETAILS A WHERE 1=1 AND A.job_name = 'BARCELOS - REFRESH DE VIEW MATERIALIZADA';
WITH LASTEXECUTION AS (
    SELECT 
        J.owner,
        J.JOB_NAME, 
        J.ACTUAL_START_DATE, 
        J.RUN_DURATION, 
        J.STATUS,
        ROW_NUMBER() OVER (PARTITION BY JOB_NAME ORDER BY ACTUAL_START_DATE DESC) AS RN
    FROM DBA_SCHEDULER_JOB_RUN_DETAILS J
    WHERE 1=1
    AND J.owner IN ('CONSINCO', 'CONSINCODW')
    AND J.job_name LIKE 'BAR%'
)
SELECT 
    OWNER,
    JOB_NAME, 
    TO_CHAR(ACTUAL_START_DATE, 'DD/MM/YY HH24:MI:SS') AS DTHREXEC,
    RUN_DURATION, 
    STATUS
FROM LASTEXECUTION
WHERE RN = 1;

```

###
```sql

```

###
```sql

```

###
```sql

```

###
```sql

```

###
```sql

```