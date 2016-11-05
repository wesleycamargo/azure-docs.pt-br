---
title: Personalizando as declarações emitidas no token SAML para aplicativos pré-integrados no Active Directory do Azure | Microsoft Docs
description: Saiba como personalizar as declarações emitidas no token SAML para aplicativos pré-integrados no Active Directory do Azure
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: asmalser

---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Personalizando as declarações emitidas no token SAML para aplicativos pré-integrados no Active Directory do Azure
Hoje, o Active Directory do Azure oferece suporte a milhares de aplicativos pré-integrados na Galeria de Aplicativos Azure AD, incluindo mais de 150 que oferecem suporte a logon único usando o protocolo SAML 2.0. Quando um usuário autentica em um aplicativo por meio do Azure AD usando SAML, o Azure AD envia um token ao aplicativo (por meio de um redirecionamento HTTP 302) que o aplicativo valida e usa para fazer o logon do usuário em vez de solicitar um nome de usuário e senha. Esses tokens SAML contêm informações sobre o usuário conhecidas como "declarações".

Em linguagem de identificação, uma "declaração" são informações que um provedor de identidade declara sobre um usuário dentro do token que ele emite para esse usuário. Em um [token SAML](http://en.wikipedia.org/wiki/SAML_2.0), esses dados geralmente estão contidos na Declaração de Atributo SAML, e a ID exclusiva do usuário é geralmente representada no Subject SAML.

Por padrão, o Azure AD emitirá um token SAML para seu aplicativo contendo uma declaração do NameIdentifier, com um valor de nome de usuário no Azure AD (esse valor identifica exclusivamente o usuário). O token SAML também contém declarações adicionais com o endereço de email, nome e sobrenome do usuário.

Para exibir ou editar as declarações emitidas no token SAML para o aplicativo, abra o registro do aplicativo no Portal de Gerenciamento do Azure e selecione a guia **Atributos** sob o aplicativo.

![][1]

Há duas razões possíveis para você precisar editar as declarações emitidas no token SAML: • O aplicativo foi escrito para exigir um conjunto diferente de URIs de declaração ou valores de declaração • Seu aplicativo foi implantado de uma forma que requer que a declaração NameIdentifier seja algo diferente do nome de usuário (conhecido como UPN) armazenado no Active Directory do Azure. 

Você pode editar qualquer um dos valores de declaração padrão selecionando o ícone em forma de lápis que aparece à direita sempre que você passa o mouse sobre uma das linhas na tabela de atributos do token SAML. Você também pode remover declarações (exceto NameIdentifier) usando o ícone **X** e adicionar novas declarações usando o botão **Adicionar atributo de usuário**.

## <a name="editing-the-nameidentifier-claim"></a>Editando a declaração NameIdentifier
Para resolver o problema no qual o aplicativo foi implantado usando um nome de usuário diferente, clique no ícone de lápis próximo à declaração NameIdentifier. Isso apresenta uma caixa de diálogo com várias opções diferentes:

![][2]

No menu **Valor do Atributo**, selecione **user.mail** para definir a declaração NameIdentifier como o endereço de email do usuário no diretório ou selecione **user.onpremisessamaccountname** para definir como o Nome de Conta SAM do usuário sincronizado do Azure AD local. 

Você também pode usar a função ExtractMailPrefix() especial para remover o sufixo do domínio do endereço de email ou do nome UPN, resultando na transmissão apenas da primeira parte do nome de usuário (por exemplo, "diogomartins" em vez de diogomartins@contoso.com).

![][3]

## <a name="adding-claims"></a>Adicionando declarações
Ao adicionar uma nova declaração, você pode especificar o nome do atributo (que não precisa seguir exatamente um padrão URI, de acordo com a especificação SAML), bem como definir o valor para qualquer atributo de usuário armazenados no diretório.

![][4]

Por exemplo, se você precisar enviar o departamento ao qual o usuário pertence em sua organização como uma declaração (por exemplo, vendas), insira qualquer valor de declaração esperado pelo aplicativo e selecione **user.department** como o valor.

Se não houver um valor armazenado para um determinado usuário e um atributo selecionado, essa declaração com não será emitida no token.

**Observação:** há suporte apenas para **user.onpremisesecurityidentifier** e **user.onpremisesamaccountname** durante a sincronização de dados de usuário do Active Directory local com a [ferramenta Azure AD Connect](active-directory-aadconnect.md).

## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](active-directory-saas-custom-apps.md)
* [Solução de problemas de logon único baseado em SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png



<!--HONumber=Oct16_HO2-->


