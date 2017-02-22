---
title: "Codificar mensagens do X12 nos aplicativos lógicos do Azure | Microsoft Docs"
description: "Como usar o codificador X12 no Enterprise Integration Pack com seus aplicativos lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: bd339e60aff1edca7f86e0ae82746f55eb67b296
ms.openlocfilehash: 9f7b53cfafa8c14ab46cf80015afc8da4e0fda2b


---
# <a name="get-started-with-encode-x12-message"></a>Introdução para Codificar Mensagem X12
Valida o EDI e as propriedades específicas de parceiro, converte as mensagens codificadas em XML em conjuntos de transações de EDI no intercâmbio e solicita uma confirmação técnica e/ou funcional

## <a name="prereqs"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração é necessária para usar o conector de Codificar Mensagem x12. Confira os detalhes sobre como criar uma [Conta de Integração](logic-apps-enterprise-integration-create-integration-account.md), [parceiros](logic-apps-enterprise-integration-partners.md) e um [contrato X12](logic-apps-enterprise-integration-x12.md)

## <a name="encode-x12-messages"></a>Codificar mensagens do X12

1. [Criar um aplicativo lógico](logic-apps-create-a-logic-app.md).
2. Esse conector não tem gatilhos. Use outros gatilhos para iniciar o Aplicativo Lógico, como um gatilho de solicitação.  No designer do Aplicativo Lógico, adicione um gatilho e uma ação.  Selecione Mostrar APIs gerenciadas da Microsoft na lista suspensa e digite “x12” na caixa de pesquisa.  Selecione X12 – Codificar mensagem do X12 pelo nome do contrato ou X12 – Codificar mensagem do X12 por identidades:  
   
    ![pesquisar x12](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 
3. Se ainda não tiver criado conexões com a Conta de Integração, você deverá fornecer os detalhes de conexão:
   
    ![conexão com a conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png) 
4. Insira os detalhes da Conta de Integração.  As propriedades com um asterisco são obrigatórias:
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da Conexão * |Digite um nome para a conexão |
   | Uma conta de integração * |Insira o nome da Conta de Integração. Certifique-se de que sua Conta de Integração e que o Aplicativo Lógico estejam na mesma localização do Azure |
   
    Uma vez concluída, os detalhes da conexão se parecerão com estes:
   
    ![conexão com a conta de integração criada](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png) 
5. Selecione **Criar**.
6. Observe que a conexão foi criada.
   
    ![detalhes da conexão com a conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Codificar mensagens do X12 pelo nome do contrato
Selecione o contrato do X12 do menu suspenso e a mensagem XML para codificar:     
    ![fornecer campos obrigatórios](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png) 

#### <a name="encode-x12-messages-by-identities"></a>Codificar mensagens do X12 por identidades
Forneça o identificador do remetente, o qualificador de remetente, o identificador do destinatário e o qualificador do destinatário conforme configurado no contrato de X12.  Selecione a mensagem XML para codificar:
   
   ![fornecer campos obrigatórios](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Detalhes de codificação do X12

O conector de Codificação X12 faz o seguinte:

* Resolução de contrato ao corresponder as propriedades de contexto do remetente e destinatário.
* Serializa o intercâmbio de EDI, convertendo mensagens codificadas em XML em conjuntos de transação EDI no intercâmbio.
* Aplica os segmentos de cabeçalho e rodapé do conjunto de transação
* Gera um número de controle de intercâmbio, um número de controle de grupo e um número de controle de conjunto de transações para cada intercâmbio de saída
* Substitui os separadores nos dados de conteúdo
* Valida o EDI e as propriedades específicas de parceiro
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema de mensagem
  * Validação de EDI executadas em elementos de dados do conjunto de transação.
  * Validação estendida executada em elementos de dados do conjunto de transação
* Solicita uma confirmação técnica e/ou funcional (se configurado).
  * Uma confirmação técnica é gerada como resultado da validação de cabeçalho. A confirmação técnica relata o status do processamento de um cabeçalho e rodapé de intercâmbio pelo destinatário no endereço
  * Uma confirmação técnica é gerada como resultado da validação do corpo. A confirmação funcional relata cada erro encontrado durante o processamento de documentos recebidos

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


