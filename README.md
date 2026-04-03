# miniguia-estudos-notebooklm
🚀 **Miniguia de Estudos: SQL no NotebookLM**
Status do Projeto: 📘 Em Desenvolvimento (Estudo e Revisão)

Repositório dedicado ao armazenamento de notas, conceitos fundamentais e exercícios práticos sobre SQL. Este guia foi estruturado para servir como uma fonte de consulta rápida para revisão técnica, focado na preparação para estágios na área de Dados.

🎯 **Motivação**
O objetivo principal deste repositório é consolidar o conhecimento em SQL através de curadoria de conteúdos e experimentação prática. A estrutura de tópicos foi desenhada para facilitar a revisão de conceitos que são frequentemente cobrados em entrevistas técnicas de dados.

📚 **Fontes de Estudo**
PostgreSQL Documentation - Referência técnica oficial.

SQL-Manual - Guia prático de comandos.

Pandas Cheat Sheet (PDF) - Para comparação de sintaxe entre SQL e Python.

Dataset Chinook: Banco de dados clássico de exemplo (convertido para .txt).

YouTube: SQLite Introduction - Fundamentos de bancos de dados.

📚 **Prompts:**
**(1)**
Detalhe de forma clara, quando usar WHERE e quando usar o AGROUP BY
Resposta: "Para detalhar o uso dessas cláusulas, é importante entender que elas operam em etapas diferentes do processamento de uma consulta SQL.
Quando usar o WHERE
A cláusula WHERE é utilizada para filtrar linhas individuais de uma tabela baseando-se em condições específicas
.
Filtragem inicial: Ela age sobre as linhas brutas da tabela, antes que qualquer agrupamento ou cálculo seja realizado
.
Expressões Booleanas: O WHERE utiliza expressões lógicas (como =, >, <>, AND, OR); apenas as linhas que resultam em "verdadeiro" para a condição são mantidas para o restante da consulta
.
Restrição importante: Você não pode usar funções agregadas (como SUM, AVG, MAX) dentro de um WHERE
. Isso ocorre porque o WHERE serve justamente para decidir quais linhas entrarão no cálculo dessas funções
.
Quando usar o GROUP BY
O GROUP BY é utilizado quando você deseja agrupar linhas que compartilham valores comuns em determinadas colunas
.
Resumo de dados: O objetivo principal é transformar várias linhas em uma única "linha de grupo" que represente o conjunto
.
Uso com Agregadores: É essencial quando você precisa realizar cálculos sobre subconjuntos de dados, como contar quantos funcionários existem por departamento ou qual a média de preço por categoria de produto
.
Regra de Seleção: Em uma consulta que utiliza agrupamento, qualquer coluna que apareça no SELECT e que não seja uma função agregada deve obrigatoriamente estar listada na cláusula GROUP BY
.
Comparação e Interação entre as Cláusulas
A principal diferença reside no momento em que a filtragem ocorre e sobre qual tipo de dado ela atua:
Característica
WHERE
GROUP BY / HAVING
Alvo da filtragem
Linhas individuais da tabela
.
Grupos de linhas criados pelo GROUP BY
.
Funções Agregadas
Nunca permitidas
.
Sempre permitidas (especificamente na cláusula HAVING)
.
Ordem de Execução
Executado primeiro, removendo linhas indesejadas da entrada
.
Executado depois, resumindo o que restou do filtro inicial
.
Exemplo prático de interação: Se você quer calcular a média de vendas apenas para produtos "Ativos" e apenas mostrar as categorias que venderam mais de R$ 1.000, você usaria o WHERE para filtrar os produtos ativos, o GROUP BY para separar por categoria e o HAVING para filtrar o valor total final
"

**(2)** 
Explique querys com subquerys com um exemplo

Resposta: 
Uma **subquery** (ou subconsulta) é uma instrução `SELECT` aninhada dentro de uma consulta principal ou de outra subquery. Elas funcionam como cálculos independentes que fornecem dados necessários para a consulta externa.

