---
title: Gerenciar suas configurações de conta em LUIS | Microsoft Docs
description: Use o website do LUIS para gerenciar suas configurações de conta.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/08/2018
ms.author: diberry
ms.openlocfilehash: 963a7f8c196702ea899ddfe31e6187a15eb5f683
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223201"
---
# <a name="manage-account-and-authoring-key"></a>Gerenciar conta e chave de criação
As duas partes principais de informação para uma conta LUIS são a conta de usuário e a chave de criador. Suas informações de logon são gerenciadas em [account.microsoft.com](https://account.microsoft.com). A chave de criação é gerenciada no site do [LUIS](luis-reference-regions.md) na página **Configurações**. 

## <a name="authoring-key"></a>Chave de criação

Essa chave única de criação específica à região, na página **Configurações**, permite que você crie todos os aplicativos no site do [LUIS](luis-reference-regions.md) assim como nas [APIs de criação](https://aka.ms/luis-authoring-api). Para sua comodidade, a chave de criador é permitida para fazer um número [limitado](luis-boundaries.md) de consultas de ponto de extremidade a cada mês. 

![Página Configurações de LUIS](./media/luis-how-to-account-settings/account-settings.png)

A chave de criador é usada para todos os seus aplicativos, bem como quaisquer aplicativos que você listou como colaborador.

## <a name="authoring-key-regions"></a>Regiões da chave de criador
A chave de criador é específica para a [região de autoria](luis-reference-regions.md#publishing-regions). A chave não funciona em uma região diferente. 

## <a name="reset-authoring-key"></a>Redefinir a chave de criador
Se a chave de criador estiver comprometida, redefina a chave. A chave é redefinida em todos os seus aplicativos no website de [LUIS](luis-reference-regions.md). Se você criar seus aplicativos por meio de APIs de criador, você precisa alterar o valor de `Ocp-Apim-Subscription-Key` para a nova chave. 

## <a name="delete-account"></a>Exclui a conta
Consulte [Armazenamento e remoção de dados](luis-concept-data-storage.md#accounts) para obter informações sobre quais dados são excluídos quando você exclui sua conta. 

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


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre sua [chave de criador](luis-concept-keys.md#authoring-key). 

