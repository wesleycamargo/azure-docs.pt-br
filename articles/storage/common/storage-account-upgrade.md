---
title: Atualizar para uma conta de armazenamento de uso geral v2 – Armazenamento do Azure | Microsoft Docs
description: Atualizar para contas de armazenamento de uso geral v2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/18/2018
ms.author: tamram
ms.openlocfilehash: 10dc25740eca43c7cbd39b8ec783084e048d2af2
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637594"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Atualizar para uma conta de armazenamento de uso geral v2

As contas de armazenamento para uso geral v2 são compatíveis com os recursos mais recentes do Armazenamento do Azure e incorporam todas as funcionalidades das contas de armazenamento de blobs e para uso geral v1. Contas de uso geral v2 são recomendadas para a maioria dos cenários de armazenamento. As contas de armazenamento para uso geral v2 têm os menores preços de capacidade por gigabyte para o Armazenamento do Azure, bem como os preços de transação competitivos no setor.

A atualização para uma conta de armazenamento de uso geral v2 das suas contas de armazenamento de Blobs ou uso geral v1 é simples. Você pode atualizar usando o portal do Azure, o PowerShell ou a CLI do Azure. 

> [!NOTE]
> A alteração da camada de armazenamento pode resultar em cobranças adicionais. Para obter mais informações, confira a seção [Preços e cobrança](#pricing-and-billing).

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
* Você optou por usar uma conta de armazenamento de uso geral v2 ou já tem uma e deseja avaliar se deve usar a camada de armazenamento esporádico frequente ou esporádico para dados de blob.

Em ambos os casos, a prioridade é estimar o custo de armazenar, acessar e operar os dados armazenados em uma conta de armazenamento de uso geral v2 e comparar isso com seus custos atuais.


## <a name="pricing-and-billing"></a>Preços e cobrança
Todas as contas de armazenamento usam um modelo de preços para o armazenamento de blobs com base na camada de cada blob. Ao se usar uma conta de armazenamento, as seguintes considerações de cobranças são aplicáveis:

* **Custos de armazenamento**: além da quantidade de dados armazenados, o custo de armazenamento de dados varia de acordo com a camada de armazenamento. O custo por gigabyte diminui conforme a camada fica mais esporádica.

* **Custos de acesso a dados**: os encargos de acesso a dados aumentam conforme a camada fica mais esporádica. Para dados na camada de armazenamento esporádico e de arquivos, será cobrado um encargo de acesso a dados por gigabyte para leituras.

* **Custos de transações**: há um encargo por transação para todas as camadas que aumenta à medida que a camada fica mais esporádica.

* **Custos de transferência de dados de replicação geográfica**: isso só se aplica a contas com replicação geográfica configurada, incluindo GRS e RA-GRS. A transferência de dados de replicação geográfica acarreta um encargo por gigabyte.

* **Custos de transferência de dados de saída**: transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) acarretam a cobrança por uso de largura de banda por gigabyte, de forma consistente com as contas de armazenamento de finalidade geral.

* **Alteração da camada de armazenamento**: A alteração da camada de armazenamento da conta de esporádico para frequente acarretará um encargo igual à leitura de todos os dados existentes na conta de armazenamento. No entanto, alterar a camada de armazenamento de conta de frequente para esporádico incorre em um custo igual à gravação de todos os dados na camada esporádica (apenas contas de GPv2).

