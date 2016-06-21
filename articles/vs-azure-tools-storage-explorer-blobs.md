<properties
	pageTitle="Gerenciar recursos do Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento (Preview) | Microsoft Azure"
	description="Gerenciar os contêineres de blob e blobs do Azure com o Gerenciador de Armazenamento (Preview)"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="06/05/2016"
	ms.author="tarcher" />

# Gerenciar os recursos de Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento (Preview)

## Visão geral

O [Armazenamento de Blobs do Azure](./storage/storage-dotnet-how-to-use-blobs.md) é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Você pode usar o armazenamento de Blob para expor dados publicamente para o mundo ou para armazenar dados do aplicativo de forma privada. Neste artigo, você aprenderá a usar o Gerenciador de Armazenamento (Preview) para trabalhar com contêineres de blob e blobs.

## Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

- [Baixe e instale o Gerenciador de Armazenamento (preview)](http://www.storageexplorer.com)
- [Conectar-se a uma conta de armazenamento do Azure ou serviço](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## Criar um contêiner de blob

Todos os blobs devem residir em um contêiner de blob, que é simplesmente um agrupamento lógico de blobs. Uma conta pode conter um número ilimitado de contêineres, e cada contêiner pode armazenar um número ilimitado de blobs.

As etapas a seguir ilustram como criar um contêiner de blobs no Gerenciador de Armazenamento (Preview).

1.	Abra o Gerenciador de Armazenamento (Preview).
1.	No painel esquerdo, expanda a conta de armazenamento na qual você deseja criar o contêiner de blobs.
1.	Clique com botão direito em **Contêineres de Blob** e, no menu de contexto, selecione **Criar Contêiner de Blobs**.

	![Menu de contexto Criar contêineres de blob][0]

1.	Uma caixa de texto será exibida abaixo da pasta **Contêineres de Blob**. Insira o nome de seu contêiner de blobs. Consulte a seção [Regras de nomenclatura do contêiner](./storage/storage-dotnet-how-to-use-blobs.md#create-a-container) para obter uma lista de regras e restrições sobre como nomear os contêineres de blob.

	![Caixa de texto Criar Contêineres de Blob][1]

1.	Pressione **Enter** quanto terminar de criar o contêiner de blobs, ou **Esc** para cancelar. Após a criação do contêiner de blobs, ele será exibido na pasta **Contêineres de Blob** da conta de armazenamento selecionada.

	![Contêiner de blob criado][2]

## Exibir o conteúdo de um contêiner de blobs

Os contêineres de blob contêm blobs e pastas (que também podem conter blobs).

As etapas a seguir ilustram como exibir o conteúdo de um contêiner de blobs no Gerenciador de Armazenamento (Preview):

1.	Abra o Gerenciador de Armazenamento (Preview).
1.	No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs que você deseja exibir.
1.	Expanda os **Contêineres de Blob** da conta de armazenamento.
1.	Clique com o botão direito no contêiner de blob que você deseja exibir e, no menu de contexto, selecione **Abrir Editor do Contêiner de Blobs**. Você também pode clicar duas vezes no contêiner de blobs que deseja exibir.

	![Menu de contexto Abrir editor do contêiner de blobs][19]

1.	O painel principal exibirá o conteúdo do contêiner de blobs.

	![Editor do Contêiner de blobs][3]

## Excluir um contêiner de blob

Os contêineres de blob podem ser facilmente criados e excluídos conforme o necessário. (Para saber como excluir blobs individuais, consulte a seção [Gerenciamento de blobs em um contêiner de blobs](./#managing-blobs-in-a-blob-container).)

As etapas a seguir ilustram como excluir um contêiner de blobs no Gerenciador de Armazenamento (Preview):

1.	Abra o Gerenciador de Armazenamento (Preview).
1.	No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs que você deseja exibir.
1.	Expanda os **Contêineres de Blob** da conta de armazenamento.
1.	Clique com o botão direito no contêiner de blobs que você deseja excluir e, no menu de contexto, escolha **Excluir**. Você também pode pressionar **Excluir** para excluir o contêiner de blobs selecionado atualmente.

	![Menu de contexto Excluir contêiner de blobs][4]

1.	Escolha **Sim** na caixa de diálogo de confirmação.

	![Confirmação de exclusão do contêiner de blobs][5]

## Copiar um contêiner de blobs

O Gerenciador de Armazenamento (Preview) permite que você copie um contêiner de blobs na área de transferência e cole esse contêiner de blobs em outra conta de armazenamento. (Para saber como copiar blobs individuais, consulte a seção [Gerenciamento de blobs em um contêiner de blobs](./#managing-blobs-in-a-blob-container).)

As etapas a seguir ilustram como copiar um contêiner de blobs de uma conta de armazenamento para outra.

1.	Abra o Gerenciador de Armazenamento (Preview).
1.	No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs que você deseja copiar.
1.	Expanda os **Contêineres de Blob** da conta de armazenamento.
1.	Clique com o botão direito no contêiner de blobs que você deseja copiar e, no menu de contexto, escolha **Copiar Contêiner de Blobs**.

	![Menu de contexto Copiar contêiner de blobs][6]

1.	Clique com botão direito na conta de armazenamento de "destino" na qual você deseja colar o contêiner de blobs e, no menu de contexto, escolha **Colar Contêiner de Blobs**.

	![Menu de contexto Colar contêiner de blobs][7]

## Obter a SAS para um contêiner de blob

Uma [SAS (Assinatura de Acesso Compartilhado)](./storage/storage-dotnet-shared-access-signature-part-1.md) fornece acesso delegado aos recursos da sua conta de armazenamento. Isso significa que você pode conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento por um período especificado e com um conjunto determinado de permissões, sem precisar compartilhar suas chaves de acesso de conta.

As etapas a seguir ilustram como criar uma SAS para um contêiner de blobs:

1.	Abra o Gerenciador de Armazenamento (Preview).
1.	No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs para o qual você deseja obter uma SAS.
1.	Expanda os **Contêineres de Blob** da conta de armazenamento.
1.	Clique com o botão direito no contêiner de blobs desejado e, no menu de contexto, escolha **Obter Assinatura de Acesso Compartilhado**.

	![Menu de contexto Obter SAS][8]

1.	Na caixa de diálogo **Assinatura de Acesso Compartilhado**, especifique a política, as datas de início e de vencimento, o fuso horário e os níveis de acesso que você deseja para o recurso.

	![Opções de Obter SAS][9]

1.	Ao terminar de especificar as opções de SAS, escolha **Criar**.

1.	Uma segunda caixa de diálogo de **Assinatura de Acesso Compartilhado** será exibida listando o contêiner de blobs juntamente com a URL e as QueryStrings que você pode usar para acessar o recurso de armazenamento. Escolha **Copiar** próximo à URL que você deseja copiar na área de transferência.

	![Copiar URLs de SAS][10]

1.	Ao terminar, escolha **Fechar**.

## Gerenciar políticas de acesso para um contêiner de blobs

As etapas a seguir ilustram como gerenciar (adicionar e remover) políticas de acesso para um contêiner de blobs:

1.	Abra o Gerenciador de Armazenamento (Preview).
1.	No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs cujas políticas de acesso você deseja gerenciar.
1.	Expanda os **Contêineres de Blob** da conta de armazenamento.
1.	Escolha o contêiner de blobs desejado e, no menu de contexto, escolha **Gerenciar Políticas de Acesso**.

	![Menu de contexto Gerenciar políticas de acesso][11]

1.	A caixa de diálogo **Políticas de Acesso** listará todas as políticas de acesso que já foram criadas para o contêiner de blobs selecionado.

	![Opções de Política de Acesso][12]

1.	Execute estas etapas, dependendo da tarefa de gerenciamento de política de acesso:

	- **Adicionar uma nova política de acesso** - Escolha **Adicionar**. Uma vez gerada, a caixa de diálogo **Políticas de Acesso** exibirá a política de acesso recém-adicionada (com configurações padrão).
	- **Editar uma política de acesso** - Faça as edições desejadas e escolha **Salvar**.
	- **Remover uma política de acesso** - Escolha **Remover** ao lado de política de acesso que você deseja remover.

## Definir o nível de acesso público para um contêiner de blobs

Por padrão, cada contêiner de blobs é definido como "Sem acesso público".

As etapas a seguir ilustram como especificar um nível de acesso público para um contêiner de blobs.

1.	Abra o Gerenciador de Armazenamento (Preview).
1.	No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs cujas políticas de acesso você deseja gerenciar.
1.	Expanda os **Contêineres de Blob** da conta de armazenamento.
1.	Escolha o contêiner de blobs desejado e, no menu de contexto, escolha **Definir o Nível de Acesso Público**.

	![Menu de contexto Definir nível de acesso público][13]

1.	Na caixa de diálogo **Definir o Nível de Acesso Público do Contêiner**, especifique o nível de acesso desejado.

	![Opções de Definir nível de acesso público][14]

1.	Escolha **Aplicar**.

## Gerenciamento de blobs em um contêiner de blobs

Depois de criar um contêiner de blob, você pode carregar um blob nesse contêiner de blob, baixar um blob em seu computador local, abrir um blob em seu computador local e muito mais.

As etapas a seguir ilustram como gerenciar os blobs (e pastas) dentro de um contêiner de blobs.

1.	Abra o Gerenciador de Armazenamento (Preview).
1.	No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs que você deseja gerenciar.
1.	Expanda os **Contêineres de Blob** da conta de armazenamento.
1.	Clique duas vezes no contêiner de blobs que você deseja exibir.
1.	O painel principal exibirá o conteúdo do contêiner de blobs.

	![Exibir contêiner de blobs][3]

1.	O painel principal exibirá o conteúdo do contêiner de blobs.

1.	Execute estas etapas, dependendo da tarefa que deseja executar:

	- **Carregar arquivos em um contêiner de blobs**

		1.	Na barra de ferramentas do painel principal, escolha **Carregar** e **Carregar Arquivos** no menu suspenso.

			![Menu Carregar arquivos][15]

		1.	Na caixa de diálogo **Carregar arquivos**, escolha o botão de reticências (**...**) no lado direito da caixa de texto **Arquivos** para selecionar os arquivos que você deseja carregar.

			![Opções de Carregar arquivos][16]

		1.	Especifique o tipo de **Tipo de blob**. O artigo [Introdução ao Armazenamento de Blobs do Azure usando o .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explica as diferenças entre os vários tipos de blob.

		1.	Como opção, especifique uma pasta de destino na qual os arquivos selecionados serão carregados. Se a pasta de destino não existir, ela será criada.

		1.	Escolha **Carregar**.

	- **Carregar uma pasta em um contêiner de blobs**

		1.	Na barra de ferramentas do painel principal, escolha **Carregar** e **Carregar Pasta** no menu suspenso.

			![Menu Carregar pasta][17]

		1.	Na caixa de diálogo **Carregar pasta**, escolha o botão de reticências (**...**) no lado direito da caixa de texto **Pasta** para selecionar a pasta cujo conteúdo você deseja carregar.

			![Opções de Carregar pasta][18]

		1.	Especifique o tipo de **Tipo de blob**. O artigo [Introdução ao Armazenamento de Blobs do Azure usando o .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explica as diferenças entre os vários tipos de blob.

		1.	Como opção, especifique uma pasta de destino na qual o conteúdo da pasta selecionada será carregado. Se a pasta de destino não existir, ela será criada.

		1.	Escolha **Carregar**.

	- **Baixar um blob em seu computador local**

		1.	Selecione o blob que você deseja baixar.

		1.	Na barra de ferramentas do painel principal, escolha **Baixar**.

		1.	Na caixa de diálogo **Especifique onde salvar o blob baixado**, especifique o local onde você deseja baixar o blob, e o nome que deseja dar a ele.

		1.	Selecione **Salvar**.

	- **Abrir um blob em seu computador local**

		1.	Selecione o blob que você deseja abrir.

		1.	Na barra de ferramentas do painel principal, escolha **Abrir**.

		1.	O blob será baixado e aberto usando o aplicativo associado ao tipo de arquivo subjacente do blob.

	- **Copiar um blob na área de transferência**

		1.	Escolha o blob que você deseja copiar.

		1.	Na barra de ferramentas do painel principal, escolha **Copiar**.

		1.	No painel esquerdo, navegue até outro contêiner de blobs e clique duas vezes nele para exibi-lo no painel principal.

		1.	Na barra de ferramentas do painel principal, escolha **Colar** para criar uma cópia do blob.

	- **Excluir um blob**

		1.	Selecione o blob que você deseja excluir.

		1.	Na barra de ferramentas do painel principal, escolha **Excluir**.

		1.	Escolha **Sim** na caixa de diálogo de confirmação.

## Próximas etapas

- Veja as [Notas de versão e vídeos mais recentes do Gerenciador de Armazenamento (Preview)](http://www.storageexplorer.com).
- Saiba como [criar aplicativos usando os blobs, tabelas, filas e arquivos do Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png

<!---HONumber=AcomDC_0608_2016-->