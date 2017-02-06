---
title: "Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure | Microsoft Docs"
description: "Este artigo fornece respostas a perguntas frequentes sobre a execução do SQL Server em VMs do Azure."
services: virtual-machines-windows
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/30/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: a7d1878931ccf45e8e3192d5d03a4292c5291366


---
# <a name="sql-server-on-azure-virtual-machines-faq"></a>Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure
Este tópico fornece respostas a algumas das perguntas mais comuns sobre a execução do [SQL Server em Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Perguntas frequentes
1. **Como criar uma máquina virtual do Azure com o SQL Server?**
   
    A solução mais fácil é criar uma máquina Virtual que inclui o SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM do SQL por meio do portal, confira [Provisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Selecione uma imagem de máquina virtual que usa o licenciamento do SQL Server pago por minuto ou use uma imagem que permite trazer sua própria licença do SQL Server. Você também tem a opção de instalar o SQL Server manualmente em uma VM e reutilizar uma licença local. Se você trouxer sua própria licença, será necessário ter o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. **Qual é a diferença entre VMs do SQL e o serviço de Banco de Dados SQL?**
   
    Conceitualmente, a execução do SQL Server em uma máquina virtual do Azure não é diferente da execução do SQL Server em um datacenter remoto. Por outro lado, o [Banco de Dados SQL](../../../sql-database/sql-database-technical-overview.md) oferece o banco de dados como serviço. Com o Banco de Dados SQL, você não tem acesso às máquinas que hospedam os bancos de dados. Para obter uma comparação completa, confira [Escolher uma opção do SQL Server de nuvem: Banco de Dados SQL do Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).
3. **Como migrar meu banco de dados do SQL Server local para a nuvem?**
   
    Primeiro, crie uma máquina virtual do Azure com uma instância do SQL Server. Em seguida, migre os bancos de dados locais para essa instância. Para obter estratégias de migração de dados, confira [Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).
4. **Posso alterar os recursos instalados ou instalar uma segunda instância do SQL Server na mesma VM?**
   
    Sim. A mídia de instalação do SQL Server está localizada em uma pasta na unidade **C** . Execute **Setup.exe** nessa localização para adicionar novas instâncias do SQL Server ou para alterar outros recursos instalados do SQL Server no computador.
5. **Como atualizar para uma nova versão/edição do SQL Server em uma VM do Azure?**
   
    Atualmente, não existe uma atualização in-loco para o SQL Server em execução em uma VM do Azure. Crie uma nova máquina virtual do Azure com a versão/edição desejada do SQL Server e migre os bancos de dados para o novo servidor usando [técnicas de migração de dados](virtual-machines-windows-migrate-sql.md) padrão.
6. **Como instalar minha cópia licenciada do SQL Server em uma VM do Azure?**
   
    Há duas maneiras de fazer isso. É possível provisionar uma das [imagens de máquina virtual que dão suporte a licenças](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Outra opção é copiar a mídia de instalação do SQL Server para uma VM do Windows Server e, em seguida, instalar o SQL Server na VM. Por motivos de licenciamento, você deve ter o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).
7. **Posso alterar uma VM para usar minha própria licença do SQL Server se ela foi criada com base em uma das imagens pré-pagas da galeria?**

    Não. Não é possível mudar do licenciamento pago por minuto para o uso de sua própria licença. Crie uma nova máquina virtual do Azure usando uma das [imagens BYOL](virtual-machines-windows-sql-server-iaas-overview.md#BYOL) e, em seguida, migre os bancos de dados para o novo servidor usando [técnicas de migração de dados](virtual-machines-windows-migrate-sql.md) padrão.

7. **É necessário pagar para licenciar o SQL Server em uma VM do Azure se ela está sendo usada somente para espera/failover?**
   
    Não será necessário pagar para licenciar um SQL Server que participa como uma réplica secundária passiva em uma implantação de HA se você tiver o Software Assurance e usar o License Mobility, conforme descrito em [Perguntas frequentes sobre licenciamento de máquinas virtuais](http://azure.microsoft.com/pricing/licensing-faq/).
    
8. **Como as atualizações e os service packs são aplicados a uma VM do SQL Server?**
   
    As máquinas virtuais oferecem controle sobre o computador host, inclusive quando e como aplicar atualizações. Para o sistema operacional, você pode aplicar manualmente as atualizações do Windows ou habilitar um serviço de agendamento chamado [Aplicação de Patch Automatizada](virtual-machines-windows-sql-automated-patching.md). A Aplicação de Patch Automatizada instala todas as atualizações marcadas como importantes, inclusive atualizações do SQL Server nessa categoria. Outras atualizações opcionais para o SQL Server devem ser instaladas manualmente.
9. **É possível definir configurações não mostradas na galeria de máquinas virtuais (por exemplo, Windows 2008 R2 + SQL Server 2012)?**
   
    Não. Para imagens da galeria de máquinas virtuais que incluem o SQL Server, você deve selecionar uma das imagens fornecidas.
10. **Como instalar as ferramentas de Dados do SQL em minha VM do Azure?**
    
     Baixe e instale as ferramentas de Dados do SQL por meio do [Microsoft SQL Server Data Tools – Business Intelligence para Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Recursos
Para obter uma visão geral do SQL Server em Máquinas Virtuais do Azure, assista ao vídeo [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)(A VM do Azure é a melhor plataforma para o SQL Server 2016). Você também pode obter uma boa introdução no tópico [Visão geral do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

Outros recursos incluem:

* [Provisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md)
* [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastres para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)




<!--HONumber=Jan17_HO2-->


