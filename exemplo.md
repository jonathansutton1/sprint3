O algoritmo Counting Sort
======

O Problema
---------

Como já foi visto diversas vezes na disciplina, existem diversos algoritmos de ordenação de vetores, com idéias e eficiências diferentes. Vimos também diversas situações nas quais é preciso saber escolher bem o seu algoritmo de ordenação, considerando diversos fatores. 

Nesse Handout, especificamente, vamos falar sobre o algoritmo Counting Sort.

!!! Aviso
Todas implementações de código desse Handout serão feitas na linguagem C.
!!!

!!! Outro aviso
Por questões de simplicidade, todos elementos dos vetores utilizados nesse Handout serão dígitos de 1 a 9.
!!!

Versão simplificada
---------

**O vetor de contagem**

O Counting Sort nada mais é do que um algoritmo de ordenação. Mas o que diferencia ele do resto? Bom, esse algoritmo, diferentemente dos outros, utiliza um *vetor auxiliar*. Esse vetor auxiliar, como está no título da seção, realiza uma **contagem**. Ele possui um tamanho que cobre todos os elementos do vetor original, e cada elemento dele representa quantas vezes aparece seu índice no original. 

Pode-se definir como *k* o número possivel de valores diferentes que o elemento do vetor pode assumir. Você verá que ele afeta diretamente a complexidade do algoritmo.

Ficou confuso? Calma, vamos explicar.

??? Exercício
Dado o vetor 
```c
v[] = {2,2,5,3,4,2,3,3,0,1,0}. 
```

Qual seria o seu vetor de contagem?

:::Gabarito
Para definir isso nessa versão simplificada, é preciso primeiramente definir o tamanho do vetor de contagem. Como o vetor dado abrange elementos de 0 a 5, ele precisará de 6 índices, certo?

Logo:

- *0* aparece 2 vezes
- *1* aparece 1 vez
- *2* aparece 3 vezes
- *3* aparece 3 vezes
- *4* aparece 1 vez
- *5* aparece 1 vez

Com isso, o vetor de contagem ficaria:
```c
count[] = {2,1,3,3,1,1}
```
:::
???

??? Exercício

Qual uma possível desvantagem desse algoritmo dado pelo fato de usar 2 vetores?

::: Gabarito
O Counting Sort possui uma grande desvantagem em questões de memória, devido ao uso de mais de um vetor.
:::
???

???Exercício
Vamos fazer a ordem inversa agora. Dado o vetor de **contagem**:
```c
count[] = {3,0,1,2,1}
```

Escreva como ficaria o vetor original ORDENADO.

:::Gabarito
Será feito o mesmo caminho da outra vez.
```c
- count[0] = 3  //0 aparece 3 vezes
- count[1] = 0  //1 aparece nenhuma vez
- count[2] = 1  //2 aparece 1 vez
- count[3] = 2  //3 aparece 2 vezes
- count[4] = 1  //4 aparece 1 vez
```
Então o vetor original ordenado ficaria:
```c
v[] = {0,0,0,2,3,3,4}
```
:::
???

Isso basicamente resume a versão simplificada do algoritmo.

```c
vetor original
elementos passam pelo vetor de contagem
cada índice desse vetor é um valor possível do original
voltam ordenados para o original.
```

Mas essa versão simplificada não é o ideal... ela possui problemas pontuais.

???Exercício
Qual seria uma possível desvantagem dessa versão simplificada?

{red}(**Dica:**) Imagine em um caso que estamos lidando com algo diferente de números inteiros de 1 a 9, como por exemplo números de matrícula de uma escola.

:::Gabarito
Ao fazer isso você está escrevendo novos elementos, não inserindo os elementos no vetor otiginal de forma ordenada. Isso funciona para situações em que o range entre os números é pequeno, mas se for maior, não funciona.
:::
???

 Detalhando mais o exemplo dado no gabarito, vamos imaginar que queremos ordenar alunos. Alunos podem ser encarados como objetos, certo? Ou seja, não estamos encarando um caso no qual queremos ordenar apenas *números*.

Imagine agora que as chaves desse dicionário são os números de matrícula dos alunos. Não faria sentido utilizar a versão simplista do Counting Sort, pois conforme falado anteriormente, ela escreve os elementos ordenados em outro vetor. Nesse caso, precisaríamos que o algoritmo inserisse no vetor original a versão ordenada.

