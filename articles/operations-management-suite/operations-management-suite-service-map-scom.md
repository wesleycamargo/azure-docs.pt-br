---
title: "Integração do Mapa do Serviço com o System Center Operations Manager | Microsoft Docs"
description: "O Mapa do Serviço é uma solução do Operations Management Suite que descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo aborda o uso do Mapa de Serviço para criar automaticamente diagramas de aplicativos distribuídos no Operations Manager."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0b710c338be3a2c2fde6bba43173f7c5f480e357
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---

# <a name="service-map-integration-with-system-center-operations-manager"></a>Integração do Mapa do Serviço com o System Center Operations Manager
  > [!NOTE]
  > Como esse recurso está em visualização particular, ele não deve ser usado em sistemas de produção.
  > 
  
O Mapa do Serviço do Operations Management Suite descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. O Mapa do Serviço permite que você exiba seus servidores da maneira desejada, como sistemas interconectados que fornecem serviços críticos. O Mapa do Serviço mostra as conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP, sem nenhuma configuração necessária além da instalação de um agente. Para obter mais informações, consulte a [documentação do Mapa do Serviço](operations-management-suite-service-map.md).

Com essa integração entre o Mapa do Serviço e o System Center Operations Manager, você pode criar automaticamente diagramas de aplicativos distribuídos no Operations Manager com base em mapas de dependência dinâmica no Mapa do Serviço.

## <a name="prerequisites"></a>Pré-requisitos
* Um grupo de gerenciamento do Operations Manager que gerencia um conjunto de servidores.
* Um espaço de trabalho do Operations Management Suite com a solução Mapa do Serviço habilitada.
* Um conjunto de servidores (pelo menos um) que está sendo gerenciado pelo Operations Manager e enviando dados para o Mapa do Serviço. Há suporte para servidores Windows e Linux.
* Uma entidade de serviço com acesso à assinatura do Azure associada ao espaço de trabalho do Operations Management Suite. Para obter mais informações, acesse [Criar uma entidade de serviço](#creating-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalar o pacote de gerenciamento do Mapa do Serviço
A integração entre o Operations Manager e o Mapa do Serviço é habilitada pela importação do pacote de gerenciamento Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). O pacote contém os seguintes pacotes de gerenciamento:
* Exibições de Aplicativo do Mapa do Serviço da Microsoft
* Mapa do Serviço Interno do Microsoft System Center
* Substituições do Mapa do Serviço do Microsoft System Center
* Mapa do Serviço do Microsoft System Center

## <a name="configure-the-service-map-integration"></a>Configurar a integração do Mapa do Serviço
Depois de instalar o pacote de gerenciamento do Mapa do Serviço, um novo nó, **Mapa do Serviço**, é exibido em **Operations Management Suite** no painel **Administração**. 

Para configurar a integração do Mapa do Serviço, faça o seguinte:

1. Para abrir o assistente de configuração, no painel **Visão Geral do Mapa do Serviço**, clique em **Adicionar espaço de trabalho**.  

    ![Painel Visão Geral do Mapa do Serviço](media/oms-service-map/scom-configuration.png)

