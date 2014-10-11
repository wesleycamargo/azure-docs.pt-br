<properties linkid="manage-services-how-to-manage-a-storage-account" urlDisplayName="How to manage" pageTitle="How to manage storage accounts | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="Learn how to manage storage accounts in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Manage Storage Accounts" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# <span id="managestorageaccounts"></span></a>Como gerenciar contas de armazenamento

## Sumário

-   [Como: Gerenciar a replicação de uma conta de armazenamento][]
-   [Como: Exibir, copiar e regenerar chaves de acesso de armazenamento][]
-   [Como: Excluir uma conta de armazenamento][]

## <span id="georeplication"></span></a>Como: Replicar os dados de sua conta de armazenamento para durabilidade e alta disponibilidade

[WACOM.INCLUDE [storage-replication-options][]]

### Para especificar as configurações de replicação para uma conta de armazenamento

1.  No [Portal de Gerenciamento do Azure][], clique em **Armazenamento** e no nome de sua conta de armazenamento para exibir o painel.

2.  Clique em **Configurar**.

3.  No campo **Replicação**, selecione o tipo de replicação desejado para sua conta de armazenamento.

4.  Clique em **Salvar** e, se solicitado, confirme a sua escolha.

## <span id="regeneratestoragekeys"></span></a>Como: Exibir, copiar e regenerar chaves de acesso de armazenamento

Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ao fornecer as duas chaves de acesso de armazenamento, o Azure permite que você regenere as chaves sem interrupção para o serviço de armazenamento ou o acesso a esse serviço.

No [Portal de Gerenciamento][], use **Gerenciar Chaves** no painel ou a página **Armazenamento** para exibir, copiar e regenerar as chaves de acesso do armazenamento que são usadas para acessar os serviços de Fila, Blob e Tabela.

### Copiar uma chave de acesso de armazenamento

Você pode usar **Gerenciar Chaves** para copiar uma chave de acesso de armazenamento para uso em uma cadeia de conexão. A cadeia de conexão requer o nome da conta de armazenamento e uma chave a ser usada na autenticação. Para obter informações sobre como configurar cadeias de conexão para acessar os serviços de armazenamento do Azure, consulte [Configurando cadeias de conexão][].

1.  No [Portal de Gerenciamento][], clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2.  Clique em **Gerenciar Chaves**.

    **Gerenciar Chaves de Acesso** é aberta.

    ![Managekeys][]

3.  Para copiar uma chave de acesso de armazenamento, selecione o texto da chave. Em seguida, clique com o botão direito do mouse e clique em **Copiar**.

### Regenerar chaves de acesso de armazenamento

Você deve alterar as chaves de acesso de sua conta de armazenamento periodicamente para ajudar a manter as conexões de armazenamento mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra chave de acesso.

<div class="dev-callout"> 
    <b>Aviso</b> 
    <p>Regenerar suas chaves de acesso afeta as m&aacute;quinas virtuais, os servi&ccedil;os de m&iacute;dia e quaisquer aplicativos que dependam da conta de armazenamento. Todos os clientes que usam a chave de acesso para acessar a conta de armazenamento precisam ser atualizados para usar a nova chave.
    </p> 
</div>

**Máquinas virtuais** - Se sua conta de armazenamento contiver máquinas virtuais em execução, será necessário reimplantar todas as máquinas virtuais após regenerar as chaves de acesso. Para evitar a reimplantação, desligue as máquinas virtuais antes de regenerar as chaves de acesso.

**Serviços de mídia** - Se você tiver serviços de mídia dependentes de sua conta de armazenamento, deverá ressincronizar as chaves de acesso com o serviço de mídia depois de regenerar as chaves.

**Aplicativos** - se tiver aplicativos Web ou serviços de nuvem que usam a conta de armazenamento, você perderá as conexões se regenerar chaves, a menos que você as reverta. Este é o processo:

1.  Atualize as cadeias de conexão no código do aplicativo para fazer referência à chave de acesso secundária da conta de armazenamento.

2.  Regenere a chave de acesso primária de sua conta de armazenamento. No [Portal de Gerenciamento][], no painel ou na página **Configurar**, clique em **Gerenciar Chave**. Clique em **Regenerar** sob a chave de acesso primária e, em seguida, clique em **Sim** para confirmar que você deseja gerar uma nova chave.

3.  Atualize as cadeias de conexão em seu código para fazer referência à nova chave de acesso primária.

4.  Regenere a chave de acesso secundária.

## <span id="deletestorageaccount"></span></a>Como: Excluir uma conta de armazenamento

Para remover uma conta de armazenamento que você não esteja mais usando, use **Excluir** no painel ou na página **Configurar**. **Excluir** exclui a conta de armazenamento inteira, incluindo todos os blobs, tabelas e filas da conta.

<div class="dev-callout">
    <b>Aviso</b>
    <p>N&atilde;o &eacute; poss&iacute;vel restaurar o conte&uacute;do de uma conta de armazenamento exclu&iacute;da. Fa&ccedil;a 
    backup de todas as informa&ccedil;&otilde;es que voc&ecirc; deseja salvar antes de excluir a conta.
    </p>
    <p>
    Se sua conta de armazenamento contiver quaisquer arquivos ou discos VHD para uma m&aacute;quina 
    virtual do Azure, voc&ecirc; dever&aacute; excluir as imagens e os discos que est&atilde;o usando esses arquivos VHD 
    antes de excluir a conta de armazenamento. Em primeiro lugar, pare a m&aacute;quina virtual se ela estiver em execu&ccedil;&atilde;o e exclua-a. Para excluir discos, navegue at&eacute; a guia Discos e exclua todos os discos contidos na conta de armazenamento. Para excluir imagens, navegue at&eacute; a guia Imagens e exclua todas as imagens armazenadas na conta.
    </p>
</div>

1.  No [Portal de Gerenciamento][], clique em **Armazenamento**.

2.  Clique em qualquer lugar na entrada da conta de armazenamento, exceto no nome, e em **Excluir**.

    -Ou-

    Clique no nome da conta de armazenamento para abrir o painel e, em seguida, clique em **Excluir**.

3.  Clique em **Sim** para confirmar que deseja excluir a conta de armazenamento.

  [Como: Gerenciar a replicação de uma conta de armazenamento]: #georeplication
  [Como: Exibir, copiar e regenerar chaves de acesso de armazenamento]: #regeneratestoragekeys
  [Como: Excluir uma conta de armazenamento]: #deletestorageaccount
  [storage-replication-options]: ../includes/storage-replication-options.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Portal de Gerenciamento]: http://manage.windowsazure.com
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/en-us/library/ee758697.aspx
  [Managekeys]: ./media/storage-manage-storage-account/Storage_ManageKeys.png
