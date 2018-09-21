---
title: Gerenciar compartilhamentos de arquivos usando o Gerenciador de Armazenamento do Azure
description: Aprenda como usar o Gerenciador de Armazenamento do Azure para gerenciar arquivos do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 949d96bb1b5ffdc948737d4a47ffa14b2e344b5e
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574715"
---
# <a name="manage-azure-file-shares-with-azure-storage-explorer"></a>Gerenciar compartilhamentos de arquivos com o Gerenciador de Armazenamento do Azure 
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem fácil de usar da Microsoft. Este artigo percorre os fundamentos de trabalhar com compartilhamentos de arquivos do Azure usando o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). O Gerenciador de Armazenamento é uma ferramenta para o cliente popular disponível para Windows, macOS e Linux. Você pode usar o Gerenciador de Armazenamento para gerenciar compartilhamentos de arquivo e outros recursos de armazenamento.

Este guia de início rápido requer que o Gerenciador de Armazenamento esteja instalado. Para baixá-lo e instalá-lo, vá até [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

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
Você não pode usar o Gerenciador de Armazenamento para criar novos recursos. Para esta demonstração, crie a conta de armazenamento no [Portal do Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Conecte o Gerenciador de Armazenamento aos recursos do Azure
Quando você iniciar o Gerenciador de Armazenamento pela primeira vez, a janela **Gerenciador de Armazenamento do Microsoft Azure - Conectar** será exibida. O Gerenciador de Armazenamento fornece várias maneiras de se conectar às contas de armazenamento: 

- **Entre usando a sua conta do Azure**: Você pode entrar usando as credenciais do usuário para a sua organização ou a sua conta da Microsoft. 
- **Conecte-se a uma conta de armazenamento específica usando uma cadeia de conexão ou um token SAS**: Uma cadeia de conexão é uma cadeia de caracteres especial que contém um nome de conta de armazenamento e a chave da conta de armazenamento/token SAS. Com o token, o Gerenciador de Armazenamento acessa diretamente a conta de armazenamento (ao invés de simplesmente ver todas as contas de armazenamento em uma conta do Azure). Para saber mais sobre cadeias de conexão, confira [Configurar cadeia de conexão do Armazenamento do Azure](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Conectar-se a uma conta de armazenamento específico usando um nome de conta de armazenamento e uma chave**: use o nome e a chave da conta de armazenamento para se conectar ao armazenamento do Azure.

Para os fins deste início rápido, entre com sua conta do Azure. Selecione **Adicionar uma Conta do Azure** e, em seguida, selecione **Entrar**. Siga os prompts para entrar na sua conta do Azure.

![Uma captura de tela da janela Gerenciador de Armazenamento do Microsoft Azure – Conectar](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos
Para criar seu primeiro compartilhamento de arquivos do Azure na conta de armazenamento *storageacct<random number>*:

1. Expanda a conta de armazenamento que você criou.
2. Clique duas vezes em **Compartilhamentos de Arquivos** e selecione **Criar Compartilhamento de Arquivos**.  
    ![Uma captura de tela da pasta de compartilhamentos de arquivo e do menu de contexto no contexto](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Para o compartilhamento de arquivos, insira *myshare* e pressione Enter.

> [!IMPORTANT]  
> Os nomes de compartilhamento podem conter somente letras em minúsculas, números e hifens (mas não podem começar com um hífen). Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Após o compartilhamento de arquivos ter sido criado, uma guia para o seu compartilhamento de arquivos é exibida no painel à direita. 

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Trabalhar com o conteúdo de um compartilhamento de arquivos do Azure
Agora que você criou um compartilhamento de arquivos do Azure, pode montar o compartilhamento de arquivos com SMB no [Windows](storage-how-to-use-files-windows.md), no [Linux](storage-how-to-use-files-linux.md) ou no [macOS](storage-how-to-use-files-mac.md). Como alternativa, você pode trabalhar com o compartilhamento de arquivos do Azure usando a CLI do Azure. A vantagem de usar a CLI do Azure em vez de montar o compartilhamento de arquivos usando SMB é que todas as solicitações feitas através da CLI do Azure são realizadas com o uso da API REST File. Você pode usar a API REST para criar, modificar e excluir arquivos e diretórios em clientes que não possuam acesso ao SMB.

### <a name="create-a-directory"></a>Criar um diretório
A adição de um diretório fornece uma estrutura hierárquica para gerenciar o compartilhamento de arquivos. Você pode criar vários níveis no seu diretório. No entanto, você deve garantir a existência de todos os diretórios pai antes de criar subdiretórios. Por exemplo, para o caminho myDirectory/mySubDirectory, você deve primeiro criar o diretório *myDirectory*. Em seguida, você deve criar o subdiretório *mySubDirectory*. 

1. Na guia para o compartilhamento de arquivos, no menu superior, selecione o botão **Nova pasta**. O painel **Criar novo diretório** é exibido.
    ![Uma captura de tela do botão Nova pasta no contexto](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Para o nome do diretório, insira *myDirectory*, e selecione **OK**. 

O diretório *myDirectory* será listado na guia para o compartilhamento de arquivos *myshare*.

### <a name="upload-a-file"></a>Carregar um arquivo 
Você pode carregar um arquivo do computador local para o novo diretório em seu compartilhamento de arquivos. Você pode carregar uma pasta inteira ou um único arquivo.

1. No menu superior, selecione **Carregar**. Essa operação dá a opção de carregar um arquivo ou uma pasta.
2. Selecione **Carregar arquivo** e selecione um arquivo do computador local a ser carregado.
3. Em **Carregar em um diretório**, insira *myDirectory* e selecione **Carregar**. 

Quando terminar, o arquivo é exibido em uma lista no painel *myDirectory*.

### <a name="download-a-file"></a>Baixar um arquivo
Para baixar uma cópia de um arquivo do seu compartilhamento de arquios, clique com o botão direito no arquivo e, em seguida, selecione **Baixar**. Escolha onde deseja colocar o arquivo em seu computador local e selecione **Salvar**.

O andamento do download é exibido no painel **Atividades** na parte inferior da janela.

## <a name="create-and-modify-share-snapshots"></a>Criar e modificar instantâneos de compartilhamento
Um instantâneo preserva um ponto no tempo para uma cópia de um compartilhamento de arquivos do Azure. Instantâneos de compartilhamento de arquivos são semelhantes a outras tecnologias que você talvez já conheça:
- [Serviço de Cópias de Sombra de Volume (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) para sistemas de arquivos Windows, como NTFS e ReFS
- Instantâneos do [LVM (Gerenciador de Volumes Lógicos)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantâneos do [Sistema de arquivos da Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS

Para criar um instantâneo de compartilhamento:

1. Selecione a guia para o compartilhamento de arquivos*myshare*.
2. No menu superior, selecione **Criar instantâneo**. (Talvez você precise selecionar primeiro **Mais** para ver esta opção, dependendo das dimensões da janela no Gerenciador de Armazenamento.)  
    ![Uma captura de tela do botão Criar instantâneo no contexto](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Listar e procurar instantâneos de compartilhamento
Após o instantâneo ser criado, selecione **Exibir instantâneos para o compartilhamento de arquivos** para listar os instantâneos do compartilhamento. (Talvez você precise selecionar primeiro **Mais** para ver esta opção, dependendo das dimensões da janela no Gerenciador de Armazenamento.) Para navegar em um instantâneo de compartilhamento, clique duas vezes nele.

![Uma captura de tela da janela de navegação de instantâneos](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Restaurar de um instantâneo de compartilhamento
Para demonstrar como restaurar um arquivo de um instantâneo de compartilhamento, é necessário primeiro excluir um arquivo do compartilhamento de arquivos do Azure ativo. Vá até a pasta *myDirectory*, clique com o botão direito do mouse no arquivo carregado e selecione **Excluir**. Para restaurar esse arquivo do instantâneo de compartilhamento:

1. Selecione **Exibir instantâneos para compartilhamento de arquivos**. (Talvez você precise selecionar primeiro **Mais** para ver esta opção, dependendo das dimensões da janela no Gerenciador de Armazenamento.)
2. Na lista de instantâneos de compartilhamento, clique duas vezes no instantâneo de compartilhamento.
3. Procure o instantâneo, até encontrar o arquivo que foi excluído. Selecione o compartilhamento de arquivos e, em seguida, selecione **Restaurar instantâneo**. (Talvez você precise selecionar primeiro **Mais** para ver esta opção, dependendo das dimensões da janela no Gerenciador de Armazenamento.) Uma janela abre e exibe um aviso de que a restauração do arquivo substituirá o conteúdo do compartilhamento de arquivo e não poderá ser desfeita. Selecione **OK**.
4. O arquivo agora deve estar no seu lugar original no compartilhamento de arquivos do Azure ativo.

### <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento
Para excluir um instantâneo de compartilhamento, vá até a [lista de instantâneos de compartilhamento](#list-and-browse-share-snapshots). Clique no instantâneo de compartilhamento que deseja excluir e, em seguida, selecione **Excluir**.

## <a name="clean-up-resources"></a>Limpar recursos
Você não pode usar o Gerenciador de Armazenamento para remover recursos. Para limpar com este guia de início rápido, você pode usar o [portal do Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Próximas etapas
- [Gerenciar compartilhamentos de arquivos com o Portal do Azure](storage-how-to-use-files-portal.md)
- [Gerenciar compartilhamentos de arquivos com o Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gerenciar compartilhamentos de arquivos com a CLI do Azure](storage-how-to-use-files-cli.md)
- [Planejar uma implantação de Arquivos do Azure](storage-files-planning.md)