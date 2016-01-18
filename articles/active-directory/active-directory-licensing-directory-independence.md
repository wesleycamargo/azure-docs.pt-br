<properties
   pageTitle="Adicione e gerencie vários diretórios do Active Directory do Azure | Microsoft Azure"
   description="Instruções e práticas recomendadas para adicionar e gerenciar diretórios do Active Directory do Azure, explicando os diretórios como recursos totalmente independentes"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/01/2015"
   ms.author="curtand"/>

# Adicionar e gerenciar vários diretórios do Active Directory do Azure

No Active Directory do Azure (Azure AD), cada diretório é um recurso completamente independente: um par completo e logicamente independente de outros diretórios que você gerencia. Não há nenhuma relação pai-filho entre os diretórios. Essa independência entre diretórios inclui independência de recursos, independência administrativa e independência de sincronização.

##Independência de recursos

Se você criar ou excluir um recurso em um diretório, ele não afeta nenhum recurso em outro diretório, com a exceção parcial de usuários externos, como descrito abaixo. Se você usar um domínio personalizado "contoso.com" com um diretório, ele não pode ser usado com nenhum outro diretório.

##Independência administrativa

Se um usuário não administrativo do diretório "Contoso" criar um diretório "Teste", então: - por padrão, o usuário que cria um diretório é adicionado como um usuário externo nesse novo diretório e atribuído à função de administrador global nesse diretório. -Os administradores do diretório "Contoso" não têm privilégios administrativos diretos para o diretório "Teste", a menos que um administrador do "Teste" conceda especificamente a eles esses privilégios. Os administradores do "Contoso" podem controlar o acesso ao diretório "Teste" se controlarem a conta de usuário que criou o "Teste". - Se você alterar (adicionar ou remover) uma função de administrador para um usuário em um diretório, a alteração não afetará nenhuma função de administrador que o usuário possa ter em outro diretório.

##Independência de sincronização

Você pode configurar cada diretório do AD do Azure independentemente para obter dados sincronizados de uma única instância de: - ferramenta de sincronização de diretório (DirSync), para sincronizar dados com uma única floresta AD. - O Active Directory Connector do Azure para o Forefront Identity Manager, para sincronizar dados com um ou mais florestas locais e/ou fontes de dados do AD não Azure.

##Adicionar um diretório do AD do Azure

Para adicionar um diretório do Active Directory do Azure no portal de clássico do Azure, selecione a extensão do Active Directory do Azure à esquerda e toque em **Adicionar**.

> [AZURE.NOTE]Observe também que, ao contrário de outros recursos do Azure, seus diretórios não são recursos filho de uma assinatura do Azure. Se você cancelar ou permitir que a assinatura do Azure expire, ainda poderá acessar os dados de diretório usando o Azure PowerShell, o Azure Graph API ou outras interfaces, como o Centro de Administração do Office 365. Você também pode associar outra assinatura ao diretório.

Para obter uma visão geral dos problemas de licenciamento do AD do Azure e as práticas recomendadas, consulte [O que é o licenciamento do Active Directory do Azure?](active-directory-licensing-what-is.md).

<!---HONumber=AcomDC_1203_2015-->