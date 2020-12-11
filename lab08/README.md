# Lab 08

## lab01-xpath-xquery

### Questão 1

Construa uma comando SELECT que retorne dados equivalentes a este XPath

```xquery
//individuo[idade>20]/@nome
```
Resposta:

```sql
SELECT individuo
FROM fichario
WHERE individuo.idade > 20

```

### Questão 2

Qual a outra maneira de escrever esta query sem o where?

```xquery
let $fichariodoc := doc('http://www.ic.unicamp.br/~santanch/teaching/db/xml/fichario.xml')

for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
```
Resposta:

```xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')

for $i in ($fichariodoc//individuo[idade>17])
return {data($i/@nome)}
```

### Questão 3
Escreva uma consulta SQL equivalente ao XQuery:
```xquery
let $fichariodoc := doc('http://www.ic.unicamp.br/~santanch/teaching/db/xml/fichario.xml')

for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
```
Resposta:

```sql
SELECT individuo
FROM fichario
WHERE individuo.idade > 20
```
### Questão 4
Retorne quantas publicações são posteriores ao ano de 2011.

Resposta:

```xquery
let $publicationsDoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

return count($publicationsDoc//publications/publication[year > 2011])
```
### Questão 5
Retorne a categoria cujo <label> em inglês seja 'e-Science Domain'.

Resposta:

```xquery
let $publicationsDoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

return data($publicationsDoc//categories/category[label="e-Science Domain"])
```
### Questão 6
Retorne as publicações associadas à categoria cujo <label> em inglês seja 'e-Science Domain'. A associação entre o label e a key da categoria deve ser feita na consulta.

Resposta:

```xquery
let $publicationsDoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

let $category := $publicationsDoc//categories/category[label="e-Science Domain"]

for $publication in ($publicationsDoc//publications/publication[key=$category/@key])
return data($publication)
```
## lab04-xquery-drom-pubchem-questoes


### Questão 1

Liste o nome de todas as classificações que estão apenas dois níveis imediatamente abaixo da raiz.

Resposta:

```xquery
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')

for $element in ($dron//*/*)

return {data($element/@name), '&#xa;'}
```
### Questão 2

Apresente todas as classificações de um componente a sua escolha (diferente de `Acetylsalicylic Acid`) que estejam hierarquicamente dois níveis acima. Note que no exemplo dado em sala foi considerado um nível hierárquico acima.

Resposta:

```xquery
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')


for $component in ($dron//drug[drug/drug/@name='PARACETAMOL'])

return {data($component/@name), '&#xa;'}
```

### Questão 3

#### Questão 3.1

Liste todos os códigos ChEBI dos componentes disponíveis.

Resposta:

```xquery
let $data := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')
for $i in ($data//Synonym[substring(text(),0,6)="CHEBI"]/substring(text(),7))
return {$i, '&#xa;'}
```

#### Questão 3.2

Liste todos os códigos ChEBI e primeiro nome (sinônimo) de cada um dos componentes disponíveis.

Resposta:

```xquery
let $content := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')
for $i in ($content//Information/Synonym[1]), $j in ($content//Synonym[substring(text(),0,6)="CHEBI"]/substring(text(),7))
return {data($i), data($j), '&#xa;'}
```

#### Questão 3.3

Para cada código ChEBI, liste os sinônimos e o nome que aparece para o mesmo componente no DRON (se existir). Para isso faça um JOIN com o DRON.

```xquery
let $pubchem := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')

for $i in ($pubchem//Information), $s in ($pubchem//Synonym),$d in ($dron//drug)
where concat('http://purl.obolibrary.org/obo/CHEBI_', substring($s/text(), 7)) = $d/@id and $i/Synonym = $s
group by $s
return {data($s), data($i/Synonym), data($d/@name), '&#xa;'}
```
