

## <a name="memory-preserving-updates"></a>Atualizações que preservam a memória
Para uma classe de atualizações no Microsoft Azure, os clientes não sentem qualquer impacto em suas máquinas virtuais em execução. Muitas dessas atualizações são componentes ou serviços que podem ser atualizados sem interferir na instância em execução. Algumas são atualizações de infraestrutura de plataforma no sistema operacional do host, que podem ser aplicadas sem exigir uma reinicialização completa das máquinas virtuais.

Essas atualizações são realizadas com uma tecnologia que permite a migração dinâmica in-loco, também chamada de uma atualização para “preservar a memória”. Durante a atualização, a máquina virtual é colocada em estado de "pausa", preservando a memória RAM, enquanto o sistema operacional subjacente do host recebe as atualizações e os patches necessários. A máquina virtual é retomada após 30 segundos no estado de pausa. Após ser reiniciada, o relógio da máquina virtual será sincronizado automaticamente.

Nem todas as atualizações podem ser implantadas usando esse mecanismo, mas, dado o breve período de pausa, a implantação de atualizações dessa maneira reduz muito o impacto nas máquinas virtuais.

Para máquinas virtuais de múltiplas instâncias (em um conjunto de disponibilidade), essas atualizações são aplicadas em um domínio por vez.  

## <a name="virtual-machine-configurations"></a>Configurações de máquina virtual
Existem dois tipos de configurações de máquina virtual: instâncias múltiplas e instância única. Em uma configuração de várias instâncias, as máquinas virtuais semelhantes são colocadas em um conjunto de disponibilidade.

A configuração de várias instâncias fornece redundância entre computadores físicos, potência e rede, sendo recomendada para garantir a disponibilidade do aplicativo. Todas as máquinas virtuais do conjunto de disponibilidade devem servir ao mesmo propósito para seu aplicativo.

