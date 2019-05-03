---
title: Trabalhando com projeções em um repositório de dados de Conhecimento - Azure Search
description: Salvar e formatar seus dados enriquecidos do pipeline de indexação do AI para uso em outros cenários além da pesquisa
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.custom: seomay2019
ms.openlocfilehash: 3ab5ffafd1b20eb0e3e453d3e730840baf9233e1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028359"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Trabalhando com projeções em um repositório de dados de Conhecimento no Azure Search

O Azure Search permite o enriquecimento de conteúdo por meio de habilidades cognitivas de inteligência Artificial e habilidades personalizadas como parte da indexação. Aprimoramentos de adicionam estrutura a documentos e facilitar a pesquisa mais eficiente. Em muitos casos, os documentos enriquecidos são úteis para outros cenários além da pesquisa, por exemplo, para mineração de dados de Conhecimento.

Projeções, um componente do [repositório de dados de Conhecimento (visualização)](knowledge-store-concept-intro.md), modos de exibição de documentos enriquecidos que podem ser salvos no armazenamento físico para fins de mineração de dados de Conhecimento. Uma projeção permite que você "projeto" seus dados em uma forma que se alinha com suas necessidades, preservando relações para que ferramentas como o Power BI podem ler os dados sem esforço adicional. 

Projeções podem ser tabulares, com dados armazenados em linhas e colunas no armazenamento de tabelas do Azure, ou objetos JSON armazenados no armazenamento de BLOBs do Azure. Você pode definir várias projeções de dados quando ele está sendo aprimorado. Isso é útil quando você deseja que os mesmos dados em relação à forma para casos de uso individual. 

O armazenamento de dados de Conhecimento dá suporte a dois tipos de projeções de:

+ **Tabelas**: Para dados que é melhor representados como linhas e colunas, as projeções de tabela permitem definir uma projeção ou forma esquematizada no armazenamento de tabelas. 

+ **Objetos**: Quando você precisar de uma representação JSON de seus dados e aprimoramentos, as projeções de objeto são salvos como blobs.

Para ver projeções definidas em contexto, percorrer [como começar com o armazenamento de dados de Conhecimento](knowledge-store-howto.md)

## <a name="projection-groups"></a>Grupos de projeção

Em alguns casos, você precisa projetar seu dados enriquecidos em formas diferentes para atender aos objetivos diferentes. O armazenamento de dados de conhecimento permite que você defina vários grupos de projeções. Grupos de projeção têm as seguintes características principais de exclusividade mútua e relacionada.

### <a name="mutually-exclusivity"></a>Exclusividade mutuamente

Todo o conteúdo projetado em um único grupo é independente dos dados projetados em outros grupos de projeção. Isso significa que você pode ter os mesmos dados em forma de maneira diferente, mas repetido em cada grupo de projeção. 

Uma restrição imposta nos grupos de projeção é a exclusividade mútua dos tipos de projeção com um grupo de projeção. Você só pode definir as projeções de tabela ou projeções de objeto dentro de um único grupo. Se você quiser tabelas e objetos, defina um grupo de projeção para tabelas e um segundo grupo de projeção para objetos.

### <a name="relatedness"></a>Relacionada

Todo o conteúdo projetado dentro de um grupo único projeção preserva as relações nos dados. As relações se baseiam em uma chave gerada e cada nó filho retém uma referência para o nó pai. As relações não abrangem grupos de projeção e tabelas ou objetos criados em um grupo de projeção não tem nenhuma relação com dados gerados em outros grupos de projeção.

## <a name="input-shaping"></a>Formatação de entrada
Obtendo seus dados na forma correta ou estrutura é o uso da chave para efetivo, tabelas ou objetos. A capacidade de formatar ou estruturar seus dados com base em como você planeja acessar e usá-lo é uma capacidade chave exposta como o **Shaper** habilidade dentro do conjunto de qualificações.  

As projeções são mais fáceis de definir quando você tem um objeto na árvore de enriquecimento que correspondem ao esquema da projeção. Atualizada [habilidade Shaper](cognitive-search-skill-shaper.md) permite que você Componha um objeto de nós diferentes da árvore enriquecimento e pai-los em um novo nó. O **Shaper** habilidade permite que você defina tipos complexos com objetos aninhados.

Quando você tem uma nova forma definida que contém todos os elementos que você precisa projetem, agora você pode usar esta forma como a origem de suas projeções ou como uma entrada para outra habilidade.

