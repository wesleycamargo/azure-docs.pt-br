<properties
	pageTitle="Guia de início rápido: API de Recomendações de Aprendizado de Máquina | Microsoft Azure"
	description="Recomendações do aprendizado de máquina do Azure - guia de início rápido"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="luisca"/>

#  Tipos de compilação e qualidade do modelo #

<a name="TypeofBuilds"></a>
## Tipos de compilação com suporte ##

A API de Recomendações atualmente dá suporte a dois tipos de compilação: *recomendação* e *FBT*. Cada um é criado usando algoritmos diferentes e cada um tem pontos fortes diferentes. Este documento descreve cada uma dessas compilações e as técnicas para comparar a qualidade dos modelos gerados.

Se você ainda não tiver feito isso, recomendamos que conclua o [guia de início rápido](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### Tipo de build de recomendação ###

O tipo de compilação de recomendação usa a fatoração de matriz para fornecer recomendações. Ele gera vetores de [recurso latente](https://en.wikipedia.org/wiki/Latent_variable) com base em suas transações para descrever cada item e usa os vetores latentes para comparar itens semelhantes.

Se você treinar o modelo com base em compras feitas em sua loja de eletrônicos e fornecer um telefone Lumia 650 como a entrada para o modelo, o modelo retornará um conjunto de itens que tendem a ser comprados pelas pessoas que têm probabilidade comprar um telefone Lumia 650. Os itens não podem ser complementares. Neste exemplo, é possível que o modelo retorne outros telefones, pois as pessoas que gostam do 650 Lumia podem gostar de outros telefones.

O build de recomendação tem duas funcionalidades que o tornam atraente:

**A recomendação de compilação dá suporte ao posicionamento de *item sem* interesse**

Itens que não têm uso significativo são chamados de itens sem interesse. Por exemplo, se você receber uma remessa de um telefone que nunca vendeu antes, o sistema não poderá inferir recomendações para esse produto apenas em transações. Isso significa que o sistema deve aprender com informações sobre o produto em si.

Se quiser usar o posicionamento de itens sem interesse, você precisará fornecer informações de recursos para cada um dos itens no catálogo. Veja a seguir a aparência que as primeiras linhas do catálogo podem ter (anote o formato chave=valor para os recursos).

>6CX-00001,Surface Pro2, Surface,, Type=Hardware, Storage=128 GB, Memory=4G, Manufacturer=Microsoft

>73H-00013,Wake Xbox 360,Gaming, Type=Software, Language=English, Rating=Mature

>WAH-0F05,Minecraft Xbox 360,Gaming, * Type=Software, Language=Spanish, Rating=Youth

Você também precisa definir os seguintes parâmetros de compilação:

| Parâmetro de build | Observações
|------------------     |-----------
|*useFeaturesInModel* | Definido como **true**. Indica se os recursos podem ser usados para aperfeiçoar o modelo de recomendação.
|*allowColdItemPlacement* | Definido como **true**. Indica se a recomendação também enviar itens sem interesse através da similaridade de recursos.
| *modelingFeatureList* | Lista separada por vírgulas de nomes de recursos a serem usados na compilação de recomendação para melhorar as recomendações. Por exemplo, "Idioma,Armazenamento" no exemplo anterior.

A compilação de Recomendação dá suporte para as **recomendações de usuário**

Uma compilação de Recomendação dá suporte para as [recomendações de usuário](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Isso significa que pode fornecer recomendações personalizadas para usuários com base em seus históricos de transações. Para obter recomendações de usuário, você pode fornecer a ID de usuário ou o histórico recente de transações desse usuário.

Um exemplo clássico de onde talvez você queira aplicar recomendações de usuário é ao entrar na página de boas-vindas. Lá você pode promover o conteúdo que se aplica ao usuário específico.

Talvez você também queira aplicar um tipo de compilação de recomendações quando o usuário estiver prestes a fazer o check-out. Nesse ponto, você tem a lista de itens que o usuário está prestes a comprar e pode fornecer recomendações com base na cesta de mercado atual.

#### Recomendações de parâmetros de compilação

| Nome | 	Descrição |	 Tipo, <br> valores válidos <br> (valor padrão)
|-------|-------------------|------------------
| *NumberOfModelIterations* |	O número de iterações que o modelo executa é refletido pelo tempo de computação geral e a precisão do modelo. Quanto maior o número, mais preciso será o modelo, mas o tempo de computação leva mais tempo. |	 Inteiro, <br> 10 a 50 <br>(40)
| *NumberOfModelDimensions* |	O número de dimensões está relacionado ao número de recursos que o modelo tentará localizar em seus dados. Aumentar o número de dimensões possibilitará um melhor ajuste dos resultados em clusters menores. No entanto, ter muitas dimensões impedirá que o modelo localize correlações entre itens. |	Inteiro, <br> 10 a 40 <br>(20) |
| *ItemCutOffLowerBound* |	Define o número mínimo de pontos de uso nos quais um item deve estar para que seja considerado parte do modelo. |		Número inteiro, <br> 2 ou mais <br> (2) |
| *ItemCutOffUpperBound* | 	Define o número máximo de pontos de uso nos quais um item deve estar para que seja considerado no modelo. | Número inteiro, <br> 2 ou mais<br> (2147483647) |
|*UserCutOffLowerBound* |	Define o número mínimo de transações que um usuário deve ter realizado para ser considerado parte do modelo. |	Número inteiro, <br> 2 ou mais <br> (2)
| *UserCutOffUpperBound* |	Define o número máximo de transações que um usuário deve ter realizado para ser considerado no modelo. |	Número inteiro, <br> 2 ou mais <br> (2147483647)|
| *UseFeaturesInModel* |	Indica se os recursos podem ser usados para aperfeiçoar o modelo de recomendação. | 	 Booliano<br> Padrão: True
|*ModelingFeatureList* |	Lista separada por vírgulas de nomes de recursos a serem usados na compilação de recomendação para melhorar as recomendações. Depende de recursos que são importantes. |	Cadeia de caracteres, até 512 caracteres
| *AllowColdItemPlacement* |	Indica se a recomendação também enviar itens sem interesse através da similaridade de recursos. | Booliano<br> Padrão: False
| *EnableFeatureCorrelation* | Indica se os recursos podem ser usados no raciocínio. |	Booliano<br> Padrão: False
| *ReasoningFeatureList* |	Lista separada por vírgulas de nomes de recursos a serem usado em frases de raciocínio, como explicações de recomendação. Depende dos recursos que são importantes para os clientes. | Cadeia de caracteres, até 512 caracteres
| *EnableU2I* |	Habilite as recomendações personalizadas, também chamadas de recomendações de usuário para item (U2I). | Booliano<br> Padrão: True
|*EnableModelingInsights* |	Define se a avaliação offline deve ser executada para reunir informações de modelagem (ou seja, métricas de precisão e diversidade). Se for definido como true, um subconjunto dos dados não será usado para treinamento, pois precisará ser reservado para testes do modelo. Leia mais sobre [avaliações offline](#OfflineEvaluation). | Booliano<br> Padrão: False
| *SplitterStrategy* | Se habilitar informações de modelagem estiver definido como *true*, essa será a maneira como os dados deverão ser divididos para fins de avaliação. | Cadeia de caracteres, *RandomSplitter* ou *LastEventSplitter* <br>Padrão: RandomSplitter


<a name="FBTBuild"></a>
### Tipo de build FBT ###

A compilação FBT (frequentemente comprada junto) faz uma análise que conta o número de vezes em que dois ou três produtos diferentes ocorrem juntos. Em seguida, classifica os conjuntos com base em uma função de similaridade (**co-occurrences**, **Jaccard**, **lift**).

Pense em **Jaccard** e **lift** como maneiras de normalizar as coocorrências. Isso significa que os itens serão retornados somente se tiverem sido adquiridos junto com o item de semente.

Em nosso exemplo do telefone Lumia 650, um telefone X será retornado se o telefone X tiver sido adquirido na mesma sessão que o telefone Lumia 650. Como isso pode ser improvável, esperaríamos que itens complementares para o Lumia 650 fossem retornados. Por exemplo, um protetor de tela ou um adaptador de energia para o Lumia 650.

No momento, assume-se que dois itens foram comprados na mesma sessão se eles ocorrerem em uma transação com a mesma ID de usuário e carimbo de data/hora.

Compilações FBT não dão suporte a itens sem interesse, pois, por definição, esperam que dois itens sejam comprados na mesma transação. Embora as compilações FBT possam retornar conjuntos de itens (triplos), elas não dão suporte a recomendações personalizadas, pois elas aceitam um único item de semente como entrada.


#### Parâmetros de compilação FBT

| Nome | 	Descrição |		Tipo, <br> valores válidos <br> (valor padrão)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | O quanto o modelo é conservador. O número de co-ocorrências de itens a ser considerado para modelagem. | Inteiro, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Limita o número de itens em um conjunto frequente.| Inteiro <br> 2-3 <br> (2)
| *FbtMinimalScore* | Pontuação mínima que um conjunto frequente deve ter para ser incluído nos resultados retornados. Quanto maior, melhor. | Duplo <br> 0 e <br> acima (0)
| *FbtSimilarityFunction* | Define a função de semelhança a ser usada pela compilação. A comparação **lift** favorece a serendipidade, **co-occurrence** favorece a previsibilidade e **Jaccard** é uma boa combinação entre os dois. | Cadeia de caracteres, <br> <i>cooccurrence, lift, jaccard</i><br> Padrão: <i>jaccard</i>

<a name="SelectBuild"></a>
## Avaliação e seleção de compilação ##

Essas diretrizes podem ajudá-lo a determinar se você deve usar uma compilação de recomendações ou uma compilação FBT, mas não fornecem uma resposta definitiva em casos em que você pode usar qualquer uma delas. Além disso, mesmo se você souber que deseja usar um tipo de compilação FBT, ainda convém escolher **Jaccard** ou **lift** como a função de similaridade.

A melhor maneira de escolher entre duas compilações diferentes é testá-las no mundo real (avaliação online) e acompanhar taxa de conversão das diferentes compilações. A taxa de conversão pode ser medida com base nos cliques de recomendação, no número de compras de fato da apresentação de recomendações ou até mesmo os valores de compra reais quando as diferentes recomendações foram apresentadas. Você pode selecionar sua métrica de taxa de conversão com base no seu objetivo de negócios.

Em alguns casos, convém avaliar o modelo offline antes de colocá-lo em produção. Embora a avaliação offline não seja uma substituição para a avaliação online, ela pode servir como uma métrica.

<a name="OfflineEvaluation"></a>
## Avaliação offline  ##

O objetivo de uma avaliação offline é prever a precisão (número de usuários que comprarão um dos itens recomendados) e a diversidade de recomendações (o número de itens que são recomendados). Como parte da avaliação de métricas de precisão e a diversidade, o sistema encontra um exemplo de usuários e divide as transações desses usuários em dois grupos: o conjunto de dados de treinamento e o conjunto de dados de teste.

> [AZURE.NOTE] Para usar métricas offline, você deve ter carimbos de da/hora em seus dados de uso. Os dados de hora são necessários para dividir o uso corretamente entre conjuntos de dados de treinamento e teste.

> Além disso, a avaliação offline pode não gerar resultados para arquivos de pouco uso. Para que a avaliação seja completa, deve haver no mínimo 1.000 pontos de uso no conjunto de dados de teste.

<a name="Precision"></a>
### Precisão-em-k ###
A tabela a seguir representa a saída da avaliação offline de precisão em k.

| K | 1 | 2 | 3 | 	4 | 	5
|---|---|---|---|---|---|
|Percentual |	13\.75 |	18\.04 | 21 |	24\.31 |	26\.61
|Usuários no teste |	10\.000 |	10\.000 |	10\.000 |	10\.000 |	10\.000
|Usuários considerados |	10\.000 |	10\.000 |	10\.000 |	10\.000 |	10\.000
|Usuários não considerados |	0 |	0 |	0 |	0 |	0

#### K
Na tabela anterior, *k* representa o número de recomendações mostradas para o cliente. A tabela informa o seguinte: "Se, durante o período de teste, apenas uma recomendação foi exibida para os clientes, apenas 13.75 dos usuários adquiriram essa recomendação". Essa instrução baseia-se no pressuposto de que o modelo foi treinado com dados de compra. Outra maneira de dizer isso é que a precisão em 1 é de 13,75.

Você observará que, à medida que mais itens são mostrados para o cliente, a probabilidade de ele comprar um item recomendado aumenta. Para o experimento anterior, a probabilidade quase dobra para 26,61% quando cinco itens são recomendados.

#### Percentual
O percentual de usuários que interagiram com pelo menos uma das recomendações *k* é mostrado. O percentual é calculado dividindo o número de usuários que interagiram com pelo menos uma recomendação pelo número total de usuários considerados. Confira Usuários considerados para obter mais informações.

#### Usuários no teste
Os dados nessa linha representam o número total de usuários do conjunto de dados de teste.

#### Usuários considerados
Um usuário é considerado somente se o sistema recomendou pelo menos *k* itens com base no modelo gerado usando o conjunto de dados de treinamento.

#### Usuários não considerados
Os dados nessa linha representam os usuários não considerados. Os usuários que não receberam pelo menos *k* itens recomendados.

Usuário não considerado = usuários no teste – usuários considerados

<a name="Diversity"></a>
### Diversidade ###
As métricas de diversidade mede o tipo de itens recomendados. A tabela a seguir representa a saída da avaliação offline de diversidade.

|Bucket de percentil |	0-90| 90-99| 99-100
|------------------|--------|-------|---------
|Percentual | 34\.258 | 55\.127| 10\.615


Total de itens recomendados: 100.000

Itens exclusivos recomendados: 954

#### Buckets de percentil
Cada bucket de percentil é representado por um período (os valores mínimo e máximo no intervalo variam entre 0 e 100). Os itens perto de 100 são os mais populares e os itens próximos a 0 são menos populares. Por exemplo, se o valor percentual para o bucket de percentil 99-100 for 10,6, isso significará que 10,6% das recomendações retornaram somente o 1% superior de itens mais populares. O valor mínimo do bucket de percentil é inclusivo, e o valor máximo é exclusivo, com exceção de 100.
#### Itens exclusivos recomendados
A métrica de itens exclusivos recomendados mostra o número de itens distintos que foram retornados para avaliação.
#### Total de itens recomendados
A métrica de total de itens recomendados mostra o número de itens recomendados. Alguns itens podem ser duplicatas.

<a name="ImplementingEvaluation"></a>
### Métricas de avaliação offline ###
As métricas offline de precisão e diversidade podem ser úteis quando você seleciona qual versão usar. No momento do compilação, como parte dos parâmetros de compilação de recomendação ou FBT:

-	Defina o parâmetro de compilação *enableModelingInsights* como **true**.
-	Opcionalmente, selecione *splitterStrategy* (*RandomSplitter* ou *LastEventSplitter*). *RandomSplitter* divide os dados de uso nos conjuntos de dados de teste e de treinamento com base na porcentagem de teste *randomSplitterParameters* determinada e em valores de semente aleatórios. *LastEventSplitter* divide os dados de uso nos conjuntos de dados de treinamento e de teste com base na última transação de cada usuário.

Isso vai disparar um compilação que usa apenas um subconjunto dos dados para treinamento e usa o restante dos dados para calcular métricas de avaliação. Após o compilação ser concluído, para obter a saída da avaliação, você precisa chamar a [API para obter métricas de compilação](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f), passando as respectivas *modelId* e *compilaçãoId*.

 A seguir está a saída JSON para a avaliação de exemplo.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }

<!---HONumber=AcomDC_0914_2016-->