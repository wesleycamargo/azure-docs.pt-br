<properties
	pageTitle="Criar uma conta do Lote do Azure | Microsoft Azure"
	description="Aprenda a criar uma conta do Lote do Azure no portal do Azure para executar cargas de trabalho paralelas em larga escala na nuvem."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/26/2016"
	ms.author="marsma"/>

# Criar e gerenciar uma conta do Lote do Azure no portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](batch-account-create-portal.md)
- [.NET de Gerenciamento do Lote](batch-management-dotnet.md)

O [portal do Azure][azure_portal] fornece as ferramentas necessárias para criar e gerenciar uma conta do Lote do Azure, que pode ser usada para processamento de cargas de trabalho paralelas em larga escala. Neste artigo, acompanharemos a criação da conta do Lote usando o portal e mostraremos as configurações e propriedades mais importantes de uma conta do Lote. Por exemplo, os aplicativos e os serviços desenvolvidos com o Lote precisam da URL e da chave de acesso da sua conta para se comunicarem com as APIs de serviço do Lote; ambas são encontradas no portal do Azure.

>[AZURE.NOTE] Atualmente, o portal do Azure dá suporte a um subconjunto dos recursos no serviço em Lotes, incluindo a criação da conta, o gerenciamento de configurações e de propriedades da conta e a criação e o monitoramento de pools e de trabalhos. O conjunto completo de recursos do Lote está disponível para desenvolvedores por meio de APIs do Lote.

## Criar uma conta do Batch

1. Entre no [Portal do Azure][azure_portal].

2. Clique em **Novo** > **Máquinas Virtuais** > **Serviço do Lote**.

	![Lote no Marketplace][marketplace_portal]

3. Examine as informações na folha **Serviço em Lotes** e clique em **Criar**. Observe que o Lote usa somente o modelo de implantação do Resource Manager.

	![Folha de criação do serviço em Lotes no portal do Azure][3]

