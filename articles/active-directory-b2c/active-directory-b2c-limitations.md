<properties
	pageTitle="Visualização do Active Directory B2C do Azure: limitações e restrições | Microsoft Azure"
	description="Uma lista de limitações e restrições com o Active Directory B2C do Azure"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: limitações e restrições

Há vários recursos e funcionalidades do Active Directory B2C do Azure (AD do Azure) que ainda não têm suporte durante a visualização. Muitas dessas limitações serão removidas antes que o AD B2C do Azure alcance disponibilidade geral, mas é preciso estar ciente delas se você estiver criando aplicativos voltados ao cliente usando o AD B2C do Azure durante a visualização.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Problemas durante a criação de locatários do AD B2C do Azure

Se você tiver problemas durante a [criação de um locatário do AD B2C do Azure](active-directory-b2c-get-started), consulte [Criar um locatário do AD do Azure ou um locatário do AD B2C do Azure -- problemas e resoluções](active-directory-b2c-support-create-directory.md) para obter diretrizes.

## Problemas de identidade visual no email de verificação

O email de verificação padrão contém a identidade visual da Microsoft. Nós a removeremos no futuro. Por enquanto, você poderá removê-la usando o [recurso de identidade visual da empresa](../active-directory/active-directory-add-company-branding.md).

## Suporte para aplicativos de produção

Os aplicativos que se integram ao AD B2C do Azure não devem ser liberados ao público como aplicativos de nível de produção. No momento, o AD B2C do Azure está no modo de visualização, as alterações significativas podem ser introduzidas em qualquer instante e não há Contrato de Nível de Serviço garantido pelo serviço. Não haverá suporte para qualquer incidente que possa ocorrer. Se você estiver disposto a aceitar os riscos de assumir uma dependência de um serviço que ainda esteja em desenvolvimento, envie um tweet para @AzureAD para discutir o escopo de seu aplicativo ou serviço.

## Restrições em aplicativos

Os seguintes tipos de aplicativos não têm suporte atualmente na visualização do AD B2C do Azure. Para obter uma descrição dos tipos de aplicativos com suporte, consulte a [Visualização do AD B2C do Azure: tipos de aplicativos](active-directory-b2c-apps.md) .

### Aplicativos de Página Única (JavaScript)

Muitos aplicativos modernos têm um front-end de SPA (Aplicativo de Página Única) escrito principalmente em JavaScript e que usa com frequência estruturas do SPA, como AngularJS, Ember.js, Durandal etc. Esse fluxo ainda não está disponível na visualização do AD B2C do Azure.

### Aplicativos daemons/do lado do servidor

