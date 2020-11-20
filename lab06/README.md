## Tarefa de Cypher e o FDA Adverse Event Reporting System (FAERS)

## Exercício 1

Escreva uma sentença em Cypher que crie o medicamento de nome `Metamizole`, código no DrugBank `DB04817`.

### Resolução
~~~cypher
CREATE (:Drug {drugbank: "DB04817", name: "Metamizole"} )
~~~

## Exercício 2

Considerando que a `Dipyrone` e `Metamizole` são o mesmo medicamento com nomes diferentes, crie uma aresta com o rótulo `:SameAs` que ligue os dois.

### Resolução
~~~cypher
MATCH (d1:Drug {name:"Dipyrone"})
MATCH (d2:Drug {name:"Metamizole"})
CREATE (d1)-[:SameAs]->(d2)
~~~

## Exercício 3

Use o `DELETE` para excluir o relacionamento que você criou (apenas ele).

### Resolução
~~~cypher
MATCH (d1:Drug {name:"Dipyrone"})
MATCH (d2:Drug {name:"Metamizole"})
MATCH (d1)-[rel]->(d2)
DELETE rel
~~~

## Exercício 4

Faça a projeção em relação a Patologia, ou seja, conecte patologias que são tratadas pela mesma droga.

### Resolução
~~~cypher
MATCH (p1:Pathology)<-[a]-(d:Drug)-[b]->(p2:Pathology)
WHERE a.weight > 20 AND b.weight > 20
MERGE (p1)<-[r:Relates]->(p2)
ON CREATE SET r.weight=1
ON MATCH SET r.weight=r.weight+1
~~~

## Exercício 5

Construa um grafo ligando os medicamentos aos efeitos colaterais (com pesos associados) a partir dos registros das pessoas, ou seja, se uma pessoa usa um medicamento e ela teve um efeito colateral, o medicamento deve ser ligado ao efeito colateral.

### Resolução
~~~cypher
(escreva aqui a resolução em Cypher)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug-use.csv' AS line
CREATE(:DrugUse {idPerson: line.idperson, codePathology: line.codepathology, codeDrug: line.codedrug})

CREATE INDEX ON : DrugUse (idPerson)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/sideeffect.csv' AS line
CREATE(:SideEffect {idPerson: line.idPerson, codePathology: line.codePathology})

CREATE INDEX ON : SideEffect (idPerson)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/sideeffect.csv' AS line

MATCH (se: SideEffect {idPerson: line.idPerson, codePathology: line.codePathology})
MATCH (du: DrugUse { idPerson: line.idPerson})
MATCH (d:  Drug {code: du.codeDrug})

MERGE (d)-[c: CauseSideEffect]->(se)
ON CREATE SET c.weight=1
ON MATCH SET c.weight=c.weight+1

~~~

## Exercício 6

Que tipo de análise interessante pode ser feita com esse grafo?

Proponha um tipo de análise e escreva uma sentença em Cypher que realize a análise.

- Analise proposta: Quais medicamentos causam quais patologias como efeitos colaterais?

### Resolução
~~~cypher
(escreva aqui a resolução em Cypher)
MATCH (d:Drug)-[c: CauseSideEffect]->(se:SideEffect)
MATCH (p:Pathology {code: se.codePathology})

MERGE (d)-[mc:MayCause]->(p)
ON CREATE SET mc.weight=1
ON MATCH SET mc.weight=mc.weight+1

MATCH (d)-[mc:MayCause]->(p)

RETURN d, mc, p
LIMIT 100
~~~
