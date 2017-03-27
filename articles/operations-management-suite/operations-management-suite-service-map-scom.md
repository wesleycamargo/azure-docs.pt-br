---
title: "Integração do Mapa do Serviço com o System Center Operations Manager | Microsoft Docs"
description: "O Mapa do Serviço é uma solução do OMS (Operations Management Suite) que descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços.  Este artigo fornece detalhes sobre como usar o Mapa de Serviço para automaticamente criar Diagramas de Aplicativos Distribuídos no SCOM."
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
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 389c01234acff068dc90f3cdfdc4916a9d76d244
ms.lasthandoff: 03/09/2017


---

# <a name="service-map-integration-with-system-center-operations-manager-integration"></a>Integração do Mapa do Serviço com o System Center Operations Manager | Integração
  > [!NOTE]
  > Esse recurso está no modo de visualização particular, portanto não deve ser usado em sistemas de produção.
  > 
  
O Mapa do Serviço Operations Management Suite (OMS) que descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Ele permite que você exiba os seus servidores da maneira como pensa neles – como sistemas interconectados que fornecem serviços essenciais. O Mapa do Serviço mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP sem nenhuma configuração necessária além da instalação de um agente.  Para obter mais detalhes, consulte a [Documentação do Mapa do Serviço](operations-management-suite-service-map.md).

Com essa integração entre o Mapa do Serviço e o System Center Operations Manager (SCOM), você pode automaticamente criar Diagramas de Aplicativos Distribuídos no SCOM com base em mapas de dependência dinâmica no Mapa do Serviço.

## <a name="prerequisites"></a>Pré-requisitos
1.    Um grupo de gerenciamento do SCOM administrando um conjunto de servidores.
2.    Um espaço de trabalho do OMS com a solução do Mapa do Serviço habilitada.
3.    Um conjunto de servidores (pelo menos um) que está sendo gerenciado pelo SCOM e enviando dados para o Mapa do Serviço.  Há suporte para servidores Windows e Linux.
4.    Uma Entidade de Serviço com acesso à Assinatura do Azure que está associada ao Espaço de Trabalho do OMS.  [Mais informações sobre como criar uma Entidade de Serviço](#creating-a-service-principal).

## <a name="installing-service-map-management-pack"></a>Instalando o Pacote de Gerenciamento do Mapa do Serviço
A integração entre o SCOM e o Mapa do Serviço é habilitada através da importação do Pacote de Gerenciamento Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb).  O pacote contém os seguintes Pacotes de Gerenciamento:
* Modos de Exibição do Aplicativo do Microsoft ServiceMap
* Microsoft System Center ServiceMap Interna
* Substituições do Microsoft System Center ServiceMap
* Microsoft System Center ServiceMap

## <a name="configuring-the-service-map-integration"></a>Configurando a Integração do Mapa do Serviço
1. Depois de instalar o pacote de gerenciamento do ServiceMap, haverá um novo nó, o Mapa do Serviço, no Operations Management Suite no painel de administração.
2. Clique em "Adicionar espaço de trabalho" no painel Visão Geral do Mapa do Serviço para abrir o assistente de configuração.

    ![Assistente de Configuração do SCOM](media/oms-service-map/scom-configuration.png)

3. A primeira etapa no assistente é a Configuração de Conexão onde você insere as informações para a Entidade de Serviço do Azure. Insira a ID ou nome de Locatário, ID do Aplicativo (ou Nome de Usuário ou ClientID) e a Senha da Entidade de Serviço.  [Mais informações sobre como criar uma Entidade de Serviço](#creating-a-service-principal).

    ![SPN da Configuração do SCOM](media/oms-service-map/scom-config-spn.png)

4. A próxima etapa é selecionar a Assinatura do Azure, o Grupo de Recursos do Azure (aquele que contém o Espaço de Trabalho do OMS) e o Espaço de Trabalho do OMS.

    ![Espaço de Trabalho da Configuração do SCOM](media/oms-service-map/scom-config-workspace.png)

5. A próxima etapa é configurar o Grupo de Servidores do Mapa do Serviço com os servidores que você gostaria de sincronizar entre o SCOM e o Mapa do Serviço.  Clique em Adicionar/Remover Servidores… . Observe que, para a integração criar um Diagrama de Aplicativo Distribuído para um servidor, o servidor deve: 1) ser gerenciado pelo SCOM, 2) ser gerenciado pelo Mapa do Serviço e 3) ser listado no Grupo de Servidores do Mapa do Serviço.

    ![Grupo da Configuração do SCOM](media/oms-service-map/scom-config-group.png)

6. Opcional - escolha o pool de recursos do Servidor de Gerenciamento para se comunicar com o OMS e clique em "Adicionar Espaço de Trabalho".

    ![Pool de Recursos da Configuração do SCOM](media/oms-service-map/scom-config-pool.png)

