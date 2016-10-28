<properties
	pageTitle="Solucionar problemas de exclusão de contas de armazenamento, contêineres ou VHDs do Azure | Microsoft Azure"
	description="Solucionar problemas excluindo contas de armazenamento, contêineres ou VHDs do Azure"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor="tysonn"
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="minet;genli;robinsh"/>

# Solucionar problemas excluindo contas de armazenamento, contêineres ou VHDs do Azure

## Resumo
Você pode receber erros ao tentar excluir a conta de armazenamento, o contêiner ou o VHD do Azure no [portal do Azure](https://portal.azure.com/) ou no [portal clássico do Azure](https://manage.windowsazure.com/). Os problemas podem ser causados pelas seguintes circunstâncias:

-	Ao excluir uma VM, o disco e o VHD não são automaticamente excluídos. Esse pode ser o motivo da falha na exclusão da conta de armazenamento. Não excluímos o disco para que você possa usá-lo para montar outra VM.

-	Ainda há uma concessão em um disco ou o blob que está associado ao disco.

Se o problema do Azure não for resolvido neste artigo, visite os fóruns do Azure no [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou no @AzureSupport no Twitter. Além disso, você pode registrar uma solicitação de suporte do Azure escolhendo **Obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/).

## Resolução
Para resolver os problemas mais comuns, tente o seguinte método:

1. Alterne para o [portal clássico do Azure](https://manage.windowsazure.com/).
2. Selecione **MÁQUINA VIRTUAL** > **DISCOS**.

	![Imagem de discos em máquinas virtuais no portal clássico do Azure.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Localize os discos associados à conta de armazenamento, ao contêiner ou ao VHD que você quer excluir. Ao verificar o local do disco, você encontrará a conta de armazenamento, o contêiner ou o VHD associado.

	![Imagem que mostra informações sobre o local para os discos no portal clássico do Azure](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Verifique se não há VM listada no campo **Conectado a** dos discos e então exclua os discos.

 	> [AZURE.NOTE] Se um disco estiver conectado a uma VM, você não poderá excluí-lo. Discos são desconectados de uma VM excluída assincronamente. A limpeza desse campo pode levar alguns minutos depois da exclusão de uma VM.

5. Escolha **MÁQUINA VIRTUAL** > **IMAGENS** e exclua as imagens associadas à conta de armazenamento, ao contêiner ou ao VHD.

	Depois disso, tente excluir a conta de armazenamento, o contêiner ou o VHD novamente.

> [AZURE.WARNING] Não se esqueça de fazer backup de todas as informações que você deseja salvar antes de excluir a conta. Não é possível restaurar uma conta de armazenamento excluída nem recuperar qualquer conteúdo que ela tinha antes da exclusão. Isso também vale para todos os recursos na conta: depois de excluir um VHD, um blob, uma tabela, uma fila ou um arquivo, eles são permanentemente excluídos. Assegure-se de que o recurso não esteja em uso.

## Problemas comuns

A seção a seguir lista erros comuns que você pode receber ao tentar excluir contas de armazenamento, contêineres ou VHDs do Azure.

### Cenário 1: Não é possível excluir uma conta de armazenamento

Ao navegar para a conta de armazenamento do [portal do Azure](https://portal.azure.com/) ou do [portal clássico do Azure](https://manage.windowsazure.com/) e selecionar **Excluir**, verá a seguinte mensagem de erro:

**No portal do Azure**:

*Falha ao excluir a conta de armazenamento <vm-storage-account-name>. Não é possível excluir a conta de armazenamento <vm-storage-account-name>: a conta de armazenamento <vm-storage-account-name> tem algumas imagens e/ou discos ativos. Garanta que esses discos e/ou essas imagens sejam removidos antes de excluir essa conta de armazenamento.'*.

**No Portal Clássico do Azure**:

*A conta de armazenamento <vm-storage-account-name> tem algumas imagens e/ou discos ativos, por exemplo, xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Garanta que esses discos e/ou essas imagens sejam removidos antes de excluir essa conta de armazenamento.*

Você também poderá ver este erro:

**No Portal do Azure**:

*A conta de armazenamento <vm-storage-account-name> tem um contêiner com artefatos de imagens e/ou discos ativos. Garanta que esses artefatos sejam removidos do repositório de imagens antes de excluir essa conta de armazenamento*.

**No Portal Clássico do Azure**:

*A conta de armazenamento com falha no envio <vm-storage-account-name> tem um contêiner com artefatos de imagens e/ou discos ativos. Garanta que esses artefatos sejam removidos do repositório de imagens antes de excluir essa conta de armazenamento. Quando você tentar excluir uma conta de armazenamento e ainda houver discos ativos associados a ela, uma mensagem informando que há discos ativos que precisam ser excluídos será exibida*.

### Cenário 2: Não é possível excluir um contêiner

Ao tentar excluir o contêiner de armazenamento, o seguinte erro poderá ser exibido:

*Falha ao excluir o contêiner de armazenamento <nome do contêiner>. Erro: 'Atualmente, há uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação*.

### Cenário 3: Não é possível excluir um VHD

Depois de excluir uma VM e então tentar excluir os blobs para os VHDs associados, você pode receber a seguinte mensagem:

*Falha ao excluir o blob 'path/XXXXXX-XXXXXX-os-1447379084699.vhd'. Erro: 'Atualmente, há uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação*.

## Sobre o status Parado (desalocado)

As VMs que foram criadas no modelo de implantação clássico e retidas terão o status **Parado (desalocado)** no [Portal do Azure](https://portal.azure.com/) ou no [Portal Clássico do Azure](https://manage.windowsazure.com/).

**Portal Clássico do Azure**:

![Status Parado (Desalocado) para VMs no portal do Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Portal do Azure**:

![Status Parado (desalocado) para VMs no portal clássico do Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Um status "Parado (desalocado)" libera recursos do computador, como CPU, memória e rede. No entanto, os discos ainda são mantidos para que o usuário possa rapidamente recriar a VM, se necessário. Esses discos são criados sobre os VHDs cujos backups são feitos pelo armazenamento do Azure. A conta de armazenamento tem esses VHDs e os discos têm concessões nesses VHDs.

## Próximas etapas

- [Excluir uma conta de armazenamento](storage-create-storage-account.md#delete-a-storage-account)
- [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Como interromper a liberação de bloqueio do armazenamento de blobs no Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0921_2016-->