<properties
	pageTitle="Aplicativo Android v2.0 do Azure AD | Microsoft Azure"
	description="Como compilar um aplicativo Android que conecte usuários com a conta da Microsoft pessoal e as contas corporativas ou de estudante e as chamadas da API do Graph usando bibliotecas de terceiros."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="brandwe"/>

#  Adicionar entrada a um aplicativo Android usando uma biblioteca de terceiros com a API do Graph com o ponto de extremidade v2.0

A plataforma de identidade da Microsoft usa padrões abertos, como OAuth2 e OpenID Connect. Isso permite aos desenvolvedores aproveitar qualquer biblioteca que queiram integrar aos nossos serviços. Para auxiliar os desenvolvedores a usar nossa plataforma com outras bibliotecas, escrevemos alguns passo a passos como este para demonstrar como configurar bibliotecas de terceiros para que elas se conectem à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação RFC6749 do OAuth2](https://tools.ietf.org/html/rfc6749) poderá conectar-se à Plataforma de Identidade da Microsoft.

Esse aplicativo permitirá que um usuário entre em sua organização e procure por conta própria em sua organização usando a API do Graph.

Se você ainda não conhece o OAuth2 ou o OpenID Connect, grande parte desta configuração de exemplo poderá não fazer muito sentido para você. Recomendamos que você examine uma breve [visão geral do protocolo que documentamos aqui](active-directory-v2-protocols-oauth-code.md).


> [AZURE.NOTE]
	Alguns recursos de nossa plataforma que possuem uma expressão nesses padrões, como o Acesso Condicional e o gerenciamento de política do Intune, exigem a utilização das nossas Bibliotecas de Identidades do Microsoft Azure de software livre.


> [AZURE.NOTE]
	Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).


## Baixar
O código para este tutorial é mantido [no GitHub](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git). Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Ou basta baixar e começar imediatamente:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## Registrar um aplicativo
Crie um novo aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com) ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md). Não se esqueça de:

- Copiar a **ID do Aplicativo** designada ao seu aplicativo, você precisará dela logo.
- Adicione a plataforma **Móvel** de seu aplicativo.
- Copie o **URI de Redirecionamento** do portal. Você deve usar o valor padrão de `https://login.microsoftonline.com/common/oauth2/nativeclient`.


## Baixe a biblioteca de terceiros nxoauth2 e inicie um espaço de trabalho

Para este passo a passo, usaremos a OIDCAndroidLib do GitHub, uma biblioteca OAuth2 com base no código do OpenID Connect do Google. Ela implementa o perfil de aplicativo nativo e dá suporte ao ponto de extremidade de autorização do usuário final. Isso é tudo do que precisamos para a integração à plataforma de identidade da Microsoft.

*  Clone

Comece pela clonagem do repositório OIDCAndroidLib em seu computador.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

### Configurar o ambiente do Android Studio

*  Crie o projeto Criar um novo Projeto do AndroidStudio e siga o assistente padrão.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

*  Configure os módulos do seu projeto; acho que a maneira mais fácil de definir os módulos é movendo o repositório clonado para o local do projeto. Você também pode começar pela criação do projeto e depois clonar diretamente para o local do projeto.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

Em seguida, abra as configurações de módulos do projeto no menu contextual ou usando o atalho `Ctrl + Alt + Maj + S`.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

Remova o módulo de aplicativo padrão, pois queremos apenas as configurações de contêiner do projeto.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

Agora, precisamos importar os módulos do repositório clonado para o projeto atual

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

> Repita estas etapas para o módulo `oidlib-sample`

Verificar as dependências de oidclib no módulo `oidlib-sample`

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

Clique em "OK" e aguarde a sincronização do gradle

O settings.gradle deve se parecer com

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

*  Compile o aplicativo de exemplo para certificar-se de que o exemplo está sendo executando corretamente.

Você ainda não poderá usá-lo com o Azure Active Directory. Você precisará configurar alguns pontos de extremidade primeiro. Isso é para garantir que você não tenha problemas no Android Studio antes de começar a personalizar o aplicativo de exemplo.

Compilar e executar o `oidlib-sample` como o destino no Android Studio

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

*  Limpar

Você pode excluir com segurança o diretório `app ` que foi deixado durante a remoção do módulo do projeto, já que o AndroidStudio não o exclui por segurança.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

Além disso, você pode remover a configuração de execução que também foi deixada durante a remoção do módulo do projeto ao abrir o menu "Editar Configurações".

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## Configurar os pontos de extremidade do exemplo

Agora que você tem o `oidlib-sample` sendo executado com êxito, vamos editar alguns pontos de extremidade para que isso funcione com o Azure Active Directory.

* Configurar seu cliente

Abra o arquivo `oidc_clientconf.xml` e faça estas alterações:

1. Já que estamos usando somente fluxos de OAuth2 para obter um token e chamar a API do Graph, vamos definir o cliente para fazer somente o OAuth2. O uso do OIDC virá em um exemplo mais adiante.

```xml
    <bool name="oidc_oauth2only">true</bool>
```

2. Configure sua ID do Cliente que você recebeu do portal de registro.

```xml
    <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
    <string name="oidc_clientSecret"></string>
```

3. Configure seu URI de redirecionamento que você recebeu do portal de registro.

```xml
    <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
```

4. Configure os escopos de que você precisa para acessar a API do Graph.

```xml
    <string-array name="oidc_scopes">
        <item>openid</item>
        <item>User.ReadBasic.All</item>
        <item>offline_access</item>
    </string-array>
```

Aqui, definimos o `oidc_scopes`. Os escopos que solicitaremos para este aplicativo são `User.ReadBasic.All`, o que nos permite ler o perfil básico de todos os usuários em nosso diretório. Você pode saber mais sobre todos os escopos disponíveis para [serem usados com o Microsft Graph aqui](https://graph.microsoft.io/docs/authorization/permission_scopes).

Se você quiser explicações sobre `openid` ou `offline_access` como escopos no OpenID Connect, veja uma breve [visão geral do protocolo que documentamos aqui](active-directory-v2-protocols-oauth-code.md).

* Configurar os pontos de extremidade do cliente

Abra o arquivo `oidc_endpoints.xml` e faça estas alterações:

```xml
<!-- Stores OpenID Connect provider endpoints. -->
<resources>
    <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
    <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
    <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
    <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
</resources>
```

Esses pontos de extremidade nunca deverão ser alterados se você estiver usando o OAuth2 como seu protocolo.

> [AZURE.NOTE] 
Os pontos de extremidade para `userInfoEndpoint` e `revocationEndpoint` no momento não têm suporte do Azure Active Directory e, portanto, os deixaremos com os valores padrão de exemplo.com, o que fornecerá um lembrete útil que não está disponível no exemplo :-)


## Configurar uma chamada à API do Graph

Abra o arquivo `HomeActivity.java` e faça estas alterações:

```Java
   //TODO: set your protected resource url
    private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
```

Aqui, faremos uma chamada a uma API do Graph simples que retornará nossas informações

## Pronto!

Estas são todas as alterações necessárias! Execute o aplicativo `oidlib-sample` e clique em entrar. O

ssim que você tiver se autenticado com êxito, pressione o botão "Solicitar Recurso Protegido" para testar sua chamada à API do Graph.

## Obter atualizações de segurança para nosso produto

Recomendamos que você obtenha notificações sobre a ocorrência de incidentes de segurança visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinando os alertas do Security Advisory.

<!---HONumber=AcomDC_0629_2016-->