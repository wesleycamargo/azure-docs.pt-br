<properties 
	pageTitle="Introdução aos blobs, tabelas e filas do Azure em 5 minutos" 
	description="Entenda rapidamente sobre os blobs, tabelas e filas do Microsoft Azure usando guias de início rápido do Azure e o Visual Studio." 
	services="storage" 
	documentationCenter=".net" 
	authors="Selcin" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/18/2015" 
	ms.author="selcint"/>

# Introdução aos blobs, tabelas e filas do Azure em 5 minutos 

Este tutorial mostra como programar rapidamente **Blobs**, **Tabelas**, e **Filas** do Armazenamento do Azure desenvolvendo um simples aplicativo do Azure no Visual Studio. 

O tutorial inclui dois cenários principais para uma compreensão rápida sobre o Armazenamento do Azure:

- Executar seu primeiro aplicativo do Armazenamento do Azure no Emulador de Armazenamento do Azure
- Executar seu primeiro aplicativo do Armazenamento do Azure no Serviço de Armazenamento do Azure

Se você quiser saber sobre o Armazenamento do Azure antes de entrarmos no código, consulte [Próximas etapas][].

## Executar seu primeiro aplicativo do Armazenamento do Azure no Emulador de Armazenamento do Azure

Esta seção demonstra como programar em **Blobs**, **Tabelas**, e **Filas** do Armazenamento do Azure desenvolvendo um aplicativo de exemplo acessando o [Emulador de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/hh403989.aspx). O emulador de armazenamento do Microsoft Azure fornece um ambiente local que emula os serviços de blob, fila e tabela do Azure para fins de desenvolvimento. Usando o emulador de armazenamento, você pode testar seu aplicativo nos serviços de armazenamento localmente, sem gerar custos.

Para concluir esta seção, certifique-se primeiro de executar as seguintes tarefas de pré-requisito:

