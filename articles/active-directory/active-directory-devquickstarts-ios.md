<properties
	pageTitle="Introdução a iOS no Azure AD | Microsoft Azure"
	description="Como criar um aplicativo para iOS que se integre ao Azure AD para fazer login e que chame as APIs protegidas do Azure AD usando o OAuth."
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# Integrar o Azure AD em um aplicativo iOS

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se você estiver desenvolvendo um aplicativo de desktop, o Azure AD torna a autenticação dos usuários com suas contas do Active Directory simples e direta. Ele também permite que seu aplicativo consuma com segurança qualquer API da Web protegida pelo Azure AD, como as APIs do Office 365 ou a API do Azure.

Para clientes iOS que precisam acessar recursos protegidos, o Azure AD fornece a biblioteca de autenticação do Active Directory, ou ADAL. A única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como é fácil, vamos criar aqui um aplicativo de lista de tarefas para Objective C que:

-	Obtém tokens de acesso para chamar a Graph API do Azure AD usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Pesquisa um diretório para usuários com um determinado alias.
-	Faz logout dos usuários.

Para compilar o aplicativo em funcionamento completo, você precisará:

2. Registrar seu aplicativo no Azure AD.
3. Instalar e configurar a ADAL.
5. Usar a ADAL para obter tokens do Azure AD.

