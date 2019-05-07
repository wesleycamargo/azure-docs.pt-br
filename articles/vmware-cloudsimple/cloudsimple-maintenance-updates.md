---
title: Solução de VMware do Azure por CloudSimple - CloudSimple manutenção e atualizações
description: Descreve o processo de serviço CloudSimple para manutenção agendada e atualizações
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160239"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Atualizações e manutenção de CloudSimple

O ambiente de nuvem privada foi projetado para não ter nenhum ponto único de falha:

* ESXi clusters são configurados com alta disponibilidade do vSphere. Os clusters são dimensionados para ter pelo menos um nó sobressalente para garantir a resiliência.
* Com redundância de armazenamento principal é fornecido pelo vSAN, que exige que pelo menos três nós para fornecer proteção contra uma falha de única. vSAN pode ser configurado para fornecer maior capacidade de recuperação para clusters maiores.
* as VMs do Gerenciador de NSX, PSC e vCenter são configuradas com a política de armazenamento RAID 10 para proteger contra falhas de armazenamento. As VMs são protegidas contra falhas de rede/nó por vSphere alta disponibilidade.
* Hosts de ESXi ter NICs e ventoinhas redundantes.
* Comutadores TOR e lombada são configurados em pares de alta disponibilidade para fornecer resiliência.

CloudSimple monitora as seguintes VMs de atividade e disponibilidade continuamente e fornece SLAs de disponibilidade:

* Hosts ESXi
* vCenter
* PSC
* Gerenciador de NSX

CloudSimple também monitora o seguinte continuamente para falhas:

* Discos rígidos
* Portas físicas NIC
* Servidores
* Ventiladores
* Potência
* Opções
* Portas de comutador

Se um disco ou um nó falhar, um novo nó é adicionado automaticamente para o cluster do VMware afetado para colocá-lo imediatamente para integridade.

CloudSimple faz o backup, mantém e atualiza esses elementos de VMware em nuvens privadas:

* ESXi
* Serviços de plataforma do vCenter
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Fazer backup e restaurar

Backup CloudSimple inclui:

* Backups incrementais noturnos do vCenter, PSC e DVS regras.
* Uso do vCenter APIs nativas para fazer backup de componentes na camada de aplicativo.
* Backup automático antes de qualquer atualização ou atualização do software de gerenciamento do VMware.
* Criptografia de dados na origem, pelo vCenter, antes de transferência de dados por um canal criptografado do TLS1.2 para o Azure. Os dados são armazenados em um blob do Azure, onde ela é replicada entre regiões.

Você pode solicitar uma restauração abrindo uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Manutenção 

CloudSimple faz vários tipos de manutenção planejada.

### <a name="backendinternal-maintenance"></a>Manutenção de back-end/interno

Essa manutenção normalmente envolve a reconfiguração de ativos físicos ou instalação de patches de software. Ela não afeta o consumo normal dos ativos que estão sendo atendidas. Com NICs redundantes vai cada rack físico, o tráfego de rede normal e operações de nuvem privada não são afetadas. Você pode notar um impacto no desempenho apenas se sua organização espera usar a largura de banda total redundante durante o intervalo de manutenção.

### <a name="cloudsimple-portal-maintenance"></a>Manutenção de portal CloudSimple

Algum tempo de inatividade do serviço limitado é necessário quando o plano de controle CloudSimple ou a infraestrutura é atualizada. Atualmente, os intervalos de manutenção podem ser tão frequentes como uma vez por mês. A frequência é esperada para recusar ao longo do tempo. CloudSimple fornece notificação para a manutenção de portal e mantém o intervalo mais curto possível. Durante um intervalo de manutenção do portal, os serviços a seguir continuam a funcionar sem qualquer impacto:

* Aplicativos e o plano de gerenciamento do VMware
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

### <a name="vmware-infrastructure-maintenance"></a>Manutenção de infraestrutura do VMware

Ocasionalmente, é necessário fazer alterações na configuração da infraestrutura do VMware.  Atualmente, esses intervalos podem ocorrer a cada 1 e 2 meses, mas espera-se que a frequência recusar ao longo do tempo. Esse tipo de manutenção geralmente pode ser feito sem interromper o consumo dos serviços CloudSimple normal. Durante um intervalo de manutenção do VMware, os serviços a seguir continuam a funcionar sem qualquer impacto:

* Aplicativos e o plano de gerenciamento do VMware
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

## <a name="updates-and-upgrades"></a>Atualizações e Upgrades

CloudSimple é responsável pelo gerenciamento de ciclo de vida do software de VMware (ESXi, vCenter, PSC e NSX) na nuvem privada.

Atualizações de software incluem:

* **Patches**. Patches de segurança ou correções de bugs, lançadas pela VMware.
* **Atualizações**. Alteração de versão secundária de um componente de pilha do VMware.
* **Atualizações**. Alteração de versão principal de um componente de pilha do VMware.

CloudSimple testa um patch de segurança crítica, assim que ele se torna disponível do VMware. Por SLA, CloudSimple lança o patch de segurança para ambientes de nuvem privada dentro de uma semana.

CloudSimple fornece atualizações de manutenção trimestrais para componentes de software do VMware. Quando uma nova versão principal do software VMware está disponível, CloudSimple trabalha com clientes para coordenar uma janela de manutenção adequado para a atualização.

## <a name="next-steps"></a>Próximas etapas

[Fazer backup de VMs de carga de trabalho usando o Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).