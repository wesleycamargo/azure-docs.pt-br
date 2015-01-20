<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Introdução à Recuperação de Site do Azure: Local para proteção de Site do Azure do Hyper-V" metaKeywords ="Recuperação de Site do Azure, o VMM, nuvens, recuperação de desastres" description="A recuperação do site do Azure coordena a replicação, o failover e a recuperação das máquinas virtuais entre um site Hyper-V no local e o Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery:  On-Premises to Azure Hyper-V Site Protection" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/19/2014" ms.author="raynew" />


# Introdução à Recuperação de Site do Azure:  Local para Proteção de Site do Azure do Hyper-V


<div class="dev-callout"> 

<p>A Recuperação de Site do Azure colabora com sua estratégia de continuidade de negócios e carga de trabalho ao gerenciar replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação.<p>

Este tutorial descreve como implantar a Recuperação de Site do Azure para proteger as cargas de trabalho em execução em um servidor Hyper-V no Windows Server 2012 R2 em um site local. As máquinas virtuais no servidor Hyper-V são replicadas para o Azure usando a replicação do Hyper-V. Essa implantação é particularmente útil se você tiver servidores Hyper-V em seu escritório ou filial, mas o System Center VMM não está implantado.


<LI>Este tutorial usa o caminho de implantação mais rápido com configurações mínimas e padrões.
Você pode ler instruções de implantação completa dos guias de <a href="http://go.microsoft.com/fwlink/?LinkId=522087">Planejamento</a> e <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">Implementação</a>.</LI>
<LI>Você pode ler sobre cenários adicionais de implantação de recuperação de Site do Azure em <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Visão geral de recuperação do site do Azure</a>.</LI>
<LI>Se você enfrentar problemas durante este tutorial, reveja o artigo wiki de <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Recuperação de Site do Azure: Cenários comuns de erro e resoluções</a> ou publique suas perguntas no <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum dos Serviços de Recuperação do Azure</a>.</LI>
</UL>

</div>


<h2><a id="before"></a>Pré-requisitos</h2> 
<div class="dev-callout"> 
<P>Certifique-se de que ter tudo em colocar antes de começar.</P>

<UL>
<LI><b>Conta do Azure</b>-Você precisará de uma conta do Azure. Se você não tem uma, consulte <a href="http://aka.ms/try-azure">Avaliação gratuita do Azure</a>. Obtenha informações de preço em <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalhes de Preços do Gerenciador de Recuperação de Site do Azure</a>.</LI>
<LI><b>Hyper-V</b>- No site local de origem será necessário pelo menos um servidor executando o Windows Server 2012 R2 com a função Hyper-V. O servidor Hyper-V deve conter uma ou mais máquinas virtuais. Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.</LI>
<LI><b>Máquinas Virtuais</b>- As máquinas virtuais que deseja proteger devem estar de acordo com os pré-requisitos do Azure para máquinas virtuais. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=522287">Suporte a máquina virtual</a>.</LI>

</UL>
<h2><a id="tutorial"></a>Etapas do tutorial</h2> 

Depois de verificar os pré-requisitos, faça o seguinte:
<UL>
<LI><a href="#vault">Etapa 1: Criar um cofre</a>-Crie um cofre de Recuperação de Site do Azure.</LI>
<LI><a href="#site">Etapa 2: Criar um site de Hyper-V</a>- Crie um site de Hyper-V como um recipiente para todos os servidores Hyper-V que contenha as máquinas virtuais que deseja proteger.</LI>
<LI><a href="#download">Etapa 3: Preparar servidores Hyper-V</a>- Gere uma chave de registro e baixe o arquivo de configuração do Provedor. Execute o arquivo em cada servidor Hyper-V no site e selecione a chave para registrar o servidor no cofre.</LI>
<LI><a href="#resources">Etapa 4: Preparar recursos</a>- Crie uma conta de armazenamento do Azure para armazenar as máquinas virtuais replicadas.</LI>
<LI><a href="#protectiongroup">Etapa 5: Criar e configurar grupos de proteção</a>- Crie um grupo de proteção e aplique suas configurações de proteção a ele. As configurações de proteção serão aplicadas a cada máquina virtual que você adicionar ao grupo.</LI>
<LI><a href="#enablevirtual">Etapa 6: Habilitar a proteção para máquinas virtuais</a>- Habilite a proteção para máquinas virtuais adicionando-as a um grupo de proteção.</LI>
<LI><a href="#recovery plans">Etapa 7: Testar a implantação</a>- Execute um failover de teste em uma máquina virtual.</LI>

