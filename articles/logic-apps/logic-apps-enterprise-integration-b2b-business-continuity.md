---
title: "Recuperação de desastre da conta de integração dos Aplicativos Lógicos B2B: Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Recuperação de desastre dos Aplicativos Lógicos B2B"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: e28c1410145d8da168a73e74251ac037997d1752
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Recuperação de desastre de região cruzada dos Aplicativos Lógicos B2B
As cargas de trabalho B2B envolvem transações de dinheiro como pedidos e faturas. Durante um evento de desastre, é essencial que uma empresa se recupere rapidamente para atender aos SLAs no nível de negócios definidos com seus parceiros. Este artigo demonstra como criar um plano de continuidade dos negócios para cargas de trabalho B2B. 

* Prontidão para recuperação de desastre 
* Alternar para a região secundária durante um evento de desastre 
* Voltar ao evento de pós-desastre na região primária

## <a name="disaster-recovery-readiness"></a>Prontidão para recuperação de desastre  

1. Identificar uma região secundária e criar uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na região secundária 

2. Adicione parceiros, esquemas e contratos para os fluxos de mensagens necessária em que o status da execução precisa ser replicado a conta de integração da região secundária.

    > [!Tip]
    > Verifique se há consistência na convenção de nomenclatura do artefato da conta de integração entre as regiões. 
    > 
    > 

3. Para obter o status da execução da região primária, crie um aplicativo lógico na região secundária.  Deve ter uma **gatilho** e uma **ação**.  O gatilho deve se conectar à conta de integração da região primária e a ação deve se conectar à conta de integração da região secundária.  Com base no intervalo de tempo, o gatilho sonda a tabela de status de execução da região primária, extrai os novos registros, caso haja algum, e a ação atualiza a conta de integração da região secundária. Isso ajuda a obter status de tempo de execução incremental da região primária para região secundária.

4. Continuidade de negócios na conta de integração de aplicativos lógicos destina-se para dar suporte baseado em protocolos B2B – X12, AS2 e EDIFACT.  Para obter etapas detalhadas, selecione os respectivos links.

5. A recomendação é implantar todos os recursos da região primária em uma região secundária também. Os recursos da região primária incluem o Banco de Dados SQL do Azure ou o Azure DocumentDB, Barramento de Serviço do Azure/Hubs de Eventos do Azure usados para mensagens, Gerenciamento de API do Azure e o recurso Aplicativos Lógicos do Serviço de Aplicativo do Azure.   

6. Estabeleça uma conexão de uma região primária para uma região secundária. Para efetuar pull do status de execução de uma região primária, crie um aplicativo lógico em uma região secundária. Ela deve ter um gatilho e uma ação. O gatilho deve estar conectado a uma conta de integração da região primária. A ação deve estar conectada a uma conta de integração da região secundária. Com base no intervalo de tempo, o gatilho sonda a tabela de status de execução da região primária e efetua pull dos novos registros, se houver. A ação atualiza-os para uma conta de integração da região secundária. Esse processo ajuda a obter o status em tempo de execução incremental da região primária para a região secundária.

A continuidade dos negócios em uma conta de integração dos Aplicativos Lógicos fornece suporte baseado nos protocolos B2B X12, AS2 e EDIFACT. Para obter etapas detalhadas sobre como usar X12 e AS2, consulte [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) e [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) neste artigo.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Fazer failover para uma região secundária durante um evento de desastre
Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade de negócios. Uma região secundária ajuda uma empresa a recuperar as funções rapidamente para atender ao RPO/RTO definido por seus parceiros. Ela também minimiza os esforços de failover de uma região para outra. 