Para saber mais sobre como configurar as máquinas virtuais para alta disponibilidade, consulte [Gerenciar a disponibilidade de máquinas virtuais do Windows](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Gerenciar a disponibilidade de máquinas virtuais do Linux](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Por outro lado, uma configuração de instância única é usada para máquinas virtuais autônomas que não são colocadas em um conjunto de disponibilidade. Essas máquinas virtuais não se qualificam para o SLA (contrato de nível de serviço), que exige a implantação de duas ou mais máquinas virtuais no mesmo conjunto de disponibilidade.

Para saber mais sobre SLAs, consulte a seção "Serviços de Nuvem, máquinas virtuais" dos [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="multi-instance-configuration-updates"></a>Atualizações de configuração de várias instâncias
Durante a manutenção planejada, a plataforma Microsoft Azure primeiro atualiza o conjunto de máquinas virtuais que estão hospedadas em uma configuração de várias instâncias. A atualização causa uma reinicialização nessas máquinas virtuais, com aproximadamente 15 minutos de tempo de inatividade.

Uma atualização de configuração de várias instâncias pressupõe que cada máquina virtual realiza uma função parecida com a das outras no conjunto de disponibilidade. Nessa configuração, as máquinas virtuais são atualizadas de uma forma que preserva a disponibilidade durante todo o processo.

Cada máquina virtual em um conjunto de disponibilidade recebe um domínio de atualização e um domínio de falha da plataforma subjacente do Azure. Cada domínio de atualização é um grupo de máquinas virtuais que serão reinicializadas na mesma janela de tempo. Cada domínio de falha é um grupo de máquinas virtuais que compartilham uma mesma fonte de energia e um mesmo comutador de rede.


Para saber mais sobre domínios de atualização e domínios de falha, consulte [Configurar várias máquinas virtuais em um conjunto de disponibilidade para redundância](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Para manter a disponibilidade por meio de uma atualização, o Azure realiza a manutenção por domínio de atualização, atualizando um domínio por vez. A manutenção em um domínio de atualização é realizada desligando cada máquina virtual no domínio, aplicando a atualização às máquinas de host e reiniciando as máquinas virtuais. Após a conclusão da manutenção do domínio, o Azure repete o processo com o próximo domínio de atualização e continua em cada domínio até que todos estejam atualizados.

A ordem dos domínios de atualização que estão sendo reinicializados não pode continuar em sequência durante a manutenção planejada, mas somente um domínio de atualização é reinicializado por vez. Atualmente, o Azure oferece notificação com 1 semana de antecedência para manutenção planejada de máquinas virtuais na configuração de várias instâncias.

Depois que uma máquina virtual é restaurada, aqui está um exemplo do que pode exibir o Visualizador de Eventos do Windows:

<!--Image reference-->
![][image2]


Use o visualizador para indicar as máquinas virtuais configuradas em uma configuração de várias instâncias usando o Portal do Azure, o Azure PowerShell ou a CLI do Azure. Por exemplo, com o Portal do Azure você pode adicionar o _Conjunto de Disponibilidade_ à caixa de diálogo de pesquisa **máquinas virtuais (clássica)**. As máquinas virtuais que reportam o mesmo conjunto de disponibilidade fazem parte de uma configuração de várias instâncias. No exemplo a seguir, a configuração de várias instâncias é composta pelas máquinas virtuais SQLContoso01 e SQLContoso02.

<!--Image reference-->
  ![Modo de exibição de Máquinas virtuais (clássica) no Portal do Azure][image4]

## <a name="single-instance-configuration-updates"></a>Atualizações de configuração de instância única
Depois de concluir as atualizações de configuração de várias instâncias, o Azure executará atualizações de configuração de instância única. Essas atualizações causarão uma reinicialização das máquinas virtuais que não estão em execução nos conjuntos de disponibilidade.

> [!NOTE]
> Se um conjunto de disponibilidade tiver apenas uma instância em execução, a plataforma do Azure tratará isso como uma atualização de configuração de várias instâncias.
>

A manutenção em uma configuração de instância única é realizada desligando cada máquina virtual em execução em uma máquina host, atualizando o computador host e, em seguida, reiniciando as máquinas virtuais. A manutenção exige aproximadamente 15 minutos de inatividade. O evento de manutenção planejada ocorre em todas as máquinas virtuais de uma região em uma única janela de manutenção.


Os eventos de manutenção planejada afetam a disponibilidade do aplicativo para configurações de instância única. O Azure oferece uma notificação avançada de uma semana para manutenção planejada de máquinas virtuais em configurações de instância única.

## <a name="email-notification"></a>Notificação por email
Somente para configurações de máquina virtual de única instância ou várias instâncias, o Azure envia um alerta de email sobre a próxima manutenção planejada (com uma semana de antecedência). Esse email é enviado para as contas de email do administrador e coadministrador da assinatura. Veja um exemplo desse tipo de email:

<!--Image reference-->
![][image1]

## <a name="region-pairs"></a>Pares de regiões

Ao executar a manutenção, o Azure atualiza apenas as instâncias de Máquina Virtual em uma única região de seu par. Por exemplo, ao atualizar as Máquinas Virtuais no Centro-Norte dos EUA, o Azure não atualizará qualquer Máquina Virtual no Centro-Sul dos EUA ao mesmo tempo. Isso será agendado em outro momento, permitindo o failover ou o balanceamento de carga entre regiões. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA.

Consulte a tabela a seguir para conhecer os pares de região atuais:

| Região 1 | Região 2 |
|:--- | ---:|
| Leste dos EUA |Oeste dos EUA |
| Leste dos EUA 2 |Centro dos EUA |
| Centro-Norte dos EUA |Centro-Sul dos Estados Unidos |
| Centro-Oeste dos EUA |Oeste dos EUA 2 |
| Leste do Canadá |Canadá Central |
| Sul do Brasil |Centro-Sul dos Estados Unidos |
| Gov do Iowa nos EUA |Gov. dos EUA – Virgínia |
| DoD do Leste dos EUA |DoD Central dos EUA |
| Norte da Europa |Europa Ocidental |
| Oeste do Reino Unido |Sul do Reino Unido |
| Alemanha Central |Nordeste da Alemanha |
| Sudeste da Ásia |Ásia Oriental |
| Sudeste da Austrália |Leste da Austrália |
| Centro da Índia |Sul da Índia |
| Oeste da Índia |Sul da Índia |
| Leste do Japão |Oeste do Japão |
| Coreia Central |Sul da Coreia |
| China Oriental |Norte da China |


<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/availabilitysetexample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/windows/manage-availability.md#Understand-planned-versus-unplanned-maintenance/
