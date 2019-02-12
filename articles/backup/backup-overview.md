---
title: O que é o Backup do Azure?
description: Fornece uma visão geral do serviço de Backup do Azure e como implantá-lo como parte de sua estratégia de BCDR (continuidade dos negócios e recuperação de desastres).
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 298c9fabca9d1994e0b952fdf8b48b70370c3ec2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490662"
---
# <a name="what-is-azure-backup"></a>O que é o Backup do Azure?

O serviço de Backup do Azure faz backup de dados na nuvem do Microsoft Azure. Você pode fazer backup de computadores e cargas de trabalho locais e de VMs (máquinas virtuais) do Azure.


## <a name="why-use-azure-backup"></a>Por que usar o Backup do Azure?

O Backup do Azure oferece estes principais benefícios:

- **Descarregue o backup local**: O Backup do Azure oferece uma solução simples para fazer backup de seus recursos locais na nuvem. Obtenha um backup de curto e longo prazo sem a necessidade de implantar soluções complexas de backup local. Elimine a necessidade de fita ou backup externo.
- **Faça backup de VMs IaaS do Azure**: O Backup do Azure fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. Os backups são armazenados em um cofre dos Serviços de Recuperação com gerenciamento interno de pontos de recuperação. A configuração e a escalabilidade são simples, os backups são otimizados e você pode fazer uma restauração com facilidade, conforme necessário.
- **Dimensione com facilidade** – o Backup do Azure usa o poder subjacente e a escala ilimitada da nuvem do Azure para proporcionar alta disponibilidade – sem sobrecarga de manutenção ou de monitoramento. Você pode configurar alertas para fornecer informações sobre eventos, mas não precisa se preocupar com a alta disponibilidade de seus dados na nuvem.
- **Obtenha transferência de dados ilimitados** – o Backup do Azure não limita a quantidade de dados de entrada ou saída transferida. O Backup do Azure também não cobra pelos dados que são transferidos. No entanto, se você usar o serviço de Importação/Exportação do Azure para importar grandes quantidades de dados, haverá um custo associado aos dados de entrada. Para obter mais informações sobre esse custo, confira o [Fluxo de trabalho de backup offline no Backup do Azure](backup-azure-backup-import-export.md). Os dados de saída são aqueles transferidos de um cofre dos Serviços de Recuperação durante uma operação de restauração.
- **Mantenha os dados seguros**: A criptografia de dados permite a transmissão e o armazenamento seguros de seus dados na nuvem pública. A senha de criptografia é armazenada localmente e nunca é transmitida nem armazenada no Azure. Se for necessário restaurar os dados, somente você tem a senha de criptografia ou chave.
- **Obtenha backups consistentes com aplicativo**: Um backup consistente com aplicativo significa que um ponto de recuperação tem todos os dados necessários para restaurar a cópia de backup. O Backup do Azure fornece backups consistentes com aplicativos, garantindo que correções adicionais não sejam necessárias para restaurar os dados. Restaurar dados consistentes com aplicativos reduz o tempo de restauração, permitindo que você rapidamente retorne ao estado de execução.
- **Obtenha retenção de curto e longo prazo**: **Retenção de longo prazo** - Você pode usar os cofres de Serviços de Recuperação para retenção de dados de curto e longo prazo. O Azure não limita o período de tempo que os dados podem ser mantidos em um cofre de Serviços de Recuperação. Você pode manter dados em um cofre para como desejar. O Backup do Azure tem um limite de pontos de recuperação 9999 por instância protegidos. Consulte o [Backup e retenção](backup-introduction-to-azure-backup.md#backup-and-retention) neste artigo para obter uma explicação de como esse limite pode afetar suas necessidades de backup.
- **Gerenciamento automático de armazenamento** - ambientes híbridos geralmente exigem armazenamento heterogêneo, alguns locais e alguns na nuvem. Com o Backup do Azure, não há nenhum custo para o uso de dispositivos de armazenamento local. O Backup do Azure aloca e gerencia automaticamente o armazenamento de backup, com um modelo de pagamento conforme o uso. Pagamento conforme o uso significa que você paga apenas pelo armazenamento que consumir. Para obter mais informações, confira [Preços do Azure](https://azure.microsoft.com/pricing/details/backup).
- **Várias opções de armazenamento** -um aspecto de alta disponibilidade é a replicação de armazenamento. O Backup do Azure oferece dois tipos de replicação: [armazenamento com redundância local](../storage/common/storage-redundancy-lrs.md) e [armazenamento com redundância geográfica](../storage/common/storage-redundancy-grs.md). Escolha a opção de armazenamento de backup com base na necessidade:
    - O Armazenamento com redundância local (LRS) replica seus dados três vezes (ele cria três cópias de seus dados) em uma unidade de escala de armazenamento em um datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger seus dados contra falhas de hardware local.
    - Armazenamento com redundância geográfica (GRS) é o padrão e opção de replicação recomendada. O GRS replica seus dados para uma região secundária (a centenas de quilômetros da região primária dos dados de origem). O GRS é mais caro do que o LRS, mas fornece um nível mais alto de durabilidade para seus dados, mesmo se houver uma interrupção regional.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Qual é a diferença entre o Backup do Azure e o Azure Site Recovery?

Os serviços Backup do Azure e Azure Site Recovery contribuem para uma estratégia de BCDR (continuidade dos negócios e recuperação de desastres) em sua empresa. A BCDR consiste em dois objetivos amplos:

- Manter seus dados corporativos seguros e recuperáveis quando ocorrerem interrupções.
- Manter seus aplicativos e suas cargas de trabalho em funcionamento durante tempos de inatividade planejados e não planejados.

Ambos os serviços fornecem funcionalidades complementares, mas diferentes.

- **Azure Site Recovery**: O Site Recovery fornece uma solução de recuperação de desastre para computadores locais e VMs do Azure. Você replica os computadores de um local primário para um secundário. Quando ocorre um desastre, você faz failover dos computadores para o local secundário e os acessa nele. Quando tudo voltar a funcionar normalmente, você fará failback dos computadores para recuperá-los no site primário.
- **Backup do Azure**: O serviço de Backup do Azure faz backup dos dados de computadores locais e VMs do Azure. Os dados podem ser copiados em backup e recuperados em um nível granular, incluindo backup de arquivos, pastas, estado do sistema de computador e backup de dados com reconhecimento de aplicativo. O Backup do Azure lida com os dados em um nível mais granular do que o Site Recovery. Por exemplo, se uma apresentação em um laptop for corrompida, você usará o Backup do Azure para restaurar a apresentação. Caso deseje manter uma configuração de VM e os dados seguros e acessíveis, você poderá usar o Site Recovery.  

Use os pontos da tabela para ajudar a descobrir suas necessidades de BCDR. 

**Objetivo** | **Detalhes** | **Comparação**
--- | --- | --- | --- |
**Backup de dados/retenção** | Os dados de backup podem ser retidos e armazenados por dias, meses ou até anos, se necessário, de uma perspectiva de conformidade. | Soluções de backup, como o Backup do Azure, permitem que você escolha minuciosamente os dados que deseja copiar em backup e ajuste as políticas de backup e retenção.<br/><br/> O Site Recovery não permite o mesmo ajuste fino.
**RPO (objetivo de ponto de recuperação)** | A quantidade de perda de dados aceitável se uma recuperação precisar ser feita. | Os backups têm um RPO mais variável.<br/><br/> Os backups de VM geralmente têm um RPO de um dia, enquanto os backups de banco de dados têm RPOs de até 15 minutos.<br/><br/> O Site Recovery fornece um RPO baixo, pois a replicação é contínua ou frequente, de modo que o delta entre a cópia de origem e de réplica seja pequeno.
**RTO (objetivo de tempo de recuperação)** |O tempo necessário para concluir uma recuperação ou restauração. | Devido ao RPO maior, a quantidade de dados que uma solução de backup precisa processar normalmente é muito maior, o que leva a RTOs mais longos. Por exemplo, a restauração de dados de fitas pode demorar dias, dependendo do tempo necessário para transportar a fita de um local externo. | As soluções de recuperação de desastre, como o Site Recovery, têm um RPO baixo, pois a replicação contínua/frequente geralmente significa que o destino está mais altamente sincronizado com a origem. |

## <a name="what-backup-scenarios-are-supported"></a>Há suporte para quais cenários de backup?

O Backup do Azure pode fazer backup de computadores locais e VMs do Azure.

**Computador** | **Cenário de backup**
--- | ---
**Computadores locais (físicos/virtuais)** |  Faça backup de computadores locais individuais.<br/><br/>Faça backup de computadores locais protegidos pelo System Center DPM (Data Protection Manager)<br/><br/> Faça backup de computadores locais protegidos pelo MABS (Servidor de Backup do Microsoft Azure).
**VMs do Azure** | Faça backup de VMs individuais do Azure.<br/><br/> Faça backup de VMs do Azure protegidas pelo DPM ou pelo MABS.

### <a name="back-up-servers"></a>Fazer backup de servidores

Talvez você deseje fazer backup de servidores e cargas de trabalho locais ou de VMs do Azure e suas cargas de trabalho, primeiro em um servidor de backup e, em seguida, em um cofre dos Serviços de Recuperação. 

**Servidor de backup** | **Detalhes**
--- | ---
**System Center DPM (Data Protection Manager)** | Use o Backup do Azure para fazer backup de dados protegidos pelo DPM:<br/><br/> - O DPM pode estar em execução em um local (físico ou virtual) ou no Azure.<br/><br/> - Proteja diferentes tipos de dados em execução em computadores locais e VMs do Azure fazendo backup dos dados no servidor DPM.<br/><br/> Por sua vez, o servidor DPM pode ser copiado em backup em um cofre dos Serviços de Recuperação usando o serviço de Backup do Azure.<br/><br/> O servidor DPM e os computadores protegidos por ele precisam estar na mesma rede. Os computadores locais só podem ser protegidos por um servidor DPM local. Da mesma forma, as VMs do Azure só podem ser protegidas pelo DPM em execução no Azure.
**MABS (Servidor de Backup do Microsoft Azure)** | Use o Backup do Azure para fazer backup de dados protegidos pelo MABS:<br/><br/> - O MABS pode estar em execução em um local (físico ou virtual) ou no Azure.<br/><br/> - Proteja diferentes tipos de dados em execução em computadores locais e VMs do Azure fazendo backup dos dados no MABS.<br/><br/> - Por sua vez, o MABS pode ser copiado em backup em um cofre dos Serviços de Recuperação usando o serviço de Backup do Azure.<br/><br/> - O MABS fornece funcionalidades semelhantes àquelas oferecidas pelo DPM, exceto que não é possível fazer backup em fita usando o MABS. O MABS não exige uma licença do System Center.<br/><br/> Como o DPM, os computadores locais só podem ser protegidos por um MABS local. As VMs do Azure só podem ser protegidas por um MABS no Azure.

As vantagens de fazer backup primeiro no DPM/MABS e, em seguida, em um cofre são as seguintes:

- Fazer backup no DPM/MABS fornece backups com reconhecimento de aplicativo otimizados para aplicativos comuns, como SQL Server, Exchange e SharePoint, além de backups de arquivo/pasta/volume e backups de estado do computador (bare-metal, estado do sistema).
- Você não precisa instalar o agente do Backup do Azure em cada computador que deseja fazer backup. Cada computador executa o agente de proteção do DPM/MABS, e o agente dos Serviços de Recuperação do Microsoft Azure do Backup do Azure é executado somente no servidor DPM/MABS.
- Você tem mais flexibilidade e opções de agendamento granulares para a execução de backups.
- Você pode gerenciar backups para vários computadores reunidos em grupos de proteção em um único console. Isso é particularmente útil quando os aplicativos são organizados em camadas em vários computadores e você deseja fazer backup deles juntos.

## <a name="what-can-be-backed-up"></a>O que pode ser copiado em backup?

**Computador** | **Servidor de backup** | **Fazer backup**
--- | --- | ---
VMs locais do Windows | Não copiado em backup no DPM ou no MABS | Faça backup de arquivos, pastas e do estado do sistema.
VMs do Azure (Windows e Linux) | Não copiado em backup no DPM ou no MABS | Faça backup de arquivos, pastas e do estado do sistema.<br/><br/> Os backups têm reconhecimento de aplicativo para computadores Windows e reconhecimento de arquivo para computadores Linux.
VMs locais/VMs do Azure | Protegido pelo DPM | Faça backup de tudo o que é protegido pelo DPM, incluindo arquivos/pastas/compartilhamentos/volumes e dados específicos do aplicativo. [Saiba](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) o que o DPM pode incluir no backup.
VMs locais/VMs do Azure | Protegido pelo MABS | Faça backup de tudo o que é protegido pelo MABS, incluindo arquivos/pastas/compartilhamentos/volumes e dados específicos do aplicativo. [Saiba](backup-mabs-protection-matrix.md) o que o MABS pode copiar em backup.

## <a name="what-backup-agents-do-i-need"></a>Quais agentes de backup são necessários?
**Cenário** | **Agente** | **Detalhes**
--- | --- | ---
Em computadores locais (sem servidor de backup) | O agente do MARS (Serviços de Recuperação do Microsoft Azure) é executado no computador Windows. | Baixe e instale o agente do MARS durante a implantação do Backup do Azure.<br/><br/> Suporte somente para computadores Windows.
VMs do Azure (sem servidor de backup) | Nenhum agente explícito é necessário. A extensão de VM do Azure para backup é executada na VM do Azure. | A extensão é instalada quando você executa o primeiro backup da VM do Azure.<br/><br/> Suporte para Windows e Linux.
Computadores locais/VMs do Azure protegidas pelo DPM. | O agente do MARS é executado no servidor DPM. | Você não precisa do agente do MARS em computadores individuais.<br/><br/> Quando você implanta o DPM, o agente de proteção do DPM é instalado em cada computador protegido. 
Fazer backup de computadores locais e VMs do Azure protegidas pelo MABS | O agente do MARS é executado no MABS. | Você não precisa do agente do MARS em computadores individuais.<br/><br/> Quando você implanta o MABS, o agente de proteção do MABS é instalado em cada computador protegido. 


## <a name="which-backup-agent-should-i-use"></a>Qual agente de backup devo usar?

**Backup** | **Solução** | **Limitação**
--- | --- | ---
Desejo fazer backup de computadores Windows locais. Os computadores não são protegidos pelo DPM nem pelo MABS | Instale o agente do MARS no computador. | Você pode fazer backup de arquivos, de pastas e do estado do sistema no Azure. Os backups não têm reconhecimento de aplicativo.
Desejo fazer backup de computadores Linux locais. Os computadores não são protegidos pelo DPM nem pelo MABS. | Você precisará implantar o DPM ou o MABS para fazer backup no Azure.
Desejo fazer backup de aplicativos executados em computadores Windows locais | Para backups com reconhecimento de aplicativo, os computadores Windows precisam ser protegidos pelo DPM ou pelo MABS.
Desejo fazer backup de VMs do Azure | Execute um backup usando o Backup do Azure. A extensão de backup será automaticamente configurada na VM do Azure no Windows ou no Linux. | Os discos de VM são copiados em backup.<br/><br/> Para VMs Windows, o backup é consistente com aplicativo. para o Linux, o backup é consistente com arquivo. Caso precise de reconhecimento de aplicativo, você precisará configurar isso com scripts personalizados.
Desejo fazer backup de VMs do Azure com flexibilidade granular das configurações de backup e recuperação | Proteja as VMs do Azure com o DPM ou o MABS em execução no Azure para flexibilidade adicional para o agendamento de backup e flexibilidade total para proteger e restaurar arquivos, pastas, volumes, aplicativos e o estado do sistema.


## <a name="next-steps"></a>Próximas etapas

- [Examine](backup-architecture.md) a arquitetura e os componentes para diferentes cenários de backup.
- [Verifique](backup-support-matrix.md) os recursos e as configurações compatíveis para backup.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

