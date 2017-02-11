---
title: "Saiba mais sobre o Conector de Mensagem de Decodificação de AS2 do Enterprise Integration Pack | Microsoft Docs"
description: "Saiba como usar parceiros com o Enterprise Integration Pack e aplicativos Lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5836e0d2bb7d1d97e9f175396f51047af0f84ca5


---
# <a name="get-started-with-decode-as2-message"></a>Introdução para Decodificar Mensagem AS2
Conecte-se à opção Decodificar Mensagem AS2 para estabelecer confiabilidade e segurança ao transmitir mensagens. Ele fornece a assinatura digital, descriptografia e confirmações por meio de MDN (notificações de disposição de mensagem).

## <a name="create-the-connection"></a>Criar a conexão
### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração é necessária para usar o conector de Decodificar Mensagem AS2. Consulte detalhes sobre como criar uma [Conta de Integração](app-service-logic-enterprise-integration-create-integration-account.md), [parceiros](app-service-logic-enterprise-integration-partners.md) e um [contrato AS2](app-service-logic-enterprise-integration-as2.md)

### <a name="connect-to-decode-as2-message-using-the-following-steps"></a>Conecte-se à opção Decodificar Mensagem AS2 usando as seguintes etapas:
1. [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md) fornece um exemplo.
2. Esse conector não tem gatilhos. Use outros gatilhos para iniciar o Aplicativo Lógico, como um gatilho de solicitação.  No designer do Aplicativo Lógico, adicione um gatilho e uma ação.  Selecione Mostrar APIs gerenciadas da Microsoft na lista suspensa e digite “AS2” na caixa de pesquisa.  Selecione AS2 – Decodificar Mensagem AS2
   
    ![Pesquisar AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)
3. Se ainda não tiver criado conexões com a Conta de Integração, você deverá fornecer os detalhes de conexão
   
    ![Criar conexão de integração](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)
4. Insira os detalhes da Conta de Integração.  As propriedades com um asterisco são obrigatórias
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da Conexão * |Digite um nome para a conexão |
   | Uma conta de integração * |Insira o nome da Conta de Integração. Certifique-se de que sua Conta de Integração e que o Aplicativo Lógico estejam na mesma localização do Azure |
   
      Uma vez concluída, os detalhes da conexão se parecerão com estes
   
      ![conexão de integração](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)
5. Selecione **Criar**
6. Observe que a conexão foi criada.  Agora, continue com as outras etapas no seu Aplicativo Lógico
   
    ![conexão de integração criada](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png) 
7. Selecione Corpo e Cabeçalhos de saídas de Solicitação
   
    ![fornecer campos obrigatórios](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png) 

## <a name="the-as2-decode-does-the-following"></a>Decodificar AS2 faz o seguinte
* Processa cabeçalhos HTTP/AS2
* Verifica a assinatura (se configurado)
* Descriptografa as mensagens (se configurado)
* Descompacta as mensagens (se configurado)
* Reconcilia um MDN recebido com a mensagem de saída original
* Atualiza e correlaciona os registros no banco de dados de não repúdio
* Grava os registros para o relatório de status do AS2
* O conteúdo da carga de saída é codificado na base64
* Determina se um MDN é necessário e se ele deve ser síncrono ou assíncrono com base na configuração no contrato do AS2
* Gera um MDN síncrono ou assíncrono (com base nas configurações do contrato)
* Define as propriedades e os tokens de correlação no MDN

## <a name="try-it-for-yourself"></a>Experimente
Por que não experimentá-lo? Clique [aqui](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) para implantar um aplicativo lógico seu totalmente operacional usando os recursos AS2 dos Aplicativos Lógicos 

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


