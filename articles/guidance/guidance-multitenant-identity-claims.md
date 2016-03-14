<properties
   pageTitle="Trabalhando com identidades baseadas em declaração em aplicativos com multilocatários | Microsoft Azure"
   description="Como usar declarações para autorização e validação de emissor"
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

# Trabalhando com identidades baseadas em declarações em aplicativos com multilocatários

Este artigo faz [parte de uma série]. Esta série também vem com um [aplicativo de exemplo] completo.

## Declarações no Azure AD

Quando um usuário entra, o Azure AD envia um token de ID que contém um conjunto de declarações sobre o usuário. Uma declaração é simplesmente uma informação expressada como um par chave/valor. Por exemplo, `email`=`bob@contoso.com` As declarações têm um emissor, nesse caso, o Azure AD, que é a entidade que autentica o usuário e cria as declarações. Você confia nas declarações porque confia no emissor. (Por outro lado, se você não confiar no emissor, não confiará nas declarações!)

Em um alto nível:

1.	O usuário é autenticado.
2.	O IDP envia um conjunto de declarações.
3.	O aplicativo normaliza ou amplia as declarações (opcional).
4.	O aplicativo usa as declarações para tomar decisões sobre a autorização.

No OpenID Connect, o conjunto de declarações que você obtém é controlado pelo [parâmetro de escopo] da solicitação de autenticação. No entanto, o Azure AD emite um conjunto limitado de declarações usando o OpenID Connect. Confira [Tipos de Declaração e Token com Suporte]. Se você quiser saber mais sobre o usuário, precisará usar a API do Azure AD Graph.

Eis algumas declarações do AAD que normalmente são relevantes para um aplicativo:

Tipo de declaração no token de ID |	Descrição
-----------------------|--------------
aud | A pessoa para quem o token foi emitido. Essa será a ID de cliente do aplicativo. Em geral, você não deve se preocupar com essa declaração, pois o middleware a valida automaticamente. Exemplo: `"91464657-d17a-4327-91f3-2ed99386406f"`
groups | Uma lista de grupos do AAD dos quais o usuário é membro. Exemplo: `["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
iss | O [emissor] do token OIDC. Exemplo: `https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
name | O nome de exibição do usuário. Exemplo: `"Alice A."`
oid | O identificador de objeto para o usuário no AAD. Esse valor é o identificador do usuário e é imutável e não reutilizável. Use esse valor, não o email, como um identificador exclusivo para os usuários. Os endereços de email podem ser alterados. Se você usar a API do Azure AD Graph em seu aplicativo, a ID de objeto será o valor usado para consultar informações de perfil. Exemplo: `"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
roles | Uma lista de funções de aplicativo para o usuário. Exemplo: `["SurveyCreator"]`
tid | ID do locatário. Esse valor é um identificador exclusivo do locatário no Azure AD. Exemplo: `"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique\_name | Um nome de exibição do usuário que pode ser lido por humanos. Exemplo: `"alice@contoso.com"`
upn | Nome do usuário principal. Exemplo: `"alice@contoso.com"`

Essa tabela lista os tipos de declaração que aparecem no token de ID. No ASP.NET Core 1.0, o middleware OpenID Connect converte alguns dos tipos de declaração quando preenche a coleção Declarações do usuário principal:

-	oid > `http://schemas.microsoft.com/identity/claims/objectidentifier`
-	tid > `http://schemas.microsoft.com/identity/claims/tenantid`
-	unique\_name > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-	upn > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## Transformações de declarações

Durante o fluxo de autenticação, talvez você queira modificar as declarações obtidas do IDP. No ASP.NET Core 1.0, você pode executar a transformação de declarações dentro do evento **AuthenticationValidated** no middleware OpenID Connect. (Confira [Eventos de autenticação].)

As declarações que você adicionar durante **AuthenticationValidated** serão armazenadas no cookie de autenticação de sessão. Elas não serão enviadas por push de volta ao Azure AD.

Veja alguns exemplos de transformação de declarações:

