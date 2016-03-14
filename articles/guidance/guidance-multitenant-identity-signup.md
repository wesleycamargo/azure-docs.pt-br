<properties
   pageTitle="Inscrição e integração de locatário em aplicativos de multilocatários | Microsoft Azure"
   description="Como integrar locatários em um aplicativo multilocatário"
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

# Inscrição e integração de locatário em um aplicativo multilocatário

Este artigo faz [parte de uma série]. Esta série também vem com um [aplicativo de exemplo] completo.

Este artigo descreve como implementar um processo de _inscrição_ em um aplicativo multilocatário que permite que um cliente inscreva a organização dele em seu aplicativo. Há várias razões para implementar um processo de inscrição:

-	Permitir que um administrador do AD autorize o uso do aplicativo por toda a organização do cliente.
-	Cobrar o pagamento por cartão de crédito ou coletar outras informações do cliente.
-	Executar a instalação avulsa por locatário que venha a ser necessária para seu aplicativo.

## Consentimento do administrador e permissões do Azure AD

Para autenticar com o Azure AD, o aplicativo precisa acessar o diretório do usuário. O aplicativo precisa, no mínimo, de permissão para ler o perfil do usuário. Na primeira vez que um usuário fizer logon, o Azure AD mostrará uma página de consentimento que lista as permissões que estão sendo solicitadas. Ao clicar em **Aceitar**, o usuário concederá permissão para o aplicativo.

Por padrão, o consentimento é concedido baseado em cada usuário. Todos os usuários que entrarem verão a página de consentimento. No entanto, o Azure AD também oferece suporte ao _consentimento do administrador_ que permite que um administrador do AD dê consentimentos em nome de toda a organização.

Quando o fluxo de consentimento do administrador é usado, a página de autorização informa que o administrador do AD está concedendo a permissão em nome do locatário inteiro:

![Solicitação de consentimento do administrador](media/guidance-multitenant-identity/admin-consent.png)

Depois que o administrador clica em **Aceitar**, outros usuários no mesmo locatário podem entrar e o Azure AD ignorará a tela de consentimento.

Somente um administrador do AD pode dar consentimento de administrador, já que ele concede permissão em nome de toda a organização. Se alguém que não for um administrador tentar autenticar com o fluxo de consentimento do administrador, o Azure AD exibirá um erro:

![Erro de consentimento](media/guidance-multitenant-identity/consent-error.png)

Se o aplicativo exigir permissões adicionais em um momento posterior, o cliente precisará se inscrever novamente e concordar com as permissões atualizadas.

## Implementando a inscrição de locatário

No caso do aplicativo [Tailspin Surveys][Tailspin], definimos vários requisitos para o processo de inscrição:

-	Um locatário deve se inscrever antes dos usuários poderem entrar.
-	A inscrição usa o fluxo de consentimento do administrador.
-	A inscrição adiciona o locatário do usuário ao banco de dados do aplicativo.
-	Depois que um locatário se inscreve, o aplicativo mostra uma página de integração.

Nesta seção, vamos examinar nossa implementação do processo de inscrição. É importante entender que "inscrição" versus "logon" é um conceito de aplicativo. Durante o fluxo de autenticação, o Azure AD não tem como saber se o usuário está no processo de inscrição. Cabe ao aplicativo controlar o contexto.

Quando um usuário anônimo visita o aplicativo Surveys, o usuário vê dois botões, um para entrar e um para "registrar sua empresa" (inscrever-se).

![Página de inscrição do aplicativo](media/guidance-multitenant-identity/sign-up-page.png)

Esses botões chamam ações na classe [AccountController].

A ação `SignIn` retorna um **ChallegeResult**, que faz com que o middleware OpenID Connect redirecione para o ponto de extremidade de autenticação. Essa é a maneira padrão de disparar a autenticação no ASP.NET Core 1.0.

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

Agora compare a ação `SignUp`:

```csharp
[AllowAnonymous]
public IActionResult SignUp()
{
    // Workaround for https://github.com/aspnet/Security/issues/546
    HttpContext.Items.Add("signup", "true");

    var state = new Dictionary<string, string> { { "signup", "true" }};
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties(state)
        {
            RedirectUri = Url.Action(nameof(SignUpCallback), "Account")
        });
}
```

