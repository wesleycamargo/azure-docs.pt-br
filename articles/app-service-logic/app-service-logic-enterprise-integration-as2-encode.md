---
title: "Saiba mais sobre o Conector de Mensagem de Codificação AS2 do Enterprise Integration Pack | Microsoft Docs"
description: "Saiba como usar parceiros com o Enterprise Integration Pack e aplicativos Lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ebfefbafe6a47beb406bbea0f7c48b61a37ac151


---
# <a name="get-started-with-encode-as2-message"></a>Introdução para Codificar Mensagem AS2
Conecte-se à opção Codificar Mensagem AS2 para estabelecer confiabilidade e segurança ao transmitir mensagens. Ele fornece a assinatura digital, criptografia e confirmações por meio de MDN (notificações de disposição de mensagem), o que também leva a suporte para Não Repúdio.

## <a name="create-the-connection"></a>Criar a conexão
### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração é necessária para usar o conector de Codificar Mensagem AS2. Confira detalhes sobre como criar uma [Conta de Integração](app-service-logic-enterprise-integration-create-integration-account.md), [parceiros](app-service-logic-enterprise-integration-partners.md) e um [contrato AS2](app-service-logic-enterprise-integration-as2.md)

### <a name="connect-to-encode-as2-message-using-the-following-steps"></a>Conecte-se à opção Codificar Mensagem AS2 usando as seguintes etapas:
1. [Criar um Aplicativo Lógico](app-service-logic-create-a-logic-app.md) fornece um exemplo
2. Esse conector não tem gatilhos. Use outros gatilhos para iniciar o Aplicativo Lógico, como um gatilho de solicitação.  No designer do Aplicativo Lógico, adicione um gatilho e uma ação.  Selecione Mostrar APIs gerenciadas da Microsoft na lista suspensa e digite “AS2” na caixa de pesquisa.  Selecione AS2 – Codificar Mensagem AS2
   
    ![pesquisar AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)
3. Se ainda não tiver criado conexões com a Conta de Integração, você deverá fornecer os detalhes de conexão
   
    ![criar conexão com a conta de integração](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage1.png)  
4. Insira os detalhes da Conta de Integração.  As propriedades com um asterisco são obrigatórias
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da Conexão * |Digite um nome para a conexão |
   | Uma conta de integração * |Insira o nome da Conta de Integração. Certifique-se de que sua Conta de Integração e que o Aplicativo Lógico estejam na mesma localização do Azure |
   
      Uma vez concluída, os detalhes da conexão se parecerão com estes
   
      ![conexão de integração estabelecida](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage2.png)  
5. Selecione **Criar**
6. Observe que a conexão foi criada.  Forneça os identificadores AS2-From, AS2-To (conforme configurado no contrato) e detalhes do Corpo (o conteúdo da mensagem). 
   
    ![fornecer campos obrigatórios](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage3.png)

## <a name="the-as2-encode-does-the-following"></a>A opção Codificar AS2 faz o seguinte
* Aplica cabeçalhos HTTP/AS2
* Sinaliza mensagens de saída (se configurado)
* Criptografa mensagens de saída (se configurado)
* Compacta as mensagens (se configurado)

## <a name="try-it-for-yourself"></a>Experimente
Por que não experimentá-lo? Clique [aqui](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) para implantar um aplicativo lógico seu totalmente operacional usando os recursos AS2 dos Aplicativos Lógicos

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