</UL>




<a name="vault"></a> <h2>Etapa 1: Criar um cofre</h2>

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).


2. Expanda os <b>Serviços de Dados</b>, expanda os <b>Serviços de Recuperação</b> e clique em <b>Cofre de Recuperação de Site</b>.


3. Clique em <b>Criar Novo</b> e, em seguida, clique em <b>Criação Rápida</b>.
	
4. Em <b>Nome</b>, digite um nome amigável para identificar o cofre.

5. Em <b>Região</b>, selecione a região geográfica para o cofre. Para verificar as regiões suportadas, consulte a Disponibilidade Geográfica nos <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Detalhes de Preços de Recuperação de Site do Azure	.

6. Clique em <b>Criar cofre</b>.  

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como <b>Ativo</b> na página de Serviços de Recuperação.</P>

<a name="site"></a> <h2>Etapa 2: Criar um site de Hyper-V</h2>

1. Na página Serviços de Recuperação, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Na lista suspensa, selecione **Entre um site do Hyper-V no local e o Azure**.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectScenario.png)

3. Em **Criar um Site de Hyper-V** clique em **Criar Site de Hyper-V**. Especifique um nome e salve.

	![Hyper-V site](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateSite2.png)

<a name="download"></a> <h2>Etapa 3: Preparar servidores Hyper-V</h2>
	

1. Em **Preparar servidores Hyper-V**, clique no arquivo **Baixar uma chave de registro**.
2. Na página **Baixar a Chave de Registro**, clique em **Baixar** ao lado do site. Baixe a chave para um local seguro onde possa ser facilmente acessada pelo servidor Hyper-V. A chave é válida para 5 dias após ser gerada.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_DownloadKey2.png)

4. Clique em<b>Baixar o provedor</b> para obter a versão mais recente.
5. Execute o arquivo em cada servidor Hyper-V que você deseja registrar no cofre. O arquivo instala dois componentes:
	- **Provedor de recuperação de Site do Azure**- Trata a comunicação e a coordenação entre o servidor Hyper-V e o portal de Recuperação de Site do Azure.
	- **Agente de Serviços de Recuperação do Azure**- Manipula o transporte de dados entre máquinas virtuais em execução no servidor de origem Hyper-V e armazenamento do Azure.
6. Em **Microsoft Update** você pode optar por atualizações. Com essa configuração habilitada, o Provedor e o Agente de atualizações serão instaladas de acordo com a diretiva do Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider1.png)

7. Em **Instalação** especifique onde deseja instalar o Provedor e o Agente no servidor Hyper-V.

	![Install location](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider2.png)

8. Após a instalação continue para registrar o servidor no cofre.

	![Installation complete](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider3.png)


9. Na página**Conexão com a Internet**, especifique como o Provedor se conecta à Recuperação de Site do Azure. Selecione <b>Usar configurações de proxy padrão do sistema</b> para usar as configurações de conexão com a Internet definidas no servidor.  

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider4.png)

9. Na página **definições de cofre**, clique em **Procurar** para selecionar o arquivo de chave. Especifica a assinatura de Recuperação de Site do Azure, o nome do cofre e o site de Hyper-V ao qual pertence o servidor Hyper-V.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectKey.png)


11. Registro começa a registrar o servidor no cofre.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider6.png)

11. Após a conclusão de registro, os metadados do servidor Hyper-V são recuperados pela Recuperação de Site do Azure e o servidor é exibido em **Sites do Hyper-V** guia de **Servidores** página no cofre.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider7.png)


<h2><a id="resources"></a>Etapa 4: Preparar recursos</h2>


