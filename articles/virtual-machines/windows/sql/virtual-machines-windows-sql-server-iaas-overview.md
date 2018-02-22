---
title: "Visão geral do SQL Server nas Máquinas Virtuais do Windows do Azure | Microsoft Docs"
description: "Saiba mais sobre como executar as edições completas do SQL Server nas Máquinas Virtuais do Azure."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/14/2017
ms.author: jroth
ms.openlocfilehash: 268ae1dae1a247ee63adef22403f89680daa4961
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>O que é o SQL Server nas Máquinas Virtuais do Azure? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

O [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) permite que você use versões completas do SQL Server na nuvem sem a necessidade de gerenciar qualquer hardware local. As VMs do SQL Server também simplificam os custos de licenciamento quando são pré-pagas.

Máquinas virtuais do Azure executadas em várias [regiões geográficas](https://azure.microsoft.com/regions/) diferentes no mundo inteiro. Eles também oferecem uma variedade de [tamanhos de máquina](../sizes.md). A galeria de imagens de máquina virtual permite que você crie uma VM do SQL Server com a versão, a edição e o sistema operacional corretos. Isso torna a máquinas virtuais uma boa opção para uma das muitas cargas de trabalho diferentes do SQL Server.

## <a name="automated-updates"></a>Atualizações automáticas

As VMs do Azure do SQL Server podem usar a [Aplicação de Patch Automatizada](virtual-machines-windows-sql-automated-patching.md) para agendar uma janela de manutenção para instalar automaticamente janelas importantes e atualizações do SQL Server.

## <a name="automated-backups"></a>Backups automatizados

As VMs do Azure do SQL Server podem aproveitar o [Backup Automatizado](virtual-machines-windows-sql-automated-backup-v2.md), que cria regularmente backups do banco de dados no armazenamento de blobs. Essa técnica também pode ser usada manualmente. Para obter mais informações, consulte [Usar o Armazenamento do Azure para o Backup e a Restauração do SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Alta disponibilidade

Se você precisar de alta disponibilidade, considere configurar grupos de disponibilidade do SQL Server. Isso envolve várias VMs do Azure do SQL Server em uma rede virtual. Você pode configurar manualmente sua solução de alta disponibilidade ou pode usar modelos no portal do Azure para a configuração automática. Para obter uma visão geral de todas as opções de alta disponibilidade, veja [Alta disponibilidade e recuperação de desastre para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Desempenho

Máquinas virtuais do Azure oferecem tamanhos de máquinas diferentes para atender às demandas de várias cargas de trabalho. VMs do SQL também fornecem a configuração automatizada de armazenamento, que é otimizada para seus requisitos de desempenho. Para saber mais sobre como configurar o armazenamento para VMs do SQL, consulte [Configuração de armazenamento para VMs do SQL Server](virtual-machines-windows-sql-server-storage-configuration.md). Para ajustar o desempenho, confira as [Práticas recomendadas de desempenho para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Introdução a VMs do SQL

Para começar, escolha uma imagem de máquina virtual do SQL Server com a versão, a edição e o sistema operacional necessários. As seções a seguir fornecem links diretos para o Portal do Azure para as imagens da Galeria de máquina virtual do SQL Server.

> [!TIP]
> Para entender os preços da VM e do SQL preços para essas imagens, consulte [Diretrizes para os preços das VMs do SQL Server do Azure](virtual-machines-windows-sql-server-pricing-guidance.md).

> [!TIP]
> Para entender a política de atualização e do ciclo de vida para imagens de galeria de máquinas virtuais do SQL Server, consulte as [Perguntas frequentes sobre VMs do SQL Server](virtual-machines-windows-sql-server-iaas-faq.md#images).

### <a id="payasyougo"></a> Pré-pagas
A tabela a seguir fornece uma matriz de imagens do SQL Server pré-pagas.

| Versão | Sistema operacional | Edição |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Para ver as imagens de máquina virtual do SQL Server do Linux disponíveis, confira [Visão geral do SQL Server em máquinas virtuais do Azure (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

### <a id="BYOL"></a> Traga sua própria licença
Também é possível usar sua própria licença (BYOL). Nesse cenário, você paga apenas pela VM sem encargos adicionais para o licenciamento do SQL Server.  Colocar sua própria licença pode economizar dinheiro ao longo do tempo para cargas de trabalho de produção contínua. Para obter os requisitos para usar essa opção, veja [Diretrizes de preços para VMs do SQL Server do Azure](virtual-machines-windows-sql-server-pricing-guidance.md).

| Versão | Sistema operacional | Edição |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

No portal, os nomes dessas imagens são prefixados com **{BYOL}**.

### <a name="connect-to-the-vm"></a>Conectar-se à VM
Depois de criar sua VM do SQL Server, conecte-se a ela de aplicativos ou ferramentas, como o SQL Server Management Studio (SSMS). Para obter instruções, veja [Conectar-se a uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrar seus dados
Se você tiver um banco de dados existente, deverá movê-lo para a VM do SQL recentemente provisionada. Para obter uma lista das opções de migração e diretrizes, consulte [Migrar um Banco de Dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="customer-experience-improvement-program-ceip"></a>Programa de aperfeiçoamento da experiência do usuário (CEIP)
O CEIP (Programa de Aperfeiçoamento da Experiência do Usuário) está habilitado por padrão. Isso envia relatórios periodicamente à Microsoft a fim de ajudar a aprimorar o SQL Server. Nenhuma tarefa de gerenciamento é necessária com o CEIP, a menos que você queira desabilitá-lo após o provisionamento. Você pode personalizar ou desabilitar o CEIP conectando-se à VM com área de trabalho remota. Em seguida, execute o utilitário **Erro do SQL Server e o Relatório de Uso** . Siga as instruções para desabilitar o relatório. Para saber mais sobre coleta de dados, veja a [Declaração de privacidade do SQL Server](https://www.microsoft.com/EN-US/privacystatement/SQLServer/Default.aspx).

## <a name="related-products-and-services"></a>Produtos e serviços relacionados
### <a name="windows-virtual-machines"></a>Máquinas Virtuais do Windows
* [Visão geral de Máquinas Virtuais](../overview.md)

### <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rede
* [Visão geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Documentação do SQL Server](https://docs.microsoft.com/sql/index)
* [Comparação de Banco de Dados SQL do Azure](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Próximas etapas

Introdução ao SQL Server em Máquinas Virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](quickstart-sql-vm-create-portal.md)

Obtenha respostas para perguntas frequentes sobre máquinas virtuais do SQL:

* [Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md)
