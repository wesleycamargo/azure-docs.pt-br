<properties
	pageTitle="Passo a passo: ativar a atualização mais recente do Banco de Dados SQL V12"
	description="Descreve as etapas para testar a versão V12 do Banco de Dados SQL do Azure usando a nova interface de usuário do portal do Microsoft Azure."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="genemi"/>


# Passo a passo: ativar a atualização mais recente do Banco de Dados SQL V12

Este tópico descreve as etapas que você pode seguir para ativar a opção Banco de Dados SQL V12 do Azure, inicialmente lançado pela Microsoft em dezembro de 2014.

Para testar a V12 mais recente, primeiramente, você precisa de uma assinatura do Microsoft Azure ou de, pelo menos, uma assinatura de [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).

É possível ativar a V12 usando o novo portal de visualização do Azure em [http://portal.azure.com/](http://portal.azure.com/), em vez do [portal do Azure antigo](http://manage.windowsazure.com/). Depois de ativar a V12 para sua assinatura, as opções de criação e atualização para a V12 serão desbloqueadas no portal de visualização do Azure. Em seguida, os usuários podem iniciar o fluxo de trabalho de [criação](sql-database-create.md), ou na folha do servidor, ou na folha de banco de dados.

> [AZURE.NOTE]Novos bancos de dados, cópias de banco de dados ou bancos de dados de teste são bons candidatos para a atualização para a visualização. Os bancos de dados de produção dos quais sua empresa depende devem esperar até após o período de visualização.

Para saber mais sobre a atualização para a V12, confira [Planejar e se preparar para atualizar para o Banco de Dados SQL V12 do Azure](sql-database-v12-plan-prepare-upgrade.md).


## R. Autorização de segurança

A primeira etapa é garantir que você tenha autorização suficiente para ativar a V12 para sua assinatura. Quando você tentar ativar a opção V12, uma verificação de autorização será executada para confirmar que você tem autoridade suficiente na assinatura.

 Para testar a V12, você deve ter uma das seguintes autorizações:

- O proprietário da assinatura
- Um coadministrador da assinatura

Para saber mais sobre as contas do Azure, confira [Gerenciar contas, assinaturas e funções administrativas](http://msdn.microsoft.com/library/hh531793.aspx).

## B. Etapas na interface de usuário do portal de visualização do Azure

Esta seção descreve uma sequência de cliques que você pode seguir uma vez na interface do usuário do portal de visualização do Azure para ativar a opção V12. Depois de ativar a opção, ele permanecerá disponível.

Todos os cenários de ativação usam a mesma ideia básica. Quando você tenta [criar um novo servidor do Banco de Dados SQL](sql-database-create.md) pela primeira vez, uma folha denominada **Atualização Mais Recente (visualização)** é exibida, apresentando uma caixa de seleção que pode ser selecionada para ativar seu privilégio para usar a versão V12. Depois de ativar o privilégio, você nunca verá a caixa de seleção novamente. Em vez disso, você verá um controle **Sim|Não** que pode usar para especificar se deseja que o novo servidor use a V12. Se escolher **Não**, você criará um servidor V11 (conforme relatado por SELECT @@VERSION;).

### B.1 Controle Sim|Não para a versão V12

Depois de ativar o privilégio da opção V12, você verá o controle **Sim|Não** que está circulado na captura de tela a seguir do portal de visualização do Azure.

![YesNoOptionForTheV12Preview][Image1]


## C. O que vem a seguir?

Os tópicos a seguir explicam como você pode usar o Banco de Dados SQL V12.

- [Criar um banco de dados na atualização do Banco de Dados SQL V12](sql-database-create.md)


<!-- References, Images. -->
[Image1]: ./media/sql-database-v12-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png

<!---HONumber=58-->