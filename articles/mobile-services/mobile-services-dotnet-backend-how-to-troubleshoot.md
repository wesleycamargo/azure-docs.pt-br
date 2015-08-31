<properties
	pageTitle="Solucionar problemas de back-end .NET dos Serviços Móveis | Microsoft Azure"
	description="Saiba como diagnosticar e corrigir problemas com os serviços móveis usando o back-end do .NET"
	services="mobile-services"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="wesmc;ricksal"/>

# Solucionar problemas de back-end .NET dos Serviços Móveis

Desenvolver com Serviços Móveis geralmente é uma tarefa fácil e sem problemas, mas de repente podem acontecer coisas e algo pode sair errado. Este tutorial abrange algumas técnicas que permitem a você solucionar problemas comuns que podem surgir com o back-end .NET dos Serviços Móveis.

1. [Depuração de HTTP](#HttpDebugging)
2. [Depuração de tempo de execução](#RuntimeDebugging)
3. [Analisar logs de diagnóstico](#Logs)
4. [Depurar Resolução de Assembly em Nuvem](#AssemblyResolution)
5. [Solucionar problemas de migrações do Entity Framework](#EFMigrations)

<a name="HttpDebugging"></a>
## Depuração de HTTP

Ao desenvolver aplicativos com os Serviços Móveis, você geralmente obtém vantagem com o SDK cliente dos Serviços Móveis para a plataforma que você está usando (Windows Store, iOS, Android, etc.). Entretanto, algumas vezes é útil verificar o nível de HTTP e observar as chamadas brutas que ocorrem na rede. Essa abordagem é particularmente útil para depurar problemas de conectividade e de serialização. Com o back-end .NET dos Serviços Móveis, você pode usar essa abordagem em combinação com a depuração remota e local do Visual Studio (veja mais sobre isso na seção seguinte), para ter uma ideia completa do caminho que uma chamada HTTP percorre antes de chamar seu código de serviço.

Você pode usar qualquer depurador HTTP para enviar e inspecionar tráfego HTTP. [Fiddler](http://www.telerik.com/fiddler) é uma ferramenta popular muito usada para essa finalidade. Para facilitar a vida dos desenvolvedores, os Serviços Móveis trazem um depurador HTTP baseado na Web (também conhecido como “cliente de teste”) junto com seu serviço móvel, reduzindo a necessidade de ferramentas externas. Ao hospedar seu serviço móvel localmente, ele estará disponível em um URI similar a [http://localhost:59233](http://localhost:59233), e ao hospedar na nuvem, o URI será na forma [http://todo-list.azure-mobile.net](http://todo-list.azure-mobile.net). As etapas a seguir funcionam da mesma maneira, independentemente de onde o serviço está hospedado:

1. Inicie com um projeto de servidor dos Serviços Móveis no **Visual Studio 2013 Update 2** ou posterior. Caso não tenha um em mãos, você pode criar um selecionando **Arquivo**, **Novo**, **Projeto** e, em seguida, selecionando o nó **Nuvem** e o modelo **Serviços Móveis do Microsoft Azure**.
2. Pressione **F5**, o que compilará e executará o projeto. Na página inicial, selecione **Experimentar**.

    >[AZURE.NOTE]Se o serviço for hospedado localmente, clicando no link você será direcionado para a próxima página. Entretanto, se hospedar na nuvem, você será solicitado a definir credenciais. Isso é feito para assegurar que usuários não autenticados não tenham acesso às informações sobre sua API e cargas. Para ver a página, é necessário fazer logon com um **nome de usuário em branco** e sua **chave do aplicativo** como a senha. Sua chave do aplicativo está disponível no **Portal de Gerenciamento do Azure**, navegando para a guia **Painel** de seu serviço móvel e selecionando **Gerenciar chaves**.
    >
    > ![Prompt de autenticação para acessar a página de ajuda][HelpPageAuth]

3. A página que você vê (referida como a “página de ajuda") mostra uma lista de todas as APIs de HTTP que seu serviço móvel está disponibilizando. Selecione uma das APIs (se você começou a usar o modelo de projeto de Serviços Móveis no Visual Studio, deve ver **Tabelas GET/TodoItem**).

    ![Página de ajuda][HelpPage]

4. A página resultante mostra a documentação e os exemplos de JSON da solicitação e resposta que a API espera. Selecione o botão **Experimentar**.

    ![Página de teste para uma API][HelpPageApi]

5. Esse é o “cliente de teste", que permite a você enviar uma solicitação HTTP para experimentar sua API. Selecione **enviar**.

    ![Cliente de teste][TestClient]

6. Você verá a resposta HTTP voltando de seu serviço móvel diretamente na janela do navegador.

    ![Cliente de teste com resposta de HTTP][TestClientResponse]

Agora, você está pronto para explorar as APIs de HTTP diferentes expostas por seu serviço móvel na conveniência de seu navegador da Web.

<a name="RuntimeDebugging"></a>
## Depuração de tempo de execução

Um dos principais recursos de back-end .NET é a capacidade de depurar o código de serviço localmente, mas também depurar o código que executa no ambiente em nuvem. Siga estas etapas:

1. Abra o projeto de serviço móvel que você gostaria de depurar no **Visual Studio 2013 Update 2** ou posterior.
2. Configurar carregamento de símbolo. Navegue para o menu **Depurar** e selecione **Opções e Configurações**. Assegure que **Habilitar Apenas Meu Código** esteja desmarcado e que **Habilitar suporte de servidor de origem** esteja marcado.

    ![Configurar carregamento de símbolo][SymbolLoading]

3. Selecione o nó **Símbolos** à esquerda e adicione uma referência para o servidor (SymbolSource)[http://symbolsource.org] usando o URI [http://srv.symbolsource.org/pdb/Public](http://srv.symbolsource.org/pdb/Public). Os símbolos para o back-end do .NET dos Serviços Móveis são disponibilizados lá com uma versão bem nova.

    ![Configurar servidor de símbolo][SymbolServer]

4. Defina um ponto de interrupção na parte do código que você deseja depurar. Por exemplo, defina um ponto de interrupção no método **GetAllTodoItems()** do **TodoItemController** fornecido com o modelo do projeto de Serviços Móveis no Visual Studio.
5. Depure o serviço localmente pressionando **F5**. A primeira carga pode ser lenta, pois o Visual Studio está baixando símbolos para o back-end .NET dos Serviços Móveis.
6. Conforme descrito na seção anterior sobre depuração de HTTP, use o cliente de teste para enviar uma solicitação de HTTP para o método em que você definiu o ponto de interrupção. Por exemplo, você pode enviar uma solicitação para o método **GetAllTodoItems()** selecionando **Tabelas GET/TodoItem** na página de ajuda, depois selecionando **Experimentar** e depois **enviar**.
7. O Visual Studio deverá parar no ponto de interrupção que você definiu, e um rastro completo com código-fonte deverá estar disponível na janela **Pilha de chamadas** no Visual Studio.

    ![Atingindo um ponto de interrupção][Breakpoint]

8. Agora você pode publicar o serviço para o Azure, e poderemos usar a depuração da mesma forma que fizemos acima. Publique o projeto clicando com o botão direito do mouse sobre ele e em **Gerenciador de Soluções** e selecionando **Publicar**.
9. Na guia **Configurações** do assistente de Publicação, selecione a configuração de **Depurar**. Isso assegura que os símbolos de depuração relevantes sejam publicados com seu código.

    ![Depurar publicação][PublishDebug]

10. Depois que o serviço é publicado com êxito, abra o **Gerenciador de Servidores** e expanda os nós **Azure** e **Serviços Móveis**. Entre, se necessário.
11. Clique com o botão direito do mouse no serviço móvel que você acabou de publicar e selecione **Anexar depurador**.

    ![Anexar depurador][AttachDebugger]

12. Assim como você fez na etapa 6, envie uma solicitação de HTTP para chamar o método em que você definiu o ponto de interrupção. Dessa vez, use a página de ajuda e o cliente de teste para o serviço móvel hospedado no Azure.
13. O Visual Studio parará no ponto de interrupção.

Agora, você tem acesso à ação total do depurador do Visual Studio quando desenvolve localmente e com relação ao seu serviço móvel ativo no Azure.

<a name="Logs"></a>
## Analisar logs de diagnóstico

Na medida em que seu serviço móvel trata das solicitações de seus clientes, ele gera uma variedade de informações diagnósticas úteis e também captura algumas exceções encontradas. Além disso, você pode manipular o código do controlador com logs adicionais aproveitando a propriedade do [**Log**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx) disponível na propriedade de [**Serviços**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx) de cada [**TableController**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx).

Quando depurar localmente, os logs serão exibidos na janela de **Saída** do Visual Studio.

![Logs na janela de Saída do Visual Studio][LogsOutputWindow]

Depois de publicar seu serviço no Azure, os logs para a instância de serviço que executa na nuvem estarão disponíveis clicando no serviço móvel no **Gerenciador de Servidores** do Visual Studio e selecionando **Exibir logs**.

![Logs no Gerenciador de Servidores do Visual Studio][LogsServerExplorer]

Os mesmos logs também estão disponíveis no **Portal de Gerenciamento do Azure** na guia **Logs** de seu serviço móvel.

![Faça logon no Portal de Gerenciamento do Azure][LogsPortal]

<a name="AssemblyResolution"></a>
## Depurar Resolução de Assembly em Nuvem

Quando você publica seu serviço móvel no Azure, ele é carregado pelo ambiente de hospedagem dos Serviços Móveis, que assegura a atualização contínua e as correções no pipeline de HTTP que hospeda o código de controlador. Isso inclui todos os assemblies referenciados pelos [pacotes do NuGet back-end .NET](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22): a equipe atualiza constantemente o serviço para usar as versões mais recentes desses assemblies.

Às vezes, é possível surgir conflitos de versão ao se referir a *versões principais diferentes* de assemblies obrigatórios (versões *secundárias* diferentes são permitidas). Frequentemente, isso acontece quando o NuGet solicita que você atualize para a versão mais recente de um dos pacotes usados pelo back-end .NET dos Serviços Móveis.

>[AZURE.NOTE]O aplicativo Serviços Móveis é atualmente compatível apenas com ASP.NET 5.1, a versão 5.2 do ASP.NET não tem suporte. Atualizar os pacotes NuGet ASP.NET para 5.2.* pode resultar em um erro após a implantação.

Se você atualizar esses pacotes, quando publicar o serviço atualizado para o Azure, verá uma página de aviso indicando o conflito:

![Página de ajuda indicando conflito de carregamento de assembly][HelpConflict]

Isso será acompanhado por uma mensagem de exceção semelhante às registradas em seus logs de serviço:

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

Esse problema é fácil de corrigir: basta reverter para uma versão com suporte do assembly obrigatório e publicar novamente seu serviço.

<a name="EFMigrations"></a>
## Solucionar problemas de migrações do Entity Framework

Quando usar o back-end .NET dos Serviços Móveis com um banco de dados SQL, o Entity Framework (EF) será usado como a tecnologia de acesso a dados que permite a você consultar o banco de dados e os objetos persistentes dentro dele. Um aspecto importante que o EF trata em nome do desenvolvedor é como as colunas do banco de dados (também conhecidas como os *esquemas*) mudam de acordo com as classes do modelo especificadas na mudança do código. Esse processo é conhecido como [Codificar Migrações Iniciais](http://msdn.microsoft.com/data/jj591621).

As migrações podem ser complexas e exigir que o estado do banco de dados seja mantido sincronizado com o modelo EF para ser bem-sucedido. Para obter instruções sobre como lidar com migrações com seu serviço móvel e erros que podem surgir, consulte [Como fazer alterações no modelo de dados para um serviço móvel de back-end .NET](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

<!-- IMAGES -->

[HelpPageAuth]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
[HelpPage]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
[HelpPageApi]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
[TestClient]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
[TestClientResponse]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
[SymbolLoading]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
[SymbolServer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
[Breakpoint]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
[PublishDebug]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
[AttachDebugger]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
[LogsOutputWindow]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
[LogsServerExplorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
[LogsPortal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
[HelpConflict]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png

<!---HONumber=August15_HO8-->