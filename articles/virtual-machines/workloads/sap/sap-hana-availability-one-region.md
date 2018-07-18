---
title: Disponibilidade do SAP HANA em uma região do Azure | Microsoft Docs
description: Descreve as operações do SAP HANA em VMs nativas do Azure em uma região do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47dba73a6c22d11953485a69435000d3d2fe6f55
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Disponibilidade do SAP HANA em uma região do Azure
Este artigo descreve vários cenários de disponibilidade em uma região do Azure. O Azure tem várias regiões, distribuídas em todo o mundo. Para obter a lista de regiões do Azure, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). Para implantar SAP HANA em VMs em uma região do Azure, a Microsoft oferece a implantação de uma única VM com uma instância do HANA. Para maior disponibilidade, você pode implantar duas VMs com duas instâncias do HANA dentro de um [Conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) use replicação de sistema do HANA para disponibilidade. 

Atualmente, o Azure está oferecendo uma versão prévia pública das [Zonas de disponibilidade do Azure (versão prévia)](https://docs.microsoft.com/azure/availability-zones/az-overview). Neste artigo, não descrevemos as zonas de disponibilidade em detalhes. Porém, incluímos uma discussão geral sobre como usar conjuntos de disponibilidade em comparação com zonas de disponibilidade.

Qual é a diferença entre um conjunto de disponibilidade e uma zona de disponibilidade no Azure? As regiões do Azure, onde as zonas de disponibilidade são oferecidas, têm vários datacenters. Os datacenters são independentes no fornecimento de fonte de alimentação, resfriamento e rede. O motivo da oferta de diferentes zonas dentro de uma única região do Azure é para que você possa implantar aplicativos em duas ou três zonas de disponibilidade oferecidas. Supondo que os problemas de fontes de alimentação ou rede afetem apenas uma infraestrutura de zona de disponibilidade, a implantação do aplicativo dentro de uma região do Azure permanecerá totalmente funcional se você utilizar diferentes zonas de disponibilidade. Pode ocorrer alguma redução de capacidade. Por exemplo, as VMs em uma zona podem ser perdidas, mas as VMs em duas outras zonas ainda estariam ativas e em execução. 
 
Um conjunto de disponibilidade do Azure é uma funcionalidade de agrupamento lógico que ajuda a garantir que os recursos da VM colocados no conjunto de disponibilidade sejam isolados uns dos outros em relação a falhas quando forem implantados em um datacenter do Azure. O Azure garante que as VMs colocadas em um conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Em algumas documentações do Azure, essa configuração é referida como posicionamentos em diferentes [domínios de atualização e de falha](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Esses posicionamentos normalmente estão dentro de um datacenter do Azure. Supondo que os problemas de fontes de alimentação ou rede afetem o datacenter que você está implantando, toda a sua capacidade em uma região do Azure será afetada.

O posicionamento dos data centers que representam as zonas de disponibilidade do Azure é um meio-termo entre a entrega de latência de rede entre serviços implantados em diferentes regiões que é aceitável para a maioria dos aplicativos e uma distância específica entre os data centers. Catástrofes naturais idealmente não afetariam o fornecimento e a infraestrutura de energia e rede para todas as zonas de disponibilidade nesta região. No entanto, como mostrado por catástrofes naturais monumentais, as zonas de disponibilidade nem sempre fornecem a disponibilidade desejada dentro de uma região. Pense no furacão Maria que atingiu a ilha de Porto Rico em 20 de setembro de 2017. O furacão basicamente causou um blecaute em quase 100 por cento da ilha de 144 km de largura.

## <a name="single-vm-scenario"></a>Cenário de VM individual

Em um cenário de VM individual, você deve criar uma VM do Azure para a instância do SAP HANA. Você usa o Armazenamento Premium do Azure para hospedar o disco do sistema operacional e todos os seus discos de dados. O SLA de tempo de atividade do Azure de 99,9% e os SLAs de outros componentes do Azure são suficientes para que você possa cumprir seu SLAs de disponibilidade para seus clientes. Nesse cenário, você não precisa utilizar um conjunto de disponibilidade do Azure para VMs que executem a camada de DBMS. Neste cenário, você conta com dois recursos diferentes:

- Reinicialização automática de VM do Azure (também conhecida como recuperação de serviço do Azure)
- Reinicialização automática de SAP HANA

A reinicialização automática de VM do Azure ou recuperação de serviço é uma funcionalidade no Azure que funciona em dois níveis:

- O host do servidor do Azure verifica a integridade de uma VM hospedada no host do servidor.
- O controlador de malha do Azure monitora a integridade e a disponibilidade do host do servidor.

Uma funcionalidade de verificação de integridade monitora a integridade de todas as VMs hospedadas em um host de servidor do Azure. Se uma VM entrar em um estado não íntegro, uma reinicialização da VM pode ser iniciada pelo agente de host do Azure que verifica a integridade da VM. O controlador de malha verifica a integridade do host, verificando muitos parâmetros diferentes que podem indicar problemas com o hardware do host. Ele também verifica a acessibilidade do host por meio da rede. Uma indicação de problemas com o host pode levar aos seguintes eventos:

- Se o host sinalizar um estado de integridade ruim, uma reinicialização do host e uma reinicialização das VMs que estavam em execução no host.
- Se o host não estiver em um estado íntegro após a reinicialização, uma reinicialização do host e uma reinicialização das VMs que foram hospedadas originalmente no host em um host íntegro. Nesse caso, o host é marcado como não íntegro. Ele não será usado para outras implantações até que seja limpo ou substituído.
- Se o host não íntegro tiver problemas durante o processo de reinicialização, uma reinicialização imediata das VMs em um host íntegro. 

Com o monitoramento do host e da VM fornecido pelo Azure, as VMs do Azure que apresentam problemas de host são automaticamente reiniciadas em um host íntegro do Azure. 

O segundo recurso com o qual você pode contar nesse cenário é o fato de que o serviço HANA que é executado em uma VM reinicializada é iniciado automaticamente após a reinicialização da VM. Você pode configurar a [Reinicialização automática do serviço HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) por meio dos serviços de watchdog dos diversos serviços HANA.

Você pode melhorar esse cenário de VM única adicionando um nó de failover passivo para uma configuração de SAP HANA. Na documentação do SAP HANA, essa configuração é chamada de [failover automático de host](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Essa configuração pode fazer sentido em uma situação de implantação local na qual o hardware do servidor é limitado e um nó de servidor único é dedicado como nó de Failover automático do host para um conjunto de hosts de produção. Mas no Azure, quando a infraestrutura subjacente do Azure fornece um servidor de destino íntegro para uma reinicialização bem-sucedida da VM, não faz sentido implantar failover automático do host do SAP HANA. Devido a isso, não temos nenhuma arquitetura de referência que preveja um nó em espera para o failover automático do host do HANA. Isso também se aplica a configurações de expansão do SAP HANA.

## <a name="availability-scenarios-for-two-different-vms"></a>Cenários de disponibilidade para duas VMs diferentes

Se você usar duas VMs do Azure em um conjunto de disponibilidade do Azure, você poderá aumentar o tempo de atividade entre essas duas VMs se elas forem colocadas em um Conjunto de disponibilidade do Azure dentro de uma região do Azure. A configuração básica no Azure seria semelhante a:

![Diagrama de duas VMs com todas as camadas](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Para ilustrar os diferentes cenários de disponibilidade, algumas das camadas no diagrama são omitidas. O diagrama mostra somente camadas que descrevem as máquinas virtuais, hosts, conjuntos de disponibilidade e regiões do Azure. Instâncias de Rede Virtual do Azure, grupos de recursos e assinaturas não têm função nos cenários descritos nesta seção.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replicar backups para uma segunda máquina virtual

Uma das configurações mais rudimentares é o uso de backups. Em particular, você pode ter backups de logs de transações enviados de uma VM para a outra VM do Azure. Você pode escolher o tipo de Armazenamento do Azure. Nessa configuração, você é responsável por gerar script da cópia dos backups agendados realizados na primeira VM para a segunda VM. Se você precisar usar as instâncias da segunda VM, você precisa restaurar os backups totais, incrementais/diferenciais e de log de transação até o ponto que precisa. 

A arquitetura é semelhante a:

![Diagrama de duas VMs com a replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Essa configuração não é adequada para alcançar excelentes tempos de RPO (objetivo de ponto de recuperação) e RTO (objetivo de tempo de recuperação). Os tempos RTO seriam especialmente afetados devido à necessidade de restaurar completamente todo o banco de dados usando os backups copiados. No entanto, essa configuração é útil para recuperação após a exclusão não intencional de dados nas instâncias principais. Com essa configuração, é possível a qualquer momento restaurar para um determinado ponto no tempo, extrair os dados e importar os dados excluídos para sua instância principal. Portanto, pode fazer sentido usar um método de cópia de backup em combinação com outros recursos de alta disponibilidade. 

Enquanto os backups estão sendo copiados, você poderá usar uma VM menor do que a VM principal em que o SAP HANA está sendo executado. Tenha em mente que você pode anexar um número menor de VHDs em VMs menores. Para obter informações sobre os limites de tipos de VM individuais, verifique [Tamanhos de máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replicação de sistema do SAP HANA sem failover automático

Os cenários descritos nesta seção usam a replicação de sistema do SAP HANA. Para obter a documentação do SAP, consulte [Replicação do sistema](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Duas VMs do Azure em uma única região do Azure têm configurações diferentes, portanto, há algumas diferenças no RTO. Em geral, cenários sem failover automático podem não se aplicar especificamente às VMs em uma região do Azure. Isso ocorre porque na maioria das falhas na infraestrutura do Azure, a recuperação de serviço do Azure reinicia a VM em outro host. Há alguns casos extremos em que essa configuração pode ajudar em termos de cenários de falha. Ou, em alguns casos, um cliente talvez queira obter mais eficiência.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>A replicação de sistema do SAP HANA sem failover automático e sem pré-carregamento de dados

Nesse cenário, você pode usar a replicação de sistema SAP HANA para mover dados de forma síncrona para alcançar um RPO de 0. Por outro lado, você tem tempo suficiente de RTO e não precisa fazer failover ou pré-carregamento de dados no cache de instância do HANA. Nesse caso, é possível obter ainda mais economia em sua configuração por meio das seguintes ações:

- Execute outra instância do SAP HANA na segunda VM. A instância do SAP HANA na segunda VM usa a maior parte da memória da máquina virtual. Geralmente, isso é para casos em que ocorra um failover para a segunda VM. Você pode desligar a segunda VM de forma que os dados replicados possam ser carregados no cache da instância do HANA de destino na segunda VM.
- Use um tamanho menor de VM na segunda VM. Se ocorrer um failover, você terá uma etapa adicional antes do failover manual. Nesta etapa, você vai redimensionar a VM ao tamanho da VM de origem. Esse cenário se assemelha a:

![Diagrama de duas VMs com a replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Mesmo que você não use o pré-carregamento de dados no destino de replicação do sistema do HANA, é necessário pelo menos de 64 GB de memória. Você também precisa de memória suficiente além de 64 GB para manter os dados de rowstore na memória da instância de destino.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>A replicação de sistema do SAP HANA sem failover automático e com pré-carregamento de dados

Nesse cenário, os dados replicados para a instância do HANA na segunda VM são pré-carregados. Isso elimina as duas vantagens de não pré-carregar dados. Nesse caso, você não pode executar outro sistema SAP HANA na segunda VM. Você também não pode usar uma VM de tamanho menor. Portanto, os clientes raramente implementam esse cenário.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replicação de sistema do SAP HANA com failover automático

Na configuração de disponibilidade padrão e mais comum dentro de uma região do Azure, duas VMs do Azure executando o Linux SLES tem um cluster de failover definido. O cluster do Linux SLES é baseado na estrutura [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) em conjunto com um dispositivo [STONITH](http://linux-ha.org/wiki/STONITH). 

Do ponto de vista do SAP HANA, o modo de replicação usado é sincronizado e um failover automático é configurado. A segunda VM, a instância do SAP HANA age como nó em espera ativa. O nó em espera recebe um fluxo síncrono de registros de alteração da instância primária do SAP HANA. Como as transações são confirmadas pelo aplicativo no nó primário do HANA, o nó primário do HANA espera para confirmar para o aplicativo até a confirmação de que o nó secundário do SAP HANA recebeu o registro de confirmação. O SAP HANA oferece dois diferentes modos de replicação síncrona. Para obter detalhes e para uma descrição das diferenças entre esses dois modos de replicação síncrona, consulte o artigo do SAP [Modos de replicação para a replicação do sistema do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

A configuração geral é da seguinte maneira:

![Diagrama de duas VMs com a replicação de armazenamento e failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Você pode escolher essa solução porque ela permite que você alcance um RPO = 0 e um RTO extremamente baixo. Configure a conectividade de cliente do SAP HANA de modo que os clientes do SAP HANA usem o endereço IP virtual para conectar-se à configuração de replicação de sistema do HANA. Isso elimina a necessidade de reconfigurar o aplicativo se ocorrer um failover para o nó secundário. Nesse cenário, os SKUs de VM do Azure para as VMs primária e secundária devem ser o mesmo.

## <a name="next-steps"></a>Próximas etapas

Para obter orientação passo a passo sobre como definir essas configurações no Azure, consulte:

- [Configurar a replicação de sistema do SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [Alta disponibilidade para SAP HANA usando replicação de sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Para obter mais informações sobre a disponibilidade do SAP HANA em regiões do Azure, consulte:

- [Disponibilidade do SAP HANA entre regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

