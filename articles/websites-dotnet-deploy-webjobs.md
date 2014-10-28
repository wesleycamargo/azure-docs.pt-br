<properties linkid="websites-dotnet-deploy-webjobs" urlDisplayName="Deploy Azure WebJobs to Azure Websites" pageTitle="Deploy Azure WebJobs to Azure Websites" metaKeywords="Azure Websites, WebJobs, background tasks" description="Learn how to deploy Azure WebJobs to Azure Websites using Visual Studio." metaCanonical="" services="web-sites" documentationCenter="" title="Deploy Azure WebJobs to Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

# Como implantar Trabalhos Web do Azure em Sites do Azure

Este tópico explica como usar o Visual Studio para implantar um projeto do Aplicativo de Console em um Site do Azure como um [Trabalho Web do Azure][Trabalho Web do Azure]. Uma maneira alternativa de implantar Trabalhos Web é usar o Portal de Gerenciamento do Azure. Para obter informações sobre esse método, consulte [Usar Trabalhos Web para executar tarefas em segundo plano em Sites do Microsoft Azure][Usar Trabalhos Web para executar tarefas em segundo plano em Sites do Microsoft Azure].

## Sumário

-   [Visão geral][Visão geral]
-   [Pré-requisitos][Pré-requisitos]
-   [Habilitar implantação de Trabalhos Web para um projeto do Aplicativo de Console existente][Habilitar implantação de Trabalhos Web para um projeto do Aplicativo de Console existente]
-   [Criar um novo projeto habilitado para Trabalhos Web][Criar um novo projeto habilitado para Trabalhos Web]
-   [A caixa de diálogo Adicionar Trabalho Web do Azure][A caixa de diálogo Adicionar Trabalho Web do Azure]
-   [O arquivo webjob-publish-settings.json][O arquivo webjob-publish-settings.json]
-   [O arquivo webjobs-list.json][O arquivo webjobs-list.json]
-   [Implantar um projeto de Trabalhos Web][Implantar um projeto de Trabalhos Web]
-   [Próximas etapas][Próximas etapas]

## <span id="overview"></span></a>Visão geral

Ao implantar um projeto do Aplicativo de Console habilitado para Trabalhos Web, o Visual Studio realiza duas tarefas:

-   Copia arquivos de tempo de execução para a pasta apropriada no Site do Azure (*App\_Data/jobs/continuous* para Trabalhos Web contínuos, *App\_Data/jobs/triggered* para Trabalhos Web agendados e sob demanda).
-   Configura [Trabalhos do Agendador do Azure][Trabalhos do Agendador do Azure] para Trabalhos Web agendados a serem executados em determinados horários. (Isso não é necessário para Trabalhos Web contínuos.)

Um projeto habilitado para Trabalhos Web tem os seguintes itens adicionados:

-   O pacote NuGet [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish].
-   Um arquivo [webjob-publish-settings.json][O arquivo webjob-publish-settings.json] que contém configurações de implantação e agendador.

![Diagrama que mostra o que é adicionado a um Aplicativo de Console para habilitar implantação como um Trabalho Web][Diagrama que mostra o que é adicionado a um Aplicativo de Console para habilitar implantação como um Trabalho Web]

É possível adicionar esses itens a um projeto do Aplicativo de Console existente ou usar um modelo para criar um novo projeto do Aplicativo de Console habilitado para Trabalhos Web.

É possível implantar um projeto como um Trabalho Web propriamente dito ou vinculá-lo a um projeto Web de forma que ele seja implantado automaticamente sempre que você implanta o projeto Web. Para vincular projetos, o Visual Studio inclui o nome do projeto habilitado para Trabalhos Web em um arquivo [webjobs-list.json][O arquivo webjobs-list.json] no projeto Web.

![Diagrama que mostra o projeto de Trabalho Web sendo vinculado ao projeto Web][Diagrama que mostra o projeto de Trabalho Web sendo vinculado ao projeto Web]

## Pré-requisitos

Os recursos de implantação de Trabalhos Web estão disponíveis no Visual Studio 2013 quando você instala o SDK do Azure versão 2.4 ou posterior:

-   [SDK do Azure para o Visual Studio 2013][SDK do Azure para o Visual Studio 2013].

Os recursos de implantação de Trabalhos Web também estão incluídos na [Atualização 3][Atualização 3].

