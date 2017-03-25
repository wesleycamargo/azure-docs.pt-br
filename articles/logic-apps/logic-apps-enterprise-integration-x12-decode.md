---
title: "Decodificar mensagens X12 - Aplicativo Lógico do Azure | Microsoft Docs"
description: "Validar EDI e gerar o XML de conjuntos de transações com o decodificador de mensagem X12 no Enterprise Integration Pack para Aplicativo Lógico do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 717069dbe211ea9cc04925875e0f28c85ef25ac2
ms.lasthandoff: 03/10/2017


---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Decodificar mensagens X12 para o Aplicativo Lógico do Azure com o Enterprise Integration Pack

Com o conector de mensagem X12 de decodificação, você pode validar o EDI e as propriedades específicas de parceiro, gera um documento XML para cada conjunto de transações e gerar uma confirmação das transações processadas. Para usar esse conector, você deve adicionar o conector para um gatilho existente em seu aplicativo lógico.

## <a name="before-you-start"></a>Antes de começar

Veja os itens necessários:

* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já esteja definida e associada à sua assinatura do Azure. Você precisa ter uma conta de integração para usar o conector de mensagem X12 de decodificação.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Pelo menos dois [contratos X12](logic-apps-enterprise-integration-x12.md) que já estão definidos em sua conta de integração

## <a name="decode-x12-messages"></a>Decodificar mensagens X12

1. [Criar um aplicativo lógico](logic-apps-create-a-logic-app.md).

2. O conector de mensagem X12 de decodificação não possui gatilhos, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de Solicitação. No Designer de Aplicativo Lógico, adicione um gatilho e uma ação ao aplicativo lógico.

3.    Na caixa de pesquisa, digite "x12" como filtro. Selecione **X12 - Decodificar mensagem X12**.
   
    ![Procure por “x12”](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Se você não criou conexões à sua conta de integração previamente, terá que criá-las agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar. 

    ![Fornece detalhes da conexão com a conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da Conexão * |Digite um nome para a conexão. |
    | Uma conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

5.    Quando terminar, os detalhes de conexão devem ser semelhantes a este exemplo. Para concluir a criação da sua conexão, escolha **Criar**.
   
    ![detalhes da conexão com a conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Após a conexão ter sido criada, conforme mostrado neste exemplo, selecione a mensagem de arquivo simples X12 para decodificação.

    ![conexão com a conta de integração criada](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Por exemplo:

    ![Selecione a mensagem de arquivo simples X12 para decodificação](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

## <a name="x12-decode-details"></a>Detalhes da Decodificação X12

O conector de Decodificação X12 executa as seguintes tarefas:

* Valida o envelope no contrato de parceiro comercial
* Gera um documento XML para cada conjunto de transação.
* Valida o EDI e as propriedades específicas de parceiro
  * Validação estrutural de EDI e validação de esquema estendido
  * Validação da estrutura do envelope de intercâmbio.
  * Validação de esquema do envelope em relação ao esquema de controle.
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema de mensagem.
  * Validação de EDI executadas em elementos de dados do conjunto de transação 
* Verifica se os números de controle de conjunto de intercâmbio, grupo e transações não são duplicatas
  * Verifica o número de controle de intercâmbio em relação aos intercâmbios recebidos anteriormente.
  * Verifica o número de controle de grupo em relação a outros números de controle no intercâmbio.
  * Verifica o número de controle do conjunto de transação em relação a outros números de controle de conjunto de transação nesse grupo.
* Converte o intercâmbio inteiro em XML 
  * Divida o intercâmbio em conjuntos de transações – conjuntos de transação suspensos com erro: analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado. Se um ou mais conjuntos de transações no intercâmbio falharem na validação, a Decodificação X12 suspenderá somente estes conjuntos de transação.
  * Divida o intercâmbio em conjuntos de transações – suspender intercâmbios com erro: analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado.  Se um ou mais conjuntos de transações do intercâmbio falharem na validação, a Decodificação X12 suspenderá o intercâmbio inteiro.
  * Preservar intercâmbio – suspender conjuntos de transação com erro: cria um documento XML para o intercâmbio em lote inteiro. A Decodificação X12 suspende somente os conjuntos de transação com falha na validação, enquanto continua a processar todos os outros conjuntos de transação
  * Preservar intercâmbio – suspender intercâmbio com erro: cria um documento XML para o intercâmbio em lote inteiro. Se um ou mais conjuntos de transações do intercâmbio falharem na validação, a Decodificação X12 suspenderá o intercâmbio inteiro. 
* Gera uma confirmação técnica e/ou funcional (se configurado).
  * Uma confirmação técnica é gerada como resultado da validação de cabeçalho. A confirmação técnica relata o status do processamento de um cabeçalho e rodapé de intercâmbio pelo destinatário no endereço.
  * Uma confirmação técnica é gerada como resultado da validação do corpo. A confirmação funcional relata cada erro encontrado durante o processamento de documentos recebidos

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


