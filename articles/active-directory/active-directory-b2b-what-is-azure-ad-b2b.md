<properties
   pageTitle="Visualização da colaboração B2B do Active Directory do Azure: integração simples e segura com parceiros de nuvem | Microsoft Azure"
   description="A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="femila"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="08/09/2016"
   ms.author="femila"/>

# Visualização da colaboração B2B do AD do Azure: integração simples e segura com parceiros de nuvem

À medida que as empresas se concentram mais em seu negócio principal, a necessidade de fazer parcerias com outras empresas aumenta. As empresas precisam compartilhar recursos com facilidade e segurança (como o acesso a aplicativos corporativos) com seus parceiros para terem uma colaboração eficaz. A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos e dados corporativos usando suas identidades autogerenciadas. A colaboração AD B2B do Azure é:

- **Simples**: cada usuário do parceiro usa uma conta existente do AD do Azure ou uma que seja facilmente criada durante a aceitação do convite. Você pode fornecer a esse usuário o acesso direto ao seu aplicativo corporativo escolhido ou a um conjunto de aplicativos por meio do Painel de Acesso do Aplicativo.

- **Seguro**: o administrador controla todo o acesso aos seus aplicativos corporativos por meio de seu diretório do AD do Azure. Quando a colaboração for encerrada, os usuários parceiros poderão ser removidos
- do seu Azure AD e o acesso aos seus aplicativos será imediatamente revogado. Além disso, quando o usuário do parceiro deixar a organização do parceiro, o acesso será perdido automaticamente.

- **Direto**: as empresas parceiras que precisam acessar seus aplicativos corporativos **não precisam ter o Azure AD.** A colaboração AD B2B do Azure oferece uma experiência simples de inscrição de usuário para fornecer a esses parceiros o acesso imediato aos seus aplicativos.

Confira [a postagem no blog anunciando a visualização pública](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) e [este vídeo detalhado](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-B2B-collaboration-demo) que explica a colaboração do AD B2B do Azure.

Para obter uma comparação entre os casos de uso da colaboração B2B do Azure AD, de B2C do Azure AD e do Aplicativo Multilocatário do Azure AD, confira [Comparando recursos para o gerenciamento de identidades externas usando o Azure AD](active-directory-b2b-compare-external-identities.md).

## Artigos relacionados
Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

- [Como funciona](active-directory-b2b-how-it-works.md)
- [Passo a passo detalhado](active-directory-b2b-detailed-walkthrough.md)
- [Referência do formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo do objeto de usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações atuais da visualização](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

<!---HONumber=AcomDC_0810_2016-->