4. A folha **Nova Conta do Lote** é exibida. Veja os itens *a* até *e* abaixo para obter descrições de cada elemento da folha.

    ![Criar uma conta do Batch][account_portal]

	a. **Nome da Conta** -- um nome exclusivo para sua conta do Lote. Esse nome deve ser exclusivo na região do Azure em que a conta é criada (confira o *Local* abaixo). Ele pode conter somente caracteres minúsculos, números e deve ter de 3 a 24 caracteres de comprimento.

	b. **Assinatura** -- uma assinatura na qual a conta do Lote será criada. Se você tiver somente uma assinatura, ela será selecionada por padrão.

	c. **Grupo de recursos** – um recurso de grupos para sua nova conta do Lote ou, opcionalmente, crie um novo.

	d. **Local** -- uma região do Azure na qual a conta do Lote será criada. Somente as regiões com suporte da sua assinatura e do seu grupo de recursos serão exibidas como opções.

    e. **Conta de Armazenamento** (opcional) -- uma conta de armazenamento de **finalidade geral** que você associa à sua nova conta do Lote. O recurso [pacotes de aplicativos](batch-application-packages.md) do Lote usará a conta de armazenamento vinculada para o armazenamento e a recuperação de pacotes de aplicativos. Confira [Implantação de aplicativos com pacotes de aplicativos do Lote do Azure](batch-application-packages.md) para saber mais sobre esse recurso.

     > [AZURE.TIP] A regeneração de chaves em uma conta de Armazenamento vinculada requer considerações especiais. Confira [Considerações sobre contas do Lote](#considerations-for-batch-accounts) abaixo para obter mais detalhes.

5. Clique em **Criar** para criar a conta.

  O portal indicará que está **Implantando** a conta e, após a conclusão, a folha Conta do Lote será exibida.

## Exibir propriedades de conta do Lote

A folha Conta do Lote exibe várias propriedades para a conta, bem como oferece acesso a outras configurações, como chaves de acesso, cotas, usuários e associação de conta de armazenamento.

* **URL da conta do Lote** -- essa URL oferecerá acesso à sua conta do Lote quando você estiver usando APIs como a API [REST do Lote][api_rest] ou a biblioteca de cliente [.NET do Lote][api_net] e terá este formato:

    `https://<account_name>.<region>.batch.azure.com`

* **Chaves de acesso** -- para exibir e gerenciar as chaves de acesso da sua conta do Lote, clique no ícone de chave para abrir a folha **Gerenciar chaves** ou clique em **Todas as configurações** > **Chaves**. Uma chave de acesso é necessária na comunicação com as APIs do serviço em Lotes, como com a API [REST do Lote][api_rest] ou com a biblioteca de cliente [.NET do Lote][api_net].

    ![Chaves da conta do Lote][account_keys]

* **Todas as configurações** -- para gerenciar todas as configurações da conta do Lote ou para exibir suas propriedades, clique em **Todas as configurações** para abrir a folha **Configurações**. Essa folha oferece acesso a todas as configurações e as propriedades da conta, inclusive exibindo as cotas da conta, selecionando uma conta do Armazenamento do Azure para vincular à conta do Lote e gerenciando usuários.

    ![Folhas de propriedades e de configurações da conta do Lote][5]

## Considerações sobre contas do Lote

* Você também pode criar e gerenciar contas do Lote com os [cmdlets do PowerShell do Lote](batch-powershell-cmdlets-get-started.md) e a biblioteca [.NET de Gerenciamento do Lote](batch-management-dotnet.md).

* Você não é cobrado pela conta do Lote. Você é cobrado por todos os recursos de computação do Azure consumidos por suas soluções do Lote e pelos recursos consumidos por outros serviços quando suas cargas de trabalho forem executadas. Por exemplo, você é cobrado pelos nós de computação em seus pools e, se usar o recurso [pacotes de aplicativos](batch-application-packages.md), será cobrado pelos recursos do Armazenamento do Azure usados para armazenar as versões do pacote de aplicativos. Veja [Preços do Lote][batch_pricing] para saber mais.

* Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote em diferentes regiões do Azure.

* Se estiver executando várias cargas de trabalho do Lote em larga escala, lembre-se das [cotas de serviço e limites específicos do Lote](batch-quota-limit.md) que se aplicam à sua assinatura do Azure e a cada conta do Lote. As cotas atuais em uma conta do Lote aparecem no portal nas propriedades da conta.

* Se você associar (link) uma conta de armazenamento à sua conta do Lote, tome cuidado ao regenerar as chaves de acesso da conta de armazenamento. Você só deve regenerar uma única chave de conta de armazenamento, clicar em **Sincronizar chaves** na folha da conta de armazenamento vinculada, aguardar cinco minutos para permitir que as chaves sejam propagadas para os nós de computação em seus pools e regenerar e sincronizar a outra chave, se necessário. Se você regenerar as chaves ao mesmo tempo, os nós de computação não poderão sincronizar nenhuma delas e, assim, perderão o acesso à conta de armazenamento.

  ![Regeneração de chaves da conta de armazenamento][4]

> [AZURE.IMPORTANT] No momento, o Lote dá suporte *somente* ao tipo de conta de armazenamento de **Finalidade geral**, conforme descrito na etapa 5 [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md). Ao vincular uma conta do Armazenamento do Azure à sua conta do Lote, você vincula *somente* uma conta de armazenamento de **Finalidade geral**.

## Próximas etapas

* Veja a [visão geral dos recursos do Lote do Azure](batch-api-basics.md) para saber mais sobre os conceitos e os recursos do serviço do Lote. O artigo aborda os recursos principais do Lote, como pools, nós de computação, trabalhos e tarefas, e fornece uma visão geral dos recursos do serviço que permitem a execução da carga de trabalho de computação em larga escala.

* Obtenha as noções básicas sobre o desenvolvimento de um aplicativo habilitado para o Lote usando a [biblioteca de cliente .NET do Lote](batch-dotnet-get-started.md). O [artigo introdutório](batch-dotnet-get-started.md) orienta você por meio de um aplicativo de trabalho que usa o serviço em Lotes para executar uma carga de trabalho em vários nós de computação e que inclui o uso do Armazenamento do Azure para preparação e recuperação de um arquivo de carga de trabalho.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[3]: ./media/batch-account-create-portal/batch_acct_03.png "Folha de criação do serviço em Lotes no portal do Azure"
[4]: ./media/batch-account-create-portal/batch_acct_04.png "Regeneração de chaves da conta de armazenamento"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "Folhas de propriedades e de configurações da conta do Lote"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0601_2016-->