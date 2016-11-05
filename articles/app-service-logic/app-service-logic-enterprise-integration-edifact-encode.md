---
title: Saiba mais sobre o Conector de Mensagem de Codificação de EDIFACT do Enterprise Integration Pack | Microsoft Docs
description: Saiba como usar parceiros com o Enterprise Integration Pack e aplicativos Lógicos
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: ''

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc

---
# Introdução para Codificar Mensagem EDIFACT
Valida o EDI e as propriedades específicas de parceiro

## Criar a conexão
### Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração é necessária para usar o conector de Codificar Mensagem EDIFACT. Consulte detalhes sobre como criar uma [Conta de Integração](app-service-logic-enterprise-integration-create-integration-account.md), [parceiros](app-service-logic-enterprise-integration-partners.md) e um [contrato EDIFACT](app-service-logic-enterprise-integration-edifact.md)

### Conecte-se à opção Decodificar Mensagem EDIFACT usando as seguintes etapas:
1. [Criar um Aplicativo Lógico](app-service-logic-create-a-logic-app.md) fornece um exemplo.
2. Esse conector não tem gatilhos. Use outros gatilhos para iniciar o Aplicativo Lógico, como um gatilho de solicitação. No designer do Aplicativo Lógico, adicione um gatilho e uma ação. Selecione Mostrar APIs gerenciadas da Microsoft na lista suspensa e digite “EDIFACT” na caixa de pesquisa. Selecione Codificar Mensagem EDIFACT pelo nome do contrato ou Codificar Mensagem EDIFACT por identidades.
   
    ![pesquisa de EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)
3. Se ainda não tiver criado conexões com a Conta de Integração, você deverá fornecer os detalhes de conexão
   
    ![criar conexão com a conta de integração](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage1.png)
4. Insira os detalhes da Conta de Integração. As propriedades com um asterisco são obrigatórias
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da Conexão * |Digite um nome para a conexão |
   | Uma conta de integração * |Insira o nome da Conta de Integração. Certifique-se de que sua Conta de Integração e que o Aplicativo Lógico estejam na mesma localização do Azure |
   
    Uma vez concluída, os detalhes da conexão se parecerão com estes
   
    ![conexão com a conta de integração](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage2.png)
5. Selecione **Criar**
6. Observe que a conexão foi criada
   
    ![detalhes da conexão com a conta de integração](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage4.png)

#### Codificar Mensagem EDIFACT pelo nome do contrato
1. Forneça a mensagem xml e o nome do contrato de EDIFACT para codificar.
   
   ![fornecer campos obrigatórios](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage6.png)

#### Codificar Mensagem EDIFACT por identidades
1. Forneça o identificador do remetente, o qualificador de remetente, o identificador do destinatário e o qualificador do destinatário conforme configurado no contrato de EDIFACT. Selecione a mensagem xml para codificar
   
    ![fornecer campos obrigatórios](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage7.png)

## A Codificação EDIFACT faz o seguinte
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

## Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

<!---HONumber=AcomDC_0824_2016-->