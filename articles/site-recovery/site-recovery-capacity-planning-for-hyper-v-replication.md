<properties
	pageTitle="Planejamento de capacidade para replicação do Hyper-V"
	description="Este artigo contém instruções sobre como usar a ferramenta de planejamento de capacidade do Azure Site Recovery e inclui recursos de planejamento de capacidade para a replicação do Hyper-V"
	services="site-recovery"
	documentationCenter="na"
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/05/2015"
	ms.author="lauraa" />

# Planejamento de capacidade para replicação do Hyper-V

O Azure Site Recovery usa a Réplica do Hyper-V para replicação entre dois sites locais do VMM ou entre um servidor VMM local e o armazenamento do Azure. Este artigo contém instruções passo a passo sobre como usar o Planejador de Capacidade do Azure Site Recovery (ASR) - ferramenta de Réplica do Hyper-V. O Planejador de Capacidade do ASR - ferramenta de Réplica do Hyper-V orienta um administrador de TI na criação da infraestrutura de servidor, armazenamento e rede que é necessária para implantar com êxito a Réplica do Hyper-V e para validar a conectividade de rede entre dois sites.

## Requisitos do sistema
- Sistema operacional: Windows Server® 2012 ou Windows Server® 2012 R2
- Memória: 20 MB (mínimo)
- CPU: sobrecarga de 5% (mínimo)
- Espaço em disco: 5 MB (mínimo)

## Etapas do tutorial
- Etapa 1: Preparar o site primário
- Etapa 2: Preparar o site de recuperação se o seu site de recuperação for local
- Etapa 3: Executar a ferramenta de planejamento de capacidade
- Etapa 4: Interpretar os resultados

## Etapa 1: Preparar o site primário
1. Faça uma lista de todas as máquinas virtuais do Hyper-V que precisarão ser habilitadas para replicação e dos hosts/clusters primários do Hyper-V correspondentes.
2. Agrupe os hosts e clusters primários do Hyper-V em um dos seguintes:

  - Servidores autônomos do Windows Server® 2012
  - Clusters do Windows Server® 2012
  - Servidores autônomos do Windows Server® 2012 R2
  - Clusters do Windows Server® 2012 R2

3. Será necessário executar a ferramenta de planejamento de capacidade uma vez para cada grupo de servidores autônomos e uma vez para cada cluster.
4. Habilite o acesso remoto ao WMI em todos os hosts e clusters primários. Certifique-se de que seja definido o conjunto certo de regras de firewall e permissões de usuário.

        netsh firewall set service RemoteAdmin enable

5. Habilite o monitoramento de desempenho nos hosts primários.

  - Abra o **Firewall do Windows** com o snap-in de **Segurança Avançada** e, em seguida, habilite as seguintes regas de entrada:
    - Acesso à rede COM+ (DCOM-IN)
    - Todas as regras no grupo de Gerenciamento Remoto do Log de Eventos

## Etapa 2: Preparar o site de recuperação
Se estiver usando o Azure como o site de recuperação ou o seu site de recuperação local ainda não estiver pronto, você poderá ignorar esta seção. Mas se você ignorá-la, não será possível medir a largura de banda disponível entre os dois sites.