-	**Normalização de declarações normalização** ou uniformizar as declarações entre os usuários. Isso é especialmente relevante se você estiver obtendo declarações de vários IDPs que podem usar tipos de declaração diferentes para informações semelhantes. Por exemplo, o Azure AD envia uma declaração "upn" que contém o email do usuário. Outros IDPs podem enviar uma declaração "email". O código abaixo converte a declaração "upn" em uma declaração "email":

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- Adição de **valores de declaração padrão** para declarações que não estão presentes, por exemplo, atribuindo um usuário a uma função padrão. Em alguns casos, isso pode simplificar a lógica da autorização.
- Adição de **tipos de declaração personalizada** com informações sobre o usuário específicas do aplicativo. Por exemplo, você pode armazenar algumas informações sobre o usuário em um banco de dados. Você pode adicionar uma declaração personalizada com essas informações ao tíquete de autenticação. A declaração fica armazenada em um cookie e, portanto, basta obtê-lo do banco de dados uma vez a cada sessão de logon. Por outro lado, você também deve evitar a criação de cookies excessivamente grandes. Sendo assim, leve em conta a compensação entre o tamanho do cookie e as pesquisas de banco de dados.   

Depois que o fluxo de autenticação for concluído, as declarações estarão disponíveis em `HttpContext.User`. Nesse momento, você deve tratá-las como uma coleção somente leitura, por exemplo, usá-las para tomar decisões de autorização.

## Validação do emissor
No OpenID Connect, a declaração do emissor ("iss") identifica o IDP que emitiu o token de ID. Parte do fluxo de autenticação OIDC serve para verificar se a declaração do emissor corresponde ao emissor verdadeiro. O middleware OIDC lida com isso para você.

No Azure AD, o valor de emissor é exclusivo por locatário do AD (`https://sts.windows.net/<tenantID>`). Portanto, o aplicativo deve fazer uma verificação adicional para ter certeza de que o emissor representa um locatário que tem permissão para entrar no aplicativo.

Para um aplicativo de locatário único, você pode apenas verificar se o emissor é seu próprio locatário. Na verdade, o middleware OIDC faz isso automaticamente por padrão. Em um aplicativo multilocatário, você precisa permitir vários emissores que correspondem a locatários diferentes. Veja uma abordagem geral que pode ser usada:

-	Nas opções de middleware OIDC, defina **ValidateIssuer** como false. Isso desliga a verificação automática.
-	Quando um locatário se inscrever, armazene o locatário e o emissor em seu banco de dados do usuário.
-	Sempre que um usuário fizer logon, pesquise o emissor no banco de dados. Se o emissor não for encontrado, significa que o locatário ainda não se inscreveu. Você pode redirecioná-lo para uma página de inscrição.
-  Você também pode colocar determinados locatários na lista negra, por exemplo, clientes que não pagaram suas assinaturas.

Para ver uma discussão mais detalhada, confira [Inscrição e integração de locatário].

## Usando declarações para autorização

Com as declarações, a identidade do usuário não é mais uma entidade monolítica. Por exemplo, um usuário pode ter um endereço de email, número de telefone, aniversário, sexo, etc. Talvez o IDP do usuário armazene todas essas informações. Mas, ao autenticar o usuário, você geralmente recebe um subconjunto dessas informações como declarações. Nesse modelo, a identidade do usuário é simplesmente um conjunto de declarações. Ao tomar decisões de autorização sobre um usuário, você buscará determinados conjuntos de declarações. Em outras palavras, a pergunta "O usuário X pode executar a ação Y?" se torna "O usuário X tem a declaração Z?".

Veja alguns padrões básicos para verificar declarações.

-  Para verificar se o usuário tem uma declaração específica com um valor específico:

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    Esse código verifica se o usuário tem uma declaração Função com o valor "Admin". Ele aborda corretamente o caso de usuários que não tenham a declaração Função ou tenham várias declarações Função.

    A classe **ClaimTypes** define constantes para tipos de declaração comuns. No entanto, você pode usar qualquer valor da cadeia de caracteres para o tipo de declaração.

-	Para obter um único valor de um tipo de declaração, quando você espera que haja no máximo um valor:
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-	Para obter todos os valores de um tipo de declaração:

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

Para obter mais detalhes sobre o uso de declarações de autorização, confira [Autorização].

## Recursos adicionais

- [Autorização baseada em declarações]


<!-- Links -->
[parte de uma série]: guidance-multitenant-identity.md
[parâmetro de escopo]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Tipos de Declaração e Token com Suporte]: ../active-directory/active-directory-token-and-claims.md
[emissor]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Eventos de autenticação]: guidance-multitenant-identity-authenticate.md#authentication-events
[Inscrição e integração de locatário]: guidance-multitenant-identity-signup.md
[Autorização]: guidance-multitenant-identity-authorize.md
[Autorização baseada em declarações]: https://docs.asp.net/en/latest/security/authorization/claims.html
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->