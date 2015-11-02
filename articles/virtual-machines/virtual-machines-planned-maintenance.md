<properties
	pageTitle="Manutenção planejada para VMs do Azure | Microsoft Azure"
	description="Compreenda o que é a manutenção planejada do Azure e como ela afeta suas máquinas virtuais em execução no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="kenazk"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="kenazk"/>


# Manutenção planejada para máquinas virtuais do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Por que o Azure realiza manutenção planejada

O Microsoft Azure realiza atualizações periodicamente em todo o mundo para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host subjacente a máquinas virtuais. Muitas dessas atualizações são realizadas sem nenhum impacto sobre as máquinas virtuais ou os Serviços de Nuvem, incluindo atualizações que preservam a memória.

Porém, algumas dessas atualizações exigem reinicialização das máquinas virtuais para que as atualizações necessárias à infraestrutura sejam aplicadas. As máquinas virtuais serão desligadas enquanto corrigimos a infraestrutura e, em seguida, elas serão reiniciadas.

Existem dois tipos de manutenção que podem afetar a disponibilidade das máquinas virtuais: planejada e não planejada. Esta página descreve como o Microsoft Azure realiza a manutenção planejada. Para obter mais informações sobre a manutenção não planejada, consulte [Compreender manutenção planejada X manutenção não planejada](virtual-machines-manage-availability.md).

## Atualizações que preservam a memória

Para uma classe de atualizações no Microsoft Azure, os clientes não sentirão qualquer impacto em suas máquinas virtuais em execução. Muitas dessas atualizações são componentes ou serviços que podem ser atualizados sem interferir na instância em execução. Algumas são atualizações de infraestrutura de plataforma no sistema operacional do host, que podem ser aplicadas sem exigir uma reinicialização completa das máquinas virtuais.

Essas atualizações são realizadas com a tecnologia que permite a migração ao vivo (uma atualização para "preservar a memória"). Durante a atualização, a máquina virtual é colocada em estado de "pausa", preservando a memória RAM, enquanto o sistema operacional subjacente do host recebe as atualizações e os patches necessários. A máquina virtual é retomada após 30 segundos no estado de pausa. Após ser reiniciada, o relógio da máquina virtual será sincronizado automaticamente.

Nem todas as atualizações podem ser implantadas usando esse mecanismo, mas, dado o breve período de pausa, a implantação de atualizações dessa maneira reduz muito o impacto nas máquinas virtuais.

Para máquinas virtuais de múltiplas instâncias (em um conjunto de disponibilidade), essas atualizações são aplicadas em um domínio por vez.

## Configurações de máquina virtual

Existem dois tipos de configurações de máquina virtual: instâncias múltiplas e instância única. Em uma configuração de várias instâncias, as máquinas virtuais semelhantes são colocadas em um conjunto de disponibilidade.

A configuração de várias instâncias oferece redundância e é recomendada para garantir a disponibilidade do aplicativo. Todas as máquinas virtuais do conjunto de disponibilidade devem ser praticamente idênticas e ter a mesma finalidade do aplicativo.

Para saber mais sobre como configurar as máquinas virtuais para alta disponibilidade, consulte [Gerenciar a disponibilidade de máquinas virtuais](virtual-machines-manage-availability.md).

Por outro lado, uma configuração de instância única é usada para máquinas virtuais autônomas que não são colocadas em um conjunto de disponibilidade. Essas máquinas virtuais não se qualificam para o contrato de nível de serviço (SLA), que exige duas ou mais máquinas virtuais sejam implantadas no mesmo conjunto de disponibilidade.

