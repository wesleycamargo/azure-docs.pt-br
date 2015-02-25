<properties 
	pageTitle="Implantar WebJobs do Azure em Websites do Azure" 
	description="Saiba como implantar WebJobs do Azure em sites do Azure usando o Visual Studio." 
	services="web-sites" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="11/12/2014" 
	ms.author="tdykstra"/>

# Como implantar Trabalhos Web do Azure em Sites do Azure

Este tópico explica como usar o Visual Studio para implantar um projeto do Aplicativo de Console em um Site do Azure como um [Trabalho Web do Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Uma maneira alternativa de implantar Trabalhos Web é usar o Portal de Gerenciamento do Azure. Para obter informações sobre esse método, consulte [Usar Trabalhos Web para executar tarefas em segundo plano em Sites do Microsoft Azure](/pt-br/documentation/articles/web-sites-create-web-jobs/).

## Sumário ##

- [Visão geral](#overview)
- [Pré-requisitos](#prerequisites)
- [Habilitar implantação de Trabalhos Web para um projeto do Aplicativo de Console existente](#convert)
- [Criar um novo projeto habilitado para Trabalhos Web](#create)
- [A caixa de diálogo Adicionar Trabalho Web do Azure](#configure)
- [O arquivo webjob-publish-settings.json](#publishsettings)
- [O arquivo webjobs-list.json](#webjobslist)
- [Implantar um projeto de Trabalhos Web](#deploy)
- [Próximas etapas](#next-steps)

## <a id="overview"></a>Visão geral

Ao implantar um projeto do Aplicativo de Console habilitado para Trabalhos Web, o Visual Studio realiza duas tarefas:

* Copia os arquivos de tempo de execução para a pasta apropriada no Website do Azure (*App_Data/jobs/continuous* para WebJobs contínuos, *App_Data/jobs/triggered* para WebJobs agendados e sob demanda).
* Configura [trabalhos do Agendador do Azure](#scheduler) para WebJobs que estão agendados para execução em horários específicos. (Isso não é necessário para Trabalhos Web contínuos.)

Um projeto habilitado para Trabalhos Web tem os seguintes itens adicionados:

* O pacote NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/).
* A [webjob-publish-settings.json](#publishsettings) que contém as configurações de implantação e do Agendador. 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

É possível adicionar esses itens a um projeto do Aplicativo de Console existente ou usar um modelo para criar um novo projeto do Aplicativo de Console habilitado para Trabalhos Web. 

É possível implantar um projeto como um Trabalho Web propriamente dito ou vinculá-lo a um projeto Web de forma que ele seja implantado automaticamente sempre que você implanta o projeto Web. Para vincular projetos, o Visual Studio inclui o nome do projeto habilitado para WebJobs em um arquivo [webjobs-list.json](#webjobslist) no projeto da web.

![Diagram showing WebJob project linking to web project](./media/websites-dotnet-deploy-webjobs/link.png)
 


## Pré-requisitos

Os recursos de implantação de Trabalhos Web estão disponíveis no Visual Studio 2013 ao instalar o SDK do Azure versão 2.4 ou posterior:

* [SDK do Azure para o Visual Studio 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409).

Os recursos de implantação de Trabalhos Web também estão incluídos no [Visual Studio 2013 atualização 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) e atualizações posteriores.

## <a id="convert"></a>Habilitar implantação de Trabalhos Web para um projeto do Aplicativo de Console existente

Você tem duas opções:

* [Habilitar implantação automática com um projeto Web](#convertlink).

	Configure um projeto do Aplicativo de Console existente de forma que ele seja implantado automaticamente como um Trabalho Web quando você implanta o projeto Web. Use essa opção quando você quiser executar o Trabalho Web no mesmo site em que executa o aplicativo Web relacionado.

* [Habilitar implantação sem um projeto Web](#convertnolink).

	Configure um projeto do Aplicativo de Console existente a ser implantado como um Trabalho Web propriamente dito, sem link para um projeto Web. Use essa opção quando você quiser executar um Trabalho Web em um site propriamente dito, sem aplicativo Web em execução no site. Você talvez queira fazer isso para ser capaz de dimensionar os recursos de Trabalho Web independentemente dos recursos do aplicativo Web.

### <a id="convertlink"></a> Habilitar implantação de Trabalhos Web automática com um projeto Web
  
1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de Soluções** e, em seguida, clique em **Adicionar** > **Projeto Existente como Trabalho Web do Azure**.

	![Existing Project as Azure WebJob](./media/websites-dotnet-deploy-webjobs/eawj.png)
	
	A caixa de diálogo [Adicionar WebJob do Azure](#configure)

1. Na lista suspensa **Nome do projeto**, selecione o projeto do Aplicativo de Console a ser adicionado como um Trabalho Web.

	![Selecting project in Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw1.png)

2. Conclua a caixa de diálogo [adicionar Webjob do Azure](#configure) e clique em **OK**. 

### <a id="convertnolink"></a> Habilitar implantação de Trabalhos Web sem um projeto Web
  
1. Clique com o botão direito do mouse no projeto do Aplicativo de Console no **Gerenciador de Soluções** e, em seguida, clique em **Publicar como Trabalho Web do Azure**. 

	![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
	A caixa de diálogo [adicionar Webjob do Azure](#configure) é exibida com o projeto selecionado na caixa **Nome do Projeto**.

2.  Conclua a caixa de diálogo [adicionar Webjob do Azure](#configure) e clique em **OK**.

	O assistente **Publicar Web** é exibido.  Se você não quiser publicar imediatamente, feche o assistente. As configurações inseridas por você são salvas para quando quiser [implantar o projeto](#deploy).

## <a id="create"></a>Criar um novo projeto habilitado para Trabalhos Web

Para criar um projeto habilitado para Trabalhos Web, é possível usar o modelo de projeto do Aplicativo de Console e habilitar a implantação de Trabalhos Web conforme explicado na [seção anterior](#convert). Também é possível usar o modelo do novo projeto de Trabalhos Web:

* [Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente](#createnolink)

	Crie um projeto e o configure para ser implantado propriamente dito como um Trabalho Web, sem link para um projeto Web. Use essa opção quando você quiser executar um Trabalho Web em um site propriamente dito, sem aplicativo Web em execução no site. Você talvez queira fazer isso para ser capaz de dimensionar os recursos de Trabalho Web independentemente dos recursos do aplicativo Web.

* [Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web](#createlink)

	Crie um projeto que seja configurado para ser implantado automaticamente como um Trabalho Web quando um projeto Web na mesma solução for implantado. Use essa opção quando você quiser executar o Trabalho Web no mesmo site em que executa o aplicativo Web relacionado.

Na versão do SDK 2.4, o novo modelo de projeto de Trabalhos Web não é muito mais fácil do que a criação de um projeto do Aplicativo de Console e a habilitação da implantação de Trabalhos Web. No futuro, o novo modelo de projeto de Trabalhos Web será mais conveniente para o desenvolvimento do [SDK de Trabalhos Web](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs), porque ele instalará automaticamente os pacotes NuGet do SDK de Trabalhos Web apropriados. Até lá, você pode configurar um projeto para usar o SDK de Trabalhos Web instalando os pacotes manualmente conforme mostrado no [tutorial do SDK de Trabalhos Web](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). 


### <a id="createnolink"></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente
  
1. Clique em **Arquivo** > **Novo Projeto** e, em seguida, na caixa de diálogo **Novo Projeto**, clique em **Nuvem** > **Trabalho Web do Microsoft Azure**.

	![New Project dialog showing WebJob template](./media/websites-dotnet-deploy-webjobs/np.png)
	
2. Siga as instruções mostradas anteriormente para [tornar o projeto do Aplicativo de Console um projeto de Trabalhos Web independente](#convertnolink).

### <a id="createlink"></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web

1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de Soluções** e, em seguida, clique em **Adicionar** > **Novo projeto Existente de Trabalho Web do Azure**.

	![New Azure WebJob Project menu entry](./media/websites-dotnet-deploy-webjobs/nawj.png)

	A caixa de diálogo [adicionar Webjob do Azure](#configure) aparece.

2. Conclua a caixa de diálogo [adicionar Webjob do Azure](#configure) e clique em **OK**.

## <a id="configure"></a>A caixa de diálogo Adicionar Trabalho Web do Azure

A caixa de diálogo **Adicionar Trabalho Web do Azure** permite inserir as configurações de nome e agendamento do Trabalho Web. 

![Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Os campos nessa caixa de diálogo correspondem aos campos na caixa de diálogo **Novo Trabalho** do Portal de Gerenciamento do Azure. Para obter mais informações, consulte [Usar Trabalhos Web para executar tarefas em segundo plano nos Sites do Microsoft Azure](/pt-br/documentation/articles/web-sites-create-web-jobs/).

Para um Trabalho Web agendado (e não para Trabalhos Web contínuos), o Visual Studio cria uma coleção de trabalhos do [Agendador do Azure](/pt-br/services/scheduler/) caso ainda não exista uma e cria um trabalho na coleção:

* A coleção de trabalhos do Agendador é denominada *WebJobs-{regionname}* onde *{regionname}*refere-se a região em que o site é hospedado. Por exemplo: WebJobs-WestUS.
* O trabalho do Agendador é chamado *{websitename}-{webjobname}*. Por exemplo: MyWebSite-MyWebJob. 
 
>[AZURE.NOTE]
>* Para obter informações sobre a implantação de linha de comando, consulte [Habilitando a entrega de linha contínua ou de comando dos Trabalhos Web do Azure](/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)..
>* Se você configurar um **Trabalho Recorrente** e definir a frequência de recorrência como um número de minutos, o serviço Agendador do Azure não estará livre. Outra frequências (horas, dias etc.) estão livres.
>* Se você implantar um Trabalho Web e depois alterar o modo de execução de contínuo para não contínuo ou vice-versa, o Visual Studio criará um novo Trabalho Web no Azure quando você o reimplantar. Se você alterar outras configurações de agendamento, mas deixar o modo de execução igual ou alternar Agendado e Sob Demanda, o Visual Studio atualizará o trabalho existente, em vez de criar um novo.

## <a id="publishsettings"></a>webjob-publish-settings.json

Ao configurar um aplicativo de Console para implantação de Trabalhos da Web, o Visual Studio instala o pacote NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) e armazena informações de agendamento em um arquivo *webjob-publish-settings.json* no projeto d pasta *Properties* do projeto de WebJobs. Aqui está um exemplo desse arquivo:

		{
		  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
		  "webJobName": "WebJob1",
		  "startTime": "2014-06-23T00:00:00-08:00",
		  "endTime": "2014-06-27T00:00:00-08:00",
		  "jobRecurrenceFrequency": "Minute",
		  "interval": 5,
		  "runMode": "Scheduled"
		}

É possível editar esse arquivo diretamente, e o Visual Studio fornece o IntelliSense. O esquema de arquivo é armazenado em [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) e pode ser visto aqui.  

>[AZURE.NOTE]
>* Se você configurar um **Trabalho Recorrente** e definir a frequência de recorrência como um número de minutos, o serviço Agendador do Azure não estará livre. Outra frequências (horas, dias etc.) estão livres.

## <a id="webjobslist"></a>webjobs-list.json

Quando você vincula um projeto habilitado para WebJobs em um projeto da Web, o Visual Studio armazena o nome do projeto do WebJobs em um arquivo *webjobs-list.json* na pasta do projeto de web *Properties*. A lista pode conter vários projetos de Trabalhos Web, conforme mostrado no seguinte exemplo:

		{
		  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
		  "WebJobs": [
		    {
		      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
		    },
		    {
		      "filePath": "../WebJob1/WebJob1.csproj"
		    }
		  ]
		}

É possível editar esse arquivo diretamente, e o Visual Studio fornece o IntelliSense. O esquema de arquivo é armazenado em [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) e pode ser exibido aqui.
  
## <a id="deploy"></a>Implantar um projeto de Trabalhos Web

Um projeto de Trabalhos Web vinculado a um projeto Web é implantado automaticamente com o projeto Web. Para obter informações sobre a implantação do projeto Web, consulte [Como implantar um Site do Azure](/pt-br/documentation/articles/websites-dotnet-deploy/).

Para implantar um projeto de Trabalhos Web propriamente dito, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e clique em **Publicar como Trabalho Web do Azure**. 

![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
Para um Trabalho Web independente, o mesmo assistente **Publicar Web** usado em projetos Web é exibido, mas com menos configurações disponíveis para serem alteradas.

## <a id="nextsteps"></a>Próximas etapas

Para obter mais informações sobre como implantar Trabalhos Web do Azure do Visual Studio e utilizando um processo de fornecimento contínuo, consulte [Trabalhos Web do Azure - Recursos recomendados - Implantação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying).


<!--HONumber=42-->
