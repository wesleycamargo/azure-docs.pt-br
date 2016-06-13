<properties
	pageTitle="Guia de início rápido: API de Recomendações de Aprendizado de Máquina | Microsoft Azure"
	description="Recomendações do aprendizado de máquina do Azure - guia de início rápido"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="luisca"/>

#  Tipos de build e qualidade do modelo #

<a name="TypeofBuilds"></a>
## Que tipo de build de recomendações devo usar? ##

No momento, damos suporte a dois tipos de build: builds de *Recomendação* e *FBT*. Cada um deles é criado usando algoritmos diferentes e tem diferentes vantagens.

<a name="RecommendationBuild"></a>
### Tipo de build de recomendação ###

O tipo de build de *recomendação* usa a fatoração de matriz para fornecer recomendações. A versão curta é que ele usará as transações do usuário para gerar vetores de [funcionalidade latente](https://en.wikipedia.org/wiki/Latent_variable) para descrever cada item e, em seguida, usar esses vetores latentes para comparar itens semelhantes.

Supondo que você treina o modelo com base em compras feitas em sua loja de eletrônicos e no momento da pontuação você fornece um telefone Lumia 650 como a entrada para o modelo, ele retornará um conjunto de itens que tendem a ser comprados pelas pessoas que têm probabilidade comprar um telefone Lumia 650. Observe que os itens podem não ser complementares. Por exemplo, neste exemplo, é possível que outros telefones sejam retornados, uma vez que as pessoas que gostam do Lumia 650 podem gostar de outros telefones.

O build de recomendação tem duas funcionalidades que o tornam atraente:

-	Ela dá suporte ao posicionamento de item sem interesse.

 *Itens sem interesse* é a terminologia usada para itens que não têm uso significativo. Por exemplo, imagine que você acabou de receber uma remessa do novo e excelente telefone Lumia. Como você nunca o vendeu no passado, o sistema não pode inferir recomendações para este produto em transações apenas. Isso significa que o sistema deve aprender com informações sobre o produto em si.

 Se você deseja usar o posicionamento de itens sem interesse, precisa fornecer informações de funcionalidades para cada um dos seus itens no catálogo, essa é a aparência que as primeiras linhas de seu catálogo podem ter (observe o formato chave=valor para as funcionalidades):

> 6CX-00001,Surface Pro2, Surface, Type=Hardware, Storage=128GB, Memory=4G, Manufacturer=Microsoft

> 73H-00013,Wake Xbox 360,Gaming, Type=Software, Language=English, Rating=Mature

> WAH-0F05,Minecraft Xbox 360,Gaming, * Type=Software, Language=Spanish, Rating=Youth

> ...

 Além disso, como parte dos parâmetros de build, você precisa definir os seguintes parâmetros de build:

| Parâmetro de build | Observações
|------------------     |-----------
|useFeaturesInModel | Definido como true. Indica se os recursos podem ser usados para aperfeiçoar o modelo de recomendação. 
|allowColdItemPlacement | Definido como true. Indica se a recomendação também enviar itens sem interesse através da similaridade de recursos.
| modelingFeatureList | Lista separada por vírgulas de nomes de recursos a serem usados na compilação de recomendação a fim de melhorar as recomendações. Por exemplo "Language,Storage" no exemplo acima.

-	Ele dá suporte a recomendações de usuário.

 Um build de Recomendação dá suporte a [recomendações de usuário.](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd) Isso significa que ele pode usar o histórico de transações de um usuário para fornecer recomendações personalizadas para esse usuário. Para obter recomendações de usuário, você pode fornecer a ID de usuário e/ou o histórico recente de transações desse usuário.

 Um exemplo clássico em que você talvez queira aplicar recomendações de usuário é quando o usuário primeiro faz logon em seu site/loja na página de boas-vindas. Lá você pode promover o conteúdo que se aplica ao usuário específico.
 
 Talvez você também queira aplicar um tipo de build Recomendações quando o usuário estiver prestes a fazer o check-out. Nesse ponto, você tem a lista de itens que o cliente está prestes a comprar e essa é sua chance para fornecer recomendações com base na cesta de compras atual.

<a name="FBTBuild"></a>
### Tipo de build FBT ###

*FBT* significa Frequentemente Comprados Juntos. O build FBT faz uma análise que conta o número de vezes em que dois ou três produtos diferentes ocorrem juntos, e, em seguida, classifica os conjuntos com base em uma função de similaridade (co-ocorrências, Jaccard, comparação de precisão).

Pense no Jaccard e na comparação de precisão como maneiras de normalizar as co-ocorrências. Isso significa que os itens serão retornados apenas se eles realmente tiverem sido adquiridos junto com o item de semente.

Em nosso exemplo do telefone Lumia 650, um telefone X será retornado se e somente se o telefone X tiver sido adquirido na mesma sessão que o telefone Lumia 650. Como isso pode ser improvável, esperaríamos que itens complementares para o Lumia 650 fossem retornados. Por exemplo, um protetor de tela ou um adaptador de energia para o Lumia 650.

No momento, assume-se que dois itens foram comprados na mesma sessão se eles ocorrerem em uma transação com a mesma ID de usuário e carimbo de data/hora.

As compilações FBT não dão suporte a itens sem interesse no momento, uma vez que elas, por definição, esperam que dois itens sejam realmente comprados na mesma transação. Embora as compilações FBT possam retornar conjuntos de itens (triplos), elas não dão suporte a recomendações personalizadas, já que elas aceitam um único item de semente como entrada.

<a name="SelectBuild"></a>
## Como seleciono o build exato a ser usado? ##

A orientação acima pode ser suficiente para que você possa determinar se deve usar um build de Recomendações ou um build FBT, mas não é uma resposta definitiva em casos em que você pode usar qualquer um deles. Além disso, mesmo se você souber que deseja usar um tipo de build FBT, ainda pode desejar usar Jaccard ou a comparação de precisão como a função de similaridade.

A melhor maneira de escolher entre duas compilações diferentes é realmente testá-las no mundo real (avaliação online) e acompanhar taxa de conversão das diferentes compilações. A taxa de conversão pode ser medida com base nos cliques de recomendação, no número de compras de fato da apresentação de recomendações ou até mesmo a quantidade de dinheiro real vendido quando as diferentes recomendações foram apresentadas. Você pode selecionar sua métrica de taxa de conversão com base no seu objetivo de negócios.

Em alguns casos, convém avaliar o modelo offline antes de colocá-lo em produção. Embora a avaliação offline não seja uma substituição para a avaliação online, ela pode servir como uma métrica.

<a name="OfflineEvaluation"></a>
## Avaliação offline  ##

O objetivo de uma avaliação offline é prever a precisão (número de usuários que realmente comprarão um dos itens recomendados) e a diversidade de recomendações (o número real de itens que são recomendados). Como parte da avaliação de métricas de precisão e a diversidade, o sistema encontra um exemplo de usuários e, em seguida, as transações desses usuários são divididas em dois grupos: o conjunto de dados de treinamento e o conjunto de dados de teste.

> Observação:
>
> Para usar métricas offline, é necessário ter carimbos de hora em seus dados de uso. (Uma vez que os dados de hora são necessários para dividir o uso corretamente entre conjuntos de dados de treinamento e teste).

> Além disso, avaliação offline pode não produzir resultados para arquivos de uso pequenos, simplesmente porque para a avaliação ser completa, esperamos um mínimo de 1000 pontos de uso no conjunto de dados de teste.

<a name="Precision"></a>
### Precisão em K ###
A tabela a seguir representa a saída da avaliação offline de precisão em k.

| K | 1 | 2 | 3 | 	4 | 	5
|---|---|---|---|---|---|
|Percentual |	13\.75 |	18\.04 | 21 |	24\.31 |	26\.61
|Usuários no teste |	10\.000 |	10\.000 |	10\.000 |	10\.000 |	10\.000
|Usuários considerados |	10\.000 |	10\.000 |	10\.000 |	10\.000 |	10\.000
|Usuários não considerados |	0 |	0 |	0 |	0 |	0

#### K
Na tabela acima, *K* representa o número de recomendações mostradas para o cliente. Portanto, a tabela é lida da seguinte forma: "Se durante o período de teste, apenas uma recomendação seria exibida para os clientes, somente 13,75 dos usuários realmente teriam comprado aquela recomendação". (Supondo que o modelo foi treinado com dados de compra). Outra maneira de dizer isso é que a "precisão em 1" é de 13,75.

Você observará que, à medida que mais itens são mostrados para o cliente, a probabilidade de ele comprar um item recomendado aumenta. Para o experimento acima, a probabilidade quase dobra para 26,61% quando cinco itens são recomendados.

#### Percentual
O percentual de usuários que interagiam com pelo menos uma das recomendações K mostradas. O percentual é calculado dividindo o número de usuários que interagiam com pelo menos um recomendação pelo número total de usuários considerados. (Consulte a definição de usuários considerados).

#### Usuários no teste
O número total de usuários do conjunto de dados de teste.

#### Usuários considerados
Um usuário é considerado somente se o sistema recomendou pelo menos K itens com base no modelo gerado usando o conjunto de dados de treinamento.

#### Usuários não considerados
Quaisquer usuários não considerados, os usuários que não recebera pelo menos K itens recomendados.

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
Cada bucket de percentil é representado por um período (valores mínimo/máximo que variam entre 0 e 100). Os itens perto de 100 são os mais populares e os itens próximos a 0 são menos populares. Por exemplo, se o valor percentual para o bucket de percentil 99-100 for 10,6, isso significará que 10,6% das recomendações retornaram somente o 1% superior de itens mais populares. O valor mínimo de bucket de percentil é inclusivo e o valor máximo é exclusivo, exceto para 100.
#### Itens exclusivos recomendados
Número de itens distintos que foram retornados para avaliação.
#### Total de itens recomendados
O número total de itens recomendados. (Alguns podem estar duplicados)


<a name="ImplementingEvaluation"></a>
### Como obter avaliações offline? ###
As métricas offline de diversidade e precisão podem ser úteis para você na seleção de qual build usar. Para obter métricas offline, você precisa fazer o seguinte:

No momento do build, como parte dos parâmetros de build de Recomendação ou FBT:
1.	Defina o parâmetro de build enableModelingInsights como true.

2.	Opcionalmente, você pode selecionar o *splitterStrategy* (*RandomSplitter* ou *LastEventSplitter*). *RandomSplitter* divide os dados de uso nos conjuntos de dados de teste e de treinamento com base na porcentagem de teste *randomSplitterParameters* determinada e em valores de semente aleatórios. *LastEventSplitter* divide os dados de uso nos conjuntos de dados de treinamento e de teste com base na última transação de cada usuário.

Isso vai disparar um build que usa apenas um subconjunto dos dados para treinamento e o restante dos dados é usado para calcular métricas de avaliação. Após o build ser concluído, para obter a saída da avaliação, você apenas precisa chamar a [API para obter métricas de build](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/573e43bb3e9d4627a8c4bd3e/console), passando as respectivas *modelId* e *buildId*.

 A seguir está a saída JSON para a avaliação de exemplo que executamos:


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

<!---HONumber=AcomDC_0601_2016-->