## <span id="convert"></span></a>Habilitar implantação de Trabalhos Web para um projeto do Aplicativo de Console existente

Você tem duas opções:

-   [Habilitar implantação automática com um projeto Web][Habilitar implantação automática com um projeto Web].

    Configure um projeto do Aplicativo de Console existente de forma que ele seja implantado automaticamente como um Trabalho Web quando você implanta o projeto Web. Use essa opção quando você quiser executar o Trabalho Web no mesmo site em que executa o aplicativo Web relacionado.

-   [Habilitar implantação sem um projeto Web][Habilitar implantação sem um projeto Web].

    Configure um projeto do Aplicativo de Console existente a ser implantado como um Trabalho Web propriamente dito, sem link para um projeto Web. Use essa opção quando você quiser executar um Trabalho Web em um site propriamente dito, sem aplicativo Web em execução no site. Você talvez queira fazer isso para ser capaz de dimensionar os recursos de Trabalho Web independentemente dos recursos do aplicativo Web.

### <span id="convertlink"></span></a> Habilitar implantação de Trabalhos Web automática com um projeto Web

1.  Clique com o botão direito do mouse no projeto Web no **Gerenciador de Soluções** e clique em **Adicionar** \> **Projeto Existente como Trabalho Web do Azure**.

    ![Projeto Existente como Trabalho Web do Azure][Projeto Existente como Trabalho Web do Azure]

    A caixa de diálogo [Adicionar Trabalho Web do Azure][A caixa de diálogo Adicionar Trabalho Web do Azure] é exibida.

2.  Na lista suspensa **Nome do projeto**, selecione o projeto do Aplicativo de Console a ser adicionado como um Trabalho Web.

    ![Selecionando projeto na caixa de diálogo Adicionar Trabalho Web do Azure][Selecionando projeto na caixa de diálogo Adicionar Trabalho Web do Azure]

3.  Complete a caixa de diálogo [Adicionar Trabalho Web do Azure][A caixa de diálogo Adicionar Trabalho Web do Azure] e clique em **OK**.

### <span id="convertnolink"></span></a> Habilitar implantação de Trabalhos Web sem um projeto Web

1.  Clique com o botão direito do mouse no projeto do Aplicativo de Console no **Gerenciador de Soluções** e clique em **Publicar como Trabalho Web do Azure**.

    ![Publicar como Trabalho Web do Azure][Publicar como Trabalho Web do Azure]

    A caixa de diálogo [Adicionar Trabalho Web do Azure][A caixa de diálogo Adicionar Trabalho Web do Azure] é exibida, com o projeto selecionado na caixa **Nome do projeto**.

2.  Complete a caixa de diálogo [Adicionar Trabalho Web do Azure][A caixa de diálogo Adicionar Trabalho Web do Azure] e clique em **OK**.

    O assistente **Publicar Web** é exibido. Se você não quiser publicar imediatamente, feche o assistente. As configurações inseridas por você são salvas para quando quiser [implantar o projeto][Implantar um projeto de Trabalhos Web].

## <span id="create"></span></a>Criar um novo projeto habilitado para Trabalhos Web

Para criar um projeto habilitado para Trabalhos Web, é possível usar o modelo de projeto do Aplicativo de Console e habilitar a implantação de Trabalhos Web conforme explicado na [seção anterior][Habilitar implantação de Trabalhos Web para um projeto do Aplicativo de Console existente]. Também é possível usar o modelo do novo projeto de Trabalhos Web:

-   [Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web][Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web]

    Crie um projeto que seja configurado para ser implantado automaticamente como um Trabalho Web quando um projeto Web na mesma solução for implantado. Use essa opção quando você quiser executar o Trabalho Web no mesmo site em que executa o aplicativo Web relacionado.

-   [Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente][Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente]

    Crie um projeto e o configure para ser implantado propriamente dito como um Trabalho Web, sem link para um projeto Web. Use essa opção quando você quiser executar um Trabalho Web em um site propriamente dito, sem aplicativo Web em execução no site. Você talvez queira fazer isso para ser capaz de dimensionar os recursos de Trabalho Web independentemente dos recursos do aplicativo Web.

