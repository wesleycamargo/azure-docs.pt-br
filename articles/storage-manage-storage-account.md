<properties linkid="manage-services-how-to-manage-a-storage-account" urlDisplayName="Como monitorar" pageTitle="Como gerenciar contas de armazenamento | Microsoft Azure" metaKeywords="O Azure gerencia as contas de armazenamento, o portal de gerenciamento da conta de armazenamento, a replicação geográfica da conta de armazenamento, a replicação geográfica do Azure, as chaves de acesso do Azure" description="Saiba como gerenciar contas de armazenamento no Azure usando o Portal de Gerenciamento." metaCanonical="" services="storage" documentationCenter="" title="Como gerenciar contas de armazenamento" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />



<h1><a id="managestorageaccounts"></a>Como gerenciar contas de armazenamento</h1>

##Sumário##

* [Como gerenciar a replicação de uma conta de armazenamento](#georeplication)
* [Como exibir, copiar e regenerar chaves de acesso de armazenamento](#regeneratestoragekeys)
* [Como excluir uma conta de armazenamento](#deletestorageaccount)

<h2><a id="georeplication"></a>Como gerenciar a replicação de uma conta de armazenamento</h2>

Você tem três opções para replicar sua conta de armazenamento:

- 	**Replicação geográfica redundante.** Por padrão, a replicação geográfica redundante está habilitada para sua conta de armazenamento. Com a replicação geográfica redundante, seus dados são replicados para um local geográfico secundário para habilitar o failover para esse local no caso de um desastre maior no local principal. O local secundário está na mesma região, mas a centenas de quilômetros do local principal. 

- **Replicação de redundância geográfica com acesso de leitura.** A replicação de redundância geográfica com acesso de leitura replica seus dados para uma localização geográfica secundária e também fornece acesso de leitura aos dados no local secundário. A replicação de redundância geográfica com acesso de leitura permite que você acesse os dados no local primário ou secundário, caso um local se torne indisponível.

- 	**Replicação localmente redundante**. Com a replicação localmente redundante, os dados de sua conta de armazenamento são replicados três vezes no mesmo data center. A replicação localmente redundante é oferecida com taxas de desconto. 
	
	Lembre-se de que, se você especificar replicação localmente redundante para sua conta de armazenamento e, posteriormente, você decidir habilitar a replicação geograficamente redundante, haverá um custo de dados único para replicar os dados existentes para o local secundário. 

- **Replicação de redundância geográfica com acesso de leitura (apenas visualização).** A replicação de redundância geográfica com acesso de leitura replica seus dados para uma localização geográfica secundária e também fornece acesso de leitura aos dados no local secundário. A replicação de redundância geográfica com acesso de leitura permite que você acesse os dados no local primário ou secundário, caso um local se torne indisponível.

Para informações de preços para replicação de conta de armazenamento, consulte [Detalhes de preços de armazenamento](http://www.windowsazure.com/pt-br/pricing/details/storage/).

### Para especificar as configurações de replicação para uma conta de armazenamento ###

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), clique em **Armazenamento** e, em seguida, clique no nome de sua conta de armazenamento para exibir o painel.

2. Clique em **Configurar**.

3. No campo **Replicação**, selecione o tipo de replicação desejado para sua conta de armazenamento.

4. Clique em **Salvar**e, se solicitado, confirme a sua escolha.


<h2><a id="regeneratestoragekeys"></a>Como exibir, copiar e regenerar chaves de acesso de armazenamento</h2>
Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ao fornecer as duas chaves de acesso de armazenamento, o Azure permite que você regenere as chaves sem interrupção para o serviço de armazenamento ou o acesso a esse serviço.

No [Portal de Gerenciamento](http://manage.windowsazure.com), use **Gerenciar Chaves** no painel ou a página **Armazenamento** para exibir, copiar e regenerar as chaves de acesso do armazenamento que são usadas para acessar os serviços de Fila, Blob e Tabela. 

### Copiar uma chave de acesso de armazenamento ###

Você pode usar **Gerenciar Chaves** para copiar uma chave de acesso de armazenamento para uso em uma cadeia de conexão. A cadeia de conexão requer o nome da conta de armazenamento e uma chave a ser usada na autenticação. Para obter informações sobre como configurar cadeias de conexão para acessar os serviços de armazenamento do Azure, consulte [Configurando cadeias de conexão](http://msdn.microsoft.com/pt-br/library/ee758697.aspx).

1. No [Portal de Gerenciamento](http://manage.windowsazure.com), clique em **Armazenamento** e, em seguida, clique no nome da conta de armazenamento para abrir o painel.

2. Clique em **Gerenciar Chaves**.

 	**Gerenciar Chaves de Acesso** é aberta.

	![Managekeys](./media/storage-manage-storage-account/Storage_ManageKeys.png)

 
3. Para copiar uma chave de acesso de armazenamento, selecione o texto da chave. Em seguida, clique com o botão direito do mouse e clique em **Copiar**.

### Regenerar chaves de acesso de armazenamento ###
Você deve alterar as chaves de acesso de sua conta de armazenamento periodicamente para ajudar a manter as conexões de armazenamento mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra chave de acesso. 

<div class="dev-callout"> 
    <b>Aviso</b> 
    <p>Regenerar suas chaves de acesso afeta as máquinas virtuais, os serviços de mídia e quaisquer aplicativos que dependam da conta de armazenamento.
    </p> 
    </div>

**Máquinas virtuais** - Se sua conta de armazenamento contiver máquinas virtuais em execução, será necessário reimplantar todas as máquinas virtuais após regenerar as chaves de acesso. Para evitar a reimplantação, desligue as máquinas virtuais antes de regenerar as chaves de acesso.
 
**Serviços de mídia** - Se você tiver serviços de mídia dependentes de sua conta de armazenamento, deverá ressincronizar as chaves de acesso com o serviço de mídia depois de regenerar as chaves.
 
**Aplicativos** - se tiver aplicativos da web ou serviços de nuvem usando a conta de armazenamento, você perderá as conexões se regenerar chaves, a menos que você as reverta. Este é o processo:

1. Atualize as cadeias de conexão no código do aplicativo para fazer referência à chave de acesso secundária da conta de armazenamento. 

2. Regenere a chave de acesso primária de sua conta de armazenamento. No [Portal de Gerenciamento](http://manage.windowsazure.com), no painel ou na página **Configurar**, clique em **Gerenciar Chave**. Clique em **Regenerar** sob a chave de acesso primária e, em seguida, clique em **Sim** para confirmar que você deseja gerar uma nova chave.

3. Atualize as cadeias de conexão em seu código para fazer referência à nova chave de acesso primária.

4. Regenerar a chave de acesso secundária.


<h2><a id="deletestorageaccount"></a>Como excluir uma conta de armazenamento</h2>

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


1. No [Portal de Gerenciamento](http://manage.windowsazure.com) clique em **Armazenamento**.

2. Clique em qualquer lugar na entrada da conta de armazenamento, exceto no nome e, em seguida, clique em **Excluir**.

	 -Ou-

	Clique no nome da conta de armazenamento para abrir o painel e, em seguida, clique em **Excluir**.

3. Clique em **Sim** para confirmar que deseja excluir a conta de armazenamento.

