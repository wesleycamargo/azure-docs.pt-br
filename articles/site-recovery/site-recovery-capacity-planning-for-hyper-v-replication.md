<properties
	pageTitle="Planejar a capacidade de replicação de máquina virtual do Hyper-V"
	description="Este artigo contém instruções sobre como usar a ferramenta Capacity Planner do Hyper-V para o Azure Site Recovery"
	services="site-recovery"
	documentationCenter="na"
	authors="rayne-wiselman"
	manager="jwhit"
	editor="" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/01/2015"
	ms.author="raynew" />

# Planejar a capacidade de replicação de máquina virtual do Hyper-V

O Azure Site Recovery usa a Réplica do Hyper-V para replicar máquinas virtuais do Hyper-V de um site local para o Azure, ou para um datacenter secundário. A ferramenta Capacity Planner para o Site Recovery ajuda a descobrir os requisitos de replicação e de largura de banda para replicação de máquina virtual do Hyper-V.

## Antes de começar

Execute a ferramenta em um nó de cluster ou de servidor do Hyper-V no site primário. Para executar a ferramenta, os servidores de host Hyper-V precisam do seguinte:

- Sistema operacional: Windows Server® 2012 ou Windows Server® 2012 R2
- Memória: 20 MB (mínimo)
- CPU: sobrecarga de 5% (mínimo)
- Espaço em disco: 5 MB (mínimo)

Antes de executar a ferramenta, será necessário preparar o site primário. Se você estiver replicando entre dois sites locais e quiser verificar a largura de banda, também será necessário preparar um servidor de réplica.


## Etapa 1: Preparar o site primário
1. No site primário, faça uma lista de todas as máquinas virtuais do Hyper-V que você deseja replicar e os hosts/clusters do Hyper-V nos quais elas estão localizadas. A ferramenta pode ser executada individualmente para vários hosts autônomos ou para um único cluster, mas não para os dois juntos. Também é necessário executá-la separadamente para cada sistema operacional. Portanto, você deve reunir e anotar seus servidores Hyper-V da seguinte maneira: 

  - Servidores autônomos do Windows Server® 2012
  - Clusters do Windows Server® 2012
  - Servidores autônomos do Windows Server® 2012 R2
  - Clusters do Windows Server® 2012 R2

3. Habilite o acesso remoto ao WMI em todos os hosts e clusters Hyper-V. Execute este comando em cada servidor/cluster para ter certeza de que as regras de firewall e as permissões de usuário estejam definidas:

        netsh firewall set service RemoteAdmin enable

5. Habilite o monitoramento do desempenho nos servidores e clusters da seguinte maneira:

  - Abra o Firewall do Windows com o snap-in **Segurança Avançada** e habilite as seguintes regras de entrada: **Acesso à Rede COM+ (DCOM-IN)** e todas as regras no **grupo Gerenciamento Remoto do Log de Eventos**.

## Etapa 2: Preparar um servidor de réplica (replicação local para o local)

Não será necessário fazer isso se você estiver replicando para o Azure.

Recomendamos a configuração de um único host Hyper-V como servidor de recuperação para que uma VM fictícia possa ser replicada nele a fim de verificar a largura de banda. Você pode ignorar isso, mas não será possível medir a largura de banda se o fizer.

1. Se você quiser usar um nó de cluster como a réplica, configure o agente da Réplica do Hyper-V:

	- Em **Gerenciador de Servidores**, abra **Gerenciador de Cluster de Failover**.
	- Conecte-se ao cluster, realce o nome do cluster e clique em **Ações** > **Configurar Função** para abrir o assistente para Alta Disponibilidade.
	- Em **Selecionar Função**, clique em **Agente de Réplica do Hyper-V**. No assistente, forneça um **Nome de NetBIOS** e o **Endereço IP** que serão usados como o ponto de conexão para o cluster (chamado de ponto de acesso do cliente). O **Agente de Réplica do Hyper-V** será configurado, resultando em um nome de ponto de acesso de cliente. Anote esse nome. 
	- Verifique se a função de Agente de Réplica do Hyper-V fica online com êxito e se pode fazer o failover entre todos os nós do cluster. Para fazer isso, clique com o botão direito do mouse na função, aponte para **Mover** e, em seguida, clique em **Selecionar Nó**. Selecione um nó > **OK**. 
	- Se você estiver usando a autenticação baseada em certificado, certifique-se de que cada nó do cluster e o ponto de acesso do cliente tenham os certificados instalados.
2.  Habilite um servidor de réplica:

	- Para um cluster, abra o Gerenciador de Cluster de Falha, conecte-se ao cluster e clique em **Funções** > selecionar função > **Configurações de Replicação** > **Habilitar este cluster como um servidor de Réplica**. Observe que se você estiver usando um cluster como a réplica, também será necessário ter a função de Agente de Réplica do Hyper-V no cluster no site primário.
	- Para um servidor autônomo, abra o Gerenciador Hyper-V. No painel **Ações**, clique em **Configurações do Hyper-V** para o servidor que você deseja habilitar e, em **Configuração de Replicação**, clique em **Habilitar este computador como um Servidor de réplica**.
3. Configure a autenticação:

	- Em **Autenticação e portas** selecione como autenticar o servidor primário e as portas de autenticação. Se você estiver usando certificados, clique em **Selecionar Certificado** para selecionar um. Use o Kerberos se os hosts primário e de recuperação do Hyper-V estiverem no mesmo domínio ou em domínios confiáveis. Use certificados para uma implantação de grupo de trabalho ou em domínios diferentes.
	- Na seção **Autorização e Armazenamento**, permita que **qualquer** servidor autenticado (primário) envie dados de replicação para este servidor de réplica. Clique em **OK** ou **Aplicar**.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

	- Execute **netsh http show servicestate** para verificar se o ouvinte está em execução para o protocolo/porta especificado:  
4. Configure os firewalls. Durante a instalação do Hyper-V ocorre a criação de regras de firewall a fim de permitir o tráfego nas portas padrão (HTTPS na 443, Kerberos na 80). Habilite essas regras da seguinte maneira:
	
		- Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
		- Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
		- Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
		- Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## Etapa 3: Executar a ferramenta de planejamento de capacidade

Após a preparação do site primário e a configuração de um servidor de recuperação, você poderá executar a ferramenta.

1. [Baixe](https://www.microsoft.com/download/details.aspx?id=39057) a ferramenta do Centro de Download da Microsoft.
2. Execute a ferramenta em um dos servidores primários (ou um de nós do cluster primário). Clique com o botão direito do mouse no arquivo .exe e selecione **Executar como administrador**.
3. Em **Antes de começar**, especifique por quanto tempo você deseja coletar dados. Recomendamos a execução da ferramenta durante as horas de produção para garantir que os dados sejam representativos. Se estiver apenas tentando validar a conectividade da rede, escolha coletar por apenas um minuto.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. Em **Detalhes do site primário** especifique nome ou o FQDN do servidor para um host autônomo ou, para um cluster, especifique o FQDN do ponto de acesso do cliente, o nome do cluster ou qualquer nó no cluster e clique em **Avançar**. A ferramenta detecta automaticamente o nome do servidor no qual está em execução. A ferramenta seleciona as VMs que podem ser monitoradas para os servidores especificados.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. Em **Detalhes do Site de Réplica**, se você estiver replicando para o Azure, ou se estiver replicando para um datacenter secundário e ainda não tiver configurado um servidor de réplica, selecione **Ignorar os testes que envolvem o site de réplica**. Se você estiver replicando em um datacenter secundário e tiver configurado um tipo de réplica no FQDN do servidor autônomo ou o ponto de acesso de cliente para o cluster em **Nome do servidor (ou) CAP do Agente de Réplica do Hyper-V**.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. Em **Detalhes de Réplica Estendida**, habilite **Ignorar os testes que envolvem o site de Réplica Estendida**. Eles não são suportados pela Recuperação de Site.
7. Em **Escolher VMs para Replicar** as ferramentas se conectam ao servidor ou cluster e exibem as VMs e discos em execução no servidor primário, de acordo com as configurações especificadas na página **Detalhes do Site Primário**. Observe que as VMs já foram habilitadas para replicação ou que não estão em execução não serão exibidas. Selecione as VMs das quais você deseja coletar métricas. A seleção dos VHDs também coleta automaticamente os dados para as VMs.
9. Se você tiver configurado um servidor de réplica ou um cluster, em **Informações de rede**, especifique a largura de banda de WAN aproximada que você acha que usará entre os sites primário e de réplica e selecione os certificados se você tiver configurado a autenticação de certificado.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. Em **Resumo**, verifique as configurações e clique em **Avançar** para começar a coletar as métricas. O progresso e o status da ferramenta são exibidos na página **Calcular Capacidade**. Após a conclusão da execução da ferramenta, clique em **Exibir Relatório** para examinar a saída. Por padrão, os relatórios e logs serão armazenados em **%systemdrive%\\Users\\Public\\Documents\\Capacity Planner**.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## Etapa 4: Interpretar os resultados
Estas são as métricas importantes. Você pode ignorar as métricas que não estão listadas aqui. Elas não são relevantes para a Recuperação de Site.

### Replicação de local para local
  - Impacto da replicação na computação do host primário, memória
  - Impacto da replicação no espaço do disco de armazenamento dos hosts primário e de recuperação, IOPS
  - Largura de banda total necessária para a replicação delta (Mbps)
  - Largura de banda de rede observada entre o host primário e o host de recuperação (Mbps)
  - Sugestão para o número ideal de transferências paralelas ativas entre os dois hosts/clusters

### Replicação de local para o Azure
  - Impacto da replicação na computação do host primário, memória
  - Impacto da replicação no espaço do disco de armazenamento do host primário, IOPS
  - Largura de banda total necessária para a replicação delta (Mbps)

## Mais recursos

- Para obter informações detalhadas sobre a ferramenta, leia o documento que acompanha o download da ferramenta.
- Assista a um passo a passo da ferramenta no [Blog TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx) de Keith Mayer.
- [Obtenha os resultados](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx) de nosso teste de desempenho para replicação Hyper-V do local para o local



## Próximas etapas

- [Configurar a proteção entre um site VMM local e o Azure](site-recovery-vmm-to-azure.md)
- [Configurar a proteção entre um site do Hyper-V local e o Azure](site-recovery-hyper-v-site-to-azure.md)
- [Configurar a proteção entre dois sites VMM locais](site-recovery-vmm-to-vmm.md)

<!---HONumber=AcomDC_1203_2015-->