Assim como a `SignIn`, a ação `SignUp` também retorna um `ChallengeResult`. Mas, dessa vez, vamos adicionar uma parte das informações de estado às `AuthenticationProperties` no `ChallengeResult`:

-	inscrição: um sinalizador booliano indicando que o usuário iniciou o processo de inscrição.

As informações de estado em `AuthenticationProperties` são adicionadas ao parâmetro [state] do OpenID Connect, que vai e volta durante o fluxo de autenticação.

![Parâmetro de estado](media/guidance-multitenant-identity/state-parameter.png)

Depois que o usuário é autenticado no Azure AD e é redirecionado ao aplicativo, o tíquete de autenticação contém o estado. Estamos usando isso para fazer com que o valor "inscrição" persista durante todo o fluxo de autenticação.

## Adicionando a solicitação de consentimento do administrador

No Azure AD, o fluxo de consentimento do administrador é disparado com a adição de um parâmetro "prompt" à cadeia de caracteres de consulta na solicitação de autenticação:

```
/authorize?prompt=admin_consent&...
```

O aplicativo Surveys adiciona o prompt durante o evento `RedirectToAuthenticationEndpoint`. Esse evento é chamado momentos antes do middleware redirecionar para o ponto de extremidade de autenticação.

```csharp
public override Task RedirectToAuthenticationEndpoint(RedirectContext context)
{
    if (context.IsSigningUp())
    {
        context.ProtocolMessage.Prompt = "admin_consent";
    }

    _logger.RedirectToIdentityProvider();
    return Task.FromResult(0);
}
```

> [AZURE.NOTE] Confira [SurveyAuthenticationEvents.cs].

A definição de ` ProtocolMessage.Prompt` ordena ao middleware que adicione o parâmetro "prompt" à solicitação de autenticação.

Observe que o prompt é necessário somente durante a inscrição. O logon normal não deve incluí-lo. Para distinguir entre eles, verificamos o valor `signup` no estado de autenticação. O seguinte método de extensão verifica essa condição:

```csharp
internal static bool IsSigningUp(this BaseControlContext context)
{
    Guard.ArgumentNotNull(context, nameof(context));

    string signupValue;
    object obj;
    // Check the HTTP context and convert to string
    if (context.HttpContext.Items.TryGetValue("signup", out obj))
    {
        signupValue = (string)obj;
    }
    else
    {
        // It's not in the HTTP context, so check the authentication ticket.  If it's not there, we aren't signing up.
        if ((context.AuthenticationTicket == null) ||
            (!context.AuthenticationTicket.Properties.Items.TryGetValue("signup", out signupValue)))
        {
            return false;
        }
    }

    // We have found the value, so see if it's valid
    bool isSigningUp;
    if (!bool.TryParse(signupValue, out isSigningUp))
    {
        // The value for signup is not a valid boolean, throw                
        throw new InvalidOperationException($"'{signupValue}' is an invalid boolean value");
    }

    return isSigningUp;
}
```

> [AZURE.NOTE] Confira [BaseControlContextExtensions.cs].

> [AZURE.NOTE] Observação: esse código inclui uma solução alternativa para um bug conhecido no ASP.NET Core 1.0 RC1. No evento `RedirectToAuthenticationEndpoint`, não é possível obter as propriedades de autenticação que contêm o estado "inscrição". Como alternativa, o método `AccountController.SignUp` também coloca o estado "inscrição" no `HttpContext`. Isso funciona porque `RedirectToAuthenticationEndpoint` ocorre antes do redirecionamento e, portanto, ainda temos o mesmo `HttpContext`.

## Registrando um locatário

O aplicativo Surveys armazena algumas informações sobre cada locatário e usuário no banco de dados do aplicativo.

![Tabela de locatário](media/guidance-multitenant-identity/tenant-table.png)

Na tabela Locatário, IssuerValue é o valor da declaração do emissor do locatário. Para o Azure AD, ele é o `https://sts.windows.net/<tentantID>` e fornece um valor exclusivo por locatário.

