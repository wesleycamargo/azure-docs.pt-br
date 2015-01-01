<properties umbracoNaviHide="0" pageTitle="Conceitos da conta de armazenamento | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Learn about storage account concepts." urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Storage Account Concepts" services="storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />


# Sobre contas de Armazenamento do Azure

Sua conta de Armazenamento do Azure é uma conta segura única através da qual é possível acessar os serviços de armazenamento do Azure - Blob, Fila, Tabela e Arquivo. Seus dados são protegidos no nível da conta de armazenamento e, por padrão, estão disponíveis apenas a você, o proprietário. 

Você é cobrado pelo uso do Armazenamento do Azure com base na sua conta de armazenamento. Os custos de armazenamento baseiam-se em quatro fatores: capacidade de armazenamento, esquema de replicação, transações de armazenamento e saída de dados. 

- A capacidade do armazenamento refere-se a quanto de sua alocação de conta de armazenamento você está usando para armazenar dados. O custo de simplesmente armazenar seus dados é determinado pela quantidade de dados que você está armazenando e como eles são replicados. 
- A replicação determina quantas cópias dos seus dados são mantidas de uma só vez e em quais locais. 
- As transações referem-se a todas as operações de leitura e gravação no Armazenamento do Azure. 
- A saída de dados refere-se a dados transferidos para fora de uma região do Azure. Quando os dados de sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, quer esse aplicativo seja um serviço de nuvem ou algum outro tipo de aplicativo, você é cobrado pela saída de dados. (Nos serviços do Azure, você pode tomar medidas para agrupar seus dados e serviços nos mesmos data centers para reduzir ou eliminar encargos de saída de dados.)  

