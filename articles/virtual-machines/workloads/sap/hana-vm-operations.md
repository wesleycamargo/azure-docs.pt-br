---
title: Operações do SAP HANA no Azure | Microsoft Docs
description: Guia de operações para sistemas SAP HANA que são implantados em máquinas virtuais (VMs) do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/13/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0cb715960a516c6b2ca16376c12cb6f796e0b395
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA no guia de operações do Azure
Este guia fornece orientações para a operação de sistemas SAP HANA que foram implantados em máquinas virtuais nativas do Azure. Este documento não pretende substituir a documentação padrão do SAP, que inclui o seguinte conteúdo:

- [Guia de administração do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de instalação do SAP](https://service.sap.com/instguides)
- [Observações sobre o SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>pré-requisitos
Para usar este guia, você precisa ter um conhecimento básico dos seguintes componentes do Azure:

- [Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Rede do Azure e redes virtuais](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Armazenamento do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Para saber mais sobre o SAP NetWeaver e outros componentes SAP no Azure, consulte a seção [SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) na [documentação do Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerações básicas sobre a instalação
As seções a seguir descrevem considerações de configuração básica para implantar sistemas SAP HANA em máquinas virtuais do Azure.

### <a name="connect-into-azure-virtual-machines"></a>Conecte-se em máquinas virtuais do Azure
Conforme documentado no [guia de planejamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), há dois métodos básicos para se conectar em máquinas virtuais do Azure:

- Conexão pela Internet e por pontos de extremidade públicos em uma VM de atalho ou na VM que executa o SAP HANA.
- Conexão por meio de uma [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou do Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Uma conectividade site a site por meio de VPN ou rota expressa é necessária para cenários de produção. Esse tipo de conexão também é necessário para cenários de não produção que alimentam em cenários de produção em que o software SAP está sendo usado. A imagem a seguir mostra um exemplo de conectividade entre sites:

![Conectividade entre sites](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Escolha os tipos de VM do Azure
Os tipos de VM do Azure que podem ser usados para cenários de produção são listados na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, você pode escolher uma variedade maior de VMs nativas do Azure.

>[!NOTE]
> Para cenários de não produção, use os tipos de VM que são listados na [nota da SAP nº 1928533](https://launchpad.support.sap.com/#/notes/1928533). Para o uso de VMs do Azure para cenários de produção, verifique as VMs certificadas para SAP HANA na [lista de plataformas de IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Implante as VMs no Azure usando:

- O portal do Azure.
- Cmdlets do Azure PowerShell.
- A CLI do Azure.

Você também pode implantar uma plataforma completa de SAP HANA instalada nos serviços de VM do Azure por meio da [plataforma SAP Cloud](https://cal.sap.com/). O processo de instalação está descrito em [Implantar SAP S/4HANA ou BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) ou com a automação liberada [aqui](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Escolha o tipo de armazenamento do Azure
O Azure fornece dois tipos de armazenamento principais adequados a máquinas virtuais do Azure que executam o SAP HANA:

- [Armazenamento Standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Armazenamento Premium do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

O Azure oferece dois métodos de implantação para VHDs nos Armazenamentos do Azure Standard e Premium. Se o cenário geral permitir, aproveite as implantações do [Azure Managed disk](https://azure.microsoft.com/services/managed-disks/).

Para obter uma lista de tipos de armazenamento e os respectivos SLAs em IOPS e taxa de transferência de armazenamento, revise a [documentação do Azure para discos gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Configurar o armazenamento para máquinas virtuais do Azure

Até o momento, você comprou dispositivos do SAP HANA para local e nunca precisou preocupar-se com os subsistemas de E/S e seus recursos porque o fornecedor do dispositivo precisa certificar-se de que os requisitos mínimos de armazenamento são atendidos para o SAP HANA. Como você mesmo compila a infraestrutura do Azure, também deve estar ciente de alguns desses requisitos para também reconhecer os requisitos de configuração que serão recomendados nas seções a seguir. Ou para casos em que você esteja configurando as Máquinas Virtuais nas quais deseja executar o SAP HANA. Algumas das características solicitadas resultam na necessidade de:

- Habilitar o volume de gravação/leitura em /hana/log de no mínimo 250 MB/s com tamanhos de E/S de 1 MB
- Habilitar a atividade de leitura de pelo menos 400MB/seg para /hana/data para tamanhos de E/S de 16 MB e 64 MB
- Habilitar a atividade de gravação de pelo menos 250 MB/s para /hana/data com tamanhos de E/S de 16 MB e 64 MB

Como a baixa latência de armazenamento é crítica para sistemas DBMS, mesmo aqueles, como o SAP HANA, mantêm dados na memória. O caminho crítico no armazenamento é geralmente em torno das gravações de log de transações dos sistemas DBMS. Mas também operações como gravar pontos de salvamento ou carregar dados na memória após a recuperação de falhas poderão ser críticas. Portanto, é obrigatório aproveitar os Discos Premium do Azure para os volumes /hana/data e /hana/log. Para obter a taxa de transferência mínima de /hana/log e /hana/data conforme desejado pelo SAP, é necessário compilar um RAID 0 utilizando MDADM ou LVM em vários discos do Armazenamento Premium do Azure e usar os volumes RAID como volumes /hana/data e /hana/log. Com tamanhos de faixa para RAID 0, o recomendável é usar:

- 64 K ou 128 K para /hana/data
- 32K para /hana/log

> [!NOTE]
> Não é necessário configurar nenhum nível de redundância usando volumes RAID, pois o armazenamento Premium e Standard do Azure mantém três imagens de um VHD. O uso de um volume RAID é puramente para configurar volumes que fornecem rendimento de E/S suficiente.

A acumulação de vários VHDs do Azure sob um RAID é cumulativa de um lado de taxa de transferência de IOPS e armazenamento. Portanto, se você colocar um RAID 0 em discos de Armazenamento Premium do Azure de 3 x P30, ele deverá fornecer três vezes a IOPS e três vezes a taxa de transferência de armazenamento de um único disco P30 do Armazenamento Premium do Azure.

Não configure o cache do Armazenamento Premium nos discos usados para /hana/data e /hana/log. Todos os discos que compilam esses volumes devem ter o cache desses discos definidos como 'Nenhum'.

Considere a taxa de transferência de E/S geral ao dimensionar ou escolher uma VM. A taxa de transferência de armazenamento de VM geral está documentada no artigo [Tamanhos da máquina virtual otimizada para memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Configuração com consciência de custos do Armazenamento do Microsoft Azure
A tabela a seguir mostra uma configuração de tipos de VM que os clientes geralmente utilizam para hospedar o SAP HANA em VMs do Azure. Pode haver alguns tipos de VM que talvez não atendam a todos os critérios mínimos para o SAP HANA. Mas até o momento essas VMs parecem funcionar adequadamente para cenários de não produção. 

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).



| SKU da VM | RAM | Máx. VM E/S<br /> Throughput | hana/data e hana/log<br /> distribuídos com LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 GB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Os discos recomendados para os tipos menores de VM com 3 x P20 ultrapassam os volumes de recomendação de espaço conforme o [Whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto a opção conforme exibida na tabela foi feita para fornecer a taxa suficiente de transferência de disco para o SAP HANA. Se for necessário realizar alterações no volume / hana/backup, que foi dimensionado para manter backups que representam o dobro do volume da memória, aproveite para fazer os ajustes.   
Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atenderá à carga de trabalho que você quer executar. Se a carga de trabalho exigir volumes maiores para /hana/data e /hana/log, será necessário aumentar o número de VHDs do Armazenamento Premium do Azure. Dimensionar um volume com mais VHDs do que o listado aumentará a taxa de transferência de E/S e IOPS dentro dos limites do tipo de máquina virtual do Azure. 

> [!NOTE]
> As configurações acima NÃO seriam benefício de [SLA de única VM de máquina virtual do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), pois ele utiliza uma combinação do Armazenamento Premium do Azure e Armazenamento Standard do Azure. No entanto, a seleção foi escolhida para otimizar os custos.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Configuração de Armazenamento do Azure para benefício de cumprimento de SLA de única VM
Se você quiser o benefício de [SLA de única VM de máquina virtual do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), será necessário utilizar exclusivamente VHDs de Armazenamento Premium do Azure.

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| SKU da VM | RAM | Máx. VM E/S<br /> Throughput | hana/data e hana/log<br /> distribuídos com LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 GB | 1000 MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


Os discos recomendados para os tipos menores de VM com 3 x P20 ultrapassam os volumes de recomendação de espaço conforme o [Whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto a opção conforme exibida na tabela foi feita para fornecer a taxa suficiente de transferência de disco para o SAP HANA. Se for necessário realizar alterações no volume / hana/backup, que foi dimensionado para manter backups que representam o dobro do volume da memória, aproveite para fazer os ajustes.  
Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atenderá à carga de trabalho que você quer executar. Se a carga de trabalho exigir volumes maiores para /hana/data e /hana/log, será necessário aumentar o número de VHDs do Armazenamento Premium do Azure. Dimensionar um volume com mais VHDs do que o listado aumentará a taxa de transferência de E/S e IOPS dentro dos limites do tipo de máquina virtual do Azure. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solução de armazenamento com Acelerador de Gravação do Azure para máquinas virtuais da Série M do Azure
O Acelerador de Gravação do Azure é uma funcionalidade que está sendo implementada exclusivamente para as VMs da Série M. Como o nome indica, o objetivo da funcionalidade é melhorar a latência de E/S das gravações no Armazenamento Premium do Azure. Para o SAP HANA, o Acelerador de Gravação deve ser utilizado somente no volume /hana/log. Portanto, as configurações mostradas até o momento precisam ser alteradas. A principal alteração é a divisão entre /hana/data e /hana/log para usar o Acelerador de Gravação do Azure somente no volume /hana/log. 

> [!IMPORTANT]
> A certificação do SAP HANA para máquinas virtuais da Série M do Azure é exclusivamente com o Acelerador de Gravação do Azure para o volume /hana/log. Como resultado, espera-se que as implantações do SAP HANA do cenário de produção nas máquinas virtuais da Série M do Azure sejam configuradas com o Acelerador de Gravação do Azure para o volume /hana/log.  

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

As configurações recomendadas são semelhantes a:

| SKU da VM | RAM | Máx. VM E/S<br /> Throughput | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M64s | 1000 GB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atenderá à carga de trabalho que você quer executar. Se a carga de trabalho exigir volumes maiores para /hana/data e /hana/log, será necessário aumentar o número de VHDs do Armazenamento Premium do Azure. Dimensionar um volume com mais VHDs do que o listado aumentará a taxa de transferência de E/S e IOPS dentro dos limites do tipo de máquina virtual do Azure.

O Acelerador de Gravação do Azure funciona somente em conjunto com o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Portanto, pelo menos, os discos do Armazenamento Premium do Azure que formam o volume /hana/log precisam ser implantados como discos gerenciados.

Há limites de VHDs de Armazenamento Premium do Azure por VM que podem ter suporte pelo Acelerador de Gravação do Azure. Os limites atuais são:

- 16 VHDs para uma VM M128xx
- 8 VHDs para uma VM M64xx

Instruções mais detalhadas sobre como habilitar o Acelerador de Gravação do Azure podem ser encontradas no artigo [Acelerador de Gravação do Azure para implantações do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/how-to-enable-write-accelerator).

Detalhes e restrições para o Acelerador de Gravação do Azure podem ser localizados na mesma documentação.


### <a name="set-up-azure-virtual-networks"></a>Configurar redes virtuais do Azure
Quando você tiver conectividade site a site no Azure por meio de VPN ou ExpressRoute, você deverá ter pelo menos uma [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) que esteja conectada por meio de um Gateway Virtual ao VPN ou circuito ExpressRoute. O Gateway Virtual reside em uma sub-rede na rede virtual do Azure. Para instalar o SAP HANA, você deve criar duas sub-redes adicionais na rede virtual. Uma sub-rede hospeda as VMs para executar as instâncias do SAP HANA. A outra sub-rede executa as VMs do Jumpbox ou Gerenciamento para hospedar o SAP HANA Studio ou outros softwares de gerenciamento.

Quando você instala as VMs para executar o SAP HANA, as VMs necessitam de:

- Duas NICs virtuais instaladas: uma NIC para conectar-se à sub-rede de gerenciamento, e uma NIC para conectar-se da rede local ou de outras redes para a instância do SAP HANA na VM do Azure.
- Endereços IP privados estáticos que são implantados para as duas NICS virtuais.

Para obter uma visão geral dos diferentes métodos para atribuir endereços IP, consulte [Tipos de endereços IP e métodos de alocação no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Para VMs em execução no SAP HANA, é necessário trabalhar com endereços IP estáticos atribuídos. A razão é que alguns atributos de configuração para o HANA referenciam endereços IP.

Os [Grupos de Segurança de Rede (NSGs) do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são usados para direcionar o tráfego que é roteado para a instância do SAP HANA ou o Jumpbox. Os NSGs são associados para a sub-rede do HANA SAP e a sub-rede de gerenciamento.

A imagem a seguir mostra uma visão geral de um esquema aproximado de implantação para o SAP HANA:

![Esquema de implantação aproximado para o SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Para implantar o SAP HANA no Azure sem uma conexão site a site, acesse a instância do SAP HANA por meio de um endereço IP público. O endereço IP deve ser atribuído à VM do Azure que está executando a VM Jumpbox. Neste cenário básico, a implantação se baseia em serviços DNS internos do Azure para resolver nomes de host. Em uma implantação mais complexa, em que endereços IP públicos são usados, os serviços DNS internos do Azure são especialmente importantes. Use os NSGs do Azure para limitar as portas em aberto ou intervalos de endereços IP que podem se conectar em sub-redes com ativos que têm endereços IP públicos. A imagem a seguir mostra um esquema aproximado para a implantação do SAP HANA sem uma conexão site a site:
  
![Esquema de implantação aproximado para o SAP HANA sem conexão site a site](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operações de implantação do SAP HANA em VMs do Azure
As seções a seguir descrevem algumas das operações relacionadas à implantação de sistemas SAP HANA  em VMs do Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Operações de backup e restauração em VMs do Azure
Os documentos a seguir descrevem como fazer backup e restaurar a sua implantação do SAP HANA:

- [Visão geral de backup do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup em nível de arquivo do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Parâmetro de comparação de instantâneos de armazenamento do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Inicie e reinicie as VMs que contêm o SAP HANA
Uma característica importante da nuvem pública do Azure é que você será cobrado somente pelos seus minutos de computação. Por exemplo, quando você desligar uma VM que executa o SAP HANA, você será cobrado somente pelos custos de armazenamento durante aquele período. Outro recurso fica disponível quando você especificar endereços IP estáticos para as VMs na sua implantação inicial. Quando você reiniciar uma VM que contém o SAP HANA, a VM é reiniciada com seus endereços IP anteriores. 


### <a name="use-saprouter-for-sap-remote-support"></a>Use o SAProuter para suporte remoto SAP
Se você tem uma conexão site a site entre suas localizações locais e o Azure, e estiver executando componentes SAP, você provavelmente já está executando o SAProuter. Nesse caso, complete os seguintes itens para suporte remoto:

- Manter o endereço IP estático e privado da VM que hospeda o SAP HANA na configuração do SAProuter.
- Configure o NSG da sub-rede que hospeda a VM do HANA para permitir o tráfego pela porta TCP/IP 3299.

Se estiver conectando ao Azure através da Internet e não tiver um roteador SAP para a VM com SAP HANA, será necessário instalar o componente. Instale o SAProuter em uma VM separada na sub-rede de gerenciamento. A imagem a seguir mostra um esquema aproximado para a implantação do SAP HANA sem uma conexão site a site:

![Esquema de implantação aproximado para o SAP HANA sem conexão site a site e SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Certifique-se de instalar o SAProuter em uma VM separada e não em sua VM Jumpbox. A VM separada precisa de um endereço IP estático. Para conectar o SAProuter ao SAProuter que é hospedado pela SAP, entre em contato com o SAP para um endereço IP. (O SAProuter que é hospedado pela SAP é a contraparte da instância do SAProuter que você instala em sua VM.) Use o endereço IP da SAP para configurar sua instância do SAProuter. Nas definições de configuração, a única porta necessária é a porta TCP 3299.

Para saber mais sobre como configurar e manter conexões de suporte remoto por meio do SAPRouter, confira a [ documentação do SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidade com o SAP HANA em VMs nativas do Azure
Se você estiver executando o SUSE Linux 12 SP1 ou posterior, você pode estabelecer um cluster Pacemaker com dispositivos STONITH. Você pode usar os dispositivos para definir a configuração do SAP HANA que usa replicação síncrona com a Replicação de sistema do HANA e failover automático. Para obter mais informações sobre o procedimento de configuração, consulte [Guia de Alta Disponibilidade do SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