Mas então como melhorar esse algoritmo? 

Passo a passo do algoritmo original
---------

O primeiro passo que torna essa "magia" possível é achar o elemento máximo do vetor original. A razão ficará mais clara a partir do próximo passo. 

!!!Aviso
Este passo não é obrigatório. Na maioria das aplicações do Couting Sort, o domínio já é conhecido, ou seja, os valores que ele pode assumir já são conhecidos.
!!!

??? Exercício

Tente implementar a idéia acima em C. Lembre-se que a função recebe um vetor de inteiros ```v[]```  e o número de elementos ```n```.
::: Gabarito
``` c
int max = v[0];
  for (int i = 1; i < n; i++) {
    if (v[i] > max)
      max = array[i];
  }
```
:::
???

Até agora, esse Handout nos trouxe muitas informações sem nexo. Chegou a hora delas se conectarem. 

Lembram do tal do *vetor auxiliar*? Então, vamos conhecê-lo agora. Ele será definido no código da seguinte maneira:
``` c
int count[max+1];
```
Sim, esse ```max``` é referente ao valor máximo, achado anteriormente! O motivo disso é que, dentro desse vetor será feita uma contagem com relação aos índices e elementos do original. Ficou confuso? Vamos com calma.

??? Exercício
Implemente um código para inicializar todos valores do vetor ```count``` como 0.
::: Gabarito
``` c
for(int i = 0; i <= max; i++){
    count[i] = 0;
}
```
???

Excelente. Feito isso, temos agora 2 vetores: o original (que precisa ser ordenado) e o auxiliar (irá ajudar na ordenação). 

```c
para j <- 0 até n
    encontra a contagem total de cada elemento e guarda
    essa contagem no índice j do vetor de contagem.
```

??? Exercício
Simule o pseudocódigo acima para o vetor original ```{2,1,4,3,0}```.
:::Gabarito
O elemento máximo desse vetor é 4. Por isso, sabemos que o vetor de contagem se iniciará
```c
{0,0,0,0,0} //tamanho do vetor é 5
```
Armazenando a contagem no vetor, é possível perceber que:
```c
0 apareceuma vez
1 aparece uma vez
2 aparece uma vez
3 aparece uma vez
4 aparece uma vez
```
Sendo assim, o vetor de contagem ficará:
```c
{1,1,1,1,1}
```
:::
???

Vamos continuar implementando o nosso código!

???Exercício
Transforme o pseudocódigo lá de cima para um código em C.
:::Gabarito
```c
for (int i = 0; i < n; i++) {
    count[v[i]]++;
  }
```
:::
???

!!!Aviso
Tente entender bem essa sessão. Ela é importante para o resto do Handout.
!!!

A *animação* abaixo pode ajudar no entendimento do conceito. Em cima temos o vetor original, e embaixo o vetor de contagem:

:count

A soma cumulativa
------------------

