---
title: O SQL Server em máquinas virtuais do Windows na FAQ do Azure | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre a execução do SQL Server em VMs do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: v-shysun
ms.openlocfilehash: 5299437dea18510fa5f85ee27240c8afc434d125
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477256"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Perguntas freqüentes sobre o SQL Server em execução em máquinas virtuais do Windows no Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Este artigo fornece respostas para algumas das perguntas mais comuns sobre como executar [SQL Server no Windows máquinas virtuais no Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Este artigo se concentra em problemas específicos ao SQL Server em VMs Windows. Se você estiver executando o SQL Server em VMs Linux, consulte as [Perguntas frequentes sobre o Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Imagens

1. **Quais imagens da galeria de máquinas virtuais do SQL Server estão disponíveis?**

   O Azure mantém imagens de máquina virtual para todas as versões principais compatíveis do SQL Server em todas as edições para o Windows e para o Linux. Para obter mais informações, consulte a lista completa dos [imagens de VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) e [imagens de VM do Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **As imagens da galeria de máquinas virtuais do SQL Server existentes são atualizadas?**

   A cada dois meses, as imagens do SQL Server na galeria de máquinas virtuais são atualizadas com as atualizações mais recentes do Windows e do Linux. Para imagens do Windows, isso inclui quaisquer atualizações marcadas como importantes no Windows Update, incluindo service packs e atualizações de segurança do SQL Server. Para imagens do Linux, isso inclui as atualizações mais recentes do sistema. As atualizações cumulativas do SQL Server são tratadas de maneira diferente para o Linux e para o Windows. Para o Linux, as atualizações cumulativas do SQL Server também são incluídas na atualização. Mas, neste momento, as VMs Windows não são atualizadas com as atualizações cumulativas do SQL Server ou do Windows Server.

1. **As imagens de máquina virtual do SQL Server podem ser removidas da galeria?**

   Sim. O Azure mantém apenas uma imagem por versão principal e edição. Por exemplo, quando um novo service pack do SQL Server é lançado, o Azure adiciona uma nova imagem à galeria para esse service pack. A imagem do SQL Server para o service pack anterior é removida imediatamente do Portal do Azure. No entanto, ela ainda estará disponível para provisionamento do PowerShell pelos próximos três meses. Depois de três meses, a imagem do service pack anterior não estará mais disponível. Essa política de remoção também se aplica quando uma versão do SQL Server se torna incompatível quando ela atinge o final de seu ciclo de vida.


1. **É possível implantar uma imagem mais antiga do SQL Server que não está visível no portal do Azure?**

   Sim, usando o PowerShell. Para obter mais informações sobre como implantar VMs do SQL Server usando o PowerShell, confira [Como provisionar máquinas virtuais do SQL Server com o Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Posso criar uma imagem de VHD de uma VM do SQL Server?**

   Sim, mas há algumas considerações. Se você implantar esse VHD para uma nova VM no Azure, você não obtém a seção de configuração do SQL Server no portal. Você deve, então, gerenciar as opções de configuração do SQL Server por meio do PowerShell. Além disso, você será cobrado à taxa da VM do SQL em que sua imagem foi originalmente baseada. Isso acontece mesmo se você remover o SQL Server do VHD antes de implantar. 

1. **É possível definir configurações não mostradas na galeria de máquinas virtuais (por exemplo, Windows 2008 R2 + SQL Server 2012)?**

   Não. Para imagens da galeria de máquinas virtuais que incluem o SQL Server, você deve selecionar uma das imagens fornecidas, seja pelo portal do Azure ou via [PowerShell](virtual-machines-windows-ps-sql-create.md). 


## <a name="creation"></a>Criação

1. **Como criar uma máquina virtual do Azure com o SQL Server?**

   A solução mais fácil é criar uma máquina Virtual que inclui o SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM do SQL por meio do portal, confira [Provisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Selecione uma imagem de máquina virtual que usa o licenciamento do SQL Server pago por segundo ou use uma imagem que permite trazer sua própria licença do SQL Server. Você também tem a opção de instalação manual do SQL Server em uma VM com um uma edição licenciada gratuitamente (Desenvolvedor ou Express) ou pela reutilização de uma licença local. Se você trouxer sua própria licença, será necessário ter o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Diretrizes de preço para VMs do Azure do SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Como migrar meu banco de dados do SQL Server local para a nuvem?**

   Primeiro, crie uma máquina virtual do Azure com uma instância do SQL Server. Em seguida, migre os bancos de dados locais para essa instância. Para obter estratégias de migração de dados, confira [Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licenciamento

1. **Como instalar minha cópia licenciada do SQL Server em uma VM do Azure?**

   Há duas maneiras de fazer isso. Você pode provisionar uma das [imagens de máquina virtual que dão suporte a licenças](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), o que também é conhecido como BYOL (traga sua própria licença). Outra opção é copiar a mídia de instalação do SQL Server para uma VM do Windows Server e, em seguida, instalar o SQL Server na VM. No entanto, se você instala manualmente o SQL Server, não há nenhuma integração com o portal e não há suporte para a extensão do SQL Server IaaS Agent, portanto, recursos como o Backup Automatizado e a Aplicação de Patch Automatizada não funcionarão nesse cenário. Por esse motivo, é recomendável usar uma das imagens da Galeria do BYOL. Para usar BYOL ou sua própria mídia do SQL Server em uma VM do Azure, você deve ter [Mobilidade de Licenças via Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Diretrizes de preço para VMs do Azure do SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md).


1. **É necessário pagar para licenciar o SQL Server em uma VM do Azure se ela está sendo usada somente para espera/failover?**

   Se você tiver o Software Assurance e usar o License Mobility, conforme descrito em [perguntas frequentes sobre licenciamento de máquina Virtual](https://azure.microsoft.com/pricing/licensing-faq/), em seguida, você não precisa pagar para licenciar um SQL Server que participa como uma réplica secundária passiva em uma implantação de alta disponibilidade. Caso contrário, você precisará pagar para licenciá-lo.

1. **Posso alterar uma VM para usar minha própria licença do SQL Server se ela foi criada com base em uma das imagens pré-pagas da galeria?**

   Sim. Você pode alterar facilmente entre os dois modelos de licenciamento, se você iniciou originalmente com uma imagem da galeria de pago conforme o uso. No entanto, você não poderá alternar sua licença para PAYG, se você começou originalmente com uma imagem BYOL. Para obter mais informações, confira [Como alterar o modelo de licenciamento para uma VM do SQL Server](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > Atualmente, esse recurso está disponível somente para clientes de nuvem pública.

1. **Deve usar as imagens BYOL ou RP de VM do SQL para criar a nova VM do SQL?**

   As imagens traga sua própria licença (BYOL) só estão disponíveis para os clientes da EA. Outros clientes que possuem o Software Assurance devem usar o provedor de recursos de VM do SQL para criar uma VM do SQL com [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Modelos de licenciamento de comutação exigirá nenhum tempo de inatividade para o SQL Server?**

   Não. [A alteração do modelo de licenciamento](virtual-machines-windows-sql-ahb.md) não requer tempo de inatividade para o SQL Server, pois a alteração entrará em vigor imediatamente e não exige a reinicialização da VM. No entanto, para registrar sua VM do SQL Server com o provedor de recursos da VM do SQL, a [extensão IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md) é um pré-requisito e a instalação da extensão IaaS do SQL reinicia o serviço do SQL Server. Portanto, se a extensão IaaS do SQL precisa ser instalada, isso deve ser feito durante uma janela de manutenção. 

1. **Assinaturas de CSP podem ativar o benefício híbrido do Azure?**

   Sim, o Benefício Híbrido do Azure está disponível para assinaturas de CSP. Os clientes do CSP devem primeiro implantar uma imagem de pago conforme o uso e, em seguida, [alterar o modelo de licenciamento](virtual-machines-windows-sql-ahb.md) para traga sua própria licença (BYOL).  

1. **Registrar minha VM com o novo provedor de recursos de VM do SQL trará custos adicionais?**

   Não. O provedor de recursos de VM de SQL apenas habilita a capacidade de gerenciamento adicional do SQL Server na VM do Azure sem custos adicionais. 

1. **O provedor de recursos da VM do SQL está disponível para todos os clientes?**
 
   Sim. Todos os clientes são capazes de se registrar com o novo provedor de recursos de VM do SQL. No entanto, somente os clientes com Benefício de Software Assurance podem ativar o [Benefício Híbrido do Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (ou BYOL) em uma VM do SQL Server. 

1. **O que acontece com o _Microsoft.SqlVirtualMachine_ recurso se o recurso de VM é movido ou descartado?** 

   Quando o recurso Microsoft.Compute / VirtualMachine é descartado ou movido, o recurso Microsoft.SqlVirtualMachine associado é notificado para replicar de maneira assíncrona a operação.

1. **O que acontece com a VM se o _Microsoft.SqlVirtualMachine_ recursos é descartado?**

    O recurso Microsoft.Compute / Virtual Machine não é afetado quando o recurso Microsoft.Sql Virtual Machine é descartado. No entanto, as alterações de licenciamento serão padronizadas de volta para a origem da imagem original. 

1. **É possível registrar VMs do SQL Server auto-implantadas com o provedor de recursos de VM do SQL?**

    Sim. Se você implantou o SQL Server a partir da sua própria mídia e instalou a extensão do SQL IaaS, é possível registrar sua VM do SQL com o provedor de recursos para obter os benefícios de capacidade de gerenciamento fornecidos pela extensão SQL IaaS. No entanto, você não consegue converter uma VM de SQL automaticamente implantado em pagamento conforme o uso.

## <a name="administration"></a>Administração

1. **Posso instalar uma segunda instância do SQL Server na mesma VM? Posso alterar os recursos instalados da instância padrão?**

   Sim. A mídia de instalação do SQL Server está localizada em uma pasta na unidade **C** . Execute **Setup.exe** nessa localização para adicionar novas instâncias do SQL Server ou para alterar outros recursos instalados do SQL Server no computador. Observe que alguns recursos, como Backup automatizado, aplicação de patch automatizada e integração do Azure Key Vault, funcionam apenas na instância padrão ou um nome de instância que foi configurada corretamente (consulte a pergunta 3). 

1. **Posso desinstalar a instância padrão do SQL Server?**

   Sim, mas há algumas considerações. Conforme mencionado na resposta anterior, há recursos que contam com o [extensão do SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).  Se você desinstalar a instância padrão sem remover a extensão IaaS também, a extensão continua a procurar por ele e pode gerar erros de log de eventos. Esses erros são provenientes das duas seguintes fontes: **Gerenciamento de Credenciais do Microsoft SQL Server** e **Microsoft SQL Server IaaS Agent**. Um dos erros pode ser semelhante ao seguinte:

      Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível.

   Se você optar por desinstalar a instância padrão, desinstale também a [Extensão do SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).

1. **Posso usar uma instância nomeada do SQL Server com a extensão IaaS**?
   
   Sim, se a instância nomeada é a única instância no SQL Server e se a instância padrão original tiver sido [desinstalado corretamente](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation). Se não houver nenhuma instância padrão e há várias instâncias nomeadas em uma única VM do SQL Server, a extensão IaaS conseguirá instalar. 

1. **Posso remover o SQL Server completamente de uma VM de SQL?**

   Sim, mas você continuará a ser cobrado pela VM do SQL conforme descrito em [Diretrizes de preços para VMs do Azure do SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md). Se você não precisar mais do SQL Server, você poderá implantar uma nova máquina virtual e migrar os dados e aplicativos para a nova máquina virtual. Em seguida, você pode remover a máquina virtual do SQL Server.
   
## <a name="updating-and-patching"></a>Atualização e aplicação de patch

1. **Como alterar para uma nova versão/edição do SQL Server em uma VM do Azure?**

   Clientes com Software Assurance são capazes de atualizações in-loco do SQL Server em execução em uma VM do Azure usando a mídia de instalação no Portal do licenciamento por Volume. No entanto, atualmente, não há nenhuma maneira de alterar a edição de uma instância do SQL Server. Criar uma nova máquina virtual do Azure com a edição do SQL Server desejada e, em seguida, migrar seus bancos de dados para o novo servidor usando o padrão [técnicas de migração de dados](virtual-machines-windows-migrate-sql.md).

1. **Como as atualizações e os service packs são aplicados a uma VM do SQL Server?**

   As máquinas virtuais oferecem controle sobre o computador host, inclusive quando e como aplicar atualizações. Para o sistema operacional, você pode aplicar manualmente as atualizações do Windows ou habilitar um serviço de agendamento chamado [Aplicação de Patch Automatizada](virtual-machines-windows-sql-automated-patching.md). A Aplicação de Patch Automatizada instala todas as atualizações marcadas como importantes, inclusive atualizações do SQL Server nessa categoria. Outras atualizações opcionais para o SQL Server devem ser instaladas manualmente.

## <a name="general"></a>Geral

1. **Há suporte para FCIs (Instâncias de Cluster de Failover) do SQL Server nas VMs do Azure?**

   Sim. É possível [criar um Cluster de Failover do Windows no Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) e usar o S2D (Espaços de Armazenamento Diretos) para o armazenamento de cluster. Como alternativa, você pode usar soluções de clustering ou armazenamento de terceiros, conforme descrito em [Alta disponibilidade e recuperação de desastre para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Neste momento, a [extensão do SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md) não é compatível com a FCI do SQL Server no Azure. É recomendável que você desinstale a extensão de VMs que participam da FCI. Essa extensão dá suporte a recursos, como Backup Automatizado e Aplicação de Patch Automatizada, além de alguns recursos do portal para SQL. Esses recursos não funcionarão para VMs do SQL depois que o agente for desinstalado.

1. **Qual é a diferença entre VMs do SQL e o serviço de Banco de Dados SQL?**

   Conceitualmente, a execução do SQL Server em uma máquina virtual do Azure não é diferente da execução do SQL Server em um datacenter remoto. Por outro lado, o [Banco de Dados SQL](../../../sql-database/sql-database-technical-overview.md) oferece o banco de dados como serviço. Com o Banco de Dados SQL, você não tem acesso às máquinas que hospedam os bancos de dados. Para obter uma comparação completa, confira [Escolher uma opção do SQL Server de nuvem: Banco de dados do Azure SQL (PaaS) ou SQL Server em máquinas virtuais do Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Como instalar as ferramentas de Dados do SQL em minha VM do Azure?**

    Baixe e instale as ferramentas de Dados SQL por meio do [Microsoft SQL Server Data Tools – Business Intelligence para Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

1. **São distribuídas com o MSDTC tem suportada em VMs do SQL Server?**
   
    Sim. DTC local tem suporte para SQL Server 2016 SP2 e superior. No entanto, os aplicativos devem ser testados quando utilizando grupos de disponibilidade Always On, como transações em andamento durante um failover falhará e deverá ser repetida. DTC clusterizado está disponível a partir do Windows Server 2019. 

## <a name="resources"></a>Recursos

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Provisionar uma VM do Windows do SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastres para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provisionar uma VM Linux do SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Perguntas Frequentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
