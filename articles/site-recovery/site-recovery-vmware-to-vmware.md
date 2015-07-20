<properties 
	pageTitle="Implantar entre sites locais do VMWare" 
	description="O InMage Scout no Azure Site Recovery lida com replicação, failover e recuperação entre sites locais do VMWare." 
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
	ms.date="05/29/2015" 
	ms.author="raynew"/>


# Implantar entre sites locais do VMWare


##Visão geral

InMage Scout na Recuperação de Site do Azure fornece replicação em tempo real entre os sites do VMWare no local. O InMage Scout está incluído nas assinaturas para o serviço de recuperação de Site do Azure.


## Pré-requisitos

- **Conta do Azure**: você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](pricing/free-trial/).


##Etapa 1: criar um cofre e baixar o InMage Scout

1. Entre no [Portal de Gerenciamento](https://portal.azure.com).
2. Clique em **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Recuperação de Site**.
3. Clique em **Criar Novo** > **Criação Rápida**.
4. Em **Nome**, digite um nome amigável para identificar o cofre.
5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](pricing/details/site-recovery/).

<P>Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como <b>Ativo</b> na página principal de Serviços de Recuperação.</P>

##Etapa 2: configurar o cofre

1. Clique em **Criar cofre**.
2. Na página **Serviços de Recuperação**, clique no cofre para abrir na página de Início Rápido.
3. Na lista suspensa, selecione **Entre dois sites do VMWare no local**.
4. Baixe InMage Scout.
5. Configure a replicação entre dois sites VMWare usando a documentação InMage Scout que é baixada com o produto.


##Próximas etapas

Publique qualquer pergunta no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).<

<!---HONumber=July15_HO2-->