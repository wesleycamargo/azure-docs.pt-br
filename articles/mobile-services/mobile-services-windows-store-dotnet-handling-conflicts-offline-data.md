<properties 
	pageTitle="Tratar conflitos de dados offline em aplicativos universais do Windows | Microsoft Azure" 
	description="Saiba como usar o manipulador de conflitos dos Serviços Móveis do Azure ao sincronizar dados offline em seu aplicativo universal do Windows" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="glenga"/>


# Tratamento de conflitos com sincronização de dados offline nos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

##Visão geral

Este tópico mostra como sincronizar dados e tratar conflitos ao usar os recursos offline dos Serviços Móveis do Azure.

Se você preferir assistir a um vídeo, o clipe abaixo segue as mesmas etapas deste tutorial.

> [AZURE.VIDEO build-offline-apps-with-mobile-services]

Neste tutorial, você baixa uma solução universal do Windows em C# para um aplicativo que dá suporte a tratamento de conflitos de sincronização offline. Você integrará um serviço móvel com o aplicativo e executará os clientes Windows Store 8.1 e Windows Phone 8.1 para gerar um conflito de sincronização e resolvê-lo.

Esse tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados offline]. Antes de iniciar este tutorial, primeiro você deverá concluir [Introdução aos dados offline].


##Pré-requisitos

Este tutorial requer o Visual Studio 2013 em execução no Windows 8.1.


##Baixar o projeto de exemplo

![][0]

Este tutorial é um passo a passo de como o [Exemplo de serviços móveis offline de tarefas pendentes] trata conflitos de sincronização entre o repositório offline local e o banco de dados de Serviço Móvel no Azure.

1. Baixe o arquivo zip no [Repositório de exemplos GitHub para Serviços Móveis] e extraia-os em um diretório de trabalho. 

2. Se você ainda não tiver instalado SQLite para Windows 8.1 e Windows Phone 8.1 como mencionado em [Introdução aos dados offline] instale ambos os tempos de execução.

3. No Visual Studio 2013, abra o arquivo de solução *mobile-services-samples\\TodoOffline\\WindowsUniversal\\TodoOffline-Universal.sln*. Pressione a tecla **F5** para recompilar e executar o projeto. Verifique se os pacotes NuGet estão restaurados e se as referências estão definidas corretamente.

    >[AZURE.NOTE]Talvez seja necessário excluir todas as referências antigas no tempo de execução do SQLite e substituí-los por uma referência atualizada conforme mencionado no tutorial [Introdução aos dados offline].

4. No aplicativo, digite algum texto em **Inserir um TodoItem**, em seguida, clique em **Salvar** para adicionar alguns itens de tarefas no repositório local. Em seguida, feche o aplicativo.

Observe que o aplicativo ainda não está conectado a nenhum serviço móvel, portanto, os botões **Push** e **Pull** gerarão exceções.




##Testar o aplicativo no serviço móvel

Está na hora de testar o aplicativo nos Serviços Móveis.

1. No Portal de Gerenciamento do Azure, localize sua chave do aplicativo de serviço móvel clicando em **Gerenciar Chaves** na barra de comandos da guia **Painel**. Copie a **Chave do Aplicativo**.

2. No Gerenciador de Soluções para o Visual Studio, abra o arquivo App.xaml.cs no projeto de amostra do cliente. Altere a inicialização do **MobileServiceClient** para usar a URL do seu serviço móvel e a chave do aplicativo:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. No Visual Studio, pressione a tecla **F5** para compilar e executar o aplicativo novamente.

    ![][0]


##Atualizar os dados no back-end para criar um conflito

Em um cenário do mundo real, um conflito de sincronização ocorre quando um aplicativo envia atualizações por push para um registro no banco de dados e, em seguida, outro aplicativo tenta enviar uma atualização por push para o mesmo registro usando uma versão desatualizada desse registro. Se você se lembrar da [Introdução aos dados offline], a propriedade de versão do sistema é necessária para dar suporte aos recursos de sincronização offline. Essas informações de versão são examinadas com cada atualização do banco de dados. Se uma instância do aplicativo tentar atualizar um registro usando uma versão desatualizada, ocorrerá um conflito e ele será capturado como uma `MobileServicePreconditionFailedException` no aplicativo. Se o aplicativo não capturar a `MobileServicePreconditionFailedException`, uma `MobileServicePushFailedException` terminará sendo lançada e descreverá o número de erros de sincronização encontrados.

>[AZURE.NOTE]Para oferecer suporte à sincronização dos registros excluídos com a sincronização de dados offline, você deverá habilitar a [Exclusão Reversível](mobile-services-using-soft-delete.md). Caso contrário, será necessário remover manualmente os registros do armazenamento local ou chamar `IMobileServiceSyncTable::PurgeAsync()` para limpar o armazenamento local.