As subquerys devem ser sempre escritas entre **parênteses**.

### **Quando usar Subquerys**
Elas são úteis quando você precisa de um resultado intermediário para filtrar dados, definir uma tabela virtual ou realizar comparações que funções agregadas sozinhas não permitem no `WHERE`.

### **Tipos Comuns e Exemplos**

#### **1. Subquery Escalar (Valor Único)**
Retorna apenas uma linha e uma coluna. É muito usada para comparar um campo com um valor agregado (como um máximo ou uma média), já que você não pode usar agregadores diretamente na cláusula `WHERE`.

*   **Exemplo**: Encontrar a cidade que registrou a maior temperatura baixa.
    ```sql
    SELECT city FROM weather 
    WHERE temp_lo = (SELECT max(temp_lo) FROM weather);
    ```
    *Neste caso, a subquery calcula o valor máximo primeiro e o "entrega" para a consulta principal filtrar a cidade correspondente.*

#### **2. Subquery com o operador IN (Múltiplos Valores)**
Retorna uma lista de valores (uma coluna e várias linhas) para serem usados em uma filtragem.

*   **Exemplo**: Selecionar nomes de uma tabela baseando-se em IDs presentes em outra.
    ```sql
    SELECT name FROM table1 
    WHERE id IN (SELECT id FROM table2);
    ```
    *A consulta principal só retornará as linhas de `table1` cujos IDs existam no resultado gerado pela subquery.*

#### **3. Subquery Correlacionada**
É um tipo especial onde a subquery faz referência a uma coluna da consulta principal. Ela é executada repetidamente, uma vez para cada linha processada pela consulta externa.

*   **Exemplo**: Encontrar o cargo e o nome da pessoa mais jovem de cada profissão.
    ```sql
    SELECT job, name FROM persons AS p 
    WHERE bdate = (SELECT max(bdate) FROM persons WHERE job = p.job);
    ```
    *Aqui, a subquery usa `p.job` da consulta externa para filtrar o máximo de data de nascimento especificamente para aquele cargo.*

#### **4. Subquery na cláusula FROM (Tabelas Derivadas)**
Você pode tratar o resultado de uma subquery como se fosse uma tabela temporária.

*   **Exemplo**:
    ```sql
    SELECT * FROM (SELECT * FROM table1) AS alias_name;
    ```
    *É obrigatório atribuir um **alias** (nome fictício) para a subquery quando usada no `FROM`.*

### **Regras Importantes**
*   **Parenteses**: Sempre obrigatórios ao redor da subquery.
*   **Escopo**: A subquery pode acessar variáveis da consulta principal ("outer query"), mas a consulta principal não acessa diretamente o que está dentro da subquery.
*   **Limitação**: Uma subquery escalar resultará em erro se retornar mais de uma linha ou mais de uma coluna.

 **(3)**
 Me dê um exercício básico

Resposta:

Aqui está um exercício básico baseado na estrutura do banco de dados **Chinook** presente em seus documentos. Este exercício foca nos conceitos que discutimos anteriormente: filtragem com `WHERE`, agrupamento com `GROUP BY` e o uso de `JOIN`.

### **Exercício: Consulta de Vendas e Gêneros**

**Cenário:** O banco de dados possui tabelas para músicas (`Track`), gêneros (`Genre`) e itens de faturas (`InvoiceLine`).

**Tarefa:**
Escreva uma consulta SQL que retorne o **nome de cada gênero** e o **total de vendas** (soma do preço unitário) para as faixas desse gênero, mas apenas para os gêneros que venderam mais de **5.00** no total.

---

