---
title: Criar Conjunto de qualificações (REST api-version=2017-11-11-Versão prévia) - Azure Search | Microsoft Docs
description: Um conjunto de qualificações é uma coleção de qualificações cognitivas que compõem um pipeline de enriquecimento.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786905"
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>Criar conjunto de qualificações (api-version=2017-11-11-Versão prévia)

**Aplica-se a:** api-version-2017-11-11-Versão prévia

Um conjunto de qualificações é uma coleção de qualificações cognitivas usado para processamento de idioma natural e outras transformações. As habilidades incluem a extração de entidade nomeada, extração de frase-chave, agrupamento de texto em páginas lógicas, entre outros.

Para usar o conjunto de qualificações, referencie-o em um indexador da pesquisa do Microsoft Azure e, em seguida, execute o indexador para importar dados, invocar as transformações e enriquecimento e mapear os campos de saída para um índice. Um conjunto de qualificações é o recurso de alto nível, mas ele estará operacional apenas no processamento do indexador. Como um recurso de alto nível, você pode criar um conjunto de qualificações uma vez e, em seguida, referenciá-lo em vários indexadores. 

Um conjunto de qualificações é expresso na pesquisa do Microsoft Azure por meio de um HTTP PUT ou solicitação POST. Para PUT, o corpo da solicitação é um esquema JSON que especifica quais habilidades são invocadas. As habilidades são encadeadas por meio de associações de entrada e saída, onde a saída de uma transformação torna-se a entrada para outra.

Um conjunto de qualificações precisa ter pelo menos uma habilidade. Não há nenhum limite teórico no número máximo de habilidades, mas as três a cinco é uma configuração comum.  


> [!NOTE]
> O recurso de Pesquisa Cognitiva está em visualização pública e a execução de qualificação é oferecida gratuitamente. Posteriormente, o preço desse recurso será anunciado.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>Solicitação  
 HTTPS é necessário para todas as solicitações de serviço. A solicitação **Criar conjunto de qualificações** pode ser criada usando um método PUT, com o nome do conjunto de qualificações como parte da URL. Se o conjunto de qualificação não existir, ele será criado. Se já existir, ele será atualizado para a nova definição. Observe que você só pode colocar um conjunto de qualificações por vez.  

 O conjunto de qualificações deve atender aos seguintes requisitos:

- Estar em letras minúsculas
- Deve começar e terminar com uma letra ou número
- Não ter barras ou pontos
- Tem menos de 128 caracteres 

Depois de iniciar o nome do conjunto de qualificações com uma letra ou número, o restante do nome pode incluir qualquer letra, número e traços, desde que os traços não sejam consecutivos.  

O parâmetro **api-version** é obrigatório A única versão disponível é `2017-11-11-Preview`. Consulte [versões da API no Azure Search](https://go.microsoft.com/fwlink/?linkid=834796) para obter uma lista de todas as versões. 


### <a name="request-headers"></a>Cabeçalhos da solicitação  

 A tabela a seguir descreve os cabeçalhos de solicitação necessários e opcionais  

|Cabeçalho da Solicitação|DESCRIÇÃO|  
|--------------------|-----------------|  
|*Tipo de Conteúdo:*|Obrigatório. Defina-o como `application/json`|  
|*api-key:*|Obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço Search. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Criar Conjunto de qualificações** deve incluir um cabeçalho de `api-key` definido como sua chave de administração (em vez de uma chave de consulta).|  

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.  

### <a name="request-body-syntax"></a>Sintaxe de corpo da solicitação  

O corpo da solicitação contém a definição de conjunto de qualificações, consistindo em uma ou mais qualificações completamente especificadas, bem como parâmetros de nome e uma descrição opcionais.  

A sintaxe para estruturar a carga da solicitação é indicada a seguir. Uma solicitação de modelo é fornecida depois neste artigo e também em [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md).  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>Exemplo de solicitação
 O exemplo a seguir cria um conjunto de qualificações usado para enriquecer uma coleção de documentos financeiros.

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

O corpo da solicitação é um documento JSON. Esse conjunto de qualificações específico usa duas qualificações assincronicamente, processando independente a substância das `contents` duas transformações diferentes. Como alternativa, você pode direcionar a saída de uma transformação para ser a entrada de outra. Para obter mais informações, confira [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md).

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ]
}
```

## <a name="response"></a>Response  

 Para uma solicitação bem-sucedida, você deverá ver o código de status “201 (Criado)”.  

 Por padrão, o corpo da resposta conterá o JSON para a definição de índice que foi criado. Porém, se o cabeçalho da solicitação preferido for definido como retorno=mínimo , o corpo da resposta estará vazio e o código de status de êxito será "204 Sem Conteúdo" em vez de "201 Criado". Isso ocorre independentemente de PUT ou POST ter sido usado para criar o conjunto de qualificações.   

## <a name="see-also"></a>Consulte também

+ [ Visão geral da pesquisa cognitiva](cognitive-search-concept-intro.md)
+ [Início Rápido: experimente a pesquisa cognitiva](cognitive-search-quickstart-blob.md)
+ [Tutorial: indexação enriquecida de blobs do Azure](cognitive-search-tutorial-blob.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Como mapear campos de mapa](cognitive-search-output-field-mapping.md)
+ [Como definir uma interface personalizada](cognitive-search-custom-skill-interface.md)
+ [Exemplo: criar uma habilidade personalizada](cognitive-search-create-custom-skill-example.md)
+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)