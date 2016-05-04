<properties 
   pageTitle="Autorização com a API do Resource Manager | Microsoft Azure"
   description="Guia do desenvolvedor para autorização com a API do Azure Resource Manager e o Active Directory para integrar um aplicativo ao Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/18/2016"
   ms.author="dugill;tomfitz" />


# Guia do desenvolvedor para autorização com a API do Azure Resource Manager

Se você é um desenvolvedor de software que deseja integrar um aplicativo ao Azure ou gerenciar recursos do Azure do cliente, este tópico mostra como autenticar com as APIs do Azure Resource Manager.

Seu aplicativo pode acessar as APIs do Gerenciador de Recursos de duas maneiras:

1. **Acesso de usuário e aplicativo**: use esse método para aplicativos que acessam os recursos em nome do usuário conectado. Ele funciona para aplicativos, como aplicativos Web e ferramentas de linha de comando, que lidam somente com o "gerenciamento interativo" de recursos do Azure.
1. **Acesso somente de aplicativo**: use esse método quando a identidade do aplicativo tiver que ter acesso direto aos recursos. Ele funciona para serviços de daemon e trabalhos agendados que precisam de "acesso offline" ao Azure em longo prazo.

Este tópico fornece uma descrição passo a passo de como criar um aplicativo que empregue esses dois métodos de autorização.

Você criará um aplicativo Web que:

1. Conecta um usuário do Azure
2. Consulta o Resource Manager em nome do usuário (acesso de usuário e aplicativo) para obter uma lista das assinaturas do Azure que o usuário possui
3. Permite que o usuário "conecte" assinaturas ao aplicativo, concedendo, dessa forma, acesso direto ao aplicativo na assinatura
4. Acessa o Gerenciador de Recursos do aplicativo para executar operações offline (acesso somente de aplicativo)

Aqui está o fluxo de ponta a ponta do aplicativo Web que você irá escrever.

![Autorização do ARM - registro do aplicativo 1](./media/resource-manager-api-authentication/ARM-Auth-Swim-Lane.png)

Todo o código para este tópico está sendo executado como um aplicativo Web que você pode experimentar em [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense).

Como usuário, você deve selecionar o tipo de conta a ser usada para fazer logon:

![selecionar entrada](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-1.png)

Forneça suas credenciais.

![fornecer credenciais](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-2.png)

Conceda ao aplicativo acesso às assinaturas do Azure:
 
 ![Conceder acesso](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-3.png)
 
Conecte-se suas assinaturas ao aplicativo de monitoramento:

![Conectar assinatura](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4.png)

Desconecte ou repare a conexão com o aplicativo:

![Desconectar assinatura](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-5.png)

## Registrar seu aplicativo com o Active Directory do Azure

Comece registrando seu aplicativo Web no Active Directory (AD). O registro do aplicativo cria uma identidade central para seu aplicativo no Azure AD. Ele contém informações básicas sobre seu aplicativo, como ID do cliente OAuth, URLs de resposta e credenciais que o aplicativo usará para autenticar e acessar as APIs do Azure Resource Manager. O registro do aplicativo também registra as várias permissões delegadas de que seu aplicativo precisa para acessar APIs da Microsoft em nome do usuário.

O tópico [Criar aplicativo do Active Directory e entidade de serviço usando o portal](resource-group-create-service-principal-portal.md) mostra todas as etapas necessárias para configurar o seu aplicativo. Consulte o tópico enquanto cria um aplicativo com as seguintes propriedades:

- Aplicativo Web chamado **CloudSense**
- URL de entrada e URI de ID do aplicativo no formato **http://{domain_name_of_your_directory}/{name_of_the_app}**.
- Chave de autenticação para entrar no aplicativo
- Permissão delegada **Acessar o Gerenciamento de Serviços do Azure** para a **API de Gerenciamento de Serviços do Azure**. Deixe o padrão **Habilitar logon único e ler o perfil do usuário** como **Azure Active Directory**.
- Aplicativos multilocatários

### Configuração opcional - credenciais de certificado

O Azure AD também dá suporte a credenciais de certificado para aplicativos: crie um certificado autoassinado, mantenha a chave privada e adicione a chave pública a seu registro de aplicativo do Azure AD. Para autenticação, seu aplicativo envia uma pequena carga ao Azure AD assinada usando sua chave privada e o Azure AD valida a assinatura usando a chave pública que você registrou.