### **Dicas para resolver:**
1.  **WHERE vs HAVING**: Lembre-se que o `WHERE` filtra linhas antes do agrupamento, enquanto o `HAVING` filtra após o `GROUP BY`.
2.  **JOIN**: Você precisará unir as tabelas `Genre`, `Track` e `InvoiceLine` usando as chaves estrangeiras (`GenreId` e `TrackId`).
3.  **Agregação**: Use a função `SUM(UnitPrice)` para calcular o total de vendas.

---

### **Solução sugerida** (para conferência após tentar):

```sql
SELECT g.Name, SUM(il.UnitPrice) AS TotalVendas
FROM Genre g
JOIN Track t ON g.GenreId = t.GenreId
JOIN InvoiceLine il ON t.TrackId = il.TrackId
GROUP BY g.Name
HAVING SUM(il.UnitPrice) > 5.00;
```

**Explicação dos passos:**
*   As cláusulas **JOIN** conectam os gêneros às suas músicas e essas músicas às vendas registradas.
*   O **GROUP BY** organiza os resultados pelo nome do gênero para que o cálculo da soma seja feito por categoria.
*   A cláusula **HAVING** é usada no final para filtrar apenas os grupos cujo cálculo de agregação (`SUM`) atende ao critério de valor superior a 5.00.
*   Não foi necessário usar o **WHERE** neste caso específico, a menos que você quisesse filtrar, por exemplo, apenas vendas ocorridas em um ano específico antes de agrupar.

**(4)**

Detalhe quando usar INNER JOIN, LEFT JOIN e RIGHT JOIN

Resposta:

As cláusulas de **JOIN** são fundamentais no SQL para combinar linhas de duas ou mais tabelas com base em uma condição de correspondência. A escolha de qual usar depende de quais dados você deseja manter no resultado final, especialmente quando não há correspondência exata entre as tabelas.

### **1. INNER JOIN (Junção Interna)**
Este é o tipo mais comum e o padrão no SQL.
*   **Quando usar:** Quando você quer apenas as linhas que possuem **correspondência em ambas as tabelas**.
*   **Comportamento:** Se uma linha na primeira tabela (T1) não tiver um par correspondente na segunda tabela (T2) de acordo com a condição (cláusula `ON` ou `USING`), essa linha é excluída do resultado.
*   **Exemplo prático:** Se você unir uma tabela de `Clima` e uma de `Cidades` por nome, o `INNER JOIN` retornará apenas as cidades que aparecem em ambas as tabelas.

### **2. LEFT JOIN ou LEFT OUTER JOIN (Junção Externa à Esquerda)**
*   **Quando usar:** Quando você deseja manter **todos os registros da tabela da esquerda** (mencionada antes da palavra `JOIN`), independentemente de haver uma correspondência na tabela da direita.
*   **Comportamento:** Primeiro, ele realiza a junção interna. Para cada linha da tabela da esquerda que **não encontrar correspondência**, o resultado incluirá essa linha, mas preencherá as colunas da tabela da direita com valores **NULL**.
*   **Exemplo prático:** Útil para encontrar registros "orfãos". Por exemplo, listar todos os fabricantes e os nomes de seus produtos; se um fabricante não tiver produtos cadastrados, ele ainda aparecerá na lista, mas o nome do produto será exibido como nulo.

### **3. RIGHT JOIN ou RIGHT OUTER JOIN (Junção Externa à Direita)**
*   **Quando usar:** Quando você deseja manter **todos os registros da tabela da direita**, independentemente de haver uma correspondência na tabela da esquerda.
*   **Comportamento:** É o inverso do `LEFT JOIN`. O resultado sempre terá pelo menos uma linha para cada registro da tabela da direita. Se não houver correspondência na tabela da esquerda, as colunas desta serão preenchidas com **NULL**.
*   **Observação:** Na prática, muitos desenvolvedores preferem usar apenas `LEFT JOIN` e inverter a ordem das tabelas na consulta para facilitar a leitura.

### **Resumo Comparativo**

