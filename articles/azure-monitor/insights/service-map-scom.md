---
title: Integração do Mapa do Serviço com o System Center Operations Manager | Microsoft Docs
description: O Mapa do Serviço é uma solução no Azure que descobre automaticamente os componentes do aplicativo nos sistemas Windows e Linux, e mapeia a comunicação entre os serviços. Este artigo aborda o uso do Mapa de Serviço para criar automaticamente diagramas de aplicativos distribuídos no Operations Manager.
services: monitoring
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren
ms.openlocfilehash: 73e27044fc8cc79b2c95471e30bca558bd14d473
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818746"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Integração do Mapa do Serviço com o System Center Operations Manager

O Mapa do Serviço detecta automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. O Mapa do Serviço permite que você exiba seus servidores da maneira desejada, como sistemas interconectados que fornecem serviços críticos. O Mapa do Serviço mostra as conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP, sem nenhuma configuração necessária além da instalação de um agente. Para obter mais informações, consulte a [documentação do Mapa do Serviço]( service-map.md).

Com essa integração entre o Mapa do Serviço e o System Center Operations Manager, você pode criar automaticamente diagramas de aplicativos distribuídos no Operations Manager com base em mapas de dependência dinâmica no Mapa do Serviço.

## <a name="prerequisites"></a>Pré-requisitos
* Um grupo de gerenciamento do Operations Manager (2012 R2 ou posterior) que gerencia um conjunto de servidores.
* Um workspace do Log Analytics com a solução do Mapa do Serviço habilitada.
* Um conjunto de servidores (pelo menos um) que está sendo gerenciado pelo Operations Manager e enviando dados para o Mapa do Serviço. Há suporte para servidores Windows e Linux.
* Uma entidade de serviço com acesso à assinatura do Azure que está associada ao workspace do Log Analytics. Para obter mais informações, acesse [Criar uma entidade de serviço](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalar o pacote de gerenciamento do Mapa do Serviço
A integração entre o Operations Manager e o Mapa do Serviço é habilitada pela importação do pacote de gerenciamento Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Você pode baixar o pacote de gerenciamento no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). O pacote contém os seguintes pacotes de gerenciamento:
* Exibições de Aplicativo do Mapa do Serviço da Microsoft
* Mapa do Serviço Interno do Microsoft System Center
* Substituições do Mapa do Serviço do Microsoft System Center
* Mapa do Serviço do Microsoft System Center

## <a name="configure-the-service-map-integration"></a>Configurar a integração do Mapa do Serviço
Depois de instalar o pacote de gerenciamento do Mapa do Serviço, um novo nó, **Mapa do Serviço**, é exibido em **Operations Management Suite** no painel **Administração**.

