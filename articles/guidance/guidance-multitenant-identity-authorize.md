<properties
   pageTitle="Autorização em aplicativos multilocatário | Microsoft Azure"
   description="Como executar a autorização em um aplicativo multilocatário"
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

# Autorização em aplicativos multilocatário

Este artigo faz [parte de uma série]. Esta série também vem com um [aplicativo de exemplo] completo.

Neste artigo, examinaremos duas abordagens gerais à autorização.

-	**Autorização baseada em função**. Autorização de uma ação baseada nas funções atribuídas a um usuário. Por exemplo, algumas ações requerem uma função de administrador.
-	**Autorização baseada em recursos**. Autorização de uma ação baseada em determinado recurso. Por exemplo, cada recurso tem um proprietário. O proprietário pode excluir o recurso, mas os outros usuários não.

Um aplicativo típico utiliza uma mistura das duas abordagens. Por exemplo, para excluir um recurso, o usuário deve ser o proprietário do recurso _ou_ um administrador.

## Autorização baseada em função

O aplicativo [Tailspin Surveys][Tailspin] define as seguintes funções:

- Administrador. Pode executar todas as operações CRUD em qualquer pesquisa que pertença ao locatário.
- Criador. Pode criar novas pesquisas
- Leitor. Pode ler todas as pesquisas que pertençam ao locatário

As funções se aplicam a _usuários_ do aplicativo. No aplicativo Surveys, o usuário pode ser um administrador, um criador ou um leitor.

Para uma discussão sobre como definir e gerenciar funções, confira [Funções de aplicativo].

Independentemente de como você gerenciar as funções, seu código de autorização terá uma aparência semelhante. O ASP.NET Core 1.0 introduz uma abstração chamada _políticas de autorização_. Com esse recurso, você define políticas de autorização no código e depois aplica-as às ações do controlador. A política é dissociada do controlador.

### Criar políticas

Para definir uma política, primeiro crie uma classe que implemente `IAuthorizationRequirement`. É mais fácil derivar de `AuthorizationHandler`. No método `Handle`, examine as solicitações relevantes.

Veja um exemplo do aplicativo Tailspin Surveys:

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Confira [SurveyCreatorRequirement.cs]

Essa classe define o requisito para um usuário criar uma nova pesquisa. O usuário deve ter a função SurveyAdmin ou SurveyCreator.

Em sua classe de inicialização, defina uma política nomeada que inclua um ou mais requisitos. Caso existam vários requisitos, o usuário deve atender a _todos_ os requisitos para ser autorizado. O código abaixo define duas políticas:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Confira [Startup.cs]

Esse código também define o esquema de autenticação, que informa ao ASP.NET que o middleware de autenticação deverá ser executado se a autorização falhar. Nesse caso, especificamos o middleware de autenticação por cookie, pois o middleware de autenticação por cookie pode redirecionar o usuário para uma página "Proibido". O local da página Proibido é definido na opção AccessDeniedPath para o middleware de autenticação por cookie. Confira [Configurar o middleware de autenticação].

### Autorizar ações do controlador

Por fim, para autorizar uma ação em um controlador MVC, defina a política no atributo `Authorize`:

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

Em versões anteriores do ASP.NET, você definiria a propriedade **Funções** do atributo:

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Ainda existe suporte para essa opção no ASP.NET Core 1.0, mas ela tem algumas desvantagens se comparada às políticas de autorização:

-	Ela pressupõe um tipo de declaração específico. As políticas podem verificar qualquer tipo de declaração. As funções são apenas um tipo de declaração.
-	O nome da função é embutido em código no atributo. Com as políticas, a lógica da autorização está em um só lugar, tornando mais fácil atualizar ou, até mesmo, carregar as definições de configuração.
-	As políticas permitem decisões de autorização mais complexas (por exemplo, idade maior ou igual a 21), que não podem ser expressas pela associação de função simples.

## Autorização baseada em recursos

A _autorização baseada em recursos_ ocorre sempre que a autorização depender de um recurso específico que será afetado por uma operação. No aplicativo Tailspin Surveys, cada pesquisa tem um proprietário e de zero a muitos colaboradores.

-	O proprietário pode ler, atualizar, excluir, publicar e cancelar a publicação da pesquisa.
-	O proprietário pode atribuir colaboradores à pesquisa.
-	Os colaboradores podem ler e atualizar a pesquisa.

Observe que "proprietário" e "colaborador" não são funções de aplicativo. Elas são armazenadas por pesquisa no banco de dados do aplicativo. Para verificar se um usuário pode excluir uma pesquisa, por exemplo, o aplicativo verifica se o usuário é o proprietário da pesquisa.

No ASP.NET Core 1.0, implemente a autorização baseada em recursos derivando de **AuthorizationHandler** e substituindo o método **Handle**.

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Observe que essa classe é fortemente tipada para objetos do Survey. Registre a classe de injeção de dependência na inicialização:

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Para executar verificações de autorização, use a interface **IAuthorizationService**, que você pode injetar em seus controladores. O código a seguir verifica se um usuário pode ler uma pesquisa:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Como nós passamos um objeto `Survey`, essa chamada invocará o `SurveyAuthorizationHandler`.

No código de autorização, uma boa abordagem é agregar todas as permissões do usuário baseadas em função e baseadas em recursos e depois verificar a agregação definida em relação à operação desejada. Veja um exemplo do aplicativo Surveys. O aplicativo define vários tipos de permissão:

- Administrador
- Colaborador
- Criador
- Proprietário
- Leitor

O aplicativo também define um conjunto de possíveis operações nas pesquisas:

- Criar
- Ler
- Atualização
- Excluir
- Publicar
- Não publicar

O código a seguir cria uma lista de permissões para uma pesquisa e usuário específicos. Observe que esse código examina as funções de aplicativo do usuário e os campos de proprietário/colaborador da pesquisa.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Confira [SurveyAuthorizationHandler.cs].

Em um aplicativo multilocatário, você deve garantir que as permissões não "vazem" para os dados de outro locatário. No aplicativo Surveys, as permissões de Colaborador são permitidas entre locatários &mdash; você pode atribuir uma pessoa de outro locatário como um colaborador. Os outros tipos de permissão são restritos a recursos que pertencem ao locatário do usuário, portanto, o código verifica a ID do locatário antes de conceder esses tipos de permissão. (O campo `TenantId` é atribuído quando a pesquisa é criada.)

A próxima etapa é verificar a operação (leitura, atualização, exclusão, etc.) em relação às permissões. O aplicativo Surveys implementa esta etapa usando uma tabela de pesquisa de funções:

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```

## Recursos adicionais

- [Autorização Baseada em Recursos](https://docs.asp.net/en/latest/security/authorization/resourcebased.html)
- [Autorização Personalizada Baseada em Políticas](https://docs.asp.net/en/latest/security/authorization/policies.html)

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte de uma série]: guidance-multitenant-identity.md
[Funções de aplicativo]: guidance-multitenant-identity-app-roles.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Configurar o middleware de autenticação]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->