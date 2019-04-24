---
title: Problemas ao entrar em um aplicativo na galeria configurado para logon único federado | Microsoft Docs
description: Diretrizes para os erros específicos ao entrar em um aplicativo configurado para o logon único federado baseado em SAML com Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: celested
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 623d684f701df8b1a7c4b84a2bd3840f039ad174
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292207"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemas ao entrar em um aplicativo na galeria configurado para logon único federado

Para solucionar os problemas de conexão abaixo, é recomendável que você siga estas sugestões para obter melhor diagnóstico e automatizar as etapas de resolução:

- Instalar o [extensão de navegador proteger meus aplicativos](access-panel-extension-problem-installing.md) para ajudar o Azure Active Directory (Azure AD) para fornecer melhor diagnóstico e resoluções ao usar o teste de experiência no portal do Azure.
- Reproduza o erro usando a experiência de teste na página de configuração de aplicativo no portal do Azure. Saiba mais sobre [SAML depurar aplicativos baseados em únicos logon](../develop/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Aplicativo não encontrado no diretório

*Erro AADSTS70001: Aplicativo com identificador ' https:\//contoso.com' não foi encontrado no diretório*.

**Possível causa**

O `Issuer` atributo enviado do aplicativo para o Azure AD na solicitação SAML não corresponde ao valor de identificador que está configurado para o aplicativo no Azure AD.

**Resolução**

Certifique-se de que o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. Se você usar o [testando experiência](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão My de navegador proteger aplicativos, você não precisa manualmente, siga estas etapas.

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador**.

1.  Abra o **extensão do Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Tipo de **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o **Azure Active Directory** item.

1.  Selecione **aplicativos empresariais** no menu de navegação à esquerda do Active Directory do Azure.

1.  Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

    Se você não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos**.

1.  Selecione o aplicativo para o qual você deseja configurar o logon único.

1.  Depois que o carregamento do aplicativo, abra **configuração SAML básico**. Verifique se o valor na caixa de texto identificador corresponde o valor para o valor do identificador exibido no erro.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não coincide com os endereços de resposta configurados para o aplicativo

*Erro AADSTS50011: O endereço de resposta ' https:\//contoso.com' não coincide com os endereços de resposta configurados para o aplicativo*

**Possível causa**

O `AssertionConsumerServiceURL` valor na solicitação SAML não corresponde o valor de URL de resposta ou o padrão configurado no Azure AD. O `AssertionConsumerServiceURL` valor na solicitação SAML é a URL que você vê no erro.

**Resolução**

Certifique-se de que o `AssertionConsumerServiceURL` valor na solicitação SAML corresponde ao valor de URL de resposta configurado no Azure AD. Se você usar o [testando experiência](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão My de navegador proteger aplicativos, você não precisa manualmente, siga estas etapas.

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador**.

1.  Abra o **extensão do Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Tipo de **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o **Azure Active Directory** item.

1.  Selecione **aplicativos empresariais** no menu de navegação à esquerda do Active Directory do Azure.

1.  Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

    Se você não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos**.

1.  Selecione o aplicativo para o qual você deseja configurar o logon único.

1.  Depois que o carregamento do aplicativo, abra **configuração SAML básico**. Verifique ou atualize o valor na caixa de texto URL de resposta para corresponder a `AssertionConsumerServiceURL` valor na solicitação SAML.    
    
Depois que você atualizou o valor de URL de resposta no Azure AD, e ele corresponde ao valor enviado pelo aplicativo na solicitação SAML, você poderá entrar aplicativo.

## <a name="user-not-assigned-a-role"></a>Usuário não atribuído a uma função

*Erro AADSTS50105: O usuário conectado ' brian\@contoso.com' não está atribuído a uma função para o aplicativo*.

**Possível causa**

O usuário não teve acesso concedido para o aplicativo no Azure AD.

**Resolução**

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo. Se você usar o [testando experiência](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão My de navegador proteger aplicativos, você não precisa manualmente, siga estas etapas.

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global**.

1.  Abra o **extensão do Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Tipo de **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o **Azure Active Directory** item.

1.  Selecione **aplicativos empresariais** no menu de navegação à esquerda do Active Directory do Azure.

1.  Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

    Se você não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos**.

1.  Na lista de aplicativos, selecione aquele que você deseja atribuir um usuário.

1.  Depois que o aplicativo é carregado, selecione **usuários e grupos** no menu de navegação à esquerda do aplicativo.

1.  Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

1.  Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

1. No **pesquisar por nome ou endereço de email** caixa de pesquisa, digite o nome completo ou endereço de email do usuário que você deseja adicionar.

1. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado de foto de perfil do usuário ou o logotipo para adicionar o usuário para o **selecionados** lista.

1. **Opcional:** Se você quiser **adicionar mais de um usuário**, digite outro nome completo ou endereço nos envie um email a **pesquisar por nome ou endereço de email** caixa de pesquisa e, em seguida, clique na caixa de seleção para adicionar o usuário para o **selecionados**  lista.

1. Quando você terminar de selecionar usuários, clique no **selecionar** botão para adicioná-los à lista de usuários e grupos devem ser atribuídos ao aplicativo.

1. **Opcional:** Clique o **Selecionar função** seletor na **Adicionar atribuição** painel para selecionar uma função para atribuir aos usuários que você selecionou.

1. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

Após um curto período de tempo, os usuários que você selecionou poderão iniciar esses aplicativos usando os métodos descritos na seção de descrição da solução.

## <a name="not-a-valid-saml-request"></a>Não é uma solicitação SAML válida

*Erro AADSTS75005: a solicitação não é uma mensagem de protocolo Saml2 válida.*

**Possível causa**

Azure AD não oferece suporte a solicitação SAML enviada pelo aplicativo para logon único. Alguns problemas comuns são:

-   Campos obrigatórios ausentes na solicitação SAML
-   Método codificado de solicitação SAML

**Resolução**

1. Capture solicitação SAML. Siga o tutorial [como depurar baseado em SAML SSO para aplicativos no Azure AD](../develop/howto-v1-debug-saml-sso-issues.md) para aprender a capturar a solicitação SAML.

1. Entre em contato com o fornecedor do aplicativo e compartilhe as seguintes informações:

   -   Solicitação SAML

   -   [Requisitos de protocolo SAML de logon único do Azure AD](../develop/single-sign-on-saml-protocol.md)

O fornecedor do aplicativo deve validar que eles dão suporte a implementação de SAML do Azure AD para logon único.

## <a name="misconfigured-application"></a>Aplicativo configurado incorretamente

*Error AADSTS650056: Aplicativos mal configurados. Isso pode ser devido a um dos seguintes: O cliente não listou qualquer permissão para 'AAD Graph' as permissões solicitadas no registro de aplicativo do cliente. Ou, o administrador não aceitou o locatário. Ou então, verifique o identificador do aplicativo na solicitação para garantir que ele corresponda ao identificador de aplicativo de cliente configurado. Entre em contato com seu administrador para corrigir a configuração ou consentir em nome do locatário.* .

**Possível causa**

O `Issuer` atributo enviado do aplicativo para o Azure AD na solicitação SAML não corresponde ao valor do identificador configurado para o aplicativo no Azure AD.

**Resolução**

Certifique-se de que o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. Se você usar o [testando experiência](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão My de navegador proteger aplicativos, você não precisa manualmente, siga estas etapas:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador**.

1.  Abra o **extensão do Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Tipo de **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o **Azure Active Directory** item.

1.  Selecione **aplicativos empresariais** no menu de navegação à esquerda do Active Directory do Azure.

1.  Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

    Se você não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos**.

1.  Selecione o aplicativo para o qual você deseja configurar o logon único.

1.  Depois que o carregamento do aplicativo, abra **configuração SAML básico**. Verifique se o valor na caixa de texto identificador corresponde o valor para o valor do identificador exibido no erro.


## <a name="certificate-or-key-not-configured"></a>Chave ou certificado não configurado

*Erro AADSTS50003: nenhuma chave de assinatura configurada.*

**Possível causa**

O objeto de aplicativo está corrompido e o Azure AD não reconhece o certificado configurado para o aplicativo.

**Resolução**

Para excluir e criar um novo certificado, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador**.

1. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

1. Tipo de **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o **Azure Active Directory** item.

1. Selecione **aplicativos empresariais** no menu de navegação à esquerda do Active Directory do Azure.

1. Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

    Se você não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos**.

1. Selecione o aplicativo para o qual deseja configurar o logon único

1. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

1. Selecione **criar novo certificado** sob o **certificado de autenticação SAML** seção.

1. Selecione a data de validade e, em seguida, clique em **salvar**.

1. Marque **Tornar o novo certificado ativo** para substituir o certificado ative. Em seguida, clique em **Salvar** na parte superior do painel e aceite para ativar o certificado de substituição.

1. Na seção **Certificado de Autenticação SAML**, clique em **remover** para remover o certificado **Não Usado**.

## <a name="saml-request-not-present-in-the-request"></a>A solicitação SAML não está presente na solicitação

*Erro AADSTS750054: SAMLRequest ou SAMLResponse deve estar presente como parâmetro de cadeia de caracteres na solicitação HTTP para a Associação de redirecionamento SAML.*

**Possível causa**

O Azure AD não pôde identificar a solicitação SAML dentro dos parâmetros de URL na solicitação HTTP. Isso pode acontecer se o aplicativo não estiver usando o redirecionamento HTTP ao enviar a solicitação SAML para o Azure AD de associação.

**Resolução**

O aplicativo precisa para enviar a solicitação SAML codificada no cabeçalho de localização usando HTTP redirecionar a associação. Para obter mais informações sobre como implementá-la, leia a seção sobre Associação de redirecionamento HTTP no [Documento de especificação de protocolo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD está enviando o token para um ponto de extremidade incorreto

**Possível causa**

Durante o logon único, se a solicitação de entrada não contiver uma URL de resposta explícito (URL de serviço do consumidor de declaração) e em seguida, o Azure AD irá selecionar qualquer um dos configurado confie URLs para o aplicativo. Mesmo se o aplicativo tem uma URL de resposta explícito configurado, o usuário pode ser redirecionada https://127.0.0.1:444. 

Quando o aplicativo foi adicionado como um aplicativo inexistente na galeria, o Azure Active Directory criou essa URL de resposta como um valor padrão. Esse comportamento mudou e o Azure Active Directory não adiciona mais essa URL por padrão. 

**Resolução**

Exclua as URLs de resposta não utilizados, configurados para o aplicativo.

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador**.

2.  Abra o **extensão do Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Tipo de **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o **Azure Active Directory** item.

4.  Selecione **aplicativos empresariais** no menu de navegação à esquerda do Active Directory do Azure.

5.  Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

    Se você não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos**.

6.  Selecione o aplicativo para o qual você deseja configurar o logon único.

7.  Depois que o carregamento do aplicativo, abra **configuração SAML básico**. No **URL de resposta (URL de serviço do consumidor de declaração)**, delete não utilizado ou URLs de resposta padrão criado pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as declarações SAML enviadas para um aplicativo

Para saber como personalizar as declarações de atributo SAML enviadas ao seu aplicativo, consulte [mapeamento de declarações no Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Próximas etapas

[Como depurar o logon único baseado em SAML em aplicativos no do Azure AD](../develop/howto-v1-debug-saml-sso-issues.md)
