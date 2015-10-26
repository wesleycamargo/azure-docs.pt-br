<properties 
	pageTitle="Introdução ao Armazenamento do Azure em cinco minutos | Microsoft Azure" 
	description="Compreenda rapidamente os Blobs, Tabelas e Filas do Microsoft Azure, usando Guias de Início Rápido do Azure, o Visual Studio e o Emulador de Armazenamento do Azure. Execute seu primeiro aplicativo de Armazenamento do Azure em cinco minutos." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/28/2015" 
	ms.author="tamram;selcint"/>

# Introdução ao Armazenamento do Azure em cinco minutos 

É fácil começar a desenvolver para o Armazenamento do Azure. Este tutorial mostra como conseguir um aplicativo para Armazenamento do Azure em funcionamento rapidamente. Demonstraremos dois cenários para aumentar facilmente o Armazenamento do Azure:

- [Executar seu primeiro aplicativo local do Armazenamento do Azure no Emulador de Armazenamento do Azure](#run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator)
- [Executar seu primeiro aplicativo do Armazenamento do Azure local no Armazenamento do Azure na nuvem](#run-your-first-azure-storage-application-against-azure-storage-in-the-cloud)

Se você quiser saber mais sobre o Armazenamento do Azure antes de entrarmos no código, consulte [Próximas Etapas](#next-steps).

## Pré-requisitos

Verifique se cumpre os seguintes pré-requisitos antes de iniciar:

1. Para compilar e criar o aplicativo, você precisará de ter uma versão do [Visual Studio](https://www.visualstudio.com/) instalada em seu computador. 
2. Instalar a versão mais recente do [SDK do Azure para .NET](http://azure.microsoft.com/downloads/). O SDK inclui os projetos de exemplo de Início Rápido do Azure, o emulador de armazenamento do Azure e a [Biblioteca de Cliente de Armazenamento do Azure para .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).
3. Certifique-se de que você tem o [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) instalado no seu computador, dado que ele é necessário para os projetos de exemplo de início rápido do Azure que usaremos neste tutorial. Se você não tiver certeza de qual versão do .NET Framework está instalada no seu computador, consulte [Como determinar qual .NET Framework versões estão instaladas](https://msdn.microsoft.com/vstudio/hh925568.aspx). Ou pressione o botão **Iniciar** ou a tecla Windows, digite **Painel de Controle**. Em seguida, clique em **Programas** > **Programas e Recursos** e determine se o .NET Framework 4.5 está listado entre os programas instalados.

A versão mais recente dos binários de Biblioteca de Cliente do Armazenamento do Azure está disponível em [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/).


## Execute seu primeiro aplicativo para o Armazenamento do Azure localmente no Emulador de Armazenamento do Azure

Ao desenvolver um aplicativo que usa o Armazenamento do Azure, você pode executar para o [Emulador do Armazenamento do Azure](storage-use-emulator.md). O emulador de armazenamento do fornece um ambiente local que emula os serviços de blob, fila e tabela do Azure para fins de desenvolvimento. Você pode usar o emulador de armazenamento para testar o aplicativo de armazenamento localmente, sem criar uma conta de armazenamento ou de assinatura do Azure e sem gerar custos.

Para experimentar, vamos criar um aplicativo simples do Armazenamento do Azure usando um dos projetos de exemplo do início rápido do Azure no Visual Studio. Este tutorial concentra-se nos projetos de exemplo para o **Armazenamento de Blob do Azure**, **Armazenamento de Tabela do Azure** e **Filas de Armazenamento do Azure**.

1. Inicie o Visual Studio.
2. No menu **Arquivo**, clique em **Novo Projeto**.
3. Na caixa de diálogo **Novo Projeto**, clique em **Instalados** > **Modelos** > **Visual C#** > **Nuvem** > **Inícios Rápidos** > **Serviços de Dados**.
	- 3\.a. Escolha um dos seguintes modelos: Armazenamento de Blob do Azure, Armazenamento de Tabelas do Azure ou Filas de Armazenamento do Azure. 
	- 3\.b. Verifique se o **.NET Framework 4.5** está selecionado como a estrutura de destino.	
	- 3\.c. Especifique um nome para seu projeto e crie a nova solução do Visual Studio, conforme mostrado:
	
	![Guias de Início Rápido do Azure][Image1]

Convém examinar o código-fonte antes de executar o aplicativo. Para examinar o código, selecione **Gerenciador de Soluções** no menu **Exibição** do Visual Studio. Em seguida, clique duas vezes no arquivo Program.cs.

Em seguida, execute o aplicativo de exemplo no Emulador de Armazenamento do Azure:

1.	Pressione o botão **Iniciar** ou a tecla Windows, procure por *Emulador de Armazenamento do Azure* e inicie o aplicativo.
2.	No Visual Studio, selecione **Implantar Solução** no menu **Compilar**. 
3.	No menu **Depurar**, pressione **F11** para executar a solução passo a passo ou pressione **F5** para executar a solução do início ao fim.

## Execute o seu primeiro aplicativo do Armazenamento do Azure no Armazenamento do Azure na nuvem

Para executar no Armazenamento do Azure na nuvem, você precisará uma assinatura do Azure e de uma conta de armazenamento, se você ainda não tiver uma:

- Para obter uma assinatura do Azure, consulte [Avaliação Gratuita](http://azure.microsoft.com/pricing/free-trial/), [Opções de compra](http://azure.microsoft.com/pricing/purchase-options/) e [Ofertas para membros](http://azure.microsoft.com/pricing/member-offers/) (para membros de outros programas da Microsoft, MSDN, Microsoft Partner Network e BizSpark).
- Para criar uma conta de armazenamento no Azure, consulte [Como criar, gerenciar ou excluir uma conta de armazenamento](storage-create-storage-account.md).

Quando você tiver uma conta, você pode criar um aplicativo simples de armazenamento do Azure usando um dos projetos de exemplo de Início Rápido do Azure no Visual Studio. Este tutorial concentra-se nos projetos de exemplo para o **Armazenamento de Blob do Azure**, **Armazenamento de Tabela do Azure** e **Filas de Armazenamento do Azure**:

1. Inicie o Visual Studio.
2. No menu **Arquivo**, clique em **Novo Projeto**.
3. Na caixa de diálogo **Novo Projeto**, clique em **Instalados** > **Modelos** > **Visual C#** > **Nuvem** > **Inícios Rápidos** > **Serviços de Dados**.
	- 3\.a. Escolha um dos seguintes modelos: Armazenamento de Blob do Azure, Armazenamento de Tabelas do Azure ou Filas de Armazenamento do Azure.
	- 3\.b. Verifique se o **.NET Framework 4.5** está selecionado como a estrutura de destino.
	- 3\.c. Especifique um nome para seu projeto e crie a nova solução do Visual Studio. 

Convém examinar o código-fonte antes de executar o aplicativo. Para examinar o código, selecione **Gerenciador de Soluções** no menu **Exibição** do Visual Studio. Em seguida, clique duas vezes no arquivo Program.cs.

Em seguida, execute o aplicativo de exemplo:

1.	No Visual Studio, selecione **Gerenciador de Soluções** no menu **Exibição**. Clique duas vezes no arquivo App.config e comente a cadeia de conexão para o Emulador de Armazenamento do SDK do Azure:

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Remova o comentário da cadeia de conexão para o Serviço de Armazenamento do Azure e forneça a chave de acesso e do nome da conta de armazenamento no arquivo App.config: `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

	Para localizar o nome da conta do armazenamento e a chave de acesso, consulte [O que é uma Conta de Armazenamento](storage-whatis-account.md).

3.	Depois de fornecer a chave de acesso e o nome da conta de armazenamento no arquivo App.config, no menu **Arquivo**, clique em **Salvar tudo** para salvar todos os arquivos de projeto.
4.	No menu **Compilar**, clique em **Compilar Solução**.
5.	No menu **Depurar**, pressione **F11** para executar a solução passo a passo ou pressione **F5** para executar a solução.


## Próximas etapas

Consulte estes recursos para saber mais sobre o Armazenamento do Azure:

* [Introdução ao Armazenamento do Microsoft Azure](storage-introduction.md)
* [Como usar o Armazenamento de Blob no .NET](storage-dotnet-how-to-use-blobs.md)
* [Como usar o Armazenamento de Tabela no .NET](storage-dotnet-how-to-use-tables.md)
* [Como usar o Armazenamento de Fila no .NET](storage-dotnet-how-to-use-queues.md)
* [Documentação do Armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/)
* [Biblioteca de Cliente do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [API REST de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
 

<!---HONumber=Oct15_HO3-->