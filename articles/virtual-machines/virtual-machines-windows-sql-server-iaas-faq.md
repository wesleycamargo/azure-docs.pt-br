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
ms.date: 11/15/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 8a506a4c9a304ebbb25d0e522f48f0eae9ca706c
ms.openlocfilehash: 1e1123f78ee61fa392dcd7eb1a649331a4fe1c53


---
# <a name="sql-server-on-azure-virtual-machines-faq"></a>Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure
Este tópico fornece respostas a algumas das perguntas mais comuns sobre a execução do [SQL Server em Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Perguntas frequentes
1. **Como criar uma máquina virtual do Azure com o SQL Server?**
   
    Há duas maneiras de fazer isso. A solução mais fácil é criar uma máquina Virtual que inclui o SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM do SQL por meio do portal, confira [Provisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Você também tem a opção de instalar manualmente o SQL Server em uma VM e reutilizar uma licença local com o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. **Qual é a diferença entre VMs do SQL e o serviço de Banco de Dados SQL?**
   
    Conceitualmente, a execução do SQL Server em uma máquina virtual do Azure não é diferente da execução do SQL Server em um datacenter remoto. Por outro lado, o [Banco de Dados SQL](../sql-database/sql-database-technical-overview.md) oferece o banco de dados como serviço. Com o Banco de Dados SQL, você não tem acesso às máquinas que hospedam os bancos de dados. Para obter uma comparação completa, confira [Escolher uma opção do SQL Server de nuvem: Banco de Dados SQL do Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).
3. **Como migrar meu banco de dados do SQL Server local para a nuvem?**
   
    Primeiro, crie uma máquina virtual do Azure com uma instância do SQL Server. Em seguida, migre os bancos de dados locais para essa instância. Para obter estratégias de migração de dados, confira [Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Posso alterar os recursos instalados ou instalar uma segunda instância do SQL Server na mesma VM?**
   
    Sim. A mídia de instalação do SQL Server está localizada em uma pasta na unidade **C** . Execute **Setup.exe** nessa localização para adicionar novas instâncias do SQL Server ou para alterar outros recursos instalados do SQL Server no computador.
5. **Como atualizar para uma nova versão/edição do SQL Server em uma VM do Azure?**
   
    Atualmente, não existe uma atualização in-loco para o SQL Server em execução em uma VM do Azure. Crie uma nova máquina virtual do Azure com a versão/edição desejada do SQL Server e migre os bancos de dados para o novo servidor usando [técnicas de migração de dados](virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) padrão.
6. **Como instalar minha cópia licenciada do SQL Server em uma VM do Azure?**
   
    Copie a mídia de instalação do SQL Server para a VM do Windows Server e instale o SQL Server na VM. Por motivos de licenciamento, você deve ter o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).
7. **É necessário pagar os custos do SQL de uma VM se ela é usada somente para espera/failover?**
   
    Se estiver criando a VM do SQL por meio da galeria, você deverá ter uma licença separada para a VM do SQL de espera, e o preço será o mesmo. Se instalar o SQL Server manualmente em uma máquina virtual com o [License Mobility](https://azure.microsoft.com/pricing/license-mobility/), você terá a opção de ter uma instância do SQL passiva gratuita para failover. Examine as restrições e os requisitos.
8. **Como as atualizações e os service packs são aplicados a uma VM do SQL Server?**
   
    As máquinas virtuais oferecem controle sobre o computador host, inclusive quando e como aplicar atualizações. Para o sistema operacional, você pode aplicar manualmente as atualizações do Windows ou habilitar um serviço de agendamento chamado [Aplicação de Patch Automatizada](virtual-machines-windows-classic-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). A Aplicação de Patch Automatizada instala todas as atualizações marcadas como importantes, inclusive atualizações do SQL Server nessa categoria. Outras atualizações opcionais para o SQL Server devem ser instaladas manualmente.
9. **É possível definir configurações não mostradas na galeria de máquinas virtuais (por exemplo, Windows 2008 R2 + SQL Server 2012)?**
   
    Não. Para imagens da galeria de máquinas virtuais que incluem o SQL Server, você deve selecionar uma das imagens fornecidas.
10. **Como instalar as ferramentas de Dados do SQL em minha VM do Azure?**
    
     Baixe e instale as ferramentas de Dados do SQL por meio do [Microsoft SQL Server Data Tools – Business Intelligence para Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Recursos
Para obter uma visão geral do SQL Server em Máquinas Virtuais do Azure, assista ao vídeo [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)(A VM do Azure é a melhor plataforma para o SQL Server 2016). Você também pode obter uma boa introdução no tópico [Visão geral do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Outros recursos incluem:

* [Provisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Alta disponibilidade e recuperação de desastres para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


