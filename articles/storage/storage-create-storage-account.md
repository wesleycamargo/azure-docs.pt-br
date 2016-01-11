<properties
	pageTitle="Como criar, gerenciar ou excluir uma conta de armazenamento | Microsoft Azure"
	description="Crie uma nova conta de armazenamento, gerencie suas chaves de acesso ou exclua uma conta de armazenamento no Portal do Azure."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="12/04/2015" 
	ms.author="robinsh"/>


# Sobre as contas de armazenamento do Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## Visão geral

Uma conta de armazenamento do Azure fornece acesso aos serviços de Blob, Fila, Tabela e Arquivo no Armazenamento do Azure. Sua conta de armazenamento fornece um namespace exclusivo para os objetos de dados de Armazenamento do Azure. Por padrão, os dados em sua conta estão disponíveis somente para você, o proprietário da conta.

Existem dois tipos de contas de armazenamento:

- Uma conta de armazenamento padrão inclui armazenamento de Blob, Tabela e Fila. 
- Atualmente, uma conta de armazenamento premium dá suporte apenas aos discos de máquina virtual do Azure. Veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure](storage-premium-storage-preview-portal.md) para obter uma visão geral detalhada do Armazenamento Premium.

## Cobrança de conta de armazenamento

Você é cobrado pelo uso do Armazenamento do Azure com base na sua conta de armazenamento. Os custos de armazenamento baseiam-se em quatro fatores: capacidade de armazenamento, esquema de replicação, transações de armazenamento e saída de dados.

- A capacidade do armazenamento refere-se a quanto de sua alocação de conta de armazenamento você está usando para armazenar dados. O custo de simplesmente armazenar seus dados é determinado pela quantidade de dados que você está armazenando e como eles são replicados.
- A replicação determina quantas cópias dos seus dados são mantidas de uma só vez e em quais locais.
- As transações referem-se a todas as operações de leitura e gravação no Armazenamento do Azure.
- A saída de dados refere-se a dados transferidos para fora de uma região do Azure. Quando os dados de sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, quer esse aplicativo seja um serviço de nuvem ou algum outro tipo de aplicativo, você é cobrado pela saída de dados. (Nos serviços do Azure, você pode tomar medidas para agrupar seus dados e serviços nos mesmos data centers para reduzir ou eliminar encargos de saída de dados.)  

