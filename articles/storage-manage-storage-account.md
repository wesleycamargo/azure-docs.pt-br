<properties urlDisplayName="How to manage" pageTitle="Como gerenciar contas de armazenamento | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="Saiba como gerenciar contas de armazenamento no Azure usando o Portal de Gerenciamento." metaCanonical="" services="storage" documentationCenter="" title="How To Manage Storage Accounts" authors="tamram" solutions="" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="tamram" />



<h1><a id="managestorageaccounts"></a>Como gerenciar contas de armazenamento</h1>

##Sumário##

* [Como: Gerenciar a replicação de uma conta de armazenamento](#georeplication)
* [Como: Exibir, copiar e regenerar chaves de acesso de armazenamento](#regeneratestoragekeys)
* [Como: Excluir uma conta de armazenamento](#deletestorageaccount)

<h2><a id="georeplication"></a>Como: Replicar os dados de sua conta de armazenamento para durabilidade e alta disponibilidade</h2>

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### Para especificar as configurações de replicação para uma conta de armazenamento ###

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), clique em **Armazenamento** e no nome de sua conta de armazenamento para exibir o painel.

2. Clique em **Configurar**.

3. No campo **Replicação**, selecione o tipo de replicação desejado para sua conta de armazenamento.

4. Clique em **Salvar**e, se solicitado, confirme a sua escolha.


<h2><a id="regeneratestoragekeys"></a>Como: Exibir, copiar e regenerar chaves de acesso de armazenamento</h2>
Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ao fornecer as duas chaves de acesso de armazenamento, o Azure permite que você regenere as chaves sem interrupção para o serviço de armazenamento ou o acesso a esse serviço.

No [Portal de Gerenciamento](http://manage.windowsazure.com), use **Gerenciar Chaves** no painel ou a página **Armazenamento** para exibir, copiar e regenerar as chaves de acesso do armazenamento que são usadas para acessar os serviços de Fila, Blob e Tabela. 

### Copiar uma chave de acesso de armazenamento ###

Você pode usar **Gerenciar Chaves** para copiar uma chave de acesso de armazenamento para uso em uma cadeia de conexão. A cadeia de conexão requer o nome da conta de armazenamento e uma chave a ser usada na autenticação.Para obter informações sobre como configurar cadeias de conexão para acessar os serviços de armazenamento do Azure, consulte [Configurando cadeias de conexão](http://msdn.microsoft.com/pt-br/library/ee758697.aspx).

1. No [Portal de Gerenciamento](http://manage.windowsazure.com), clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2. Clique em **Gerenciar Chaves**.

 	**Manage Access Keys** opens.

	![Managekeys](./media/storage-manage-storage-account/Storage_ManageKeys.png)

 
3. Para copiar uma chave de acesso de armazenamento, selecione o texto da chave. Em seguida, clique com o botão direito do mouse e clique em **Copiar**.

### Regenerar chaves de acesso de armazenamento ###
Você deve alterar as chaves de acesso de sua conta de armazenamento periodicamente para ajudar a manter as conexões de armazenamento mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra chave de acesso. 

<div class="dev-callout"> 
    <b>Aviso</b> 
    <p>Regenerar suas chaves de acesso afeta as máquinas virtuais, os serviços de mídia e quaisquer aplicativos que dependam da conta de armazenamento. Todos os clientes que usam a chave de acesso para acessar a conta de armazenamento precisam ser atualizados para usar a nova chave.
    </p> 
    </div>

**Máquinas virtuais** - Se sua conta de armazenamento contiver máquinas virtuais em execução, será necessário reimplantar todas as máquinas virtuais após regenerar as chaves de acesso.Para evitar a reimplantação, desligue as máquinas virtuais antes de regenerar as chaves de acesso.
 
**Serviços de mídia** - Se você tiver serviços de mídia dependentes de sua conta de armazenamento, deverá ressincronizar as chaves de acesso com o serviço de mídia depois de regenerar as chaves.
 
**Aplicativos** - se tiver aplicativos Web ou serviços de nuvem que usam a conta de armazenamento, você perderá as conexões se regenerar chaves, a menos que você as reverta.Este é o processo:

1. Atualize as cadeias de conexão no código do aplicativo para fazer referência à chave de acesso secundária da conta de armazenamento. 

2. Regenere a chave de acesso primária de sua conta de armazenamento. No [Portal de Gerenciamento](http://manage.windowsazure.com), no painel ou na página **Configurar**, clique em **Gerenciar chaves**. Clique em **Regenerar** sob a chave de acesso primária e, em seguida, clique em **Sim** para confirmar que você deseja gerar uma nova chave.

3. Atualize as cadeias de conexão em seu código para fazer referência à nova chave de acesso primária.

4. Regenere a chave de acesso secundária.


<h2><a id="deletestorageaccount"></a>Como: Excluir uma conta de armazenamento</h2>

Para remover uma conta de armazenamento que você não esteja mais usando, use **Excluir** no painel ou na página **Configurar**. **Excluir** exclui a conta de armazenamento inteira, incluindo todos os blobs, tabelas e filas da conta. 

<div class="dev-callout">
	<b>Aviso</b>
	<p>Não é possível restaurar o conteúdo de uma conta de armazenamento excluída. Faça 
	backup de todas as informações que você deseja salvar antes de excluir a conta.
	</p>
	<p>
	Se sua conta de armazenamento contiver quaisquer arquivos ou discos VHD para uma máquina 
	virtual do Azure, você deverá excluir as imagens e os discos que estão usando esses arquivos VHD 
	antes de excluir a conta de armazenamento. Em primeiro lugar, pare a máquina virtual se ela estiver em execução e exclua-a. Para excluir discos, navegue até a guia Discos e exclua todos os discos contidos na conta de armazenamento. Para excluir imagens, navegue até a guia Imagens e exclua todas as imagens armazenadas na conta.
	</p>
</div>


1. No [Portal de Gerenciamento](http://manage.windowsazure.com), clique em **Armazenamento**.

2. Clique em qualquer lugar na entrada da conta de armazenamento, exceto no nome e, em seguida, clique em **Excluir**.

	 -Ou-

	Clique no nome da conta de armazenamento para abrir o painel e, em seguida, clique em **Excluir**.

3. Clique em **Sim** para confirmar que deseja excluir a conta de armazenamento.

<!--HONumber=35.1-->
