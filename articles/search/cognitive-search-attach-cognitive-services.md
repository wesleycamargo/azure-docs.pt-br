---
title: Anexar o Serviços Cognitivos ao seu conjunto de habilidades – Azure Search
description: Instruções para anexar uma assinatura todos-em-um dos Serviços Cognitivos a um conjunto de habilidades cognitivo
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317229"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>Associar um recurso dos Serviços Cognitivos a um conjunto de habilidades 

> [!NOTE]
> A partir de 21 de dezembro de 2018, você poderá associar um recurso de Serviços Cognitivos a um conjunto de habilidades do Azure Search. Isso nos permitirá começar a cobrar pela execução do conjunto de habilidades. Nessa data, também começaremos a cobrar pela extração de imagens como parte do estágio de decodificação de documentos. A extração de texto de documentos continuará sendo oferecida sem custo adicional.
>
> A execução das habilidades internas será cobrada conforme o [preço pago conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existente. O preço da extração de imagens será cobrado conforme o preço da versão prévia, descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


A pesquisa cognitiva extrai e enriquece dados para torná-los pesquisáveis no Azure Search. Chamamos as etapas de extração e aprimoramento de *habilidades cognitivas*. O conjunto de habilidades chamado durante a indexação de conteúdo é definido em um *conjunto de habilidades*. Um conjunto de habilidades pode usar [habilidades predefinidas](cognitive-search-predefined-skills.md) ou personalizadas (confira [Exemplo: criar uma habilidade personalizada](cognitive-search-create-custom-skill-example.md) para obter mais informações).

Neste artigo, você aprenderá a associar o recurso dos [Serviços Cognitivos ](https://azure.microsoft.com/services/cognitive-services/) ao seu conjunto de habilidades cognitivo.

O recurso dos Serviços Cognitivos selecionado capacitará as habilidades cognitivas internas. O recurso também será usado para fins de cobrança. Os aprimoramentos realizados usando as habilidades cognitivas internas serão cobrados com relação ao recurso dos Serviços Cognitivos selecionado. Será cobrada a mesma taxa como se você tivesse realizado a mesma tarefa usando o recurso dos Serviços Cognitivos. Confira os [preços do Serviço Cognitivo](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Limites quando nenhum recurso dos Serviços Cognitivos é selecionado
A partir de 1 de fevereiro de 2019, se você não associar uma assinatura dos Serviços Cognitivos ao seu conjunto de habilidades, você só poderá aprimorar um pequeno número de documentos gratuitamente (20 documentos por dia). 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>Associar um recurso dos Serviços Cognitivos a um novo conjunto de habilidades

1. Como parte da experiência de *Importar dados*, após se conectar à sua fonte de dados, você navegará até a etapa opcional *Adicionar pesquisa cognitiva*. 

1. Expanda a seção *Anexar Serviços Cognitivos*. Isso mostrará os recursos dos Serviços Cognitivos que você tem nas mesmas regiões do Serviço de Pesquisa. 
![Anexar Serviço Cognitivo expandido](./media/cognitive-search-attach-cognitive-services/attach1.png "Anexar Serviço Cognitivo expandido")

1. Selecione um recurso dos Serviços Cognitivos existente ou *Crie um recurso dos Serviços Cognitivos*. Se você selecionar o *recurso gratuito (aprimoramentos limitados)*, só poderá aprimorar um pequeno número de documentos gratuitamente (20 documentos por dia). Se você clicar em *Criar um recurso de Serviços Cognitivos*, uma nova guia será aberta que permitirá criar o recurso de Serviços cognitivos. 

1. Se você tiver criado um novo recurso, clique em *Atualizar* para atualizar a lista de recursos dos Serviços cognitivos e, em seguida, selecione o recurso. 
![Recurso do Serviço Cognitivo selecionado](./media/cognitive-search-attach-cognitive-services/attach2.png "Recurso do Serviço Cognitivo selecionado")

1. Depois de fazer isso, será possível expandir a seção *Adicionar aprimoramentos* para selecionar as habilidades cognitivas específicas que você deseja executar sobre seus dados e prossiga com o restante do fluxo.

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>Associando um recurso dos Serviços Cognitivos a um conjunto de habilidades existente

1. Na página Visão geral do serviço, selecione a guia *Conjuntos de habilidades*. ![Guia Conjuntos de Habilidades](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Guia Conjuntos de Habilidades")

1. *Clique* no conjunto de habilidades que você gostaria de modificar. Isso abrirá uma folha que permitirá editar um conjunto de habilidades.

1. Selecione um recurso dos Serviços Cognitivos existente ou *Crie um recurso dos Serviços Cognitivos*. Se você selecionar o *recurso gratuito (aprimoramentos limitados)*, só poderá aprimorar um pequeno número de documentos gratuitamente (20 documentos por dia). Se você clicar em *Criar um recurso de Serviços Cognitivos*, uma nova guia será aberta que permitirá criar o recurso de Serviços cognitivos. <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Se você tiver criado um novo recurso, clique em *Atualizar* para atualizar a lista de recursos dos Serviços cognitivos e, em seguida, selecione o recurso.
1. Clique em *OK* para confirmar as alterações

## <a name="associating-a-cognitive-services-resource-programmatically"></a>Associando um recurso dos Serviços Cognitivos de maneira programática

Ao definir o conjunto de habilidades de forma programática, adicione uma seção `cognitiveServices`. A seção deve incluir a chave do recurso de Serviços Cognitivos que você gostaria de associar ao conjunto de habilidades, bem como o @odata.type, que deve ser definido como "#Microsoft.Azure.Search.CognitiveServicesByKey". Esse padrão é mostrado no exemplo abaixo.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>Exemplo: Estimando o custo da decodificação e aprimoramento de documentos
Talvez convenha estimar quanto custa aprimorar determinado tipo de documento. O exercício abaixo é apenas um exemplo, mas pode ser útil para você.

Imagine que temos 1000 PDFs e estimamos que, em média, cada um desses documentos tem 6 páginas. Digamos que cada um deles tem uma imagem por página para este exercício. Também vamos imaginar que, em média, há cerca de 3.000 caracteres por página. 

Agora, vamos supor que desejamos seguir estas etapas como parte do pipeline de aprimoramento:
1. Como parte da decodificação de documentos, extraia o conteúdo e as imagens do documento.
1. Como parte do aprimoramento, faça OCR de cada uma das páginas extraídas, combine o texto para todas as páginas e extraia cada uma das organizações no texto combinado de toda essas imagens.

Vamos estimar quanto custaria para ingerir esses documentos, passo a passo.

Para mil documentos:

1. Codificação de documentos, extrairíamos um número combinado de 6 mil imagens. Supondo US$ 1 para cada mil imagens extraídas, que custariam US$ 6,00.

2. Extrairíamos o texto de cada uma dessas 6 mil imagens. Em inglês, a habilidade cognitiva do OCR usa o melhor algoritmo (DescribeText). Supondo um custo de US$ 2,50 por mil imagens a serem analisadas, pagaríamos US$ 15,00 para essa etapa.

3. Para extração de entidades, teríamos um total de três registros de texto por página (cada registro tem mil caracteres). 3 registros de texto/página * 6 mil páginas = 18 mil registros de texto. Supondo US$ 2/1.000 registros de texto, essa etapa custaria US$ 36.

Juntando as peças, pagaríamos US$ 57,00 para ingerir 1.000 documentos PDF dessa natureza com o conjunto de habilidades descrito.  Neste exercício, supomos o preço mais caro por transação; ele poderia ter sido menor devido a preços de graduação. Confira os [preços dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Próximas etapas
+ [Página de preços do Azure Search](https://azure.microsoft.com/pricing/details/search/)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar conjunto de qualificações (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
