<properties
	pageTitle="Azure Active Directory B2C: limitações e restrições | Microsoft Azure"
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
	ms.date="07/24/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C: limitações e restrições

Há vários recursos e funcionalidades do Azure AD (Azure Active Directory) B2C que ainda não têm suporte. Muitos desses problemas conhecidos e limitações serão abordados no futuro, mas você deverá estar ciente deles se estiver criando aplicativos voltados para o consumidor usando o Azure AD B2C.

## Problemas durante a criação de locatários do AD B2C do Azure

Se você tiver problemas durante a [criação de um locatário do Azure AD B2C](active-directory-b2c-get-started.md), veja [Criar um locatário do Azure AD ou um locatário do Azure AD B2C -- problemas e resoluções](active-directory-b2c-support-create-directory.md) para obter diretrizes.

## Observação sobre cotas de locatário B2C

Por padrão, o número de usuários em um locatário do B2C é limitado a 50.000 usuários. Se você precisar aumentar a cota de seu locatário do B2C, contate o suporte.

## Problemas de identidade visual no email de verificação

O email de verificação padrão contém a identidade visual da Microsoft. Nós a removeremos no futuro. Por enquanto, você poderá removê-la usando o [recurso de identidade visual da empresa](../active-directory/active-directory-add-company-branding.md).

## Restrições em aplicativos

Os seguintes tipos de aplicativos não têm suporte atualmente no Azure AD B2C. Para obter uma descrição dos tipos de aplicativos com suporte, confira [Azure AD B2C: tipos de aplicativos](active-directory-b2c-apps.md) .

### Aplicativos de Página Única (JavaScript)

Muitos aplicativos modernos têm um front-end de SPA (Aplicativo de Página Única) escrito principalmente em JavaScript e que usa com frequência estruturas do SPA, como AngularJS, Ember.js, Durandal etc. Esse fluxo ainda não está disponível no Azure AD B2C.

### Aplicativos daemons/do lado do servidor