> [!NOTE]
> Para saber mais informações sobre o modelo de preços para contas de armazenamento, confira a página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para saber mais informações sobre os encargos de transferência de dados de saída, confira a página [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Estimar custos para seus padrões de uso atuais

Para estimar o custo de armazenar e acessar dados de blob em uma conta de armazenamento de uso geral v2 em uma camada específica, avalie seu padrão de uso existente ou estime seu padrão de uso esperado. Em geral, você precisa saber:

* Seu consumo de armazenamento de Blobs, em gigabytes, incluindo:
    - Quantos dados estão sendo armazenados na conta de armazenamento?
    - Como o volume de dados é alterado mensalmente; os dados novos substituem constantemente os dados antigos?
* O padrão de acesso primário para seus dados de armazenamento de Blobs, incluindo:
    - Qual é quantidade de dados que está sendo lida e gravada para a conta de armazenamento? 
    - Quantas operações de leitura versus operações de gravação ocorrem nos dados na conta de armazenamento?

Para decidir a melhor camada de acesso para suas necessidades, pode ser útil determinar a capacidade de dados de blob e como esses dados estão sendo usados. Isso pode ser feito melhor examinando as métricas de monitoramento para sua conta.

### <a name="monitoring-existing-storage-accounts"></a>Monitorando contas de armazenamento existentes

Para monitorar suas contas de armazenamento existentes e reunir esses dados, você poderá fazer uso da Análise de Armazenamento do Azure, que executa logs e fornece dados de métrica para uma conta de armazenamento. A Análise de Armazenamento pode armazenar métricas que incluem estatísticas de transação agregadas e dados de capacidade sobre solicitações em um serviço de armazenamento para tipos de conta de Armazenamento de GPv1, GPv2 e armazenamento de Blobs. Esses dados são armazenados em tabelas conhecidas na mesma conta de armazenamento.

Para obter mais detalhes, acesse [Sobre métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Esquema de tabela de métricas da análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> As contas de Armazenamento de Blobs expõem o ponto de extremidade de serviço de tabela apenas para armazenar e acessar dados de métricas dessa conta. 

Para monitorar o consumo do Armazenamento de Blobs, você precisa habilitar as métricas de capacidade.
Com esse recurso habilitado, os dados de capacidade são gravados diariamente para o serviço Blob de uma conta de armazenamento e registrados como uma entrada de tabela gravada na tabela *$MetricsCapacityBlob* dentro da mesma conta de armazenamento.

Para monitorar os padrões de acesso a dados para o armazenamento de Blobs, você precisa habilitar as métricas de transações por hora da API. Com esse recurso habilitado, as transações por API são agregadas a cada hora e registradas como uma entrada de tabela que é gravada na tabela *$MetricsHourPrimaryTransactionsBlob* dentro da mesma conta de armazenamento. A tabela *$MetricsHourSecondaryTransactionsBlob* registra as transações para o ponto de extremidade secundário ao usar contas de armazenamento RA-GRS.

> [!NOTE]
> Caso você tenha uma conta de armazenamento para uso geral na qual armazenou blobs de página, discos de máquina virtual, ou filas, arquivos ou tabelas junto com blob de bloco e de acréscimo, esse processo de previsão não se aplica. Isso ocorre porque os dados de capacidade não diferenciam blobs de bloco de outros tipos e não fornecem dados de capacidade para outros tipos de dados. Caso use esses tipos, uma metodologia alternativa é examinar as quantidades na sua conta mais recente.

Para obter uma boa aproximação do consumo de dados e do padrão de acesso, recomendamos escolher um período de retenção para as métricas que representam seu uso regular e extrapolar. Uma opção é manter os dados de métricas por sete dias e coletar os dados de cada semana, para analisá-las no final do mês. Outra opção é manter os dados de métricas dos últimos 30 dias e coletar e analisar os dados ao fim do período de 30 dias.

Para obter detalhes sobre como habilitar, coletar e exibir dados de métricas, consulte [Habilitando métricas do Armazenamento do Azure e exibindo dados de métricas](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> O armazenamento, acesso e download de dados de análise também serão cobrados, assim como os dados de usuário comuns.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilizando métricas de uso para estimar custos

#### <a name="capacity-costs"></a>Custos de capacidade

A última entrada na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'data'* mostra a capacidade de armazenamento consumida por dados do usuário. A última entrada na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'analytics'* mostra a capacidade de armazenamento consumida por logs de análise.

Essa capacidade total consumida tanto por dados de usuário quanto logs de análise (se habilitado) pode ser usada para estimar o custo de armazenar dados na conta de armazenamento. O mesmo método também pode ser usado para estimar os custos de armazenamento nas contas de armazenamento de GPv1.

#### <a name="transaction-costs"></a>Custos de transação

A soma de *'TotalBillableRequests'* de todas as entradas para uma API na tabela de métricas de transação indica a quantidade total de transações para essa API em particular. *Por exemplo*, o número total de transações *'GetBlob'* em um determinado período pode ser calculado pela soma do total de solicitações faturáveis para todas as entradas com a chave de linha *'user;GetBlob'*.

Para estimar os custos de transação para contas de armazenamento de Blobs, você precisa dividir as transações em três grupos, já que elas possuem preços diferentes.

* Transações de gravação, como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* e *'CopyBlob'*.
* Transações de exclusão, como *'DeleteBlob'* e *'DeleteContainer'*.
* Todas as outras transações.

Para estimar os custos de transação para contas de armazenamento de GPv1, você precisa agregar todas as transações, independentemente da operação/API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Custos de transferência de dados de acesso aos dados e de replicação geográfica

Embora a análise de armazenamento não forneça a quantidade de dados lida e gravada em uma conta de armazenamento, ela pode ser estimada aproximadamente examinando a tabela de métricas de transação. A soma de *'TotalIngress'* de todas as entradas para uma API na tabela de métricas de transação indica a quantidade total de dados de entrada em bytes para essa API em particular. Da mesma forma, a soma de *'TotalEgress'* indica a quantidade total de dados de saída, em bytes.

Para estimar os custos de acesso de dados para as contas de armazenamento de Blobs, você precisa dividir as transações em dois grupos.

* A quantidade de dados recuperados da conta de armazenamento pode ser estimada observando a soma de *'TotalEgress'* basicamente para as operações *'GetBlob'* e *'CopyBlob'*.

* A quantidade de dados gravados na conta de armazenamento pode ser estimada observando a soma de *'TotalIngress'* basicamente para as operações *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'*.

O custo da transferência de dados de replicação geográfica para contas de armazenamento de Blobs também pode ser calculado usando a estimativa de quantidade de dados gravados ao usar uma conta de armazenamento GRS ou RA-GRS.

> [!NOTE]
> Para obter um exemplo mais detalhado de como calcular os custos para usar a camada de armazenamento quente ou fria, dê uma olhada nas Perguntas frequentes intituladas *“O que são as camadas de acesso quente e fria e como devo determinar qual delas usar?”* na [Página de preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta de armazenamento](storage-quickstart-create-account.md)
- [Gerenciar contas de Armazenamento do Azure](storage-account-manage.md)
