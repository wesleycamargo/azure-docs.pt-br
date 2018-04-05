---
title: Gerenciando compartilhamentos de arquivos com o Gerenciador de Armazenamento do Azure
description: Aprenda a usar o Gerenciador de Armazenamento do Azure para gerenciar arquivos do Azure.
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
ms.date: 02/27/2018
ms.author: wgries
ms.openlocfilehash: 0c16d3b0ef0b178f99eaafecd74eabb00cca5af9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-azure-storage-explorer"></a>Gerenciando compartilhamentos de arquivos com o Gerenciador de Armazenamento do Azure 
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem fácil de usar da Microsoft. Este guia percorre os fundamentos de trabalhar com compartilhamentos de arquivos do Azure usando o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). O Gerenciador de Armazenamento do Azure é uma ferramenta de cliente popular disponível para Windows, macOS e Linux para gerenciar compartilhamentos de arquivos do Azure e outros recursos de armazenamento.

Este guia de início rápido requer que o Gerenciador de Armazenamento do Azure esteja instalado. Se você precisa instalá-lo, visite o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para baixá-lo.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma conta de armazenamento
> * Criar um compartilhamento de arquivos do Azure 
> * Criar um diretório
> * Carregar um arquivo
> * Baixar um arquivo
> * Criar e usar um instantâneo de compartilhamento

Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
O Gerenciador de Armazenamento do Azure não tem a capacidade de criar novos recursos e, portanto, para fins desta demonstração, crie a conta de armazenamento com o [Portal do Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connecting-azure-storage-explorer-to-azure-resources"></a>Conectando o Gerenciador de Armazenamento do Azure a recursos do Azure
Na primeira inicialização, é exibida a janela **Gerenciador de Armazenamento do Microsoft Azure – Conectar**. O Gerenciador de Armazenamento do Azure fornece várias maneiras de se conectar às contas de armazenamento: 

- **Logon por meio da conta do Azure**: permite a você fazer logon com suas credenciais de usuário da sua conta da Microsoft ou da sua organização. 
- **Conectar-se a uma conta de armazenamento específica com uma cadeia de conexão ou um token SAS**: uma cadeia de conexão é a cadeia de caracteres especial que contém a conta de armazenamento e o nome do token SAS/da chave de conta que permite que o Gerenciador de Armazenamento do Azure acesse diretamente a conta de armazenamento (em vez de simplesmente ver todas as contas de armazenamento dentro de uma conta do Azure). Para saber mais sobre cadeias de conexão, confira [Configurar cadeia de conexão do Armazenamento do Azure](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Conectar-se a uma conta de armazenamento específico com um nome de conta de armazenamento e uma chave**: use o nome e a chave da conta de armazenamento para se conectar ao armazenamento do Azure.

Para os fins deste início rápido, faça logon com sua conta do Azure. Selecione **Adicionar uma Conta do Azure** e clique em **Entrar**. Siga os avisos da tela para entrar na sua conta do Azure.

![Gerenciador de Armazenamento do Microsoft Azure – Janela Conexão](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos
Para criar seu primeiro compartilhamento de arquivos do Azure na conta de armazenamento *storageacct<random number>* :

1. Expanda a conta de armazenamento que você criou.
2. Clique duas vezes em **Compartilhamentos de Arquivos** e selecione **Criar Compartilhamento de Arquivos**.  
    ![Uma captura de tela da pasta de compartilhamentos de arquivo e do menu de contexto no contexto](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Digite *myshare* para o arquivo de compartilhamento e pressione **Enter**.

> [!Important]  
> Os nomes de compartilhamento precisam ter somente letras em minúsculas, números e hifens, mas não podem começar com um hífen. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Quando o compartilhamento de arquivos for criado, o painel direito abrirá uma guia para ele. 

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Manipulando o conteúdo do compartilhamento de Arquivos do Azure
Agora que você criou um compartilhamento de arquivos do Azure, pode montar o compartilhamento de arquivos com SMB no [Windows](storage-how-to-use-files-windows.md), no [Linux](storage-how-to-use-files-linux.md) ou no [macOS](storage-how-to-use-files-mac.md). Como alternativa, você pode manipular o compartilhamento de arquivos do Azure com o Portal do Azure. Todas as solicitações feitas por meio do Portal do Azure são feitas com a API REST do arquivo, permitindo a você criar, modificar e excluir arquivos e diretórios nos clientes sem acesso ao SMB.

### <a name="create-a-directory"></a>Criar um diretório
A adição de um diretório fornece uma estrutura hierárquica para gerenciar o compartilhamento de arquivos. Você pode criar vários níveis, mas deve fazer com que todos os diretórios pai existam antes de criar um subdiretório. Por exemplo, para o caminho myDirectory/mySubDirectory, você deve primeiro criar o diretório *myDirectory* e depois criar *mySubDirectory*. 

1. Na guia para o compartilhamento de arquivos, no menu superior, clique no botão **+ Nova Pasta**. A página **Criar Novo Diretório** será aberta.
    ![Uma captura de tela do botão Nova pasta no contexto](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Digite *myDirectory* como o nome e clique em **OK**. 

O diretório *myDirectory* será listado na guia para o compartilhamento de arquivos *myshare*.

### <a name="upload-a-file"></a>Carregar um arquivo 
Carregue um arquivo do computador local para o novo diretório em seu compartilhamento de arquivos. Você pode carregar uma pasta inteira ou apenas um único arquivo.

1. No menu na parte superior, selecione **Carregar**. Essa operação dá a opção de carregar um arquivo ou uma pasta.

2. Selecione **Carregar arquivo** e escolha um arquivo do computador local a ser carregado.

3. Em **Carregar em um diretório**, digite *myDirectory* e clique em **Carregar**. 

Quando terminar, o arquivo deverá aparecer na lista da página **myDirectory**.

### <a name="download-a-file"></a>Baixar um arquivo
Você pode baixar uma cópia de um arquivo em seu compartilhamento de arquivo clicando com o botão direito do mouse no arquivo e selecionando **Baixar**. Escolha onde deseja colocar o arquivo em seu computador local e clique em **Salvar**.

Você verá o andamento do download no painel **Atividades** na parte inferior da janela.

## <a name="create-and-modify-share-snapshots"></a>Criar e modificar instantâneos de compartilhamento
Um instantâneo preserva um ponto no tempo para uma cópia de um compartilhamento de arquivos do Azure. Instantâneos de compartilhamento de arquivos são semelhantes a outras tecnologias que você talvez já conheça, como:
- [VSS (Serviço de Cópias de Sombra de Volume)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) para sistemas de arquivos Windows, como NTFS e ReFS.
- Instantâneos do [LVM (Gerenciador de Volumes Lógicos)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantâneos do [APFS (Sistema de arquivos da Apple)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 

Para criar um instantâneo de compartilhamento:

1. Abra a guia para o compartilhamento de arquivos*myshare*.
2. No menu na parte superior da guia, clique em **Criar Instantâneo** (ele pode estar oculto por trás de um **... Mais** , dependendo das dimensões da janela do Gerenciador de Armazenamento do Azure).  
    ![Uma captura de tela do botão de criação de instantâneo no contexto](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Listar e procurar instantâneos de compartilhamento
Quando o instantâneo é criado, você pode clicar em **Exibir Instantâneos para Compartilhamento de Arquivos** (ele pode estar oculto por trás de um **... Mais**, dependendo das dimensões da janela do Gerenciador de Armazenamento do Azure) para listar os instantâneos do compartilhamento. Clique duas vezes em um instantâneo de compartilhamento para navegar por ele.

![Uma captura de tela da tela de procura de instantâneos](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Restaurar de um instantâneo de compartilhamento
Para demonstrar a restauração de um arquivo de um instantâneo de compartilhamento, é necessário primeiro excluir um arquivo do compartilhamento de arquivos do Azure ativo. Navegue até a pasta *myDirectory*, clique com botão direito do mouse no arquivo carregado e clique em **Excluir**. Em seguida, para restaurar esse arquivo do instantâneo de compartilhamento:

1. Clique no **Exibir Instantâneos para Compartilhamento de Arquivos** (ele pode estar oculto por trás de um **... Mais** , dependendo das dimensões da janela do Gerenciador de Armazenamento do Azure).
2. Selecione um instantâneo de compartilhamento na lista e clique duas vezes para navegar por ele.
3. Percorra o instantâneo até encontrar o arquivo excluído, selecione-o e clique em **Restaurar Instantâneo** (ele pode estar oculto por trás de um **... Mais** , dependendo das dimensões da janela do Gerenciador de Armazenamento do Azure). Você receberá um aviso de que a restauração do arquivo substituirá o conteúdo do compartilhamento de arquivo e não poderá ser desfeita. Selecione **OK**.
4. O arquivo agora deve estar no seu lugar original no compartilhamento de arquivos do Azure ativo.

### <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento
Para excluir um instantâneo de compartilhamento, [navegue até a lista de instantâneos de compartilhamento](#list-and-browse-share-snapshots). Clique com o botão direito do mouse no instantâneo de compartilhamento que você deseja excluir e selecione Excluir.

## <a name="clean-up-resources"></a>Limpar recursos
O Gerenciador de Armazenamento do Azure não tem a capacidade de remover recursos; você pode limpar os vestígios deste guia de início rápido no [Portal do Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Próximas etapas
- [Gerenciando compartilhamentos de arquivos com o Portal do Azure](storage-how-to-use-files-portal.md)
- [Gerenciar compartilhamentos de arquivos com o Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gerenciando compartilhamentos de arquivos com a CLI do Azure](storage-how-to-use-files-cli.md)
- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)