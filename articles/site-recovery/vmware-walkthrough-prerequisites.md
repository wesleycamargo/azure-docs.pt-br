---
title: "Pré-requisitos para replicação do VMware para o Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Resume os pré-requisitos para a replicação de cargas de trabalho em execução em VMs VMware para o Azure usando o serviço Azure Site Recovery."
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
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 7a82e58d9ff9208130c43fcd11d03dcc3238696a
ms.contentlocale: pt-br
ms.lasthandoff: 08/25/2017

---

# <a name="step-2-review-the-prerequisites-for-vmware-to-azure-replication"></a>Etapa 2: examinar os pré-requisitos para replicação do VMware para o Azure

Leia os pré-requisitos resumidos na tabela seguinte.

**Pré-requisito** | **Detalhes**
--- | ---
**As tabelas** | Saiba mais sobre [requisitos do Azure](site-recovery-prereq.md#azure-requirements)
**Servidor de configuração local** | Você precisa de uma VM VMware que esteja executando o Windows Server 2012 R2 ou posterior. Você pode configurar este servidor durante a implantação de Site Recovery.<br/><br/> Por padrão, o servidor de processo e o servidor de destino mestre também são instalados nesta VM. Quando você escalar verticalmente, talvez seja necessário um servidor de processo separado; ele tem os mesmos requisitos que o servidor de configuração.<br/><br/> Saiba mais sobre esses componentes [aqui](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)
**Servidores VMware locais** | Um ou mais servidores VMware vSphere, executando 6.5, 6.0, 5.5, 5.1 com as últimas atualizações. Os servidores devem estar localizados na mesma rede que o servidor de configuração (ou servidor de processo separado).<br/><br/> Recomendamos um servidor vCenter para gerenciar hosts, executando 6.5, 6.0 ou 5.5 com as atualizações mais recentes.
**VMs locais** | As VMs que você deseja replicar devem estar executando um [sistema operacional com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) e estar de acordo com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). A VM deve ter ferramentas VMware em execução.
**URLs** | O servidor de configuração precisa acessar estas URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se tiver regras de firewall baseadas no endereço IP, verifique se elas permitem a comunicação com o Azure.<br/></br> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/></br> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).<br/><br/> Baixe o MySQL desta URL: .http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi
**Serviço de mobilidade** | Instalado em todas as VMs replicadas.




## <a name="limitations"></a>Limitações

Confirme se você compreende as limitações resumidas na tabela antes de implantar.

**Limitação** | **Detalhes**
--- | ---
**As tabelas** | Contas de armazenamento e rede devem estar na mesma região que o cofre<br/><br/> Se usar uma conta de armazenamento premium, você também precisará de uma conta de armazenamento padrão para armazenar logs de replicação<br/><br/> Você não pode replicar contas premium no Sul e no Centro da Índia.
**Servidor de configuração local** | O tipo de adaptador de VM VMware deve ser VMXNET3. Caso contrário, [instale esta atualização](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> O vSphere PowerCLI 6.0 deve estar instalado.<br/><br> O computador não deve ser um controlador de domínio. O computador deve ter um endereço IP estático.<br/><br/> O nome do host deve ter 15 caracteres ou menos, e o sistema operacional deve estar em inglês.
**VMware** | O Site Recovery não dá suporte aos novos recursos do vCenter e vSphere 6.5 e 6.0, como o vCenter vMotion cruzado, os volumes virtuais e o DRS de armazenamento.
**VMs** | Verifique [Limitações de VM do Azure](site-recovery-prereq.md#azure-requirements)<br/><br/> Você não pode replicar máquinas virtuais com discos criptografados ou máquinas virtuais com inicialização EFI/UEFI.<br/><br> Não há suporte para clusters de disco compartilhado. Se a VM de origem tiver agrupamento NIC é feita a conversão para uma única NIC após o failover.<br/><br/> Se as VMs tiverem um disco iSCSI, a Site Recovery o converterá em um arquivo VHD após o failover. Se o destino iSCSI puder ser acessado pela VM do Azure, ela se conectará a ele e o verá juntamente com o VHD. Se isso acontecer, desconecte o destino de iSCSI.<br/><br/> Se desejar habilitar a consistência de várias VMs, o que habilita VMs que executam a mesma carga de trabalho a serem recuperadas juntas em um ponto de dados consistente, abra a porta 20004 na VM.<br/><br/> O Windows deve estar instalado na unidade C. O disco do SO deve ser básico e não dinâmico. O disco de dados pode ser dinâmico.<br/><br/> Os arquivos Linux /etc/hosts nas VMs devem conter entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede. O nome do host, os pontos de montagem, o nome do dispositivo e os caminhos do sistema e nomes de arquivos (/etc/; /usr) devem ser somente em inglês.<br/><br/> Tipos específicos de [armazenamento do Linux](site-recovery-support-matrix-to-azure.md#support-for-storage) têm suporte.<br/><br/>Crie ou defina **disk.enableUUID=true** nas configurações de VM. Isso fornece um UUID consistente para o VMDK, para que ele seja montado corretamente, e garante que apenas as alterações delta sejam transferidas para o local durante o failback, sem replicação completa.


## <a name="next-steps"></a>Próximas etapas

- Se estiver fazendo uma implantação completa, vá para a [Etapa 3: planejar a capacidade](vmware-walkthrough-capacity.md)
- Se estiver fazendo uma implantação de teste simples, vá para a [Etapa 4: Planejar a rede](vmware-walkthrough-network.md).

