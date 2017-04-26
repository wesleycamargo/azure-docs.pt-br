---
title: "Recuperação de desastres de conta da integração de aplicativos B2B lógicos – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Recuperação de desastres de aplicativos lógicos B2B do Azure"
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
ms.date: 04/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 0ebaa553f360cc7deb52139fe9adaaca39a2ee23
ms.lasthandoff: 04/13/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>recuperação de desastres de região cruzada de Aplicativos Lógicos B2B
Cargas de trabalho B2B envolvem transações de dinheiro como pedidos, faturas.  Para a empresa, é importante se recuperar rapidamente para atender a SLAs conforme acordado com seus parceiros durante um evento de desastre de nível de negócios.  Este tópico demonstra como criar um plano de continuidade de negócios para cargas de trabalho B2B.  Ele abrange

* Criar uma conta de integração na região secundária
* Estabelecer uma conexão da região primária para a região secundária 
* Alternar para a região secundária durante um evento de desastre
* Voltar ao evento de pós-desastre na região primária

## <a name="create-an-integration-account-in-secondary-region"></a>Criar uma conta de integração na região secundária
1. Selecionar uma região secundária e criar uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).  

2. Adicione parceiros, esquemas e contratos para os fluxos de mensagens necessária em que o status da execução precisa ser replicado a conta de integração da região secundária.

    > [!Tip]
    > Verifique a consistência da convenção de nomenclatura dos artefatos da conta de integração entre regiões 
    > 
    > 

3. A recomendação é implantar todos os recursos da região primária (por exemplo, bancos de dados SQL do Azure ou DocumentDB, ou barramento de serviço / Hubs de eventos usados para mensagens, APIM, aplicativos lógicos) na região secundária.  

## <a name="establish-a-connection-from-primary-to-secondary"></a>Estabelecer uma conexão da região primária para a região secundária 
Para obter o status da execução da região primária, crie um aplicativo lógico na região secundária.  Deve ter uma **gatilho** e uma **ação**.  O gatilho deve se conectar à conta de integração da região primária e a ação deve se conectar à conta de integração da região secundária.  Com base no intervalo de tempo, o gatilho sonda a tabela de status de execução da região primária, extrai os novos registros, caso haja algum, e a ação atualiza a conta de integração da região secundária. Isso ajuda a obter status de tempo de execução incremental da região primária para região secundária.

Continuidade de negócios na conta de integração de aplicativos lógicos destina-se para dar suporte baseado em protocolos B2B – X12, AS2 e EDIFACT.  Para obter etapas detalhadas, selecione os respectivos links.

* [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12)
* [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)
* EDIFACT (em breve)

## <a name="fail-over-to-secondary-region-during-a-disaster-event"></a>Alternar para a região secundária durante um evento de desastre
Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade de negócios. Região secundária ajuda a recuperar funções de negócios rapidamente para atender aos objetivos de tempo/ponto de recuperação (RPO/RTO) conforme acordado com seus parceiros.  Além disso, minimiza os esforços de failover de uma região para outra região. 