Na versão do SDK 2.4, o novo modelo de projeto de Trabalhos Web não é muito mais fácil do que a criação de um projeto do Aplicativo de Console e a habilitação da implantação de Trabalhos Web. No futuro, o novo modelo de projeto de Trabalhos Web será mais conveniente para o desenvolvimento do [SDK de Trabalhos Web][SDK de Trabalhos Web], porque ele instalará automaticamente os pacotes NuGet do SDK de Trabalhos Web apropriados. Até lá, você pode configurar um projeto para usar o SDK de Trabalhos Web instalando os pacotes manualmente conforme mostrado no [tutorial do SDK de Trabalhos Web][SDK de Trabalhos Web].

### <span id="createlink"></span></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web

-   Clique com o botão direito do mouse no projeto Web no **Gerenciador de Soluções** e clique em **Adicionar** \> **Novo Projeto de Trabalho Web do Azure**.

    ![Entrada de menu de Novo Projeto de Trabalho Web do Azure][Entrada de menu de Novo Projeto de Trabalho Web do Azure]

    A caixa de diálogo [Adicionar Trabalho Web do Azure][A caixa de diálogo Adicionar Trabalho Web do Azure] é exibida.

-   Complete a caixa de diálogo [Adicionar Trabalho Web do Azure][A caixa de diálogo Adicionar Trabalho Web do Azure] e clique em **OK**.

### <span id="createnolink"></span></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente

1.  Clique em **Arquivo** \> **Novo Projeto** e, na caixa de diálogo **Novo Projeto**, clique em **Nuvem** \> **Trabalho Web do Microsoft Azure**.

    ![Caixa de diálogo Novo Projeto mostrando o modelo de Trabalho Web][Caixa de diálogo Novo Projeto mostrando o modelo de Trabalho Web]

2.  Siga as direções mostradas anteriormente para [tornar o projeto do Aplicativo de Console um projeto de Trabalhos Web independente][Habilitar implantação sem um projeto Web].

## <span id="configure"></span></a>A caixa de diálogo Adicionar Trabalho Web do Azure

A caixa de diálogo **Adicionar Trabalho Web do Azure** permite inserir as configurações de nome e agendamento do Trabalho Web.

![Caixa de diálogo Adicionar Trabalho Web do Azure][Caixa de diálogo Adicionar Trabalho Web do Azure]

Os campos nessa caixa de diálogo correspondem aos campos na caixa de diálogo **Novo Trabalho** do Portal de Gerenciamento do Azure. Para obter mais informações, consulte [Usar Trabalhos Web para executar tarefas em segundo plano nos Sites do Microsoft Azure][Usar Trabalhos Web para executar tarefas em segundo plano em Sites do Microsoft Azure].

Para um Trabalho Web agendado (e não para Trabalhos Web contínuos), o Visual Studio cria uma coleção de trabalhos do [Agendador do Azure][Agendador do Azure] caso ainda não exista uma e cria um trabalho na coleção:

-   A coleção de trabalhos do trabalho do agendador se chama *WebJobs-{regionname}* em que *{regionname}* se refere à região em que o site está hospedado. Por exemplo: WebJobs-WestUS.
-   O trabalho do agendador se chama *{websitename}-{webjobname}*. Por exemplo: MyWebSite-MyWebJob.

> [WACOM.NOTE]
>
> -   A implantação de linha de comando que usa MSBuild não configura o agendamento para Trabalhos Web agendados. Esse recurso só funciona quando você o implanta usando o Visual Studio.
> -   Se você configurar um **Trabalho Recorrente** e definir a frequência de recorrência como um número de minutos, o serviço Agendador do Azure não estará livre. Outra frequências (horas, dias etc.) estão livres.
> -   Se você implantar um Trabalho Web e depois alterar o modo de execução de contínuo para não contínuo ou vice-versa, o Visual Studio criará um novo Trabalho Web no Azure quando você o reimplantar. Se você alterar outras configurações de agendamento, mas deixar o modo de execução igual ou alternar Agendado e Sob Demanda, o Visual Studio atualizará o trabalho existente, em vez de criar um novo.

## <span id="publishsettings"></span></a>webjob-publish-settings.json

Quando você configura um Aplicativo de Console para implantação de Trabalhos Web, o Visual Studio instala o pacote NuGet [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish] e armazena informações de agendamento em um arquivo *webjob-publish-settings.json* na pasta *Propriedades* do projeto de Trabalhos Web. Aqui está um exemplo desse arquivo:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

É possível editar esse arquivo diretamente, e o Visual Studio fornece o IntelliSense. O esquema de arquivo é armazenado em [][]<http://schemastore.org></a> e pode ser exibido aqui.

