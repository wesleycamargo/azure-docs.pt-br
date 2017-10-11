---
title: "Pré-requisitos de servidor físico para replicação do Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Resume os pré-requisitos para a replicação de cargas de trabalho em execução em servidores físicos Windows/Linux para o Azure usando o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 318156ba-793b-48d0-98d4-cc5436bf28a3
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 5f5cb4b9b6fcee6b56ccdcb6223afddd9de90fec
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2017
---
# <a name="step-2-review-the-prerequisites-for-physical-server-to-azure-replication"></a>Etapa 2: Examinar os pré-requisitos de servidor físico para a replicação do Azure

Reveja os pré-requisitos resumidos na tabela.


**Pré-requisito** | **Detalhes**
--- | ---
**As tabelas** | Saiba mais sobre [requisitos do Azure](site-recovery-prereq.md#azure-requirements)
**Servidor de configuração local** | Você precisa de um servidor físico (ou VM VMware) executando o Windows Server 2012 R2 ou versão posterior. Você pode configurar este servidor durante a implantação de Site Recovery.<br/><br/> Por padrão, o servidor de processo e o servidor de destino mestre também são instalados nesta máquina. Ao escalar verticalmente, talvez seja necessário um servidor de processo separado. Se você fizer isso, ele terá os mesmos requisitos que o servidor de configuração.<br/><br/> [Saiba mais](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements).
**VMs locais** | Os computadores que você deseja replicar devem estar executando um [sistema operacional com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) e estar em conformidade com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URLs** | O servidor de configuração precisa acessar estas URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se tiver regras de firewall baseadas no endereço IP, verifique se elas permitem a comunicação com o Azure.<br/></br> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/></br> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).<br/><br/> Baixe o MySQL desta URL: .http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi
**Serviço de mobilidade** | Instalado em todos os servidores replicados.




## <a name="limitations"></a>Limitações

Observe as limitações resumidas na tabela antes de implantar.

**Limitação** | **Detalhes**
--- | ---
**As tabelas** | Contas de armazenamento e rede devem estar na mesma região que o cofre<br/><br/> Se usar uma conta de armazenamento premium, você também precisará de uma conta de armazenamento padrão para armazenar logs de replicação<br/><br/> Você não pode replicar contas premium no Sul e no Centro da Índia.
**Servidor de configuração local** | Se você usa uma VM VMware como a máquina do servidor de configuração, o tipo de adaptador da VM VMware deve ser VMXNET3. Caso contrário, [instale esta atualização](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> Para uma VM VMware, o vSphere PowerCLI 6.0 deve ser instalado.<br/><br> O computador não deve ser um controlador de domínio.<br/><br/> O computador deve ter um endereço IP estático.<br/><br/> O nome do host deve ter 15 caracteres ou menos, e o sistema operacional deve estar em inglês.
**Replicar máquinas** | Verifique [Limitações de VM do Azure](site-recovery-prereq.md#azure-requirements)<br/><br/> Você não pode replicar máquinas virtuais com discos criptografados ou máquinas virtuais com inicialização EFI/UEFI.<br/><br> Não há suporte para clusters de disco compartilhado. Se a VM de origem tiver agrupamento NIC é feita a conversão para uma única NIC após o failover.<br/><br/> Se as VMs tiverem um disco iSCSI, a Site Recovery o converterá em um arquivo VHD após o failover. Se o destino iSCSI puder ser acessado pela VM do Azure, ela se conectará a ele e o verá juntamente com o VHD. Se isso acontecer, desconecte o destino de iSCSI.<br/><br/> Se desejar habilitar a consistência de várias VMs, o que habilita VMs que executam a mesma carga de trabalho a serem recuperadas juntas em um ponto de dados consistente, abra a porta 20004 na VM.<br/><br/> O Windows deve estar instalado na unidade C. O disco do SO deve ser básico e não dinâmico. O disco de dados pode ser dinâmico.<br/><br/> Os arquivos Linux /etc/hosts nas VMs devem conter entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede. O nome do host, os pontos de montagem, o nome do dispositivo e os caminhos do sistema e nomes de arquivos (/etc/; /usr) devem ser somente em inglês.<br/><br/> Tipos específicos de [armazenamento do Linux](site-recovery-support-matrix-to-azure.md#support-for-storage) têm suporte.<br/><br/>Crie ou defina **disk.enableUUID=true** nas configurações de VM. Isso fornece um UUID consistente para o VMDK, para que ele seja montado corretamente, e garante que apenas as alterações delta sejam transferidas para o local durante o failback, sem replicação completa.


## <a name="next-steps"></a>Próximas etapas

- Se estiver fazendo uma implantação completa, vá para a [Etapa 3: planejar a capacidade](physical-walkthrough-capacity.md)
- Se estiver fazendo uma implantação de teste simples, vá para a [Etapa 4: Planejar a rede](physical-walkthrough-network.md).