A página [Preços do Azure](http://azure.microsoft.com/pricing/details/#storage) fornece informações detalhadas de preços para capacidade de armazenamento, replicação e transações. A página [Detalhes de preços de transferências de dados](http://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas de preços para a saída de dados.

Para obter detalhes sobre a capacidade da conta de armazenamento e as metas de desempenho, confira [Escalabilidade e metas de desempenho do Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx).

> [AZURE.NOTE]Ao criar uma máquina virtual do Azure, uma conta de armazenamento será criada automaticamente no local da implantação se você ainda não tiver uma conta de armazenamento nesse local. Assim, não é necessário seguir as etapas abaixo para criar uma conta de armazenamento para seus discos da máquina virtual. O nome da conta de armazenamento será baseado no nome da máquina virtual. Confira a [Documentação de máquinas virtuais do Azure](http://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais detalhes.

## Pontos de extremidade da conta de armazenamento

Cada objeto armazenado no Armazenamento do Azure tem um endereço de URL exclusivo. O nome da conta de armazenamento forma o subdomínio desse endereço. A combinação do nome do subdomínio e do domínio, que é específico a cada serviço, forma um *ponto de extremidade* para a sua conta de armazenamento.

Por exemplo, se a sua conta de armazenamento se chamar *mystorageaccount*, os pontos de extremidade padrão para a conta de armazenamento serão:

- Serviço blob: http://*mystorageaccount*.blob.core.windows.net

- Serviço de tabela: http://*mystorageaccount*.table.core.windows.net

- Serviço de fila: http://*mystorageaccount*.queue.core.windows.net

- Serviço de arquivo: http://*mystorageaccount*.file.core.windows.net

A URL para acessar um objeto em uma conta de armazenamento é compilada acrescentando o local do objeto na conta de armazenamento ao ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também é possível configurar um nome de domínio para usar com a conta de armazenamento. Confira [Configurar um nome de domínio personalizado para dados de blob em uma conta de armazenamento do Azure](storage-custom-domain-name.md) para obter mais detalhes.

## Criar uma conta de armazenamento

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, selecione **Novo** -> **Dados + Armazenamento** -> **Conta de armazenamento**.

3. Selecione um modelo de implantação: **Gerenciador de Recursos** ou **Clássico**. O **Gerenciador de Recursos** é o modelo de implantação recomendado. Para saber mais, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../resource-manager-deployment-model.md).

4. Insira um nome para a conta de armazenamento.

	> [AZURE.NOTE]Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.
	>  
	> O nome da sua conta de armazenamento deve ser exclusivo no Azure. O Portal do Azure indicará se o nome de conta de armazenamento que você selecionar já é usado.

	Confira [Pontos de extremidade da conta de armazenamento](#storage-account-endpoints) abaixo para obter detalhes sobre como o nome da conta de armazenamento será usado em seus objetos no Armazenamento do Azure.

5. Especifique o tipo de conta de armazenamento a ser criada. O tipo de conta de armazenamento determina como a conta de armazenamento é replicada e se ela é uma conta de armazenamento standard ou uma conta de armazenamento premium.

	O tipo de conta de armazenamento standard é **Standard-RAGRS**, que é uma conta de armazenamento standard com replicação de redundância geográfica com acesso de leitura. Esse tipo de conta de armazenamento é replicado para uma região secundária a centenas de milhas de distância da região primária e fornece acesso de leitura para o local secundário.

	Para obter mais detalhes sobre as opções de replicação do Armazenamento do Azure, confira [Replicação do Armazenamento do Azure](storage-redundancy.md). Para obter mais detalhes sobre contas de armazenamento standard e premium, veja [Introdução ao armazenamento](storage-introduction.md) e [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure](storage-premium-storage-preview-portal.md)

6. Indique se deseja habilitar o Diagnóstico para sua conta de armazenamento. Os diagnósticos incluem logs e métricas do Storage Analytics.

7. Se você tiver mais de uma assinatura do Azure, o campo **Assinatura** será exibido. Selecione a assinatura na qual você deseja criar a nova conta de armazenamento.

8. Especifique um novo grupo de recursos ou selecione um grupo de recursos existente. Para saber mais sobre grupos de recursos, veja [Usando o Portal do Azure para gerenciar os recursos do Azure](../resource-group-portal.md).

9. Selecione a região geográfica para sua conta de armazenamento.

10. Clique em **Criar** para criar a conta de armazenamento.

## Gerenciar as chaves de acesso de armazenamento

Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ao fornecer as duas chaves de acesso de armazenamento, o Azure permite que você regenere as chaves sem interrupção para o serviço de armazenamento ou o acesso a esse serviço.

> [AZURE.NOTE]Recomendamos que você evite compartilhar suas chaves de acesso de armazenamento com outras pessoas. Para permitir o acesso a recursos de armazenamento sem dar as suas chaves de acesso, você pode usar uma *assinatura de acesso compartilhado*. Uma assinatura de acesso compartilhado fornece acesso a um recurso na sua conta para um intervalo que você definir e com as permissões que você especificar. Consulte o [Tutorial de assinatura de acesso compartilhado](storage-dotnet-shared-access-signature-part-1.md) para obter mais informações.

### Exibir e copiar as chaves de acesso de armazenamento

No [Portal do Azure](https://portal.azure.com), navegue até a conta de armazenamento e clique no ícone **Chaves** para exibir, copiar e regenerar as chaves de acesso da conta. A folha **Chaves de Acesso** também inclui cadeias de conexão pré-configuradas usando suas chaves primária e secundária, que você pode copiar para usar em seus aplicativos.

### Regenerar chaves de acesso de armazenamento

Você deve alterar as chaves de acesso de sua conta de armazenamento periodicamente para ajudar a manter as conexões de armazenamento seguras. Duas chaves de acesso são atribuídas para que você possa manter conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra.

> [AZURE.WARNING]Regenerar suas chaves de acesso afeta as máquinas virtuais, os serviços de mídia e quaisquer aplicativos que dependam da conta de armazenamento. Todos os clientes que usam a chave de acesso para acessar a conta de armazenamento precisam ser atualizados para usar a nova chave.

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

> [AZURE.WARNING]Não é possível restaurar uma conta de armazenamento excluída nem recuperar nenhuma parte de seu conteúdo antes da exclusão. Não se esqueça de fazer backup de todas as informações que você deseja salvar antes de excluir a conta. Isso também é verdadeiro para todos os recursos na conta – depois que você excluir um blob, tabela, fila ou arquivo, ele será excluído permanentemente.

## Próximas etapas

- Para saber mais sobre o Armazenamento do Azure, consulte a documentação do Armazenamento do Azure em [Azure.com](http://azure.microsoft.com/documentation/services/storage/).
- Visite o [Blog da equipe do Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy)

<!---HONumber=AcomDC_1223_2015-->