Se você entendeu bem a tarefa anterior, a próxima não será muito complexa. Já ouviu falar de [soma cumulativa](https://www.cimm.com.br/portal/verbetes/exibir/1769-soma-cumulativa#:~:text=Defini%C3%A7%C3%A3o%20%2D%20O%20que%20%C3%A9%20Soma,c%C3%A1lculo%2C%20tanto%20positivo%20quanto%20negativo.)? Se sim, excelente, pois é exatamente o que faremos agora. Caso você não saiba, tudo bem também.

???Exercício
Dado o vetor de contagem ```count[] = {2,2,3,1}```, em qual posição do vetor ```v[]``` estará o **ÚLTIMO** elemento com valor 0? E o com valor 1?

:::Gabarito
*Para o 0:* Como tem 2 elementos 0, ficaria em ```v[1]```! 
*Para o 1:* Como tem 2 elementos 1, ficaria em ```v[1 + 2] = v[3]```

Perceba que para o número 1, nos baseamos na posição final do número 0. Ou seja, é preciso considerar o contador do 0.

Vetor original ```count[] = {0,0,1,1,2,2,2,3}```
:::
???

???Exercício 
Vamos ver se você entendeu. Utilizando o mesmo vetor de contagem do item anterior, em qual posição estará o **ÚLTIMO** elemento com valor 2?
:::Gabarito
```v[3 + 3] = v[6]```

Consideramos a posição do elemento anterior, e somamos o número de ocorrências. Perceba que não é preciso somar para o caso do elemento 1, pois a soma já foi feita antes.
:::
???

```c
para i <= até max
    encontra a soma cumulativa dos elementos e guarda no 
    próprio vetor.
```

???Exercício
Simule esse passo para o vetor de contagem achado anteriormente (na seção anterior):
```c
v[] = {2,1,4,3,0}
count[] = {1,1,1,1,1}
```
:::Gabarito

Vamos implementar a soma cumulativa para o ```count[]```. 

```
soma do primeiro elemento: 1
soma do segundo elemento: 2
soma do terceiro elemento: 3
soma do quarto elemento: 4
soma do quinto elemento: 5
```

Logo, os vetores ficariam da seguinte maneira:
```c
v[] = {2,1,4,3,0}
count[] = {1,2,3,4,5}
```
:::
???

???Exercício
Implemente o código em C.
:::Gabarito
```c
for(int i=0; i <= max;i++){
    count[i] += count[i-1];
}
```
:::
???

Mais uma animação para auxiliar na compreensão do conceito de soma cumulativa:

:soma

???Exercício
Levando em conta o vetor de contagem ```c count[] = {1,2,3,4,5}```, qual seria o índice **FINAL** (ou seja, o último índice) em que o elemento 0 aparece?

E para os outros números?
:::Gabarito

Para responder essa pergunta, a tabela abaixo deve organizar melhor as informações:

| Elemento| Índice final |
|----------|----------|
| 0     | 0      |
| 1        | 1         |
| 2      |  2       |
| 3  |   3  |
| 4  | 4  |

Há um padrão entre o índice final e o vetor de contagem. Caso você não tenha percebido, não tem problema. O próximo passo explicará logo de cara.
:::
???


Bom, chegou a hora da magia acontecer. A partir do vetor de contagem com a soma cumulativa, será possível ordenar o vetor original. Como? Preste bem atenção na *receita*:

1. **Pegue o primeiro elemento do vetor original**
2. **Encontre seu índice no vetor de contagem, e o valor presente no índice**
3. **Subtraia 1**
4. **Insira o valor do item 1 no índice calculado no item 3**

Mas antes de tudo, por que será que é preciso subtrair 1? Ora, lembra quando foi dito no exercício acima que há um padrão entre o índice final e o vetor de contagem? Pois bem, **O ÍNDICE FINAL SEMPRE SERÁ O VALOR ENCONTRADO NO VETOR DE CONTAGEM, SUBTRAIDO POR 1**. Agora você sabe porque.

Ficou confuso? Calma, vamos simular para o nosso vetor. A imagem abaixo pode ajudar.

![](passo4.webp)

???Exercício
Use a *receita* para ordenar o vetor original ```c {2,1,4,3,0}``` (apenas o primeiro elemento)
:::Gabarito
1. **Pegue o primeiro elemento do vetor original**: 2
2. **Encontre seu índice no vetor de contagem, e o valor presente no índice**: ```count[2] = 3```
3. **Subtraia 1**: 3 - 1 = 2
4. **Insira o valor do item 1 no índice calculado no item 3**: ```v[2] = 2```

```
antes: v[] = {2,1,4,3,0}
depois: v[] = {4,1,2,3,0}
```
:::
???

Se você entendeu a passagem acima, concentre-se no próximo exercício. As chances de acertar são altas, mas as de errar também!

???Exercício
Utilizando a *receita*, simule o resto do Couting Sort.
:::Gabarito
1. **Pegue o segundo elemento do vetor original**: 1
2. **Encontre seu índice no vetor de contagem, e o valor presente no índice**: ```count[1] = 2```
3. **Subtraia 1**: 2 - 1 = 1
4. **Insira o valor do item 1 no índice calculado no item 3**: ```v[1] = 1```

```
antes: v[] = {4,1,2,3,0}
depois: v[] = {4,1,2,3,0}
```
------------------------------------
1. **Pegue o terceiro elemento do vetor original**: 4
2. **Encontre seu índice no vetor de contagem, e o valor presente no índice**: ```count[4] = 5```
3. **Subtraia 1**: 5 - 1 = 4
4. **Insira o valor do item 1 no índice calculado no item 3**: ```v[4] = 4```

```
antes: v[] = {4,1,2,3,0}
depois: v[] = {0,1,2,3,4}
```
------------------------------------
*Perceba que o vetor já está ordenado, mas apenas nesse caso específico. Não passamos ainda por todos os elementos.*
1. **Pegue o quarto elemento do vetor original**: 3
2. **Encontre seu índice no vetor de contagem, e o valor presente no índice**: ```count[3] = 4```
3. **Subtraia 1**: 4 - 1 = 3
4. **Insira o valor do item 1 no índice calculado no item 3**: ```v[3] = 3```

```
antes: v[] = {0,1,2,3,4}
depois: v[] = {0,1,2,3,4}
```
------------------------------------
*Perceba que o vetor já está ordenado, mas apenas nesse caso específico. Não passamos ainda por todos os elementos.*
1. **Pegue o quinto elemento do vetor original**: 0
2. **Encontre seu índice no vetor de contagem, e o valor presente no índice**: ```count[0] = 1```
3. **Subtraia 1**: 1 - 1 = 0
4. **Insira o valor do item 1 no índice calculado no item 3**: ```v[0] = 0```

```
antes: v[] = {0,1,2,3,4}
depois: v[] = {0,1,2,3,4}
```
----------------------------------
:::
???

Fez sentido agora? Bom, mas ainda falta fecharmos um pequeno detalhe.

???Desafio
Tente implementar o código da receita em C.

{red}(**Dica:**) Por que não varrer o loop do final até o começo, ou seja, de maneira decrescente?
:::Gabarito
```c
for (int i = n - 1; i >= 0; i--) {
    count[v[i]] - 1 = v[i];
    count[v[i]]--;
  }
```
:::
???

Vamos juntar tudo. O código final ficaria dessa maneira:
```c
void countingSort(int v[], int n) {

  int max = v[0];
  for (int i = 1; i < n; i++) {
    if (v[i] > max)
      max = array[i];
  }

  int count[max+1];

  for (int i = 0; i <= max; i++) {
    count[i] = 0;
  }

  for (int i = 0; i < n; i++) {
    count[v[i]]++;
  }

  for (int i = 1; i <= max; i++) {
    count[i] += count[i - 1];
  }

  for (int i = n - 1; i >= 0; i--) {
    count[v[i]] - 1 = v[i];
    count[v[i]]--;
  }
}
```

Complexidade e Exemplos de Aplicação
---------

O algoritmo é considerado eficiente para situações nas quais o alcance (range) do input não seja muito maior que a quantidade de objetos dentro do vetor inserido.

???Exercício
Levando em conta a frase acima, você consegue estimar a complexidade do algoritmo?

{red}(**Dica:** Lembre-se do *k*, citado no começo do Handout.)
:::Gabarito
A complexidade é *O(n + k)*. 

Relembrando a definição de k: é o número possivel de valores diferentes que o elemento do vetor pode assumir. 

Ou seja, é por isso que o range do algoritmo não deve ser muito alto! A complexidade aumenta!
:::
???

Os melhores algoritmos de comparação são *O(n log(n))* e por isso é melhor que os não quadráticos e quadráticos.

A tabela abaixo indica algumas características do Counting Sort:

| Característica|  |
|----------|----------|
| **Complexidade**      | *O(n + k)*        |
| **Estável**          | {green}(Sim)         |
| **Memória**        |  {red}(Ruim)       |

Sobre a *memória*, conforme dito no começo do Handout, esse algoritmo larga atrás dos outros devido ao uso do vetor de contagem. Pense que, quanto maior o elemento máximo do vetor que deve ser ordenado, mais memória ocupará!


???Exercício
Tendo isso em mente, qual seria uma possível aplicação para o Counting Sort?
:::Gabarito
Uma boa aplicação para o Counting Sort é para ordenar caracteres, pois não tem muitas variações (há apenas 26 caracteres disponíveis no alfabeto). 
:::
???

Vamos ver se você entendeu....

???Exercício
Em quais dos seguintes casos o Counting Sort seria eficaz e qual não seria? Tente pensar 

- Ordenar letras de uma palavra
- Ordenar números de CPFs do Brasil em ordem crescente
:::Gabarito
NO primeiro caso, pois não há grandes variações entre as letras (há apenas 26 letras no alfabeto). O range do segundo caso seria muito alto, pois os números de CPFs são muito grandes (possuem 11 dígitos) e podem variar muito entre si! Então não adiantaria.
:::
???