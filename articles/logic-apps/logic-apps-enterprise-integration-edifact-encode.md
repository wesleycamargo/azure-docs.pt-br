---
title: "Codificar mensagens EDIFACT nos Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Como usar o codificador EDIFACT no Enterprise Integration Pack com seus aplicativos lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 5bc7011d7b0d22a7f8c11a2fee8d002c24d3467c
ms.openlocfilehash: 94d120cd8a5e33733ecc39af96d2719ad59ab090


---
# <a name="get-started-with-encode-edifact-message"></a>Introdução para Codificar Mensagem EDIFACT
Valida o EDI e as propriedades específicas de parceiro 

## <a name="prereqs"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração é necessária para usar o conector de Codificar Mensagem EDIFACT. Veja os detalhes de como criar uma [Conta de Integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [parceiros](logic-apps-enterprise-integration-partners.md) e um [contrato EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)

## <a name="encode-edifact-messages"></a>Codificar mensagens EDIFACT
1. [Crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).
2. Esse conector não tem gatilhos. Use outros gatilhos para iniciar o Aplicativo Lógico, como um gatilho de solicitação.  No designer do Aplicativo Lógico, adicione um gatilho e uma ação.  Escolha Mostrar APIs gerenciadas da Microsoft na lista suspensa e digite "EDIFACT" na caixa de pesquisa.  Selecione Codificar Mensagem EDIFACT pelo nome do contrato ou Codificar Mensagem EDIFACT por identidades:
   
    ![pesquisar EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  
3. Se ainda não tiver criado conexões com a Conta de Integração, você deverá fornecer os detalhes de conexão:
   
    ![criar conexão com a conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  
4. Insira os detalhes da Conta de Integração.  As propriedades com um asterisco são obrigatórias:
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da Conexão * |Digite um nome para a conexão |
   | Uma conta de integração * |Insira o nome da Conta de Integração. Certifique-se de que sua Conta de Integração e que o Aplicativo Lógico estejam na mesma localização do Azure |
   
    Uma vez concluída, os detalhes da conexão se parecerão com estes:
   
    ![conexão com a conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)
5. Selecione **Criar**.
6. Observe que a conexão foi criada:
   
    ![detalhes da conexão com a conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Codificar Mensagem EDIFACT pelo nome do contrato
Insira o nome do contrato de EDIFACT e a mensagem XML para codificar:
   
   ![fornecer campos obrigatórios](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Codificar Mensagem EDIFACT por identidades
Insira o identificador do remetente, o qualificador de remetente, o identificador do destinatário e o qualificador do destinatário conforme configurado no contrato de EDIFACT. Selecione a mensagem XML para codificar:  
    ![fornecer campos obrigatórios](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Detalhes de codificação do EDIFACT
O conector de codificação do EDIFACT faz o seguinte: 

* Resolver o contrato, correspondendo o qualificador de remetente e o identificador e o qualificador do destinatário e o identificador
* Serializa o intercâmbio de EDI, convertendo mensagens codificadas em XML em conjuntos de transação EDI no intercâmbio.
* Aplica os segmentos de cabeçalho e rodapé do conjunto de transação
* Gera um número de controle de intercâmbio, um número de controle de grupo e um número de controle de conjunto de transações para cada intercâmbio de saída
* Substitui os separadores nos dados de conteúdo
* Valida o EDI e as propriedades específicas de parceiro
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema de mensagem.
  * Validação de EDI executadas em elementos de dados do conjunto de transação.
  * Validação estendida executada em elementos de dados do conjunto de transação
* Gera um documento XML para cada conjunto de transação.
* Solicita uma confirmação técnica e/ou funcional (se configurado).
  * Como uma confirmação técnica, a mensagem CONTRL indica o recebimento de um intercâmbio.
  * Como uma confirmação funcional, a mensagem CONTRL indica a aceitação ou a rejeição do intercâmbio recebido, do grupo ou da mensagem, com uma lista de erros ou funcionalidade sem suporte

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


