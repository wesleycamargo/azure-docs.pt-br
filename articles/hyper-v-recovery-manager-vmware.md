<properties 
	pageTitle="Tutorial: Configurar a proteção entre sites locais do VMWare" 
	description="InMage no Azure Site Recovery lida com replicação, failover e recuperação entre sites locais do VMWare." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Tutorial: Configurar a proteção entre sites locais do VMWare


<h2><a id="overview" name="overview" href="#overview"></a>Visão geral</h2>

<p>InMage na Recuperação de Site do Azure fornece replicação em tempo real entre os sites do VMWare no local. InMage está incluído nas assinaturas para o serviço de recuperação de Site do Azure.</p>




<h2><a id="before" name="before" href="#before"></a>Pré-requisitos</h2>
<div class="dev-callout"> 

<UL>
<LI><b>Conta do Azure</b>-Você precisará de uma conta do Azure. Se você não tem uma, consulte <a href="http://aka.ms/try-azure">Avaliação gratuita do Azure</a>. Obtenha informações sobre preços em <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalhes de Preços do Gerenciador de Recuperação de Site do Azure</a>.</LI>
</UL>


<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Etapas do tutorial</h2>
<a name="vault"></a> <h3>Etapa 1: Criar um cofre e baixar InMage</h3>

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).


2. Expanda os <b>Serviços de Dados</b>, expanda os <b>Serviços de Recuperação</b> e clique em <b>Cofre de Recuperação de Site</b>.


3. Clique em <b>Criar Novo</b> e, em seguida, clique em <b>Criação Rápida</b>.
	
4. Em <b>Nome</b>, digite um nome amigável para identificar o cofre.

5. Em <b>Região</b>, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Detalhes dos Preços do Azure Site Recovery</a>

6. Clique em <b>Criar cofre</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como <b>Ativo</b> na página de Serviços de Recuperação.</P>

<a name="upload"></a> <h3>Etapa 2: Configurar o cofre</h3>


1. Na página <b>Serviços de Recuperação</b>, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. Na lista suspensa, selecione **Entre dois sites do VMWare no local**.
3. Baixe InMage Scout.
	
	![VMWare](./media/hyper-v-recovery-manager-configure-vault/SRVMWare_SelectScenario.png)

4. Configure a replicação entre dois sites VMWare usando a documentação InMage que é baixada com o produto.


<h2><a id="next" name="next" href="#next"></a>Próximas etapas</h2>
<UL>

<LI>Se tiver dúvidas, visite o <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum de Serviços de Recuperação do Azure</a>.</LI> 
</UL>

<!--HONumber=49-->