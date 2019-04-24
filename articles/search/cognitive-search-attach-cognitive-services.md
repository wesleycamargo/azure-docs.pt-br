---
title: Anexar um recurso dos Serviços Cognitivos a um conjunto de habilidades – Azure Search
description: Instruções para anexar uma assinatura Todos-em-um dos Serviços Cognitivos a um pipeline de aprimoramento cognitivo no Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 09695f764ff71b274e125e90835f5314eb25c980
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344443"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Anexar um recurso dos Serviços Cognitivos a um conjunto de habilidades no Azure Search 

Unidade de algoritmos de inteligência Artificial a [pipelines de indexação cognitivas](cognitive-search-concept-intro.md) usado para o processamento de dados não estruturados no Azure Search. Esses algoritmos baseiam-se em [recursos dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/), incluindo a [Pesquisa Visual Computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para análise de imagem e OCR (reconhecimento óptico de caracteres) e a [Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para o reconhecimento de entidades, extração de frases-chave e outros enriquecimentos.

Você pode aprimorar gratuitamente um número limitado de documentos ou anexar um recurso de Serviços Cognitivos faturável para cargas de trabalho maiores e mais frequentes. Neste artigo, aprenda a associar um recurso dos Serviços Cognitivos a seu conjunto de habilidades cognitivo para enriquecer os dados durante a [indexação do Azure Search](search-what-is-an-index.md).

Se o pipeline consistir em habilidades não relacionadas à API de Serviços Cognitivos, você ainda deverá anexar um recurso dos Serviços Cognitivos. Essa ação substitui o recurso **Gratuito** que limita você a uma pequena quantidade de enriquecimentos por dia. Não há nenhum encargo para as habilidades que não estão associadas à API de Serviços Cognitivos. Essas habilidades incluem: [habilidades personalizadas](cognitive-search-create-custom-skill-example.md), [fusão de texto](cognitive-search-skill-textmerger.md), [divisor de texto](cognitive-search-skill-textsplit.md) e [formatador](cognitive-search-skill-shaper.md).

> [!NOTE]
> À medida que você expande o escopo ao aumentar a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará anexar um recurso faturável dos Serviços Cognitivos. As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem como parte do estágio de decodificação de documentos no Azure Search. Não há encargos para extração de texto em documentos.
>
> Execução de [habilidades cognitivas internos](cognitive-search-predefined-skills.md) execução será cobrada com o [dos serviços Cognitivos pagamento medida que vá preços](https://azure.microsoft.com/pricing/details/cognitive-services), na mesma taxa como se você executou a tarefa diretamente. Extração de imagem é um encargo de Azure Search, refletido na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="use-free-resources"></a>Usar recursos gratuitos

Você pode usar uma opção de processamento limitado e gratuito para concluir os exercícios do início rápido e o tutorial de pesquisa cognitiva. 

**Gratuito (aprimoramentos de limitado)** estão restritas a 20 documentos por dia, por assinatura.

1. Abra o assistente **Importar dados**.

   ![Comando Importar dados](media/search-get-started-portal/import-data-cmd2.png "Comando Importar dados")

1. Escolha uma fonte de dados e continue para **Adicionar pesquisa cognitiva (opcional)**. Para obter uma explicação passo a passo deste assistente, veja [Importar, indexar e consultar usando ferramentas de portal](search-get-started-portal.md).

1. Expanda **Anexar Serviços Cognitivos** e selecione **Gratuito (Aprimoramentos Limitados)**.

   ![Anexar seção Serviço Cognitivo expandido](./media/cognitive-search-attach-cognitive-services/attach1.png "Anexar Serviço Cognitivo expandido")

Continuar para a próxima etapa, **Adicionar aprimoramentos**. Para obter uma descrição das habilidades disponíveis no portal, veja ["Etapa 2: Adicionar habilidade cognitiva"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) no início rápido de pesquisa cognitiva.

## <a name="use-billable-resources"></a>Usar recursos faturáveis

Para cargas de trabalho com um número superior a 20 enriquecimentos por dia, será necessário anexar um recurso faturável dos Serviços Cognitivos. 

Você é cobrado apenas pelas habilidades que chamam a API de Serviços Cognitivos. As habilidades não baseadas em API como [habilidades personalizadas](cognitive-search-create-custom-skill-example.md), [fusão de texto](cognitive-search-skill-textmerger.md), [divisor de texto](cognitive-search-skill-textsplit.md) e [formatador](cognitive-search-skill-shaper.md) não são cobradas.

1. Abra o **importar dados** assistente, escolha uma fonte de dados e continuar a **(opcional) de pesquisa cognitiva adicionar**. 

1. Expandir **anexar serviços Cognitivos** e, em seguida, selecione **criar novo recurso de serviços Cognitivos**. Uma nova guia é aberta para que você possa criar o recurso. 

   ![Criar um recurso de serviços Cognitivos](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "criar um recurso de serviços Cognitivos")

1. No local, escolha a mesma região do Azure Search para evitar encargos de largura de banda de saída entre regiões.

1. No tipo de preço, escolha **S0** para obter a coleção em um dos recursos de serviços Cognitivos, incluindo os recursos de visão e a linguagem que faça as habilidades predefinidas usadas pelo Azure Search. 

   Para a camada S0, você pode encontrar as taxas para cargas de trabalho específicas na [página de preços dos serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + No **selecionar oferecem**, verifique se *dos serviços Cognitivos* está selecionado.
   + Em recursos de linguagem, as taxas *padrão de análise de texto* se aplicam a indexação de inteligência Artificial. 
   + Em recursos de visão, as taxas *S1 de visão do computador* são aplicadas.

1. Clique em **Criar** para provisionar o novo recurso de Serviços Cognitivos. 

1. Retornar para a guia anterior que contém o assistente **Importar dados**. Clique em **Atualizar** para mostrar o recurso de Serviços Cognitivos e, em seguida, selecione o recurso.

   ![Recurso do Serviço Cognitivo selecionado](./media/cognitive-search-attach-cognitive-services/attach2.png "Recurso do Serviço Cognitivo selecionado")

1. Expanda a seção **Adicionar aprimoramentos** para selecionar as habilidades cognitivas específicas que você deseja executar sobre seus dados e prossiga com o restante do fluxo. Para obter uma descrição das habilidades disponíveis no portal, veja ["Etapa 2: Adicionar habilidade cognitiva"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) no início rápido de pesquisa cognitiva.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Anexar um conjunto de habilidades existente a um recurso de Serviços Cognitivos

Se você tiver um conjunto de habilidades existente, poderá anexá-lo a um recurso de Serviços Cognitivos novo ou diferente.

1. Na página **Visão geral do serviço**, clique em **Conjuntos de habilidades**.

   ![Guia Conjuntos de Habilidades](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Guia Conjuntos de Habilidades")

1. Clique no nome do conjunto de habilidades e, em seguida, selecione um recurso existente ou crie um novo. Clique em **OK** para confirmar as alterações. 

   ![Lista de recursos do conjunto de habilidades](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lista de recursos do conjunto de habilidades")

Lembre-se de que **Gratuito (Aprimoramentos Limitados)** está limitado a 20 documentos por dia e que **Criar novo recurso de Serviços Cognitivos** é usado para provisionar um novo recurso faturável. Se você criar um novo recurso, selecione **Atualizar** para atualizar a lista de recursos dos Serviços cognitivos e, em seguida, selecione o recurso.

## <a name="attach-cognitive-services-programmatically"></a>Anexar Serviços Cognitivos de modo programático

Ao definir o conjunto de habilidades de forma programática, adicione uma seção `cognitiveServices` ao conjunto de habilidades. Na seção, inclua a chave do recurso dos Serviços Cognitivos que você deseja associar o conjunto de habilidades. Lembre-se de que o recurso deve estar na mesma região que o Azure Search. Também inclua `@odata.type`e defina como `#Microsoft.Azure.Search.CognitiveServicesByKey`. 

O exemplo a seguir mostra esse padrão. Observe a seção cognitiveServices na parte inferior da definição

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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Exemplo: Estimar custos

Para estimar os custos associados à indexação de pesquisa cognitiva, comece com uma ideia de como é um documento médio de modo que você possa executar realizar alguns cálculos. Por exemplo, para fins de estimativa, você pode aproximar:

+ 1.000 PDFs
+ Seis páginas cada
+ Uma imagem por página (6.000 imagens)
+ 3.000 caracteres por página

Suponha um pipeline que consiste em decifração de documento de cada PDF com extração de imagem e texto, OCR (reconhecimento óptico de caracteres) de imagens e reconhecimento de entidade nomeada de organizações. 

Neste exercício, estamos usando o preço mais caro por transação. Os custos reais podem ser inferiores devido a preços graduados. Confira os [preços dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Para decifração de documento com conteúdo de texto e imagem, a extração de texto atualmente é gratuita. Para 6.000 imagens, suponha US$ 1 para cada 1.000 imagens extraídas, custando US$ 6,00 para esta etapa.

2. Para OCR de 6.000 imagens em inglês, a habilidade cognitiva do OCR usa o melhor algoritmo (DescribeText). Supondo um custo de US$ 2,50 por 1.000 imagens a serem analisadas, pagaríamos US$ 15,00 para essa etapa.

3. Para extração de entidades, teríamos um total de três registros de texto por página. Cada registro tem 1.000 caracteres. Três registros de texto por página * 6.000 páginas = 18.000 registros de texto. Supondo US$ 2,00 por 1.000 registros de texto, essa etapa custaria US$36,00.

Juntando as peças, pagaríamos cerca de US$ 57,00 para ingerir 1.000 documentos PDF dessa natureza com o conjunto de habilidades descrito. 

## <a name="next-steps"></a>Próximos passos
+ [Página de preços do Azure Search](https://azure.microsoft.com/pricing/details/search/)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar conjunto de qualificações (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
