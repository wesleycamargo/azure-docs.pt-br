---
title: "Alterar a camada de serviço e o nível de desempenho de um Banco de Dados SQL do Azure usando PowerShell | Microsoft Docs"
description: "Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure mostra como escalar verticalmente seu banco de dados SQL com o PowerShell. Alterando o tipo de preço de um banco de dados SQL do Azure com o PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6f016c17-b048-4968-b82b-d2dcec954e54
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aea3bcbd6ac73a05b00b7b79b2dc47bf06d67f6f


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um banco de dados SQL com o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-scale-up.md)
> * [**PowerShell**](sql-database-scale-up-powershell.md)
> 
> 

As camadas de serviço e níveis de desempenho descrevem as características e recursos disponíveis para o banco de dados SQL e podem ser atualizados conforme as necessidades do seu aplicativo mudam. Para obter detalhes, consulte [Camadas de serviço](sql-database-service-tiers.md).

Observe que a alteração da camada de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e então faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. Essa janela varia, mas é em média inferior a quatro segundos e, em mais de 99% dos casos, de menos de 30 segundos. Muito raramente, especialmente se houver grandes números de transações em andamento no momento em que as conexões estiverem desabilitadas, esta janela poderá ser maior.  

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Standard deverá ser concluída dentro de 6 horas. Um banco de dados do mesmo tamanho cujos níveis de desempenho estão mudando dentro da camada de serviço Premium deverá ser concluído dentro de 3 horas.

* Para fazer downgrade de um banco de dados, este deve ter um tamanho menor do que o máximo permitido para a camada de serviço de destino. 
* Ao atualizar um banco de dados com [Replicação Geográfica](sql-database-geo-replication-portal.md) habilitada, é necessário primeiro atualizar seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário.
* Ao fazer downgrade de uma camada de serviço Premium, primeiro, você deve encerrar todos os relacionamentos de Replicação Geográfica. Você pode seguir as etapas descritas no tópico [Recuperação de uma interrupção](sql-database-disaster-recovery.md) para interromper o processo de replicação entre os bancos de dados primários e secundários ativos.
* As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se estiver fazendo downgrade, talvez você perca a capacidade de fazer uma restauração pontual ou tenha um período menor de retenção do backup. Para saber mais, confira [Backup e restauração do Banco de dados SQL do Azure](sql-database-business-continuity.md).
* As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

**Para concluir este artigo, você precisa do seguinte:**

* Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **CONTA GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
* Um banco de dados SQL do Azure. Se você não tiver um banco de dados SQL, crie um executando as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
* PowerShell do Azure.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>Alterar a camada de serviço e o nível de desempenho do banco de dados SQL
Execute o cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) e defina o **-RequestedServiceObjectiveName** com o nível de desempenho do tipo de preço desejado, por exemplo, *S0*, *S1*, *S2*, *S3*, *P1*, *P2*, etc.

```
$ResourceGroupName = "resourceGroupName"

$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```






## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>Amostra de script do PowerShell para alterar a camada de serviços e o nível de desempenho do banco de dados SQL
Substitua ```{variables}``` por seus valores (não inclua chaves).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```



## <a name="next-steps"></a>Próximas etapas
* [Escalar horizontalmente](sql-database-elastic-scale-get-started.md)
* [Conectar-se a e consultar um banco de dados SQL com SSMS](sql-database-connect-query-ssms.md)
* [Exportar um banco de dados SQL do Azure](sql-database-export-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
* [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* [Documentação do Banco de Dados SQL](http://azure.microsoft.com/documentation/services/sql-database/)
* [Cmdlets do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/mt574084https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx.aspx)




<!--HONumber=Nov16_HO3-->


