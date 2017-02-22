---
title: "Codificar mensagens AS2 nos Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Como usar o codificador AS2 incluído com o Enterprise Integration Pack e Aplicativos lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: a2ca24c1800a01e6a15af35937a654093cf224af
ms.openlocfilehash: 754ec2e829babd2e5ca3e5e5290db950ef5035e7


---
# <a name="get-started-with-encode-as2-message"></a>Introdução para Codificar Mensagem AS2
Conecte-se à opção Codificar Mensagem AS2 para estabelecer confiabilidade e segurança ao transmitir mensagens. Ele fornece a assinatura digital, criptografia e confirmações por meio de MDN (notificações de disposição de mensagem), o que também leva a suporte para Não Repúdio.

## <a name="prereqs"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração é necessária para usar o conector de Codificar Mensagem AS2. Confira detalhes sobre como criar uma [Conta de Integração](logic-apps-enterprise-integration-create-integration-account.md), [parceiros](logic-apps-enterprise-integration-partners.md) e um [contrato AS2](logic-apps-enterprise-integration-as2.md)

## <a name="encode-as2-messages"></a>Codificar mensagens AS2
1. [Criar um aplicativo lógico](logic-apps-create-a-logic-app.md).
2. Esse conector não tem gatilhos. Use outros gatilhos para iniciar o Aplicativo Lógico, como um gatilho de solicitação.  No designer do Aplicativo Lógico, adicione um gatilho e uma ação.  Selecione Mostrar APIs gerenciadas da Microsoft na lista suspensa e digite “AS2” na caixa de pesquisa.  Selecione AS2 – Codificar Mensagem AS2:
   
    ![pesquisar AS2](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)
3. Se ainda não tiver criado conexões com a Conta de Integração, você deverá fornecer os detalhes de conexão:
   
    ![criar conexão com a conta de integração](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  
4. Insira os detalhes da Conta de Integração.  As propriedades com um asterisco são obrigatórias:
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da Conexão * |Digite um nome para a conexão |
   | Uma conta de integração * |Insira o nome da Conta de Integração. Certifique-se de que sua Conta de Integração e que o Aplicativo Lógico estejam na mesma localização do Azure |
   
      Uma vez concluída, os detalhes da conexão se parecerão com estes:
   
      ![conexão de integração estabelecida](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)  
5. Selecione **Criar**.
6. Observe que a conexão foi criada.  Forneça os identificadores AS2-From, AS2-To (conforme configurado no contrato) e detalhes do Corpo (o conteúdo da mensagem):
   
    ![fornecer campos obrigatórios](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Detalhes do codificador AS2
O conector de Codificação AS2 faz o seguinte: 

* Aplica cabeçalhos HTTP/AS2
* Sinaliza mensagens de saída (se configurado)
* Criptografa mensagens de saída (se configurado)
* Compacta as mensagens (se configurado)

## <a name="try-it-yourself"></a>Experimente por conta própria
Experimente. Use o [cenário e o modelo de aplicativo de lógica do AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) para implantar um aplicativo lógico totalmente operacional.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


