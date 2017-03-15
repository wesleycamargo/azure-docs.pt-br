---
title: Implantar Trabalhos Web usando o Visual Studio
description: "Aprenda como implantar Trabalhos Web do Azure para Aplicativos Web do Serviço de Aplicativo do Azure usando o Visual Studio."
services: app-service
documentationcenter: 
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: fcbd9e10e4cc336dc6ea37f84201249e14b1af91
ms.openlocfilehash: 9f792f6ea082461f3304516fc9b4c3273e2f50b8
ms.lasthandoff: 12/08/2016


---
# <a name="deploy-webjobs-using-visual-studio"></a>Implantar Trabalhos Web usando o Visual Studio
## <a name="overview"></a>Visão geral
Esse tópico explica como usar o Visual Studio para implantar um projeto de Aplicativo de Console para um aplicativo Web do [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) como um [Trabalho Web do Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Para obter informações sobre como implantar Trabalhos Web usando o [Portal do Azure](https://portal.azure.com), consulte [Executar tarefas em segundo plano com Trabalhos Web](web-sites-create-web-jobs.md).

Ao implantar um projeto do Aplicativo de Console habilitado para Trabalhos Web, o Visual Studio realiza duas tarefas:

* Copia arquivos de tempo de execução para a pasta apropriada no aplicativo Web (*App_Data/jobs/continuous* para Trabalhos Web contínuos, *App_Data/jobs/triggered* para Trabalhos Web agendados e sob demanda).
* Configura [Trabalhos do Agendador do Azure](#scheduler) para Trabalhos Web agendados a serem executados em determinados horários. (Isso não é necessário para Trabalhos Web contínuos.)

Um projeto habilitado para Trabalhos Web tem os seguintes itens adicionados:

* O pacote NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Um arquivo [webjob-publish-settings.json](#publishsettings) que contém configurações de implantação e agendador. 

![Diagrama que mostra o que é adicionado a um Aplicativo de Console para habilitar implantação como um Trabalho Web](./media/websites-dotnet-deploy-webjobs/convert.png)

É possível adicionar esses itens a um projeto do Aplicativo de Console existente ou usar um modelo para criar um novo projeto do Aplicativo de Console habilitado para Trabalhos Web. 

É possível implantar um projeto como um Trabalho Web propriamente dito ou vinculá-lo a um projeto Web de forma que ele seja implantado automaticamente sempre que você implanta o projeto Web. Para vincular projetos, o Visual Studio inclui o nome do projeto habilitado para Trabalhos Web em um arquivo [webjobs-list.json](#webjobslist) no projeto Web.

![Diagrama que mostra o projeto de Trabalho Web sendo vinculado ao projeto Web](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Pré-requisitos
Os recursos de implantação do WebJobs estão disponíveis no Visual Studio 2015 quando você instala o SDK do Azure para .NET:

* [SDK do Azure para .NET (Visual Studio 2015)](http://go.microsoft.com/fwlink/?linkid=518003).

## <a id="convert"></a>Habilitar a implantação de Trabalhos Web para um projeto do Aplicativo de Console existente
Você tem duas opções:

* [Habilitar implantação automática com um projeto Web](#convertlink).
  
    Configure um projeto do Aplicativo de Console existente de forma que ele seja implantado automaticamente como um Trabalho Web quando você implanta o projeto Web. Use esta opção quando quiser executar o Trabalho Web no mesmo aplicativo Web em que você executa o aplicativo Web relacionado.
* [Habilitar implantação sem um projeto Web](#convertnolink).
  
    Configure um projeto do Aplicativo de Console existente a ser implantado como um Trabalho Web propriamente dito, sem link para um projeto Web. Use esta opção quando você quiser executar um Trabalho Web em um aplicativo Web por si só, sem nenhum aplicativo Web em execução no aplicativo Web. Você talvez queira fazer isso para ser capaz de dimensionar os recursos de Trabalho Web independentemente dos recursos do aplicativo Web.

### <a id="convertlink"></a> Habilitar a implantação de Trabalhos Web automática com um projeto Web
1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de Soluções** e clique em **Adicionar** > **Projeto Existente como Trabalho Web do Azure**.
   
    ![Projeto Existente como Trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida.
2. Na lista suspensa **Nome do projeto** , selecione o projeto do Aplicativo de Console a ser adicionado como um Trabalho Web.
   
    ![Selecionando projeto na caixa de diálogo Adicionar Trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Complete a caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) e clique em **OK**. 

### <a id="convertnolink"></a> Habilitar a implantação de Trabalhos Web sem um projeto Web
1. Clique com o botão direito do mouse no projeto do Aplicativo de Console no **Gerenciador de Soluções** e clique em **Publicar como Trabalho Web do Azure**. 
   
    ![Publicar como Trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida, com o projeto selecionado na caixa **Nome do projeto** .
2. Complete a caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) e clique em **OK**.
   
   O assistente **Publicar Web** é exibido.  Se você não quiser publicar imediatamente, feche o assistente. As configurações inseridas por você são salvas para quando quiser [implantar o projeto](#deploy).

## <a id="create"></a>Criar um novo projeto habilitado para Trabalhos Web
Para criar um projeto habilitado para Trabalhos Web, é possível usar o modelo de projeto do Aplicativo de Console e habilitar a implantação de Trabalhos Web conforme explicado na [seção anterior](#convert). Também é possível usar o modelo do novo projeto de Trabalhos Web:

* [Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente](#createnolink)
  
    Crie um projeto e o configure para ser implantado propriamente dito como um Trabalho Web, sem link para um projeto Web. Use esta opção quando você quiser executar um Trabalho Web em um aplicativo Web por si só, sem nenhum aplicativo Web em execução no aplicativo Web. Você talvez queira fazer isso para ser capaz de dimensionar os recursos de Trabalho Web independentemente dos recursos do aplicativo Web.
* [Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web](#createlink)
  
    Crie um projeto que seja configurado para ser implantado automaticamente como um Trabalho Web quando um projeto Web na mesma solução for implantado. Use esta opção quando quiser executar o Trabalho Web no mesmo aplicativo Web em que você executa o aplicativo Web relacionado.

> [!NOTE]
> O modelo new-project do WebJobs instala automaticamente pacotes NuGet e inclui o código em *Program.cs* para o [SDK do WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se você não quiser usar o SDK do WebJobs ou quiser usar um WebJob agendado em vez de contínuo, remova ou altere a instrução `host.RunAndBlock` em *Program.cs*.
> 
> 

### <a id="createnolink"></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente
1. Clique em **Arquivo** > **Novo Projeto** e, na caixa de diálogo **Novo Projeto**, clique em **Nuvem** > **Trabalho Web do Microsoft Azure**.
   
    ![Caixa de diálogo Novo Projeto mostrando o modelo de Trabalho Web](./media/websites-dotnet-deploy-webjobs/np.png)
2. Siga as direções mostradas anteriormente para [tornar o projeto do Aplicativo de Console um projeto de Trabalhos Web independente](#convertnolink).

### <a id="createlink"></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web
1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de Soluções** e clique em **Adicionar** > **Novo Projeto de Trabalho Web do Azure**.
   
    ![Entrada de menu de Novo Projeto de Trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida.
2. Complete a caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) e clique em **OK**.

## <a id="configure"></a>A caixa de diálogo Adicionar Trabalho Web do Azure
A caixa de diálogo **Adicionar Trabalho Web do Azure** permite inserir as configurações de nome e agendamento do Trabalho Web. 

![Caixa de diálogo Adicionar Trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Os campos nessa caixa de diálogo correspondem aos campos na caixa de diálogo **Novo Trabalho** do Portal do Azure. Para obter mais informações, consulte [Executar tarefas em segundo plano com o Trabalhos Web](web-sites-create-web-jobs.md).

Para um Trabalho Web agendado (e não para Trabalhos Web contínuos), o Visual Studio cria uma coleção de trabalhos do [Agendador do Azure](/services/scheduler/) caso ainda não exista uma e cria um trabalho na coleção:

* A coleção de trabalhos do agendador é denominada *WebJobs-{regionname}*, em que *{regionname}* refere-se à região em que o aplicativo Web está hospedado. Por exemplo: WebJobs-OesteEUA.
* O trabalho do agendador se chama *{nomedoaplicativoweb}-{nomedotrabalhoweb}*. Por exemplo: MeuAplicativoWeb-MeuTrabalhoWeb. 

> [!NOTE]
> * Para obter informações sobre a implantação de linha de comando, consulte [Habilitando a entrega de linha de comando ou contínua de Trabalhos Web do Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se você configurar um **Trabalho Recorrente** e definir a frequência de recorrência como um número de minutos, o serviço Agendador do Azure não estará livre. Outra frequências (horas, dias etc.) estão livres.
> * Se você implantar um Trabalho Web e, em seguida, decidir que deseja alterar o tipo de Trabalho Web e implantá-lo novamente, você precisará excluir o arquivo webjobs-publish-settings.json. Isso fará com que o Visual Studio exiba novamente as opções de publicação para que você possa alterar o tipo de Trabalho Web.
> * Se você implantar um Trabalho Web e depois alterar o modo de execução de contínuo para não contínuo ou vice-versa, o Visual Studio criará um novo Trabalho Web no Azure quando você o reimplantar. Se você alterar outras configurações de agendamento, mas deixar o modo de execução igual ou alternar Agendado e Sob Demanda, o Visual Studio atualizará o trabalho existente, em vez de criar um novo.
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
Quando você configura um Aplicativo de Console para implantação de Trabalhos Web, o Visual Studio instala o pacote NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) e armazena informações de agendamento em um arquivo *webjob-publish-settings.json* na pasta *Propriedades* do projeto dos Trabalhos Web. Aqui está um exemplo desse arquivo:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

É possível editar esse arquivo diretamente, e o Visual Studio fornece o IntelliSense. O esquema de arquivo é armazenado em [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) e pode ser exibido aqui.  

> [!NOTE]
> * Se você configurar um **Trabalho Recorrente** e definir a frequência de recorrência como um número de minutos, o serviço Agendador do Azure não estará livre. Outra frequências (horas, dias etc.) estão livres.
> 
> 

## <a id="webjobslist"></a>webjobs-list.json
Quando você vincula um projeto habilitado para Trabalhos Web a um projeto Web, o Visual Studio armazena o nome do projeto de Trabalhos Web em um arquivo *webjobs-list.json* na pasta *Propriedades* do projeto Web. A lista pode conter vários projetos de Trabalhos Web, conforme mostrado no seguinte exemplo:

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
Um projeto de Trabalhos Web vinculado a um projeto Web é implantado automaticamente com o projeto Web. Para obter informações sobre a implantação de projetos Web, consulte [Como implantar Aplicativos Web](web-sites-deploy.md).

Para implantar um projeto de Trabalhos Web propriamente dito, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e clique em **Publicar como Trabalho Web do Azure**. 

![Publicar como Trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/paw.png)

Para um Trabalho Web independente, o mesmo assistente **Publicar Web** usado em projetos Web é exibido, mas com menos configurações disponíveis para serem alteradas.

## <a id="nextsteps"></a>Próximas etapas
Este artigo explicou como implantar WebJobs usando o Visual Studio. Para obter mais informações sobre como implantar o Azure WebJobs, consulte [Azure WebJobs – Recursos recomendados - Implantação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying).


