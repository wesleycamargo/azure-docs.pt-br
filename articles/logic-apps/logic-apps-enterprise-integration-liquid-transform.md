---
title: "Converter dados JSON com transformações de Liquid – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Crie transformações ou mapas para transformações avançadas de JSON usando o Aplicativo Lógico do Azure e o modelo Liquid."
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 4bb003afd757faac675a9af8599a781247717a64
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Executar transformações avançadas de JSON com um modelo Liquid

O Aplicativo Lógico do Azure oferece suporte a transformações básicas de JSON por meio de ações de operação de dados nativos como **compor** ou **Parse JSON**. Para transformações avançadas de JSON, você pode usar modelos Liquid com seus aplicativos lógicos. 
[Liquid](https://shopify.github.io/liquid/) é uma linguagem de modelo de código-fonte aberto para aplicativos web flexível.
 
Neste artigo, saiba como usar um mapa ou modelo Liquid, o que pode dar suporte a transformações JSON mais complexas, como iterações, fluxos de controle, variáveis e assim por diante. Antes de executar uma transformação Liquid no seu aplicativo lógico, você deve definir o mapeamento de JSON para JSON com o mapa Liquid e armazenar esse mapa em sua conta de integração.

## <a name="prerequisites"></a>pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá [iniciar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, você pode [inscrever-se para uma assinatura Pré-paga](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma [Conta de Integração básica](logic-apps-enterprise-integration-create-integration-account.md) 


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>Criar um modelo ou mapa Liquid para sua conta de integração

1. Crie o modelo Liquid de amostra para este exemplo. O modelo Liquid define como transformar a entrada JSON, conforme descrito aqui:

   ``` json
   {%- assign deviceList = content.devices | Split: ', ' -%}
      {
        "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
        "firstNameUpperCase": "{{content.firstName | Upcase}}",
        "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
        "devices" : [
        {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
      }
   ```
   > [!NOTE]
   > Se seu modelo Liquid usa qualquer [filtro](https://shopify.github.io/liquid/basics/introduction/#filters), esse filtro deve começar com letras maiúsculas. 

2. Entre no [Portal do Azure](https://portal.azure.com).

3. No menu principal do Azure, escolha **Todos os recursos**. 

4. Na caixa de pesquisa, encontre e selecione sua conta de integração.

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  No bloco de conta de integração, selecione **Mapas**.

   ![Selecione mapas](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Escolha **Adicionar** e forneça esses detalhes para o mapa:

   * **Nome**: o nome de seu mapa, que é "JsontoJsonTemplate" neste exemplo
   * **Tipo de mapa**: o tipo para o mapa. Para JSON para transformação de JSON, você deve selecionar **Liquid**.
   * **Mapa**: um arquivo de modelo ou mapa Liquid existente para usar para a transformação, que é "SimpleJsonToJsonTemplate.liquid" neste exemplo. Para localizar este arquivo, você pode usar o seletor de arquivos.

   ![Adicionar modelo de Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Adicione a ação Liquid para transformação de JSON

1. [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Adicione o [Gatilho de solicitação](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) ao seu aplicativo lógico.

3. Escolha **+ Nova Etapa> Adicionar uma ação**. Na caixa de pesquisa, digite *Liquid*e selecione **Liquid - Transformar JSON em JSON**.

   ![Localizar e selecionar a ação Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Na caixa **Conteúdo**, selecione **Corpo** na lista de conteúdo dinâmico ou lista de parâmetros, o que for exibido.
  
   ![Selecione corpo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Na lista **Mapa**, selecione o modelo de Liquid, que é “JsonToJsonTemplate” neste exemplo.

   ![Selecionar mapa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se a lista estiver vazia, o aplicativo lógico provavelmente não está vinculado à sua conta de integração. 
   Para vincular seu aplicativo lógico à conta de integração que tem o modelo ou mapa Liquid, siga estas etapas:

   1. No menu aplicativo lógica, selecione **Configurações de fluxo de trabalho**.
   2. Na lista **Selecionar uma conta de integração**, selecione sua conta de integração e escolha **Salvar**.

   ![Vincular o aplicativo lógico a uma conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Poste a entrada JSON no seu aplicativo lógico do [Postman](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. A saída JSON transformada do seu aplicativo lógico é parecida com este exemplo:
  
![Saída de exemplo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Mais exemplos de ação de Liquid
O Liquid não está limitado a apenas transformações de JSON. Aqui estão outras ações de transformação disponíveis que usam o Liquid.

* Transformar o JSON em texto
  
  Aqui está o modelo Liquid usado para este exemplo:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Aqui estão exemplos de entrada e saída:
  
   ![Exemplo de saída JSON para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformar XML em JSON
  
  Aqui está o modelo Liquid usado para este exemplo:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Aqui estão exemplos de entrada e saída:

   ![Exemplo de saída XML para JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformar XML em texto
  
  Aqui está o modelo Liquid usado para este exemplo:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Aqui estão exemplos de entrada e saída:

   ![Exemplo de saída XML para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md "Saiba mais sobre mapas da integração corporativa")  

