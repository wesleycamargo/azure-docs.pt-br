<properties
	pageTitle="Gerenciar recursos de armazenamento do Microsoft Azure com o Gerenciador de Armazenamento (Visualização) | Microsoft Azure"
	description="Descreve como usar o Gerenciador de Armazenamento do Microsoft Azure (Visualização) para criar e gerenciar recursos de armazenamento do Azure."
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
	ms.date="05/08/2016"
	ms.author="tarcher" />

# Gerenciar os recursos de armazenamento do Azure com o Gerenciador de Armazenamento (Visualização)

O Gerenciador de Armazenamento do Microsoft Azure (Visualização) é uma ferramenta autônoma que ajuda você a gerenciar com facilidade suas contas de armazenamento do Azure. Ele será útil em situações em que se deseja gerenciar rapidamente o armazenamento fora do Portal do Azure, como quando você estiver desenvolvendo aplicativos no Visual Studio. Essa versão de visualização permite que você trabalhe facilmente com o armazenamento de blobs. Você pode criar e excluir contêineres, carregar, baixar e excluir blobs e pesquisar em todos os contêineres e blobs. Os recursos avançados permitem que os desenvolvedores e operadores trabalhem com políticas e chaves SAS. Os desenvolvedores do Windows também podem usar o emulador de armazenamento do Azure para testar seus códigos usando a conta de armazenamento de Desenvolvimento Local.