Para saber mais sobre SLAs, consulte a seção "Serviços de Nuvem, máquinas virtuais e rede virtual" dos [Contratos de Nível de Serviço](http://azure.microsoft.com/support/legal/sla/).


## Atualizações de configuração de várias instâncias

Durante a manutenção planejada, a plataforma Windows Azure primeiro atualiza o conjunto de máquinas virtuais que estão hospedadas em uma configuração de várias instâncias. Isso gera uma reinicialização para essas máquinas virtuais.

Em uma configuração de várias instâncias, as máquinas virtuais são atualizadas de maneira a preservar a disponibilidade ao longo de todo o processo, pressupondo-se que cada máquina ofereça uma função semelhante à função das outras no conjunto.

Para cada máquina virtual em seu conjunto de disponibilidade, será atribuído um domínio de atualização e domínio de falha pela plataforma subjacente do Azure. Cada domínio de atualização é um grupo de máquinas virtuais que serão reinicializadas na mesma janela de tempo. Cada domínio de falha é um grupo de máquinas virtuais que compartilham uma mesma fonte de energia e um mesmo comutador de rede.

Para saber mais sobre domínios de atualização e domínios de falha, consulte [Configurar várias máquinas virtuais em um conjunto de disponibilidade para redundância](virtual-machines-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Para impedir que os domínios de atualização fiquem offline ao mesmo tempo, a manutenção é executada, desligando cada máquina virtual em um domínio de atualização, aplicando a atualização para as máquinas de host, reiniciando as máquinas virtuais e passando para o próximo domínio de atualização. O evento de manutenção planejada termina depois que todos os domínios de atualização tiverem sido atualizados.

A ordem dos domínios de atualização que estão sendo reinicializados não pode continuar em sequência durante a manutenção planejada, mas somente um domínio de atualização é reinicializado por vez. Atualmente, o Azure envia notificações de manutenção planejada de máquinas virtuais com 48 horas de antecedência na configuração de várias instâncias.

Depois que uma máquina virtual é restaurada, aqui está um exemplo do que pode exibir o Visualizador de Eventos do Windows:

<!--Image reference-->
![][image2]

## Atualizações de configuração de instância única

Depois de concluir as atualizações de configuração de várias instâncias, o Azure executará atualizações de configuração de instância única. Essa atualização causará uma reinicialização das máquinas virtuais que não estão em execução nos conjuntos de disponibilidade.

Observe que mesmo se você tiver apenas uma instância em execução em um conjunto de disponibilidade, a plataforma do Azure tratará isso como uma atualização de configuração de várias instâncias.

Para máquinas virtuais em uma configuração de instância única, as máquinas virtuais são atualizadas desligando-se as máquinas virtuais, aplicando-se a atualização à máquina de host e reiniciando-se as máquinas virtuais. Essas atualizações são executadas em todas as máquinas virtuais de uma região em uma única janela de manutenção.

Esse evento de manutenção planejada afetará a disponibilidade do aplicativo para esse tipo de configuração de máquina virtual. O Azure oferece notificações de manutenção planejada de máquinas virtuais com uma semana de antecedência na configuração de instância única.

### Notificação por email

Somente para máquinas virtuais de instância única ou de múltiplas instâncias, o Azure enviar um email de comunicação prévio para alertar você sobre a próxima manutenção planejada, (com uma semana de antecedência para instância única e 48 horas de antecedência para múltiplas instâncias). Este email será enviado para o administrador da conta e para as contas de email de coadministradores fornecidas na assinatura. Veja um exemplo desse tipo de email:

<!--Image reference-->
![][image1]

## Pares de regiões

O Azure organiza um conjunto de pares de regiões. O Azure não distribui atualizações simultâneas em regiões pareadas durante a manutenção planejada de máquinas virtuais de instância única.

Veja a tabela a seguir para saber mais sobre os atuais pares de regiões:

Região 1 | Região 2
:----- | ------:
Centro-Norte dos EUA | Centro-Sul dos Estados Unidos
Leste dos EUA | Oeste dos EUA
Leste dos EUA 2 | Centro dos EUA
Norte da Europa | Europa Ocidental
Sudeste da Ásia | Ásia Oriental
China Oriental | Norte da China
Leste do Japão | Oeste do Japão
Sul do Brasil | Centro-Sul dos Estados Unidos
Sudeste da Austrália | Leste da Austrália
Gov do Iowa nos EUA | Gov da Virgínia nos EUA

Por exemplo, durante uma manutenção planejada, o Azure não distribuirá uma atualização no Oeste dos EUA se o Leste dos EUA estiver em manutenção ao mesmo tempo. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA.

<!--Anchors-->
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG


<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=Oct15_HO4-->