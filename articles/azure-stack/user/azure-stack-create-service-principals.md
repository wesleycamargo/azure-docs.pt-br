---
title: Criar uma entidade de serviço para o Azure Stack | Microsoft Docs
description: Descreve como criar uma entidade de serviço que pode ser usada com o controle de acesso baseado em função no Azure Resource Manager para gerenciar o acesso aos recursos.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: 3a6fa631fdf3436dc3a76817d2c0043b0407b2ce
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121546"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Fornecer aos aplicativos acesso aos recursos do Azure Stack, Criando entidades de serviço

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode atribuir um aplicativo acesso aos recursos do Azure Stack, criando um serviço principal que usa o Azure Resource Manager. Uma entidade de serviço permite que você delegar permissões específicas usando [controle de acesso baseado em função](azure-stack-manage-permissions.md).

Como prática recomendada, você deve usar as entidades de serviço para seus aplicativos. As entidades de serviço são preferíveis para executar um aplicativo usando suas próprias credenciais pelos seguintes motivos:

* Você pode atribuir permissões à entidade que são diferentes de suas próprias permissões de conta de serviço. Normalmente, as permissões de uma entidade de serviço são restritas a exatamente o que o aplicativo precisa fazer.
* Você não precisa alterar as credenciais do aplicativo se alterar sua função ou responsabilidade.
* Você pode usar um certificado para automatizar a autenticação ao executar um script autônomo.

## <a name="example-scenario"></a>Cenário de exemplo

Você tem um aplicativo de gerenciamento de configuração que precisa fazer o inventário de recursos do Azure usando o Azure Resource Manager. Você pode criar um serviço principal e atribuí-lo à função Leitor. Essa função fornece ao aplicativo acesso somente leitura aos recursos do Azure.

## <a name="getting-started"></a>Introdução

Use as etapas neste artigo como um guia para:

* Crie uma entidade de serviço para seu aplicativo.
* Registrar seu aplicativo e criar uma chave de autenticação.
* Atribua o aplicativo a uma função.

A maneira como você configurou o Active Directory para o Azure Stack determina como você pode criar uma entidade de serviço. Escolha uma das seguintes opções:

* Criar um serviço principal para [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Criar um serviço principal para [serviços de Federação do Active Directory (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

As etapas para atribuir uma entidade de serviço a uma função o mesmo para o Azure AD e o AD FS. Depois de criar a entidade de serviço, você pode [delegar permissões](azure-stack-create-service-principals.md) atribuindo-a uma função.

## <a name="create-service-principal-for-azure-ad"></a>Criar entidade de serviço do AD do Azure

Se a pilha do Azure usa o Azure AD como o repositório de identidades, você pode criar um serviço de entidade usando as mesmas etapas do Azure, usando o portal do Azure.

> [!NOTE]
> Verifique se você tem o [permissões do AD do Azure necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) antes de começar a criar uma entidade de serviço.

### <a name="create-service-principal"></a>Criar entidade de serviço

Para criar uma entidade de serviço para seu aplicativo:

1. Entre sua conta do Azure através do [portal do Microsoft Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** > **registros do aplicativo** > **adicionar**.
3. Forneça um nome e uma URL para o aplicativo. Selecione **aplicativo Web/API** ou **Nativo** para o tipo de aplicativo que você deseja criar. Depois de definir os valores, selecione **Criar**.

### <a name="get-credentials"></a>Obter credenciais

Ao fazer logon por meio de programação, use a ID para seu aplicativo e uma chave de autenticação. Para obter estes valores:

1. De **Registros do Aplicativo** no Active Directory, selecione seu aplicativo.

2. Copie a **ID do aplicativo** e armazene-a no código do aplicativo. Os aplicativos em aplicativos de exemplo usam **id do cliente** ao fazer referência à **ID do aplicativo**.

     ![ID do aplicativo para o aplicativo](./media/azure-stack-create-service-principals/image12.png)
3. Para gerar uma chave de autenticação, selecione **Chaves**.

4. Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.

> [!IMPORTANT]
> Depois de salvar a chave, a chave **valor** é exibida. Anote esse valor, porque você não pode recuperar a chave mais tarde. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

![Aviso de valor de chave para a chave salva.](./media/azure-stack-create-service-principals/image15.png)

A etapa final é [atribuindo uma função de seu aplicativo](azure-stack-create-service-principals.md).

## <a name="create-service-principal-for-ad-fs"></a>Criar entidade de serviço do AD FS

Se você implantou o Azure Stack usando o AD FS como o repositório de identidades, você pode usar o PowerShell para as seguintes tarefas:

* Crie uma entidade de serviço.
* Atribua a entidade de serviço a uma função.
* Entre usando a identidade da entidade de serviço.

Para obter detalhes sobre como criar a entidade de serviço, consulte [criar entidade de serviço do AD FS](../azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Atribuir a entidade de serviço a uma função

Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: Funções internas](../../role-based-access-control/built-in-roles.md).

> [!NOTE]
> Você pode definir o escopo da função no nível de uma assinatura, um grupo de recursos ou um recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, um aplicativo com a função de leitor para um grupo de recursos significa que o aplicativo pode ler qualquer um dos recursos no grupo de recursos.

Use as etapas a seguir como um guia para atribuir uma função a uma entidade de serviço.

1. No portal do Azure Stack, navegue até o nível de escopo que deseja atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**.

2. Selecione a assinatura para atribuir o aplicativo. Neste exemplo, a assinatura é o Visual Studio Enterprise.

     ![Selecione a assinatura do Visual Studio Enterprise para atribuição](./media/azure-stack-create-service-principals/image16.png)

3. Selecione **controle de acesso (IAM)** para a assinatura.

4. Selecione **Adicionar atribuição de função**.

5. Selecione a função que deseja atribuir ao aplicativo.

6. Procure seu aplicativo e selecione-o.

7. Selecione **OK** para finalizar a atribuição da função. Você pode ver seu aplicativo na lista de usuários atribuídos a uma função para esse escopo.

Agora que você criou uma entidade de serviço e atribuído a uma função, seu aplicativo pode acessar os recursos do Azure Stack.

## <a name="next-steps"></a>Próximas etapas

[Gerenciar permissões de usuário](azure-stack-manage-permissions.md)
