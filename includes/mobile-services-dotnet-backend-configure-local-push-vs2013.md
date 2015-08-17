
Você também pode testar notificações por push com seu serviço móvel em execução no computador ou VM local antes de publicar no Azure. Para fazer isso, defina as informações sobre o hub de notificação utilizado pelo seu aplicativo no arquivo web.config. Essas informações só são utilizadas quando a execução ocorre localmente para conexão com o hub de notificação; elas são ignoradas quando publicadas no Azure.

1. Abra o arquivo readme.html na pasta do projeto tanto Windows como do aplicativo WindowsPhone. 

	Este exibe a página **Configuração de push está quase concluída**, se você ainda não estiver com ela aberta. A seção **Etapa 3: Modificar o Web Config** contém as informações de conexão do hub de notificação.

2. No seu projeto do serviço móvel no Visual Studio, abra o arquivo Web.config para o serviço e, em **connectionStrings**, adicione a cadeia de conexão **MS\_NotificationHubConnectionString** da página **Configuração de push está quase concluída**.

3. Em **appSettings**, substitua o valor da configuração de aplicativo **MS\_NotificationHubName** pelo nome do hub de notificação encontrado na página **Configuração de push está quase concluída**.

4. Clique com o botão direito do mouse no projeto de serviço móvel clique em **Depurar** e, em seguida em **Iniciar nova instância** e tome nota da URL raiz do serviço da página de inicialização exibida no navegador.

	Esta é a URL do host local para o projeto back-end .NET. Você usará esta URL para testar o aplicativo no serviço móvel eme xecução no computador local.

Agora, o projeto do serviço móvel é configurado para se conectar ao hub de notificação no Azure, quando a execução ocorre localmente. Observe que é importante utilizar o mesmo nome de hub de notificação e cadeia de conexão utilizados no portal, porque essas configurações do arquivo Web.config são substituídas pelas configurações do portal durante a execução no Azure.

<!---HONumber=August15_HO6-->