As etapas a seguir mostram os clientes Windows Phone 8.1 e Windows Store 8.1 em execução ao mesmo tempo para causar e resolver um conflito usando o exemplo.

1. No Visual Studio, clique com botão direito no projeto do Windows Phone 8.1 e clique em **Configurar como Projeto de Inicialização**. Em seguida, pressione as teclas **Ctrl+F5** para executar o cliente do Windows Phone 8.1 sem depuração. Depois de colocar o cliente do Windows Phone 8.1 em funcionamento no emulador, clique no botão **Pull** para sincronizar o armazenamento local com o estado atual do banco de dados.
 
    ![][3]
 
   
2. No Visual Studio, clique com botão direito no projeto de tempo de execução do Windows 8.1 e clique em **Configurar como o Projeto de Inicialização** para defini-lo novamente como o projeto de inicialização. Em seguida, pressione **F5** para executá-lo. Depois de colocar o cliente da Windows Store 8.1 em funcionamento, clique no botão **Pull** para sincronizar o armazenamento local com o estado atual do banco de dados.

    ![][4]
 
3. Nesse ponto, ambos os clientes estão sincronizados com o banco de dados. O código de ambos os clientes também está usando a sincronização incremental, para que eles sincronizem apenas itens de tarefas pendentes. Itens de tarefas pendentes concluídas serão ignorados. Escolha um dos itens e edite o texto do mesmo item em ambos os clientes com um valor diferente. Clique no botão **Push** para sincronizar as alterações com o banco de dados no servidor.

    ![][5]

    ![][6]


4. O cliente cujo push estava sendo executado por último encontra o conflito e permite que o usuário decida o valor a ser confirmado no banco de dados. A exceção fornece o valor de versão correto, que é usado para resolver o conflito.

    ![][7]



##Revisão do código para tratamento de conflitos de sincronização

Para usar recursos offline nos serviços móveis, você deve incluir a coluna de versão no banco de dados local e o objeto de transferência de dados. Isso é feito atualizando a classe `TodoItem` do seguinte membro:

        [Version]
        public string Version { get; set; }

A coluna `__version` está incluída no banco de dados local do método `OnNavigatedTo()` quando a classe `TodoItem` é usada para definir o armazenamento local.

Para resolver conflitos de sincronização offline em seu código, crie uma classe que implemente `IMobileServiceSyncHandler`. Passe um objeto desse tipo na chamada a `MobileServiceClient.SyncContext.InitializeAsync()`. Isso também ocorre no método `OnNavigatedTo()` do exemplo.

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

A classe `SyncHandler` em **SyncHandler.cs** implementa `IMobileServiceSyncHandler`. O método `ExecuteTableOperationAsync` é chamado quando cada operação de push é enviada ao servidor. Se uma exceção do tipo `MobileServicePreconditionFailedException` for lançada, isso significa que há um conflito entre as versões locais e remotas de um item.

Para resolver conflitos em favor do item local, simplesmente repita a operação. Depois que um conflito ocorreu, a versão local do item será atualizada para corresponder à versão do servidor, portanto, executar a operação novamente substituirá as alterações de servidor pelas alterações locais:

    await operation.ExecuteAsync(); 

Para resolver conflitos em favor do item de servidor, simplesmente retorne da `ExecuteTableOperationAsync`. A versão local do objeto será descartada e substituída pelo valor do servidor.

Para parar a operação de push (mas manter as mudanças enfileiradas), use o método `AbortPush()`:

    operation.AbortPush();

Isso interromperá a operação atual de push mas manterá todas as alterações pendentes, incluindo a operação atual, se `AbortPush` for chamado de `ExecuteTableOperationAsync`. Na próxima vez em que `PushAsync()` for chamado, essas alterações serão enviadas ao servidor.

Quando um push é cancelado, `PushAsync` lançará uma `MobileServicePushFailedException` e a propriedade de exceção `PushResult.Status` terá o valor `MobileServicePushStatus.CancelledByOperation`.



<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/javascript-backend-database.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/dotnet-backend-database.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-view.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-view.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-edit-text.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-edit-text.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/conflict.png




<!-- URLs -->
[Handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=394787
[Get started with Mobile Services]: ../mobile-services-windows-store-get-started.md
[Introdução aos dados offline]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Azure Management Portal]: https://manage.windowsazure.com/
[Handling Database Conflicts]: mobile-services-windows-store-dotnet-handle-database-conflicts.md#test-app
[Repositório de exemplos GitHub para Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=512865
[Exemplo de serviços móveis offline de tarefas pendentes]: http://go.microsoft.com/fwlink/?LinkId=512866
 

<!---HONumber=Oct15_HO3-->