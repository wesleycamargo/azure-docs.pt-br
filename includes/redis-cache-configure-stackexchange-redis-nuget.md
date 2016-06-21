Os aplicativos .NET podem usar o cliente de cache **StackExchange.Redis**, que pode ser configurado no Visual Studio usando um pacote NuGet que simplifica a configuração dos aplicativos do cliente de cache.

>[AZURE.NOTE] Para obter mais informações, consulte a página do github [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) e a [documentação do cliente do cache StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).

Para configurar um aplicativo de cliente no Visual Studio utilizando o pacote NuGet StackExchange.Redis, clique com o botão direito no projeto em **Gerenciador de Soluções** e escolha **Gerenciar pacotes NuGet**.

![Gerenciar pacotes NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Digite **StackExchange.Redis** ou **StackExchange.Redis.StrongName** na caixa de texto de pesquisa, selecione a versão desejada nos resultados e clique em **Instalar**.

>[AZURE.NOTE] Se preferir utilizar uma versão de nome forte da biblioteca do cliente de **StackExchange.Redis**, escolha **StackExchange.Redis.StrongName**; caso contrário escolha **StackExchange.Redis**.

![Pacote NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias para o seu aplicativo de cliente para acessar o cache Redis do Azure com o cliente de cache StackExchange.Redis.

<!---HONumber=AcomDC_0615_2016-->