> [WACOM.NOTE]
>
> -   Se você configurar um **Trabalho Recorrente** e definir a frequência de recorrência como um número de minutos, o serviço Agendador do Azure não estará livre. Outra frequências (horas, dias etc.) estão livres.

## <span id="webjobslist"></span></a>webjobs-list.json

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

É possível editar esse arquivo diretamente, e o Visual Studio fornece o IntelliSense. O esquema de arquivo é armazenado em [][1]<http://schemastore.org></a> e pode ser exibido aqui.

## <span id="deploy"></span></a>Implantar um projeto de Trabalhos Web

Um projeto de Trabalhos Web vinculado a um projeto Web é implantado automaticamente com o projeto Web. Para obter informações sobre a implantação do projeto Web, consulte [Como implantar um Site do Azure][Como implantar um Site do Azure].

Para implantar um projeto de Trabalhos Web propriamente dito, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e clique em **Publicar como Trabalho Web do Azure**.

![Publicar como Trabalho Web do Azure][Publicar como Trabalho Web do Azure]

Para um Trabalho Web independente, o mesmo assistente **Publicar Web** usado em projetos Web é exibido, mas com menos configurações disponíveis para serem alteradas.

## <span id="nextsteps"></span></a>Próximas etapas

Para obter mais informações sobre Trabalhos Web do Azure e o SDK de Trabalhos Web, consulte [Trabalhos Web do Azure - Recursos recomendados][Trabalhos Web do Azure - Recursos recomendados].

  [Trabalho Web do Azure]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Usar Trabalhos Web para executar tarefas em segundo plano em Sites do Microsoft Azure]: /pt-br/documentation/articles/web-sites-create-web-jobs/
  [Visão geral]: #overview
  [Pré-requisitos]: #prerequisites
  [Habilitar implantação de Trabalhos Web para um projeto do Aplicativo de Console existente]: #convert
  [Criar um novo projeto habilitado para Trabalhos Web]: #create
  [A caixa de diálogo Adicionar Trabalho Web do Azure]: #configure
  [O arquivo webjob-publish-settings.json]: #publishsettings
  [O arquivo webjobs-list.json]: #webjobslist
  [Implantar um projeto de Trabalhos Web]: #deploy
  [Próximas etapas]: #next-steps
  [Trabalhos do Agendador do Azure]: #scheduler
  [Microsoft.Web.WebJobs.Publish]: http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/
  [Diagrama que mostra o que é adicionado a um Aplicativo de Console para habilitar implantação como um Trabalho Web]: ./media/websites-dotnet-deploy-webjobs/convert.png
  [Diagrama que mostra o projeto de Trabalho Web sendo vinculado ao projeto Web]: ./media/websites-dotnet-deploy-webjobs/link.png
  [SDK do Azure para o Visual Studio 2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Atualização 3]: http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409
  [Habilitar implantação automática com um projeto Web]: #convertlink
  [Habilitar implantação sem um projeto Web]: #convertnolink
  [Projeto Existente como Trabalho Web do Azure]: ./media/websites-dotnet-deploy-webjobs/eawj.png
  [Selecionando projeto na caixa de diálogo Adicionar Trabalho Web do Azure]: ./media/websites-dotnet-deploy-webjobs/aaw1.png
  [Publicar como Trabalho Web do Azure]: ./media/websites-dotnet-deploy-webjobs/paw.png
  [Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web]: #createlink
  [Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente]: #createnolink
  [SDK de Trabalhos Web]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Entrada de menu de Novo Projeto de Trabalho Web do Azure]: ./media/websites-dotnet-deploy-webjobs/nawj.png
  [Caixa de diálogo Novo Projeto mostrando o modelo de Trabalho Web]: ./media/websites-dotnet-deploy-webjobs/np.png
  [Caixa de diálogo Adicionar Trabalho Web do Azure]: ./media/websites-dotnet-deploy-webjobs/aaw2.png
  [Agendador do Azure]: /pt-br/services/scheduler/
  []: http://schemastore.org/schemas/json/webjob-publish-settings.json
  [1]: http://schemastore.org/schemas/json/webjobs-list.json
  [Como implantar um Site do Azure]: /pt-br/documentation/articles/websites-dotnet-deploy/
  [Trabalhos Web do Azure - Recursos recomendados]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources
