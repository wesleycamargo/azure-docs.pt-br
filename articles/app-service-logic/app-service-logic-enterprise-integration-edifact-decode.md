---
title: "Saiba mais sobre o Conector de Mensagem EDIFACT de Decodificação do Enterprise Integration Pack | Microsoft Docs"
description: "Saiba como usar parceiros com o Enterprise Integration Pack e aplicativos Lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c09660c805615364b164b5410508e49b0d9ad6f


---
# <a name="get-started-with-decode-edifact-message"></a>Introdução à Decodificar Mensagem EDIFACT
Valida o EDI e as propriedades específicas de parceiro, gera um documento XML para cada conjunto de transação e gera a confirmação de transação processada.

## <a name="create-the-connection"></a>Criar a conexão
### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração é necessária para usar o conector de Decodificar mensagem EDIFACT. Veja os detalhes de como criar uma [Conta de Integração](app-service-logic-enterprise-integration-create-integration-account.md), [parceiros](app-service-logic-enterprise-integration-partners.md) e um [contrato EDIFACT](app-service-logic-enterprise-integration-edifact.md)

### <a name="connect-to-decode-edifact-message-using-the-following-steps"></a>Conecte-se à opção Decodificar Mensagem EDIFACT usando as seguintes etapas:
1. [Criar um Aplicativo Lógico](app-service-logic-create-a-logic-app.md) fornece um exemplo.
2. Esse conector não tem gatilhos. Use outros gatilhos para iniciar o Aplicativo Lógico, como um gatilho de solicitação.  No designer do Aplicativo Lógico, adicione um gatilho e uma ação.  Escolha Mostrar APIs gerenciadas da Microsoft na lista suspensa e digite "EDIFACT" na caixa de pesquisa.  Escolha Decodificar Mensagem EDIFACT
   
    ![pesquisar EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)
3. Se ainda não tiver criado conexões com a Conta de Integração, você deverá fornecer os detalhes de conexão
   
    ![criar uma conta de integração](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage2.png)  
4. Insira os detalhes da Conta de Integração.  As propriedades com um asterisco são obrigatórias
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da Conexão * |Digite um nome para a conexão |
   | Uma conta de integração * |Insira o nome da Conta de Integração. Certifique-se de que sua Conta de Integração e que o Aplicativo Lógico estejam na mesma localização do Azure |
   
    Uma vez concluída, os detalhes da conexão se parecerão com estes
   
    ![conta de integração criada](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage3.png)  
5. Escolha **Criar**
6. Observe que a conexão foi criada
   
    ![detalhes da conexão da conta de integração](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  
7. Escolha a mensagem do arquivo simples EDIFACT para decodificar
   
    ![fornecer campos obrigatórios](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  

## <a name="edifact-decode-does-following"></a>A Decodificação EDIFACT faz o seguinte
* Resolve o contrato correspondendo o qualificador e o identificador do remetente com o qualificador e o identificador do receptor
* Divide diversos intercâmbios em uma única mensagem em mensagens separadas.
* Valida o envelope no contrato de parceiro comercial
* Desmonta o intercâmbio.
* Valida o EDI e as propriedades específicas de parceiro incluem
  * Validação da estrutura do envelope de intercâmbio.
  * Validação de esquema do envelope em relação ao esquema de controle.
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema de mensagem.
  * Validação de EDI executadas em elementos de dados do conjunto de transação
* Verifica se os números de controle de conjunto de intercâmbio, grupo e transações não estão duplicados (se configurados) 
  * Verifica o número de controle de intercâmbio em relação aos intercâmbios recebidos anteriormente. 
  * Verifica o número de controle de grupo em relação a outros números de controle no intercâmbio. 
  * Verifica o número de controle do conjunto de transações em relação a outros números de controle de conjunto de transações nesse grupo.
* Gera um documento XML para cada conjunto de transações.
* Converte o intercâmbio inteiro em XML 
  * Dividir intercâmbio como conjuntos de transação – suspender conjuntos de transações no erro: analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado. Se a validação de um ou mais conjuntos de transações no intercâmbio falhar, a Decodificação EDIFACT suspenderá somente esses conjuntos de transações. 
  * Dividir intercâmbio como conjuntos de transação – suspender intercâmbio no erro: analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado.  Se a validação de um ou mais conjuntos de transações do intercâmbio falhar, a Decodificação EDIFACT suspenderá o intercâmbio inteiro.
  * Preservar intercâmbio - suspender conjuntos de transações no erro: cria um documento XML para o intercâmbio em lote inteiro. A Decodificação EDIFACT suspende somente os conjuntos de transações com falha na validação, enquanto continua processando todos os outros conjuntos de transações
  * Preservar intercâmbio - suspender intercâmbio no erro: cria um documento XML para o intercâmbio em lote inteiro. Se a validação de um ou mais conjuntos de transações do intercâmbio falhar, a Decodificação EDIFACT suspenderá o intercâmbio inteiro. 
* Gera uma confirmação técnica (controle) e/ou funcional (se configurado).
  * Uma confirmação técnica ou o CONTRL ACK reporta os resultados de uma verificação sintática do intercâmbio completo recebido.
  * Uma confirmação funcional confirma aceitar ou rejeitar um intercâmbio recebido ou um grupo

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