1. Identificar o método de autenticação

	a. Kerberos: a ser usado quando os hosts primário e de recuperação do Hyper-V estiverem no mesmo domínio ou em domínios mutuamente confiáveis.

	b. Certificado: a ser usado quando os hosts primário e de recuperação do Hyper-V estiverem em domínios diferentes. Os certificados podem ser criados usando makecert. Para saber mais sobre as etapas necessárias para implantar certificados usando essa técnica, consulte a postagem de blog [Autenticação baseada em certificado da Réplica do Hyper-V - makecert](http://blogs.technet.com/b/virtualization/archive/2013/04/13/hyper-v-replica-certificate-based-authentication-makecert.aspx).

2. Identifique um **único** host/cluster de recuperação do Hyper-V no site de recuperação.

	a. Esse host/cluster de recuperação será usado para replicar uma máquina virtual fictícia e estimar a largura de banda disponível entre os sites primários e secundários.

	b. **Recomendado**: use um único host de recuperação do Hyper-V para executar os testes.

### Preparar um único host do Hyper-V como um servidor de recuperação
1. No Gerenciador do Hyper-V, clique em **Configurações do Hyper-V** no painel **Ações**.
2. Na caixa de diálogo **Configurações do Hyper-V**, clique em **Configuração de replicação**.
3. No painel Detalhes, selecione **Habilitar este computador como um servidor de réplica**.
4. Na seção **Autenticação e portas**, selecione o método de autenticação escolhida anteriormente. Para cada método de autenticação, especifique a porta a ser usada (as portas padrão são 80 para Kerberos sobre HTTP e 443 para autenticação baseada em certificado sobre HTTPS).
5. Se você estiver usando autenticação baseada em certificado, clique em **Selecionar Certificado** e, em seguida, forneça as informações de certificado.
6. Na seção **Autorização e Armazenamento**, especifique permitir que **qualquer** servidor autenticado (primário) envie dados de replicação para este servidor de réplica.
7. Clique em **OK** ou **Aplicar**.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

8. Validar se o ouvinte https está em execução por meio do comando: netsh http show servicestate
9. Abrir portas de firewall:

        Port 443 (certificae-based authentication):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"


        Port 80 (Kerberos):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"

### Preparar um único cluster do Hyper-V como um destino de recuperação
Se você já tiver preparado um host Hyper-V autônomo como servidor de recuperação, poderá ignorar esta seção.

1. Configurar o agente de réplica do Hyper-V:

	a. Em **Gerenciador de Servidores**, abra **Gerenciador de Cluster de Failover**.

	b. No painel esquerdo, conecte-se ao cluster e, enquanto o nome do cluster estiver realçado, clique em **Configurar Função** no painel **Ações**. O **Assistente para Alta Disponibilidade**.

	c. Na tela **Selecionar Função**, selecione **Agente de Réplica do Hyper-V**.

	d. Conclua o assistente, fornecendo um **Nome de NetBIOS** e o **Endereço IP** a ser usado como o ponto de conexão para o cluster (chamado de ponto de acesso do cliente). O **Agente de Réplica do Hyper-V** é configurado, resultando em um nome de ponto de acesso de cliente. Anote o nome do ponto de acesso do cliente - você o usará para configurar a réplica mais tarde.

	e. Verifique se a função de Agente de Réplica do Hyper-V fica online com êxito e se pode fazer o failover entre todos os nós do cluster. Para fazer isso, clique com o botão direito do mouse na função, aponte para **Mover** e, em seguida, clique em **Selecionar Nó**. Selecione um nó > **OK**.

	f. Se você usar a autenticação baseada em certificado, assegure-se de que cada nó do cluster e o ponto de acesso do cliente do Agente de Réplica do Hyper-V tenham todos os certificados instalados.

2. Defina as configurações de réplica:

	a. Em **Gerenciador de Servidores**, abra **Gerenciador de Cluster de Failover**.
	
	b. No painel esquerdo, conecte-se ao cluster e, enquanto o nome do cluster estiver realçado, clique em **Funções** na categoria **Navegar** do painel **Detalhes**.
	
	c. Clique com o botão direito do mouse na função e, em seguida, selecione **Configurações de Replicação**.
	
	d. No painel **Detalhes**, selecione **Habilitar este cluster como um Servidor de Réplica**.

	e. Na seção **Autenticação e portas**, selecione o método de autenticação que você escolheu anteriormente. Para cada método de autenticação, especifique a porta a ser usada (as portas padrão são 80 para Kerberos sobre HTTP e 443 para autenticação baseada em certificado sobre HTTPS).

	f. Se você estiver usando autenticação baseada em certificado, clique em **Selecionar Certificado** e, em seguida, forneça as informações de certificado solicitadas.

	g. Na seção **Autorização e armazenamento**, especifique se deseja permitir que **qualquer** servidor autenticado (primário) envie dados de replicação para este servidor de réplica ou limitar a aceitação de determinados servidores primários. Você pode usar caracteres curinga para limitar a aceitação aos servidores de um determinado domínio sem ter de especificá-los individualmente (por exemplo, *.contoso.com).

	h. Abra portas de firewall em todos os hosts de recuperação do Hyper-V: porta 443 (autenticação de certificado): Get-ClusterNode | ForEach-Object {Invoke-command - computername \\$\_.name - scriptblock {Enable-Netfirewallrule - displayname "Ouvinte HTTPS da Réplica do Hyper-V (TCP-In)"}}


          Port 80 (Kerberos auth):
              Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}


## Etapa 3: Executar a ferramenta de planejamento de capacidade
1. Baixe a ferramenta de planejamento de capacidade.
2. Execute a ferramenta em um dos servidores primários (ou um de nós do cluster primário). Clique com o botão direito do mouse no arquivo .exe e selecione **Executar como administrador**.
3. Se desejar, aceite os **Termos da Licença** e, em seguida, clique em **Avançar**.
4. Selecione uma **Duração da coleta de métrica**. É altamente recomendável que a ferramenta seja executada durante as horas de produção para garantir que sejam coletados os dados mais representativos. A duração sugerida para coleta de métrica é de 30 minutos. Se estiver tentando apenas validar a conectividade de rede, você pode escolher um minuto como a duração.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

5. Especifique os **Detalhes do site primário**, conforme mostrado e, em seguida, clique em **Avançar**.

	Para um host autônomo, digite o nome ou o FQDN do servidor.

	Se o seu host primário fizer parte de um cluster, você pode inserir o FQDN de um dos itens a seguir:

	a. Ponto de acesso do cliente (CAP) do Agente de Réplica do Hyper-V

	b. O nome do cluster

	c. Qualquer nó do cluster

  ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)


