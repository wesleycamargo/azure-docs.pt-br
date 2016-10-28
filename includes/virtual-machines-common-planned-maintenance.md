

## Atualizações que preservam a memória

Para uma classe de atualizações no Microsoft Azure, os clientes não sentirão qualquer impacto em suas máquinas virtuais em execução. Muitas dessas atualizações são componentes ou serviços que podem ser atualizados sem interferir na instância em execução. Algumas são atualizações de infraestrutura de plataforma no sistema operacional do host, que podem ser aplicadas sem exigir uma reinicialização completa das máquinas virtuais.

Essas atualizações são realizadas com uma tecnologia que permite a migração dinâmica in-loco, também chamada de uma atualização para “preservar a memória”. Durante a atualização, a máquina virtual é colocada em estado de "pausa", preservando a memória RAM, enquanto o sistema operacional subjacente do host recebe as atualizações e os patches necessários. A máquina virtual é retomada após 30 segundos no estado de pausa. Após ser reiniciada, o relógio da máquina virtual será sincronizado automaticamente.

Nem todas as atualizações podem ser implantadas usando esse mecanismo, mas, dado o breve período de pausa, a implantação de atualizações dessa maneira reduz muito o impacto nas máquinas virtuais.

Para máquinas virtuais de múltiplas instâncias (em um conjunto de disponibilidade), essas atualizações são aplicadas em um domínio por vez.

## Configurações de máquina virtual

Existem dois tipos de configurações de máquina virtual: instâncias múltiplas e instância única. Em uma configuração de várias instâncias, as máquinas virtuais semelhantes são colocadas em um conjunto de disponibilidade.

A configuração de várias instâncias fornece redundância entre computadores físicos, potência e rede, sendo recomendada para garantir a disponibilidade do aplicativo. Todas as máquinas virtuais do conjunto de disponibilidade devem servir ao mesmo propósito para seu aplicativo.

Para obter mais informações sobre como configurar as máquinas virtuais para alta disponibilidade, consulte [Manage the availability of your Windows virtual machines](../articles/virtual-machines/virtual-machines-windows-manage-availability.md) ou [Manage the availability of your Linux virtual machines](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

Por outro lado, uma configuração de instância única é usada para máquinas virtuais autônomas que não são colocadas em um conjunto de disponibilidade. Essas máquinas virtuais não se qualificam para o contrato de nível de serviço (SLA), que exige duas ou mais máquinas virtuais sejam implantadas no mesmo conjunto de disponibilidade.

Para saber mais sobre SLAs, consulte a seção "Serviços de Nuvem, máquinas virtuais e rede virtual" dos [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).


## Atualizações de configuração de várias instâncias

Durante a manutenção planejada, a plataforma Microsoft Azure primeiro atualiza o conjunto de máquinas virtuais que estão hospedadas em uma configuração de várias instâncias. Isso causa uma reinicialização para essas máquinas virtuais com aproximadamente 15 minutos de tempo de inatividade.

Em uma atualização da configuração de várias instâncias, as máquinas virtuais são atualizadas de maneira a preservar a disponibilidade ao longo de todo o processo, pressupondo que cada máquina atenda a uma função semelhante às outras no conjunto.

Para cada máquina virtual em seu conjunto de disponibilidade, será atribuído um domínio de atualização e domínio de falha pela plataforma subjacente do Azure. Cada domínio de atualização é um grupo de máquinas virtuais que serão reinicializadas na mesma janela de tempo. Cada domínio de falha é um grupo de máquinas virtuais que compartilham uma mesma fonte de energia e um mesmo comutador de rede.

Para saber mais sobre domínios de atualização e domínios de falha, consulte [Configurar várias máquinas virtuais em um conjunto de disponibilidade para redundância](../articles/virtual-machines/virtual-machines-windows-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Para impedir que os domínios de atualização fiquem offline ao mesmo tempo, a manutenção é executada, desligando cada máquina virtual em um domínio de atualização, aplicando a atualização para as máquinas de host, reiniciando as máquinas virtuais e passando para o próximo domínio de atualização. O evento de manutenção planejada termina depois que todos os domínios de atualização tiverem sido atualizados.

A ordem dos domínios de atualização que estão sendo reinicializados não pode continuar em sequência durante a manutenção planejada, mas somente um domínio de atualização é reinicializado por vez. Atualmente, o Azure oferece notificação com 1 semana de antecedência para manutenção planejada de máquinas virtuais na configuração de várias instâncias.

Depois que uma máquina virtual é restaurada, aqui está um exemplo do que pode exibir o Visualizador de Eventos do Windows:

<!--Image reference-->
![][image2]

Use o visualizador para determinar quais máquinas virtuais são configuradas em uma configuração de várias instâncias usando o portal do Azure, Azure PowerShell ou CLI do Azure. Por exemplo, para determinar quais máquinas virtuais estão em uma configuração de várias instâncias, você pode procurar a lista de máquinas virtuais com a coluna Conjunto de Disponibilidade adicionada à caixa de diálogo de procura das máquinas virtuais. No exemplo a seguir, as máquinas virtuais Exemplo-VM1 e Exemplo-VM2 estão em uma configuração de várias instâncias:

<!--Image reference-->
![][image4]

## Atualizações de configuração de instância única

Depois de concluir as atualizações de configuração de várias instâncias, o Azure executará atualizações de configuração de instância única. Essa atualização causará uma reinicialização das máquinas virtuais que não estão em execução nos conjuntos de disponibilidade.

Observe que mesmo se você tiver apenas uma instância em execução em um conjunto de disponibilidade, a plataforma do Azure tratará isso como uma atualização de configuração de várias instâncias.

Para máquinas virtuais em uma configuração de instância única, as máquinas virtuais são atualizadas pelo seu desligamento, com a aplicação da atualização ao computador host e a reinicialização das máquinas virtuais, com aproximadamente 15 minutos de tempo de inatividade. Essas atualizações são executadas em todas as máquinas virtuais de uma região em uma única janela de manutenção.

Esse evento de manutenção planejada afetará a disponibilidade do aplicativo para esse tipo de configuração de máquina virtual. O Azure oferece notificações de manutenção planejada de máquinas virtuais com uma semana de antecedência na configuração de instância única.

## Notificação por email

Somente para configurações de máquina virtual de única instância ou várias instâncias, o Azure envia um comunicado prévio por email para alertar você sobre a próxima manutenção planejada (com 1 semana de antecedência). Este email será enviado para as contas de email do administrador e coadministrador da assinatura. Veja um exemplo desse tipo de email:

<!--Image reference-->
![][image1]

## Pares de regiões

Ao executar a manutenção, o Azure atualizará apenas as instâncias de Máquina Virtual em uma única região de seu par. Por exemplo, ao atualizar as Máquinas Virtuais no Centro-Norte dos EUA, o Azure não atualizará qualquer Máquina Virtual no Centro-Sul dos EUA ao mesmo tempo. Isso será agendado em outro momento, permitindo o failover ou o balanceamento de carga entre regiões. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA.

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
Centro da Índia | Sul da Índia
Oeste da Índia | Sul da Índia
Gov. dos EUA – Iowa | Gov. dos EUA – Virgínia

<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/AvailabilitySetExample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

