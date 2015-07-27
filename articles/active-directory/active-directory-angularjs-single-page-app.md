<properties
	pageTitle="Como compilar um aplicativo de página única AngularJS com o AD do Azure"
	description="Demonstra o uso da Active Directory Authentication Library (ADAL) para Javascript para proteger um AngularJS com base em aplicativos de única página, implementados com uma API Web back-end do ASP.NET, que chama outra API Web ASP.NET usando CORS."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/11/2015"
	ms.author="justinha"/>


# Como compilar um aplicativo de página única AngularJS com o AD do Azure

Esse tutorial demonstra o uso da Active Directory Authentication Library (ADAL) para Javascript para proteger um AngularJS com base em aplicativos de única página, implementados com uma API Web back-end do ASP.NET, que chama outra API Web ASP.NET usando CORS. Para ver o exemplo de código usado neste tutorial, consulte [AzureADSamples/SinglePageApp-AngularJS-DotNet](https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet) no github.

O ADAL para Javascript é uma biblioteca de software livre. Para obter as opções de distribuição, código-fonte e contribuições, consulte o [repositório ADAL JS](https://github.com/AzureAD/azure-activedirectory-library-for-js).

Para obter mais informações sobre como os protocolos funcionam neste cenário e outros cenários, consulte [Cenários de autenticação do Azure AD](http://go.microsoft.com/fwlink/?LinkId=394414).

## Como executar esse exemplo

A introdução é simples! Para executar este exemplo, você precisará de:

- Visual Studio 2013
- Uma conexão com a Internet
- Uma assinatura do Azure (uma [avaliação gratuita](https://account.windowsazure.com/organization) é suficiente)

Cada assinatura do Azure tem um locatário do Active Directory do Azure (AD do Azure) associado. Todos os recursos do AD do Azure usados por este exemplo estão disponíveis gratuitamente.

## Clonar ou baixar este repositório

No shell ou na linha de comando: `git clone https://github.com/AzureADSamples/SinglePageApp-WebAPI-AngularJS-DotNet.git`

## Registrar o To Go API Service com seu locatário do Active Directory do Azure

1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em **Active Directory** no painel de navegação à esquerda.
3. Clique na pasta onde você deseja registrar o aplicativo de exemplo.
4. Clique na guia **Aplicativos**.
5. Na gaveta, clique em **Adicionar**.
6. Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.
7. Insira um nome amigável para o aplicativo, por exemplo, "API To Go", selecione **Aplicativo Web** e/ou **API Web** e clique em **Avançar**.
8. Para a URL de logon, insira a URL base para o exemplo, que é, por padrão, `https://localhost:44327/`.
9. Para o URI de ID do aplicativo, insira `https://<your_directory_name>/ToGoAPI`, substituindo `<your_directory_name>` pelo nome do seu diretório do Azure AD. Salve a configuração.

Tudo feito! Antes de passar para a próxima etapa, você precisa localizar o URI de ID do aplicativo de sua API.

1. Ainda no portal do Azure, clique na guia **Configurar** de seu aplicativo.
2. Localize o valor do URI de ID do aplicativo e copie-o para a área de transferência.

## Configure o Ir para a API para usar o seu locatário do Active Directory do Azure

1. Abra a solução no Visual Studio 2013.
2. No projeto ToGoAPI, abra o arquivo `web.config`.
3. Localize a chave do aplicativo `ida:Tenant` e substitua o valor pelo nome de locatário do Azure AD.
4. Localize a chave do aplicativo `ida:Audience` e substitua o valor pelo URI de ID do aplicativo que você copiou do portal do Azure.
5. Também no projeto ToGoAPI, abra o arquivo `Controllers/ToGoListController.cs`. No atributo `[EnableCors...]`, digite o local do cliente To Do SPA. Por padrão, ele é `https://localhost:44326`. Certifique-se de omitir a barra à direita.
5. No projeto TodoSPA, abra o arquivo `App/Scripts/App.js` e localize a declaração do objeto `endpoints`.
6. Insira um mapeamento do local de ponto de extremidade de Ir para API para o seu identificador de recurso ou URI de ID do aplicativo. O nome da propriedade do objeto `endpoints` deve ser o local da API To Go. Por padrão, ele é `https://localhost:44327/`. O valor dessa propriedade deve ser o URI de ID do aplicativo copiado do portal, por exemplo, `https://<your_tenant_name>/ToGoAPI`.
8. Não se preocupe ainda com os outros valores de configuração nesse arquivo, vamos voltar para eles em instantes.
9. Também no projeto TodoSPA, abra o arquivo `App/Scripts/toGoListSvc.js`. Substitua o valor da variável `apiEndpoint` pelo local de sua API To Go. Por padrão, ele é `https://localhost:44327/`.

## Registre o Fazer aplicativo de página única com seu locatário do Active Directory do Azure

1. Entre novamente no [portal de gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em **Active Directory** no painel de navegação à esquerda.
3. Clique no locatário onde você deseja registrar o aplicativo de exemplo.
4. Clique na guia **Aplicativos**.
5. Na gaveta, clique em **Adicionar**.
6. Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.
7. Insira um nome amigável para o aplicativo, por exemplo, "To Do SPA", selecione o **Aplicativo Web e/ou API Web** e clique em **Avançar**.
8. Para a URL de logon, insira a URL base para o exemplo, que é, por padrão, `https://localhost:44326/`.
9. Para o URI de ID do aplicativo, insira `https://<your_directory_name>/ToDoSPA`, substituindo `<your_directory_name>` pelo nome do seu diretório do Azure AD.
10. Na seção **Permissões para outros aplicativos**, clique em **Adicionar aplicativo**. Selecione **Outros** na lista suspensa **Mostrar** e clique na marca de seleção superior. Localize e clique em Ir para API e clique na marca de seleção da parte inferior para adicionar o aplicativo. Selecione **Acessar API To Go** na lista suspensa **Permissões delegadas** e salve a configuração.

Tudo feito! Antes de passar para a próxima etapa, você precisa localizar a ID de cliente do seu aplicativo.

1. Ainda no portal do Azure, clique na guia **Configurar** de seu aplicativo.
2. Localize o valor da ID do cliente e copie-o na área de transferência.


## Habilite a concessão implícita OAuth2 para seu aplicativo

Por padrão, os aplicativos provisionados no AD do Azure não são habilitados para usar a concessão implícita OAuth2. Para executar este exemplo, você precisa aceitar explicitamente.

1. A partir das etapas anteriores, o navegador ainda deve estar no portal de gerenciamento do Azure - e, especificamente, exibindo a guia **Configurar** da entrada do aplicativo.
2. Usando o botão **Gerenciar manifesto** na gaveta, baixe o arquivo de manifesto do aplicativo e salve-o em disco.
3. Abra o arquivo de manifesto com um editor de texto. Pesquise a propriedade `oauth2AllowImplicitFlow`. Você descobrirá que ela está definida como `false`; altere-a para `true` e salve o arquivo.
4. Usando o botão **Gerenciar manifesto**, carregue o arquivo de manifesto atualizado. Salve a configuração do aplicativo.

## Configure o Fazer SPA para usar o seu locatário do Active Directory do Azure

1. Abra a solução no Visual Studio 2013.
2. No projeto TodoSPA, abra o arquivo `web.config`.
3. Localize a chave do aplicativo `ida:Tenant` e substitua o valor pelo nome do diretório do Azure AD.
4. Localize a chave do aplicativo `ida:Audience` e substitua o valor pela ID do cliente no portal do Azure.
5. Também no projeto TodoSPA, abra o arquivo `App/Scripts/App.js` novamente e localize a linha `adalAuthenticationServiceProvider.init(`.
6. Substitua o valor de `tenant` pelo seu nome de diretório do Azure AD.
7. Substitua o valor de `clientId` pela ID do cliente no portal do Azure.

## Execute o exemplo

Limpe a solução, recompile a solução e execute-a.

Você pode disparar o processo de inscrição clicando no link de inscrição no canto superior direito ou clicando diretamente nas guias **Lista de tarefas** ou **Lista To Go**. Explore o exemplo entrando, adicionando itens à Lista de tarefas, removendo a conta de usuário e iniciando novamente. Adicione locais à Lista para ir, executando as operações CRUD com relação à API para ir usando CORS.

## Como implantar este exemplo no Azure

Para implantar o SPA para fazer e a API para ir para sites do Azure, crie dois sites, publique cada projeto em um site e atualize os dois projetos para referenciar os novos locais em vez do IIS Express.

### Crie o Site do Azure Ir para API

1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em **Sites** no painel de navegação à esquerda.
3. Clique em **Novo** no canto inferior esquerdo, selecione **Computação** > **Site** > **Criação personalizada**, selecione o plano de hospedagem e a região e dê um nome, por exemplo, togo-contoso.azurewebsites.net, ao seu site. Selecione um banco de dados para usar ou criar um novo. Clique em **Criar site**.
4. Quando o site é criado, clique nele para gerenciá-lo. Para esse conjunto de etapas, baixe o arquivo .publishsettings e salve-o. Outros mecanismos de implantação, como controle do código-fonte, também podem ser usados. Para obter mais informações sobre como usar um arquivo .publishsettings, consulte [Como: Conectar-se à sua assinatura](../install-configure-powershell.md#Connect).

### Criar um Site do Azure Fazer SPA

1. Navegue até o [portal de gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em **Sites** no painel de navegação à esquerda.
3. Clique em **Novo** no canto inferior esquerdo, selecione **Computação** > **Site** > **Criação personalizada**, selecione o plano de hospedagem e a região e dê um nome, por exemplo, todo-contoso.azurewebsites.net, ao seu site. Selecione um banco de dados para uso; o mesmo banco de dados para o Ir para API é suficiente. Clique em **Criar Site**.
4. Quando o site é criado, clique nele para gerenciá-lo. Mais uma vez, baixe o arquivo .publishsettings para este site e salve-o.

### Atualize ambos os projetos para usar Sites do Azure

1. No Visual Studio, vá para o projeto TodoSPA.
2. Duas alterações são necessárias. Em `App\Scripts\app.js`, substitua o nome da propriedade do objeto `endpoints` pelo novo local do sua API To Go, por exemplo, `https://togo-contoso.azurewebsites.net/`. Em `App\Scripts\toGoListSvc.js`, substitua a variável `apiEndpoint` pelo mesmo valor.
3. No projeto ToGoAPI, apenas uma alteração será necessária. Em `Controllers\ToGoListController.cs`, atualize o atributo `[EnableCors...]` para refletir o novo local de To Do SPA, por exemplo, `https://todo-contoso.azurewebsites.net`. Mais uma vez, certifique-se de omitir a barra à direita.

### Publicar o Ir para API para Sites do Azure

1. Alterne para o Visual Studio e vá para o projeto ToGoAPI. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **Publicar**. Clique em **Importar** e importe o perfil de publicação da API To Go baixado.
6. Na guia **Conexão**, atualize a URL de destino para que ela seja https, como https://togo-constoso.azurewebsites.net. Clique em **Próximo**.
7. Na guia **Configurações**, verifique se **Habilitar autenticação organizacional** NÃO está selecionado. Clique em **Publicar**.
8. O Visual Studio publicará o projeto e abrirá automaticamente um navegador para a URL do projeto. Se você vir a página da Web padrão do projeto, a publicação foi bem-sucedida.

### Publique o Fazer SPA para Sites do Azure

1. Alterne para o Visual Studio e vá para o projeto TodoSPA. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **Publicar**. Clique em **Importar** e importe o arquivo .publishsettings de To Do SPA baixado.
6. Na guia **Conexão**, atualize a URL de destino para que ela seja https, como https://todo-contoso.azurewebsites.net. Clique em **Próximo**.
7. Na guia **Configurações**, verifique se **Habilitar autenticação organizacional** NÃO está selecionado. Clique em **Publicar**.
8. O Visual Studio publicará o projeto e abrirá automaticamente um navegador para a URL do projeto. Se você vir a página da Web padrão do projeto, a publicação foi bem-sucedida.

### Atualização da Configuração Fazer SPA no Locatário de Diretório

1. Navegue até o [portal de gerenciamento do Azure](https://manage.windowsazure.com).
2. No painel de navegação à esquerda, clique em **Active Directory** e selecione seu locatário.
3. Na guia **Aplicativos**, selecione o aplicativo **To Do SPA**.
4. Na guia **Configurar**, atualize os campos **URL de logon** e **URL de resposta** para o endereço do seu SPA, como https://todo-contoso.azurewebsites.net. Salve a configuração.

## Sobre o código

Os arquivos de chave que contêm a lógica de autenticação são os seguintes:

- **App.js** - injeta a dependência de módulo ADAL, fornece os valores de configuração do aplicativo usados por ADAL para conduzir as interações de protocolo com o Azure AD e indica as rotas que não devem ser acessadas sem autenticação anterior.

- **index.html** - contém uma referência a adal.js

- **HomeController.js**- mostra como aproveitar os métodos de login() e logOut() no ADAL.

- **UserDataController.js** - mostra como extrair informações de usuário do id_token em cache.

Agradecimentos especiais a @matvelloso por auxiliar na criação deste tutorial.


## Próximas etapas

Aqui estão alguns recursos adicionais para ajudá-lo a usar o AD do Azure para adicionar autenticação e autorização a aplicativos Web e APIs da web:

+ [Exemplos de código do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn646737.aspx)
+ [Cenários de autenticação do Azure AD](https://msdn.microsoft.com/library/azure/dn499820.aspx)
 

<!---HONumber=July15_HO3-->