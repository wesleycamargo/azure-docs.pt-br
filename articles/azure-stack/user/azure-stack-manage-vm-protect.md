---
title: Proteger as VMs implantadas na pilha do Azure | Microsoft Docs
description: "Diretrizes sobre como proteger as máquinas virtuais implantadas na pilha do Azure."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: 02get-started-article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: hector.linares
ms.openlocfilehash: e7c437e3310fbf5c921920a3f08ecb8fe1f0d931
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Proteger máquinas virtuais implantadas na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Este artigo aborda as diretrizes sobre como proteger VMs implantadas na pilha do Azure do usuário.

Para proteger contra perda de dados e o tempo de inatividade não planejado, você precisa implementar um plano de recuperação de backup ou recuperação de desastres para seus aplicativos de usuário e dados. Esse plano é exclusivo para cada aplicativo, mas segue uma estrutura estabelecida pela estratégia de recuperação (BC/DR) de desastre e continuidade de negócios abrangente da sua organização. Para padrões gerais e práticas recomendadas para o aplicativo de disponibilidade e resiliência consulte [desenvolvendo aplicativos resilientes do Azure](https://docs.microsoft.com/azure/architecture/resiliency) no Centro de arquitetura do Azure.

## <a name="azure-stack-infrastructure-recovery"></a>Recuperação de infraestrutura de pilha do Azure

Os usuários precisarão proteger suas VMs separadamente dos serviços de infraestrutura da pilha do Azure.

Se a nuvem do Azure pilha estiver offline por um longo período ou irrecuperáveis permanentemente, você precisará ter um plano para manter o seu aplicativo atendendo a solicitações de usuário com o tempo de inatividade mínimo. O operador da nuvem do Azure pilha é responsável para preparar um plano de recuperação para os serviços e infraestrutura subjacente de pilha do Azure. Para obter mais informações, leia o artigo [recuperação da perda de dados catastrófica de](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data). 

O plano de recuperação para os serviços de infraestrutura do Azure pilha não incluir a recuperação de VMs do usuário, contas de armazenamento ou bancos de dados. Como o proprietário do aplicativo, você é responsável por implementar um plano de recuperação para seus aplicativos e dados.
 
## <a name="sourcetarget-combinations"></a>Combinações de origem/destino

Cada nuvem do Azure pilha é implantado em um datacenter. Um ambiente separado é necessário para que possa recuperar seus aplicativos. O ambiente pode ser uma nuvem do Azure pilha separado em um datacenter diferente ou na nuvem pública do Azure. Seu Soberania de dados e requisitos de privacidade de dados determinará o ambiente de recuperação para o seu aplicativo. Como habilitar a proteção para cada aplicativo, você tem a flexibilidade de escolher uma opção de recuperação específico para cada um. Você pode ter aplicativos na assinatura de um backup de dados para outro datacenter. Em outra assinatura, você poderá replicar dados na nuvem pública do Azure. 
 
Planeje sua estratégia de recuperação de backup e recuperação de desastres para cada aplicativo determinar o destino para cada aplicativo. Isso vai ajudar sua organização corretamente tamanho local a capacidade de armazenamento necessária e o consumo na nuvem pública do projeto. 

|  | Azure global | A pilha do Azure implantada no datacenter do CSP e operada por CSP | Pilha do Azure implantada no datacenter do cliente e operada por cliente |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **A pilha do Azure implantada no datacenter do CSP e operada por CSP** | Usuário VMs são implantadas para a pilha do Azure operado do CSP. VMs do usuário são restauradas do backup ou failover diretamente no Azure. | CSP opera as instâncias primárias e secundárias da pilha do Azure em seus próprios data centers. VMs do usuário são restauradas ou failover entre as duas instâncias de pilha do Azure. | CSP opera pilha do Azure no site primário. Datacenter do cliente é o destino de restauração ou failover. |
| **Pilha do Azure implantada no datacenter do cliente e operada por cliente** | Usuário VMs são implantadas para o cliente operado pilha do Azure. VMs do usuário são restauradas do backup ou failover diretamente no Azure. | Cliente opera as instâncias primárias e secundárias da pilha do Azure em seus próprios data centers. VMs do usuário são restauradas ou failover entre as duas instâncias de pilha do Azure. | Cliente opera pilha do Azure no site primário. Datacenter do CSP é o destino de restauração ou failover. |

![Combinações de origem e destino](media\azure-stack-manage-vm-backup\vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Objetivos de recuperação de aplicativos

Você precisará determinar a quantidade de tempo de inatividade e perda de dados que sua organização pode tolerar para cada aplicativo, para determinar a melhor abordagem para proteger seus aplicativos na pilha do Azure. A quantidade de centro e a quantidade de valores de perda de dados são necessários para identificar-se para desenvolver e implementar um plano de recuperação que minimiza o impacto para sua organização. Para cada aplicativo, considere:  
 - **Objetivo de tempo de recuperação (RTO)**  
O RTO é o tempo máximo aceitável que um aplicativo pode estar indisponível após um incidente. Se o RTO for de 90 minutos, você deverá ser capaz de restaurar o aplicativo para um estado de execução dentro de 90 minutos a partir do início de um desastre. Se você tiver um baixo RTO, você pode manter uma segunda implantação continuamente em execução no modo de espera para proteger contra uma interrupção regional.
 - **Objetivo de ponto de recuperação (RPO)**  
RPO é a duração máxima de perda de dados é aceitável durante um desastre. Por exemplo, se você armazena dados em um único banco de dados, com nenhuma replicação para outros bancos de dados, e executar backups a cada hora, poderá perder até uma hora de dados.
 
RTO e RPO são os requisitos de negócios. Conduza uma avaliação de risco para definir o RTO e RPO do aplicativo. Outra métrica comum é **tempo médio de recuperação** (MTTR), que é o tempo médio necessário para restaurar o aplicativo após uma falha. MTTR é um fato empírico sobre um sistema. Se o MTTR exceder o RTO, uma falha no sistema causará uma interrupção do negócio inaceitável, porque não é possível restaurar o sistema dentro do RTO definido.

### <a name="backup-restore"></a>Backup-restore

O esquema de proteção mais comuns para aplicativos baseados em VM é usar o software de backup. Fazendo backup de uma VM normalmente inclui o sistema operacional, configuração do sistema operacional, os binários de aplicativo e dados de aplicativo. Os backups são criados usando um instantâneo de toda a VM, os volumes ou discos. Com a pilha do Azure, você tem a flexibilidade de backup de dentro do contexto do sistema operacional convidado ou do armazenamento do Azure pilha e APIs de computação. A pilha do Azure não oferece suporte a fazer backups no nível do hipervisor. 
 
![Backup-restor](media\azure-stack-manage-vm-backup\vm_backupdataflow_03.png)
 
A recuperação do aplicativo requer a restauração de uma ou mais máquinas virtuais para a mesma nuvem ou em uma nuvem de novo. Você pode direcionar uma nuvem no seu data center ou na nuvem pública. Quais nuvem de destino está completamente dentro de seu controle e baseia-se nos seus requisitos de privacidade e Soberania de dados. 
 
 - RTO: Tempo de inatividade medido em segundos 
 - RPO: Perda de dados mínima
 - Topologia de implantação: ativo/passivo 

#### <a name="planning-your-backup-strategy"></a>Planejar sua estratégia de backup

Planejar sua estratégia de backup e definir os requisitos de escala inicia com a quantificar o número de instâncias VM que precisam ser protegidos. Fazendo backup de todas as VMs em todos os servidores em um ambiente é uma estratégia comum. No entanto, com a pilha do Azure, há algumas VMs que precisa ser feito. Por exemplo, as VMs em um conjunto de escala são consideradas efêmeros recursos que podem ir e vir, às vezes, sem aviso prévio. Quaisquer dados duráveis que precisam ser protegidos são armazenados em um repositório separado, como um armazenamento de banco de dados ou objeto. Considerações importantes para fazer backup de máquinas virtuais na pilha do Azure:

 - **Categorização**
    - Considere um modelo onde os usuários optar pelo backup de VM.
    - Defina uma recuperação que SLA com base na prioridade de aplicativos ou o impacto nos negócios.
 - **Escala**
    - Considere backups escalonados quando um grande número de novas VMs de entrada (se o backup é necessário).
    - Avalie produtos de backup que podem capturar e transmitir dados de backup para minimizar o conteúdo de recurso na solução com eficiência.
    - Avalie produtos de backup que armazenam com eficiência dados de backup usando backups incrementais ou diferenciais para minimizar a necessidade de backups completos de pull em todas as máquinas virtuais no ambiente.
 - **Restore**
    - Produtos de backup podem restaurar discos virtuais, dados de aplicativo dentro de uma VM existente, ou o recurso inteiro de VM e discos virtuais associados. O esquema de restauração precisar depende de como você planeja restaurar o aplicativo e terá impacto sobre o tempo de recuperação do aplicativo. Por exemplo, pode ser mais fácil reimplantar o SQL server a partir de um modelo e, em seguida, restaurar os bancos de dados em vez de restaurar a VM inteira ou um conjunto de máquinas virtuais.


### <a name="replicationmanual-failover"></a>Failover de replicação/manual

Uma abordagem alternativa para dar suporte a alta disponibilidade é replicar suas VMs de aplicativo para outra nuvem dependem de um failover disparado manualmente. A replicação do sistema operacional, os binários do aplicativo e os dados de aplicativo pode ser executada no nível da VM ou no nível do sistema operacional convidado. Gerencie o failover usando o software adicional separado do aplicativo.
 
Com essa abordagem, o aplicativo só é implantado em uma nuvem. A máquina virtual é replicada para outros um em um de seus data centers de nuvem ou na nuvem pública. Se um failover é disparado, o secundário VMs precisará ser ligado na nuvem segundo. Em alguns casos, o failover será criar as VMs e anexá-los aos discos. Esse processo pode levar um longo período de tempo para ser concluída, principalmente em um aplicativo de várias camadas com uma sequência de inicialização específica. Pode haver etapas adicionais que devem ser executadas antes que o aplicativo está pronto para iniciar solicitações de serviço.

![Failover manual de replicação](media\azure-stack-manage-vm-backup\vm_backupdataflow_02.png)

 - RTO: Tempo de inatividade em minutos 
 - RPO: Perda de dados da variável
 - Topologia de implantação: modo de espera por ativo/passivo
 
### <a name="high-availabilityautomatic-failover"></a>Failover de alta disponibilidade/automático

Para aplicativos em que sua empresa pode tolerar alguns segundos ou minutos de tempo de inatividade e perda mínima de dados, você precisará considerar uma configuração de alta disponibilidade. Aplicativos de alta disponibilidade são projetados para se recuperar de forma rápida e automática de falhas. Para falhas de hardware local, a infraestrutura de pilha do Azure implementa a alta disponibilidade da rede física usando dois principais de switches de rack. Para falhas de nível de computação, a pilha do Azure usa vários nós em uma unidade de escala. No nível da VM, você pode usar conjuntos de escala em combinação com domínios de falha para garantir que as falhas de nó não entrem em seu aplicativo. 
 
Em combinação com conjuntos de escala, seu aplicativo precisará dar suporte à alta disponibilidade nativamente suporta o uso de software de cluster. Por exemplo, Microsoft SQL Server oferece suporte a alta disponibilidade nativamente para bancos de dados usando o modo de confirmação síncrona. No entanto, se você só pode oferecer suporte a replicação assíncrona, em seguida, haverá perda de dados. Aplicativos também podem ser implantados em um cluster de failover em que o software de cluster trata o failover automático do aplicativo. 
 
Usando essa abordagem, o aplicativo só está ativo em uma nuvem, mas o software é implantado em várias nuvens. Outras nuvens estão no modo de espera pronto para iniciar o aplicativo quando o failover é acionado.

 - RTO: Tempo de inatividade medido em segundos 
 - RPO: Perda de dados mínima
 - Topologia de implantação: modo de espera por ativo/ativo

### <a name="fault-tolerance"></a>Tolerância a falhas

Pilha do Azure, disponibilidade do serviço de redundância e a infraestrutura física proteger apenas contra falhas de nível de hardware como um disco com falha, falha na fonte de alimentação, falha na porta de rede ou nó. No entanto, se seu aplicativo deve estar sempre disponível e nunca pode perder os dados, você precisará implementar a tolerância a falhas nativamente no seu aplicativo ou habilitar a tolerância a falhas com o software adicional. 
 
Primeiro, você precisa garantir que o aplicativo que VMs são implantadas em escala define para proteger contra falhas de nível de nó. Para proteger contra a nuvem off-line, o mesmo aplicativo já deve estar implantado em uma nuvem diferente, então ele poderá continuar atendendo a solicitações sem interrupções. Normalmente, isso é chamado para uma implantação ativa.
 
Tenha em mente que cada nuvem do Azure pilha é independente do outro, para que as nuvens são consideradas sempre ativas a partir de uma perspectiva de infraestrutura. Nesse caso, várias instâncias ativas do aplicativo são implantadas em uma ou mais nuvens ativos. 
 
 - RTO: Nenhum tempo de inatividade
 - RPO: Sem perda de dados
 - Topologia de implantação: ativo/ativo

### <a name="no-recovery"></a>Sem recuperação

Alguns aplicativos no seu ambiente podem não precisar de proteção contra perda de dados ou de tempo de inatividade não planejado. Por exemplo, VMs usado para desenvolvimento e teste geralmente não precisam ser recuperados. É sua decisão em sem proteção de um aplicativo ou uma VM específica. A pilha do Azure não oferece backup ou replicação de máquinas virtuais da infraestrutura subjacente. Semelhante ao Azure, você precisará aceitar a proteção para cada VM em cada uma das suas assinaturas.  
 
 - RTO: Unrecoverable 
 - RPO: Perda de dados completo
 
## <a name="recommended-topologies"></a>Topologias recomendadas 

Considerações importantes para sua implantação do Azure de pilha:

|     | Recomendações | Comentários |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Backup/restauração de máquinas virtuais para um destino de backup externo já implantado em seu data center | Recomendadas | Tirar proveito da infra-estrutura de backup existente e a operação. Certifique-se de dimensionar a infra-estrutura de backup para que ele está pronto para proteger as instâncias de VM adicionais. Verifique se a infra-estrutura de backup não está em estreita proximidade com sua fonte. Você pode restaurar máquinas virtuais para a fonte de pilha do Azure, uma instância secundária da pilha do Azure ou do Azure. |
| Backup/restauração de máquinas virtuais para um destino de backup externo dedicado a pilha do Azure | Recomendadas | Você pode comprar nova infraestrutura de backup ou provisionar a infra-estrutura de backup dedicada para a pilha do Azure. Verifique se a infra-estrutura de backup não está em estreita proximidade com sua fonte. Você pode restaurar máquinas virtuais para a fonte de pilha do Azure, uma instância secundária da pilha do Azure ou do Azure. |
| Máquinas virtuais diretamente para o Azure global ou um provedor confiável de backup e restauração | Recomendadas | Como você pode atender a seus requisitos normativos e privacidade de dados, você pode armazenar os backups no Azure global ou um provedor confiável. O ideal é o provedor de serviços também está executando pilha do Azure para obter consistência na experiência operacional quando você restaurar. |
| Replicar/failover VMs a uma instância separada de pilha do Azure | Recomendadas | No caso de failover, você precisará ter uma nuvem do Azure pilha segundo totalmente operacional, assim, você pode evitar tempo de inatividade do aplicativo. |
| Replicar/failover VMs diretamente no Azure ou em um provedor de serviços confiável | Recomendadas | Como você pode atender a seus requisitos normativos e privacidade de dados, você pode replicar seus dados no Azure global ou um provedor confiável. O ideal é o provedor de serviços também está executando pilha do Azure para obter consistência na experiência operacional após o failover. |
| Implantar o destino de backup na nuvem do Azure pilha mesmo com os dados do aplicativo | Não é recomendado | Evite armazenar backups na mesma nuvem de pilha do Azure. Tempo de inatividade não planejado da nuvem pode impedi-lo de seus dados primários e de dados de backup. Se você optar por implantar um destino de backup como um dispositivo virtual (para fins de otimização para backup e restauração), você deve garantir que todos os dados continuamente é copiado para um local de backup externo. |
| Implantar o dispositivo de backup físico ao mesmo rack em que a solução de pilha do Azure está instalada | Sem suporte | Neste momento, você não pode se conectar todos os outros dispositivos para a parte superior de switches de rack que não fazem parte da solução original. |
 
## <a name="next-steps"></a>Próximas etapas 

Neste artigo, abordamos as diretrizes sobre como proteger VMs implantadas na pilha do Azure do usuário. Para obter mais informações sobre como proteger suas VMs usando os serviços do Azure, consulte:
 - [Suporte de servidor de Backup do Azure para a pilha do Azure](https://docs.microsoft.com/en-us/azure/backup/ ) 
 - [Suporte do Azure Site Recovery para a pilha do Azure](https://docs.microsoft.com/en-us/azure/site-recovery/)  
 
Para saber mais sobre os produtos de parceiros que oferecem proteção de VM na pilha do Azure, consulte "[proteger aplicativos e dados na pilha do Azure](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
