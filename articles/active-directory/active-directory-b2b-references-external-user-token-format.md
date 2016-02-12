<properties
   pageTitle="Formato do token de usuário externo para a visualização da colaboração B2B do Active Directory do Azure | Microsoft Azure"
   description="A B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos"
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Visualização da colaboração B2B do AD do Azure: formato de token de usuário externo

As declarações de um token padrão do AD do Azure são descritas no artigo [Tipos de declaração e token com suporte](active-directory-token-and-claims.md) em azure.microsoft.com.

As declarações que são diferentes para um usuário externo da colaboração B2B autenticado são as seguintes:<br/> - **OID:** a ID de objeto do locatário de recursos<br/> - **TID**: a ID de locatário do locatário de recursos<br/> - **Emissor**: este é o locatário de recursos<br/> - **IDP**: este é o locatário inicial do usuário<br/> - **AltSecId**: esta é a ID de segurança alternativa, que é opaca para você<br/>

## Artigos relacionados
Confira nossos outros artigos sobre a colaboração B2B do Azure:

- [O que é a colaboração AD B2B do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Passo a passo detalhado](active-directory-b2b-detailed-walkthrough.md)
- [Referência do formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Alterações de atributo do objeto de usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações atuais da visualização](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=AcomDC_0204_2016-->