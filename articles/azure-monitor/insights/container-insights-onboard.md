---
title: Como carregar o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como carregar e configurar o Azure Monitor para contêineres para que você possa entender o desempenho do seu contêiner e quais problemas relacionados ao desempenho foram identificados.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: 10b80a9749c5698195ac5d3493ac3b07fd6e24e1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073302"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Como carregar o Azure Monitor para contêineres  

Este artigo fornece uma visão geral das opções disponíveis para configurar o Azure Monitor para contêineres para monitorar o desempenho de cargas de trabalho que são implantados em ambientes Kubernetes e hospedado no [serviço Kubernetes do Azure](https://docs.microsoft.com/azure/aks/).

O Azure Monitor para contêineres pode ser habilitado para novas implantações ou para uma ou mais implantações existentes do AKS usando os seguintes métodos compatíveis:

* No portal do Azure, Azure PowerShell, ou com a CLI do Azure
* Usando [Terraform e AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
Antes de começar, verifique se você tem o seguinte:

- **Um espaço de trabalho do Log Analytics.** É possível criá-lo ao habilitar o monitoramento do novo cluster do AKS ou permitir que a experiência de integração crie um workspace padrão no grupo de recursos padrão da assinatura de cluster do AKS. Se optar por criá-lo por conta própria, será possível criá-lo por meio do [Azure Resource Manager](../platform/template-workspace-configuration.md), do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../learn/quick-create-workspace.md).
- Você é membro do **função de Colaborador do Log Analytics** para habilitar o monitoramento de contêiner. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../platform/manage-access.md).
- Você é membro do **[proprietário](../../role-based-access-control/built-in-roles.md#owner)** função no recurso de cluster do AKS. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Componentes 

A capacidade de monitorar o desempenho se baseia em um agente do Log Analytics para Linux em contêineres desenvolvido especificamente para o Azure Monitor para contêineres. Esse agente especializado coleta dados de desempenho e eventos de todos os nós no cluster e é implantado e registrado automaticamente com o espaço de trabalho do Log Analytics especificado durante a implantação. A versão do agente é microsoft/oms:ciprod04202018 ou posterior e é representada por uma data no seguinte formato: *mmddaaaa*. 

>[!NOTE]
>Com a versão prévia de suporte do Windows Server para o AKS, um cluster AKS conosco do Windows Server não tem um agente instalado para coletar dados e encaminhe para o Azure Monitor. Em vez disso, um nó automaticamente implantado no cluster como parte da implantação padrão do Linux coleta e encaminha os dados para o Azure Monitor em nome todos os nós do Windows no cluster.  
>

Quando uma nova versão do agente é lançada, ele é atualizado automaticamente nos clusters gerenciados do Kubernetes hospedados no AKS (Serviço de Kubernetes do Azure). Para seguir as versões lançadas, confira [comunicados sobre lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Se você já implantou um cluster do AKS, habilite o monitoramento usando a CLI do Azure ou um modelo do Azure Resource Manager fornecido, conforme demonstrado posteriormente neste artigo. Não é possível usar `kubectl` para atualizar, excluir, implantar ou reimplantar o agente. O modelo precisa ser implantado no mesmo grupo de recursos que o cluster.

Habilitar o Azure Monitor para contêineres usando um dos métodos a seguir, descritos na tabela a seguir.

| Estado de implantação | Método | DESCRIÇÃO | 
|------------------|--------|-------------| 
| Novo cluster do AKS | [Criar um cluster usando a CLI do Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Você pode habilitar o monitoramento de um novo cluster AKS que você cria com a CLI do Azure. | 
| | [Criar o cluster usando o Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Você pode habilitar o monitoramento de um novo cluster AKS que você cria usando a ferramenta de código-fonte aberto Terraform. | 
| Cluster do AKS existente | [Habilitar o uso da CLI do Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Você pode habilitar o monitoramento de um cluster do AKS já implantado usando a CLI do Azure. | 
| |[Habilitar o uso do Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Você pode habilitar o monitoramento de um cluster do AKS já implantado usando a ferramenta de código-fonte aberto Terraform. | 
| | [Habilitar o Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Você pode habilitar o monitoramento de um ou mais clusters AKS já implantadas na página de vários cluster AKS no Azure Monitor. | 
| | [Habilitar o cluster do AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Você pode habilitar o monitoramento diretamente de um cluster do AKS no portal do Azure. | 
| | [Habilitar o uso de um modelo do Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Você pode habilitar o monitoramento de um cluster AKS com um modelo pré-configurado do Azure Resource Manager. | 

## <a name="next-steps"></a>Próximas etapas

* Com o monitoramento habilitado para capturar métricas de integridade para os nós de cluster do AKS e pods, essas métricas de integridade estão disponíveis no portal do Azure. Para saber como usar o Azure Monitor para contêineres, veja [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md).