Para começar, [baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Você também precisará de um locatário do Azure AD no qual você possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário, [saiba como obter um](active-directory-howto-tenant.md).

## Etapa 1: Baixe e execute o servidor de exemplo da API REST TODO .Net ou Node. js

Este exemplo é escrito especificamente para funcionar em nosso exemplo existente para criar uma API REST ToDo de locatário único para o Microsoft Azure Active Directory. Este é um pré-requisito para o início rápido.

Para obter informações sobre como configurar isso, visite nosso exemplo existente aqui:

* [Serviço de API REST de exemplo para do Microsoft Azure Active Directory para Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## Etapa 2: Registre sua API da Web com seu locatário do Microsoft Azure AD

**O que estou fazendo?**

*O Microsoft Active Directory oferece suporte à adição de dois tipos de aplicativos. APIs da Web que oferecem serviços a usuários e aplicativos (ou na web ou em um aplicativo em execução em um dispositivo) que acessam essas APIs da Web. Nesta etapa, você está registrando a API da Web que está sendo executada localmente para testar este exemplo. Normalmente, essa API Web seria um serviço REST que está oferecendo a funcionalidade que você quer que um aplicativo acesse. O Microsoft Azure Active Directory pode proteger qualquer ponto de extremidade!*

*Aqui, estamos supondo que você está registrando a API REST TODO mencionada acima, mas isso funciona para qualquer API da Web que você gostaria que o Azure Active Directory protegesse.*

Etapas para registrar uma API da Web com o Microsoft Azure AD

1. Faça login no [portal de gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em Active Directory no NAV à esquerda.
3. Clique no locatário do diretório onde você deseja registrar o aplicativo de exemplo.
4. Clique na guia Aplicativos.
5. Na gaveta, clique em Adicionar.
6. Clique em “Adicionar um aplicativo que minha organização está desenvolvendo”.
7. Insira um nome amigável para o aplicativo, por exemplo, "TodoListService", selecione “Aplicativo Web e/ou API da Web” e clique em Avançar.
8. Para a URL de logon, insira a URL base para o exemplo, que é, por padrão, `https://localhost:8080`.
9. Para o URI de ID do aplicativo, insira`https://<your_tenant_name>/TodoListService`, substituindo `<your_tenant_name>` pelo nome do seu locatário do Azure AD. Clique em OK para concluir o registro.
10. Ainda no portal do Azure, clique na guia Configurar de seu aplicativo.
11. **Encontre o valor da ID do cliente, copie-o e guarde-o**; você precisará dele mais tarde ao configurar seu aplicativo.

## Etapa 3: Registre o aplicativo cliente nativo do iOS de exemplo

Registrar seu aplicativo Web é a primeira etapa. Em seguida, você precisará informar o Azure Active Directory sobre seu aplicativo. Isso permite que o aplicativo se comunique com a API da Web recém-registrada

**O que estou fazendo?**

*Como mencionado acima, o Microsoft Azure Active Directory oferece suporte à adição de dois tipos de aplicativos. APIs da Web que oferecem serviços a usuários e aplicativos (ou na web ou em um aplicativo em execução em um dispositivo) que acessam essas APIs da Web. Nesta etapa, você está registrando o aplicativo neste exemplo. Você deve fazer isso para que este aplicativo seja capaz de solicitar acesso à API da Web que você acabou de registrar. O Azure Active Directory irá se recusar a permitir que o aplicativo solicite o login a menos que ele esteja registrado! Isso faz parte da segurança do modelo.*

*Aqui, estamos supondo que você está registrando este aplicativo de exemplo mencionado acima, mas isso funciona para qualquer aplicativo que você está desenvolvendo.*

**Por que eu estou colocando um aplicativo e uma API da Web em um locatário?**

*Como você deve ter adivinhado, você pode criar um aplicativo que acessa uma API externa que é registrada no Azure Active Directory de outro locatário. Se você fizer isso, os clientes precisarão concordar com o uso da API no aplicativo. A parte boa é que a biblioteca de autenticação do Active Directory para iOS se encarrega desse consentimento por você! Conforme chegamos aos recursos mais avançados, você verá que essa é uma parte importante do trabalho necessário para acessar o conjunto de APIs da Microsoft no Azure e Office, bem como em qualquer outro provedor de serviço. Por enquanto, como você registrou sua API da Web e seu aplicativo sob o mesmo locatário, você não verá solicitações de permissão. Isso normalmente acontece se você estiver desenvolvendo um aplicativo para uso somente por sua própria empresa.*

1. Faça login no [portal de gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em Active Directory no NAV à esquerda.
3. Clique no locatário do diretório onde você deseja registrar o aplicativo de exemplo.
4. Clique na guia Aplicativos.
5. Na gaveta, clique em Adicionar.
6. Clique em “Adicionar um aplicativo que minha organização está desenvolvendo”.
7. Insira um nome amigável para o aplicativo, por exemplo, "TodoListClient-iOS", selecione “Aplicativo cliente nativo” e clique em Avançar.
8. Para o URI de redirecionamento, digite `http://TodoListClient`. Clique em Concluir.
9. Clique na guia Configurar do aplicativo.
10. Encontre o valor da ID do cliente, copie-o e guarde-o; você precisará dele mais tarde ao configurar seu aplicativo.
11. Em “Permissões para outros aplicativos”, clique em “Adicionar aplicativo.” Selecione “Outros” na lista suspensa “Mostrar” e clique na marca de seleção superior. Localize e clique em TodoListService e clique na marca de seleção da parte inferior para adicionar o aplicativo. Selecione “Acessar TodoListService” na lista suspensa “Permissões delegadas” e salve a configuração.


## Etapa 4: Baixe o código de exemplo do cliente nativo do iOS

* `$ git clone git@github.com:AzureADSamples/NativeClient-iOS.git`

## Etapa 5: Baixe a ADAL para iOS e adicione-a ao seu espaço de trabalho do XCode

#### Baixe a ADAL para iOS

* `git clone git@github.com:MSOpenTech/azure-activedirectory-library-for-ios.git`

#### Importe a biblioteca no espaço de trabalho

No XCode, clique com o botão direito do mouse no diretório do projeto e selecione "Adicionar arquivos ao exemplo do iOS"

Quando solicitado, selecione o diretório onde você clonou a ADAL para iOS

#### Adicione libADALiOS.a às suas estruturas e bibliotecas vinculadas

Clique no botão Adicionar em "Estruturas e bibliotecas vinculadas" e adicione o arquivo de biblioteca a partir das estruturas importadas.

Compile o projeto para garantir que tudo seja compilado corretamente.


## Etapa 6: Configure o arquivo settings.plist com as informações da API da Web

Em "Arquivos de suporte", você encontrará um arquivo settings.plist. Ele contém as seguintes informações:

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>authority</key>
	<string>https://login.windows.net/common/oauth2/token</string>
	<key>clientId</key>
	<string>xxxxxxx-xxxxxx-xxxxxxx-xxxxxxx</string>
	<key>resourceString</key>
	<string>https://localhost/todolistservice</string>
	<key>redirectUri</key>
	<string>http://demo_todolist_app</string>
	<key>userId</key>
	<string>user@domain.com</string>
	<key>taskWebAPI</key>
	<string>https://localhost/api/todolist/</string>
</dict>
</plist>
```

Substitua as informações no arquivo plist pelas suas configurações da API da Web.

##### OBSERVAÇÃO

Os padrões atuais são configurados para funcionar com nosso [serviço da API REST de exemplo do Azure Active Directory para Node. js](https://github.com/AzureADSamples/WebAPI-Nodejs). No entanto, você precisará especificar o clientID de sua API da Web. Se você estiver executando sua própria API, você precisará atualizar os pontos de extremidade conforme necessário.

## Etapa 7: Compile e execute o aplicativo

Você deve ser capaz de se conectar ao ponto de extremidade da API REST e informar as credenciais de sua conta do Azure Active Directory.

Para obter recursos adicionais, confira: - [AzureADSamples no GitHub >>](https://github.com/AzureAdSamples) - Documentação do Azure AD em [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=58--> 