<properties
   pageTitle="Colaboração B2B do AD do Azure: como funciona | Microsoft Azure"
   description="Descreve como a colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos"
   services="active-directory"
   documentationCenter=""
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
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Visualização de colaboração B2B do AD do Azure: como funciona
A colaboração B2B do AD do Azure baseia-se em um modelo de convite e resgate. Você fornece os endereços de email das partes com as quais você deseja trabalhar, junto com os aplicativos que você deseja que elas usem. O AD do Azure envia um convite por email com um link. O usuário do parceiro segue o link e é solicitado a entrar usando sua conta do AD do Azure ou se inscrever em uma nova conta do AD do Azure.

1. O administrador convida usuários do parceiro carregando [um arquivo .csv estruturado](active-directory-b2b-references-csv-file-format.md) usando o portal do Azure.
2. O portal envia emails de convite a esses usuários do parceiro.
3. Os usuários do parceiro clicam no link no email e são solicitados a entrar usando suas credenciais corporativas (se eles já estiverem no AD do Azure) ou se inscrever como um usuário da colaboração B2B do AD do Azure.
4. Os usuários do parceiro são redirecionados para o aplicativo para o qual foram convidados, em que agora têm acesso.

## Operações de diretório
Os usuários do parceiro existem no AD do Azure como usuários externos. Isso significa que o administrador pode provisionar licenças, atribuir a associação de grupo e conceder mais acesso aos aplicativos corporativos por meio do portal do Azure ou usando o Azure PowerShell, assim como para os usuários em sua empresa.

Embora uma assinatura paga (Básico ou Premium) do AD do Azure não seja necessária para usar o B2B do AD do Azure, os locatários que têm uma assinatura paga (Básico ou Premium) do AD do Azure obtêm os seguintes benefícios adicionais:

 - Os administradores podem atribuir grupos a aplicativos, possibilitando um gerenciamento mais simples do acesso de usuário convidado.
 - A identidade visual do locatário administrador é usada para marcar os emails de convite e a experiência de resgate, fornecendo mais contexto para os usuários convidados do parceiro.

## Artigos relacionados
 Conferir nossos outros artigos sobre a colaboração B2B do Azure

 - [O que é a colaboração AD B2B do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Passo a passo detalhado](active-directory-b2b-detailed-walkthrough.md)
 - [Referência do formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
 - [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
 - [Alterações de atributo do objeto de usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Limitações atuais da visualização](active-directory-b2b-current-preview-limitations.md)
 - [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->