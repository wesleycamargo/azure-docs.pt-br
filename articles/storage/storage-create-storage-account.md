<properties
	pageTitle="Como criar, gerenciar ou excluir uma conta de armazenamento no Portal do Azure | Microsoft Azure"
	description="Crie uma nova conta de armazenamento, gerencie as chaves de acesso de sua conta ou exclua uma conta de armazenamento no Portal do Azure. Saiba mais sobre contas de armazenamento standard e premium."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/14/2016"
	ms.author="robinsh"/>


# Sobre as contas de armazenamento do Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## Visão geral

Uma conta de armazenamento do Azure fornece acesso aos serviços de Blob, Fila, Tabela e Arquivo no Armazenamento do Azure. Sua conta de armazenamento fornece um namespace exclusivo para os objetos de dados de Armazenamento do Azure. Por padrão, os dados em sua conta estão disponíveis somente para você, o proprietário da conta.

Existem dois tipos de contas de armazenamento:

- Uma conta de armazenamento padrão inclui armazenamento de Blob, Tabela e Fila.
- Atualmente, uma conta de armazenamento premium dá suporte apenas aos discos de máquina virtual do Azure. Confira [Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure](storage-premium-storage.md) para ter uma visão geral detalhada do Armazenamento Premium.

## Cobrança de conta de armazenamento

Você é cobrado pelo uso do Armazenamento do Azure com base na sua conta de armazenamento. Os custos de armazenamento baseiam-se em quatro fatores: capacidade de armazenamento, esquema de replicação, transações de armazenamento e saída de dados.

- A capacidade do armazenamento refere-se a quanto de sua alocação de conta de armazenamento você está usando para armazenar dados. O custo de simplesmente armazenar seus dados é determinado pela quantidade de dados que você está armazenando e como eles são replicados.
- A replicação determina quantas cópias dos seus dados são mantidas de uma só vez e em quais locais.
- As transações referem-se a todas as operações de leitura e gravação no Armazenamento do Azure.
- A saída de dados refere-se a dados transferidos para fora de uma região do Azure. Quando os dados de sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, quer esse aplicativo seja um serviço de nuvem ou algum outro tipo de aplicativo, você é cobrado pela saída de dados. (Nos serviços do Azure, você pode tomar medidas para agrupar seus dados e serviços nos mesmos data centers para reduzir ou eliminar encargos de saída de dados.)  

