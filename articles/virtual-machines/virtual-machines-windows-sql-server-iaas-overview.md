---
title: "Visão geral do SQL Server nas Máquinas Virtuais do Azure | Microsoft Docs"
description: "Saiba mais sobre como executar as edições completas do SQL Server nas máquinas virtuais do Azure. Obtenha links diretos para todas as imagens de VM do SQL Server e conteúdo relacionado."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fcc2883ae52bb3d4be098ce741bbf5f5047777d2


---
# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Visão geral do SQL Server nas Máquinas Virtuais do Azure
Este tópico descreve as opções para executar o SQL Server nas VMs (máquinas virtuais) do Azure, juntamente com [links para as imagens do portal](#option-1-create-a-sql-vm-with-per-minute-licensing) e uma visão geral das [tarefas comuns](#manage-your-sql-vm).

> [!NOTE]
> Se você já estiver familiarizado com o SQL Server e quiser apenas ver como implantar uma VM do SQL Server, consulte [Provisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).
> 
> 

## <a name="overview"></a>Visão geral
Se você for um administrador de banco de dados ou um desenvolvedor, as VMs do Azure fornecem uma maneira de mover seus aplicativos e cargas de trabalho locais do SQL Server para a nuvem. O vídeo a seguir fornece uma visão geral técnica de VMs do SQL Server do Azure.

> [!VIDEO https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016/player]
> 
> 

O vídeo abrange as seguintes áreas:

| Hora | Área |
| --- | --- |
| 00:21 |O que são VMs do Azure? |
| 01:45 |Segurança |
| 02:50 |Conectividade |
| 03:30 |Desempenho e confiabilidade do armazenamento |
| 05:20 |Tamanhos de VM |
| 05:54 |Alta disponibilidade e SLA |
| 07:30 |Suporte de configuração |
| 08:00 |Monitoramento |
| 08:32 |Demonstração: Criar uma VM do SQL Server 2016 |

> [!NOTE]
> O vídeo se concentra no SQL Server 2016, mas o Azure fornece imagens da VM para muitas versões do SQL Server, incluindo 2008, 2012, 2014 e 2016. 
> 
> 

## <a name="scenarios"></a>Cenários
Há muitos motivos para você optar por hospedar seus dados no Azure. Se o seu aplicativo estiver mudando para o Azure, isso também melhora o desempenho de movimentação dos dados. Mas há outros benefícios. Você tem acesso automaticamente a vários data centers, visando a recuperação de desastres e uma presença global. Os dados também são altamente seguros e duradouros.

A execução do SQL Server em VMs do Azure é uma opção para armazenar dados relacionais no Azure. É uma boa escolha para vários cenários. Por exemplo, talvez você queira configurar a VM do Azure o mais próximo possível de uma máquina local do SQL Server. Ou talvez você queira executar outros aplicativos e serviços no mesmo servidor de banco de dados. Há dois recursos principais que podem ajudar você a pensar em mais cenários e considerações:

* [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) fornece uma visão geral dos melhores cenários para uso do SQL Server em VMs do Azure. 
* [Escolher uma opção do SQL Server de nuvem: Banco de Dados do SQL Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md)fornece uma comparação detalhada entre o Banco de Dados SQL e o SQL Server em execução em uma VM.

## <a name="create-a-new-sql-vm"></a>Criar uma nova VM de SQL
As seções a seguir fornecem links diretos para o Portal do Azure para as imagens da Galeria de máquina virtual do SQL Server. Dependendo da imagem selecionada, você poderá pagar pelos custos de licenciamento do SQL Server por minuto, ou poderá usar sua própria licença (BYOL).

Encontre diretrizes passo a passo para este processo no tutorial [Provisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Além disso, revise as [Práticas recomendadas para as VMs do SQL Server](virtual-machines-windows-sql-performance.md), que explicam como selecionar o tamanho da máquina apropriado e outros recursos disponíveis durante o provisionamento.

## <a name="option-1-create-a-sql-vm-with-perminute-licensing"></a>Opção 1: Criar uma VM do SQL com licenciamento por minuto
A tabela a seguir fornece uma matriz de imagens do SQL Server disponíveis na galeria de máquinas virtuais. Clique em qualquer link para começar a criação de uma nova VM do SQL com a versão, a edição e o sistema operacional especificados.

| Versão | Sistema operacional | Edição |
| --- | --- | --- |
| **SQL 2016** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2) |
| **SQL 2014 SP1** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2) |
| **SQL 2014** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2) |
| **SQL 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL 2012 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2) |
| **SQL 2012 SP2** |Windows Server 2012 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012) |
| **SQL 2008 R2 SP3** |Windows Server 2008 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2) |
| **SQL 2008 R2 SP3** |Windows Server 2012 |[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012) |

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Opção 2: Criar uma VM do SQL com uma licença existente
Também é possível usar sua própria licença (BYOL). Nesse cenário, você paga apenas pela VM sem encargos adicionais para o licenciamento do SQL Server. Para usar sua própria licença, use a tabela abaixo de versões, edições e sistemas operacionais do SQL Server. No portal, os nomes dessas imagens são prefixados com **{BYOL}**.