Para obter informações sobre como configurar o certificado, confira [Criar aplicativos de serviço e daemon no Office 365](https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365). A seção "Configurando um certificado público x. 509 para o seu aplicativo" tem instruções passo a passo para configurar o certificado. Ou consulte [Autenticando uma entidade de serviço com o Azure Resource Manager](resource-group-authenticate-service-principal.md) para obter exemplos de como configurar um certificado por meio do Azure PowerShell ou da CLI do Azure.

## Autenticar o usuário e obter token de acesso

Agora você tem tudo de que precisa para começar a codificação do aplicativo. Este tópico fornece exemplos de API REST para cada etapa do fluxo de ponta a ponta e links para o código C# relevante em cada etapa. O exemplo de aplicativo ASP.NET MVC completo está disponível em [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Você começa no momento em que o usuário decide conectar a assinatura do Azure dele ao seu aplicativo.

Você deve perguntar duas coisas ao usuário:

1. **Nome de domínio do diretório**: o nome de domínio do Azure Active Directory associado à assinatura do Azure do usuário. A solicitação de autorização do OAuth 2.0 deve ser enviada para esse Azure AD. O usuário pode encontrar o nome de domínio do seu Azure AD navegando até o Portal do Azure e selecionando a conta no canto superior direito. Você pode fornecer instruções visuais ao usuário, como: 

     ![](./media/resource-manager-api-authentication/show-directory.png)
   
1. **Conta da Microsoft versus Conta de trabalho**: determine se o usuário gerencia a sua assinatura do Azure com uma conta da Microsoft (também conhecida como Live Id) ou uma conta de trabalho (também conhecida como conta organizacional). Se for uma conta da Microsoft, o seu aplicativo redirecionará o usuário para a página de logon do Azure Active Directory com um parâmetro de cadeia de caracteres de consulta (&domain\_hint=live.com) que instruirá o Azure AD a levar o usuário diretamente para a página de entrada da Conta da Microsoft. O código de autorização e os tokens recebidos para qualquer tipo de conta serão processados da mesma maneira.

Em seguida, seu aplicativo redireciona o usuário para o Azure AD com uma solicitação de autorização OAuth 2.0 a fim de autenticar as credenciais do usuário e obter um código de autorização. Seu aplicativo usará o código de autorização a fim de obter um token de acesso para o Gerenciador de Recursos.

### Solicitação de autorização (OAuth 2.0)

Emita uma solicitação de autorização Open ID Connect/OAuth 2.0 para o ponto de extremidade de autorização do Azure AD:

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Authorize

