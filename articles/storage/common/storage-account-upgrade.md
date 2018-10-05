---
title: Atualizar para uma conta de armazenamento de uso geral v2 – Armazenamento do Azure | Microsoft Docs
description: Atualizar para contas de armazenamento de uso geral v2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 6e77c4836531a7efd0b52b9a411ac40ff6a613fa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224487"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Atualizar para uma conta de armazenamento de uso geral v2

As contas de armazenamento para uso geral v2 são compatíveis com os recursos mais recentes do Armazenamento do Azure e incorporam todas as funcionalidades das contas de armazenamento de blobs e para uso geral v1. Contas de uso geral v2 são recomendadas para a maioria dos cenários de armazenamento. As contas de armazenamento para uso geral v2 têm os menores preços de capacidade por gigabyte para o Armazenamento do Azure, bem como os preços de transação competitivos no setor.

A atualização para uma conta de armazenamento de uso geral v2 das suas contas de armazenamento de Blobs ou uso geral v1 é simples. Você pode atualizar usando o portal do Azure, o PowerShell ou a CLI do Azure. A ação de atualizar uma conta não pode ser revertida e pode resultar na cobrança de encargos.

## <a name="upgrade-using-the-azure-portal"></a>Atualizar usando o portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Navegue até sua conta de armazenamento.
3. Na seção **Configurações**, clique em **Configuração**.
4. Em **Tipo de conta**, clique em **Upgrade**.
5. Em **Confirmar upgrade**, digite o nome da sua conta. 
6. Clique em **Atualizar** na parte inferior da folha.

## <a name="upgrade-with-powershell"></a>Atualização com o PowerShell

Para atualizar uma conta de uso geral v1 para uma conta de uso geral v2 usando o PowerShell, primeiro atualize o PowerShell para usar a versão mais recente do módulo **AzureRm.Storage**. Acesse [Como instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) para obter informações sobre como instalar o PowerShell. 

Em seguida, chame o comando a seguir para atualizar a conta, substituindo o nome do seu grupo de recursos e a conta de armazenamento:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Atualização com a CLI do Azure

Para atualizar uma conta de uso geral v1 para uma conta de uso geral v2 usando a CLI do Azure, primeiro instale a versão mais recente da CLI do Azure. Para obter informações sobre como instalar a CLI 2.0 do Azure, veja [Como instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

Em seguida, chame o comando a seguir para atualizar a conta, substituindo o nome do seu grupo de recursos e a conta de armazenamento:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Especificar uma camada de acesso para dados de blob

Contas de uso geral v2 dão suporte a todos os objetos de dados e serviços de Armazenamento do Azure, mas as camadas de acesso estão disponíveis apenas para blobs de blocos no armazenamento de blobs. Quando você atualiza para uma conta de armazenamento de uso geral v2, pode especificar uma camada de acesso para seus dados de blob. 

As camadas de acesso permitem que você escolha o armazenamento mais econômico com base nos seus padrões de uso previstos. Blobs de blocos podem ser armazenados em uma camada frequente, esporádica ou de arquivos. Para obter mais informações sobre camadas de acesso, veja [Armazenamento de Blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivos](../blobs/storage-blob-storage-tiers.md).

Por padrão, uma nova conta de armazenamento é criada na camada de acesso frequente e uma conta de armazenamento de uso geral v1 é atualizada para a camada de acesso frequente. Se você está explorando qual camada de acesso usar para seus dados após a atualização, considere seu cenário. Há dois cenários de usuário comuns para a migração para uma conta de uso geral v2:

* Você tem uma conta de armazenamento de uso geral v1 existente e deseja avaliar uma alteração a uma conta de armazenamento de uso geral v2 com a camada de armazenamento certa para dados de blob.
* Você optou por usar uma conta de armazenamento de uso geral v2 ou já tem uma e deseja avaliar se deve usar a camada de armazenamento frequente ou esporádico para dados de blob.

Em ambos os casos, a prioridade é estimar o custo de armazenar, acessar e operar os dados armazenados em uma conta de armazenamento de uso geral v2 e comparar isso com seus custos atuais.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Estimar custos para seus padrões de uso atuais

Para estimar o custo de armazenar e acessar dados de blob em uma conta de armazenamento de uso geral v2 em uma camada específica, avalie seu padrão de uso existente ou estime seu padrão de uso esperado. Em geral, você precisa saber:

* Seu consumo de armazenamento de Blobs, em gigabytes, incluindo:
    - Quantos dados estão sendo armazenados na conta de armazenamento?
    - Como o volume de dados é alterado mensalmente; os dados novos substituem constantemente os dados antigos?
* O padrão de acesso primário para seus dados de armazenamento de Blobs, incluindo:
    - Qual é quantidade de dados que está sendo lida e gravada para a conta de armazenamento? 
    - Quantas operações de leitura versus operações de gravação ocorrem nos dados na conta de armazenamento?

Para decidir a melhor camada de acesso para suas necessidades, pode ser útil determinar quanta capacidade seus dados de blob estão usando no momento e como esses dados estão sendo usados. 

Para coletar dados de uso para sua conta de armazenamento antes da migração, você pode monitorar a conta de armazenamento usando o [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). O Azure Monitor executa logs e fornece dados de métricas para serviços do Azure, incluindo o Armazenamento do Azure. 

Para monitorar dados de consumo para blobs em sua conta de armazenamento, habilite as métricas de capacidade no Azure Monitor. As métricas de capacidade gravam dados sobre quanto armazenamento os blobs na sua conta estão usando em diariamente. As métricas de capacidade podem ser usadas para estimar o custo de armazenar dados na conta de armazenamento. Para saber como é definido o preço da capacidade de armazenamento de Blobs para cada tipo de conta, veja [Preços de blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).

Para monitorar padrões de acesso a dados para armazenamento de Blobs, habilite as métricas no Azure Monitor. Você pode filtrar as diferentes operações de Armazenamento do Azure para estimar a frequência com que cada uma é chamada. Para saber como o preço de diferentes tipos de transações é estabelecido para blocos de blocos e acréscimo para cada tipo de conta, veja [Preço de blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).  

Para obter mais informações sobre a coleta de métricas do Azure Monitor, veja [Métricas de Armazenamento do Azure no Azure Monitor](storage-metrics-in-azure-monitor.md).

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta de armazenamento](storage-quickstart-create-account.md)
- [Gerenciar contas de Armazenamento do Azure](storage-account-manage.md)