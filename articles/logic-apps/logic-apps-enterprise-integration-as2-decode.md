---
title: "Decodificar mensagens AS2 nos Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Como usar o decodificador AS2 incluído com o Enterprise Integration Pack e Aplicativos lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 37562ff385305088590c793147b8ad268148c40b


---
# <a name="get-started-with-decoding-as2-message-in-your-logic-apps"></a>Introdução à decodificação de mensagem AS2 em seus aplicativos lógicos
Conecte-se à opção Decodificar Mensagem AS2 para estabelecer confiabilidade e segurança ao transmitir mensagens. Ele fornece a assinatura digital, descriptografia e confirmações por meio de MDN (notificações de disposição de mensagem).

## <a name="prereqs"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração é necessária para usar o conector de Decodificar Mensagem AS2. Confira detalhes sobre como criar uma [Conta de Integração](logic-apps-enterprise-integration-create-integration-account.md), [parceiros](logic-apps-enterprise-integration-partners.md) e um [contrato AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

## <a name="decode-as2-messages"></a>Decodificar mensagens AS2
1. [Crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).
2. Esse conector não tem gatilhos. Use outros gatilhos para iniciar o Aplicativo Lógico, como um gatilho de solicitação.  No designer do Aplicativo Lógico, adicione um gatilho e uma ação.  Selecione Mostrar APIs gerenciadas da Microsoft na lista suspensa e digite “AS2” na caixa de pesquisa.  Selecione AS2 – Decodificar Mensagem AS2:
   
    ![Pesquisar AS2](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)
3. Se ainda não tiver criado conexões com a Conta de Integração, você deverá fornecer os detalhes de conexão:
   
    ![Criar conexão de integração](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)
4. Insira os detalhes da Conta de Integração.  As propriedades com um asterisco são obrigatórias:
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da Conexão * |Digite um nome para a conexão |
   | Uma conta de integração * |Insira o nome da Conta de Integração. Certifique-se de que sua Conta de Integração e que o Aplicativo Lógico estejam na mesma localização do Azure |
   
      Uma vez concluída, os detalhes da conexão se parecerão com estes
   
      ![conexão de integração](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)
5. Selecione **Criar**.
6. Observe que a conexão foi criada.  Agora, continue com as outras etapas no seu Aplicativo Lógico:
   
    ![conexão de integração criada](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 
7. Selecione Corpo e Cabeçalhos de saídas de Solicitação:
   
    ![fornecer campos obrigatórios](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>Detalhes do decodificador AS2
O conector de Decodificação AS2 faz o seguinte: 

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

## <a name="try-it-yourself"></a>Experimente por conta própria
Experimente. Use o [cenário e o modelo de aplicativo de lógica do AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) para implantar um aplicativo lógico totalmente operacional.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 




<!--HONumber=Jan17_HO5-->