1. Para compilar e criar o aplicativo, você precisa ter o [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) instalado em seu computador. Se você não tiver instalado o Visual Studio, poderá instalar o Visual Studio Express para Web ao instalar o [SDK do Azure 2.5 para Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) ou posterior. 
2. Verifique se você tem [SDK do Azure 2.5 para Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) ou posterior instalado no seu computador, pois ele inclui os projetos de exemplo de início rápido do Azure e a [Biblioteca de Cliente de Armazenamento do Azure para .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).  
3. Verifique se você tem o [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) instalado no computador, pois os projetos de exemplo de início rápido do Azure necessitam dele. Se você não tiver certeza de qual versão do .NET Framework está instalada no seu computador, consulte [Como: Determinar quais versões do .NET Framework estão instaladas](https://msdn.microsoft.com/vstudio/hh925568.aspx). Ou pressione o botão **Iniciar** ou a tecla Windows, digite **Painel de Controle**. Em seguida, clique em **Programas** > **Programas e Recursos**. Verifique se o .NET Framework 4.5 está listado entre todos os programas instalados.

Agora, vamos criar um aplicativo simples do Armazenamento do Azure usando um dos projetos de exemplo do início rápido do Azure no Visual Studio. Este tutorial concentra-se nos projetos de exemplo **Armazenamento de Blob do Azure**, **Armazenamento de Tabela do Azure** e **Filas de Armazenamento do Azure**. As instruções a seguir se aplicam para todos os projetos de exemplo, exceto se você escolher um modelo diferente na etapa 3.a:

1. Pressione o botão **Iniciar** ou a tecla Windows, digite Visual Studio 2013 ou VS Express 2013 para Web. Clique no programa para iniciar.
2. No menu **Arquivo**, clique em **Novo Projeto**.
3. Na caixa de diálogo **Novo projeto**, clique em **Instalados** > **Modelos** > **Visual C#** > **Nuvem** > **Inícios rápidos** > **Serviços de Dados**.
	- 3.a.  Escolha um dos seguintes modelos: Armazenamento de Blob do Azure, Armazenamento de Tabelas do Azure ou Filas de Armazenamento do Azure. 
	- 3.b. Verifique se o **.NET Framework 4.5** está selecionado como a estrutura de destino.	
	- 3.c. Dependendo do modelo escolhido, nomeie o aplicativo como **DataBlobStorage**, **DataTableStorage** ou **DataStorageQueue**. Clique em **OK**. Isso deve criar uma nova solução do Visual Studio. Consulte a captura de tela a seguir como exemplo:
	
	![Azure QuickStarts][Image1]

Incentivamos você a analisar o código-fonte para aprender a programar no Armazenamento do Azure antes de executar o aplicativo. Para examinar o código, selecione **Gerenciador de Soluções** no menu **Exibição** do Visual Studio. Em seguida, clique duas vezes no arquivo Program.cs. 

Execute então o aplicativo de exemplo usando o [Emulador de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/hh403989.aspx) que é instalado como parte do SDK do Azure:

1.	Abra o Emulador de Armazenamento do Azure: Pressione o botão **Iniciar** ou a tecla Windows, pesquise-o digitando o Emulador de Armazenamento do Azure. Selecione-o na lista de aplicativos para iniciá-lo.
2.	No Visual Studio, selecione **Implantar Solução** no menu **Compilar**. 
3.	No menu **Depurar**, pressione **F11** para executar a solução passo a passo ou pressione **F5** para executar a solução.

## Executar seu primeiro aplicativo do Armazenamento do Azure no Serviço de Armazenamento do Azure
Esta seção demonstra como programar em **Blobs**, **Tabelas**, e **Filas** do Armazenamento do Azure desenvolvendo um aplicativo de exemplo acessando o [Serviço de Armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/).

Para concluir esta seção, certifique-se primeiro de executar as seguintes tarefas de pré-requisito:

1. Para compilar e criar o aplicativo, você precisa ter o [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) instalado em seu computador. Se você não tiver instalado o Visual Studio, poderá instalar o Visual Studio Express para Web ao instalar o [SDK do Azure 2.5 para Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) ou posterior. 
2. Verifique se você tem [SDK do Azure 2.5 para Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) ou posterior instalado no seu computador, pois ele inclui os projetos de exemplo de início rápido do Azure e a [Biblioteca de Cliente de Armazenamento do Azure para .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).  
3. Verifique se você tem o [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) instalado no computador, pois os projetos de exemplo de início rápido do Azure necessitam dele. Se você não tiver certeza de qual versão do .NET Framework está instalada no seu computador, consulte [Como: Determinar quais versões do .NET Framework estão instaladas](https://msdn.microsoft.com/vstudio/hh925568.aspx). Ou pressione o botão **Iniciar** ou a tecla Windows, digite **Painel de Controle**. Em seguida, clique em **Programas** > **Programas e Recursos**. Verifique se o .NET Framework 4.5 está listado entre todos os programas instalados.
4.	Obtenha uma assinatura do Azure (se você ainda não tiver uma) e crie também uma conta de **Armazenamento padrão**:
	- Para obter uma assinatura do Azure, consulte [Avaliação gratuita](, http://azure.microsoft.com/pricing/free-trial/)[Opções de compra](http://azure.microsoft.com/pricing/purchase-options/) e [Ofertas para membros](http://azure.microsoft.com/pricing/member-offers/) (para membros de outros programas da Microsoft, MSDN, Microsoft Partner Network e BizSpark).
	- Para criar uma conta de **Armazenamento padrão** no Azure, consulte [Como criar, gerenciar ou excluir uma conta de armazenamento](storage-create-storage-account.md). **Observação:** Há dois tipos de contas de armazenamento no Azure: Conta de Armazenamento Padrão e a conta de Armazenamento Premium. Uma conta de armazenamento padrão fornece acesso ao Armazenamento de Blob, Tabela e Fila do Azure. Uma conta de Armazenamento Premium está disponível apenas para armazenar dados em discos usados por máquinas virtuais do Azure. Para obter mais informações, consulte [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure](storage-premium-storage-preview-portal.md).

Agora, vamos criar um aplicativo simples do Armazenamento do Azure usando um dos projetos de exemplo do início rápido do Azure no Visual Studio. Este tutorial concentra-se nos projetos de exemplo **Armazenamento de Blob do Azure**, **Armazenamento de Tabela do Azure** e **Filas de Armazenamento do Azure**. As instruções a seguir se aplicam para todos os projetos de exemplo, exceto se você escolher um modelo diferente na etapa 3.a:

1. Pressione o botão **Iniciar** ou a tecla Windows, digite Visual Studio 2013 ou VS Express 2013 para Web. Clique no programa para iniciar.
2. No menu **Arquivo**, clique em **Novo Projeto**.
3. Na caixa de diálogo **Novo projeto**, clique em **Instalados** > **Modelos** > **Visual C#** > **Nuvem** > **Inícios rápidos** > **Serviços de Dados**.
	- 3.a. Escolha um dos seguintes modelos: Armazenamento de Blob do Azure, Armazenamento de Tabelas do Azure ou Filas de Armazenamento do Azure. 
	- 3.b. Verifique se o **.NET Framework 4.5** está selecionado como a estrutura de destino.
	- 3.c. Dependendo do modelo escolhido, nomeie o aplicativo como **DataBlobStorage**, **DataTableStorage** ou **DataStorageQueue**. Clique em **OK**. Isso deve criar uma nova solução do Visual Studio. 

Incentivamos você a analisar o código-fonte para aprender a programar no Armazenamento do Azure antes de executar o aplicativo. Para examinar o código, selecione **Gerenciador de Soluções** no menu **Exibição** do Visual Studio. Em seguida, clique duas vezes no arquivo Program.cs. 

Agora, execute o aplicativo de exemplo:

1.	No Visual Studio, selecione **Gerenciador de Soluções** no menu **Exibição**. Clique duas vezes no arquivo App.config e comente a cadeia de conexão para o Emulador de Armazenamento do SDK do Azure: 

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Remova o comentário da cadeia de conexão para o Serviço de Armazenamento do Azure e forneça a chave de acesso e do nome da conta de armazenamento no arquivo App.config:
	`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"` 

	Para localizar o nome da conta do armazenamento e a chave de acesso, consulte [O que é uma conta de armazenamento](storage-whatis-account.md). 

3.	Depois de fornecer a chave de acesso e o nome da conta de armazenamento no arquivo App.config, no menu **Arquivo**, clique em **Salvar tudo** para salvar todos os arquivos de projeto. 
4.	No menu **Compilar**, clique em **Compilar Solução**. 
5.	No menu **Depurar**, pressione **F11** para executar a solução passo a passo ou pressione **F5** para executar a solução.


## Próximas etapas
Neste tutorial, você aprendeu como programar no Armazenamento de Blob do Azure, Armazenamento de Tabela do Azure e Filas de Armazenamento do Azure. 

Se você quiser saber mais sobre eles, siga estes links:

* [Introdução ao Armazenamento do Microsoft Azure](storage-introduction.md)
* [Como usar o Armazenamento de Blob no .NET](storage-dotnet-how-to-use-blobs.md)
* [Como usar o Armazenamento de Tabela no .NET](storage-dotnet-how-to-use-tables.md)
* [Como usar o Armazenamento de Fila no .NET](storage-dotnet-how-to-use-queues.md)
* [Documentação do Armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/)
* [Referência do MSDN de Armazenamento do Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
* [Biblioteca de cliente do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [API REST de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png


<!--HONumber=47-->
