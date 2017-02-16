---
title: "Pré-requisitos de implantação do Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve os pré-requisitos para configurar a replicação com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 92abadf68e4acbd9daae9e15dcfdce53563f6eb2
ms.openlocfilehash: a407f5ebe096db74820ff93354a636bc63894b1d

---

# <a name="site-recovery-deployment-prerequisites"></a>Pré-requisitos de implantação do Site Recovery

As organizações precisam de uma estratégia de BCDR que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é Recuperação de Site?](site-recovery-overview.md)

Este artigo resume os pré-requisitos de implantação para cenários de replicação do Site Recovery.  

Poste comentários no final do artigo ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="azure-deployment-models"></a>Modelos de implantação do Azure

O Azure tem dois [modelos de implantação](../azure-resource-manager/resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: o modo Azure Resource Manager e o modelo clássico. O Azure também tem dois portais: o [portal clássico do Azure](https://manage.windowsazure.com/), que dá suporte ao modelo de implantação clássico, e o [Portal do Azure](https://ms.portal.azure.com/), com suporte aos dois modelos de implantação.

Novos cenários do Site Recovery devem ser implantados no [portal do Azure](https://portal.azure.com). Esse portal fornece novos recursos e uma experiência avançada de implantação. Os cofres podem ser mantidos no portal clássico, mas não é possível criar novos cofres.


## <a name="azure-account-requirements"></a>Requisitos de conta do Azure

**Requisito** | **Detalhes**
--- | --- 
**Conta do Azure** | Uma conta do [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site.


## <a name="azure-storage-requirements"></a>Requisitos de armazenamento do Azure

Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure são criadas quando ocorre failover.

**Requisito** | **Detalhes**
--- | --- 
[Conta de armazenamento do Azure](../storage/storage-introduction.md) | Você pode usar [GRS](../storage/storage-redundancy.md#geo-redundant-storage) ou o armazenamento LRS.<br/><br/> É recomendável usar GRS para que os dados sejam resilientes caso ocorra uma interrupção regional, ou se a região principal não puder ser recuperada. [Saiba mais](https://docs.microsoft.com/en-us/azure/storage/storage-redundancy)
**Portal do Azure** | No Portal do Azure, use o armazenamento do Resource Manager ou uma conta de armazenamento clássica.
**Armazenamento Premium** | O [Armazenamento Premium](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) terá suporte se você replicar VMs do VMware ou servidores físicos no Azure, usando o Portal do Azure.<br/><br/> O armazenamento Premium normalmente é usado para máquinas virtuais que precisam de um desempenho de E/S consistentemente alto e baixa latência para hospedar cargas de trabalho com uso intensivo de E/S.<br/><br/> Se você não usar o armazenamento premium, também precisará de uma conta de armazenamento padrão para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais.
**Limitações de armazenamento** | O portal clássico dava suporte apenas para GRS.<br/><br/> As contas de armazenamento criadas no portal do Azure não podem ser movidas entre grupos de recursos.<br/><br/> No momento, não há suporte para replicação para as contas de armazenamento premium na Índia Central e no Sul da Índia.

## <a name="azure-network-requirements"></a>Requisitos de rede do Azure

Você precisa de uma rede do Azure à qual as VMs do Azure criadas após o failover serão conectadas.

**Requisito** | **Detalhes**
--- | ---
**Região da rede** | A rede deve estar na mesma região do que o cofre.
**Portal do Azure** | No Portal do Azure, você pode usar uma rede clássica ou do Resource Manager.
**Mapeamento de rede** | Se você replicar do VMM para o Azure, você configurará o [mapeamento de rede](site-recovery-network-mapping.md) entre as redes de VM do VMM e as redes do Azure, para garantir que as VMs do Azure se conectem a redes apropriadas após o failover.


## <a name="vmware-replication-requirements-to-azure"></a>Requisitos de replicação do VMware (para o Azure)

**Componente** | **Requisito**
--- | ---
**Servidor de configuração** | Uma máquina física ou virtual local que executa o Windows Server 2012 R2. Todos os componentes locais do Site Recovery estão instalados neste computador.<br/><br/>Para replicação de VM VMware, recomendamos que você implante o servidor como uma VM VMware altamente disponível. <br/><br/>Se o servidor for uma VM VMware, o tipo de adaptador de rede deverá ser VMXNET3. Se você usar outro tipo de adaptador de rede, instale uma [atualização da VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) no servidor vSphere 5.5.<br/><br/>O servidor deve ter um endereço IP estático.<br/><br/>O servidor não deve ser um controlador de domínio.<br/><br/>O nome do host do servidor deve ter 15 caracteres ou menos.<br/><br/>O sistema operacional deve ser apenas em inglês.<br/><br/> Instale o servidor VMware vSphere PowerCLI 6.0. .<br/><br/>O servidor de configuração precisa de acesso à Internet. O acesso de saída é necessário conforme o seguinte:<br/><br/>Acesso temporário em HTTP 80 durante a instalação dos componentes do Site Recovery (para baixar o MySQL)<br/><br/>Acesso de saída em andamento em HTTPS 443 para gerenciamento de replicação<br/><br/>Acesso de saída em andamento em HTTPS 9443 para o tráfego de replicação (essa porta pode ser modificada)<br/><br/> Permita essa URL para o download do MySQL: ``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> O servidor também precisa acessar [estas URLs](#requirements-for-url-access)
**Servidor de processo** | Instalado por padrão no servidor de configuração<br/><br/> Atua como um gateway de replicação. Ele recebe dados de replicação de computadores de origem protegida, otimiza-os com caching, compactação e criptografia e envia os dados para o armazenamento do Azure. Ele também manipula a instalação por push do Serviço de mobilidade em computadores protegidos e executa a descoberta automática de máquinas virtuais VMware. À medida que a implantação cresce, você pode adicionar outros servidores de processo dedicados separados para lidar com o aumento do volume de tráfego de replicação.
**Servidor de destino mestre** | Instalado por padrão no servidor de configuração. Lida com os dados de replicação durante o failback do Azure.
**Hosts vSphere** | Um ou mais hipervisores do VMware vSphere.<br/><br/>Os hipervisores devem estar executando o vSphere versão 6.0, 5.5 ou 5.1 com as últimas atualizações.<br/><br/>Recomendamos que os hosts vSphere e os servidores vCenter estejam localizados na mesma rede do servidor em processo (essa é a rede na qual o servidor de configuração está localizado, a menos que você configurou um servidor em processo dedicado). 
**Servidores vCenter** | Recomendamos que você implante um servidor VMware vCenter para gerenciar os hosts vSphere. Ele deve executar o vCenter versão 6.0 ou 5.5, com as últimas atualizações.<br/><br/>**Limitação**: o Site Recovery não dá suporte aos novos recursos do vCenter e vSphere 6.0, como o vCenter vMotion cruzado, os volumes virtuais e o DRS de armazenamento. O suporte à Recuperação de Site está limitado a recursos que também estavam disponíveis na versão 5.5

### <a name="vmware-vm-requirements-to-azure"></a>Requisitos de VM do VMware (para Azure)

**Componente** | **Requisito**
--- | ---
**VMs VMware** | As VMs replicadas devem ter as ferramentas VMware instaladas e em execução.<br/><br/> Para que seja possível criar VMs do Azure, elas devem estar em conformidade com os [pré-requisitos do Azure](site-recovery-support-matrix.md#support-for-azure-vms).<br/><br/>A capacidade do disco individual nos computadores protegidos não deve ser maior que 1023 GB. Uma VM pode ter até 64 discos (portanto, até 64 TB). <br/><br/>Mínimo de 2 GB de espaço disponível na unidade de instalação para instalação de componentes.<br/><br/>**Limitação**: não há suporte para a proteção de VMs com discos criptografados.<br/><br/>**Limitação**: não há suporte para clusters convidados de disco compartilhado.<br/><br/>A **porta 20004** deverá ser aberta no firewall local da VM se você desejar habilitar a coerência de múltiplas VMs.<br/><br/>Não há suporte para computadores que têm inicialização UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface).<br/><br/>Os nomes dos computadores devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. Depois de habilitar a replicação em um computador, você poderá modificar o nome do Azure.<br/><br/>Se a VM de origem tiver agrupamento NIC, será feita a conversão para uma única NIC após o failover no Azure.<br/><br/>Se as máquinas virtuais protegidas tiverem um disco iSCSI, o Site Recovery converterá o disco iSCSI da VM protegida em um arquivo VHD quando a VM realizar failover no Azure. Se o destino iSCSI puder ser acessado pela VM do Azure, ela se conectará a ele e verá basicamente dois discos – o disco VHD na VM do Azure e o disco iSCSI de origem. Nesse caso, será necessário desconectar o destino iSCSI exibido na VM do Azure.
**VMs que executam o Windows** | As máquinas do Windows devem estar executando um sistema operacional de 64 bits com [suporte](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> O sistema operacional deve ser instalado na unidade C:\.<br/><br/> O disco do sistema operacional deve ser um disco básico do Windows e não dinâmico. O disco de dados pode ser dinâmico.<br/><br/> A Recuperação de Site dá suporte a VMs com um disco RDM. Durante o failback, o Site Recovery reutilizará o disco RDM se o disco RDM e a VM de origem original estiverem disponíveis. Se eles não estiverem disponíveis, durante o failback, o Site Recovery criará um novo arquivo VMDK para cada disco.
**VMs executando o Linux** | As máquinas do Linux devem estar executando um [sistema operacional com suporte](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Os arquivos /etc/hosts nos computadores protegidos devem conter entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.<br/><br/> O nome do host, os pontos de montagem, os nomes de dispositivos e os caminhos do sistema e nomes de arquivos do Linux (por exemplo: /etc/; /usr) devem ser somente em inglês.<br/><br/> O armazenamento deve [ter suporte](site-recovery-support-matrix.md#support-for-replicated-machines)<br/><br/> A Recuperação de Site dá suporte a VMs com um disco RDM.  Durante o failback para Linux, a Recuperação de Site não reutiliza o disco RDM. Em vez disso, ele cria um novo arquivo VMDK para cada disco RDM correspondente.<br/><br/> Certifique-se de definir a configuração disk.enableUUID=true nos parâmetros de configuração da VM no VMware. Se ela não existir, crie a entrada. Isso é necessário para fornecer um UUID consistente para o VMDK para que ele seja montado corretamente. Adicionar essa configuração também garante que as alterações delta somente sejam transferidas para o local durante o failback e não uma replicação completa.



## <a name="physical-servers-replication-requirements-to-azure"></a>Requisitos de replicação de servidores físicos (para o Azure)

**Componente** | **Requisito**
--- | ---
**Servidor de configuração** | Uma máquina física ou virtual local que executa o Windows Server 2012 R2. Todos os componentes locais do Site Recovery estão instalados neste computador.<br/><br/> Para a replicação de um computador físico, o computador pode ser um servidor físico.<br/><br/>  O servidor de configuração precisa de acesso à Internet. O acesso de saída é necessário conforme o seguinte:<br/><br/> Acesso temporário em HTTP 80 durante a instalação dos componentes do Site Recovery (para baixar o MySQL)<br/><br/>Acesso de saída em andamento em HTTPS 443 para gerenciamento de replicação<br/><br/>Acesso de saída em andamento em HTTPS 9443 para o tráfego de replicação (essa porta pode ser modificada)<br/><br/> Permita essa URL para o download do MySQL: ``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> O servidor também precisa acessar [estas URLs](#requirements-for-url-access)
**Failback** | O failback do Azure é sempre executado para VMs VMware, mesmo se você replicou de um servidor físico. Se você não implantar o servidor de configuração como uma VM VMware, configure uma VM do VMware para receber o tráfego de failback, antes do failback.

### <a name="physical-machine-requirements-to-azure"></a>Requisitos de máquina física (para Azure)

**Componente** | **Requisito**
--- | ---
**Servidores físicos que executam o Windows** | As máquinas do Windows devem estar executando um sistema operacional de 64 bits com [suporte](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> O sistema operacional deve ser instalado na unidade C:\.<br/><br/> O disco do sistema operacional deve ser um disco básico do Windows e não dinâmico. O disco de dados pode ser dinâmico.<br/><br/> A Recuperação de Site dá suporte a VMs com um disco RDM. Durante o failback, o Site Recovery reutilizará o disco RDM se o disco RDM e a VM de origem original estiverem disponíveis. Se eles não estiverem disponíveis, durante o failback, o Site Recovery criará um novo arquivo VMDK para cada disco.
**Servidores físicos que executam o Linux** | As máquinas do Linux devem estar executando um [sistema operacional com suporte](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Os arquivos /etc/hosts nos computadores protegidos devem conter entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.<br/><br/> O nome do host, os pontos de montagem, os nomes de dispositivos e os caminhos do sistema e nomes de arquivos do Linux (por exemplo: /etc/; /usr) devem ser somente em inglês.<br/><br/> O armazenamento deve [ter suporte](site-recovery-support-matrix.md#support-for-replicated-machines).

## <a name="hyper-v-replication-requirements-to-azure"></a>Requisitos de replicação do Hyper-V (para o Azure)

**Componente** | **Requisito**
--- | ---
**VMM (opcional)** | Opcionalmente, você pode replicar VMs em hosts do Hyper-V gerenciados em nuvens do VMM.<br/><br/> Se você não estiver usando o VMM, reúna um ou mais hosts do Hyper-V ou clusters em sites do Hyper-V e configure a replicação de um site.<br/><br/> Se você estiver usando o VMM, ele deve estar executando no System Center 2012 R2.<br/><br/> Se você estiver usando o VMM, cada servidor do VMM deve ter uma ou mais nuvens configuradas. Uma nuvem deve conter um ou mais grupos de host de VMM, e um ou mais servidores de host do Hyper-V ou clusters em cada grupo de hosts.<br/><br/> O servidor do VMM deve estar conectado à internet, com acesso às [URLs necessárias](#requirements-for-url-access).<br/><br/> Se você tiver regras de firewall baseadas em endereço IP no servidor VMM, verifique se as regras permitem a comunicação com o Azure.<br/><br/> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/><br/> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA.
**Hosts Hyper-V** | Os servidores host do Hyper-V devem estar executando pelo menos o **Windows Server 2012 R2** com a função Hyper-V ou o **Microsoft Hyper-V Server 2012 R2** e ter as atualizações mais recentes instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/>Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.<br/><br/>Os servidores Hyper-V devem ter as correções mencionadas no [artigo 2961977](https://support.microsoft.com/kb/2961977) instaladas.<br/><br/>Os servidores host do Hyper-V precisam de acesso à Internet para a replicação de dados no Azure, incluindo acesso às [URLs necessárias](#requirements-for-url-access). Além disso, permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/><br/> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA.<br/><br/> Se os hosts do Hyper-V estiverem em nuvens do VMM, instale a correção descrita no [KB 2961977](https://support.microsoft.com/en-us/kb/2961977)

### <a name="hyper-v-vm-requirements-to-azure"></a>Requisitos de VM do Hyper-V (para Azure)

**Componente** | **Requisito**
--- | ---
**Conformidade da VM** |Antes de fazer failover de uma VM, verifique se o nome que será atribuído à VM do Azure é compatível com os [pré-requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Você pode modificar o nome depois de habilitar a replicação para a VM.
**Disco** | A capacidade do disco individual nos computadores protegidos não deve ser maior que 1023 GB. Uma VM pode ter até 64 discos (portanto, até 64 TB).<br/><br/> Não há suporte para clusters convidados de disco compartilhado.
**UEFI** | Não há suporte para UEFI (Unified Extensible Firmware Interface)/inicialização EFI (Extensible Firmware Interface).
**Agrupamento NIC** | Se a VM de origem tiver agrupamento NIC, será feita a conversão para uma única NIC após o failover no Azure.
**Linux estático** | Não há suporte para a proteção de VMs Hyper-V que executam o Linux com um endereço IP estático.



## <a name="vmwarephysical-servers-replication-requirements-secondary-site"></a>Requisitos de replicação de servidores físicos/VMware (site secundário)

Esse cenário é manipulado pelo produto InMage, que pode ser baixado do cofre do Site Recovery quando você opta por replicar VMs do VMware ou servidores físicos em um local secundário. Observe que esse cenário não está disponível no Portal do Azure.

**Componente** | **Requisito**
--- | ---
**Site primário** | É necessário um servidor de processo para manipular o cache, a compactação e a otimização de dados. Esse servidor também lida com a instalação por push do agente unificado que é necessário em cada computador que você deseja replicar.<br/><br/> Se você quiser replicar VMs do VMware, é necessário um ou mais servidores VMware ESX/ESXi e, opcionalmente, um servidor VMware vCenter.
**Site secundário** | Você precisa de um servidor de configuração definido no site secundário, para configurar e gerenciar seu ambiente.<br/><br/> Um servidor de destino mestre está instalado no site secundário para manter os dados replicados.<br/><br/> Um servidor vContinuum é instalado por padrão no servidor de destino mestre e fornece um console para gerenciar e monitorar seu ambiente.

### <a name="vmware-vmphysical-machine-requirements-secondary-site"></a>Requisitos da máquina física/VM do VMware (site secundário)

Verifique os requisitos na [matriz de suporte do InMage](https://aka.ms/asr-scout-cm).


## <a name="hyper-v-replication-requirements-secondary-site"></a>Requisitos de replicação do Hyper-V (site secundário)

**Componente** | **Requisito**
--- | ---
**VMM** | Para replicar VMs do Hyper-V em um site secundário, o host Hyper-V deve ser gerenciado em nuvens do System Center VMM. <br/><br/> O VMM deve estar executando pelo menos o System Center 2012 SP1 com as atualizações mais recentes.<br/><br/> Recomendamos um servidor VMM no site primário, e outro servidor VMM no site secundário. Você pode replicar entre [nuvens diferentes no mesmo servidor VMM](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-single-vmm), mas a configuração manual será necessária nesse cenário.<br/><br/> O servidor do VMM deve estar conectado à internet, com acesso às [URLs necessárias](#requirements-for-url-access).<br/><br/> Se você tiver regras de firewall baseadas em endereço IP no servidor VMM, verifique se as regras permitem a comunicação com o Azure.<br/><br/> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/><br/> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA.
**Hyper-V** | Os servidores Hyper-V devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e ter as últimas atualizações instaladas.<br/><br/> O servidor Hyper-V deve conter uma ou mais VMs<br/><br/> Os hosts Hyper-V devem estar localizados em grupos de hosts nos servidores VMM primários e secundários.<br/><br/> Se você estiver executando o Hyper-V em um cluster no Windows Server 2012 R2, instale a [atualização 2961977](https://support.microsoft.com/kb/2961977). Se houver um cluster Hyper-V no Windows Server 2012, o agente de cluster não será criado automaticamente se você tiver um cluster baseado em endereço IP estático. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx) para configurar manualmente.

### <a name="hyper-v-vm-requirements-secondary-site"></a>Requisitos de VM do Hyper-V (site secundário)

**Componente** | **Detalhes**
--- | ---
**Nuvem do VMM** | As VMs devem estar localizadas no host Hyper-V em nuvens do VMM.






## <a name="url-access-requirements"></a>Requisitos de acesso da URL

Essas URLs devem estar disponíveis no VMware, no VMM e nos servidores host Hyper-V.

**URL** | **VMM no VMM** | **VMM para Azure** | **Hyper-V para Azure** | **VMware no Azure** 
--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | PERMITIR | Permitir | Permitir | Permitir
``*.backup.windowsazure.com`` | Não requerido | Permitir | Permitir | Permitir
``*.hypervrecoverymanager.windowsazure.com`` | Permitir | Permitir | Permitir | Permitir
``*.store.core.windows.net`` | Permitir | Permitir | Permitir | Permitir
``*.blob.core.windows.net`` | Não requerido | Permitir | Permitir | Permitir
``https://www.msftncsi.com/ncsi.txt`` | Permitir | Permitir | Permitir | Permitir
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Não requerido | Não requerido | Não requerido | Permitir download do SQL
``time.windows.com`` | PERMITIR | Permitir | Permitir | Permitir
``time.nist.gov`` | Permitir | Permitir | Permitir | PERMITIR

## <a name="failover-requirements"></a>Requisitos de failover

**Cenário** | **Teste** | **Planejado** | **Não planejado**
--- | --- | --- | ---
VMware no Azure | Suportado | Sem suporte | Suportado
Físico para Azure | Suportado | Sem suporte | Suportado
Hyper-V (VMM) para Azure | Suportado | Com suporte | Suportado
Hyper-V (sem VMM) para Azure | Suportado | Suportado | Sem suporte
Hyper-V (VMM/nuvem primária) para secundário | Suportado | Com suporte | Suportado

## <a name="failback-requirements"></a>Requisitos de failback

**Cenário** | **Teste** | **Planejado** | **Não planejado**
--- | --- | --- | ---
**Azure para VMware** | Sem suporte | Sem suporte | Suportado
**Azure para físico** | Sem suporte | Sem suporte | Suporte apenas para o VMware
**Azure para VMM** | Sem suporte | Com suporte | Sem suporte
**Azure para Hyper-V (sem VMM)** | Sem suporte | Com suporte | Sem suporte
**Hyper-V (VMM/nuvem secundária) para primário** | Suportado | Com suporte | Suportado

### <a name="failback-from-azure-to-vmware-vms"></a>Failback do Azure para VMs do VMware

**Componente** | **Detalhes**
--- | ---
**Servidor de processos temporário no Azure** | Se você deseja fazer failback do Azure após o failover, precisa criar uma VM do Azure configurada como um servidor de processo para lidar com a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
**Conexão VPN** | Em caso de failback, você precisará de uma conexão de VPN (ou ExpressRoute do Azure) configurada da rede do Azure para o site local.
**Servidor de destino mestre local** | O servidor de destino mestre local trata o failback. O servidor de destino mestre está instalado por padrão no servidor de gerenciamento, mas se você estiver fazendo failback de volumes de tráfego maiores, deverá configurar um servidor de destino mestre separado local com essa finalidade.



### <a name="failback-from-azure-to-physical-machines"></a>Failback do Azure para máquinas físicas

**Componente** | **Detalhes**
--- | ---
**Failback de físico para físico** | Não há suporte para isso. Isso significa que, se você realizar o failover de servidores físicos para o Azure e, depois, quiser realizar o failback, deverá realizar o failback para uma VM VMware. Você não pode realizar o failback para um servidor físico.
**VM** | Você precisa de uma VM do VMware local para realizar o failback.
**Servidor de configuração** | Se você não implantou o servidor de configuração local como uma VM do VMware, será necessário configurar um servidor de destino mestre separado como a VM do VMware. Isso é necessário porque o servidor de destino mestre interage e anexa no armazenamento VMware para restaurar os discos para uma VM VMware.



## <a name="deployment-optimization"></a>Otimização da implantação

Use as dicas a seguir para otimizar e escalar sua implantação.

- **Tamanho de volume do sistema operacional**: quando você replica uma máquina virtual no Azure, o volume do sistema operacional deve ser inferior a 1TB. Se você tiver mais volumes do que isso, poderá movê-los manualmente para um disco diferente antes de iniciar a implantação.
- **Tamanho do disco de dados**: se você estiver replicando para o Azure, poderá ter até 64 discos de dados em uma máquina virtual, cada uma com um máximo de 1 TB. Você pode replicar e executar failover de forma efetiva de uma máquina virtual de aproximadamente 64 TB.
- **Limites de plano de recuperação**: o Site Recovery pode ser ampliada para milhares de máquinas virtuais. Os planos de recuperação são projetados como um modelo para aplicativos que devem sofrer o failover juntos. Por isso, limitamos para 50 o número de máquinas em um plano de recuperação.
- **Limites de serviço do Azure**: toda assinatura do Azure vem com um conjunto de limites padrão de núcleos, serviços de nuvem etc. Recomendamos a execução de um failover de teste para validar a disponibilidade de recursos em sua assinatura. Você pode modificar esses limites por meio do suporte do Azure.
- **Planejamento de capacidade**: leia sobre o [planejamento de capacidade](site-recovery-capacity-planner.md) para o Site Recovery.
- **Largura de banda de replicação**: se você tiver pouca largura de banda de replicação, observe o seguinte:
- **ExpressRoute**: a Recuperação de Site funciona com o Azure ExpressRoute e com otimizadores de WAN, por exemplo, Riverbed. [Leia mais](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre o ExpressRoute.
- **Tráfego de replicação**: a Recuperação de Site realiza uma replicação inicial inteligente usando apenas os blocos de dados e não o VHD inteiro. Somente as alterações são replicadas durante a replicação contínua.
- **Tráfego de rede**: você pode controlar o tráfego de rede usado para replicação configurando o [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) com uma política baseada no endereço IP de destino e na porta.  Além disso, se você estiver replicando para o Azure Site Recovery usando o agente de Backup do Azure, poderá configurar a limitação desse agente. [Leia mais](https://support.microsoft.com/kb/3056159).
- **RTO**: para medir o RTO (Objetivo do Tempo de Recuperação) que você pode esperar com o Site Recovery, sugerimos executar um failover de teste e exibir os trabalhos do Site Recovery a fim de analisar o tempo até a conclusão das operações. Se você estiver efetuando o failover no Azure, para obter o melhor RTO, recomendamos a automatização de todas as ações manuais, integrando com os planos de recuperação e de automação do Azure.
- **RPO**: a Recuperação de Site oferece suporte a um objetivo de ponto de recuperação quase síncrono (RPO) quando você replica para o Azure. Isso pressupõe uma largura de banda suficiente entre seu armazenamento de dados e o Azure.



## <a name="next-steps"></a>Próximas etapas
Depois de examinar os requisitos de implantação gerais, leia os pré-requisitos detalhados e implante um cenário.

* [Replique as máquinas virtuais VMware para o Azure](site-recovery-vmware-to-azure.md)
* [Replicar servidores físicos para o Azure](site-recovery-vmware-to-azure.md)
* [Replicar servidor Hyper-V em nuvens do VMM para o Azure](site-recovery-vmm-to-azure.md)
* [Replicar máquinas virtuais Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicar máquinas virtuais do Hyper-V para um site secundário](site-recovery-vmm-to-vmm.md)
* [Replicar máquinas virtuais do Hyper-V para um site secundário com SAN](site-recovery-vmm-san.md)
* [Replicar máquinas virtuais do Hyper-V com um único servidor VMM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