1. Na página de Início Rápido, em **Preparar recursos** selecione **Criar Conta de Armazenamento** para criar uma conta de armazenamento do Azure se você não tiver uma. A conta deve ter a replicação geográfica habilitada. Ele deve estar na mesma região que o cofre da recuperação de Site do Azure e estar associado com a mesma assinatura.

	![Create storage account](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateResources1.png)



<h2><a id="protectiongroup"></a>Etapa 5: Criar e configurar grupos de proteção</h2>

<p>Grupos de proteção agrupam as máquinas virtuais que têm as mesmas configurações de proteção. Aplique as configurações de proteção em um grupo de proteção e essas configurações serão aplicadas em todas as máquinas virtuais que você adicionar ao grupo.</p>
1. Em **Criar e configurar grupos de proteção** clique **Criar um grupo de proteção**. Se todos os pré-requisitos não estiverem em vigor uma mensagem é emitida e você pode clicar em **Exibir detalhes** para obter mais informações.

2. Na guia **Grupos de Proteção**, adicione um grupo de proteção. Especifique um nome, o site de origem Hyper-V, o destino **Azure**, o nome da sua assinatura de Recuperação de Site do Azure e a conta de armazenamento do Azure.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroupCreate.png)


2. Em **Configurações de replicação**, deixe as configurações padrão.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroup2.png)


<h2><a id="enablevirtual"></a>Etapa 6: Habilitar a proteção da máquina virtual</h2>
<p>Habilite a proteção para máquinas virtuais adicionando-as a um grupo de proteção.</p>

1. Na guia <b>Máquinas</b> para o grupo de proteção, clique em <b>Adicionar máquinas virtuais aos grupos de proteção para habilitar a proteção</b>.
2. Na página **Habilitar Proteção da Máquina Virtual** selecione as máquinas virtuais que deseja proteger.  

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_AddVM1.png)

Os trabalhos de Habilitar a Proteção são iniciados. Você pode acompanhar o andamento na guia **Trabalhos**. Após o trabalho Finalizar Proteção ser executado, a máquina virtual está pronta para failover. Depois que a proteção estiver habilitada e a replicação inicial for concluída, você pode exibir as máquinas virtuais no Azure.

Na recuperação de Site do Azure você pode exibir máquinas virtuais protegidas em **Itens Protegidos** > **Grupos de Proteção** > *protectiongroup_name* > **Máquinas Virtuais**.  
 

<h2><a id="recoveryplans"></a>Etapa 7: Testar a implantação</h2>

Teste a implantação para simular o mecanismo de failover e de recuperação em uma rede isolada sem afetar seu ambiente de produção. Para fazer isso, você executará um failover de teste para uma máquina virtual protegida.

1. **Itens Protegidos** > **Grupos de Proteção** > *protectiongroup_name* > **Máquinas Virtuais** selecione a máquina virtual que você deseja fazer failover e, em seguida, clique em **Failover de Teste**.
2. Na página **Confirmar Failover de Teste** selecione **Nenhum**.  

	![Recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_TestFailoverNoNetwork.png)

3. Você pode acompanhar o progresso de failover e o status na guia **Trabalho**.
4. Quando o failover atingir a fase **Testes completos**, conclua a verificação da seguinte maneira:
	- Após o failover, visualize a máquina virtual de réplica no portal do Azure. Verifique se a máquina virtual foi iniciada com êxito.
	- Se tiver configurado para máquinas virtuais de acesso a rede local, você pode iniciar uma conexão de área de trabalho remota para a máquina virtual.
	- Clique em **Concluir o teste** para concluí-lo.
	- Clique em **Observações** para gravar e salvar observações associadas com o teste de failover.
	- Clique em **Failover de teste concluído**. Limpa o ambiente de teste para desligar automaticamente e excluir a máquina virtual de teste.

<h2><a id="next"></a>Próximas etapas</h2>
<UL>
<LI>Para planejar e implantar a Recuperação de Site do Azure em um ambiente de produção completo, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=522087">Guia de planejamento para a Recuperação do Hyper-V</a> e <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">Guia de implantação de Recuperação de Site do Azure</a>.</LI>
<LI>Para dúvidas, visite o <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum de serviços de recuperação do Windows Azure</a>.</LI> 
</UL>

<!--HONumber=35.2-->
