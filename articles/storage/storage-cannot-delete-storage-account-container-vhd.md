<properties
	pageTitle="Solucionar problemas excluindo contas de armazenamento, contêineres ou VHDs do Azure | Microsoft Azure"
	description="Solucionar problemas excluindo contas de armazenamento, contêineres ou VHDs do Azure"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor=""
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/20/2016"
	ms.author="genli"/>

# Solucionar problemas excluindo contas de armazenamento, contêineres ou VHDs do Azure

## Resumo
Você pode receber erros quando tentar excluir a conta de armazenamento, o contêiner ou o VHD do Azure no [portal do Azure](https://portal.azure.com/) ou no [portal clássico do Azure](https://manage.windowsazure.com/). Motivos dos problemas:

-	Quando você exclui uma VM, o disco e o VHD não são excluídos automaticamente, e isso pode ser o motivo da falha na exclusão da conta de armazenamento. Não excluímos o disco para que você possa usá-lo para montar outra VM.

-	Mas ainda há uma concessão em um disco ou o blob associado no disco.

Se o problema do Azure não for resolvido neste artigo, visite os fóruns do Azure no [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou no @AzureSupport no Twitter. Além disso, você pode registrar uma solicitação de suporte do Azure, escolhendo **Obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/).

## Resolução
Para resolver os problemas mais comuns, tente o seguinte método:

1. Alterne para o [portal clássico do Azure](https://manage.windowsazure.com/).
2. Escolha **MÁQUINA VIRTUAL** > **DISCOS**.

	![disk.png](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Localize os discos associados à conta de armazenamento, ao contêiner ou ao VHD que você quer excluir. Verifique o local do disco; você encontrará a conta de armazenamento, o contêiner e o VHD associados.

	![location](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Verifique se não há VMs listadas no campo **Conectado a** dos discos e exclua os discos.

 	> [AZURE.NOTE] Se um disco estiver se conectando a uma VM, você não poderá excluí-lo. Os discos são desconectados de uma VM excluída de modo assíncrono; alguns minutos podem ser necessários para que o campo seja limpo depois que VM é excluída.

5. Escolha **MÁQUINA VIRTUAL** > **IMAGENS** e exclua as imagens que estão associadas à conta de armazenamento, ao contêiner ou ao VHD.

Depois disso, tente excluir a conta de armazenamento, o contêiner ou o VHD novamente.

> [AZURE.WARNING] Não se esqueça de fazer backup de todas as informações que você deseja salvar antes de excluir a conta. Não é possível restaurar uma conta de armazenamento excluída nem recuperar qualquer conteúdo que ela tinha antes da exclusão. Isso também vale para todos os recursos na conta — uma vez que você exclui um VHD, um blob, uma tabela, uma fila ou um arquivo, eles são permanentemente excluídos. Assegure-se de que o recurso não esteja em uso.

## Sintoma

A seção a seguir lista erros comuns que você pode receber ao tentar excluir contas de armazenamento, contêineres ou VHDs do Azure.

### Cenário 1 Não é possível excluir uma conta de armazenamento

Quando você tenta excluir uma conta de armazenamento que não está mais usando navegando até a conta no [portal do Azure](https://portal.azure.com/) ou no [portal clássico do Azure](https://manage.windowsazure.com/) e seleciona Excluir, talvez a seguinte mensagem de erro seja exibida:

**No portal do Azure**:

*Falha ao excluir a conta de armazenamento<vm-storage-account-name>. Não é possível excluir a conta de armazenamento <vm-storage-account-name>: 'A conta de armazenamento <vm-storage-account-name> tem algum(a) disco e/ou imagem ativo(a). Garanta que esses discos e/ou essas imagens sejam removidos(as) antes de excluir essa conta de armazenamento.'*.

**No portal clássico do Azure**:

*A conta de armazenamento <vm-storage-account-name> tem algum(a) disco e/ou imagem ativo(a), por exemplo, xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Garanta que esses discos e/ou essas imagens sejam removidos(as) antes de excluir essa conta de armazenamento.*

Você também pode ver este erro:

**No portal do Azure**:

*A conta de armazenamento <vm-storage-account-name> tem 1 contêiner que tem artefatos de imagem e/ou disco ativo. Garanta que esses artefatos sejam removidos do repositório de imagens antes de excluir essa conta de armazenamento*.

**No portal clássico do Azure**:

*A conta de armazenamento com falha no envio <vm-storage-account-name> tem 1 contêiner que tem artefatos de imagem e/ou disco ativo. Garanta que esses artefatos sejam removidos do repositório de imagens antes de excluir essa conta de armazenamento. Quando você tentar excluir uma conta de armazenamento e ainda houver discos ativos associados a ela, uma mensagem informando que há discos ativos que precisam ser excluídos será exibida*.

### Cenário 2 Não é possível excluir um contêiner

Quando você tenta excluir o contêiner de armazenamento, o seguinte erro poderá ser exibido:

*Falha ao excluir o contêiner de armazenamento <container name>. Erro: 'Atualmente, há uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação*.

### Cenário 3 Não é possível excluir um VHD

Depois de excluir uma VM, você tenta excluir os blobs dos VHDs associados e recebe a mensagem abaixo:

*Falha ao excluir o blob 'caminho/XXXXXX-XXXXXX-os-1447379084699.vhd'. Erro: 'Atualmente, há uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação*.

## Mais informações

VMs V1 que foram retidas serão mostradas no estado parado desalocado no [portal do Azure](https://portal.azure.com/) ou no [portal clássico do Azure](https://manage.windowsazure.com/).

**Portal clássico do Azure**:

![screenshot1](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

**Portal do Azure**:

![screenshot2](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

Um status de "Parado (desalocado)" libera os recursos de computação, como CPU, memória e rede, mas os discos ainda ficam retidos para que o usuário possa recriar rapidamente a VM se for necessário. Esses discos são criados sobre os VHDs cujos backups são feitos pelo armazenamento do Azure. A conta de armazenamento tem esses VHDs e os discos têm concessões nesses VHDs.

## Referências

- [Delete a Storage Account (Excluir uma conta do Armazenamento)](storage-create-storage-account.md#delete-a-storage-account)
- [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Como interromper a liberação de bloqueio do armazenamento de blobs no Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0323_2016-->