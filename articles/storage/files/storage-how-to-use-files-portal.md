---
title: Gerenciando compartilhamentos de arquivos com o Portal do Azure
description: Saiba como usar o portal do Azure para gerenciar os Arquivos do Azure.
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 588d260bb939c8f6439ca66828296ea455f1524a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-the-azure-portal"></a>Gerenciando compartilhamentos de arquivos com o Portal do Azure 
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem de fácil acesso da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados no Windows, no Linux e no macOS. Este guia percorre os fundamentos de trabalhar com compartilhamentos de arquivos do Azure usando o [Portal do Azure](https://portal.azure.com/). Saiba como:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma conta de armazenamento
> * Criar um compartilhamento de arquivos do Azure 
> * Criar um diretório
> * Carregar um arquivo 
> * Baixar um arquivo
> * Criar e usar um instantâneo de compartilhamento

Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos
Para criar um compartilhamento de arquivos:

1. Selecione a conta de armazenamento no seu painel.
2. Na página da conta de armazenamento, na seção **Serviços**, selecione **Arquivos**.
    ![Uma captura de tela da seção de serviços da conta de armazenamento; selecione o Serviço de arquivos](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. No menu na parte superior da página **Serviço de arquivos**, clique em **+ Compartilhamento de arquivos**. O menu suspenso da página **Novo compartilhamento de arquivos**.
4. Em **Nome**, digite *myshare*.
5. Clique em **OK** para criar o compartilhamento de arquivos do Azure.

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Manipulando o conteúdo do compartilhamento de Arquivos do Azure
Agora que você criou um compartilhamento de arquivos do Azure, pode montar o compartilhamento de arquivos com SMB no [Windows](storage-how-to-use-files-windows.md), no [Linux](storage-how-to-use-files-linux.md) ou no [macOS](storage-how-to-use-files-mac.md). Como alternativa, você pode manipular o compartilhamento de arquivos do Azure com o Portal do Azure. Todas as solicitações feitas por meio do Portal do Azure são feitas com a API REST do arquivo, permitindo a você criar, modificar e excluir arquivos e diretórios nos clientes sem acesso ao SMB.

### <a name="create-directory"></a>Criar diretório
Para criar um novo diretório chamado *myDirectory* na raiz do seu compartilhamento de arquivos do Azure:

1. Na página **Serviço de arquivos**, selecione o compartilhamento de arquivos **myshare**. A página do compartilhamento de arquivos é aberta.
2. No menu na parte superior da página, selecione **+ Adicionar diretório**. Menu suspenso da página **Novo diretório**.
3. Digite *myDirectory* e clique em **OK**.

### <a name="upload-a-file"></a>Carregar um arquivo 
Para demonstrar o upload de um arquivo, primeiro crie ou selecione um arquivo a ser carregado. Você pode fazer isso por qualquer meio que desejar. Depois de selecionar o arquivo que você deseja carregar:

1. Clique no diretório **myDirectory**. O painel **myDirectory** é aberto.
2. No menu na parte superior, clique em **Carregar**. O painel **Carregar arquivos** é aberto.  
    ![Uma captura de tela do painel de upload de arquivos](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Clique no ícone de pasta para abrir uma janela e procurar seus arquivos locais. 
4. Selecione um arquivo e clique em **Abrir**. 
5. Na página **Carregar arquivos**, verifique o nome do arquivo e clique em **Carregar**.
6. Quando terminar, o arquivo deverá aparecer na lista da página **myDirectory**.

### <a name="download-a-file"></a>Baixar um arquivo
Você pode baixar uma cópia do arquivo carregado clicando com o botão direito do mouse no arquivo. Depois de clicar no botão de download, a experiência exata dependerá do sistema operacional e do navegador que você está usando.

## <a name="create-and-modify-share-snapshots"></a>Criar e modificar instantâneos de compartilhamento
Outra tarefa útil que você pode fazer com um compartilhamento de arquivos do Azure é criar instantâneos de compartilhamento. Um instantâneo preserva um ponto no tempo para um compartilhamento de arquivos do Azure. Os instantâneos de compartilhamento são semelhantes às tecnologias de sistema operacional que você talvez já conheça, como:
- [VSS (Serviço de Cópias de Sombra de Volume)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) para sistemas de arquivos Windows, como NTFS e ReFS
- Instantâneos do [LVM (Gerenciador de Volumes Lógicos)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantâneos do [APFS (Sistema de arquivos da Apple)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 

Para criar um instantâneo de compartilhamento:

1. Abra a página do compartilhamento de arquivos abrindo a conta de armazenamento no seu painel > **Arquivos** > **myshare**. 
2. Na página do compartilhamento de arquivos, clique no botão **Instantâneo** no menu na parte superior da página e selecione **Criar um instantâneo**.  
    ![Uma captura de tela que ilustra como encontrar o botão Criar instantâneo](media/storage-how-to-use-files-portal/create-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Listar e procurar instantâneos de compartilhamento
Quando o instantâneo é criado, você pode clicar em **Instantâneo** novamente e selecionar **Exibir instantâneos** para listar os instantâneos do compartilhamento. O painel resultante mostrará os instantâneos para esse compartilhamento. Clique em um instantâneo de compartilhamento para navegar por ele.

### <a name="restore-from-a-share-snapshot"></a>Restaurar de um instantâneo de compartilhamento
Para demonstrar a restauração de um arquivo de um instantâneo de compartilhamento, é necessário primeiro excluir um arquivo do compartilhamento de arquivos do Azure ativo. Navegue até a pasta *myDirectory*, clique com botão direito do mouse no arquivo carregado e clique em **Excluir**. Em seguida, para restaurar esse arquivo do instantâneo de compartilhamento:

1. Clique em **Instantâneos** no menu superior e selecione **Exibir instantâneos**. 
2. Clique no instantâneo criado anteriormente e ele abrirá o conteúdo em uma nova página. 
3. Clique em **myDirectory** no instantâneo e você verá o arquivo que foi excluído. 
4. Clique com botão direito do mouse no arquivo excluído e selecione **Restaurar**.
5. Um pop-up será exibido oferecendo a opção de restaurar o arquivo como uma cópia ou substituindo o arquivo original. Como excluímos o arquivo original, podemos selecionar **Substituir arquivo original** para restaurar o arquivo. Clique em **OK** para restaurar o arquivo no compartilhamento de arquivos do Azure.  
    ![Uma captura de tela do diálogo de restauração do arquivo](media/storage-how-to-use-files-portal/restore-snapshot-1.png)

6. Depois que o arquivo for restaurado, feche a página do instantâneo, volte para **myshare** > **myDirectory** e o arquivo deverá estar em seu local original.

### <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento
Para excluir um instantâneo de compartilhamento, [navegue até a lista de instantâneos de compartilhamento](#list-and-browse-a-share-snapshot). Clique na caixa de seleção ao lado do nome do instantâneo de compartilhamento e selecione o botão **Excluir**.

![Uma captura de tela da exclusão de um instantâneo de compartilhamento](media/storage-how-to-use-files-portal/delete-snapshot-1.png)

## <a name="clean-up-resources"></a>Limpar recursos
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Próximas etapas
- [Gerenciar compartilhamentos de arquivos com o Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gerenciando compartilhamentos de arquivos com a CLI do Azure](storage-how-to-use-files-cli.md)
- [Gerenciando compartilhamentos de arquivos com o Gerenciador de Armazenamento do Azure](storage-how-to-use-files-storage-explorer.md)
- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)