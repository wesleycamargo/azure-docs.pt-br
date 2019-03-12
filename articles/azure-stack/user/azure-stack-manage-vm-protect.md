---
title: Proteger as VMs implantadas no Azure Stack | Microsoft Docs
description: Diretrizes sobre como proteger as máquinas virtuais implantadas no Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 50b3499a52ac646b6c896dd79b2191651c5b277e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782931"
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Proteger máquinas virtuais implantadas no Azure Stack

Use este artigo como um guia para desenvolver um plano para proteger as máquinas virtuais (VMs) que os usuários implantam no Azure Stack.

Para proteger contra perda de dados e o tempo de inatividade não planejado, você precisa implementar um plano de recuperação de backup ou recuperação de desastres para aplicativos de usuário e seus dados. Esse plano pode ser exclusivo para cada aplicativo, mas segue uma estrutura estabelecida pelo estratégia de recuperação (continuidade de negócios/recuperação de Desastre) de desastres e continuidade de negócios abrangente da sua organização. É um bom ponto de partida [Projetando aplicativos resilientes para Azure](https://docs.microsoft.com/azure/architecture/resiliency), que fornece padrões gerais e práticas recomendadas para disponibilidade de aplicativos e resiliência.

>[!IMPORTANT]
> Teste seus planos de recuperação de backup e recuperação de desastres em uma base contínua. Você deve fazer isso para garantir que:
> * Os planos de trabalho
> * Os planos ainda atender às necessidades para que foram projetados.

## <a name="azure-stack-infrastructure-recovery"></a>Recuperação de infraestrutura do Azure Stack

Os usuários são responsáveis por proteger suas VMs separadamente dos serviços de infraestrutura do Azure Stack.

O plano de recuperação para serviços de infraestrutura do Azure Stack **não** incluir a recuperação de VMs do usuário, contas de armazenamento ou bancos de dados. Como o proprietário do aplicativo, você é responsável por implementar um plano de recuperação para seus aplicativos e dados.

Se a nuvem do Azure Stack está offline por um longo período ou irrecuperáveis permanentemente, você precisa ter uma recuperação plano coloque que:

* Garante o tempo de inatividade mínimo
* Mantém VMs essenciais, como servidores de banco de dados, executando
* Permite que os aplicativos manter atendendo às solicitações de usuário

O operador de nuvem do Azure Stack é responsável por criar um plano de recuperação para a infraestrutura subjacente do Azure Stack e serviços. Para saber mais, leia o artigo [recuperar da perda de dados catastrófica](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data).

## <a name="sourcetarget-combinations"></a>Combinações de origem/destino

Cada nuvem do Azure Stack é implantado em um datacenter. Um ambiente separado é necessário para que possa recuperar seus aplicativos. O ambiente de recuperação pode ser outra nuvem do Azure Stack em um datacenter diferente ou nuvem pública do Azure. Seus requisitos de privacidade de dados e Soberania de dados determinará o ambiente de recuperação para o seu aplicativo. Como habilitar a proteção para cada aplicativo, você tem a flexibilidade de escolher uma opção de recuperação específico para cada um deles. Você pode ter aplicativos em uma assinatura de backup de dados para outro datacenter. Em outra assinatura, você pode replicar dados para a nuvem pública do Azure.

Planeje sua estratégia de recuperação de backup e recuperação de desastre para cada aplicativo determinar o destino para cada aplicativo. Um plano de recuperação ajudarão sua organização corretamente dimensionar o armazenamento capacidade necessária no local e consumo na nuvem pública do projeto.

|  | Azure Global | O Azure Stack implantados no datacenter do CSP e operados pelo CSP | O Azure Stack implantados no datacenter do cliente e operados por cliente |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **O Azure Stack implantados no datacenter do CSP e operados pelo CSP** | VMs do usuário são implantadas à pilha do Azure CSP operado.<br><br>VMs do usuário são restauradas a partir do backup ou failover diretamente no Azure. | CSP opera instâncias primárias e secundárias do Azure Stack em seus próprios datacenters.<br><br>VMs do usuário são restauradas ou failover entre as duas instâncias do Azure Stack. | CSP opera o Azure Stack no site primário.<br><br>Datacenter do cliente é o destino de restauração ou failover. |
| **O Azure Stack implantados no datacenter do cliente e operados por cliente** | VMs do usuário são implantadas para o cliente operado Azure Stack.<br><br>VMs do usuário são restauradas a partir do backup ou failover diretamente no Azure. | Cliente opera o Azure Stack no site primário.<br><br>Datacenter do CSP é o destino de restauração ou failover. | Cliente opera instâncias primárias e secundárias do Azure Stack em seus próprios datacenters.<br><br>VMs do usuário são restauradas ou failover entre as duas instâncias do Azure Stack. |

![Combinações de origem e destino](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Objetivos de recuperação do aplicativo

Você precisará determinar a quantidade de tempo de inatividade e perda de dados que sua empresa pode tolerar para cada aplicativo. Quantificando o tempo de inatividade e perda de dados, você pode criar um plano de recuperação que minimiza o impacto de um desastre em sua organização. Para cada aplicativo, considere:

 - **RTO (objetivo de tempo de recuperação)**  
O RTO é o tempo máximo aceitável que um aplicativo pode ficar indisponível após um incidente. Por exemplo, o RTO é de 90 minutos significa que você deve ser capaz de restaurar o aplicativo para um estado de execução dentro de 90 minutos desde o início de um desastre. Se você tiver um baixo RTO, você pode manter uma segunda implantação continuamente em execução no modo de espera para proteger contra uma interrupção regional.
 - **RPO (objetivo de ponto de recuperação)**  
RPO é a duração máxima de perda de dados é aceitável durante um desastre. Por exemplo, se você armazena dados em um único banco de dados, com nenhuma replicação para outros bancos de dados, e executar backups a cada hora, poderá perder até uma hora de dados.

RTO e RPO são os requisitos de negócios. Conduza uma avaliação de risco para definir o RTO e RPO do aplicativo.

Outra métrica é **tempo médio de recuperação** (MTTR), que é o tempo médio que leva para restaurar o aplicativo após uma falha. MTTR é um valor empírico para um sistema. Se o MTTR exceder o RTO, uma falha no sistema causará uma interrupção do negócio inaceitável, porque não é possível restaurar o sistema dentro do RTO definido.

### <a name="backup-restore"></a>Restauração de backup

O esquema de proteção mais comuns para aplicativos baseados em máquina virtual é usar o software de backup. Fazer backup de uma VM normalmente inclui o sistema operacional, configuração do sistema operacional, binários de aplicativo e dados de aplicativo. Os backups são criados por tirar um instantâneo de toda a VM, discos ou volumes. Com o Azure Stack, você tem a flexibilidade de fazer backup de dentro do contexto do sistema operacional convidado ou do armazenamento do Azure Stack e APIs de computação. O Azure Stack não dá suporte a fazer backups no nível do hipervisor.
 
![Backup restor](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Recuperando o aplicativo requer a restauração de uma ou mais VMs na nuvem mesma ou para uma nova nuvem. Você pode direcionar uma nuvem em seu datacenter ou nuvem pública. A nuvem escolhida está completamente dentro de seu controle e baseia-se em seus requisitos de privacidade e Soberania de dados.
 
 - RTO: Medido em horas de tempo de inatividade
 - RPO: Perda de dados da variável (dependendo da frequência de backup)
 - Topologia de implantação: Ativo/passivo

#### <a name="planning-your-backup-strategy"></a>Planejando sua estratégia de backup

Planejando sua estratégia de backup e a definição dos requisitos de dimensionamento começa com quantificar o número de instâncias de VM que precisam ser protegidos. Fazer backup todas as VMs em todos os servidores em um ambiente é uma estratégia comum. No entanto, com o Azure Stack, há algumas VMs que precisa ser submetido ao backup. Por exemplo, VMs em um conjunto de dimensionamento são consideradas efêmeros recursos que podem entrar e sair, às vezes, sem aviso prévio. Todos os dados duráveis que precisam ser protegido são armazenados em um repositório separado, como um armazenamento de banco de dados ou objeto.

Considerações importantes para fazer backup de VMs no Azure Stack:

 - **Categorização**
    - Considere um modelo em que os usuários optar pelo backup de VM.
    - Defina um recuperação nível contrato de serviço (SLA) com base na prioridade de aplicativos ou o impacto nos negócios.
 - **Escala**
    - Considere escalonados backups ao integrar um grande número de novas VMs (se o backup é necessário).
    - Avalie os produtos de backup que podem capturar e transmitir dados de backup para minimizar o conteúdo de recurso na solução com eficiência.
    - Avalie os produtos de backup que armazenam com eficiência dados de backup usando backups incrementais ou diferenciais para minimizar a necessidade de backups completos em todas as VMs no ambiente.
 - **Restore**
    - Produtos de backup podem restaurar discos virtuais, dados de aplicativo dentro de uma VM existente, ou o recurso de máquina virtual inteiro e discos virtuais associados. O esquema de restauração que você precisa depende de como você planeja restaurar o aplicativo e ele terá impacto sobre o aplicativo tempo para recuperação. Por exemplo, pode ser mais fácil reimplantar o SQL server a partir de um modelo e, em seguida, restaurar os bancos de dados em vez de restaurar a VM inteira ou um conjunto de VMs.

### <a name="replicationmanual-failover"></a>Failover de replicação/manual

Uma abordagem alternativa para dar suporte a alta disponibilidade é replicar suas VMs de aplicativo para outra nuvem e contam com um failover manual. A replicação do sistema operacional, os binários do aplicativo e os dados de aplicativo pode ser executada no nível da VM ou no nível do sistema operacional convidado. O failover é gerenciado usando o software adicional que não faz parte do aplicativo.

Com essa abordagem, o aplicativo é implantado em uma nuvem e sua VM é replicado para outra nuvem. Se um failover for disparado, o secundário as VMs precisam ser ligado na nuvem segundo. Em alguns cenários, o failover cria os discos de máquinas virtuais e anexa a eles. Esse processo pode levar muito tempo para ser concluído, especialmente com um aplicativo de várias camadas que requer uma sequência de inicialização específica. Também pode haver etapas que devem ser executadas antes que o aplicativo está pronto para começar a atender às solicitações.

![Failover manual de replicação](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO: Medida de tempo de inatividade em minutos
 - RPO: Perda de dados da variável (dependendo da frequência de replicação)
 - Topologia de implantação: Espera por ativo/passivo
 
### <a name="high-availabilityautomatic-failover"></a>Failover de alta disponibilidade/automático

Para aplicativos em que sua empresa pode tolerar a alguns segundos ou minutos de tempo de inatividade e perda mínima de dados, você precisará considerar uma configuração de alta disponibilidade. Aplicativos de alta disponibilidade são projetados para recuperar de forma rápida e automática de falhas. Para falhas de hardware local, a infraestrutura do Azure Stack implementa a alta disponibilidade da rede física usando dois top de comutadores de rack. Para falhas de nível de computação, o Azure Stack usa vários nós em uma unidade de escala. No nível da VM, você pode usar conjuntos de dimensionamento em combinação com domínios de falha para garantir que falhas de nó não desative seu aplicativo.

Em combinação com conjuntos de dimensionamento, seu aplicativo precisará dar suporte a alta disponibilidade nativamente ou suporte ao uso do software de cluster. Por exemplo, Microsoft SQL Server dá suporte a alta disponibilidade nativamente para bancos de dados usando o modo de confirmação síncrona. No entanto, se você só pode dar suporte a replicação assíncrona, em seguida, haverá perda de dados. Aplicativos também podem ser implantados em um cluster de failover em que o software de cluster trata o failover automático do aplicativo.

Usando essa abordagem, o aplicativo está ativo somente em uma nuvem, mas o software é implantado em várias nuvens. Outras nuvens estão no modo de espera pronto para iniciar o aplicativo quando o failover é disparado.

 - RTO: Medido em segundos de tempo de inatividade
 - RPO: Perda mínima de dados
 - Topologia de implantação: Espera por ativo/ativo

### <a name="fault-tolerance"></a>Tolerância a falhas

O Azure Stack, disponibilidade do serviço de redundância e a infraestrutura física proteger apenas contra hardware nível falhas/falhas de tal um disco, fonte de alimentação, porta de rede ou nó. No entanto, se seu aplicativo deve estar sempre disponível e nunca pode perder todos os dados, você precisará implementar a tolerância a falhas nativamente em seu aplicativo ou usar o software adicional para habilitar a tolerância a falhas.

Primeiro, você precisa garantir que o aplicativo que as VMs são implantadas usando a escala define para proteger contra falhas de nível de nó. Para proteger contra a nuvem ficar offline, o mesmo aplicativo deve já ser implantado em uma nuvem diferente, para que ele possa continuar atendendo às solicitações sem interrupções. Normalmente, esse modelo é chamado para uma implantação ativa-ativa.

Tenha em mente que cada nuvem do Azure Stack é independente uns dos outros, portanto, as nuvens são sempre consideradas ativos com base em uma perspectiva de infraestrutura. Nesse caso, várias instâncias ativas do aplicativo são implantadas para uma ou mais nuvens Active Directory.

 - RTO: Sem tempo de inatividade
 - RPO: Sem perda de dados
 - Topologia de implantação: Ativo/ativo

### <a name="no-recovery"></a>Sem recuperação

Alguns aplicativos em seu ambiente podem não precisar de proteção contra tempo de inatividade não planejado ou perda de dados. Por exemplo, as VMs usadas para desenvolvimento e teste normalmente não precisa ser recuperado. É sua decisão de fazer sem proteção de um aplicativo ou uma VM específica. O Azure Stack não oferece backup ou replicação de VMs da infraestrutura subjacente. Semelhante ao Azure, você precisará participar de proteção para cada VM em cada uma das suas assinaturas.

 - RTO: Irrecuperável
 - RPO: Perda de dados completo

## <a name="recommended-topologies"></a>Topologias recomendadas

Considerações importantes para sua implantação do Azure Stack:

|     | Recomendações | Comentários |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VMs de backup/restauração para um destino de backup externo já implantado em seu datacenter | Recomendadas | Tirar proveito da infra-estrutura de backup existente e habilidades operacionais. Certifique-se de dimensionar a infraestrutura de backup para que ele esteja pronto para proteger as instâncias de VM adicionais. Certifique-se de infraestrutura de backup não está em estreita proximidade com sua fonte. Você pode restaurar VMs para a fonte do Azure Stack, uma instância secundária do Azure Stack, ou no Azure. |
| VMs de backup/restauração para um destino de backup externo dedicado para o Azure Stack | Recomendadas | Você pode adquirir nova infraestrutura de backup ou provisionar infraestrutura de backup dedicada para o Azure Stack. Certifique-se de infraestrutura de backup não está em estreita proximidade com sua fonte. Você pode restaurar VMs para a fonte do Azure Stack, uma instância secundária do Azure Stack, ou no Azure. |
| Backup/restauração de VMs diretamente para o Azure global ou um provedor de serviços confiáveis | Recomendadas | Desde que você pode atender a requisitos regulatórios e privacidade de dados, você pode armazenar seus backups no Azure global ou um provedor de serviços confiáveis. O ideal é que o provedor de serviço também está executando do Azure Stack, assim, você obtém consistência na experiência operacional quando você restaura. |
| Replicate/failover VMs para uma instância separada do Azure Stack | Recomendadas | No caso de failover, você precisará ter uma nuvem do Azure Stack segundo totalmente operacional assim, você pode evitar tempo de inatividade do aplicativo estendido. |
| Replicate/failover VMs diretamente no Azure ou um provedor de serviços confiáveis | Recomendadas | Desde que você pode atender a requisitos regulatórios e privacidade de dados, você pode replicar seus dados para o Azure global ou um provedor de serviços confiáveis. O ideal é que o provedor de serviço também está executando do Azure Stack, assim, você obtém consistência na experiência operacional após o failover. |
| Implantar o destino de backup na mesma nuvem do Azure Stack com os dados do aplicativo | Não é recomendado | Evite armazenar backups na mesma nuvem do Azure Stack. Tempo de inatividade não planejado da nuvem pode mantê-lo dos seus dados primários e os dados de backup. Se você optar por implantar um destino de backup como um dispositivo virtual (para fins de otimização para backup e restauração), você deve garantir que todos os dados continuamente é copiado para um local de backup externo. |
| Implantar o dispositivo de backup físico ao mesmo rack em que a solução do Azure Stack está instalada | Sem suporte | Neste momento, você não pode conectar todos os outros dispositivos na parte superior de comutadores de rack que não fazem parte da solução original. |

## <a name="next-steps"></a>Próximas etapas

Este artigo fornece diretrizes gerais para proteger as VMs de usuário implantadas no Azure Stack. Para obter informações sobre como usar os serviços do Azure para proteger as VMs do usuário, consulte:

 - [Use o Backup do Azure para fazer backup de arquivos e aplicativos no Azure Stack](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Suporte do servidor de Backup do Azure para o Azure Stack](https://docs.microsoft.com/azure/backup/ ) 
 - [Suporte de recuperação de Site do Azure para o Azure Stack](https://docs.microsoft.com/azure/site-recovery/)  

Para saber mais sobre os produtos de parceiros que oferecem proteção de VM no Azure Stack, consulte "[Protegendo aplicativos e dados no Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