A página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage) fornece informações detalhadas de preços para a capacidade de armazenamento, replicação e transações. A página [Detalhes dos Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas dos preços para a saída de dados.

Para obter detalhes sobre a capacidade e produtividade da conta de armazenamento, confira [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md).

> [AZURE.NOTE] Ao criar uma máquina virtual do Azure, uma conta de armazenamento será criada automaticamente no local da implantação se você ainda não tiver uma conta de armazenamento nesse local. Assim, não é necessário seguir as etapas abaixo para criar uma conta de armazenamento para seus discos da máquina virtual. O nome da conta de armazenamento será baseado no nome da máquina virtual. Confira a [Documentação de máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais detalhes.

## Pontos de extremidade da conta de armazenamento

Cada objeto armazenado no Armazenamento do Azure tem um endereço de URL exclusivo. O nome da conta de armazenamento forma o subdomínio desse endereço. A combinação do nome do subdomínio e do domínio, que é específico a cada serviço, forma um *ponto de extremidade* para a sua conta de armazenamento.

Por exemplo, se a sua conta de armazenamento se chamar *mystorageaccount*, os pontos de extremidade padrão para a conta de armazenamento serão:

- Serviço blob: http://*mystorageaccount*.blob.core.windows.net

- Serviço de tabela: http://*mystorageaccount*.table.core.windows.net

- Serviço de fila: http://*mystorageaccount*.queue.core.windows.net

- Serviço de arquivo: http://*mystorageaccount*.file.core.windows.net

A URL para acessar um objeto em uma conta de armazenamento é compilada acrescentando o local do objeto na conta de armazenamento ao ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também é possível configurar um nome de domínio para usar com a conta de armazenamento. Para as contas de armazenamento Clássico, confira [Configurar um Nome de domínio personalizado para o Ponto de Extremidade do Armazenamento de Blob ](storage-custom-domain-name.md) para obter detalhes. Para as contas de armazenamento do ARM, esse recurso ainda não foi adicionado ao [Portal do Azure](https://portal.azure.com), mas você pode configurá-lo com o PowerShell. Para obter mais informações, confira o cmdlet [AzureRmStorageAccount conjunto](https://msdn.microsoft.com/library/mt607146.aspx).

## Criar uma conta de armazenamento

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, selecione **Novo** -> **Dados + Armazenamento** -> **Conta de armazenamento**.

3. Insira um nome para a conta de armazenamento.

	> [AZURE.NOTE] Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.
	>  
	> O nome da sua conta de armazenamento deve ser exclusivo no Azure. O Portal do Azure indicará se o nome da conta de armazenamento que você selecionou já está em uso.

	Confira [Pontos de extremidade da conta de armazenamento](#storage-account-endpoints) abaixo para obter detalhes sobre como o nome da conta de armazenamento será usado em seus objetos no Armazenamento do Azure.

4. Selecione um modelo de implantação a ser usado: **Resource Manager** ou **Clássico**. O **Gerenciador de Recursos** é o modelo de implantação recomendado. Para saber mais, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../resource-manager-deployment-model.md).

5. Especifique o nível de desempenho para a conta de armazenamento como **Standard** ou **Premium**. O padrão é **Standard**. Para obter mais detalhes sobre as contas de armazenamento standard e premium, confira [Introdução ao Armazenamento do Microsoft Azure](storage-introduction.md) e [Armazenamento Premium: Armazenamento de Alto Desempenho para Cargas de Trabalho da Máquina Virtual do Azure](storage-premium-storage.md).

6. Selecione a opção de replicação para a conta de armazenamento: **LRS**, **GRS**, **RA-GRS** ou **ZRS**. O padrão é **RA-GRS**. Para obter mais detalhes sobre as opções de replicação do Armazenamento do Azure, confira [Replicação do Armazenamento do Azure](storage-redundancy.md).

7. Selecione a assinatura na qual você deseja criar a nova conta de armazenamento.

8. Especifique um novo grupo de recursos ou selecione um grupo de recursos existente. Para saber mais sobre os grupos de recursos, confira [Usando o Portal do Azure para gerenciar os recursos do Azure](../azure-portal/resource-group-portal.md).

9. Selecione a região geográfica para sua conta de armazenamento.

10. Clique em **Criar** para criar a conta de armazenamento.

## Gerenciar sua conta de armazenamento

### Alterar a configuração da conta

Depois de criar sua conta de armazenamento, você pode modificar sua configuração, por exemplo, alterando a opção de replicação usada para a conta. No Portal do Azure, navegue até a conta de armazenamento, clique em **Todas as configurações** e clique em **Configuração** para exibir e/ou alterar a configuração da conta. A alteração da opção de replicação mudará seu preço.

> [AZURE.NOTE] Dependendo do nível de desempenho que você escolheu durante a criação da conta de armazenamento, talvez algumas opções de replicação não estejam disponíveis.

### Gerenciar as chaves de acesso de armazenamento

Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ao fornecer as duas chaves de acesso de armazenamento, o Azure permite que você regenere as chaves sem interrupção para o serviço de armazenamento ou o acesso a esse serviço.

> [AZURE.NOTE] Recomendamos que você evite compartilhar suas chaves de acesso de armazenamento com outras pessoas. Para permitir o acesso a recursos de armazenamento sem dar as suas chaves de acesso, você pode usar uma *assinatura de acesso compartilhado*. Uma assinatura de acesso compartilhado fornece acesso a um recurso na sua conta para um intervalo que você definir e com as permissões que você especificar. Confira [Assinaturas de Acesso Compartilhado: Compreender o modelo SAS](storage-dotnet-shared-access-signature-part-1.md) para obter mais informações.

#### Exibir e copiar as chaves de acesso de armazenamento

No [Portal do Azure](https://portal.azure.com), navegue até a conta de armazenamento, clique em **Todas as configurações** e clique em **Chaves de acesso** para exibir, copiar e gerar novamente as chaves de acesso de sua conta. A folha **Chaves de Acesso** também inclui cadeias de conexão pré-configuradas usando suas chaves primária e secundária, que você pode copiar para usar em seus aplicativos.

#### Regenerar chaves de acesso de armazenamento

Você deve alterar as chaves de acesso de sua conta de armazenamento periodicamente para ajudar a manter as conexões de armazenamento seguras. Duas chaves de acesso são atribuídas para que você possa manter conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra.

> [AZURE.WARNING] Regenerar suas chaves de acesso afeta as máquinas virtuais, os serviços de mídia e quaisquer aplicativos que dependam da conta de armazenamento. Todos os clientes que usam a chave de acesso para acessar a conta de armazenamento precisam ser atualizados para usar a nova chave.

**Máquinas virtuais** - Se sua conta de armazenamento contiver máquinas virtuais em execução, será necessário reimplantar todas as máquinas virtuais após regenerar as chaves de acesso. Para evitar a reimplantação, desligue as máquinas virtuais antes de regenerar as chaves de acesso.

**Serviços de mídia** - se algum serviço de mídia depender de sua conta de armazenamento, você deverá ressincronizar as chaves de acesso com o serviço de mídia depois de regenerá-las.

**Aplicativos** - se você tiver algum aplicativo Web ou serviços de nuvem que usam a conta de armazenamento, perderá as conexões se regenerar as chaves, a menos que você as reverta.

Veja o processo para trocar suas chaves de acesso de armazenamento:

1. Atualize as cadeias de conexão no código do aplicativo para fazer referência à chave de acesso secundária da conta de armazenamento.

2. Regenere a chave de acesso primária de sua conta de armazenamento. Na folha **Chaves de Acesso**, clique em **Regenerar Chave1** e clique em **Sim** para confirmar que você deseja gerar uma nova chave.

3. Atualize as cadeias de conexão em seu código para fazer referência à nova chave de acesso primária.

4. Regenere a chave de acesso secundária da mesma maneira.

## Excluir uma conta de armazenamento

Para remover uma conta de armazenamento que você não estiver mais usando, navegue até a conta de armazenamento do [Portal do Azure](https://portal.azure.com) e clique em **Excluir**. A exclusão de uma conta de armazenamento exclui a conta inteira, incluindo todos os dados na conta.

> [AZURE.WARNING] Não é possível restaurar uma conta de armazenamento excluída nem recuperar nenhuma parte de seu conteúdo antes da exclusão. Não se esqueça de fazer backup de todas as informações que você deseja salvar antes de excluir a conta. Isso também é verdadeiro para todos os recursos na conta – depois que você excluir um blob, tabela, fila ou arquivo, ele será excluído permanentemente.

Para excluir uma conta de armazenamento que está associada a uma máquina virtual do Azure, primeiro você deve verificar se quaisquer discos de máquina virtual foram excluídos. Se você não excluir os discos de máquina virtual primeiro, durante a tentativa de excluir sua conta de armazenamento, você verá uma mensagem de erro semelhante a:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Se a conta de armazenamento usar o modelo de implantação Clássico, você poderá remover o disco da máquina virtual seguindo estas etapas no [Portal do Azure](https://manage.windowsazure.com):

1. Navegue até o [Portal Clássico](https://manage.windowsazure.com).
2. Navegue até a guia Máquinas Virtuais.
3. Clique na guia Discos.
4. Selecione o disco de dados e clique em Excluir o Disco.
5. Para excluir imagens de disco, navegue até a guia Imagens e exclua todas as imagens armazenadas na conta.

Para saber mais, confira a [Documentação da máquina virtual do Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## Próximas etapas

- [Replicação de Armazenamento do Azure](storage-redundancy.md)
- [Configurar cadeias de conexão do Armazenamento do Azure](storage-configure-connection-string.md)
- [Transferir dados com o Utilitário da Linha de Comando AzCopy](storage-use-azcopy.md)
- Visite o [Blog da equipe do Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/).

<!---HONumber=AcomDC_0406_2016-->