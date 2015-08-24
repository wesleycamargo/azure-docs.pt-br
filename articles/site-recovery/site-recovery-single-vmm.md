
<properties
	pageTitle="Configurar a proteção com um único servidor VMM"
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais localizadas em nuvens VMM locais para o Azure ou para uma nuvem VMM secundária."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/05/2015"
	ms.author="raynew"/>

#  Configurar a proteção com um único servidor VMM

## Visão geral

O Azure Site Recovery colabora com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Para obter uma lista completa de cenários de implantação, confira [Visão geral do Azure Site Recovery](site-recovery-overview.md).

Se você tiver apenas um servidor VMM em sua infraestrutura, será possível implantar a Recuperação de Site a fim de replicar máquinas virtuais nas nuvens VMM para o Azure, ou replicar entre nuvens em um único servidor VMM. Recomendamos que você faça isso somente se não for possível implantar dois servidores VMM (um em cada site), uma vez que o failover e a recuperação não são perfeitos nessa implantação. Para a recuperação, você precisará realizar manualmente o failover do servidor VMM de fora do console do Azure Site Recovery (usando a Réplica do Hyper-V no console do Gerenciador do Hyper-V).

É possível configurar de duas maneiras a replicação usando um único servidor VMM:

### Implantação autônoma

Implante um servidor VMM autônomo como uma máquina virtual em um site primário e replique essa máquina virtual para um site secundário com a Recuperação de Site e a Réplica do Hyper-V. Para reduzir o tempo de inatividade, o SQL Server pode ser instalado na máquina virtual do VMM. Se o VMM estiver usando um SQL Server remoto, será necessário recuperar ele primeiro antes de recuperar o servidor VMM.

![Servidor VMM virtual autônomo](./media/site-recovery-single-vmm/SingleVMMStandalone.png)

### Implantação de cluster

Para tornar o VMM altamente disponível, ele pode ser implantado como uma máquina virtual em um cluster de failover do Windows. Isso será útil se as cargas de trabalho críticas estiverem sendo gerenciadas pelo VMM, pois isso garante a disponibilidade da carga de trabalho e protege contra failover de hardware do host no qual o VMM é executado. Para implantar um único servidor VMM com a Recuperação de Site, a máquina virtual do VMM deve ser implantada em um cluster estendido em sites geograficamente separados. O banco de dados SQL Server usados pelo VMM deve ser protegido com os grupos de disponibilidade AlwaysOn do SQL Server com uma réplica no site secundário. Se ocorrer um desastre, o servidor VMM e seu banco de dados SQL Server correspondente passarão por failover e serão acessados no site secundário.

![Servidor VMM virtual clusterizado.](./media/site-recovery-single-vmm/SingleVMMCluster.png)


## Antes de começar

- As etapas passo a passo explicam como implantar a Recuperação de Site com um único servidor VMM autônomo.
- Verifique se [pré-requisitos](site-recovery-vmm-to-vmm.md/#before-you-start) foram atendidos antes de iniciar a implantação.
- O servidor VMM único deve ter pelo menos duas nuvens configuradas. Uma nuvem atuará como a nuvem protegida e a outra como a que fornece a proteção.
- A nuvem que você deseja proteger deve conter o seguinte:
	- Um ou mais grupos de hosts do VMM
	- Um ou mais servidores host Hyper-V em cada grupo de hosts
	- Uma ou mais máquinas virtuais Hyper-V em cada servidor host

Se você enfrentar problemas ao configurar esse cenário, publique suas perguntas no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).



## Configurar uma implantação de servidor único

1. Se o VMM não estiver implantado, configure o VMM em uma máquina virtual com um banco de dados SQL Server instalado. Leia os [requisitos de sistema](https://technet.microsoft.com/library/dn771747.aspx) 
2. Configure pelo menos duas nuvens no servidor VMM. Saiba mais em:

	- [Novidades na nuvem privada com o System Center 2012 R2 VMM](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=) e em [VMM 2012 e as nuvens](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html). 
	- [Configurando a malha de nuvem do VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- [Criando uma nuvem privada no VMM](https://technet.microsoft.com/library/jj860425.aspx) e [Passo a passo: criando nuvens privadas com o System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
3. Adicione o servidor host Hyper-V de origem no qual a máquina virtual que você deseja proteger está localizada à nuvem que você protegerá (nuvem de origem). Adicione o servidor host Hyper-V de destino à nuvem no servidor VMM que fornecerá a proteção.
4. [Crie](site-recovery-vmm-to-vmm.md/#step-1-create-a-site-recovery-vault) um cofre do Azure Site Recovery e gere uma chave de registro do cofre.
4. [Instale](site-recovery-vmm-to-vmm.md/#step-3-install-the-azure-site-recovery-provider) o Provedor do Azure Site Recovery Provider no servidor VMM e registre o servidor no cofre. 
5. Verifique se as nuvens aparecem no portal de Recuperação de Site, e [defina as configurações de proteção de nuvem](site-recovery-vmm-to-vmm.md/#step-4-configure-cloud-protection-settings).
	- Em **Local de Origem** e **Local de Destino**, especifique o nome do servidor VMM único.
	- Em **Método de Replicação**, selecione **Pela rede** para a replicação inicial, pois as nuvens estão localizadas no mesmo servidor.

6. Opcionalmente, [configure o mapeamento de rede](site-recovery-vmm-to-vmm.md/#step-5-configure-network-mapping):

	- Em **Origem** e **Destino**, especifique o nome do servidor VMM único.
	- Em **Rede na Origem** selecione a rede VM que está configurada para a nuvem que você está protegendo.
	- Em **Rede no Destino** selecione a rede VM que está configurada para a nuvem que você deseja usar para proteção.
	- O mapeamento de rede pode ser configurado entre duas redes de máquina virtual (VM) no mesmo servidor VMM. Se a mesma rede VMM existir em dois sites diferentes, você poderá mapear entre as mesmas redes.
7. [Habilite a proteção](site-recovery-vmm-to-vmm.md/#step-7-enable-virtual-machine-protection) para máquinas virtuais na nuvem do VMM que você deseja proteger. 
7. No console do Gerenciador do Hyper-V, configure a replicação para máquina virtual do VMM com a Réplica do Hyper-V. A máquina virtual do VMM não deve ser adicionada a todas as nuvens do VMM.


## Failover e recuperação

### Criar planos de recuperação

Os planos de recuperação agrupam as máquinas virtuais que devem passar por failover e recuperação juntas.

1. Quando você cria um plano de recuperação, em **Origem** e **Destino**, especifique o nome do servidor VMM único. Em **Selecionar Máquinas Virtuais**, as máquinas virtuais associadas à nuvem primária serão exibidas.
2. Em seguida, [crie e personalize planos de recuperação](https://msdn.microsoft.com/library/azure/dn337331.aspx).


### Recuperação

No caso de um desastre, as cargas de trabalho podem ser recuperadas usando as seguintes etapas:

1. Execute manualmente o failover da máquina virtual de réplica do VMM para o site de recuperação do console do Gerenciador do Hyper-V.
2. Após a recuperação da máquina virtual do VMM, você poderá efetuar logon no console do Gerenciador de Recuperação do Hyper-V no portal e realizar um failover não planejado das máquinas virtuais do site primário para o site de recuperação.
3.  Após a conclusão do failover não planejado os usuários poderão acessar todos os recursos no site primário.


 

<!---HONumber=August15_HO7-->