Os parâmetros de cadeia de caracteres de consulta que estão disponíveis para essa solicitação estão descritos no tópico [Fluxo de concessão de código de autorização](https://msdn.microsoft.com/library/azure/dn645542.aspx).

O exemplo abaixo mostra como solicitar autorização OAuth 2.0:

    https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

O Azure AD autentica o usuário e, se necessário, pede a ele para conceder permissão ao aplicativo. Ele retorna o código de autorização para a URL de resposta do seu aplicativo. Dependendo do response\_mode solicitado, o Azure AD envia de volta os dados na cadeia de caracteres de consulta ou como dados de postagem.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### Solicitação de autorização (Open ID Connect)

Se você não deseja apenas acessar o Azure Resource Manager em nome do usuário, mas também permitir que o usuário entre em seu aplicativo usando a conta do Azure AD, emita uma solicitação de autorização Open ID Connect. Com o Open ID Connect, seu aplicativo também receberá um id\_token do Azure AD e poderá usá-lo para conectar o usuário.

Os parâmetros de cadeia de caracteres de consulta da solicitação de autorização OAuth 2.0 são:

| Parâmetro QS | Valor
|----|----
| client\_id | ID do cliente do seu aplicativo
| response\_mode | **form\_post** ou **query**
| response\_type | **code+id\_token**
| redirect\_uri | URL de resposta codificada de seu aplicativo Por exemplo: http://www.vipswapper.com/cloudsense/Account/SignIn |
| recurso | Identificador codificado na URL de APIs de Gerenciamento de Serviços do Azure: https://management.core.windows.net/ |
| scope | OpenID +profile
| nonce | Parte dos dados para ligar a solicitação de autorização ao id\_token retornado a fim de garantir que a resposta de autorização seja solicitada e não seja repetida.
| domain\_hint | live.com <br />**Observação**: somente use o parâmetro domain\_hint se o usuário gerencia suas assinaturas do Azure usando uma conta da Microsoft.
| state | Opcionalmente, especifique dados de estado que você deseja que o Azure AD retorne com a resposta.

Eis um exemplo de solicitação Open ID Connect:

     https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

O Azure AD autentica o usuário e, se necessário, pede a ele para conceder permissão ao aplicativo. Ele retorna o código de autorização para a URL de resposta do seu aplicativo. Dependendo do response\_mode solicitado, o Azure AD envia de volta os dados na cadeia de caracteres de consulta ou como dados de postagem.

Eis um exemplo de resposta Open ID Connect:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### Validar id\_token

Seu aplicativo precisará validar o id\_token antes de conectar o usuário. Validação de token é um tópico avançado e eu recomendo que você use uma biblioteca de manipulador JSON Web Token padrão para sua plataforma de desenvolvimento (confira o [código-fonte do manipulador JWT .NET do Azure AD](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/System.IdentityModel.Tokens.Jwt/JwtSecurityTokenHandler.cs)). Dito isso, a segurança do seu aplicativo é sua responsabilidade. Portanto, verifique se a biblioteca que você usa para manipular o id\_token corretamente valida os seguintes aspectos do token:

- **Tempo do token**: verifique se o token não está muito atualizado ou obsoleto nas declarações nbf e exp. É comum dar um pouco de folga (cinco minutos) para acomodar variações de tempo.
- **Emissor**: verifique se o emissor do token é o Azure Active Directory na declaração iss: https://sts.windows.net/{tenant_id_of_the_directory}
- **Público**: verifique se o token foi criado para seu aplicativo na declaração aud. O valor deve ser o ID do cliente do seu aplicativo.
- **Nonce**: verifique se a resposta foi solicitada pelo seu aplicativo e se o token não está sendo repetido comparando a declaração nonce com os dados nonce enviados na solicitação de autorização.
- **Assinatura**: seu aplicativo deve verificar se o token foi assinado pelo Azure Active Directory. As chaves de assinatura do Azure AD são substituídas com frequência e, portanto, seu aplicativo deverá sondar chaves atualizadas diariamente ou indicar falha das chaves atualizadas se houver falha na validação da assinatura. Para saber mais, confira [Informações importantes sobre a substituição da chave de assinatura no Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx)

Quando o **id\_token** tiver sido validado, use o valor da declaração oid como identificador imutável e não reutilizável do usuário. Use uma declaração **unique\_name** ou a declaração upn/email como o nome de exibição legível humano do usuário. Você também pode usar as declarações iven\_name/family\_name opcionais para fins de exibição.

### Solicitação de token (Fluxo de concessão de código OAuth 2.0)

Agora que seu aplicativo recebeu o código de autorização do Azure AD, é hora de obter o token de acesso para o Azure Resource Manager. Poste uma solicitação de token de concessão de código OAuth 2.0 no ponto de extremidade de token do Azure AD:

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

Os parâmetros de cadeia de caracteres de consulta que estão disponíveis para essa solicitação estão descritos no tópico [Fluxo de concessão de código de autorização](https://msdn.microsoft.com/library/azure/dn645542.aspx).

O exemplo abaixo mostra uma solicitação de token de concessão de código com credenciais de senha:

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Ao trabalhar com as credenciais de certificado, crie um JWT (Token Web JSON) e assine (RSA SHA256) usando a chave particular de credenciais de certificado do seu aplicativo. Os tipos de declaração para o token são mostrados no [Fluxo de concessão de código de autorização](https://msdn.microsoft.com/library/azure/dn645542.aspx). Para referência, confira o [Código da biblioteca de autenticação do Active Directory (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/master/src/ADAL.NET/CryptographyHelper.cs) para assinar tokens JWT de asserção de cliente.

Confira as [especificações do Open ID Connect](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) para obter detalhes sobre a autenticação de cliente. Aqui está um [token JWT de asserção de cliente de exemplo](https://www.authnauthz.com/OAuth/ParseJWTToken?token=eyJhbGciOiJSUzI1NiIsIng1dCI6IlFwcXdKZnJNZ003ekJ4M1hkM2NSSFdkYVFsTSJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ud2luZG93cy5uZXRcL2FhbHRlc3RzLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQyODk2Mjk5MSwiaXNzIjoiOTA4M2NjYjgtOGE0Ni00M2U3LTg0MzktMWQ2OTZkZjk4NGFlIiwianRpIjoiMmYyMjczMzQtZGQ3YS00NzZkLWFlOTYtYzg4NDQ4YTkxZGM0IiwibmJmIjoxNDI4OTYyMzkxLCJzdWIiOiI5MDgzY2NiOC04YTQ2LTQzZTctODQzOS0xZDY5NmRmOTg0YWUifQ.UXQE9H-FlwxYQmRVG0-p7pAX9TFgiRXcYr7GhbcC7ndIPHKpZ5tfHWPEgBl3ZVRvF2l8uA7HEV86T7t2w7OHhHwLBoW7XTgj-17hnV1CY21MwjrebPjaPIVITiilekKiBASfW2pmss3MjeOYcnBV2MuUnIgt4A_iUbF_-opRivgI4TFT4n17_3VPlChcU8zJqAMpt3TcAxC3EXXfh10Mw0qFfdZKqQOQxKHjnL8y7Of9xeB9BBD_b22JNRv0m7s0cYRx2Cz0cUUHw-ipHhWaW7YwhVRMfK6BMkaDUgaie4zFkcgHb7rm1z0rM1CvzIqP-Mwu3oEqYpY9cYo8nEjMyA).

O exemplo abaixo mostra uma solicitação de token de concessão de código com credenciais de certificado:

	POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
	
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 1012
	
	grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Um exemplo de resposta de token de concessão de código:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### Manipular resposta de token de concessão de código

Uma resposta bem-sucedida de token conterá o token de acesso (usuário e aplicativo) para o Azure Resource Manager. Seu aplicativo usará esse token de acesso para acessar o Gerenciador de Recursos em nome do usuário. A vida útil dos tokens de acesso emitidos pelo Azure AD é de uma hora. É improvável que seu aplicativo Web precise renovar o token de acesso(usuário e aplicativo). No entanto, se isso acontecer, você poderá usar o token de atualização que seu aplicativo recebe na resposta do token. Poste uma solicitação de token OAuth 2.0 no ponto de extremidade de token do Azure AD:

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

Os parâmetros para usar na solicitação de atualização estão descritos no [Fluxo de concessão de código de autorização](https://msdn.microsoft.com/library/azure/dn645542.aspx).

O exemplo abaixo mostra como usar o token de atualização:

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Observe que, embora os tokens de atualização possam ser usados para obter novos tokens de acesso para o Azure Resource Manager, eles não são adequados para que seu aplicativo tenha acesso offline. O tempo de vida dos tokens de atualização é limitado e os tokens de atualização estão associados ao usuário. Se o usuário sai da organização, o aplicativo que usa o token de atualização perderá o acesso. Essa abordagem não é adequada para aplicativos que são usados pelas equipes para gerenciar seus recursos do Azure.

## Listar assinaturas disponíveis

Seu aplicativo agora tem um token para acessar o Azure Resource Manager em nome do usuário.

A próxima etapa da experiência é permitir que o usuário conecte sua assinatura do Azure ao seu aplicativo para que este possa gerenciar as assinaturas mesmo quando o usuário não está presente (acesso offline a longo prazo). Mostre ao usuário a lista das assinaturas do Azure cujo acesso ele pode gerenciar e permita que o usuário atribua uma função RBAC diretamente à identidade do aplicativo.

![Autorização do ARM - aplicativo de exemplo Ux 4](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4-full.png)

### Listar assinaturas à qual o usuário tem acesso

Devemos chamar primeiro a API [de listagem de assinaturas do Gerenciador de Recursos](https://msdn.microsoft.com/library/azure/dn790531.aspx) para listar todas as assinaturas em que o usuário tem qualquer tipo de acesso. Em seguida, podemos identificar as assinaturas em que o usuário pode gerenciar o acesso.

O método [GetUserSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L79) do aplicativo de exemplo do ASP.NET MVC implementa essa chamada.

Eis um exemplo de solicitação para listar assinaturas:

    GET https://management.azure.com/subscriptions?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Eis uma resposta de exemplo para listar assinaturas:

    HTTP/1.1 200 OK

    {"value":[{"id":"/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2","subscriptionId":"34370e90-ac4a-4bf9-821f-85eeedeae1a2","displayName":"Sandbox","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}},{"id":"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e","subscriptionId":"c276fc76-9cd4-44c9-99a7-4fd71546436e","displayName":"Production","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}}]}

### Obter permissões do usuário na assinatura

A ação de conectar/desconectar só deverá ser exibida para assinaturas nas quais o usuário pode gerenciar o acesso. Para cada assinatura, você chamará a API [de listagem de permissões do Gerenciador de Recursos](https://msdn.microsoft.com/library/azure/dn906889.aspx) para determinar se o usuário tem direitos de gerenciamento de acesso na assinatura.

O método [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L132) do aplicativo de exemplo do ASP.NET MVC implementa essa chamada.

O exemplo a seguir mostra como solicitar permissões de um usuário em uma assinatura. 83cfe939-2402-4581-b761-4f59b0a041e4 é a ID da assinatura.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Eis um exemplo de resposta para obter permissões do usuário na assinatura:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

A API de permissões retorna várias permissões. Cada permissão consiste em ações permitidas (actions) e ações não permitidas (notactions). Se uma ação está presente na lista de ações permitidas de alguma permissão e não está presente na lista notactions daquela permissão, o usuário tem permissão para executar essa ação. **microsoft.authorization/roleassignments/write** é a ação que concede acesso a direitos de gerenciamento. Seu aplicativo deve analisar o resultado de permissões e procurar uma correspondência de regex nessa cadeia de caracteres de ação em actions e notactions de cada permissão.

### Opcional: listar diretórios em que a conta do usuário está presente

Uma conta de usuário pode estar presente em vários Azure Active Directories. É possível que o usuário não especifique o nome do diretório correto inicialmente. Nesse caso, a assinatura desejada não será exibida na lista.

A API[de listagem de locatários do Gerenciador de Recursos](https://msdn.microsoft.com/library/azure/dn790536.aspx) lista os identificadores de todas as pastas em que a conta do usuário está presente. Você pode chamar a API para determinar se a conta do usuário está em mais de um diretório e, opcionalmente, mostrar ao usuário uma mensagem como "não encontrou a assinatura que estava procurando? Ela pode estar em outros Azure Active Directory dos quais você é membro. Clique aqui para alternar o diretório".

O método [GetUserOrganizations](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) do aplicativo de exemplo do ASP.NET MVC implementa essa chamada.

Um exemplo de solicitação para listar diretórios:

    GET https://management.azure.com/tenants?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1Qi****8DJf1UO4a-ZZ_TJmWFlwO1mM7Cw6JWtfY2lGc5A

Um exemplo de resposta para listar diretórios:

    HTTP/1.1 200 OK

    {"value":[{"id":"/tenants/7fe877e6-a150-4992-bbfe-f517e304dfa0","tenantId":"7fe877e6-a150-4992-bbfe-f517e304dfa0"},{"id":"/tenants/62e173e9-301e-423e-bcd4-29121ec1aa24","tenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24"}]}

## Conectar a assinatura ao aplicativo

Agora você tem uma lista de assinaturas do Azure que o usuário pode conectar ao seu aplicativo. A próxima etapa é dar ao usuário um comando para criar a conexão. Quando o usuário seleciona **conectar**, seu aplicativo:

1. Atribui a função RBAC apropriada para a identidade do seu aplicativo na assinatura.
2. Valida a atribuição de acesso consultando a permissão do aplicativo na assinatura ou acessando o Gerenciador de Recursos usando o token somente de aplicativo.
1. Registra a conexão em sua estrutura de dados de "assinaturas conectadas" do aplicativo, persistindo a ID da assinatura.

Vamos examinar mais detalhadamente a primeira etapa. Para atribuir a função RBAC apropriada para a identidade do aplicativo, você deve determinar:

- A ID do objeto de identidade do seu aplicativo no Azure Active Directory do usuário
- O identificador da função RBAC que seu aplicativo requer na assinatura

Vamos analisar a primeira parte: a primeira vez que o aplicativo autentica um usuário do Azure AD, um objeto Entidade de Serviço é criado para seu aplicativo nesse Azure AD. O Azure permite que as funções RBAC sejam atribuídas a entidades de serviço para conceder acesso direto a aplicativos correspondentes nos recursos do Azure. Isso é exatamente o que desejamos fazer. Portanto, começamos consultando a API do Azure AD Graph para determinar o identificador da entidade de serviço do seu aplicativo no Azure AD do usuário conectado.

Você tem apenas um token de acesso para o Azure Resource Manager. É necessário um novo token de acesso para chamar a API do Azure AD Graph. Cada aplicativo no Azure AD tem permissão para consultar seu próprio objeto Entidade de Serviço e, portanto, não precisamos de um token de usuário e acesso ao aplicativo para isso. Um token de acesso somente de aplicativo é suficiente.

<a id="app-azure-ad-graph">
### Obter o token de acesso somente de aplicativo para a API do Azure AD Graph

Para autenticar seu aplicativo e obter um token para a API do Azure AD Graph, emita uma solicitação de token de fluxo OAuth 2.0 de concessão de credencial de cliente para o ponto de extremidade de token do Azure AD (**http://login.microsoftonline.com/{directory\_domain\_name}/OAuth2/Token**).

As linhas 73-77 do método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L73) do exemplo de aplicativo ASP.net MVC obtém um token de acesso somente de aplicativo para a API do Graph usando a biblioteca de autenticação do Active Directory para .NET.

Os dados de solicitação do token de concessão de credencial do cliente:

| Elemento | Valor
|----|----
| grant\_type | **client\_credentials**
| client\_id | ID do cliente do seu aplicativo
| recurso | Identificador codificado na URL do recurso para o qual o token de acesso está sendo solicitado. Nesse caso, o identificador da API do Azure AD Graph: **https://graph.windows.net/** 
| client\_secret or client\_assertion\_type + client\_assertion | Se seu aplicativo usa credenciais de senha, use client\_secret. Se seu aplicativo usa credenciais de certificado, use client\_assertion.

Um exemplo de solicitação de token de concessão de credencial de cliente:

    POST https://login.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Um exemplo de resposta de token de concessão de credencial de cliente:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### Obter a ID de objeto da entidade de serviço de aplicativo no Azure AD do usuário

Use o token de acesso somente de aplicativo para consultar a API [Entidades de serviço do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ServicePrincipalEntity) a fim de determinar a ID de objeto da entidade de serviço do aplicativo no diretório.

O método [GetObjectIdOfServicePrincipalInOrganiation](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L66) do aplicativo de exemplo ASP.net MVC implementa essa chamada.

O exemplo a seguir mostra como solicitar a entidade de serviço do aplicativo. a0448380-c346-4f9f-b897-c18733de9394 é a ID do cliente do aplicativo.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

O exemplo a seguir mostra uma resposta à solicitação da entidade de serviço do aplicativo

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### Obter o identificador de função RBAC do Azure

Você deve atribuir a função RBAC apropriada à entidade de serviço do aplicativo na assinatura selecionada. Para isso, você deve determinar o identificador da função RBAC do Azure.

A função RBAC certa para seu aplicativo:

- Se seu aplicativo monitora apenas a assinatura, sem fazer alterações, ele exige apenas permissões de leitura na assinatura. Atribuir a função **Leitor**.
- Se seu aplicativo gerencia a assinatura do Azure criando/modificando/excluindo entidades, é necessária uma das permissões de colaborador.
  - Para gerenciar um determinado tipo de recurso, atribua as funções de colaborador específicas do recurso (colaborador de máquina virtual, colaborador de rede virtual, colaborador da conta de armazenamento, etc.)
  - Para gerenciar um tipo de recurso, atribua a função **Colaborador**.

A atribuição de função para seu aplicativo estará visível para os usuários. Portanto, selecione o privilégio mínimo exigido.

Chame a [API de definição de função do Resource Manager](https://msdn.microsoft.com/library/azure/dn906879.aspx) para listar todas as funções RBAC do Azure, pesquisar e iterar sobre o resultado a fim de localizar a definição de função desejada pelo nome.

O método [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L354) do aplicativo de exemplo ASP.net MVC implementa essa chamada.

O exemplo de solicitação a seguir mostra como obter o identificador de função RBAC do Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb é a ID da assinatura.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

A resposta está no seguinte formato:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Observe que você não precisa chamar essa API continuamente. Depois de determinar o GUID conhecido da definição da função, você poderá construir a ID de definição de função como:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Aqui estão os GUIDS conhecidos de funções internas comumente usadas:

| Função | Guid |
| ----- | ------ |
| Leitor | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Colaborador | b24988ac-6180-42a0-ab88-20f7382dd24c
| Colaborador de Máquina Virtual | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Colaborador de Rede Virtual | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Colaborador da Conta de Armazenamento | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Colaborador do Site | de139f84-1756-47ae-9be6-808fbbe84772
| Colaborador do Plano de Web | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| Colaborador do SQL Server | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| Colaborador do banco de dados SQL | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### Atribuir função RBAC ao aplicativo

Você tem tudo o que é necessário para atribuir a função RBAC apropriada à entidade de serviço do aplicativo na assinatura selecionada usando a API [de criação e atribuição de função do Gerenciador de Recursos](https://msdn.microsoft.com/library/azure/dn906887.aspx).

O método [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L269) do aplicativo de exemplo ASP.net MVC implementa essa chamada.

Um exemplo de solicitação para atribuir função RBAC ao aplicativo:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2014-10-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Na solicitação, são usados os seguintes valores:

| Guid | Descrição |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | a ID da assinatura
| c3097b31-7309-4c59-b4e3-770f8406bad2 | a ID de objeto da entidade de serviço do aplicativo
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | o GUID conhecido da função RBAC Leitor
| 4f87261d-2816-465d-8311-70a27558df4c | um novo GUID criado para a nova atribuição de função

A resposta está no seguinte formato:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### Obter token de acesso somente de aplicativo para o Azure Resource Manager

A próxima etapa é validar que o aplicativo tem o acesso desejado na assinatura. Para isso, você deve executar uma tarefa de teste na assinatura usando um token somente de aplicativo para o Azure Resource Manager. A tarefa de teste deve validar que o seu aplicativo, de fato, tem o acesso desejado na assinatura para executar monitoramento/gerenciamento offline.

Para obter um token de acesso somente de aplicativo para o Azure Resource Manager, siga as instruções da seção [,Obter o token de acesso somente de aplicativo para a API do Azure AD Graph](#app-azure-ad-graph) com um valor diferente para o parâmetro de recurso:

    https://management.core.windows.net/

As linhas 210 a 214 do método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) do exemplo de aplicativo ASP.net MVC obtém um token de acesso somente de aplicativo para o Azure Resource Manager usando a biblioteca de autenticação do Active Directory para .NET.

#### Obter permissões do aplicativo na assinatura

Para verificar se seu aplicativo tem o acesso desejado em uma assinatura do Azure, você também pode chamar a API de [permissões do Resource Manager](https://msdn.microsoft.com/library/azure/dn906889.aspx) de maneira semelhante a como você determinou se o usuário tem acesso de gerenciamento de direitos para a assinatura ou não. No entanto, desta vez, chame a API de permissões com o token de acesso somente de aplicativo que foi recebido na etapa anterior.

O método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) do aplicativo de exemplo ASP.net MVC implementa essa chamada.

## Gerenciar assinaturas conectadas

Quando a função RBAC apropriada é atribuída à entidade de serviço do seu aplicativo na assinatura, seu aplicativo pode continuar a monitorar e a gerenciar usando tokens de acesso somente de aplicativo para o Azure Resource Manager.

Se um proprietário de assinatura remove a atribuição de função do aplicativo usando o Portal de Gerenciamento do Azure ou as ferramentas de linha de comando, o aplicativo não consegue acessar essa assinatura. Nesse caso, você deve informar ao usuário que a conexão com a assinatura foi cortada fora do aplicativo e dar-lhe uma opção para "reparar" a conexão. "Reparar" é simplesmente recriar a atribuição de função que foi excluída offline.


## Desconectar assinaturas

Assim como você habilitou o usuário para conectar suas assinaturas ao seu aplicativo, deve permitir que o usuário também desconecte as assinaturas. Do ponto de vista do gerenciamento de acess, desconectar significa remover a atribuição da entidade de serviço do aplicativo na assinatura. Opcionalmente, o estado da assinatura no aplicativo também pode ser removido. Somente os usuários com permissão de gerenciamento de acesso na assinatura poderão desconectar a assinatura.

O método [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L303) do aplicativo de exemplo ASP.net MVC implementa essa chamada.

Pronto, os usuários podem agora se conectar e gerenciar suas assinaturas do Azure com seu aplicativo facilmente.

<!---HONumber=AcomDC_0420_2016-->