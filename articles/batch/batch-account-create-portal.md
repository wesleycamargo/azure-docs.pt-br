<properties
	pageTitle="Criar uma conta do Lote do Azure | Microsoft Azure"
	description="Aprenda a criar uma conta do Lote do Azure no portal de visualização do Azure para executar cargas de trabalho paralelas em larga escala na nuvem."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="danlep"/>



# Criar e gerenciar uma conta do Lote do Azure no portal de visualização do Azure

Este artigo mostra como usar o [portal de visualização do Azure](https://portal.azure.com) para criar e gerenciar uma conta do Lote do Azure e configurações como chaves de conta. Você precisa de uma URL da conta do Lote e uma chave de acesso associada para autenticar todas as solicitações de API do Lote. E associar todos os recursos do Lote (como pools, trabalhos e tarefas) para seu Lote de carga de trabalho com uma conta específica do Lote.

>[AZURE.NOTE]Atualmente o portal de visualização dá suporte a recursos de gerenciamento de conta do Lote e a exibição de alguns recursos da conta. Os recursos completos do Lote estão disponíveis para desenvolvedores por meio de APIs do Lote.

## Criar uma conta do Batch

1. Entre no [portal de visualização do Azure](https://portal.azure.com).

2. Clique em **Novo** > **Computação** > **Serviço de Lote**.

	![Lote no Marketplace][marketplace_portal]

3. Examine as informações e clique em **Criar**.

4. Na folha **Nova Conta em Lotes**, insira as seguintes informações:

	a. Em **Nome da Conta**, insira um nome exclusivo a ser usado na URL da conta do Batch.

	>[AZURE.NOTE]O nome da conta do Lote deve ser exclusivo no Azure, conter entre 3 e 24 caracteres e usar somente números e letras minúsculas.

	b. Se você tiver mais de uma assinatura, clique em **Assinatura** para selecionar uma assinatura disponível onde a conta será criada.

	c. Clique em **grupo de recursos** para selecionar um grupo de recursos existente para a conta ou criar um novo.

	d. Em **Local**, selecione uma região do Azure na qual o Lote está disponível.

	![Criar uma conta do Batch][account_portal]

5. Clique em **Criar** para concluir a criação da conta.

## Gerenciar chaves de acesso e configurações de conta
Depois que a conta é criada, você pode encontrá-la no portal para gerenciar chaves de acesso, usuários autorizados e outras configurações.

A conta do Lote URL aparece em **Informações Gerais**. É uma URL do formulário `https://<account_name>.<region>.batch.azure.com`.

Para ver e gerenciar as chaves de acesso, clique no ícone da chave.

![Chaves da conta do Lote][account_keys]

## Informações adicionais para saber sobre a conta do Lote

* Outras maneiras de criar e gerenciar contas do Lote incluem o [cmdlets do PowerShell do Lote](batch-powershell-cmdlets-get-started.md) e a [biblioteca do Gerenciamento do Lote .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/).


* O Azure não cobra você para ter uma conta do Lote. Você só é cobrado pelo seu uso dos recursos de computação do Azure e outros serviços quando executar suas cargas de trabalho (consulte [Preços do Lote](https://azure.microsoft.com/pricing/details/batch/)).

* Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote em diferentes regiões do Azure.

* Se você estiver executando várias cargas de trabalho do Lote em larga escala, lembre-se das determinadas [cotas de serviço e os limites do Lote](batch-quota-limit.md) que se aplicam à sua assinatura do Azure e a cada conta do Lote. As cotas atuais da conta do Lote aparecem no portal de visualização nas propriedades da conta.

## Próximas etapas

* Confira as [Noções básicas de API para o Lote do Azure](batch-api-basics.md) para saber mais sobre os conceitos do Lote.

* Comece a desenvolver seu primeiro aplicativo com a [biblioteca de cliente .NET do Lote](batch-dotnet-get-started.md).

[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=Nov15_HO3-->