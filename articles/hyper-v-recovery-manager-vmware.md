<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Introdução à Recuperação de Site do Azure: Sites de proteção entre dois locais VMWare com InMage" metaKeywords ="Recuperação de Site do Azure, o VMM, nuvens, recuperação de desastres, VMWare, InMage" description="InMage na recuperação do site do Azure lida com a replicação, failover e recuperação entre os sites VMWare no local." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises VMWare Site Protection with InMage" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/19/2014" ms.author="raynew" />


# Introdução à Recuperação de Site do Azure:  Local para proteção do Site do VMWare local


<div class="dev-callout"> 

<p>InMage na Recuperação de Site do Azure fornece replicação em tempo real entre os sites do VMWare no local. InMage está incluído nas assinaturas para o serviço de recuperação de Site do Azure.</p>


</div>


<h2><a id="before"></a>Pré-requisitos</h2> 
<div class="dev-callout"> 

<UL>
<LI><b>Conta do Azure</b>- Você precisará de uma conta do Azure. Se você não tiver uma, consulte <a href="http://aka.ms/try-azure">Avaliação gratuita do Azure</a>. Obter informações de preço de assinatura no <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalhes dos preços do gerenciador de recuperação de site do Azure</a>.</LI>
</UL>



<a name="vault"></a> <h2>Etapa 1: Criar um cofre e baixar InMage</h2>

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).


2. Expanda <b>Serviços de dados</b>, expanda <b>Serviços de Recuperação</b>e clique em <b>Cofre de Recuperação de Site</b>.


3. Clique em <b>Criar Novo</b> e, em seguida, clique em <b>Criação Rápida</b>.
	
4. No <b>Name</b>, digite um nome amigável para identificar o cofre.

5. No <b>Região</b>, selecione a região geográfica para o cofre. Para verificar as regiões com suporte consulte a Disponibilidade Geográfica em <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Detalhes de preços de recuperação de Site do Azure</a>

6. Clique em <b>Criar cofre</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como <b>Ativo</b> na página principal de Serviços de Recuperação.</P>

<a name="upload"></a> <h2>Etapa 2: Configurar o cofre</h2>


1. No <b>Serviços de Recuperação</b> , clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. Na lista suspensa, selecione **Entre dois sites do VMWare no local**.
3. Baixe InMage Scout.
	
	![VMWare](./media/hyper-v-recovery-manager-configure-vault/SRVMWare_SelectScenario.png)

4. Configure a replicação entre dois sites VMWare usando a documentação InMage que é baixada com o produto.



<h2><a id="next"></a>Próximas etapas</h2>
<UL>

<LI>Para fazer perguntas, visite o <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum de serviços de recuperação do Azure</a>.</LI> 
</UL>

<!--HONumber=35.2-->