6. Insira os **Detalhes do site de réplica** (somente replicação do site local para o próprio site local)

  Se deseja habilitar a replicação para o Azure ou não preparou um host ou cluster Hyper-V como um servidor de recuperação (conforme explicado na Etapa 2), ignore o teste envolvendo o site de réplica.

  Especifique os detalhes do **Site de réplica** e clique em **Avançar**.

i. Para um host autônomo, digite o nome ou o FQDN do servidor

II. Se o seu host primário fizer parte de um cluster, você pode inserir o FQDN de um dos itens a seguir:

a. Ponto de acesso do cliente (CAP) do Agente de Réplica do Hyper-V

b. O nome do cluster

c. Qualquer nó do cluster

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

7. Ignore os testes que envolvam o **Site de Réplica Estendido**. Eles não têm suporte do ASR.
8. Selecione as máquinas virtuais para fazer o perfil. A ferramenta conecta-se aos servidores de cluster ou autônomos especificados em **Detalhes do Site Primário** e enumera as máquinas virtuais em execução. Selecione as máquinas virtuais e os discos virtuais para os quais você deseja coletar métricas.

As seguintes máquinas virtuais não serão enumeradas nem mostradas:

- Máquinas virtuais que já estejam habilitadas para replicação
- Máquinas virtuais que não estão em execução

9. Digite **Informações de rede** (isso é aplicável somente para replicação de site local para o próprio site local e quando são fornecidos os detalhes do site de réplica).

Especifique as informações de rede solicitadas e, em seguida, clique em **Avançar**.

- Largura de banda WAN estimada
- O certificado a ser usado para autenticação (opcional): se planeja usar a autenticação baseada em certificado, você deve fornecer os certificados necessários nesta página.

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. No próximo conjunto de telas, clique em **Avançar** para iniciar a ferramenta.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

11. Quando a execução da ferramenta é concluída, clique em **Exibir Relatório** para examinar a saída.

    Localização padrão do relatório:

    %systemdrive%\\Usuários\\Público\\Documentos\\Capacity Planner

    Localização dos logs:

    %systemdrive%\\Usuários\\Público\\Documentos\\CapacityPlanner

## Etapa 4: Interpretar os resultados
Você pode ignorar as métricas que não estejam listadas em um dos dois cenários a seguir, uma vez que elas não são relevantes para esse cenário.

### Replicação do site local para o próprio site local
  - Impacto da replicação na computação do host primário, memória
  - Impacto da replicação no espaço do disco de armazenamento dos hosts primário e de recuperação, IOPS
  - Largura de banda total necessária para a replicação delta (Mbps)
  - Largura de banda de rede observada entre o host primário e o host de recuperação (Mbps)
  - Sugestão para o número ideal de transferências paralelas ativas entre os dois hosts/clusters

### Replicação do site local para o Azure
  - Impacto da replicação na computação do host primário, memória
  - Impacto da replicação no espaço do disco de armazenamento do host primário, IOPS
  - Largura de banda total necessária para a replicação delta (Mbps)

Diretrizes mais detalhadas podem ser encontradas na [Página de download do Planejador de Capacidade para Réplica do Hyper-V](http://go.microsoft.com/?linkid=9876170).

## Outros recursos
Os recursos a seguir podem ajudá-lo com o planejamento de capacidade para replicação do Hyper-V:

- [Atualização: Planejador de Capacidade para a Réplica do Hyper-V](http://go.microsoft.com/fwlink/?LinkId=510891) — leia esta entrada de blog para obter uma visão geral dessa nova ferramenta.

- [Planejador de Capacidade para a Réplica do Hyper-V](http://go.microsoft.com/fwlink/?LinkId=510892) — baixe a versão mais recente dessa ferramenta.

- [Laboratório prático interativo](http://go.microsoft.com/fwlink/?LinkId=510893) — obtenha um ótimo passo a passo do planejamento da capacidade com a ferramenta no blog TechNet de Keith Mayer.

- [Teste de desempenho e dimensionamento - do local para o próprio local](https://msdn.microsoft.com/library/azure/dn760892.aspx) — leia os resultados dos testes de replicação de uma implantação do local para o próprio local.


## Próximas etapas

Para iniciar a implantação do ASR:

- [Configurar a proteção entre um site VMM local e o Azure](site-recovery-vmm-to-azure.md)
- [Configurar a proteção entre um site do Hyper-V local e o Azure](site-recovery-hyper-v-site-to-azure)
- [Configurar a proteção entre dois sites VMM locais](site-recovery-vmm-to-vmm)
- [Configurar a proteção entre dois sites VMM locais com SAN](site-recovery-vmm-san)
- [Configurar a proteção com um único servidor VMM](site-recovery-single-vmm)
 

<!---HONumber=August15_HO7-->