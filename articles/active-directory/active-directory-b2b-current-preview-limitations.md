<properties
   pageTitle="Limitações atuais da visualização para a colaboração B2B do Active Directory do Azure | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Limitações atuais da visualização para a colaboração B2B do AD do Azure (Active Directory do Azure)

- O MFA (autenticação multifator) não tem suporte em usuários externos. Por exemplo, se a Contoso tiver o MFA, mas a Organização do Parceiro não, os usuários da Organização do Parceiro não podem ter permissão ao MFA por meio da colaboração B2B.
- Convites são possíveis apenas por meio de CSV; não há suporte ao acesso de API e a convites individuais.
- Somente Administradores Globais do AD do Azure podem carregar arquivos .csv.
- Atualmente, não há suporte a convites para endereços de email do consumidor (como hotmail.com, Gmail.com ou comcast.net).
- O acesso de usuário externo aos aplicativos locais não é testado.
- Os usuários externos não são automaticamente limpos quando o usuário real é excluído de seu diretório.
- Não há suporte a convites para DLs.
- Um máximo de 2.000 registros pode ser carregado por meio do CSV.

## Artigos relacionados
Confira nossos outros artigos sobre a colaboração B2B do Azure:

- [O que é a colaboração AD B2B do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Passo a passo detalhado](active-directory-b2b-detailed-walkthrough.md)
- [Referência do formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo do objeto de usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)

<!---HONumber=Nov15_HO1-->