Os aplicativos que contêm processos de longa duração ou que operam sem a presença de um usuário também precisam encontrar uma maneira de acessar os recursos protegidos, tais como APIs Web. Esses aplicativos podem autenticar e obter tokens pelo uso da identidade do aplicativo (em vez de usar a identidade delegada do consumidor), usando o [fluxo de credenciais de cliente do OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Esse fluxo ainda não está disponível na visualização do AD B2C do Azure. Portanto, os aplicativos só poderão obter tokens após um fluxo de entrada interativa do consumidor.

### APIs da Web autônomas

Na visualização do AD B2C do Azure, você pode [compilar uma API Web protegida usando tokens do OAuth 2.0](active-directory-b2c-apps.md#web-apis). No entanto, essa API Web só poderá receber tokens de um cliente que compartilha a mesma ID de aplicativo. Não há suporte para compilação de uma API Web que é acessada de vários clientes diferentes.

### Cadeias de API Web (Em nome de)

Muitas arquiteturas incluem uma API da Web que precisa chamar outra API da Web downstream, ambas protegidas pelo AD B2C do Azure. Este cenário é comum em clientes nativos que têm um back-end de API Web que, por sua vez, chama um serviço online da Microsoft, como a API do Graph do AD do Azure.

Este cenário de API Web encadeada pode ter suporte usando a concessão Credencial de Portador Jwt do OAuth 2.0, também conhecido como fluxo Em nome de. No entanto, o fluxo Em nome de não está implementado atualmente na visualização do AD B2C do Azure.

## Restrição em bibliotecas e SDKs

Nem todas as linguagens e plataformas têm bibliotecas com suporte e visualização do AD B2C do Azure. O conjunto de bibliotecas de autenticação é atualmente limitado ao .NET, iOS, Android e NodeJS. Tutoriais de início rápido correspondentes a cada um estão disponíveis na seção [Introdução](active-directory-b2c-overview.md#getting-started).

Para integrar um aplicativo com a visualização do AD B2C do Azure usando outro idioma ou plataforma, consulte a [Referência do protocolo OAuth 2.0 e OpenID Connect](active-directory-b2c-reference-protocols.md), que o instruirá sobre como construir as mensagens HTTP necessárias para se comunicar com o serviço do AD B2C do Azure.

## Restrição em protocolos

A visualização AD B2C do Azure dá suporte a OAuth 2.0 e OpenID Connect. No entanto, nem todos os recursos e capacidades de cada protocolo foram implementados. Para entender melhor o escopo da funcionalidade de protocolo com suporte no modo de visualização do AD B2C do Azure, leia nossa [referência do protocolo OAuth 2.0 e OpenID Connect](active-directory-b2c-reference-protocols.md). Suporte a protocolo SAML e WS-Fed não está disponível.

## Restrição em tokens

Muitos dos tokens emitidos pela visualização AD B2C do Azure são implementados como Tokens da Web JSON ou JWTs. No entanto, nem todas as informações contidas no JWTs (conhecidas como "declarações") são exatamente como deveriam ser ou não existem. Alguns exemplos incluem "sub" e as declarações de "preferred\_username". Você deve esperar que as coisas mudem um pouco durante a visualização. Para entender melhor os tokens emitidos atualmente pelo serviço do AD B2C do Azure, leia nossa [referência de token](active-directory-b2c-reference-tokens.md).

## Problemas de gerenciamento de usuário no Portal Clássico do Azure

Recursos de B2C são acessíveis no Portal do Azure. No entanto, você pode usar o Portal Clássico do Azure para acessar outros recursos de locatário, incluindo o gerenciamento de usuários. Atualmente, há alguns problemas conhecidos com o gerenciamento de usuários (a guia **Usuários**) no portal clássico do Azure:

- Para um usuário de conta local (ou seja, um consumidor que se inscreve com um endereço de email e senha ou um nome de usuário e senha), o campo **Nome do Usuário** não corresponde ao identificador de entrada (endereço de email ou nome de usuário) usado na inscrição. Isso ocorre porque o campo exibido no Portal Clássico do Azure é, na verdade, o UPN (Nome UPN), que não é usado em cenários B2C. Para exibir o identificador de entrada da conta local, localize o objeto do usuário no [Gerenciador do Graph](https://graphexplorer.cloudapp.net/). Você encontrará o mesmo problema com um usuário de conta social (ou seja, um consumidor que se inscreve com o Facebook, Google +, etc.), mas nesse caso, não há nenhum identificador de entrada.

    ![Conta local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Para um usuário de conta local, você não será capaz de editar nenhum dos campos e salvar as alterações na guia **Perfil**. Corrigiremos isso em breve.

## Problemas com a redefinição de senha iniciada pelo administrador no Portal Clássico do Azure

Se você redefinir a senha para um consumidor baseado em conta local no Portal Clássico do Azure (o comando **Redefinir Senha** na guia **Usuários**), o consumidor não poderá alterar a senha no próximo logon e será bloqueado dos seus aplicativos. Estamos trabalhando para corrigir esse problema. Como solução alternativa, use a [API do Graph do AD do Azure](active-directory-b2c-devquickstarts-graph-dotnet.md) para redefinir a senha do consumidor.

## Restrição de exclusão de locatários do AD B2C do Azure

Você não poderá excluir um locatário do AD B2C do Azure no Portal Clássico do Azure.

## Problemas de verificação de um domínio no Portal Clássico do Azure

Atualmente, você não pode verificar um domínio com êxito no [Portal Clássico do Azure](https://manage.windowsazure.com/). Estamos trabalhando em uma correção.

<!---HONumber=AcomDC_0427_2016-->