Há uma latência esperada durante a cópia de números de controle de uma região primária para uma região secundária. Para evitar o envio de números de controle gerados duplicados para parceiros durante um evento de desastre, a recomendação é aumentar os números de controle nos contratos da região secundária usando [cmdlets do PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Fazer failback para o evento pós-desastre em uma região primária
Para fazer failback em uma região primária quando ela estiver disponível, siga estas etapas:

1. Pare de aceitar mensagens de parceiros na região secundária.  

2. Aumente os números de controle gerados para todos os contratos da região primária usando [cmdlets do PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Direcione o tráfego da região secundária para a região primária.

4. Verifique se o aplicativo lógico criado na região secundária para efetuar pull do status de execução da região primária está habilitado.

## <a name="x12"></a>X12 
A continuidade dos negócios para documentos EDI X12 tem como base os números de controle:
* Números de controle recebidos (mensagens de entrada) de parceiros  
* Números de controle gerados (mensagens de entrada) e enviados para parceiros 
    
    > [!Tip]
    > Você também pode usar o [modelo de início rápido de X12](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) para criar aplicativos lógicos. Criar contas de integração primárias e secundárias são os pré-requisitos para usar o modelo. O modelo ajuda a criar dois aplicativos lógicos, um para números de controle recebidos e outro para números de controle gerados. As respectivas ações e os respectivos gatilhos são criados nos aplicativos lógicos, conectando o gatilho à conta de integração primária e a ação à conta de integração secundária.
    > 
    >

### <a name="control-numbers-received-from-partners"></a>Números de controle recebidos de parceiros

1. Habilitar verificações de duplicatas nas configurações de recebimento do contrato   
![Pesquisa de X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

2. Crie um [aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) em uma região secundária. 

3. Pesquise **X12** e selecione **X12 – quando um número de controle recebido é modificado**.   
![Pesquisa de X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

4. O gatilho solicita que você estabeleça uma conexão com uma conta de integração. O gatilho deve estar conectado a uma conta de integração da região primária. Insira um nome de conexão, selecione sua **conta de integração da região primária** na lista e clique em **Criar**.  
![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

5. A configuração **DateTime para iniciar a sincronização de números de controle** é opcional. A **Frequência** pode ser definida como **Dia**, **Hora**, **Minuto** ou **Segundo** com um intervalo.  
![DateTime e Frequency](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

6. Selecione **Nova etapa** > **Adicionar uma ação**.    
![Adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

7. Pesquise **X12** e selecione **X12 – adicionar ou atualizar um número de controle recebido**.   
![Modificação de números de controle recebidos](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

8. Para conectar uma ação a uma conta de integração da região secundária, selecione **Alterar conexão** > **Adicionar nova conexão** para obter uma lista das contas de integração disponíveis. Insira um nome de conexão, selecione sua **conta de integração da região secundária** na lista e clique em **Criar**.   
![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

9. Selecione o conteúdo dinâmico e salve o aplicativo lógico. 
![Conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

10. Com base no intervalo de tempo, o gatilho sonda a tabela de números de controle recebidos da região primária e efetua pull dos novos registros. A ação atualiza-os para a conta de integração da região secundária. Se não houver nenhuma atualização, o status do gatilho será exibido como **Ignorado**.
![Tabela de números de controle](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)

### <a name="control-numbers-generated-and-sent-to-partners"></a>Números de controle gerados e enviados aos parceiros
1. Crie um [aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) em uma região secundária.

2. Pesquise **X12** e selecione **X12 – quando um número de controle gerado é modificado**.  
![Modificação de números de controle gerados](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. O gatilho solicita que você estabeleça uma conexão com uma conta de integração. O gatilho deve estar conectado a uma conta de integração da região primária. Insira um nome de conexão, selecione sua **conta de integração da região primária** na lista e clique em **Criar**.   
![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. A configuração **DateTime para iniciar a sincronização de números de controle** é opcional. A **Frequência** pode ser definida como **Dia**, **Hora**, **Minuto** ou **Segundo** com um intervalo.  
![DateTime e Frequency](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. Selecione **Nova etapa** > **Adicionar uma ação**.  
![Adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. Pesquise **X12** e selecione **X12 – adicionar ou atualizar um número de controle gerado**.   
![Adição ou atualização de números de controle gerados](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. Para conectar uma ação a uma conta de integração secundária, selecione **Alterar conexão** > **Adicionar nova conexão** para obter uma lista das contas de integração disponíveis. Insira um nome de conexão, selecione sua **conta de integração da região secundária** na lista e clique em **Criar**.   
![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. Selecione o conteúdo dinâmico e salve o aplicativo lógico. 
![Conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. Com base no intervalo de tempo, o gatilho sonda a tabela de números de controle recebidos da região primária e efetua pull dos novos registros. A ação atualiza-os para a conta de integração da região secundária. Se não houver nenhuma atualização, o status do gatilho será exibido como **Ignorado**.  
![Tabela de números de controle](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

Com base no intervalo de tempo, o status em tempo de execução incremental é replicado de uma região primária para uma região secundária. Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade de negócios. 

## <a name="as2"></a>AS2 
A continuidade dos negócios para documentos que usam o protocolo AS2 se baseia na ID da mensagem e no valor do MIC.

> [!Tip]
    > Você também pode usar o [modelo de início rápido de AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) para criar aplicativos lógicos. Criar contas de integração primárias e secundárias são os pré-requisitos para usar o modelo. O modelo ajuda a criar um aplicativo lógico que tem um gatilho e uma ação. O aplicativo lógico cria uma conexão de um gatilho para uma conta de integração primária e uma ação para uma conta de integração secundária.
    > 
    >

1. Crie um [aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) na região secundária.  

2. Pesquise **AS2** e selecione **AS2 – quando um valor de MIC é criado**.   
![Pesquisa de AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. Um gatilho solicita que você estabeleça uma conexão com uma conta de integração. O gatilho deve estar conectado a uma conta de integração da região primária. Insira um nome de conexão, selecione sua **conta de integração da região primária** na lista e clique em **Criar**.   
![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. A configuração **DateTime para iniciar a sincronização de valor do MIC** é opcional. A **Frequência** pode ser definida como **Dia**, **Hora**, **Minuto** ou **Segundo** com um intervalo.   
![DateTime e Frequency](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. Selecione **Nova etapa** > **Adicionar uma ação**.  
![Adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. Pesquise **AS2** e selecione **AS2 – adicionar ou atualizar um MIC**.  
![Adição ou atualização do MIC](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. Para conectar uma ação a uma conta de integração secundária, selecione **Alterar conexão** > **Adicionar nova conexão** para obter uma lista das contas de integração disponíveis. Insira um nome de conexão, selecione sua **conta de integração da região secundária** na lista e clique em **Criar**.    
![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. Selecione o conteúdo dinâmico e salve o aplicativo lógico.   
![Conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. Com base no intervalo de tempo, o gatilho sonda a tabela da região primária e efetua pull dos novos registros. A ação atualiza-os para a conta de integração da região secundária. Se não houver nenhuma atualização, o status do gatilho será exibido como **Ignorado**.  
![Tabela da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

Com base no intervalo de tempo, o status em tempo de execução incremental é replicado da região primária para a região secundária. Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade de negócios. 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o [monitoramento de mensagens de B2B](logic-apps-monitor-b2b-message.md).   


