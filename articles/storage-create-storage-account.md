<properties urlDisplayName="How to create" pageTitle="Como criar uma conta de armazenamento | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />


<h1><a id="createstorageaccount"></a>Como criar uma conta de armazenamento</h1>

Para armazenar arquivos e dados nos serviços Blob, Tabela, Fila e Arquivo no Azure, crie uma conta de armazenamento na região geográfica em que deseja armazenar os dados. Este tópico descreve como criar uma conta de armazenamento no Portal de Gerenciamento do Azure.

Para obter detalhes sobre a capacidade da conta de armazenamento e a taxa de transferência, consulte [Metas de desempenho e escalabilidade do Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx).

> [WACOM.NOTE] Para uma máquina virtual do Azure, uma conta de armazenamento será criada automaticamente no local da implantação se você ainda não tiver uma conta de armazenamento nesse local. O nome da conta de armazenamento será baseado no nome da máquina virtual.

##Sumário##

* [Como: Criar uma conta de armazenamento](#create)
* [Próximas etapas](#next)

<h2><a id="create"></a>Como: Criar uma conta de armazenamento</h2>

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).

2. Clique em **Criar Novo**, em **Armazenamento** e em **Criação Rápida**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. Na **URL**, digite um nome de subdomínio a ser usado na URL da conta de armazenamento. Para acessar um objeto no armazenamento, você acrescentará o local do objeto ao ponto de extremidade. Por exemplo, a URL para acessar um blob deve ser http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

4. Em **Região/Grupo de Afinidade**, selecione uma região ou um grupo de afinidade para o armazenamento.  Selecione um grupo de afinidade em vez de uma região se desejar ter os serviços de armazenamento no mesmo data center com outros serviços do Azure que você está usando. Isso pode melhorar o desempenho e nenhum encargo será incorrido para a saída.

	> [WACOM.NOTE]  Para criar um grupo de afinidade, abra a área <b>Configurações</b> do Portal de gerenciamento, clique em <b>Grupos de afinidade</b>e, em seguida, clique no botão <b>Adicionar um grupo de afinidade</b> ou <b>Adicionar</b> . Você também pode criar e gerenciar os grupos de afinidade usando o Portal de Gerenciamento de Serviços do Azure. Consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/ee460798.aspx">Operações em grupos de afinidade</a> para obter mais informações.

5. Se você tiver mais de uma assinatura do Azure, o campo **Assinatura** será exibido. Em **Assinatura**, digite a assinatura do Azure com a qual você deseja usar a conta de armazenamento. Você pode criar até cinco contas de armazenamento para uma assinatura.

6. Em **Replicação**, selecione o nível de replicação desejado para sua conta de armazenamento.

	[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

6. Clique em **Criar Conta de Armazenamento**.

	Pode levar alguns minutos para criar sua conta de armazenamento. Para verificar o status, você pode monitorar as notificações na parte inferior do portal. Depois que a conta de armazenamento for criada, sua nova conta de armazenamento terá o status **Online** e estará pronta para uso. 

	![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

<h2><a id="next"></a>Próximas etapas</h2>

- Para saber mais sobre o armazenamento do Azure, consulte a documentação em [azure.com](http://azure.microsoft.com/pt-br/documentation/services/storage/) e no [MSDN](http://msdn.microsoft.com/pt-br/library/gg433040.aspx). 

- Visite o [Blog da equipe do Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/).

