<properties
	pageTitle="Coletando Dados para Treinar seu Modelo: API de Recomendações do Aprendizado de Máquina | Microsoft Azure"
	description="Recomendações do Aprendizado de Máquina do Azure - Coletando Dados para Treinar seu Modelo"
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
	ms.date="08/31/2016"
	ms.author="luisca"/>

#  Coletando Dados para Treinar seu Modelo #

A API de Recomendações aprende com suas transações anteriores para localizar quais itens devem ser recomendados para um usuário específico.

Depois de criar um modelo, você precisará fornecer duas partes de informações antes de poder realizar qualquer treinamento: um arquivo de catálogo e os dados de uso.

>   Se você ainda não fez isso, incentivamos que termine o [guia de início rápido](cognitive-services-recommendations-quick-start.md).


## Dados do Catálogo ##

### Formato do arquivo de catálogo ###

O arquivo de catálogo contém informações sobre os itens oferecidos ao cliente. Os dados do catálogo devem seguir o seguinte formato:

- Sem recursos – `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Com recursos – `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### Linhas de Exemplo em um Arquivo de Catálogo

Sem recursos:

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Com recursos:

    AAA04294,Office Language Pack Online DwnLd,Office, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia, hardwaretype=mobile

#### Detalhes do formato

| Nome | Obrigatório | Tipo | Descrição |
|:---|:---|:---|:---|
| Id do item |Sim | [A-z], [a-z], [0-9], [\_] &#40;Underscore&#41;, [-] &#40;Dash&#41;<br> Comprimento máximo: 50 | Identificador exclusivo de um item |
| Nome do Item | Sim | Qualquer caractere alfanumérico<br> Comprimento máximo: 255 | Nome do item. |
| Categoria do Item | Sim | Qualquer caractere alfanumérico <br> Comprimento máximo: 255 | Categoria à qual este item pertence (por exemplo, Livros de Culinária, Drama...); pode estar vazia. |
| Descrição | Não, a menos que os recursos estejam presentes (mas pode estar vazia) | Qualquer caractere alfanumérico <br> Comprimento máximo: 4000 | Descrição deste item. |
| Lista de recursos | Não | Qualquer caractere alfanumérico <br> Comprimento máximo: 4.000; Número máximo de recursos: 20 | Lista separada por vírgulas de nome do recurso = valor do recurso que pode ser usada para aperfeiçoar a recomendação.|

#### Carregando um arquivo de Catálogo

Veja a [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) para carregar um arquivo de catálogo.

Observe que o conteúdo do arquivo de catálogo deve ser passado como o corpo da solicitação.

Se você carregar vários arquivos de catálogo para o mesmo modelo com várias chamadas, inseriremos apenas os novos itens de catálogo. Os itens existentes permanecerão com os valores originais. Você não pode atualizar os dados do catálogo usando este método.

>   Observação: o tamanho máximo do arquivo é de 200 MB. O número máximo de itens no catálogo com suporte é de 100.000 itens.


## Por que adicionar recursos ao catálogo?

O mecanismo de recomendações cria um modelo estatístico que informa quais itens provavelmente serão apreciados ou comprados por um cliente. Quando você tem um novo produto com o qual nunca interagiu, não é possível criar um modelo com as ocorrências apenas. Digamos que você inicie uma nova oferta de "violinos para crianças" em sua loja; como você nunca vendeu esse violino antes, não é possível dizer quais outros itens recomendar com ele.

Dito isso, se o mecanismo conhece as informações sobre esse violino (ou seja, ele é um instrumento musical, é para crianças de 7 a 10 anos, não é um violino caro etc.), então, ele pode aprender com outros produtos com recursos semelhantes. Por exemplo, você vendeu o violino no passado e geralmente as pessoas que o compram tendem a comprar CDs de música clássica e suportes para partituras. O sistema pode encontrar essas conexões entre os recursos e fornecer recomendações com base nos recursos, enquanto o novo violino tem pouco uso.

