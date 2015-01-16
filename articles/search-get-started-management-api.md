<properties title="Get started with Azure Search Management REST API" pageTitle="Introdução à API REST de Gerenciamento de Pesquisa do Azure" description="Introdução à API REST de Gerenciamento de Pesquisa do Azure" metaKeywords="" services="Azure Search" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="" ms.date="10/10/2014" ms.author="heidist" />
# Introdução à API REST de Gerenciamento de Pesquisa do Azure #

A API REST de Gerenciamento de Pesquisa do Azure é uma alternativa programática para realizar tarefas administrativas no portal. As operações de gerenciamento de serviço incluem criar ou excluir o serviço, dimensionar o serviço e gerenciar chaves. Este tutorial é fornecido com um aplicativo cliente de exemplo que demonstra a API de gerenciamento de serviço. Ele também inclui etapas de configuração necessárias para executar o exemplo no seu ambiente de desenvolvimento local.

Para concluir este tutorial, você precisará de:

- Visual Studio 2012 ou 2013
- o download do aplicativo cliente de exemplo

Para concluir o tutorial, dois serviços serão provisionados: Pesquisa do Azure e do AD (Active Directory) do Azure. Além disso, você criará um aplicativo AD que estabelece a relação de confiança entre o aplicativo cliente e o ponto de extremidade do gerenciador de recursos no Azure.

Você precisa de uma conta do Azure para concluir este tutorial.


<h2 id="download">Baixar o aplicativo de exemplo</h2>

Este tutorial se baseia em um aplicativo de console do Windows escrito em c#, que você pode editar e executar no Visual Studio 2012 ou 2013