Os [Detalhes de preços de armazenamento](http://www.windowsazure.com/pt-br/pricing/details/#storage) fornece informações detalhadas de preços para capacidade de armazenamento, replicação e transações. Os [Detalhes de preços de transferências de dados](http://www.windowsazure.com/pt-br/pricing/details/data-transfers/) fornecem informações detalhadas de preços para saída de dados.

Este artigo descreve como criar uma conta de armazenamento e algumas decisões a considerar ao criá-la. Também descreve como gerenciar as chaves de acesso da conta de armazenamento e como excluir uma conta de armazenamento.

## Sumário ##

- [Como: Criar uma conta de armazenamento](#create)
- [Como: Exibir, copiar e regenerar chaves de acesso de armazenamento](#regeneratestoragekeys)
- [Como: Excluir uma conta de armazenamento](#deletestorageaccount)


## <a id="create"></a>Como: Criar uma conta de armazenamento ##

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).

2. Clique em **Criar Novo**, em **Armazenamento** e em **Criação Rápida**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. Em **URL**, insira um nome para a conta de armazenamento. Consulte [Pontos de extremidade da conta de armazenamento](#account-endpoints) a seguir para detalhes sobre como esse nome será usado para abordar os objetos que você armazena no Armazenamento do Azure.

4. Em **Local/Grupo de afinidade**, selecione um local para a conta de armazenamento que esteja perto de você ou seus clientes. Se os dados na conta de armazenamento forem ser acessados de outro serviço do Azure, como uma máquina virtual ou serviço de nuvem do Azure, você pode desejar selecionar um grupo de afinidade na lista para agrupar sua conta no mesmo data center que outros serviços do Azure que você está usando para melhorar o desempenho e reduzir os custos. 

	> [WACOM.NOTE] Observe que você deve selecionar um grupo de afinidades quando sua conta de armazenamento for criada; se não puder, mova uma conta existente para um grupo de afinidade.

	Para detalhes sobre grupos de afinidade, consulte [Colocalização de serviços com um grupo de afinidade](#affinity-group) abaixo.

	
5. Se você tiver mais de uma assinatura do Azure, o campo **Assinatura** será exibido. Em **Assinatura**, digite a assinatura do Azure com a qual você deseja usar a conta de armazenamento.Você pode criar até cinco contas de armazenamento para uma assinatura.

6. Em **Replicação**, selecione o nível desejado de replicação para sua conta de armazenamento. A opção de replicação recomendada é a replicação georredundante, que fornece a máxima durabilidade para seus dados.Para mais detalhes sobre as opções de replicação de Armazenamento do Azure, consulte [Opções de replicação de conta de armazenamento](#replication-options) abaixo.

6. Clique em **Criar Conta de Armazenamento**.

	Pode levar alguns minutos para criar sua conta de armazenamento. Para verificar o status, você pode monitorar as notificações na parte inferior do portal. Depois que a conta de armazenamento for criada, sua nova conta de armazenamento terá o status **Online** e estará pronta para uso. 

![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)


### <a id="account-endpoints"></a>Pontos de extremidade da conta de armazenamento 

Cada objeto armazenado no Azure Storage possui um endereço de URL único; o nome da conta de armazenamento forma o subdomínio desse endereço. O subdomínio junto com o nome de domínio, que é específico para cada serviço, forma um *ponto de extremidade* para sua conta de armazenamento. 

Por exemplo, se a sua conta de armazenamento se chamar *mystorageaccount*, os pontos de extremidade padrão para a conta de armazenamento são: 

- Serviço Blob: http://*mystorageaccount*.blob.core.windows.net

- Serviço Tabela: http://*mystorageaccount*.table.core.windows.net

- Serviço Fila: http://*mystorageaccount*.queue.core.windows.net

- Serviço de arquivo: http://*mystorageaccount*.file.core.windows.net

É possível ver os pontos de extremidade para sua conta de armazenamento no Painel de armazenamento no Portal de Gerenciamento do Azure quando a conta tiver sido criada.

A URL para acessar um objeto em uma conta de armazenamento é compilada acrescentando o local do objeto na conta de armazenamento ao ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também é possível configurar um nome de domínio para usar com a conta de armazenamento. Consulte[ Configurar um nome de domínio personalizado para dados de blob em uma conta de armazenamento](http://azure.microsoft.com/pt-br/documentation/articles/storage-custom-domain-name/) para detalhes.

### <a id="affinity-group"></a>Colocalização de serviço com grupo de afinidade 

Um *grupo de afinidade* é um agrupamento geográfico de seus serviços Azure e VMs na conta de armazenamento do Azure. Um grupo de afinidade pode melhorar o desempenho do serviço localizando cargas de trabalho do computador no mesmo data center ou próximo do público-alvo de usuários. Além disso, não há encargos de cobrança incorridos para saída quando os dados em uma conta de armazenamento são acessados por um serviço em execução do mesmo grupo de afinidade.

> [WACOM.NOTE]  Para criar um grupo de afinidade, abra a área <b>Configurações</b> do Portal de gerenciamento, clique em <b>Grupos de afinidade</b>e, em seguida, clique no botão <b>Adicionar um grupo de afinidade</b> ou <b>Adicionar</b> . Você também pode criar e gerenciar os grupos de afinidade usando o Portal de Gerenciamento de Serviços do Azure. Consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/ee460798.aspx">Operações em grupos de afinidade</a> para obter mais informações.


### <a id="replication-options"></a>Opções de replicação de conta de armazenamento

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]


## <a id="regeneratestoragekeys"></a>Como: Exibir, copiar e regenerar chaves de acesso de armazenamento

Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ao fornecer as duas chaves de acesso de armazenamento, o Azure permite que você regenere as chaves sem interrupção para o serviço de armazenamento ou o acesso a esse serviço.

> [WACOM.NOTE] Recomendamos que você evite compartilhar suas chaves de acesso a contas de armazenamento com outras pessoas. Para permitir o acesso a recursos de armazenamento sem dar as suas chaves de acesso, você pode usar uma *assinatura de acesso compartilhado*. Uma assinatura de acesso compartilhado fornece acesso a um recurso na sua conta para um intervalo que você definir e com as permissões que você especificar. Consulte o [tutorial de assinatura de acesso compartilhado](../storage-dotnet-shared-access-signature-part-1/) para obter mais informações.

No [Portal de Gerenciamento](http://manage.windowsazure.com), use **Gerenciar Chaves** no painel ou a página **Armazenamento** para exibir, copiar e regenerar as chaves de acesso do armazenamento que são usadas para acessar os serviços de Fila, Blob e Tabela. 

### Copiar uma chave de acesso de armazenamento ###

Você pode usar **Gerenciar Chaves** para copiar uma chave de acesso de armazenamento para uso em uma cadeia de conexão. A cadeia de conexão requer o nome da conta de armazenamento e uma chave a ser usada na autenticação.Para obter informações sobre como configurar cadeias de conexão para acessar os serviços de armazenamento do Azure, consulte [Configurando cadeias de conexão](http://msdn.microsoft.com/pt-br/library/ee758697.aspx).

1. No [Portal de Gerenciamento](http://manage.windowsazure.com), clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2. Clique em **Gerenciar Chaves**.

 	**Gerenciar Chaves de Acesso** é aberta.

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

## <a id="deletestorageaccount"></a>Como: Excluir uma conta de armazenamento

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




<!--HONumber=35_1-->