| Versão | Sistema operacional | Edição |
| --- | --- | --- |
| **SQL Server 2016** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2) |
| **SQL Server 2014 SP1** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

> [!IMPORTANT]
> Para usar as imagens da VM BYOL, você deve ter o Enterprise Agreement com [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Também é necessário uma licença válida para a versão/edição do SQL Server que você deseja usar. Você deve [fornecer as informações necessárias do BYOL à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) em **10** dias de provisionamento da VM.
> 
> 

## <a name="manage-your-sql-vm"></a>Gerenciar sua VM do SQL
Após o provisionamento da VM do SQL Server, várias tarefas de gerenciamento opcionais estarão disponíveis. Em muitos aspectos, você configura e gerencia o SQL Server exatamente como configuraria uma instância local do SQL Server. No entanto, algumas tarefas são específicas do Azure. As seções a seguir destacam algumas dessas áreas com links para obter mais informações.

### <a name="connect-to-the-vm"></a>Conectar-se à VM
Uma das etapas mais básicas do gerenciamento é conectar-se à sua VM do SQL Server por meio de ferramentas, como o SSMS (SQL Server Management Studio). Para obter instruções sobre como se conectar à sua nova VM do SQL Server, confira [Conectar-se a uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrar seus dados
Se você tiver um banco de dados existente, deverá movê-lo para a VM do SQL recentemente provisionada. Para obter uma lista das opções de migração e diretrizes, consulte [Migrar um Banco de Dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configurar alta disponibilidade
Se você precisar de alta disponibilidade, considere configurar grupos de disponibilidade do SQL Server. Isso envolve várias VMs do Azure em uma rede virtual. O portal do Azure tem um modelo que define essa configuração para você. Para obter mais informações, consulte [Configurar um grupo de disponibilidade AlwaysOn nas máquinas virtuais do Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Se você quiser configurar manualmente o Grupo de Disponibilidade e o ouvinte associado, consulte [Configurar os Grupos de Disponibilidade AlwaysOn na VM do Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para outras considerações sobre alta disponibilidade, consulte [alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Fazer backup dos dados
As VMs do Azure podem aproveitar o [Backup Automatizado](virtual-machines-windows-sql-automated-backup.md), que cria regularmente backups do banco de dados no armazenamento de blobs. Essa técnica também pode ser usada manualmente. Para obter mais informações, consulte [Usar o Armazenamento do Azure para o Backup e a Restauração do SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Para obter uma visão geral das opções de backup e restauração, consulte [Backup e Restauração do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatizar as atualizações
As VMs do Azure podem usar a [Aplicação de Patch Automatizada](virtual-machines-windows-sql-automated-patching.md) para agendar uma janela de manutenção para instalar automaticamente janelas importantes e atualizações do SQL Server.

### <a name="customer-experience-improvement-program-ceip"></a>Programa de aperfeiçoamento da experiência do usuário (CEIP)
O CEIP (Programa de Aperfeiçoamento da Experiência do Usuário) está habilitado por padrão. Isso envia relatórios periodicamente à Microsoft a fim de ajudar a aprimorar o SQL Server. Nenhuma tarefa de gerenciamento é necessária com o CEIP, a menos que você queira desabilitá-lo após o provisionamento. Você pode personalizar ou desabilitar o CEIP conectando-se à VM com área de trabalho remota. Em seguida, execute o utilitário **Erro do SQL Server e o Relatório de Uso** . Siga as instruções para desabilitar o relatório. 

Para saber mais, consulte a seção CEIP do tópico [Aceitar os Termos de Licença](https://msdn.microsoft.com/library/ms143343.aspx). 

## <a name="next-steps"></a>Próximas etapas
[Explorar o Roteiro de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) do SQL Server nas máquinas virtuais do Azure.

Mais perguntas? Primeiro, consulte as [Perguntas Frequentes sobre o SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md). Mas, adicione também suas perguntas ou comentários à parte inferior de qualquer um dos tópicos da VM do SQL para interagir com a Microsoft e a comunidade.




<!--HONumber=Nov16_HO2-->