7. Observe que levará alguns minutos para configurar e registrar o espaço de trabalho do OMS. Depois que isso estiver configurado, o SCOM iniciará a primeira sincronização do Mapa do Serviço a partir do OMS.

    ![Pool de Recursos da Configuração do SCOM](media/oms-service-map/scom-config-success.png)

**Observação:** o intervalo de sincronização padrão é definido como 60 minutos. Os usuários podem definir substituições para alterar o intervalo de sincronização. Os usuários também podem adicionar servidores ao Grupo de Servidores do Mapa do Serviço manualmente por meio do painel Criação (painel Criação--> Grupos e, em seguida, pesquise "Grupo de Servidores do Mapa do Serviço"). Os mapas do servidor desses servidores serão sincronizados com a próxima sincronização (com base no intervalo de sincronização configurado).

## <a name="monitoring-service-map"></a>Mapa do Serviço de Monitoramento
Quando o espaço de trabalho do OMS for conectado, uma nova pasta Mapa do Serviço será exibida no painel Monitoramento do console do SCOM.
![Monitoramento do SCOM](media/oms-service-map/scom-monitoring.png)

A pasta do Mapa do Serviço tem três nós:
### <a name="all-alerts"></a>Todos os Alertas:
Exibe todos os alertas sobre a comunicação entre as soluções SCOM e Mapa do Serviço no OMS.

**Observação:** esses não são alertas do OMS que estão sendo exibidos no SCOM.
### <a name="servers"></a>Servidores:
Conterá a lista de servidores monitorados configurados para sincronizar a partir do Mapa do Serviço.

![Servidores de Monitoramento do SCOM](media/oms-service-map/scom-monitoring-servers.png)

### <a name="server-dependency-views"></a>Modos de Exibição de Dependências do Servidor:
Essa exibição terá a lista de todos os servidores sincronizados do Mapa de Serviço. Os usuários podem clicar em qualquer servidor para exibir seu Diagrama de Aplicativo Distribuído.

![Diagrama do Aplicativo Distribuído do SCOM](media/oms-service-map/scom-dad.png)

## <a name="editdelete-workspace"></a>Editar/Excluir o Espaço de Trabalho:
Os usuários podem editar ou excluir o espaço de trabalho configurado por meio do painel Visão Geral do Mapa do Serviço (painel Administração--> Operations Management Suite--> Mapa do Serviço).  Observe que, por enquanto, você só pode configurar um espaço de trabalho do OMS.

![Espaço de Trabalho de Edição do SCOM](media/oms-service-map/scom-edit-workspace.png)

## <a name="configuring-rules-and-overrides"></a>Como configurar Regras e Substituições:
Uma Regra **_Microsoft.SystemCenter.ServiceMap.Import.Rule**_ é criada para periodicamente buscar informações a partir do Mapa do Serviço.  Os usuários podem configurar Substituições dessa regra para alterar os intervalos de sincronização.
Painel de criação --> Regras --> Microsoft.SystemCenter.ServiceMapImport.Rule

![Substituições do SCOM](media/oms-service-map/scom-overrides.png)
* **Enabled** - habilitar/desabilitar atualizações automáticas 
* **IntervalSeconds** - o tempo entre as atualizações.  O padrão é 1 hora. Os usuários podem alterar o valor aqui se quiserem sincronizar os mapas de servidor com mais frequência.
* **TimeoutSeconds** - a quantidade de tempo antes da solicitação expirar 
* **TimeWindowMinutes** - quão ampla é a consulta de dados.  O padrão é uma janela de 60 minutos. O valor máximo é de 1 hora (o máximo permitido pelo Mapa do Serviço).

## <a name="known-issueslimitations"></a>Problemas Conhecidos/Limitações:
No projeto atual:
1. Uma vez que os usuários podem adicionar servidores ao "Grupo de Servidores do Mapa do Serviço" manualmente por meio do painel de criação, os mapas desses servidores serão sincronizados a partir do Mapa do Serviço somente durante o próximo ciclo de sincronização (60 minutos por padrão. Os usuários podem substituir o tempo de sincronização.). 
2. Os usuários podem se conectar a um único espaço de trabalho do OMS.

## <a name="creating-a-service-principal"></a>Como Criar uma Entidade de Serviço
Os links a seguir levarão você até a documentação oficial do Azure que apresenta três formas distintas de criar uma Entidade de Serviço.
* [Criar Entidade de Serviço com o PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Criar Entidade de Serviço com a CLI do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Criar Entidade de Serviço com o Portal do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Comentários
Você tem algum comentário sobre o Mapa de Serviço ou sobre esta documentação?  Visite nossa [página User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), onde você pode sugerir recursos ou votar em sugestões existentes.