Há uma latência esperada durante a cópia de números de controle do principal para a região secundária.  Para evitar o envio de números de controle duplicados para parceiros durante um evento de desastre, é recomendável aumentar os números de controle de **contratos da região secundária** usando [cmdlets do PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-primary-region-post-disaster-event"></a>Voltar ao evento de pós-desastre na região primária
Quando a região primária está disponível, para voltar a região primária execute as etapas a seguir     
* Pare de aceitar mensagens de parceiros na **região secundária**   
* Eleve os números de controle gerados para todos os **contratos de região primária** usando [cmdlets do PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)   
* Direcione o tráfego da região secundária para a região primária   
* Verifique o aplicativo lógico criado na região secundária para receber o status de execução da primária está habilitado    

## <a name="x12"></a>X12 
Continuidade dos negócios para documentos EDI X12 foi projetada com base em números de controle   
* Números de controle recebidos (mensagens de entrada) de parceiros  
* Números de controle gerados (mensagens de entrada) e envie para parceiros  
    
    > [!Tip]
    > Você também pode usar [modelo de início rápido de X12](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) para criar aplicativos lógicos.  Criar contas de integração primárias e secundárias são os pré-requisitos para usar o modelo.  O modelo ajuda a criar 2 aplicativos lógicos, um número de controle recebido e outro para número de controle gerado.  Ações e gatilhos respectivos são criados nos aplicativos lógicos, conecta o gatilho a conta de integração primária e ação para conta de integração secundária.
    > 
    >

### <a name="control-numbers-received-from-the-partners"></a>Números de controle recebidos de parceiros
1. Criar um [aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) na região secundária   

2. Pesquise **X12** e selecione **X12 – quando um número de controle recebido é modificado**    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

3. Selecionar o gatilho solicita estabelecer uma conexão à conta de integração. Gatilho a ser conectado à conta de integração da região primária.  Dê um nome de conexão, selecione sua **conta de integração da região primária** na lista e clique em criar.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

4. Data e hora para iniciar a sincronização de números de controle é opcional.  Frequência pode ser definida para **dia**, **hora**, **minuto**, ou **segundo** com um intervalo.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

5. Clique em **nova etapa** e **adicionar uma ação**    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

6. Pesquise **X12** e selecione **X12 – adicionar ou atualizar um número de controle recebido**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

7. Ação a ser conectada à conta secundária de integração.  Selecione as listas **alterar conexão** e **adicionar nova conexão** nas contas de integração disponíveis.  Forneça um nome de conexão, selecione sua **conta de integração da região secundária** na lista e clique em criar.     
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

8. Selecione o conteúdo dinâmico e salve o aplicativo lógico   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

9. Com base no intervalo de tempo, o gatilho sonda a tabela de números de controle de região primária recebida, recebe novos registros e ações atualiza a conta de integração da região secundária.  Se eles não são atualizações, o status de gatilho é mostrado como ignorado.
    
    > [!Tip]
    > Habilitar verificação de duplicatas no contrato de receber o estado de tempo de execução configurações mantém recebido controle números e ajuda os acionadores em intervalos regulares
    > 
    >

![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)


### <a name="control-numbers-generated-and-send-to-the-partners"></a>Números de controle gerados e enviados aos parceiros
1. Criar um [aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) na região secundária  

2. Pesquise **X12** e selecione **X12 – quando um número de controle gerado é modificado**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. Selecionar o gatilho solicita estabelecer uma conexão à conta de integração. Gatilho a ser conectado à conta de integração da região primária.  Dê um nome de conexão, selecione sua **conta de integração da região primária** na lista e clique em criar.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. Data e hora para iniciar a sincronização de números de controle é opcional.  Frequência pode ser definida para **dia**, **hora**, **minuto**, ou **segundo** com um intervalo.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. Clique em **nova etapa** e **adicionar uma ação**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. Pesquise **X12** e selecione **X12 – adicionar ou atualizar um número de controle gerado**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. Ação a ser conectada à conta secundária de integração.  Selecione as listas **alterar conexão** e **adicionar nova conexão** nas contas de integração disponíveis.  Forneça um nome de conexão, selecione sua **conta de integração da região secundária** na lista e clique em criar.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. Selecione o conteúdo dinâmico e salve o aplicativo lógico   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. Com base no intervalo de tempo, o gatilho sonda a tabela de números de controle de região primária recebida, recebe novos registros e ações atualiza a conta de integração da região secundária.  Se eles não são atualizações, o status de gatilho é mostrado como ignorado.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

O status de tempo de execução incremental com base no intervalo de tempo se replica da região primária para a região secundária.  Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade de negócios. 

## <a name="as2"></a>AS2 
Continuidade de negócios para documentos que usam o protocolo AS2 é criada com base na ID de mensagem e o valor do MIC

> [!Tip]
    > Você também pode usar [modelo de início rápido de AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) para criar aplicativos lógicos.  Criar contas de integração primárias e secundárias são os pré-requisitos para usar o modelo.  O modelo ajuda a criar um aplicativo lógico, tendo um gatilho e uma ação.  Cria uma conexão de gatilho para conta de integração primária e ação de conta de integração secundária.
    > 
    >

1. Criar um [aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) na região secundária  

2. Pesquise **AS2** e selecione **AS2 – quando um valor de MIC é criado**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. Selecionar o gatilho solicita estabelecer uma conexão à conta de integração. Gatilho a ser conectado à conta de integração da região primária.  Dê um nome de conexão, selecione sua **conta de integração da região primária** na lista e clique em criar.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. Data e hora para iniciar a sincronização de valor do MIC é opcional.  Frequência pode ser definida para **dia**, **hora**, **minuto**, ou **segundo** com um intervalo.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. Clique em **nova etapa** e **adicionar uma ação**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. Pesquise **AS2** e selecione **AS2 – adicionar ou atualizar um MIC**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. Ação a ser conectada à conta secundária de integração.  Selecione as listas **alterar conexão** e **adicionar nova conexão** nas contas de integração disponíveis.  Forneça um nome de conexão, selecione sua **conta de integração da região secundária** na lista e clique em criar.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. Selecione o conteúdo dinâmico e salve o aplicativo lógico   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. Com base no intervalo de tempo, o gatilho sonda a tabela da região primária, recebe novos registros e ações atualiza a conta de integração da região secundária.  Se eles não são atualizações, o status de gatilho é mostrado como ignorado.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

O status de tempo de execução incremental com base no intervalo de tempo se replica da região primária para a região secundária.  Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade de negócios. 

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [monitoramento de mensagens de B2B](logic-apps-monitor-b2b-message.md).   
