---
title: "Características dos diretórios do Azure Active Directory | Microsoft Docs"
description: "Gerenciar seus diretórios do Azure Active Directory entendendo os diretórios como recursos totalmente independentes"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 5ec00d5e8380f121dd9302cf08a0708c530aab9b
ms.contentlocale: pt-br
ms.lasthandoff: 03/01/2017


---
# <a name="understand-how-multiple-azure-active-directory-directories-interact"></a>Entender como interagem vários diretórios do Azure Active Directory
No Active Directory do Azure (Azure AD), cada diretório é um recurso completamente independente: um par completo e logicamente independente de outros diretórios que você gerencia. Não há nenhuma relação pai-filho entre os diretórios. Essa independência entre diretórios inclui independência de recursos, independência administrativa e independência de sincronização.

## <a name="resource-independence"></a>Independência de recursos
Se você criar ou excluir um recurso em um diretório, ele não afeta nenhum recurso em outro diretório, com a exceção parcial de usuários externos, como descrito abaixo. Se você usar um domínio personalizado "contoso.com" com um diretório, ele não pode ser usado com nenhum outro diretório.

## <a name="administrative-independence"></a>Independência administrativa
Se um usuário não administrativo do diretório "Contoso" criar um diretório "Teste", então:

* Por padrão, o usuário que cria um diretório é adicionado como um usuário externo nesse novo diretório e recebe a função de administrador global nesse diretório.
* Os administradores do diretório "Contoso" não têm privilégios administrativos diretos para o diretório "Teste", a menos que um administrador de "Teste" conceda especificamente esses privilégios a eles. Os administradores do "Contoso" podem controlar o acesso ao diretório "Teste" se controlarem a conta de usuário que criou "Teste".
* Se você alterar (adicionar ou remover) uma função de administrador para um usuário em um diretório, a alteração não afeta nenhuma função de administrador que o usuário pode ter em outro diretório.

## <a name="synchronization-independence"></a>Independência de sincronização
Você pode configurar cada diretório do Azure AD independentemente para que os dados sejam sincronizados a partir de uma única instância de:

* A ferramenta de sincronização de diretório (DirSync), para sincronizar dados com uma única floresta do AD.
* Azure Active Directory Connector para o Forefront Identity Manager, para sincronizar dados com uma ou mais florestas locais e/ou fontes de dados não Azure AD.

## <a name="add-an-azure-ad-directory"></a>Adicionar um diretório do AD do Azure
Para adicionar um diretório do Active Directory do Azure no portal de clássico do Azure, selecione a extensão do Active Directory do Azure à esquerda e toque em **Adicionar**.

> [!NOTE]
> Observe também que, ao contrário de outros recursos do Azure, seus diretórios não são recursos filho de uma assinatura do Azure. Se você cancelar ou permitir que a assinatura do Azure expire, ainda poderá acessar os dados de diretório usando o Azure PowerShell, a API do Graph do Azure ou outras interfaces, como o Centro de Administração do Office 365. Você também pode associar outra assinatura ao diretório.
>
>

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral dos problemas de licenciamento do AD do Azure e as práticas recomendadas, consulte [O que é o licenciamento do Active Directory do Azure?](active-directory-licensing-what-is.md).

