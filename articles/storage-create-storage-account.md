<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="Como criar" pageTitle="Como criar uma conta de armazenamento | Azure" metaKeywords="" description="Saiba como criar uma conta de armazenamento no Portal de Gerenciamento do Azure." metaCanonical="" services="storage" documentationCenter="" title="Como criar uma conta de armazenamento" authors="tysonn" solutions="" manager="paulettm" editor="cgronlun" />




<h1><a id="createstorageaccount"></a>Como criar uma conta de armazenamento</h1>

Para armazenar arquivos e dados nos serviços de Blob, Tabela e Fila no Azure, você deve criar uma conta de armazenamento na região geográfica em que deseja armazenar os dados. Uma conta de armazenamento pode conter até 200 TB de dados, e você pode criar até vinte contas de armazenamento para cada assinatura do Azure. Consulte [Metas de desempenho e de escalabilidade do Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx) para obter mais informações.

Este tópico descreve como criar uma conta de armazenamento no Portal de Gerenciamento do Azure.

<div class="dev-callout"> 
<b>Observação</b> 
<p>Para uma máquina virtual do Azure, uma conta de armazenamento será criada automaticamente no local da implantação se você ainda não tiver uma conta de armazenamento nesse local. O nome da conta de armazenamento será baseada no nome da máquina virtual.</p> 
</div>

##Sumário##

* [Como criar uma conta de armazenamento](#create)
* [Próximas etapas](#next)

<h2><a id="create"></a>Como criar uma conta de armazenamento</h2>

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).

2. Clique em **Criar Novo**, em **Armazenamento** e em **Criação Rápida**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. Na **URL**, digite um nome de subdomínio a ser usado na URL da conta de armazenamento. Para acessar um objeto no armazenamento, você acrescentará o local do objeto ao ponto de extremidade. Por exemplo, a URL para acessar um blob deve ser http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

4. Em **Região/Grupo de Afinidade**, selecione uma região ou um grupo de afinidade para o armazenamento.  Selecione um grupo de afinidade em vez de uma região se desejar ter os serviços de armazenamento no mesmo data center com outros serviços do Azure que você está usando. Isso pode melhorar o desempenho e nenhum encargo será incorrido para a saída.

	> [WACOM.NOTE]
        > Para criar um grupo de afinidade, abra a área <b>Redes</b> do Portal de Gerenciamento, clique em <b>Grupos de Afinidade</b> e em <b>Criar um novo grupo de afinidade</b> ou em <b>Criar</b>. Você pode usar os grupos de afinidade criados anteriormente no Portal de Gerenciamento. Para abrir o outro portal, clique em <b>Visualização</b> na barra de títulos e clique em <b>Levar-me para o portal anterior</b>. (Para retornar a este portal, clique em <b>Exibir o Portal de Visualização</b> na parte inferior do portal.) Você também pode criar e gerenciar os grupos de afinidade usando o Portal de Gerenciamento de Serviços do Azure. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/ee460798.aspx">Operações em grupos de afinidade</a>.

5. Se você tiver mais de uma assinatura do Azure, o campo **Assinatura** será exibido. Em **Assinatura**, digite a assinatura do Azure com a qual você deseja usar a conta de armazenamento. Você pode criar até cinco contas de armazenamento para uma assinatura.

6. Na **Replicação**, selecione o nível de replicação desejado para sua conta de armazenamento.

	Por padrão, a replicação é definida como **Geograficamente Redundante**. Com a replicação geograficamente redundante, o failover de sua conta de armazenamento e de todos os dados contidos nela será feito em um local secundário no caso de um desastre maior no local principal. O Azure atribui um local secundário na mesma região que não pode ser alterado. Após um failover, o local secundário se tornará o local primário da conta de armazenamento, e seus dados serão replicados para um novo local secundário.

	Para poder ler os dados no local secundário, você pode selecionar a replicação de **Redundância Geográfica com Acesso de Leitura**. Essa opção fornece replicação geograficamente redundante e permite o acesso somente leitura aos dados replicados no local secundário. A replicação de redundância geográfica com acesso de leitura permite que você acesse os dados no local primário ou secundário, caso um local se torne indisponível.

	Uma terceira opção de replicação, **Redundância Geográfica com Acesso de Leitura**, está em visualização no momento. Essa opção fornece acesso somente leitura aos dados replicados no local secundário. A replicação de redundância geográfica com acesso de leitura permite que você acesse os dados no local primário ou secundário, caso um local se torne indisponível.

	> [WACOM.NOTE]
        > Para usar a replicação de redundância geográfica com acesso de leitura enquanto ela estiver no modo de visualização, você deve solicitar manualmente que esse recurso seja habilitado para sua assinatura. Visite a página <a href="https://account.windowsazure.com/PreviewFeatures">Recursos de visualização do Azure</a> para a replicação de redundância geográfica com acesso de leitura para sua assinatura. Para obter mais detalhes sobre a replicação de redundância geográfica com acesso de leitura, consulte o <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx">Blog da equipe de Armazenamento do Azure</a>.
	> Se a replicação de redundância geográfica com acesso de leitura não for habilitada como um recurso de visualização em sua assinatura, a opção de selecioná-la para a sua conta de armazenamento será desabilitada.

	Para informações de preços para replicação de conta de armazenamento, consulte [Detalhes de preços de armazenamento](http://www.windowsazure.com/pt-br/pricing/details/storage/).

6. Clique em **Criar Conta de Armazenamento**.

	Pode levar alguns minutos para criar sua conta de armazenamento. Para verificar o status, você pode monitorar as notificações na parte inferior do portal. Depois que a conta de armazenamento for criada, sua nova conta de armazenamento terá o status **Online** e estará pronta para uso. 

	![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

<h2><a id="next"></a>Próximas etapas</h2>

- Para saber mais sobre os serviços de armazenamento do Azure, consulte [Compreendendo o armazenamento na nuvem (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/develop/net/fundamentals/cloud-storage/) e [Blobs, filas e tabelas (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/gg433040.aspx). 

- Visite o [Blog da Equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/).

- Configure seus aplicativos para usar os Serviços de Blob, Tabela e Fila do Azure. O [Centro de Desenvolvedores do Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/develop/overview/) fornece Guias de instruções de como usar os serviços de Armazenamento de Blob, Tabela e Fila com seus aplicativos .NET, Node.js, Java, e PHP. Para obter instruções específicas sobre uma linguagem de programação, consulte os Guias de instruções dessa linguagem.


