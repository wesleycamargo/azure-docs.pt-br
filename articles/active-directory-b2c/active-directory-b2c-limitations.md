<properties
	pageTitle="Visualização do Active Directory B2C do Azure: limitações e restrições | Microsoft Azure"
	description="Uma lista de limitações e restrições com o Active Directory B2C do Azure"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: limitações e restrições

Há vários recursos e funcionalidades do Active Directory B2C do Azure que ainda não têm suporte durante a visualização. Muitas dessas limitações serão removidas antes que o AD B2C do Azure alcance disponibilidade geral, mas é preciso estar ciente delas se você estiver criando aplicativos voltados ao cliente usando o AD B2C do Azure durante a visualização.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Problemas durante a criação de diretórios AD B2C do Azure

Há problemas conhecidos que você pode encontrar durante a [criação de um diretório do AD B2C do Azure](active-directory-b2c-get-started). Confira este [artigo](active-directory-b2c-support-create-directory.md) para obter orientação.

## Problema de marca na página de entrada da conta local

O modelo padrão na página de Entrada na Conta Local vai conter elementos de identidade visual do "Microsoft Azure". Estamos trabalhando ativamente para resolver esse problema. Você pode alterar a identidade visual nesta página usando [recurso de identidade visual da empresa](./active-directory/active-directory-add-company-branding.md).

## Problemas de saída da conta local

Atualmente a saída da conta local não funciona. Estamos trabalhando ativamente para resolver esse problema. A solução é fechar o navegador ou limpar os cookies.

## Suporte para aplicativos de produção

Aplicativos que se integram com o AD B2C do Azure não devem ser liberados ao público como aplicativos de nível de produção. O AD B2C do Azure está no modo de visualização no momento - alterações significativas podem ser introduzidas em qualquer instante e não há SLA garantida pelo serviço. Não haverá suporte para qualquer incidente que possa ocorrer. Se você estiver disposto a aceitar os riscos de ter uma dependência de um serviço que ainda está em desenvolvimento, envie-nos um tweet no @AzureAD para discutir o escopo de seu aplicativo ou serviço.

## Restrições em aplicativos

Os seguintes tipos de aplicativos não têm suporte atualmente na visualização do AD B2C do Azure. Para obter uma descrição dos tipos com suporte de aplicativos, consulte este [artigo](active-directory-b2c-apps).

### Aplicativos de página única (Javascript)

Muitos aplicativos modernos têm um front-end de SPA (Aplicativo de Página Única) escrito principalmente em Javascript e frequentemente usando estruturas SPA, como AngularJS, Ember.js, Durandal, etc. Esse fluxo ainda não está disponível na visualização do AD B2C do Azure.

### Aplicativos Daemons /do lado do servidor

Aplicativos que contêm processos de longa duração ou que operam sem a presença de um usuário também precisam encontrar uma maneira de acessar os recursos protegidos, tais como APIs Web. Esses aplicativos podem autenticar e obter tokens usando a identidade do aplicativo (em vez de identidade delegada do consumidor) usando o [fluxo de credenciais de cliente do OAuth 2.0](active-directory-b2c-protocols.md#oauth2-client-credentials-grant-flow). Esse fluxo ainda não está disponível na visualização do AD B2C do Azure - o que significa que os aplicativos só podem obter somente tokens após um fluxo de entrada interativa do consumidor.

### APIs da Web autônomas

Na visualização AD B2C do Azure você tem a capacidade de [compilar uma API Web protegida usando tokens do OAuth 2.0](active-directory-b2c-apps.md#web-apis). No entanto, essa API Web só poderá receber tokens de um cliente que compartilha a mesma ID de aplicativo. Não há suporte para compilação de uma API Web que é acessada de vários clientes diferentes.

## Restrição de bibliotecas e SDKs

Nem todas as linguagens e plataformas têm bibliotecas com suporte e visualização do AD B2C do Azure. O conjunto de bibliotecas de autenticação é atualmente limitado ao .NET, iOS, Android e NodeJS. Tutoriais de início rápido correspondentes a cada um estão disponíveis na seção [Introdução](active-directory-b2c-overview.md#getting-started).

Para integrar um aplicativo com a visualização do AD B2C do Azure usando outro idioma ou plataforma, consulte a [Referência do protocolo OAuth 2.0 e OpenID Connect](active-directory-b2c-protocols.md) que instruirá sobre como construir as mensagens HTTP necessárias para se comunicar com o serviço AD B2C do Azure.

## Restrição em protocolos

A visualização AD B2C do Azure dá suporte a OAuth 2.0 e OpenID Connect. No entanto, nem todos os recursos e capacidades de cada protocolo foram implementados. Para entender melhor o escopo de funcionalidade de protocolo com suporte no modo de visualização do AD B2C do Azure, leia nossa [referência do protocolo OAuth 2.0 e OpenID Connect](active-directory-b2c-protocols.md).

## Restrição de tokens

Muitos dos tokens emitidos pela visualização AD B2C do Azure são implementados como Tokens da Web JSON ou JWTs. No entanto, nem todas as informações contidas no JWTs (conhecidas como "declarações") são exatamente como deveriam ser ou não existem. Alguns exemplos incluem "sub" e as declarações de "preferred\_username". Você deve esperar que as coisas mudem um pouco durante a visualização. Para entender melhor os tokens emitidos no momento pelo serviço do AD B2C do Azure, leia nossa [referência de token](active-directory-b2c-tokens.md).

## Problemas de gerenciamento de usuário no Portal do Azure

Recursos de B2C são acessíveis no portal de visualização do Azure. No entanto, você pode usar o portal do Azure para acessar outros recursos de diretório, incluindo gerenciamento de usuários. Atualmente, há alguns problemas conhecidos com o gerenciamento de usuários (a guia **Usuários**) no portal de visualização do Azure.

- Para usuário de conta local (ou seja, um consumidor que se inscreve com um endereço de email senha ou um nome de usuário e senha), o campo **Nome do Usuário** não corresponde ao entrar identificador de entrada (endereço de email ou nome de usuário) usado na inscrição. Isso ocorre porque o campo exibido no portal do Azure é, na verdade, o UPN (Nome UPN), que é usado em cenários B2C. Para exibir o identificador de entrada da conta local, localize o objeto user no [Gerenciador de gráficos](https://graphexplorer.cloudapp.net/). Você encontrará o mesmo problema com um usuário de conta social (ou seja, um consumidor que se inscreve com o Facebook, Google +, etc.), mas nesse caso, não há nenhum identificador de entrada.

    ![Conta local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Para um usuário de conta local, você não será capaz de editar nenhum dos campos e salvar as alterações na guia **Perfil**. Corrigiremos isso em breve.

## Restrição de exclusão de diretórios AD B2C do Azure

Você não poderá excluir um diretório AD B2C do Azure no portal do Azure.

<!---HONumber=Sept15_HO3-->