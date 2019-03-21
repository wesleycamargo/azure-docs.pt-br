---
title: Mainframe rehosting em máquinas virtuais do Azure | Microsoft Docs
description: Hospede novamente suas cargas de trabalho de mainframe como sistemas com base em IBM Z usando máquinas virtuais (VMs) no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 8be0ebc486739f8826e8a1d5a5307a219ba71b6f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192710"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe rehosting em máquinas virtuais do Azure

Migrar cargas de trabalho de mainframe ambientes na nuvem permite que você modernize sua infraestrutura e muitas vezes economizar nos custos. Muitas cargas de trabalho podem ser transferidas para o Azure com apenas pequenas alterações de código, como atualizar os nomes dos bancos de dados.

O termo *mainframe* geralmente se refere a um sistema de computador grande, mas a grande maioria atualmente implantada são servidores de sistema do IBM Z ou IBM plug-compatible sistemas que executam MVS, DOS, VSE, OS/390 ou z/OS.

Uma máquina virtual do Azure (VM) é usada para isolar e gerenciar os recursos para um aplicativo específico em uma única instância. Mainframes, como IBM z/OS usam partições lógicas (LPARS) para essa finalidade. Um mainframe poderia usar um LPAR para uma região do CICS com programas associados do COBOL e um LPAR separado para o banco de dados IBM Db2. Um típico [aplicativo de n camadas no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) implanta as VMs do Azure em uma rede virtual que pode ser segmentada em sub-redes de cada camada.

As VMs do Azure podem executar ambientes de emulação de mainframe e compiladores que dão suporte a cenários de lift-and-shift. Desenvolvimento e teste estão geralmente entre as cargas de trabalho primeiro migrar de um mainframe para um ambiente de desenvolvimento/teste do Azure. Componentes de servidor comuns que você pode emular incluem o processo de transações online (OLTP), lotes e sistemas de ingestão de dados, como mostra a figura a seguir.

![Ambientes de emulação no Azure que você possa executar sistemas baseados em z/OS.](media/01-overview.png)

