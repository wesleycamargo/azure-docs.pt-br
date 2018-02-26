---
title: Coletar dados de computadores Linux locais com o Azure Log Analytics | Microsoft Docs
description: Saiba como implantar o agente do Log Analytics para Linux e habilitar a coleta de dados desse sistema operacional com o Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/11/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: bc7c7ea1a01ad784ae53090f1ae0edb042b4f07f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="collect-data-from-linux-computer-hosted-in-your-environment"></a>Coletar dados de computadores Linux hospedados no seu ambiente
O [Azure Log Analytics](log-analytics-overview.md) pode coletar dados diretamente dos computadores Linux físicos ou virtuais e de outros recursos do ambiente em um único repositório para correlação e análise detalhadas.  Este guia de início rápido mostra como configurar e coletar dados do computador Linux com algumas etapas simples.  Para VMs Linux do Azure, consulte o tópico a seguir [Coletar dados sobre as Máquinas Virtuais do Azure](log-analytics-quick-collect-azurevm.md).  

Para entender os requisitos de sistema e de rede para implantar o agente do Linux, examine os [pré-requisitos para o sistema operacional Linux](log-analytics-concept-hybrid.md#prerequisites).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure-portal"></a>Fazer logon no portal do Azure
Faça logon no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar um espaço de trabalho
1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.<br><br> ![portal do Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Clique em **Criar** e, em seguida, selecione opções para os seguintes itens:

  * Forneça um nome para o novo **Espaço de Trabalho do OMS**, como *DefaultLAWorkspace*. 
  * Selecione uma **Assinatura** a vincular escolhendo uma na lista suspensa, se a selecionada por padrão não é adequada.
  * Para **Grupo de Recursos**, selecione um grupo de recursos existente que contém uma ou mais máquinas virtuais do Azure.  
  * Selecione o **Local** no qual as VMs serão implantadas.  Para obter mais informações, consulte em quais [regiões o Log Analytics está disponível](https://azure.microsoft.com/regions/services/).
  * Você pode escolher um de três diferentes **tipos de preço** no Log Analytics, mas para este guia de início rápido, você vai selecionar o preço **gratuito**.  Para obter mais informações sobre os tipos específicos, consulte [Detalhes de preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Depois de fornecer as informações necessárias no painel **Espaço de Trabalho do OMS**, clique em **OK**.  

Enquanto as informações são verificadas e o espaço de trabalho é criado, você pode acompanhar seu progresso no menu **Notificações**. 

## <a name="obtain-workspace-id-and-key"></a>Obter a ID e a chave do espaço de trabalho
Antes de instalar o agente do OMS para Linux, você precisa da ID do espaço de trabalho e da chave para o espaço de trabalho do Log Analytics.  Essas informações são exigidas pelo script de wrapper de agente para configurar o agente corretamente e garantir que ele pode se comunicar com o Log Analytics com êxito.  

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
2. Na lista de espaços de trabalho do Log Analytics, selecione *DefaultLAWorkspace* criado anteriormente.
3. Selecione **Configurações avançadas**.<br><br> ![Configurações avançadas do Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **Fontes Conectadas** e, em seguida, selecione **Servidores Linux**.   
5. O valor à direita da **ID do Espaço de Trabalho** e **Chave Primária**. Copie e cole os dois em seu editor favorito.   

## <a name="install-the-agent-for-linux"></a>Instalar o agente para Linux
As etapas a seguir configuram a instalação do agente para Log Analytics no Azure e na nuvem do Azure Governamental.  

>[!NOTE]
>O agente do OMS para Linux não pode ser configurado para se reportar a mais de um espaço de trabalho do Log Analytics.  

Se seu computador Linux precisa se comunicar através de um servidor proxy para o Log Analytics, a configuração de proxy pode ser especificada na linha de comando, incluindo `-p [protocol://][user:password@]proxyhost[:port]`.  A propriedade *proxyhost* aceita um nome de domínio totalmente qualificado ou o endereço IP do servidor proxy. 

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar o computador Linux para se conectar ao Log Analytics, execute o seguinte comando fornecendo a ID do espaço de trabalho e a chave primária copiadas anteriormente.  O comando a seguir baixa o agente, valida sua soma de verificação e o instala. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    O comando a seguir inclui o parâmetros de proxy `-p` e o exemplo de sintaxe.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador Linux para se conectar ao Log Analytics na nuvem do Azure Governamental, execute o seguinte comando fornecendo a ID do espaço de trabalho e a chave primária copiadas anteriormente.  O comando a seguir baixa o agente, valida sua soma de verificação e o instala. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    O comando a seguir inclui o parâmetros de proxy `-p` e o exemplo de sintaxe.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Reinicie o agente executando o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Coletar dados de desempenho e eventos
O Log Analytics pode coletar eventos do Syslog do Linux e de contadores de desempenho especificados para relatórios e análise de prazo mais longo e tomar uma ação quando determinada condição for detectada.  Siga estas etapas para configurar a coleta de eventos do Syslog do Linux e de vários contadores de desempenho comuns para começar.  

1. Selecione **Syslog**.  
2. Adicione um novo log de eventos digitando o nome do log.  Digite **Syslog** e, em seguida, clique no sinal de adição **+**.  
3. Na tabela, desmarque as severidades **Informações**, **Aviso** e **Depuração**. 
4. Clique em **Salvar** na parte superior da página para salvar a configuração.
5. Selecione **Dados de Desempenho do Linux** para habilitar a coleta de contadores de desempenho em um computador Windows. 
6. Quando você configura os contadores de desempenho do Linux para um novo espaço de trabalho do Log Analytics pela primeira vez, você tem a opção de criar rapidamente vários contadores comuns. Eles são listados com uma caixa de seleção ao lado de cada um.<br><br> ![Contadores de desempenho padrão do Windows selecionados](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png)<br> Clique em **Adicionar os contadores de desempenho selecionados**.  Eles são adicionados e predefinidos com um intervalo de amostragem de coleta de dez segundos.  
7. Clique em **Salvar** na parte superior da página para salvar a configuração.

## <a name="view-data-collected"></a>Exibir os dados coletados
Agora que você habilitou a coleta de dados, vamos executar um exemplo simples de pesquisa de logs para ver alguns dados do computador de destino.  

1. No portal do Azure, navegue para Log Analytics e selecione o espaço de trabalho criado anteriormente.
2. Clique no bloco **Pesquisa de Logs** e, no painel da Pesquisa de Logs, no campo de consulta, digite `Perf` e, em seguida, pressione Enter ou clique no botão de pesquisa à direita do campo de consulta.<br><br> ![Exemplo de consulta da pesquisa de logs do Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Por exemplo, a consulta na imagem a seguir retornou 735 Registros de desempenho.<br><br> ![Resultado da pesquisa de logs do Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, você poderá remover o agente do computador Linux e excluir o espaço de trabalho do Log Analytics.  

Para remover o agente, execute o comando a seguir no computador Linux.  O argumento *--purge* remove completamente o agente e sua configuração.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Para excluir o espaço de trabalho, selecione o espaço de trabalho do Log Analytics criado anteriormente e, na página de recursos, clique em **Excluir**.<br><br> ![Excluir um recurso do Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você está coletando dados operacionais e de desempenho do computador Linux local, você pode facilmente começar a explorar, analisar e agir em relação aos dados coletados *gratuitamente*.  

Para saber como exibir e analisar os dados, continue lendo o tutorial.   

> [!div class="nextstepaction"]
> [Exibir ou analisar dados no Log Analytics](log-analytics-tutorial-viewdata.md)
