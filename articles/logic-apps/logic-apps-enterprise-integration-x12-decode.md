---
title: "Decodificar mensagens X12 nos Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Como usar o codificador X12 no Enterprise Integration Pack com seus aplicativos lógicos"
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
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 2fa435d5a52ee7a9c18926912a7212ee862b3832


---
# <a name="get-started-with-decode-x12-messages"></a>Introdução à decodificação de mensagens X12
Valida o EDI e as propriedades específicas de parceiro, gera um documento XML para cada conjunto de transação e gera a confirmação de transação processada.

## <a name="prereqs"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração é necessária para usar o conector de Decodificar Mensagem X12. Confira detalhes sobre como criar uma [Conta de Integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) e um [contrato X12](../logic-apps/logic-apps-enterprise-integration-x12.md)

## <a name="decode-x12-messages"></a>Decodificar mensagens X12

1. [Criar um Aplicativo Lógico](../logic-apps/logic-apps-create-a-logic-app.md) fornece um exemplo
2. Esse conector não tem gatilhos. Use outros gatilhos para iniciar o Aplicativo Lógico, como um gatilho de solicitação.  No designer do Aplicativo Lógico, adicione um gatilho e uma ação.  Selecione Mostrar APIs gerenciadas da Microsoft na lista suspensa e digite “x12” na caixa de pesquisa.  Selecione X12 – Decodificar Mensagem X12:
   
    ![pesquisar x12](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  
3. Se ainda não tiver criado conexões com a Conta de Integração, você deverá fornecer os detalhes de conexão:
   
    ![conexão com a conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)     
4. Insira os detalhes da Conta de Integração.  As propriedades com um asterisco são obrigatórias:
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da Conexão * |Digite um nome para a conexão |
   | Uma conta de integração * |Insira o nome da Conta de Integração. Certifique-se de que sua Conta de Integração e que o Aplicativo Lógico estejam na mesma localização do Azure |
   
    Uma vez concluída, os detalhes da conexão se parecerão com estes:
   
    ![conexão com a conta de integração criada](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 
5. Selecione **Criar**.
6. Observe que a conexão foi criada:
   
    ![detalhes da conexão com a conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 
7. Selecione a mensagem de arquivo simples X12 para decodificar:
   
    ![fornecer campos obrigatórios](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

## <a name="x12-decode-details"></a>Detalhes da Decodificação X12

O conector de Decodificação X12 faz o seguinte:

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




<!--HONumber=Jan17_HO5-->


