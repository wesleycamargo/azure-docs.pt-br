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
ms.openlocfilehash: cd177b1ebcb5d236ce265dc153ee6a02125a69df
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="advanced-json-transformations-using-liquid-template"></a>Transformações avançadas de JSON usando o modelo Liquid
O Aplicativo Lógico do Azure oferece suporte a transformações básicas de JSON por meio de ações de operação de dados nativos como compor ou analisar JSON. O Aplicativo Lógico do Azure agora também oferece suporte a transformações avançadas de JSON com modelos Liquid. [Liquid](https://shopify.github.io/liquid/) é uma linguagem de modelo de código-fonte aberto para aplicativos web flexível.
 
Neste artigo, saiba como usar um mapa ou modelo Liquid, o que pode dar suporte a transformações JSON mais complexas, como iterações, fluxos de controle, variáveis e assim por diante. Você deve definir JSON para o mapeamento de JSON com o mapa Liquid e armazenar esse mapa em sua conta de integração antes de executar uma transformação de Liquid em seu aplicativo lógico.

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão os pré-requisitos para usar a ação do Liquid:

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá [iniciar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, você pode [inscrever-se para uma assinatura Pré-paga](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/logic-apps-create-a-logic-app.md).

* Uma [Conta de Integração](logic-apps-enterprise-integration-create-integration-account.md) básica.


## <a name="create-and-add-liquid-template-or-map-to-integration-account"></a>Criar e adicionar um modelo ou mapa Liquid para a conta de integração

1. Crie o modelo Liquid de amostra para este exemplo. O modelo Liquid define como transformar a entrada JSON, conforme descrito aqui:

   ```
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

4. Na caixa de pesquisa, forneça sua conta de integração. Selecione sua conta.

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  No bloco de conta de integração, selecione **Mapas**.

   ![Selecione mapas](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Escolha **Adicionar** e forneça esses detalhes para o mapa:
  * **Nome**: o nome de seu mapa, que é "JsontoJsonTemplate" neste exemplo.
  * **Tipo de mapa**: o tipo para o mapa. Para JSON para transformação de JSON, você deve selecionar **Liquid**.
  * **Mapa**: um arquivo de modelo ou mapa Liquid existente para usar para a transformação, que é "SimpleJsonToJsonTemplate.liquid" neste exemplo. Você pode usar o seletor de arquivos para localizar este arquivo.

    ![Adicionar modelo de Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

    
## <a name="add-the-liquid-action-to-transform-json-in-your-logic-app"></a>Adicione a ação do Liquid para transformar JSON em seu aplicativo lógico

1. [Criar um aplicativo lógico](logic-apps-create-a-logic-app.md).

2. Adicione o [Gatilho de solicitação](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) ao seu aplicativo lógico.

3. Escolha **+ Nova Etapa> Adicionar uma ação**. Pesquise pelo *Liquid* na caixa de pesquisa. Selecione **Liquid – Transformar JSON em JSON**.

  ![Pesquisar-ação-Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Na caixa **Conteúdo**, selecione **Corpo** na lista de conteúdo dinâmico ou lista de parâmetros, o que for exibido. 
  
  ![Selecionar-corpo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Na lista suspensa **Mapa**, selecione o modelo de Liquid, que é JsonToJsonTemplate neste exemplo.

  ![Selecionar-mapa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se a lista estiver vazia, o aplicativo lógico provavelmente não está vinculado à sua conta de integração. Para vincular seu aplicativo lógico à conta de integração que tem o modelo ou mapa Liquid, siga estas etapas:

   1. No menu aplicativo lógica, selecione **Configurações de fluxo de trabalho**. 
   2. Na lista **Selecionar uma conta de integração**, selecione sua conta de integração e escolha **Salvar**.

     ![Vincular o aplicativo lógico a uma conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)


## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

   Poste a entrada JSON no seu aplicativo lógico do [Postman](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. A saída JSON transformada do seu aplicativo lógico é parecida com este exemplo:
  
   ![Saída de exemplo](./media/logic-apps-enterprise-integration-liquid-transform/example-output.png)


## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md "Saiba mais sobre mapas da integração corporativa")  

