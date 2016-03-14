<properties
   pageTitle="Funções de aplicativo | Microsoft Azure"
   description="Como executar a autorização usando funções de aplicativo"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

#  Funções de aplicativo em aplicativos multilocatários

Este artigo faz [parte de uma série]. Esta série também vem com um [aplicativo de exemplo] completo.

As funções do aplicativo são usadas para atribuir permissões aos usuários. Por exemplo, aplicativo [Tailspin Surveys][Tailspin] define as seguintes funções:

- Administrador. Pode executar todas as operações CRUD em qualquer pesquisa que pertença ao locatário.
- Criador. Pode criar novas pesquisas.
- Leitor. Pode ler todas as pesquisas que pertençam ao locatário.

Você pode ver que as funções são definitivamente traduzidas em permissões durante a [autorização]. No entanto, a primeira pergunta é como atribuir e gerenciar funções. Identificamos as três opções principais:

-	[Funções de Aplicativo do Azure AD](#roles-using-azure-ad-app-roles)
-	[Grupos de segurança do AD Azure](#roles-using-azure-ad-security-groups)
-	[Gerenciador de funções do aplicativo](#roles-using-an-application-role-manager).

## Funções usando funções de aplicativo do Azure AD

Essa é a abordagem que usamos no aplicativo Tailspin Surveys.

Nessa abordagem, o provedor de SaaS define as funções do aplicativo adicionando-as ao manifesto do aplicativo. Depois que um cliente se inscrever, um administrador do diretório do AD do cliente atribuirá usuários às funções. Quando um usuário entrar, as funções atribuídas ao usuário serão enviadas como declarações.

> [AZURE.NOTE] Se o cliente tiver o Azure AD Premium, o administrador poderá atribuir um grupo de segurança a uma função e os membros do grupo herdarão a função do aplicativo. Essa é uma maneira conveniente de gerenciar funções porque o proprietário do grupo não precisa ser um administrador do AD.

As vantagens dessa abordagem:

-	Modelo de programação simples.
-	As funções são específicas do aplicativo. As declarações de função para um aplicativo não são enviadas para outro aplicativo.
-	Se o cliente remover o aplicativo do seu locatário do AD, as funções desaparecerão.
-	O aplicativo não precisa de permissões extras do Active Directory além da leitura do perfil do usuário.

As desvantagens:

- Os clientes que não têm o Azure AD Premium não podem atribuir grupos de segurança a funções. Para esses clientes, todas as atribuições de usuário devem ser feitas por um administrador do AD.
- Se você tiver uma API Web de back-end, que é separada do aplicativo Web, as atribuições de função para o aplicativo Web não se aplicarão à API Web. Para obter mais informações sobre este ponto, confira [Protegendo uma API Web de back-end].

### Implementação

**Defina as funções.** O provedor de SaaS declara as funções de aplicativo no manifesto do aplicativo. Por exemplo, esta é a entrada do manifesto do aplicativo Surveys:

```
"appRoles": [
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Creators can create Surveys",
    "displayName": "SurveyCreator",
    "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
    "isEnabled": true,
    "value": "SurveyCreator"
  },
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Administrators can manage the Surveys in their tenant",
    "displayName": "SurveyAdmin",
    "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
    "isEnabled": true,
    "value": "SurveyAdmin"
  }
],
```

A propriedade `value` aparece na declaração de função. A propriedade `id` é o identificador exclusivo para a função definida. Gere sempre um novo valor de GUID para `id`.

**Atribua usuários**. Quando um novo cliente se inscrever, o aplicativo será registrado no locatário do AD do cliente. Neste ponto, um administrador do AD para o locatário poderá atribuir usuários a funções.

> [AZURE.NOTE] Como observado anteriormente, os clientes com o Azure AD Premium também podem atribuir grupos de segurança a funções.

A seguinte captura de tela do portal do Azure mostra três usuários. Alice foi diretamente atribuída a uma função. Pedro herdou uma função como membro de um grupo de segurança chamado “Administrador do Surveys” que é atribuído a uma função. Vinícius não está atribuído a qualquer função.

![Usuários atribuídos](media/guidance-multitenant-identity/role-assignments.png)

> [AZURE.NOTE] Como alternativa, o aplicativo pode atribuir funções programaticamente usando a API do Azure AD Graph. No entanto, isso requer que o aplicativo obtenha permissões de gravação para o diretório do AD do cliente. Um aplicativo com essas permissões poderia causar muitos danos &mdash; o cliente confia que o aplicativo não desorganizará seu diretório. É provável que muitos clientes não queiram conceder esse nível de acesso.

**Obtenha declarações de função**. Quando um usuário entra, o aplicativo recebe as funções atribuídas ao usuário em uma declaração com o tipo `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`.

Um usuário pode ter várias funções ou nenhuma função. Em seu código de autorização, não presuma que o usuário tenha exatamente uma função de declaração. Em vez disso, grave um código que verifique se há um valor de declaração específico presente:

```csharp
if (context.User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
```

## As funções que usam grupos de segurança do Azure AD

Nessa abordagem, as funções são representadas como grupos de segurança do AD. O aplicativo atribui permissões a usuários com base em suas associações a grupos de segurança.

Vantagens:

-	Para os clientes que não têm o Azure AD Premium, essa abordagem permite que o cliente use grupos de segurança para gerenciar as atribuições de função.

As desvantagens:

- Complexidade. Como todos os locatários enviam diferentes declarações de grupo, o aplicativo deverá manter o controle de quais grupos de segurança correspondem a quais funções de aplicativo para cada locatário.
- Se o cliente remover o aplicativo do seu locatário do AD, os grupos de segurança serão deixados no diretório do AD dele.

### Implementação

No manifesto do aplicativo, defina a propriedade `groupMembershipClaims` como "SecurityGroup". Isso é necessário para obter as declarações de associação de grupo do AAD.

```
{
   // ...
   "groupMembershipClaims": "SecurityGroup",
}
```

Quando um novo cliente se inscrever, o aplicativo instruirá o cliente a criar grupos de segurança para as funções exigidas pelo aplicativo. Em seguida, o cliente precisará inserir as IDs de objeto de grupo no aplicativo. O aplicativo as armazena em uma tabela que mapeia as IDs de grupo para as funções de aplicativo por locatário.

> [AZURE.NOTE] Como alternativa, o aplicativo poderia criar os grupos programaticamente usando a API do Azure AD Graph. Isso seria menos propenso a erros. No entanto, isso requer que o aplicativo obtenha permissões de “leitura e de gravação em todos os grupos” para o diretório do AD do cliente. É provável que muitos clientes não queiram conceder esse nível de acesso.

Quando um usuário entra:

1.	O aplicativo recebe os grupos do usuário como declarações. O valor de cada declaração é a ID do objeto de um grupo.
2.	O Azure AD limita o número de grupos enviados no token. Se o número de grupos exceder esse limite, o Azure AD enviará uma declaração "excedente" especial. Se essa declaração estiver presente, o aplicativo deverá consultar a API do Azure AD Graph para obter todos os grupos aos quais o usuário pertence. Para obter detalhes, confira [Autorização em Aplicativos de Nuvem usando grupos do AD], na seção intitulada "Excedente de grupos de declarações".
3.	O aplicativo procura as IDs do objeto em seu próprio banco de dados para localizar as funções de aplicativo correspondentes a serem atribuídas ao usuário.
4.	O aplicativo adiciona um valor de declaração personalizada à entidade de usuário que expressa a função de aplicativo. Por exemplo: `survey_role` = "SurveyAdmin".

As políticas de autorização devem usar a declaração de função personalizada e não a declaração de grupo.

## Funções que usam um aplicativo do gerenciador de funções

Com essa abordagem, as funções de aplicativo não são armazenadas no Azure AD. Em vez disso, o aplicativo armazena as atribuições de função para cada usuário em seu próprio banco de dados &mdash; por exemplo, usando a classe **RoleManager** no ASP.NET Identity.

Vantagens:

-	O aplicativo tem controle total sobre as funções e as atribuições de usuário.

As desvantagens:

- Mais complexo e mais difícil de manter.
- Não é possível usar grupos de segurança do AD para gerenciar atribuições de função.
- Armazena informações de usuário no banco de dados do aplicativo, onde podem ficar fora de sincronia com o diretório do AD do locatário à medida que usuários são adicionados ou removidos.   

Há muitos exemplos existentes para essa abordagem. Por exemplo, confira [Criar um aplicativo ASP.NET MVC com autenticação e o banco de dados SQL e implantar no Serviço de Aplicativo do Azure].


## Recursos adicionais

-	[Autorização em um aplicativo Web usando funções de aplicativo e declarações de função do Azure AD]
-	[Autorização em Aplicativos de Nuvem usando Grupos do AD]
-	[Controle de acesso baseado em funções em aplicativos de nuvem usando o Azure AD]
-	[Tipos de declaração e de token com suporte]. Descreve a função e as declarações de grupo no Azure AD.
-	[Noções básicas sobre o manifesto de aplicativo do Active Directory do Azure]

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte de uma série]: guidance-multitenant-identity.md
[autorização]: guidance-multitenant-identity-authorize.md
[Protegendo uma API Web de back-end]: guidance-multitenant-identity-web-api.md
[Autorização em Aplicativos de Nuvem usando grupos do AD]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[Criar um aplicativo ASP.NET MVC com autenticação e o banco de dados SQL e implantar no Serviço de Aplicativo do Azure]: ../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[Noções básicas sobre o manifesto de aplicativo do Active Directory do Azure]: ../active-directory/active-directory-application-manifest.md
[Tipos de declaração e de token com suporte]: ../active-directory/active-directory-token-and-claims.md
[Controle de acesso baseado em funções em aplicativos de nuvem usando o Azure AD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[Autorização em um aplicativo Web usando funções de aplicativo e declarações de função do Azure AD]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->