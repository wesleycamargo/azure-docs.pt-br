<properties
	pageTitle="Replicar máquinas virtuais do Hyper-V em nuvens de VMM para um site de VMM secundário usando o Portal do Azure | Microsoft Azure"
	description="Descreve como implantar o Azure Site Recovery para orquestrar a replicação, o failover e a recuperação de VMs do Hyper-V em nuvens do VMM para um local de VMM secundário usando o portal do Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="raynew"/>

# Replicar máquinas virtuais do Hyper-V em nuvens de VMM para um site de VMM secundário usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-vmm.md)
- [Portal clássico](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell – Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bem-vindo ao Azure Site Recovery! Use este artigo se você quiser replicar máquinas virtuais do Hyper-V locais gerenciadas em nuvens do VMM (System Center Virtual Machine Manager) do System Center no Azure para um site secundário. Este artigo descreve como configurar a replicação usando o Azure Site Recovery no portal do Azure.

> [AZURE.NOTE] O Azure tem dois [modelos de implantação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: Azure Resource Manager e Clássico. O Azure também tem dois portais – o portal clássico do Azure, que dá suporte ao modelo de implantação clássica, e o portal do Azure, com suporte para ambos os modelos de implantação.


No Portal do Azure, o Azure Site Recovery fornece vários recursos novos:

- No Portal do Azure, os serviços Backup do Azure e Azure Site Recovery são combinados em um único cofre de Serviços de Recuperação para que você possa configurar e gerenciar a continuidade dos negócios e a recuperação de desastres (BCDR) em um único local. Um painel unificado permite monitorar e gerenciar operações em seus sites locais e de nuvem pública do Azure.
- Agora, os usuários com assinaturas do Azure provisionadas pelo programa CSP (Provedor de Soluções na Nuvem) podem gerenciar as operações de Recuperação de Site no portal do Azure.


Depois de ler este artigo, publique quaisquer comentários na parte inferior dos comentários do Disqus. Faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Visão geral

As organizações precisam de uma estratégia de BCDR que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. Sua estratégia de BCDR devem manter os dados comerciais seguros e passíveis de recuperação e garantir que as cargas de trabalho permaneçam continuamente disponíveis mediante um desastre.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é o Azure Site Recovery?](site-recovery-overview.md)

Este artigo fornece todas as informações de que você precisa para replicar máquinas virtuais locais do Hyper-V em nuvens do VMM para um site do VMM secundário. Ele inclui uma visão de geral da arquitetura, das informações de planejamento e das etapas de implantação para configurar os servidores locais, as configurações de replicação e o planejamento de capacidade. Depois de configurar a infraestrutura, você poderá habilitar a replicação nos computadores que deseja proteger e verificar se o failover funciona.

## Vantagens de negócios

- A Recuperação de Site fornece proteção para cargas de trabalho de negócios e aplicativos em execução em máquinas virtuais do Hyper-V replicando-os para um servidor secundário do Hyper-V.
- O portal de Recuperação de Serviços oferece um único local para configurar, gerenciar e monitorar a replicação, o failover e a recuperação.
- Você pode facilmente executar failovers de sua infraestrutura local primária para o site secundário e o failback (restauração) do site secundário para o primário.
- Você pode configurar planos de recuperação com vários computadores para que as cargas de trabalho do aplicativo em camadas façam failover juntas.

## Arquitetura de cenário

Estes são os componentes do cenário:

- **Site primário**: no site primário, há um ou mais servidores de host Hyper-V executando VMs de origem que você deseja replicar. Esses servidores de host primários estão localizados em uma nuvem privada do VMM.
- **Site secundário**: no site secundário, há um ou mais servidores de host do Hyper-V executando VMs de destino para as quais você replicará as VMs primárias. Esses servidores de host estão localizados em uma nuvem privada do VMM. A nuvem pode estar no servidor primário (se você tiver apenas um único servidor VMM) ou em um servidor secundário do VMM.
- **Provedor**: durante a implantação do Site Recovery, você instala o Provedor do Azure Site Recovery nos servidores VMM e registra os servidores em um cofre de Serviços de Recuperação. O Provedor em execução no servidor VMM se comunica com a Recuperação de Site por HTTPS 443 para replicar a orquestração. A replicação de dados ocorre entre servidores de host Hyper-V primários e secundários. Os dados replicados permanecem dentro de redes e sites locais e não são enviados para o Azure. Leia mais sobre [privacidade](#privacy-information-for-site-recovery).

![Topologia E2E](./media/site-recovery-vmm-to-vmm/architecture.png)

### Visão geral de privacidade de dados

Esta tabela resume como os dados são armazenados neste cenário:
****
Ação | **Detalhes** | **Dados coletados** | **Uso** | **Obrigatório**
--- | --- | --- | --- | ---
**Registro** | Você registra um servidor VMM em um cofre de Serviços de Recuperação. Se posteriormente você desejar cancelar o registro de um servidor, poderá fazer isso excluindo as informações do servidor do portal do Azure. | Depois que um servidor VMM é registrado, a Recuperação de Site coleta, processa e transfere os metadados sobre o servidor VMM e os nomes das nuvens do VMM detectados pela Recuperação de Site. | Os dados são usados para identificar e se comunicar com o servidor VMM apropriado e definir as configurações para as nuvens do VMM apropriadas. | Este recurso é necessário. Se você não quiser enviar essas informações à Recuperação de Site, não deverá usar o serviço de Recuperação de Site.
**Habilitar a replicação** | O provedor do Azure Site Recovery está instalado no servidor VMM e é o canal de comunicação com o serviço de Recuperação de Site. O Provedor é uma DLL (biblioteca de vínculo dinâmico) hospedada no processo do VMM. Depois que o Provedor é instalado, o recurso "Recuperação do Data Center" é habilitado no console do administrador do VMM. VMs novas e existentes podem habilitar esse recurso habilitar a proteção de uma VM. | Com essa propriedade definida, o Provedor envia o nome e a ID da VM para Recuperação de Site. A replicação é habilitada pelo Windows Server 2012 ou Réplica do Hyper-V do Windows Server 2012 R2. Os dados da máquina virtual são replicados de um host Hyper-V para outro (normalmente localizado em um data center de "recuperação" diferente). | A Recuperação de Site usa os metadados para preencher as informações da VM no portal do Azure. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações, não habilite a proteção de Recuperação de Site para VMs. Observe que todos os dados enviados pelo Provedor à Recuperação de Site são enviados por HTTPS.
**Plano de recuperação** | Planos de recuperação ajudam a compilar um plano de orquestração para o data center de recuperação. Você pode definir a ordem na qual as VMs ou um grupo de máquinas virtuais devem ser iniciados no site de recuperação. Você também pode especificar scripts automatizados para execução ou qualquer ação manual a ser executada no momento da recuperação para cada VM. O failover costuma ser disparado no nível do plano de recuperação para a recuperação coordenada. | A Recuperação de Site coleta, processa e transmite metadados para o plano de recuperação, incluindo metadados de máquina virtual e metadados de scripts de automação e anotações de ações manuais. | Os metadados são usados para compilar o plano de recuperação no portal do Azure. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações à Recuperação de Site, não crie planos de recuperação.
**Mapeamento de rede** | Mapeia as informações de rede do data center principal para o data center de recuperação. Quando as VMs são recuperadas no site de recuperação, o mapeamento de rede ajuda a estabelecer a conectividade de rede. | A Recuperação de Site coleta, processa e transmite os metadados das redes lógicas para cada site (primário e datacenter). | Os metadados são usados para preencher as configurações de rede para que você possa mapear as informações de rede. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações à Recuperação de Site, não use o mapeamento de rede.
**Failover (planejado/não planejado/teste)** | O failover faz o failover de VMs de um datacenter gerenciado por VMM para outro. A ação de failover é disparada manualmente no portal do Azure. | O Provedor no servidor VMM é notificado sobre o evento de failover pela Recuperação de Site e executa uma ação de failover no host Hyper-V por meio de interfaces do VMM. O failover real de uma VM é de um host do Hyper-V para outro e é tratado pelo Windows Server 2012 ou pela Réplica do Hyper-V do Windows Server 2012 R2. Após a conclusão do failover, o provedor no servidor VMM no data center de recuperação envia informações de êxito para Recuperação de Site. | A Recuperação de Site usa as informações enviadas para preencher o status das informações de ação de failover no portal do Azure. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações à Recuperação de Site, não use o failover.


## Pré-requisitos do Azure

Veja o que é necessário no Azure para implantar este cenário:

**Pré-requisitos** | **Detalhes**
--- | ---
**As tabelas**| Você precisa de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site.


## Pré-requisitos do local

Aqui está o que será necessário nos sites primário e secundário locais para implantar este cenário:

**Pré-requisitos** | **Detalhes**
--- | ---
**VMM** | Recomendamos a implantação com um servidor VMM no site primário e de um servidor VMM no site secundário.<br/><br/> Você também pode [replicar entre nuvens em um único servidor VMM](site-recovery-single-vmm.md). Para fazer isso, você precisará de pelo menos duas nuvens configuradas no servidor VMM.<br/><br/> Os servidores VMM devem estar executando pelo menos o System Center 2012 SP1 com as atualizações mais recentes.<br/><br/> Cada servidor VMM deve ter uma ou mais nuvens configuradas, e todas as nuvens devem ter o perfil de Capacidade do Hyper-V definido. <br/><br/>As nuvens devem conter um ou mais grupos de hosts VMM.<br/><br/>Saiba mais sobre como configurar nuvens VMM em [Configurando a malha de nuvem do VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) e o [passo a passo para criar nuvens privadas com o VMM do System Center 2012 SP1](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Servidores VMM precisam de acesso à Internet.
**Hyper-V** | Os servidores Hyper-V devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e ter as últimas atualizações instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/> Os servidores host Hyper-V devem estar localizados em grupos de host nas nuvens VMM primárias e secundárias.<br/><br/> Se você estiver executando o Hyper-V em um cluster no Windows Server 2012 R2, deverá instalar a [atualização 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se estiver executando o Hyper-V em um cluster no Windows Server 2012, observe que o agente de cluster não será criado automaticamente se você tiver um cluster de baseados em endereço IP estático. Você precisará configurar o agente de cluster manualmente. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Provedor** | Durante a implantação do Site Recovery, você instala o Provedor do Azure Site Recovery em servidores VMM. O Provedor se comunica com a Recuperação de Site por HTTPS 443 para orquestrar a replicação. A replicação de dados ocorre entre os servidores Hyper-V primário e secundário sobre a LAN ou uma conexão VPN.<br/><br/> O Provedor em execução no servidor VMM precisa de acesso a estas URLs: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net.<br/><br/> Além disso, permita a comunicação de firewall de servidores VMM para os [Intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e permita o protocolo HTTPS (443).

## Preparar para a implantação

Para se preparar para a implantação, você precisará:

1. [Preparar o servidor VMM](#prepare-the-vmm-server) para a implantação da Recuperação de Site.
2. [Preparar para mapeamento de rede](#prepare-for-network-mapping). Configure as redes de modo que você possa configurar o mapeamento de rede.

### Preparar o servidor do VMM

Certifique-se de que o servidor VMM seja compatível com os [pré-requisitos](#on-premises-prerequisites) e possa acessar as URLs listadas.


### Preparar para mapeamento de rede

Mapeamento de rede mapeiam entre redes de VM do VMM nos servidores primário e secundário do VMM para:

- Colocar de maneira ideal VMs de réplica em hosts secundários do Hyper-V após o failover.
- Conecte VMs de réplica às redes de VM apropriadas.
- Se você não configurar o mapeamento de rede, as VMs de réplica não serão conectadas a nenhuma rede após o failover.
- Se você quiser configurar o mapeamento de rede durante a implantação do Site Recovery, você precisará disto:

	- Verifique se as VMs do servidor host de origem do Hyper-V estão conectadas a uma rede de VMs do VMM. Essa rede deve ser vinculada a uma rede lógica que esteja associada à nuvem.
	- Verifique se a nuvem secundária que você usará para recuperação tem uma rede VM correspondente configurada. Essa rede de VM deverá ser vinculada a uma rede lógica associada à nuvem secundária.

- [Saiba mais](site-recovery-network-mapping.md) sobre o funcionamento do mapeamento de rede.

## Preparar para a implantação com um único servidor VMM

Se você tiver apenas um único servidor VMM, será possível replicar VMs em hosts Hyper-V na nuvem do VMM para [Azure](site-recovery-vmm-to-azure.md) ou para uma nuvem do VMM secundária. Recomendamos a primeira opção porque a replicação entre nuvens não é perfeita, mas, se você precisar fazer isso, faça o seguinte:

1. **Configure o VMM em uma VM Hyper-V**. Quando fazemos isso, sugerimos que você coloque a instância do SQL Server usada pelo VMM na mesma VM. Isso economiza tempo, pois apenas uma VM deverá ser criada. Se você quiser usar uma instância remota do SQL Server e uma paralisação ocorrer, precisará recuperar essa instância antes de recuperar o VMM.
2. **Garanta que o servidor VMM tenha pelo menos duas nuvens configuradas**. Uma nuvem conterá as máquinas virtuais que deseja replicar e outra nuvem servirá como o local secundário. A nuvem que contém as VMs que você deseja proteger deve estar de acordo com os [pré-requisitos](#on-premises-prerequisites).
3. Configure a Recuperação de Site conforme descrito neste artigo. Crie e registre o servidor VMM no cofre, configure uma política de replicação e habilite a replicação. Você deve especificar que a replicação inicial ocorre pela rede.
4. Ao configurar o mapeamento de rede, você vai mapear a rede da VM para a nuvem primária para a rede da VM para a nuvem secundária.
5. No console do Gerenciador do Hyper-V, habilite a Réplica do Hyper-V no host Hyper-V que contém a VM do VMM e habilite a replicação na VM. Certifique-se de que você não adicione a máquina virtual do VMM às nuvens que são protegidas pela Recuperação de Site, para garantir que as configurações de réplica do Hyper-V não sejam substituídas pela Recuperação de Site.
6. Se você criar planos de recuperação para failover, usará o mesmo servidor VMM para origem e destino.
7. Faça o failover e a recuperação da seguinte maneira:

	- Faça o failover da réplica da VM do VMM manualmente para o site secundário usando o Gerenciador do Hyper-V com um failover planejado.
	- Failover das VMs.
	- Depois que a VM do VMM primário tiver sido recuperada, faça logon no Portal do Azure -> cofre Serviços de Recuperação e execute um failover não planejado das VMs do site secundário para o site primário.
	- Após a conclusão do failover não planejado, todos os recursos poderão ser acessados no site principal novamente.


### Criar um cofre dos Serviços de Recuperação

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Gerenciamento** > **Serviços de Recuperação**. Como alternativa, você pode clicar em **Procurar** > cofres dos **Serviços de Recuperação** > **Adicionar**.

	![Novo cofre](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione uma delas.
4. [Crie um novo grupo de recursos](../resource-group-template-deploy-portal.md) ou selecione um existente e especifique uma região do Azure. Os computadores serão replicados para essa região. Para verificar as regiões suportadas, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços de Recuperação de Site do Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se você deseja acessar rapidamente o cofre pelo Painel, clique em **Fixar no painel** > **Criar cofre**.

	![Novo cofre](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

O novo cofre será exibido no **Painel** > **Todos os recursos** e na folha **Cofres dos Serviços de Recuperação** principal.




## Introdução

A Recuperação de Site fornece uma experiência de introdução que ajuda você a implantar o mais rápido possível. A Introdução verifica os pré-requisitos e orienta você durante as etapas de implantação da Recuperação de Site na ordem correta.

Na Introdução, você seleciona o tipo de computador que deseja replicar e para onde deseja replicar. Você configura servidores locais, cria políticas de replicação e realiza planejamento de capacidade. Depois de configurar sua infraestrutura, habilite a replicação para VMs. Você pode executar failovers para computadores específicos ou criar planos de recuperação para failover de vários computadores.

Inicie a Introdução ao escolher como deseja implantar a Recuperação de Site. O fluxo de Introdução mudará ligeiramente, dependendo dos requisitos da sua replicação.

## Etapa 1: escolher a meta de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Na folha **Cofres dos Serviços de Recuperação**, selecione seu cofre e clique em **Configurações**.
2. Em **Configurações** > **Introdução**, clique em **Recuperação de Site** > **Etapa 1: Preparar a Infraestrutura** > **Meta de proteção**.
3. Em **Meta de proteção**, selecione **Para site de recuperação** e selecione **Sim, com o Hyper-V**.
4. Selecione **Sim** para indicar que você está usando o VMM para gerenciar os hosts do Hyper-V e selecione **Sim** se você tiver um servidor VMM secundário. Se você estiver implantando a replicação entre nuvens em um único servidor VMM, clique em **Não**. Em seguida, clique em **OK**.

	![Escolher metas](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## Etapa 2: Configurar o ambiente de origem

Instale o Provedor do Azure Site Recovery nos servidores VMM e registre servidores no cofre.

1. Clique em **Etapa 2: Preparar a Infraestrutura** > **Origem**.

	![Configurar origem](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. Em **Preparar a origem**, clique em **+ VMM** para adicionar um servidor VMM.

	![Configurar origem](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. Na folha **Adicionar Servidor**, verifique se **Servidor do System Center VMM** aparece em **Tipo de servidor** e se o servidor VMM atende [aos pré-requisitos e aos requisitos de URL](#on-premises-prerequisites).
4. Baixe o arquivo de instalação do Provedor do Azure Site Recovery.
5. Baixe a chave do registro. Você precisará dela quando executar a instalação. A chave é válida por 5 dias após ser gerada.

	![Configurar origem](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. Instale o Provedor do Azure Site Recovery no servidor do VMM de origem.

> [AZURE.NOTE] Você não precisa instalar nada explicitamente nos servidores de host do Hyper-V.


### Configurar o Provedor do Azure Site Recovery

1. Execute o arquivo de configuração do Provedor em cada servidor VMM. Se o VMM for implantado em um cluster e você estiver instalando o Provedor pela primeira vez, instale-o em um nó ativo e conclua a instalação para registrar o servidor VMM no cofre. Em seguida, instale o Provedor nos outros nós. Nós de cluster devem todos executar a mesma versão do provedor.
2. A instalação executa algumas verificações de pré-requisitos e solicita permissão para interromper o serviço do VMM. O Serviço VMM será reiniciado automaticamente quando a instalação for finalizada. Se estiver instalando em um cluster do VMM, você deverá parar a função de Cluster.

2.  Em **Microsoft Update**, você pode aceitar as atualizações para que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.
3. Em **Instalação**, aceite ou modifique o local de instalação padrão do Provedor e clique em **Instalar**.

	![Local de instalação](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. Quando a instalação terminar, clique em **Registrar** para registrar o servidor no cofre.

	![Local de instalação](./media/site-recovery-vmm-to-vmm/provider-register.png)

9. Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado. Clique em *Próximo*.

	![Registros do servidor](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. Em **Conexão de Internet**, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Selecione **Conectar-se com as configurações de proxy existentes** para usar as configurações de conexão com a Internet padrão definidas no servidor.

	![Configurações da Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

	- Se quiser usar um proxy personalizado, você deverá configurá-lo antes de instalar o provedor. Quando você definir as configurações personalizadas de proxy, será executado um teste para verificar a conexão proxy.
	- Se usar um proxy personalizado ou se seu proxy padrão exigir autenticação, você precisará inserir os detalhes do proxy, incluindo a porta e o endereço do proxy.
	- As URLs a seguir devem estar acessíveis no servidor do VMM e nos hosts Hyper-V
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net
		- *.store.core.windows.net
	- Permita os endereços IP descritos em [Intervalos de IP do armazenamento de dados do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e o protocolo HTTPS (443). Você teria que fazer uma lista de intervalos IP válidos da região do Azure que você planeja usar e do oeste dos EUA.
	- Se você usar um proxy personalizado, uma conta RunAs VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada com êxito. As configurações da conta RunAs VMM podem ser modificadas no console do VMM. Para fazer isso, abra o espaço de trabalho **Configurações**, expanda **Segurança**, clique em **Executar como Contas**e modifique a senha de DRAProxyAccount. Você precisará reiniciar o serviço VMM para que essa configuração entre em vigor.


8. Em **Chave de Registro**, selecione a chave que você baixou no Azure Site Recovery e copiou para o servidor VMM.


10.  A configuração de criptografia é usada somente quando você estiver replicando VMs do Hyper-V em nuvens do VMM no Azure. Se estiver replicando para um site secundário, ela não é usada.

11.  Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome de função de cluster do VMM.
12.  Em **Sincronizar metadados de nuvem**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor do VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcada e sincronizar cada nuvem individualmente nas propriedades da nuvem no console VMM.

13.  Clique em **Avançar** para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido na guia **Servidores VMM** da página **Servidores** no cofre.

	![Servidor](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

11. Depois que o servidor estiver disponível no console de Recuperação de Site, em **Origem** > **Preparar origem** selecione o servidor VMM e selecione a nuvem na qual o host Hyper-V está localizado. Em seguida, clique em **OK**.

#### Instalação de linha de comando

O Provedor do Azure Site Recovery pode ser instalado da linha de comando. Esse método pode ser usado para instalar o Provedor em um Server Core para o Windows Server 2012 R2.

1. Baixar o arquivo de instalação do provedor e a chave de registro em uma pasta. Por exemplo, C:\\ASR.
2. Pare o Serviço do System Center Virtual Machine Manager.
3. Em um prompt de comando com privilégios elevados, execute estes comandos para extrair o instalador do Provedor:

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Execute este comando para instalar o Provedor:

    	C:\ASR> setupdr.exe /i

5. Em seguida, execute estes comandos para registrar o servidor no cofre:

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Em que os parâmetros são:

 - **/Credentials**: parâmetro obrigatório que especifica o local no qual o arquivo da chave de registro está localizado
 - **/FriendlyName**: parâmetro obrigatório para o nome do servidor do host Hyper-V que aparece no Portal do Azure Site Recovery.
 - **/EncryptionEnabled**: parâmetro opcional que você usa somente ao replicar do VMM para o Azure.
 - **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
 - **/proxyport**: parâmetro opcional que especifica a porta do servidor proxy.
 - **/proxyUsername**: parâmetro opcional que especifica o nome de usuário de Proxy (se o proxy exige autenticação).
 - **/proxyPassword**: parâmetro opcional que especifica a Senha para autenticação com o servidor proxy (se o proxy exige autenticação).

## Etapa 3: Configurar o ambiente de origem

Selecione o servidor VMM e a nuvem de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione o servidor VMM de destino que deseja usar.
2.	Nuvens no servidor sincronizadas com a Recuperação de Site serão exibidas. Selecione a nuvem de destino.

	![Destino](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## Etapa 4: Definir as configurações da replicação

1. Para criar uma nova política de replicação, clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.

	![Rede](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. Em **Criar e associar política**, especifique um nome de política. Os tipos de origem e de destino devem ser **Hyper-V**.
3. Em **Versão do host do Hyper-V**, selecione qual sistema operacional está em execução no host.

	> [AZURE.NOTE] A nuvem do VMM pode conter hosts Hyper-V que executam diferentes versões (com suporte) do Windows Server, mas uma política de replicação é aplicada a hosts que executam o mesmo sistema operacional. Se você tiver hosts executando mais de uma versão do sistema operacional, crie políticas de replicação separadas.

4. Em **Tipo de autenticação** e **Porta de autenticação**, especifique como o tráfego é autenticado entre os servidores de host do Hyper-V primário e de recuperação. Selecione **Certificado**, a menos que você tenha um ambiente Kerberos em funcionamento. A Recuperação de Site do Azure configurará automaticamente os certificados para autenticação HTTPS. Você não precisa fazer nada manualmente. Por padrão, as portas 8083 e 8084 (para certificados) serão abertas no Firewall do Windows nos servidores host Hyper-V. Se você selecionar **Kerberos**, um tíquete Kerberos será usado para autenticação mútua dos servidores host. Observe que esta configuração só é relevante para servidores de host Hyper-V no Windows Server 2012 R2.
3. Em **Frequência de cópia**, especifique com que frequência você deseja replicar os dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
4. Em **Retenção do ponto de recuperação**, especifique, em horas, qual será a duração da janela de retenção para cada ponto de recuperação. Os computadores protegidos podem ser recuperados para qualquer ponto nessa janela.
6. Em **Frequência do instantâneo consistente com aplicativo**, especifique com que frequência (1 a 12 horas) são criados os pontos de recuperação que incluam instantâneos consistentes com aplicativos. O Hyper-V usa dois tipos de instantâneos: um instantâneo padrão, que fornece um instantâneo incremental de toda a máquina virtual, e um instantâneo consistente com aplicativos, que cria um instantâneo pontual dos dados do aplicativo na máquina virtual. Os instantâneos consistentes com aplicativos usam o Serviço de VSS (Cópias de Sombra de Volume) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido. Observe que, se você habilitar instantâneos consistentes com aplicativos, isso afetará o desempenho de aplicativos executados em máquinas virtuais de origem. Verifique se o valor definido é menor do que o número de pontos de recuperação adicionais que você configurar.
7. Em **Compactação da transferência de dados**, especifique se os dados replicados transferidos devem ser compactados.
8. Selecione **Excluir VM de réplica** para especificar que a máquina virtual de réplica deverá ser excluída se você desabilitar a proteção para a VM de origem. Se você habilitar essa configuração, quando você desabilitar a proteção para a VM de origem, ela será removida do console de Recuperação de Site, as configurações de Recuperação de Site para o VMM serão removidas do console do VMM e a réplica será excluída.
3. Em **Método de replicação inicial**, se você estiver replicando pela rede, especifique se deseja iniciar a replicação inicial ou agendá-la. Para economizar largura de banda de rede, convém agendá-la fora dos seus horários mais ocupados. Em seguida, clique em **OK**.

	![Política de replicação](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. Quando você cria uma nova política, ela é automaticamente associada à nuvem do VMM. Em **Política de replicação**, clique em **OK**. Você pode associar Nuvens do VMM adicionais (e as VMs nelas) a essa política de replicação em **Configurações** > **Replicação** > nome da política > **Associar Nuvem do VMM**.

	![Política de replicação](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### Preparar a replicação inicial offline

Você pode fazer a replicação offline para a cópia inicial de dados. Você pode preparar isso da seguinte maneira:

- No servidor de origem, você especificará um local do caminho do qual será realizada a exportação de dados. Atribua Controle Total para permissões de NTFS e de Compartilhamento ao serviço VMM no caminho de exportação. No servidor de destino, você especificará um local do caminho do qual será feita a importação de dados. Atribua as mesmas permissões nesse caminho de importação.
- Se o caminho de importação ou de exportação for compartilhado, atribua a associação de grupo de Administrador, Usuário Avançado, Operador de Impressão ou Operador de Servidor à conta de serviço VMM no computador remoto no qual o caminho compartilhado está localizado.
- Se você estiver usando contas Executar como para adicionar hosts, nos caminhos de importação e de exportação, atribua permissões de leitura e gravação às contas Executar como no VMM.
- Os compartilhamentos de importação e de exportação não devem estar localizados em um computador usado como um servidor host Hyper-V porque o Hyper-V não dá suporte à configuração de loopback.
- No Active Directory, em cada servidor host Hyper-V que contém máquinas virtuais que deseja proteger, habilite e configure a delegação restrita para confiar nos computadores remotos nos quais os caminhos de importação e de exportação estão localizados, da seguinte maneira:
	1. No controlador de domínio, abra **Usuários e Computadores do Active Directory**.
	2. Na árvore de console, clique em **DomainName** > **Computadores**.
	3. Clique com o botão direito do mouse no nome do servidor host Hyper-V > **Propriedades**.
	4. Na guia **Delegação**, clique em **Confiar no computador para delegação apenas a serviços especificados**.
	5. Clique em **Usar qualquer protocolo de autenticação**.
	6. Clique em **Adicionar** > **Usuários e Computadores**.
	7. Digite o nome do computador que hospeda o caminho de exportação > **OK**. Na lista de serviços disponíveis, mantenha pressionada a tecla CTRL e clique em **cifs** > **OK**. Repita o procedimento para o nome do computador que hospeda o caminho de importação. Repita conforme necessário para servidores host Hyper-V adicionais.


### Configurar o mapeamento de rede

Configure o mapeamento de rede entre redes de origem e destino.

- [Leia](#prepare-for-network-mapping) uma rápida visão geral sobre o que o mapeamento de rede faz. Além disso, [leia](site-recovery-network-mapping.md) para obter uma explicação mais detalhada.
- Verifique se as máquinas virtuais nos servidores VMM estão conectadas a uma rede de VM.

Configure o mapeamento da seguinte maneira:

1. Em **Configurações** > **Infraestrutura da Recuperação de Site** > **Mapeamentos de rede** > **Mapeamento de Rede**, clique no ícone **+Mapeamento de Rede**.

	![Mapeamento de rede](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Na guia **Adicionar mapeamento de rede**, selecione os servidores de origem e destino do VMM. As redes da VM associadas aos servidores VMM são recuperadas.
3. Em **Rede de origem**, selecione a rede que você deseja usar na lista de redes VM associadas ao servidor primário do VMM.
6. Em **Rede de destino**, selecione a rede que você deseja usar no servidor VMM secundário. Em seguida, clique em **OK**.

	![Mapeamento de rede](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Veja o que acontece quando começa o mapeamento de rede:

- As máquinas virtuais de réplica existentes que corresponderem à rede VM de origem serão conectadas às redes da VM de destino.
- As máquinas virtuais que estiverem conectadas à rede VM de origem serão conectadas à rede mapeada de destino após a replicação.
- Se você modificar um mapeamento existente com uma nova rede, as máquinas virtuais de réplica serão conectadas usando as novas configurações.
- Se a rede de destino tiver várias sub-redes, e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada à sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.

## Etapa 5: Planejamento de capacidade

Agora que você tem a infraestrutura básica configurada, pode pensar sobre o planejamento de capacidade e descobrir se precisa de recursos adicionais.

O Site Recovery fornece um planejador de capacidade baseado em Excel para ajudá-lo a alocar os recursos certos para seu ambiente de origem, para os componentes da recuperação de site, para a rede e para o armazenamento. Você pode executar o planejador no modo rápido para estimativas baseadas em um número médio de VMs, de discos e de armazenamento, ou no modo detalhado, no qual você vai inserir números no nível de carga de trabalho. Antes de começar, será necessário:

- Reunir informações sobre seu ambiente de replicação, inclusive VMs, discos por VMs e armazenamento por disco.
- Estime a taxa de alteração (variação) diária que você terá para os dados delta replicados. Você pode usar o [Planejador de Capacidade para Réplica do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para ajudá-lo a fazer isso.

1.	Clique em **Baixar** para baixar a ferramenta e, então, executá-la. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.	Quando terminar, selecione **Sim** em **Você executou o Planejador de Capacidade**?

	![Planejamento da capacidade](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### Controle de largura de banda

Depois que você coletou informações de replicação delta em tempo real usando a Réplica do Hyper-V do Planejador de Capacidade, a ferramenta de planejador de capacidade baseada em Excel o ajudará a calcular a largura de banda necessária para replicação (inicial e delta). Para controlar a quantidade de largura de banda usada para replicação, é possível configurar a Política de NetQos usando a Política de Grupo ou o Windows PowerShell. Há algumas maneiras de fazer isso:

**PowerShell** | **Detalhes**
--- | ---
**New -NetQosPolicy "QoS to destination subnet"** | Limite o tráfego de um host do Hyper-V executando o Windows Server 2012 R2 a uma sub-rede secundária. Use se suas sub-redes primária e secundária forem diferentes.
**New -NetQosPolicy "QoS to destination port"** | Limite o tráfego de um host do Hyper-V executando o Windows Server 2012 R2 a porta de destino.
**New -NetQosPolicy "Throttle traffic from VMM"** | Limite o tráfego de vmms.exe. Isso limitará o tráfego do Hyper-V e o Tráfego da Migração Dinâmica. Você pode combinar os protocolos IP e as portas a refinar.

Você pode usar configurações de peso de largura de banda ou limitar o tráfego por bits por segundo. Se você estiver usando um cluster, precisará fazer isso em todos os nós de cluster. Para obter mais informações, consulte:


- Blog de Thomas Maurer sobre [limitação do tráfego de Réplica do Hyper-V](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)
- Informações adicionais sobre o [cmdlet New-NetQosPolicy](https://technet.microsoft.com/library/hh967468.aspx).


## Etapa 6: Habilitar a replicação

Agora habilite a replicação da seguinte maneira:

1. Clique em **Etapa 2: replicar aplicativo** > **Origem**. Depois de habilitar a replicação pela primeira vez, clique em **+Replicar** no cofre para habilitar a replicação para outros computadores.

	![Habilitar a replicação](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. Na folha **Fonte** > selecione o servidor VMM e a nuvem em que os hosts do Hyper-V você deseja replicar estão localizados. Em seguida, clique em **OK**.

	![Habilitar a replicação](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. Na folha de **Destino**, verifique o servidor secundário do VMM e a nuvem.
4. Em **Máquinas virtuais**, selecione as VMs que deseja proteger na lista.

	![Habilitar a proteção da máquina virtual](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Você pode acompanhar o progresso da ação **Habilitar Proteção** em Configurações > **Trabalhos** > **Trabalhos a Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, a máquina virtual está pronta para failover.


>[AZURE.NOTE] Também é possível habilitar a proteção para máquinas virtuais no console do VMM. Clique em **Habilitar Proteção** na barra de ferramentas na guia propriedades > **Azure Site Recovery** da máquina virtual.

Depois de habilitar a replicação, você pode exibir propriedades da VM em **Configurações** > **Itens Duplicados** > nome da vm. No painel **Essentials**, você pode ver informações sobre a política de replicação para a VM e seu status. Clique em **Propriedades** para obter mais detalhes.

### Integrar máquinas virtuais existentes

Se tiver máquinas virtuais existentes no VMM replicadas usando a Réplica do Hyper-V, você poderá integrá-las para a proteção do Azure Site Recovery, da seguinte maneira:

1. Verifique se o servidor Hyper-V que hospeda a VM existente está localizado em nuvem primária e o servidor Hyper-V que hospeda a máquina virtual de réplica está localizado na nuvem secundária.
2. Certifique-se de ter uma política de replicação configurada para a nuvem primária do VMM.
2. Habilite a replicação para a máquina virtual primária. O Azure Site Recovery e o VMM garantirão que o mesmo host de réplica e a mesma máquina virtual sejam detectados, e o Azure Site Recovery reutilizará e restabelecerá a replicação usando as configurações especificadas.


## Etapa 7: Testar a implantação

Para testar a implantação, você pode executar um failover de teste para uma única máquina virtual ou um plano de recuperação que contenha uma ou mais máquinas virtuais.

### Preparar para failover

- Para testar totalmente a implantação, você precisa de uma infraestrutura para o computador replicado funcionar como esperado. Se você quiser testar o Active Directory e o DNS, poderá criar uma máquina virtual como um controlador de domínio com DNS e replicar isso para o Azure usando o Azure Site Recovery. Leia mais em [considerações sobre failover de teste para o Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- As instruções neste artigo descrevem como executar um failover de teste sem rede. Esta opção testará se a VM executa failover, mas não testará as configurações de rede para a VM. [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre outras opções.
- Se você quiser executar um failover não planejado em vez de um teste de failover, observe o seguinte:

	- Se possível, você deve desligar os computadores primários antes de fazer um failover não planejado. Isso faz com que você não tenha os computadores de origem e de réplica em execução ao mesmo tempo.
	- Quando você executa um failover não planejado, ele interrompe a replicação de dados de computadores primários para que qualquer delta de dados não seja transferido após o início de um failover não planejado. Além disso se você executar um failover não planejado em um plano de recuperação, ele será executado até ser concluído, mesmo se ocorrer um erro.




### Execute um teste de failover

1. Para fazer failover em uma única VM, em **Configurações** > **Itens Replicados**, clique na VM > **+Failover de Teste**.

	![Failover de teste](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. Para fazer failover de um plano de recuperação, em **Configurações** > **Planos de Recuperação**, clique com o botão direito do mouse no plano > **Failover de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).
2. Em **Failover de Teste**, selecione **Nenhum**. Com essa opção, você testará que a VM faz o failover conforme o esperado, mas a VM replicada não será conectada a nenhuma rede.

	![Failover de teste](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. Clique em **OK** para iniciar o failover. Você pode acompanhar o andamento clicando na VM para abrir suas propriedades ou no trabalho **Failover de Teste** em **Configurações** > **Trabalhos** > **trabalhos da Recuperação de Site**.
4. Quando o trabalho de failover atingir a fase **Concluir testes**, faça o seguinte:

	-  Exiba a VM de réplica na nuvem do VMM secundária.
	-  Clique em **Concluir o teste** para concluir o failover de teste.
	-  Clique em **Observações** para gravar e salvar observações associadas ao failover de teste.

5. A máquina virtual de teste será criada no mesmo host como o host em que a máquina virtual de réplica existe. Ela não é adicionada à mesma nuvem na qual a máquina virtual de réplica está localizada.
6. Depois de verificar que as VMs iniciam com êxito, clique em **O failover de teste está concluído**. Neste ponto, todos os elementos criados automaticamente pela Recuperação de Site durante o failover de teste são excluídos.

	> [AZURE.NOTE] Se um failover de teste continuar por mais de duas semanas, ele será concluído à força.



### Atualizar o DNS com o endereço IP da VM de réplica

Após o failover, a VM de réplica poderá não ter o mesmo endereço IP da máquina virtual primária.

- As máquinas virtuais atualizarão o servidor DNS que estiverem usando depois que forem iniciadas.
- Você também pode atualizar manualmente DNS da seguinte maneira:

#### Recuperar o endereço IP

Execute este script de exemplo para recuperar o endereço IP.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

#### Atualizar o DNS

Execute este script de exemplo para atualizar o DNS especificando o endereço IP recuperado usando o script de exemplo anterior.

		string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Próximas etapas

Depois que a implantação estiver configurada e em funcionamento, [saiba mais](site-recovery-failover.md) sobre o os diferentes tipos de failover.

<!---HONumber=AcomDC_0824_2016-->