<properties 
	pageTitle="Manutenção planejada para máquinas virtuais do Azure" 
	description="Compreenda o que é a manutenção planejada do Azure e como ela afeta suas máquinas virtuais em execução no Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="kenazk" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="kenazk"/>


# Manutenção planejada para máquinas virtuais do Azure

## Por que o Azure realiza manutenção planejada
<p> O Microsoft Azure realiza periodicamente atualizações em todo o mundo para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host subjacente a máquinas virtuais. Muitas dessas atualizações são realizadas sem nenhum impacto sobre as máquinas virtuais ou os Serviços de Nuvem. Porém, algumas dessas atualizações exigem uma reinicialização da máquina virtual para que se apliquem as atualizações obrigatórias à infraestrutura. A máquina virtual será desligada enquanto corrigimos a infraestrutura e, em seguida, as máquinas virtuais serão reiniciadas. Existem dois tipos de manutenção que podem afetar a disponibilidade da máquina virtual: planejada e não planejada. Esta página descreve como o Microsoft Azure realiza a manutenção planejada. Para obter mais informações sobre a manutenção não planejada, consulte [Compreender manutenção planejada X manutenção não planejada].

## Configurações de máquina virtual
Existem dois tipos de configurações de máquina virtual: instâncias múltiplas e instância única. As máquinas virtuais de várias instâncias são configuradas colocando-se máquinas virtuais idênticas em um conjunto de disponibilidade. A configuração de várias instâncias oferece redundância e é recomendada para garantir a disponibilidade do aplicativo. Todas as máquinas virtuais do conjunto de disponibilidade devem ser praticamente idênticas e ter a mesma finalidade do aplicativo. Para obter mais informações sobre como configurar as máquinas virtuais para alta disponibilidade, confira “<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/">Gerenciar a disponibilidade das máquinas virtuais</a>”.

Por outro lado, as máquinas virtuais de instância única são máquinas virtuais independentes que não são colocadas no conjunto de disponibilidade. Sozinhas, as máquinas virtuais de instância única não se qualificam para o Contrato de Nível de Serviço (SLA), que exige duas ou mais máquinas virtuais implantadas no mesmo conjunto de disponibilidade. Para obter mais informações sobre o SLA, consulte a seção “Serviços de Nuvem, máquinas virtuais e rede virtual" dos [Contratos de Nível de Serviço](http://azure.microsoft.com/support/legal/sla/).


## Atualização de várias instâncias
Durante a manutenção planejada, a plataforma do Azure atualizará primeiramente o conjunto de máquinas de host que hospedam o conjunto de máquinas virtuais em uma configuração de várias instâncias, causando a reinicialização dessas máquinas virtuais. Para máquinas virtuais em uma configuração de várias instâncias, as máquinas virtuais são atualizadas de maneira a preservar a disponibilidade ao longo de todo o processo, pressupondo-se que cada máquina ofereça uma função semelhante à função das outras no conjunto. Toda máquina virtual no conjunto de disponibilidade recebe um Domínio de Atualização (UD) e um Domínio de Falha (FD) da plataforma do Azure subjacente. Cada UD é um grupo de máquinas virtuais que serão reinicializadas na mesma janela de tempo. Cada FD é um grupo de máquinas virtuais que compartilham uma mesma fonte de energia e um mesmo comutador de rede.

Para obter mais informações sobre UDs e FDs, confira “<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy">Configurar várias máquinas virtuais em um conjunto de disponibilidade para redundância</a>”.

O Microsoft Azure garante que nenhum evento de manutenção planejada deixará máquinas virtuais de dois UDs diferentes ficarem offline ao mesmo tempo. A manutenção é realizada desligando-se cada máquina virtual, aplicando-se a atualização às máquinas de host, reiniciando-se a máquina virtual e passando-se ao próximo UD. O evento de manutenção planejada termina assim que todos os UDs forem atualizados. A ordem de UDs reinicializados não pode continuar sequencialmente durante a manutenção planejada, e sim apenas um UD será reinicializado por vez. Atualmente, nenhuma notificação prévia de manutenção planejada é dada para máquinas virtuais na configuração de várias instâncias.

Depois que a máquina virtual for restaurada, aqui está um exemplo do que o Visualizador de Eventos do Windows poderá exibir:

<!--Image reference-->
![][image2]

## Atualização de instância única
Assim que as atualizações de várias instâncias forem concluídas, o Azure realizará a atualização no conjunto de máquinas de host que estão hospedando as máquinas virtuais de instância única. Essa atualização fará uma reinicialização das máquinas virtuais que não estão em execução nos conjuntos de disponibilidade. Mesmo que você tenha apenas uma instância em execução em um conjunto de disponibilidade, a plataforma do Azure continuará o tratando como várias instâncias. Para máquinas virtuais em uma configuração de instância única, as máquinas virtuais são atualizadas desligando-se as máquinas virtuais, aplicando-se a atualização à máquina de host e reiniciando-se a máquina virtual. Essas atualizações são executadas em todas as máquinas virtuais de uma região em uma única janela de manutenção. Esse evento de manutenção planejada afetará a disponibilidade do aplicativo para esse tipo de configuração de máquina virtual.
 
### Notificação por email
Apenas para máquinas virtuais de configuração de instância única, o Azure envia comunicação por email com pelo menos uma semana de antecedência para alertar você da próxima manutenção planejada. Esse email será enviado para a conta de email principal fornecida na assinatura. Um exemplo desse tipo de email é mostrado abaixo:

<!--Image reference-->
![][image1]

## Pares de região
O Azure organiza um conjunto de pares de regiões e garante que apenas uma região do par passe pela manutenção planejada. O Azure não distribui uma atualização em regiões pareadas simultaneamente durante uma manutenção planejada. Veja a tabela abaixo para saber mais sobre os atuais pares de regiões:

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

Por exemplo, durante uma distribuição de manutenção planejada, o Azure não distribuirá uma atualização no Oeste dos EUA se o Leste dos EUA estiver em manutenção ao mesmo tempo. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA.

<!--Anchors-->
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG


<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Compreender manutenção planejada X manutenção não planejada]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=58--> 