| Tipo de Join | O que retorna? | Tratamento de Não Correspondentes |
| :--- | :--- | :--- |
| **INNER JOIN** | Apenas o que existe em ambas. | Linhas sem par são descartadas. |
| **LEFT JOIN** | Tudo da esquerda + correspondentes da direita. | Lado direito fica **NULL**. |
| **RIGHT JOIN** | Tudo da direita + correspondentes da esquerda. | Lado esquerdo fica **NULL**. |

**Dica de Sintaxe:** Ao usar `JOIN`, é recomendável usar **aliases** (apelidos) para as tabelas para tornar a consulta mais legível e evitar erros quando as tabelas possuem colunas com nomes idênticos. Além disso, a filtragem via `WHERE` ocorre após a junção, o que é especialmente relevante em junções externas.

**(5)**
Considere que você esteja ensinando para um iniciante, como adicionar uma tabela, linha e coluna com SQL

Resposta: 

Para aprender SQL, é fundamental entender três comandos básicos que permitem estruturar e preencher seu banco de dados: **CREATE TABLE** (para criar tabelas), **ALTER TABLE** (para modificar a estrutura, como adicionar colunas) e **INSERT INTO** (para adicionar dados ou linhas).

Aqui está um guia passo a passo:

### 1. Como adicionar uma Tabela (**CREATE TABLE**)
Para criar uma nova tabela, você define o nome dela e as colunas que ela terá, especificando o tipo de dado de cada uma (como números ou texto).

*   **Sintaxe básica:** `CREATE TABLE nome_da_tabela (coluna1 tipo, coluna2 tipo);`
*   **Exemplo prático:** Vamos criar uma tabela simples de produtos.
    ```sql
    CREATE TABLE produtos (
        produto_no integer,
        nome text,
        preco numeric
    );
    ```
    Neste exemplo, criamos três colunas: uma para o número do produto (inteiro), uma para o nome (texto) e outra para o preço (numérico).

### 2. Como adicionar uma Coluna (**ALTER TABLE**)
Se você já criou uma tabela e percebeu que esqueceu de algo ou os requisitos mudaram, você usa o comando **ALTER TABLE** para modificar a estrutura existente sem precisar apagar a tabela.

*   **Sintaxe básica:** `ALTER TABLE nome_da_tabela ADD COLUMN nome_da_coluna tipo;`
*   **Exemplo prático:** Adicionando uma coluna de "descrição" à nossa tabela de produtos.
    ```sql
    ALTER TABLE produtos ADD COLUMN descricao text;
    ```
    A nova coluna será preenchida inicialmente com valores nulos (`NULL`) para todas as linhas que já existirem.

### 3. Como adicionar uma Linha (**INSERT INTO**)
Depois que a estrutura da tabela (colunas) está pronta, você usa o comando **INSERT INTO** para começar a inserir dados reais, linha por linha.

Existem duas formas principais de fazer isso:

*   **Forma recomendada (especificando colunas):** É a melhor prática porque evita erros se a ordem das colunas mudar no futuro.
    ```sql
    INSERT INTO produtos (produto_no, nome, preco) 
    VALUES (1, 'Queijo', 9.99);
    ```
*   **Forma compacta (apenas valores):** Você deve listar os valores exatamente na mesma ordem em que as colunas aparecem na tabela.
    ```sql
    INSERT INTO produtos VALUES (2, 'Pão', 1.99);
    ```

### Dicas importantes para iniciantes:
*   **Nomes de objetos:** Nomes de tabelas e colunas devem começar com uma letra e podem conter letras, números e sublinhados (_).
*   **Ponto e vírgula:** No SQL, cada comando deve terminar com um `;` para indicar ao sistema que a instrução acabou.
*   **Aspas simples:** Valores de texto (como 'Queijo') devem estar entre aspas simples, enquanto números não precisam.
*   **Constraints:** Você pode adicionar regras às colunas, como `NOT NULL` (para tornar o preenchimento obrigatório) ou `PRIMARY KEY` (para garantir que aquele identificador seja único).