Para exibir ou gerenciar recursos de armazenamento no Gerenciador de Armazenamento, será preciso acessar uma conta de armazenamento do Azure, em sua assinatura ou em uma conta de armazenamento externa. Se você não tiver uma conta de armazenamento, crie uma em apenas alguns minutos. Se você tiver uma assinatura do MSDN, confira [Crédito mensal do Azure para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, confira [Um mês de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

## Gerenciar assinaturas e contas do Azure

Para ver os recursos de armazenamento do Azure no Gerenciador de Armazenamento, você precisará fazer logon em uma conta do Azure com uma ou mais assinaturas ativas. Se você tiver mais de uma conta do Azure, adicione-as ao Gerenciador de Armazenamento e escolha as assinaturas que você deseja incluir no modo de exibição de recursos do Gerenciador de Armazenamento. Se você nunca tiver usado o Azure ou se não tiver adicionado as contas necessárias ao Visual Studio, você receberá uma solicitação para fazer logon em uma conta do Azure.

### Adicionar uma conta do Azure ao Gerenciador de Armazenamento

1.	Escolha o ícone **Configurações** (engrenagem) na barra de ferramentas do Gerenciador de Armazenamento.
1.	Escolha o link **Adicionar uma conta**. Faça logon na conta do Azure cujos recursos de armazenamento você deseja procurar. A conta que você acabou de adicionar deve ser selecionada na lista suspensa seletora de conta. Todas as assinaturas para essa conta aparecem sob a entrada de conta.

	![][0]

1.	Marque as caixas de seleção para as assinaturas de conta que você deseja procurar e escolha o botão **Aplicar**.

	![][1]

	Os recursos de armazenamento do Azure para as assinaturas selecionadas aparecem no Gerenciador de Armazenamento.

### Anexar um armazenamento externo

1. Obtenha o nome da conta e a chave da conta de armazenamento que você deseja anexar.
	1.	No [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), escolha a conta de armazenamento que você deseja anexar.
	1.	Na seção **Gerenciar** do painel **Configurações**, escolha o botão **Chaves**.
	1.	Copie os valores de **Nome da Conta de Armazenamento** e **Tecla de Acesso Primária**.

		![][2]

1.	No menu de atalho do nó **Contas de Armazenamento** no Gerenciador de Armazenamento, escolha o comando **Anexar Armazenamento Externo**.

	![][3]

1. Insira o Nome da Conta de Armazenamento na caixa **Nome da conta** e a Tecla de Acesso Primária na caixa **Chave de conta**. Escolha o botão **Ok** para continuar.

	![][4]

	O armazenamento externo é exibida no Gerenciador de Armazenamento.

	![][5]

1. Para remover o armazenamento externo, no menu de atalho do armazenamento externo, escolha o comando Desanexar.

	![][6]

## Exibir e navegar pelos recursos de armazenamento

Para navegar até um recurso de armazenamento do Azure e exibir suas informações no Gerenciador de Armazenamento, expanda o tipo de armazenamento e escolha o recurso. As informações sobre o recurso selecionado aparecem nas guias **Ações** e **Propriedades** na parte inferior do Gerenciador de Armazenamento.

![][7]

-	A guia **Ações** exibe as ações que você pode executar no Gerenciador de Armazenamento para o recurso de armazenamento selecionado, como abrir, copiar ou excluir. As ações também aparecem no menu de atalho do recurso.

-	A guia **Propriedades** exibe as propriedades do recurso de armazenamento, como o tipo, a localidade, o grupo de recursos associado e a URL.

Todas as contas de armazenamento têm a ação **Abrir no portal**. Quando você escolhe essa ação, o Gerenciador de Armazenamento mostra a conta de armazenamento selecionada no portal de visualização do Azure.

Ações adicionais e valores de propriedade são exibidos de acordo com o recurso selecionado. Por exemplo, os nós Contêineres de Blob, Filas e Tabelas têm uma ação **Criar**. Os itens individuais (como contêineres de blob) têm ações como **Abrir**, **Excluir** e **Obter Assinatura de Acesso Compartilhado**. As ações para abrir o editor de blob aparecem quando você escolhe um blob da conta de armazenamento.

## Pesquisar contas de armazenamento e contêineres de blob

Para localizar as contas de armazenamento e os contêineres de blob com um nome específico em suas assinaturas de conta do Azure, insira o nome na caixa **Pesquisa** no Gerenciador de Armazenamento.

![][8]

Enquanto você insere os caracteres na caixa **Pesquisa**, somente as contas de armazenamento ou os contêineres de blob com nomes que correspondem a esses caracteres aparecem na árvore de recursos. Para limpar a pesquisa, escolha o botão **x** na caixa **Pesquisa**.

## Editar contas de armazenamento

Para adicionar ou alterar o conteúdo de uma conta de armazenamento, escolha o comando **Abrir Editor** para esse tipo de armazenamento. Você pode escolher ações no menu de atalho do item selecionado ou na guia **Ações** na parte inferior do Gerenciador de Armazenamento.

![][9]

Você pode criar ou excluir contêineres de blob, filas e tabelas. Você também pode editar blobs no Gerenciador de Armazenamento escolhendo a ação **Abrir Editor de Contêiner de Blob**.

### Editar um contêiner de blob

1.	Escolha a ação **Abrir Editor de Contêiner de Blob**. O Editor de Contêiner de Blob aparece no painel direito.

	![][10]

1.	Escolha o botão **Carregar** e escolha o comando **Carregar Arquivos**.

	![][11]

	Se os arquivos que você deseja carregar estiverem em uma única pasta, escolha o comando Carregar Pasta.

1. Na caixa de diálogo **Carregar arquivos**, escolha o botão de reticências (**...**) no lado direito da caixa Arquivos para selecionar os arquivos que você deseja carregar. Em seguida, escolha o tipo de blob que você deseja carregar (bloco, página ou anexo). Se quiser, você pode optar por carregar os arquivos em uma pasta no contêiner de blob. Insira o nome da pasta na caixa **Carregar na pasta (opcional)**. Se a pasta não existir, ela será criada.

	![][12]

	Na captura de tela a seguir, três arquivos de imagem foram carregados em uma nova pasta chamada **Meus Novos Arquivos** no contêiner de blob **Imagens**.

	![][13]

	Os botões na barra de ferramentas do editor de blob permitem que você selecione, baixe, abra, copie e exclua arquivos e muito mais. O painel **Atividade** na parte inferior da caixa de diálogo mostra se a operação foi bem-sucedida e permite que você remova somente as atividades bem-sucedidas da exibição, ou limpe totalmente o painel. Escolha o ícone **+** ao lado dos arquivos carregados para exibir uma lista detalhada dos arquivos carregados.

## Criar uma SAS (Assinatura de Acesso Compartilhado)

Para algumas operações, talvez seja necessário um SAS para acessar um recurso de armazenamento. Você pode criar uma usando o Gerenciador de Armazenamento.

1.	Selecione o item para o qual você deseja criar uma SAS e escolha o comando **Obter Assinatura de Acesso Compartilhado** no painel**Ações** ou no menu de atalho do item.

	![][14]

1.	Na caixa de diálogo **Assinatura de Acesso Compartilhado**, escolha a política, as datas de início e vencimento e o fuso horário. Além disso, marque as caixas de seleção dos níveis de acesso desejados para o recurso, como somente leitura, leitura e gravação etc. Quando terminar, escolha o botão **Criar** para criar a SAS.

	![][15]

1.	A caixa de diálogo **Assinatura de Acesso Compartilhado** lista o contêiner juntamente com a URL e as QueryStrings que você pode usar para acessar o recurso de armazenamento. Escolha o botão **Copiar** para copiar as cadeias de caracteres.

	![][16]

## Gerenciar SAS e permissões

Para controlar o acesso aos contêineres de blob, escolha os comandos **Gerenciar Lista de Controle de Acesso** e **Definir o Nível de Acesso Público**.

-	Gerenciar a Lista de Controle de Acesso permite que você adicione, edite e remova as políticas de acesso (se os usuários podem ler, gravar e assim por diante) no contêiner de blob selecionado.
-	Definir o Nível de Acesso Público permite que você determine a quantidade de acesso público os usuários receberão para o recurso.  

-

1.	Escolha o contêiner de blob e escolha o comando **Gerenciar Lista de Controle de Acesso** no menu de atalho ou no painel **Ações**.

	![][17]

1.	Na caixa de diálogo **Lista de Controle de Acesso**, escolha o botão **Adicionar** para adicionar políticas de acesso. Escolha uma política de acesso e selecione as permissões para ela. Quando tiver terminado, escolha o botão **Salvar**.

	![][18]

1.	Para definir um nível de acesso para um contêiner de blob, escolha-o no Gerenciador de Armazenamento e escolha o comando **Definir o Nível de Acesso Público** no menu de atalho ou no painel **Ações**.

	![][19]

1.	Na caixa de diálogo **Definir o Nível de Acesso Público do Contêiner**, selecione o botão de opção para o nível de acesso que você deseja conceder aos usuários públicos e escolha o botão **Aplicar**.

	![][20]

## Próximas etapas
Saiba mais sobre os recursos nos serviços de Armazenamento do Azure lendo artigos em [Introdução ao Armazenamento do Microsoft Azure](./storage/storage-introduction.md).

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount1c.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount2c.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External1c.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External2c.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External3c.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External4c.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External5c.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Navigatec.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Searchc.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit1c.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit2c.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit3c.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit4c.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit5c.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS1c.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS2c.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS3c.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS1c.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS2c.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS3c.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS4c.png

<!---HONumber=AcomDC_0511_2016-->