Algumas cargas de trabalho de mainframe podem ser migradas para o Azure com relativa facilidade, enquanto outros podem ser hospedado novamente no Azure usando uma solução de parceiro. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, o [Centro de migração de Mainframe Azure](https://azure.microsoft.com/migration/mainframe/) pode ajudar.

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Configurar o ambiente de desenvolvimento/teste usando uma plataforma de rehosting Micro Focus

Micro Focus Enterprise Server é um dos maior mainframe rehosting plataformas disponíveis. Você pode usá-lo executar suas cargas de trabalho do z/OS em um x86 mais barato plataforma no Azure.

Para começar, consulte os seguintes artigos:

- [Instalar o Micro Focus Enterprise Server 4.0 e do Enterprise Developer 4.0 no Azure](./microfocus/set-up-micro-focus-on-azure.md)
- [Configurar o Micro foco CICS BankDemo para Micro foco Enterprise Developer 4.0 no Azure](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame no Azure

TmaxSoft OpenFrame é um popular mainframe rehosting solução que torna mais fácil para seus ativos de mainframe existentes de lift- and -shift-los para o Azure. Um ambiente OpenFrame no Azure é adequado para desenvolvimento, demonstrações, teste ou cargas de trabalho de produção.

Para começar, baixe o [instalar o TmaxSoft OpenFrame no Azure](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/) livro eletrônico.

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>Configurar um ambiente de desenvolvimento/teste usando 12.0 de desenvolvimento/teste do IBM Z

IBM Z Development e ambiente de teste (IBM zD & T) define um ambiente de não produção no Azure que você pode usar para desenvolvimento, testes e demonstrações de aplicativos baseados em z/OS.

O ambiente de emulação do Azure pode hospedar uma variedade de instâncias de Z por meio de distribuições de desenvolvedores de aplicativo controlado (ADCDs). Você pode executar zD T Personal Edition, zD Sysplex paralelo de T e zD & T Enterprise Edition no Azure e o Azure Stack.

Para começar, consulte os seguintes artigos:

-   [Configurar o IBM zD & 12.0 T no Azure](./ibm/install-ibm-z-environment.md)
-   [Configurar ADCD em zD & T](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>Migrar pureScale IBM DB2 para o Azure

O ambiente IBM DB2 pureScale fornece um cluster de banco de dados para o Azure com alta disponibilidade e escalabilidade nos sistemas operacionais Linux. Apesar de não ser idêntico ao ambiente original, o IBM DB2 pureScale no Linux oferece recursos semelhantes de alta disponibilidade e escalabilidade como o IBM DB2 para z/OS em execução em uma configuração de Parallel Sysplex no mainframe.

Para começar, consulte [pureScale IBM DB2 no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considerações

Quando você migra cargas de trabalho de mainframe para a infraestrutura do Azure como um serviço (IaaS), você pode escolher entre vários tipos e sob demanda escalonáveis recursos de computação, incluindo as VMs do Azure. O Azure oferece uma variedade de [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) VMs.

### <a name="high-availability-and-failover"></a>Failover e alta disponibilidade

O Azure oferece o compromisso com base em contratos de nível de serviço (SLAs), onde várias 9s disponibilidade é o padrão, otimizado com replicação local ou com base geográfica dos serviços. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

Por IaaS do Azure, como VMs, o failover se baseia na funcionalidade específica do sistema, como instâncias de cluster de failover e [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets). Quando você usa a plataforma do Azure como um recurso de serviço (PaaS), como [banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) e [do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), a plataforma lida com failovers automaticamente.

### <a name="scalability"></a>Escalabilidade

Mainframes normalmente se expandir, durante a escala do ambiente de nuvem-out. O Azure oferece uma variedade de [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) tamanhos para atender às suas necessidades. A nuvem também pode ser dimensionado para cima ou para baixo até as especificações exatas do usuário de correspondência. Potência de computação, armazenamento e serviços [escala](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) sob demanda em um modelo de cobrança baseada em uso.

### <a name="storage"></a>Armazenamento

Na nuvem, você tem uma variedade de opções de armazenamento flexível e escalonável, e você paga apenas pelo que precisa. O [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) oferece um armazenamento de objetos altamente escalonável para objetos de dados, um serviço de sistema de arquivos para a nuvem, armazenamento de mensagens confiáveis e armazenamento de NoSQL. Para VMs, discos gerenciados e discos não gerenciados fornecem armazenamento em disco persistente e seguro.

### <a name="backup-and-recovery"></a>Backup e recuperação

Manter seu próprio site de recuperação de desastre pode ser uma proposição cara. Opções de fácil de implementar e econômicas para o Azure tem [backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup), [recuperação](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), e [redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) nos níveis locais ou regionais ou por meio de redundância geográfica.

## <a name="azure-government-for-mainframe-migrations"></a>O Azure governamental para migrações de mainframe

Muitas entidades do setor público adoraria mover seus aplicativos de mainframe para uma plataforma mais moderna e flexível. O Microsoft Azure governamental é uma instância fisicamente separada de serviços de nuvem com base na plataforma Microsoft Azure global, mas empacotados para sistemas do governo federal, estadual e local. Ele fornece segurança de classe mundial, proteção e serviços de conformidade especificamente para agências governamentais dos Estados Unidos e seus parceiros.

O Azure governamental ganhos uma autoridade provisória de funcionamento (P-ATO) para FedRAMP alto impacto para os sistemas que precisam desse tipo de ambiente. 

Para começar, baixe [nuvem do Microsoft Azure governamental para aplicativos de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="learn-more"></a>Saiba mais

Se você estiver considerando uma migração de mainframe, nossos abrangente [parceiro](partner-workloads.md) ecossistema está disponível para ajudá-lo. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, consulte a [Aliança de Modernização de Plataforma](https://www.platformmodernization.org/pages/mainframe.aspx).

Consulte também:

-   [Migração de mainframe](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [Solução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [Desmistificando mainframe para a migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