Os recursos são importados como parte dos dados do catálogo e sua classificação (ou a importância do recurso no modelo) é associada quando é feita uma compilação da classificação. A classificação de recursos pode mudar de acordo com o padrão dos dados de uso e tipo de itens. Mas, para uso/itens consistentes, a classificação deve ter apenas pequenas flutuações. A classificação de recursos é um número não negativo. O número 0 significa que o recurso não foi classificado (acontece se você invocar essa API antes da conclusão da primeira compilação de classificação). A data em que a classificação foi atribuída é chamada de atualização da pontuação.

Para usar os recursos como parte da compilação, você precisa:

1. Verificar se o catálogo tem recursos quando você o carrega.

2. Inicializar uma compilação de classificação. Isso fará a análise da importância/classificação dos recursos.

3. Disparar uma compilação das recomendações, definir os seguintes parâmetros de compilação: defina useFeaturesInModel para true, allowColdItemPlacement para true e modelingFeatureList deve ser definido para a lista separada por vírgulas dos recursos que você deseja usar para aprimorar seu modelo. Consulte [Parâmetros do tipo para compilar as recomendações](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0) para obter mais informações.


## Dados de uso ##
Um arquivo de uso contém informações sobre como esses itens são usados ou as transações de sua empresa.

#### Detalhes do Formato de uso
Um arquivo de uso é um arquivo CSV (valores separados por vírgula) no qual cada linha em um arquivo de uso representa uma interação entre um usuário e um item. Cada linha é formatada da seguinte maneira:<br> `<User Id>,<Item Id>,<Time>,[<Event>]`



| Nome | Obrigatório | Tipo | Descrição
|-------|------------|------|---------------
|Id de usuário| Sim|[A-z], [a-z], [0-9], [\_] &#40;Underscore&#41;, [-] &#40;Dash&#41;<br> Comprimento máximo: 255 |Identificador exclusivo de um usuário.
|Id do item|Sim|[A-z], [a-z], [0-9], [&#95;] &#40;Underscore&#41;, [-] &#40;Dash&#41;<br> Comprimento máximo: 50|Identificador exclusivo de um item
|Hora|Sim|Data no formato: AAAA/MM/DDTHH:MM:SS (por exemplo, 2013/06/20T10:00:00)|Hora dos dados.
|Evento|Não | Um dos seguintes:<br>• clique em<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Compra| O tipo de transação. |

#### Linhas de Exemplo em um Arquivo de Uso

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### Carregar um arquivo de uso

Veja a [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) para carregar os arquivos de uso. Observe que você precisa passar o conteúdo do arquivo de uso como o corpo da chamada HTTP.

>  Observação:

>  Tamanho máximo do arquivo: 200 MB. Você pode carregar vários arquivos de uso.

>  Você precisa carregar um arquivo de catálogo antes de começar a adicionar dados de uso ao seu modelo. Apenas os itens no arquivo de catálogo serão usados durante a fase de treinamento. Todos os outros itens serão ignorados.

## Qual é a quantidade de dados de que você precisa?

A qualidade do seu modelo é muito dependente da qualidade e da quantidade de dados. O sistema aprende quando os usuários compram itens diferentes (Chamamos isso de ocorrências conjuntas). Para as compilações FBT, também é importante saber quais itens são comprados nas mesmas transações.

Uma boa regra é que a maioria dos itens tenha 20 transações ou mais, portanto, se você tivesse 10.000 itens no catálogo, recomendaríamos que tivesse pelo menos 20 vezes esse número de transações, ou cerca de 200.000 transações. Mais uma vez, essa é uma apenas uma boa regra. Você precisará fazer experiências com seus dados.

Depois de criar um modelo, você poderá executar uma [avaliação offline](cognitive-services-recommendations-buildtypes.md) para verificar o quanto seu modelo provavelmente será bem executado.

<!---HONumber=AcomDC_0831_2016-->