>[!NOTE]
>[Operations Management Suite foi uma coleção de serviços](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) que incluiu o Log Analytics, que agora faz parte do [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Para configurar a integração do Mapa do Serviço, faça o seguinte:

1. Para abrir o assistente de configuração, no painel **Visão Geral do Mapa do Serviço**, clique em **Adicionar workspace**.  

    ![Painel Visão Geral do Mapa do Serviço](media/service-map-scom/scom-configuration.png)

2. Na janela **Configuração da Conexão**, insira o nome ou a ID do locatário, a ID do aplicativo (também conhecida como o nome de usuário ou a clientID) e a senha da entidade de serviço e, em seguida, clique em **Avançar**. Para obter mais informações, acesse Criar uma entidade de serviço.

    ![A janela Configuração da Conexão](media/service-map-scom/scom-config-spn.png)

3. Na janela **Seleção de Assinatura**, selecione a assinatura do Azure, o grupo de recursos do Azure (aquele que contém o workspace do Log Analytics) e o workspace do Log Analytics e, em seguida, clique em **Avançar**.

    ![O workspace de configuração do Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. No **seleção de grupo do computador** janela, que você escolha quais grupos de máquina do mapa de serviço que deseja sincronizar com o Operations Manager. Clique em **adicionar ou remover grupos de computadores**, escolha grupos na lista de **grupos de computadores disponíveis**e clique em **adicionar**.  Quando você terminar de selecionar os grupos, clique em **OK** para concluir.

    ![Os grupos de computadores de configuração do Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. Na janela **Seleção de Servidor**, você configura o Grupo de Servidores do Mapa do Serviço com os servidores que você deseja sincronizar entre o Operations Manager e o Mapa do Serviço. Clique em **Adicionar/Remover Servidores**.   

    Para que a integração crie um diagrama de aplicativo distribuído para um servidor, o servidor deve ser/estar:

    * Gerenciado pelo Operations Manager
    * Gerenciado pelo Mapa do Serviço
    * Listado no Grupo de Servidores do Mapa do Serviço

    ![O grupo de configuração do Operations Manager](media/service-map-scom/scom-config-group.png)

6. Opcional: Selecione o pool de recursos do Servidor de Gerenciamento para se comunicar com o Log Analytics e, em seguida, clique em **Adicionar Workspace**.

    ![O pool de recursos de configuração do Operations Manager](media/service-map-scom/scom-config-pool.png)

    Pode levar alguns minutos para configurar e registrar o workspace do Log Analytics. Depois que ele for configurado, o Operations Manager iniciará a primeira sincronização do Mapa do Serviço.

    ![O pool de recursos de configuração do Operations Manager](media/service-map-scom/scom-config-success.png)


## <a name="monitor-service-map"></a>Monitorar o Mapa do Serviço
Depois que o workspace do Log Analytics estiver conectado, uma nova pasta, Mapa do Serviço, será exibida no painel **Monitoramento** do console do Operations Manager.

![O painel Monitoramento do Operations Manager](media/service-map-scom/scom-monitoring.png)

A pasta do Mapa do Serviço tem quatro nós:
* **Alertas Ativos**: Lista todos os alertas ativos sobre a comunicação entre o Operations Manager e o Mapa do Serviço.  Observe que esses alertas não estão que sendo sincronizados para o Operations Manager de alertas do Log Analytics.

* **Servidores**: Lista os servidores monitorados configurados para sincronização por meio do Mapa do Serviço.

    ![O painel Monitorando Servidores do Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Exibições de Dependência do Grupo de Computadores**: Lista todos os grupos de computadores que foram sincronizados por meio do Mapa do Serviço. É possível clicar em qualquer grupo para exibir seu diagrama de aplicativo distribuído.

    ![O diagrama de aplicativo distribuído do Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Exibições de Dependências do Servidor**: Lista todos os servidores que foram sincronizados por meio do Mapa do Serviço. É possível clicar em qualquer servidor para exibir seu diagrama de aplicativo distribuído.

    ![O diagrama de aplicativo distribuído do Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Editar ou excluir o workspace
É possível editar ou excluir o workspace configurado por meio do painel **Visão Geral do Mapa do Serviço** (painel **Administração** &gt; **Operations Management Suite** > **Mapa do Serviço**).

>[!NOTE]
>[Operations Management Suite foi uma coleção de serviços](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) que incluiu o Log Analytics, que agora faz parte do [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Por enquanto, você só pode configurar um workspace do Log Analytics.

![O painel Editar Workspace do Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurar regras e substituições
Uma regra, _Microsoft.SystemCenter.ServiceMapImport.Rule_, é criada para buscar informações periodicamente do Mapa do Serviço. Para alterar os intervalos de sincronização, é possível configurar substituições da regra (painel **Criação** > **Regras** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![A janela de propriedades Substituições do Operations Manager](media/service-map-scom/scom-overrides.png)

* **Enabled**: Habilite ou desabilite as atualizações automáticas.
* **IntervalMinutes**: Redefine o tempo entre as atualizações. O intervalo padrão é uma hora. Se você desejar sincronizar os mapas do servidor com mais frequência, poderá alterar o valor.
* **TimeoutSeconds**: Redefina a duração de tempo antes que a solicitação atinja o tempo limite.
* **TimeWindowMinutes**: Redefina a janela de tempo para consulta dos dados. O padrão é uma janela de 60 minutos. O valor máximo permitido pelo Mapa do Serviço é 60 minutos.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

O design atual apresenta os seguintes problemas e limitações:
* Você só pode se conectar a um único workspace do Log Analytics.
* Embora você possa adicionar servidores ao Grupo de Servidores do Mapa do Serviço manualmente por meio do painel **Criação**, os mapas desses servidores não são sincronizados imediatamente.  Eles serão sincronizados do mapa de serviço durante o próximo ciclo de sincronização.
* Se você fizer alterações para os diagramas de aplicativo distribuído criado pelo pacote de gerenciamento, essas alterações provavelmente serão substituídas na próxima sincronização com o mapa de serviço.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço
Para obter a documentação oficial do Azure sobre como criar uma entidade de serviço, consulte:
* [Criar uma entidade de serviço usando o PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Criar uma entidade de serviço usando a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Criar uma entidade de serviço usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Comentários
Você tem algum comentário sobre o Mapa de Serviço ou sobre esta documentação? Visite nossa [página do User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), na qual você pode sugerir recursos ou votar em sugestões existentes.
