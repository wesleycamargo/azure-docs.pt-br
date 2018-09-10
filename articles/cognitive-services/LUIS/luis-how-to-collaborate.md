---
title: Colaborar com outros colaboradores em aplicativos LUIS no Azure | Microsoft Docs
description: Saiba como colaborar com outros colaboradores em aplicativos LUIS (Reconhecimento vocal).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 99f37cb6dc5e05fc5eb4bde09685435ee57fecc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397768"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Como gerenciar autores e colaboradores 

Você pode colaborar com outras pessoas em seu aplicativo LUIS. 

## <a name="owner-and-collaborators"></a>Proprietário e colaboradores

Um aplicativo tem um único autor, mas pode ter vários colaboradores. 

## <a name="add-collaborator"></a>Adicionar colaborador

Para permitir que os colaboradores editem seu aplicativo LUIS, na página **Configurações** do aplicativo LUIS, insira o email do colaborador e clique em **Adicionar colaborador**. Os colaboradores podem entrar e editar o aplicativo LUIS ao mesmo tempo em que você está trabalhando no aplicativo.

![Adicionar colaborador](./media/luis-how-to-collaborate/add-collaborator.png)

## <a name="transfer-of-ownership"></a>Transferência de propriedade

Embora atualmente o LUIS não dê suporte à transferência de propriedade, você pode exportar seu aplicativo e outro usuário do LUIS pode importá-lo. Pode haver pequenas diferenças nas pontuações do LUIS entre os dois aplicativos. 

## <a name="azure-active-directory-tenant-user"></a>Usuário de locatário do Azure Active Directory

LUIS usa o fluxo de autorização padrão do Azure Active Directory (Azure AD). 

O administrador do locatário deverá trabalhar diretamente com o usuário que precisa de ter o acesso concedido para usar LUIS no Azure AD. 

Primeiro, o usuário faz logon no LUIS e verá a caixa de diálogo pop-up que necessita de aprovação do administrador. O usuário entra em contato com o administrador do locatário antes de continuar. 

Segundo, o administrador do locatário faz logon no LUIS e verá uma caixa de diálogo de pop-up de fluxo de autorização. Esta é a caixa de diálogo que o administrador deve conceder permissão para o usuário. Depois que o administrador aceita a permissão, o usuário pode continuar com o LUIS.

Se o administrador do locatário não fizer logon no LUIS, o administrador pode acessar [consentimento](https://account.activedirectory.windowsazure.com/r#/applications) para LUIS. 

![Permissão do Azure Active Directory pelo website do aplicativo](./media/luis-how-to-account-settings/tenant-permissions.png)

Se o administrador do locatário deseja que somente determinados usuários usem o LUIS, consulte este [blog de identidade](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Contas de usuários com vários emails para colaboradores

Se você adicionar colaboradores a um aplicativo LUIS, você está especificando o endereço de email exato que um colaborador necessita para usar LUIS como colaborador. Enquanto o Azure Active Directory (AD do Azure) permite que um único usuário tenha mais de uma conta de email usada alternadamente, LUIS exige que o usuário faça logon com o endereço de email especificado na lista do colaborador.