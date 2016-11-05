Este tópico mostra como criar um aplicativo tanto com um dispositivo móvel quanto com um cliente Web. Você criará um aplicativo móvel e um aplicativo Web e usará o mesmo banco de dados subjacente para ambos.

Primeiro você criará um novo back-end de aplicativo móvel e um aplicativo simples de *lista de tarefas*, que armazena dados de aplicativo no novo back-end de aplicativo móvel. O back-end do aplicativo móvel usa, para lógica de negócios do lado do servidor, os idiomas do .NET para os quais há suporte. O aplicativo cliente pode usar qualquer plataforma de cliente à qual o aplicativo móvel dá suporte, incluindo iOS, Windows, Xamarin iOS e Xamarin Android.

Em seguida, você criará um aplicativo Web, usando o mesmo banco de dados como seu aplicativo móvel. Ao final do tutorial, você terá um cliente da Web e um cliente móvel que funcionam com os mesmos dados.

> [!NOTE]
> Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## Criar um novo cliente e back-end de aplicativo móvel
* Siga as etapas no tutorial [Criar um aplicativo móvel] para criar tanto um cliente quanto um back-end de aplicativo móvel. Você pode usar qualquer plataforma de cliente à qual os aplicativo móveis dão suporte, incluindo iOS, Windows, Xamarin iOS e Xamarin Android.
* Certifique-se de ter implantado o back-end do aplicativo móvel no Azure e de conectar seu aplicativo de cliente móvel ao back-end hospedado. O projeto de código de aplicativo móvel usa o Entity Framework Code First e inicializa o banco de dados após a primeira solicitação REST de um aplicativo cliente móvel.

## Publicar uma API Web de lista de tarefas pendentes do Visual Studio
Nesta seção, você criará um novo aplicativo Web usando uma solução de exemplo de aplicativo Web. Você modificará o exemplo para usar o mesmo nome do esquema de banco de dados e a mesma cadeia de conexão que o aplicativo móvel.

> [!NOTE]
> Antes de concluir essas etapas, certifique-se de ter inicializado o banco de dados de back-end do aplicativo móvel, conectando a ele um cliente. Caso contrário, o aplicativo Web não poderá se conectar ao mesmo banco de dados.
> 
> 

1. No [Portal do Azure](https://portal.azure.com/), crie um novo aplicativo Web usando o mesmo Grupo de Recursos e Plano de Hospedagem como seu aplicativo móvel.
2. Baixar a solução de exemplo [MultiChannelToDo] e abri-la no Visual Studio. A solução contém um projeto de API da Web e um projeto de aplicativo Web para a interface de usuário do cliente Web.
3. No projeto da API da Web, edite o MultiChannelToDoContext.cs. Em `OnModelCreating`, atualize o nome de esquema para o mesmo que o nome do aplicativo móvel:
   
        modelBuilder.HasDefaultSchema("your_mobile_app"); // your service name, replacing dashes with underscore
4. Em seguida, vamos obter a cadeia de conexão do aplicativo móvel por meio do Portal do Azure:
   
   * Selecione seu aplicativo móvel no portal e clique na parte chamada **Código de usuário**.
   * Na lâmina que se abre, selecione **Todas as configurações** e, em seguida, **Configurações do aplicativo**.
   * Em **Cadeias de conexão**, clique em **Mostrar cadeias de conexão**. Copie o valor da configuração **MS\_TableConnectionString**. Esta é a cadeia de conexão usada pelo aplicativo móvel para conectar-se ao banco de dados SQL.
5. No Visual Studio, clique com botão direito do mouse no projeto de API da Web e selecione **Publicar**. Selecione **Aplicativos Web do Azure** como o destino de publicação e selecione o aplicativo Web que você criou acima. Clique em **Avançar** até chegar à seção **Configurações** do assistente Publicar Web.
6. Na seção **Bancos de dados**, cole a cadeia de conexão do aplicativo móvel como o valor para **MultiChannelToDoContext**. Selecione apenas a caixa de seleção **Usar essa cadeia de conexão em tempo de execução**.
7. Depois de sua API da Web ter sido publicada com êxito no Azure, você verá uma página de confirmação. Copie a URL para o serviço publicado.

## Publicar uma interface de usuário de cliente Web de lista de tarefas pendentes do Visual Studio
Nesta seção você usará um aplicativo de cliente da Web de exemplo, implementado com AngularJS. Em seguida, você usará o Visual Studio para publicar o projeto em um novo aplicativo Web de Serviço de Aplicativo hospedado no Azure.

1. No Visual Studio, abra o projeto **MultiChannelToDo.Web**. Edite o arquivo `js/service/ToDoService.js`, adicionando a URL para a API da Web recém-publicada por você:
   
        var apiPath = "https://your-web-api-site-name.azurewebsites.net";
2. Clique com botão direito do mouse no projeto **MultiChannelToDo.Web** e selecione **Publicar**.
3. No assistente **Publicar para a Web**, selecione **Aplicativo Web do Azure** como o destino de publicação e crie um novo aplicativo Web, sem um banco de dados.
4. Assim que o projeto tiver sido publicado com sucesso, você verá a interface de usuário da Web no seu navegador.

## Testar os aplicativos Web e móveis
1. Na interface de usuário da Web, adicione ou edite alguns itens de tarefas pendentes.
   
    ![Exibição de aplicativo Web no navegador](./media/app-service-mobile-dotnet-backend-web-and-mobile/web-app-in-browser.png)
2. Execute o aplicativo móvel que você criou no tutorial [Criar um aplicativo móvel]. Você verá os mesmos itens de tarefas pendentes no aplicativo Web.
   
    ![Exibição do aplicativo móvel Xamarin](./media/app-service-mobile-dotnet-backend-web-and-mobile/xamarin-ios-quickstart-device.png)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Links -->

[MultiChannelToDo]: https://github.com/Azure/mobile-services-samples/tree/web-mobile/MultiChannelToDo
[Criar um aplicativo móvel]: ../article/app-service-mobile/app-service-mobile-xamarin-ios-get-started.md

<!---HONumber=AcomDC_1203_2015-->