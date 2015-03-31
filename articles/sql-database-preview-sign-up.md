<properties 
	pageTitle="Passo a passo: Ativar a atualização V12 mais recente do banco de dados SQL (visualização)" 
	description="Descreve as etapas para experimentar a versão experimental do V12 de banco de dados do SQL Azure, usando o portal do Azure novo interface do usuário." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard, jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="genemi"/>


# Passo a passo: Ativar a atualização V12 mais recente do banco de dados SQL (visualização)

Este tópico descreve as etapas que você pode seguir para ativar a opção para a atualização V12 do banco de dados SQL Azure (visualização), inicialmente lançado pela Microsoft em dezembro de 2014.

Para experimentar a mais recente visualização V12 é necessária, primeiro, uma assinatura para o Microsoft Azure ou ao menos uma assinatura de [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).

Você pode ativar a visualização V12 usando o portal de gerenciamento da nova visualização do Microsoft em [http://portal.azure.com/](http://portal.azure.com/). Depois de ativar a visualização V12 para sua assinatura, as opções de criação e atualização para a visualização V12 serão desbloqueadas no portal do Azure. Em seguida, os usuários podem iniciar o fluxo de trabalho de [criação](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) ou [atualização](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) a partir da lâmina do servidor ou da lâmina de banco de dados.

> [AZURE.NOTE]
> Novos bancos de dados, cópias de banco de dados ou bancos de dados de teste são bons candidatos para a atualização para a visualização. Os bancos de dados de produção dos quais sua empresa depende devem esperar até após o período de visualização.

Para obter mais informações sobre a visualização, consulte [Planejar e preparar a atualização V12 do banco de dados do SQL Azure (visualização)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


## R. Autorização de segurança

A primeira etapa é garantir que você tem autorização suficiente para ativar a visualização V12 para sua assinatura. Quando você tentar ativar a opção de visualização V12, uma verificação de autorização é executada para verificar se que você tem autoridade suficiente dentro da assinatura.

 Para tentar a visualização, você deve ter uma das seguintes autorizações:

- O proprietário da assinatura
- Um coadministrador da assinatura

Para obter mais informações sobre contas do Azure, consulte [Gerenciar contas, assinaturas e funções administrativas](http://msdn.microsoft.com/library/hh531793.aspx).

## B. Etapas no portal de interface do usuário

Esta seção descreve uma sequência de cliques que você pode seguir uma vez na interface do usuário do portal do Azure para ativar a opção de visualização V12. Depois de ativar a opção, ele permanecerá disponível.

Todos os cenários de ativação usam a mesma ideia básica. Quando você primeiro tenta [criar um novo servidor de banco de dados SQL](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/), uma lâmina rotulada **Atualização mais recente (visualização)** é exibida e oferece uma caixa de verificação que você pode optar por ativar o privilégio para usar a versão de visualização V12. Depois de ativar o privilégio, você nunca verá a caixa de seleção novamente. Em vez disso, você verá um controle **Sim|Não** que você pode usar para especificar se deseja que o novo servidor use a visualização V12. Se você escolher **Não**, você cria um servidor de V11 (conforme relatado pelo SELECT@@VERSION;).

### B.1 Sim|Não controle da versão de visualização V12

Depois de ter ativado o privilégio de visualização V12, você verá o controle **Sim|Não** circulado na seguinte captura de tela do portal.

![YesNoOptionForTheV12Preview][Image1]


## C. O que vem a seguir

Em seguida, os tópicos a seguir explicam maneiras que você pode usar a visualização V12.

- [Criar um banco de dados na atualização do banco de dados SQL V12 (visualização) mais recente](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)
- [Atualização para a atualização mais recente do banco de dados SQL V12 (visualização)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/)

> [AZURE.NOTE]
> Novos bancos de dados, cópias de banco de dados ou bancos de dados de teste são bons candidatos para a atualização para a visualização. Os bancos de dados de produção dos quais sua empresa depende devem esperar até após o período de visualização.


<!-- References, Images. -->
[Image1]: ./media/sql-database-preview-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png


<!-- EOF -->

<!--HONumber=47-->