## <a name="table-projections"></a>Projeções de tabela

Pois ele torna mais fácil a importação, recomendamos as projeções de tabela para exploração de dados com o Power BI. Além disso, as projeções de tabela permitem de alterar a cardinalidade entre a relação de tabela de alteração. 

Você pode projetar um único documento no índice em várias tabelas, preservando as relações. Ao projetar a várias tabelas, a forma completa será projetada em cada tabela, a menos que um nó filho é a origem da outra tabela dentro do mesmo grupo.

### <a name="defining-a-table-projection"></a>Definindo uma projeção de tabela

Ao definir uma projeção de tabela dentro do `knowledgeStore` elemento do conjunto de qualificações, iniciar o mapeamento de um nó na árvore de enriquecimento para a origem da tabela. Normalmente, esse nó é a saída de um **Shaper** habilidades que você adicionou à lista de habilidades para produzir uma forma específica que você precisa para projetar em tabelas. O nó que você optar por projeto pode ser fatiado ao projeto em várias tabelas. A definição de tabelas é uma lista de tabelas que você deseja que o projeto. 

Cada tabela exige três propriedades:

+ tableName: O nome da tabela no armazenamento do Azure.

+ generatedKeyName: O nome da coluna da chave que identifica exclusivamente essa linha.

+ source: O nó da árvore de enriquecimento você estiver fornecendo seus aprimoramentos do. Isso geralmente é a saída de um formatador, mas poderia ser a saída de qualquer uma das habilidades.

Aqui está um exemplo de projeções de tabela.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [
            
          ]
        }
      ]
    }
}
```
Conforme demonstrado neste exemplo, as frases-chave e as entidades são modeladas em tabelas diferentes e conterá uma referência para o pai (MainTable) para cada linha. 

A ilustração a seguir é uma referência para o exercício Caselaw [como começar com o armazenamento de dados de Conhecimento](knowledge-store-howto.md). Em um cenário em que um caso tenha várias opiniões e cada opinião é aprimorada por meio da identificação de entidades contidas nele, você pode modelar as projeções, conforme mostrado aqui.

![Entidades e relações em tabelas](media/knowledge-store-projection-overview/TableRelationships.png "modelando as relações em projeções de tabela")

## <a name="object-projections"></a>Projeções de objeto

As projeções de objeto são representações de JSON da árvore de enriquecimento que podem se originar de qualquer nó. Em muitos casos, o mesmo **Shaper** habilidade que cria uma projeção de tabela pode ser usada para gerar uma projeção de objeto. 

```json
{
 
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        }
      ]
    }
}
```

Gerar uma projeção de objeto requer alguns atributos de objeto específicos:

+ storageContainer: O contêiner onde os objetos serão salvos
+ source: O caminho para o nó da árvore de enriquecimento que é a raiz da projeção
+ Chave: Um caminho que representa uma chave exclusiva para o objeto a ser armazenado. Ele será usado para criar o nome do blob no contêiner.

## <a name="projection-lifecycle"></a>Ciclo de vida de projeção

Suas projeções têm um ciclo de vida que esteja vinculado à fonte de dados na fonte de dados. Como seus dados são atualizados e reindexados, suas projeções são atualizadas com os resultados de aprimoramentos de garantir que suas projeções são eventualmente consistentes com os dados na fonte de dados. As projeções herdam a política de exclusão que você configurou para seu índice. 

## <a name="using-projections"></a>Usar projeções

Depois que o indexador for executado, você pode ler os dados projetados no contêineres ou tabelas especificadas por meio de projeções. 

Para análise, a exploração no Power BI é tão simple quanto definir armazenamento de tabelas do Azure como fonte de dados. Com muita facilidade, você pode criar um conjunto de visualizações em seus dados, aproveitando as relações dentro.

Como alternativa, se você precisar usar os dados enriquecidos em um pipeline de ciência de dados, você poderia [carregar os dados de blobs em um DataFrame Pandas](../machine-learning/team-data-science-process/explore-data-blob.md).

Por fim, se você precisar exportar seus dados do armazenamento de dados de Conhecimento, o Azure Data Factory tem conectores para exportar os dados e land-lo no banco de dados de sua escolha. 

## <a name="next-steps"></a>Próximas etapas

Como uma próxima etapa, crie seu primeiro armazenamento de dados de conhecimento usando dados de exemplo e instruções.

> [!div class="nextstepaction"]
> [Como criar um repositório de dados de Conhecimento](knowledge-store-howto.md).