Quando um novo locatário se inscreve, o aplicativo Surveys grava um registro de locatário no banco de dados. Isso acontece dentro do evento `AuthenticationValidated`. (Não faça isso antes desse evento, pois o token de ID ainda não foi validado e os valores de declaração ainda não são confiáveis.) Confira [Autenticação].

Veja o código relevante do aplicativo Surveys:

```csharp
public override async Task AuthenticationValidated(AuthenticationValidatedContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var userId = principal.GetObjectIdentifierValue();
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    _logger.AuthenticationValidated(userId, issuerValue);

    // Normalize the claims first.
    NormalizeClaims(principal);
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue)
        .ConfigureAwait(false);

    if (context.IsSigningUp())
    {
        // Originally, we were checking to see if the tenant was non-null, however, this would not allow
        // permission changes to the application in AAD since a re-consent may be required.  Now we just don't
        // try to recreate the tenant.
        if (tenant == null)
        {
            tenant = await SignUpTenantAsync(context, tenantManager)
                .ConfigureAwait(false);
        }

        // In this case, we need to go ahead and set up the user signing us up.
        await CreateOrUpdateUserAsync(context.AuthenticationTicket, userManager, tenant)
            .ConfigureAwait(false);
    }
    else
    {
        if (tenant == null)
        {
            _logger.UnregisteredUserSignInAttempted(userId, issuerValue);
            throw new SecurityTokenValidationException($"Tenant {issuerValue} is not registered");
        }

        await CreateOrUpdateUserAsync(context.AuthenticationTicket, userManager, tenant)
            .ConfigureAwait(false);
    }
}
```

> [AZURE.NOTE] Confira [SurveyAuthenticationEvents.cs].

O código faz o seguinte:

1.	Verifique se o valor do emissor do locatário já está no banco de dados. Se o locatário não se inscreveu, `FindByIssuerValueAsync` retorna null.
2.	Se o usuário estiver se inscrevendo:
  1.	Adicione o locatário ao banco de dados (`SignUpTenantAsync`).
  2.	Adicione o usuário autenticado ao banco de dados (`CreateOrUpdateUserAsync`).
3.	Caso contrário, siga o fluxo de entrada normal:
  1.	Se o emissor do locatário não foi encontrado no banco de dados, isso significa que o locatário não está registrado e o cliente precisa se inscrever. Nesse caso, lance uma exceção para causar uma falha na autenticação.
  2.	Caso contrário, crie um registro de banco de dados para o usuário, se já não houver um (`CreateOrUpdateUserAsync`).

Veja o método [SignUpTenantAsync] que adiciona o locatário ao banco de dados.

```csharp
private async Task<Tenant> SignUpTenantAsync(BaseControlContext context, TenantManager tenantManager)
{
    Guard.ArgumentNotNull(context, nameof(context));
    Guard.ArgumentNotNull(tenantManager, nameof(tenantManager));

    var principal = context.AuthenticationTicket.Principal;
    var issuerValue = principal.GetIssuerValue();
    var tenant = new Tenant
    {
        IssuerValue = issuerValue,
        Created = DateTimeOffset.UtcNow
    };

    try
    {
        await tenantManager.CreateAsync(tenant)
            .ConfigureAwait(false);
    }
    catch(Exception ex)
    {
        _logger.SignUpTenantFailed(principal.GetObjectIdentifierValue(), issuerValue, ex);
        throw;
    }

    return tenant;
}
```

Veja um resumo de todo o fluxo de inscrição no aplicativo Surveys:

1.	O usuário clica no botão **Inscrever-se**.
2.	A ação `AccountController.SignUp` retorna um resultado de desafio. O estado da autenticação inclui o valor "inscrição".
3.	No evento `RedirectToAuthenticationEndpoint`, adicione o prompt `admin_consent`.
4.	O middleware OpenID Connect redireciona para o Azure AD e o usuário é autenticado.
5.	No evento `AuthenticationValidated`, procure o estado "inscrição".
6.	Adicione o locatário ao banco de dados.


<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte de uma série]: guidance-multitenant-identity.md
[AccountController]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Controllers/AccountController.cs
[state]: http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest
[SurveyAuthenticationEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs
[BaseControlContextExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/BaseControlContextExtensions.cs
[Autenticação]: guidance-multitenant-identity-authenticate.md
[SignUpTenantAsync]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->