Os aplicativos que contêm processos de longa duração ou que operam sem a presença de um usuário também precisam encontrar uma maneira de acessar os recursos protegidos, tais como APIs Web. Esses aplicativos podem autenticar e obter tokens pelo uso da identidade do aplicativo (em vez de usar a identidade delegada do consumidor), usando o [fluxo de credenciais de cliente do OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Esse fluxo ainda não está disponível no Azure AD B2C. Portanto, os aplicativos só poderão obter tokens após um fluxo de entrada interativa do consumidor.

### APIs da Web autônomas

No Azure AD B2C, você pode [compilar uma API Web protegida usando tokens do OAuth 2.0](active-directory-b2c-apps.md#web-apis). No entanto, essa API Web só poderá receber tokens de um cliente que compartilha a mesma ID de aplicativo. Não há suporte para compilação de uma API Web que é acessada de vários clientes diferentes.

### Cadeias de API Web (Em nome de)

Muitas arquiteturas incluem uma API da Web que precisa chamar outra API da Web downstream, ambas protegidas pelo AD B2C do Azure. Este cenário é comum em clientes nativos que têm um back-end de API Web que, por sua vez, chama um serviço online da Microsoft, como a API do Graph do AD do Azure.

Este cenário de API Web encadeada pode ter suporte usando a concessão Credencial de Portador Jwt do OAuth 2.0, também conhecido como fluxo Em nome de. No entanto, o fluxo Em Nome de não está implementado atualmente no Azure AD B2C.

## Restrição em bibliotecas e SDKs

O conjunto de bibliotecas da Microsoft com suporte que funcionam com o Azure AD B2C é bastante limitado no momento. Temos suporte para aplicativos Web e serviços baseados no .NET, bem como aplicativos Web e serviços do NodeJS. Também temos uma biblioteca de cliente .NET de visualização conhecida como MSAL, que pode ser usada com o Azure AD B2C em outros aplicativos .NET e Windows.

No momento, não temos suporte da biblioteca para outras linguagens ou plataformas, incluindo iOS e Android. Se você deseja criar em uma plataforma diferente das mencionadas acima, é recomendável usar um SDK de software livre, referindo-se a nossa [Referência de protocolo de OAuth 2.0 e OpenID Connect](active-directory-b2c-reference-protocols.md) conforme necessário. OAzure AD B2C implementa OAuth e OpenID Connect, o que permite usar uma biblioteca de OAuth ou OpenID Connect genérica para integração.

Nossos tutoriais de início rápido do iOS e do Android usam bibliotecas de software livre que testamos para verificar a compatibilidade com o Azure AD B2C. Todos os tutoriais de início rápido estão disponíveis na seção de [Introdução](active-directory-b2c-overview.md#getting-started).

## Restrição em protocolos

O Azure AD B2C dá suporte a OAuth 2.0 e OpenID Connect. No entanto, nem todos os recursos e capacidades de cada protocolo foram implementados. Para entender melhor o escopo da funcionalidade de protocolo com suporte no Azure AD B2C, leia nossa [referência do protocolo OAuth 2.0 e OpenID Connect](active-directory-b2c-reference-protocols.md). Suporte a protocolo SAML e WS-Fed não está disponível.

## Restrição em tokens

Muitos dos tokens emitidos pelo Azure AD B2C são implementados, como Tokens da Web JSON ou JWTs. No entanto, nem todas as informações contidas no JWTs (conhecidas como "declarações") são exatamente como deveriam ser ou não existem. Alguns exemplos incluem "sub" e as declarações de "preferred\_username". Como os valores, o formato ou o significado de declarações se alteram ao longo do tempo, os tokens para as políticas existentes permanecerão inalterados. Você pode utilizar seus valores em aplicativos de produção. À medida que os valores forem alterados, lhe daremos a oportunidade de configurar essas alterações para cada uma de suas políticas. Para entender melhor os tokens emitidos atualmente pelo serviço do AD B2C do Azure, leia nossa [referência de token](active-directory-b2c-reference-tokens.md).

## Restrição em grupos aninhados

Associações de grupo aninhado não têm suporte nos locatários Azure AD B2C . Não estamos planejando adicionar esse recurso.

## Restrição de recurso de consulta diferencial na API do Graph do Azure AD

O [recurso de consulta diferencial na API do Graph do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) não tem suporte em locatários do Azure AD B2C. Esse é nosso roteiro de longo prazo.

## Problemas de gerenciamento de usuário no Portal Clássico do Azure

Recursos de B2C são acessíveis no Portal do Azure. No entanto, você pode usar o Portal Clássico do Azure para acessar outros recursos de locatário, incluindo o gerenciamento de usuários. Atualmente, há alguns problemas conhecidos com o gerenciamento de usuários (a guia **Usuários**) no portal clássico do Azure:

- Para um usuário de conta local (ou seja, um consumidor que se inscreve com um endereço de email e senha ou um nome de usuário e senha), o campo **Nome do Usuário** não corresponde ao identificador de entrada (endereço de email ou nome de usuário) usado na inscrição. Isso ocorre porque o campo exibido no Portal Clássico do Azure é, na verdade, o UPN (Nome UPN), que não é usado em cenários B2C. Para exibir o identificador de entrada da conta local, localize o objeto do usuário no [Gerenciador do Graph](https://graphexplorer.cloudapp.net/). Você encontrará o mesmo problema com um usuário de conta social (ou seja, um consumidor que se inscreve com o Facebook, Google +, etc.), mas nesse caso, não há nenhum identificador de entrada.

    ![Conta local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Para um usuário de conta local, você não será capaz de editar nenhum dos campos e salvar as alterações na guia **Perfil**.

## Problemas com a redefinição de senha iniciada pelo administrador no Portal Clássico do Azure

Se você redefinir a senha para um consumidor baseado em conta local no Portal Clássico do Azure (o comando **Redefinir Senha** na guia **Usuários**), o consumidor não poderá alterar a senha no próximo logon, se for utilizada uma política de logon ou de inscrição, e terá seus aplicativos bloqueados. Como alternativa, use o [API do Graph do Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md) para redefinir a senha do consumidor (sem data de vencimento de senha) ou usar uma política de Logon em vez de uma política de Inscrição ou de Logon.

## Problemas com a criação de um atributo personalizado

Um [atributo personalizado adicionado no portal do Azure](active-directory-b2c-reference-custom-attr.md) não é criado imediatamente no seu locatário B2C. Você terá que usar o atributo personalizado em pelo menos uma de suas políticas para que ele seja criado no locatário do B2C e disponibilizado por meio da API do Graph.

## Problemas de verificação de um domínio no Portal Clássico do Azure

Atualmente, você não pode verificar um domínio com êxito no [Portal Clássico do Azure](https://manage.windowsazure.com/).

## Problemas com e entrada com a política MFA nos navegadores Safari

Solicitações para políticas de entrada (com MFA ativado) falham intermitentemente em navegadores Safari com erros HTTP 400 (solicitação incorreta). Isso é devido aos baixos limites de tamanho de cookie do Safari. Há duas soluções alternativas para esse problema:

- Use a "política de inscrição ou entrada" em vez da "política de entrada".
- Reduza o número de **declarações de aplicativo** solicitadas na sua política.

<!---HONumber=AcomDC_0727_2016-->