2. Na janela **Configuração da Conexão**, insira o nome ou a ID do locatário, a ID do aplicativo (também conhecida como o nome de usuário ou a clientID) e a senha da entidade de serviço e, em seguida, clique em **Avançar**. Para obter mais informações, acesse [Criar uma entidade de serviço](#creating-a-service-principal).

    ![A janela Configuração da Conexão](media/oms-service-map/scom-config-spn.png)

3. Na janela **Seleção de Assinatura**, selecione a assinatura do Azure, o grupo de recursos do Azure (aquele que contém o espaço de trabalho do Operations Management Suite) e o espaço de trabalho do Operations Management Suite e, em seguida, clique em **Avançar**.

    ![O espaço de trabalho de configuração do Operations Manager](media/oms-service-map/scom-config-workspace.png)

4. Na janela **Seleção de Servidor**, você configura o Grupo de Servidores do Mapa do Serviço com os servidores que você deseja sincronizar entre o Operations Manager e o Mapa do Serviço. Clique em **Adicionar/Remover Servidores**.   
    
    Para que a integração crie um diagrama de aplicativo distribuído para um servidor, o servidor deve ser/estar:

    * Gerenciado pelo Operations Manager.
    * Gerenciado pelo Mapa do Serviço.
    * Listado no Grupo de Servidores do Mapa do Serviço.

    ![O grupo de configuração do Operations Manager](media/oms-service-map/scom-config-group.png)

5. Opcional: selecione o pool de recursos do Servidor de Gerenciamento para se comunicar com o Operations Management Suite e, em seguida, clique em **Adicionar Espaço de Trabalho**.

    ![O pool de recursos de configuração do Operations Manager](media/oms-service-map/scom-config-pool.png)

    Pode levar alguns minutos para configurar e registrar o espaço de trabalho do Operations Management Suite. Depois que ele for configurado, o Operations Manager iniciará a primeira sincronização do Mapa do Serviço por meio do Operations Management Suite.

    ![O pool de recursos de configuração do Operations Manager](media/oms-service-map/scom-config-success.png)

    >[!NOTE]
    >O intervalo de sincronização padrão é definido como 60 minutos. É possível configurar substituições para alterar o intervalo de sincronização. Você também pode adicionar servidores ao Grupo de Servidores do Mapa do Serviço manualmente por meio do painel **Criação**. Para fazer isso, selecione **Grupos** e, em seguida, pesquise **Grupo de Servidores do Mapa do Serviço**. Os mapas do servidor desses servidores são sincronizados com a próxima sincronização, que se baseia no intervalo de sincronização configurado.

## <a name="monitor-service-map"></a>Monitorar o Mapa do Serviço
Depois que o espaço de trabalho do Operations Management Suite estiver conectado, uma nova pasta, Mapa do Serviço, será exibida no painel **Monitoramento** do console do Operations Manager.

![O painel Monitoramento do Operations Manager](media/oms-service-map/scom-monitoring.png)

A pasta do Mapa do Serviço tem três nós:
* **Alertas Ativos**: lista todos os alertas ativos sobre a comunicação entre o Operations Manager e a solução Mapa do Serviço no Operations Management Suite.

    >[!NOTE]
    >Esses alertas não são os alertas do Operations Management Suite exibidos no Operations Manager.

* **Servidores**: lista os servidores monitorados configurados para sincronização por meio do Mapa do Serviço.

    ![O painel Monitorando Servidores do Operations Manager](media/oms-service-map/scom-monitoring-servers.png)

* **Exibições de Dependência de Servidor**: lista todos os servidores sincronizados por meio do Mapa do Serviço. É possível clicar em qualquer servidor para exibir seu diagrama de aplicativo distribuído.

    ![O diagrama de aplicativo distribuído do Operations Manager](media/oms-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Editar ou excluir o espaço de trabalho
É possível editar ou excluir o espaço de trabalho configurado por meio do painel **Visão Geral do Mapa do Serviço** (painel **Administração** > **Operations Management Suite** > **Mapa do Serviço**). No momento, é possível configurar apenas um espaço de trabalho do Operations Management Suite.

![O painel Editar Espaço de Trabalho do Operations Manager](media/oms-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurar regras e substituições
Uma regra, _Microsoft.SystemCenter.ServiceMapImport.Rule_, é criada para buscar informações periodicamente do Mapa do Serviço. Para alterar os intervalos de sincronização, é possível configurar substituições da regra (painel **Criação** > **Regras** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![A janela de propriedades Substituições do Operations Manager](media/oms-service-map/scom-overrides.png)

* **Enabled**: habilite ou desabilite atualizações automáticas. 
* **IntervalMinutes**: redefina o tempo entre as atualizações. O intervalo padrão é uma hora. Se você desejar sincronizar os mapas do servidor com mais frequência, poderá alterar o valor.
* **TimeoutSeconds**: redefina a duração de tempo antes que a solicitação atinja o tempo limite. 
* **TimeWindowMinutes**: redefina a janela de tempo para consultar os dados. O padrão é uma janela de 60 minutos. O valor máximo permitido pelo Mapa do Serviço é 60 minutos.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

O design atual apresenta os seguintes problemas e limitações:
* Embora você possa adicionar servidores ao Grupo de Servidores do Mapa do Serviço manualmente por meio do painel **Criação**, os mapas desses servidores são sincronizados por meio do Mapa do Serviço somente durante o próximo ciclo de sincronização. A configuração padrão é 60 minutos, mas é possível substituir o intervalo. 
* Você pode se conectar a um único espaço de trabalho do Operations Management Suite.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço
Para obter a documentação oficial do Azure sobre como criar uma entidade de serviço, consulte:
* [Criar uma entidade de serviço usando o PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Criar uma entidade de serviço usando a CLI do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Criar uma entidade de serviço usando o portal do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Comentários
Você tem algum comentário sobre o Mapa de Serviço ou sobre esta documentação? Visite nossa [página do User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), na qual você pode sugerir recursos ou votar em sugestões existentes.

