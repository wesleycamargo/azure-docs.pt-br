---
title: "Decodificar mensagens EDIFACT - Aplicativo Lógico do Azure | Microsoft Docs"
description: "Validar EDI e gerar o XML de conjuntos de transações com o decodificador de mensagem EDIFACT no Enterprise Integration Pack para Aplicativo Lógico do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 176963837f4f3fc8b89e31000ef8722ef3258b11
ms.lasthandoff: 03/10/2017


---

# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Decodificar mensagens EDIFACT para o Aplicativo Lógico do Azure com o Enterprise Integration Pack

Com o conector de mensagem EDIFACT de decodificação, você pode validar o EDI e as propriedades específicas de parceiro, gerar um documento XML para cada conjunto de transações e gerar uma confirmação das transações processadas. Para usar esse conector, você deve adicionar o conector para um gatilho existente em seu aplicativo lógico.

## <a name="before-you-start"></a>Antes de começar

Veja os itens necessários:

* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já esteja definida e associada à sua assinatura do Azure. Você precisa ter uma conta de integração para usar o conector de mensagem EDIFACT de decodificação. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Um [contrato EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido em sua conta de integração

## <a name="decode-edifact-messages"></a>Decodificar mensagens EDIFACT

1. [Criar um aplicativo lógico](logic-apps-create-a-logic-app.md).

2. O conector de mensagem EDIFACT de decodificação não possui gatilhos, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de Solicitação. No Designer de Aplicativo Lógico, adicione um gatilho e uma ação ao aplicativo lógico.

3. Na caixa de pesquisa, digite "EDIFACT" como filtro. Escolha **Decodificar Mensagem EDIFACT**.
   
    ![pesquisar EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Se você não criou conexões à sua conta de integração previamente, terá que criá-las agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar.
   
    ![criar uma conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da Conexão * |Digite um nome para a conexão. |
    | Uma conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

4. Ao concluir a criação da sua conexão, escolha **Criar**. Os detalhes de conexão devem ser semelhantes a este exemplo:

    ![detalhes da conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Após a conexão ter sido criada, conforme mostrado neste exemplo, selecione a mensagem de arquivo simples EDIFACT para decodificação.

    ![conexão com a conta de integração criada](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Por exemplo:

    ![Escolha a mensagem de arquivo simples EDIFACT para decodificação](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Detalhes de decodificador EDIFACT

O conector EDIFACT de decodificação executa as seguintes tarefas: 

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
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