Você pode encontrar o aplicativo cliente em Codeplex na [Demonstração da API de Gerenciamento de Pesquisa do Azure](https://azuresearchmgmtapi.codeplex.com/).


<h2 id="config">Configurar o aplicativo</h2>

Antes de executar o aplicativo de exemplo, você deve habilitar a autenticação para que as solicitações enviadas do aplicativo cliente para o ponto de extremidade do gerenciador de recursos podem ser aceitas. O requisito de autenticação é obtido com o [Gerenciador de Recursos do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn790568.aspx), que é a base para todas as operações relacionadas portal solicitada por meio de uma API, inclusive aquelas relacionadas ao gerenciamento de serviço de pesquisa. A API de gerenciamento de serviço para Pesquisa do Azure é simplesmente uma extensão do Gerenciador de Recursos do Azure e, portanto, herda suas dependências.  

O Gerenciador de Recursos do Azure requer o serviço Active Directory do Azure como seu provedor de identidade. 

Para obter um token de acesso que permitirá às solicitações acessarem o gerenciador de recursos, o aplicativo cliente inclui um segmento de código que chama o Active Directory. O segmento de código e as etapas necessárias para usar o segmento de código foram emprestadas deste artigo: [Solicitações do Gerenciador de Recursos de autenticação do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn790557.aspx).

Você pode seguir as instruções no link acima ou usar as etapas neste documento se preferir acompanhar o tutorial passo a passo.

Nesta seção, você realizará as seguintes tarefas:

1. Criar um serviço do AD
1. Criar um aplicativo do AD
1. Configurar o aplicativo do AD registrando detalhes sobre o aplicativo cliente de exemplo que você baixou
1. Carregar o aplicativo cliente de exemplo com os valores que ele usará para obter autorização para suas solicitações

> [WACOM.NOTE] Estes links fornecem um contexto sobre o uso do Active Directory do Azure para autenticar solicitações de cliente para o gerenciador de recursos: [Gerenciador de Recursos do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn790568.aspx), [Autenticando as solicitações do Gerenciador de Recursos do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn790557.aspx) e [Active Directory do Azure](http://msdn.microsoft.com/library/azure/jj673460.aspx).

<h3>Criar um serviço do Active Directory</h3>

1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

2. Desça no painel de navegação esquerdo e clique em **Ative Directory**.

4. Clique em **NOVO** para abrir **Serviços de Aplicativos** | **Active Directory**. Nesta etapa, você está criando um novo serviço do Active Directory. Esse serviço hospedará o aplicativo do AD que você definirá algumas etapas a partir de agora. Criar um novo serviço ajuda a isolar o tutorial de outros aplicativos que você possa já estar hospedando no Azure.

5. Clique em **Diretório** | **Criação personalizada**.

6. Digite um nome, domínio e localização geográfica do serviço. O domínio deve ser exclusivo. Clique na marca de seleção para criar o serviço.

     ![][5]

<h3>Criar um novo aplicativo do AD para este serviço</h3>

1. Selecione o serviço do Active Directory "SearchTutorial" que você acabou de criar.

2. No menu superior, clique em **Aplicativos**. 
 
3. Clique em **Adicionar um aplicativo**. Um aplicativo do AD armazena informações sobre os aplicativos cliente que o utilizarão como provedor de identidade.  
 
4. Escolha **Adicionar um aplicativo que minha organização está desenvolvendo**. Essa opção fornece configurações de registro para aplicativos que não são publicadas na galeria de aplicativos. Como o aplicativo cliente não faz parte da galeria de aplicativos, essa é a escolha certa para este tutorial.

     ![][6]
 
5. Digite um nome, como "Gerenciador-de-pesquisa-do-Azure".

6. Escolha **Aplicativo cliente nativo** para o tipo de aplicativo. Isso está correto para o aplicativo de exemplo; ele é um aplicativo cliente (console) do Windows, não um aplicativo Web.

     ![][7]
 
7. Em URI de redirecionamento, insira "http://localhost/Azure-Search-Manager-App". Esse é o URI para o qual o Active Directory do Azure redirecionará o agente do usuário em resposta a uma solicitação de autorização OAuth 2.0. O valor não precisa ser um ponto de extremidade físico, mas deve ser um URI válido. 

    Para os fins deste tutorial, o valor pode ser qualquer um, mas aquele que você inserir se torna uma entrada necessária para a conexão administrativa no aplicativo de exemplo. 
 
7. Clique na marca de seleção para criar o aplicativo do Active Directory. Você deve ver "Azure-Search-Manager-App" no painel de navegação à esquerda.

<h3>Configurar o aplicativo do AD</h3>
 
9. Clique no aplicativo do AD, "Azure-Search-Manager-App", que você acabou de criar. Você o verá que listado no painel de navegação à esquerda.

10. Clique em **Configurar** no menu superior.
 
11. Desça até Permissões e selecione **API de Gerenciamento do Windows Azure**. Nesta etapa, você especifica a API (nesse caso, a API do Gerenciador de Recursos do Azure) que o aplicativo cliente precisa acessar, juntamente com o nível de acesso necessário.

12. Em Permissões Delegadas, clique na lista suspensa e selecione **Acessar Gerenciamento de Serviço do Azure (Visualização**).
 
     ![][8]
 
13. Salve as alterações. 

Mantenha a página de configuração de aplicativo aberta. Na próxima etapa, você copiará os valores dessa página e os inserirá no aplicativo de exemplo.

<h3>Carregar o programa do aplicativo de exemplo com valores de registro e assinatura</h3>

Nesta seção, você editará a solução no Visual Studio, substituindo os valores válidos obtidos no portal.
Os valores que você adicionará aparecerão na parte superior de Program.cs:

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Se você ainda não tiver [baixado o aplicativo de exemplo do Codeplex](linkTBD), será preciso baixá-lo para esta etapa.

1. Abra **ManagementAPI.sln** no Visual Studio.

2. Abra Program.cs.

3. Forneça `ClientId`. Da página de configuração do aplicativo AD deixada aberta da etapa anterior, copie a ID do cliente da página de configuração de aplicativo AD no portal e cole-a em Program.cs.

4. Forneça `RedirectUrl`. Copie o URI de redirecionamento da mesma página de portal e cole-o em Program.cs.

	![][9]

5. Forneça `TenantID.` 
	- Volte para Active Directory | SearchTutorial (serviço). 
	- Clique em **Aplicativos** na barra superior. 
	- Clique em **Visualizar Pontos de Extremidade** na parte inferior da página. 
	- Copie o Ponto de Extremidade de Autorização OAUTH 2.0 na parte inferior da lista. 
	- Cole o ponto de extremidade em TenantID, cortando o valor de todos os parâmetros URI, exceto a ID do locatário.

    Dado "https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0", exclua tudo, exceto "55e324c7-1656-4afe-8dc3-43efcd4ffa50".

	![][10]

6. Forneça `SubscriptionID`.
	- Vá para a página principal do portal.
	- Clique em **Configurações** na parte inferior do painel de navegação.
	- Na guia Assinaturas, copie a ID da assinatura e cole-a em Program.cs.

7. Salve e compile a solução.


<h3>Resolvendo erros de compilação</h3>

Problemas com referências de assembly podem impedir que uma solução seja criada. Esta seção fornece soluções alternativas para alguns deles.

- Microsoft.IdentityModel.Clients.ActiveDirectory deve ser instalado (consulte [Autenticando solicitações do Gerenciador de Recursos do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn790557.aspx) para detalhes sobre como instalar a [Biblioteca de autenticação do Active Directory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)).
- Reinstalar um assembly também pode resolver o problema. Acesse **Ferramentas** | **Gerenciador de Pacotes NuGet Pack** | **Console do Gerenciador de Pacotes**. Ao prompt PM>, insira *Update-package -reinstall Microsoft.IdentityModel.Clients.ActiveDirectory*.
- Newtonsoft.Json também é referido nessa solução. Se ocorrer um erro para este assembly, exclua-o do projeto e adicione-o novamente. Clique com o botão direito em **Referências** | **Gerenciar Pacotes NuGet** | **Pacotes Instalados**. Selecione **Json.Net** e desinstale-o. Expanda **Online**, selecione **Json.Net** | **Instalar**.


<h2 id="explore">Explorar o aplicativo</h2>

Adicione um ponto de interrupção na primeira chamada de método para que você possa percorrer o programa. Pressione **F5** para executar o aplicativo e pressione **F11** para percorrer o código.

O aplicativo de exemplo cria um serviço gratuito de Pesquisa do Azure para uma assinatura do Azure existente. Se já existir um serviço gratuito para sua assinatura, o aplicativo de exemplo falhará. É permitido somente um serviço gratuito de Pesquisa por assinatura.

1. Abra Program.cs no Gerenciador de Soluções e acesse a função Main(string[] void). 
 
3. Observe que **ExecuteArmRequest** é usado para executar solicitações com relação ao ponto de extremidade do Gerenciador de Recursos do Azure, `https://management.azure.com/subscriptions` para uma `subscriptionID` especificada. Esse método é usado em todo o programa para executar operações usando a API do Gerenciador de Recursos ou a API de gerenciamento de Pesquisa.

3. Solicitações para o Gerenciador de Recursos do Azure devem ser autenticadas e autorizadas. Isso é feito usando o método **GetAuthorizationHeader** chamado pelo método **ExecuteArmRequest**, emprestado de [solicitações de autenticação de Gerenciador de Recursos do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn790557.aspx). Observe que **GetAuthorizationHeader** chama `https://management.core.windows.net` para obter um token de acesso.

4. Você precisará entrar com um nome de usuário e senha válidos para sua assinatura.

5. Em seguida, um novo serviço de Pesquisa do Azure será registrado com o provedor do Gerenciador de Recursos do Azure. Novamente, isso é o método **ExecuteArmRequest**, usado neste momento para criar o serviço de pesquisa no Azure para sua assinatura por meio de `providers/Microsoft.Search/register`. 

6. O restante do programa usa a [API REST de Gerenciamento de Pesquisa do Azure](http://msdn.microsoft.com/pt-br/library/dn832684.aspx). Observe que a 'api-version' para essa API é diferente da versão de api do Gerenciador de Recursos do Azure. Por exemplo, `/listAdminKeys?api-version=2014-07-31-Preview` mostra o 'api-version' da API REST de gerenciamento de Pesquisa do Azure.

	A próxima série de operações recupera a definição de serviço que você acabou de criar, as chaves de api de administração, regenera recupera as chaves, altera a réplica e a partição e, por fim, exclui o serviço.

	Ao alterar a contagem de réplica ou partição de serviço, espera-se que essa ação falhe se você estiver usando a edição gratuita. Somente a edição standard pode fazer uso de réplicas e partições adicionais.

	Excluir o serviço é a última operação.

<h2 id="next-steps">Próximas etapas</h2>

Depois de ter concluído este tutorial, você pode querer saber mais sobre gerenciamento de serviços ou autenticação com o serviço Active Directory:

- Saiba mais sobre como integrar um aplicativo cliente com o Active Directory. Consulte [Integrando aplicativos no Active Directory do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn151122.aspx).
- Saiba mais sobre outras operações de gerenciamento de serviço no Azure. Consulte [Gerenciando seus serviços](http://msdn.microsoft.com/pt-br/library/azure/dn578292.aspx).

<!--Anchors-->
[Baixar o aplicativo de exemplo]: #Download
[Configurar o aplicativo]: #config
[Explorar o aplicativo]: #explore
[Próximas etapas]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Gerenciar sua solução de pesquisa no Microsoft Azure]: ../search-manage/
[Fluxo de trabalho de desenvolvimento de Pesquisa do Azure]: ../search-workflow/
[Criar sua primeira solução de pesquisa do Azure]: ../search-create-first-solution/
[Criar um aplicativo de pesquisa geoespacial usando a Pesquisa do Azure]: ../search-create-geospatial/


