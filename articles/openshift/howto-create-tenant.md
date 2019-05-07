---
title: Criar um locatário do AD do Azure para Azure Red Hat OpenShift | Microsoft Docs
description: Aqui está como criar um locatário do Azure Active Directory (Azure AD) para hospedar o cluster do Microsoft Azure Red Hat OpenShift.
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 3c7f0b263cc775b7f9fec62e4d5388250e5124fb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079114"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Criar um locatário do AD do Azure para Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift requer um [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) locatário no qual criar o cluster. Um *locatário* é uma instância dedicada do AD do Azure que um desenvolvedor de aplicativo ou organização recebe quando ele criam uma relação com a Microsoft inscrevendo-se para o Azure, Microsoft Intune ou Microsoft 365. Cada locatário do Azure AD é distinto e separado de outros Azure ADS locatários e tem seu próprio trabalho e identidades de escola e registros do aplicativo.

Se você ainda não tiver um locatário do AD do Azure, siga estas instruções para criar um.

## <a name="create-a-new-azure-ad-tenant"></a>Criar um novo locatário do Azure Active Directory

Para criar um locatário:

1. Entrar para o [portal do Azure](https://portal.azure.com/) usando a conta que você deseja associar ao seu cluster do Azure Red Hat OpenShift.
2. Abra o [folha Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) para criar um novo locatário (também conhecido como uma nova *Azure Active Directory*).
3. Fornecer um **nome da organização**.
4. Fornecer um **nome de domínio inicial**. Isso terá *onmicrosoft.com* anexado a ele. Você pode reutilizar o valor para *nome da organização* aqui.
5. Escolha um país ou região onde o locatário será criado.
6. Clique em **Criar**.
7. Depois que seu locatário do AD do Azure é criado, selecione o **clique aqui para gerenciar seu novo diretório** link. O novo nome do locatário deve ser exibido no canto superior direito do portal do Azure:  

    ![Captura de tela do portal mostrando o nome do locatário no canto superior direito][tenantcallout]  

8. Anote o *ID do locatário* para que mais tarde, você pode especificar onde criar o cluster do Azure Red Hat OpenShift. No portal, você agora verá a folha de visão geral do Azure Active Directory para o novo locatário. Selecione **propriedades** e copie o valor de sua **ID de diretório**. Vamos nos referir a esse valor como o `tenant id` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Recursos

Fazer check-out [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) para obter mais informações sobre [locatários do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Próximas etapas

Saiba como criar uma entidade de serviço, gerar uma URL de retorno de chamada de autenticação e o segredo do cliente e criar um novo usuário do Active Directory para testar aplicativos em seu cluster do Azure Red Hat OpenShift.

[Criar um objeto de aplicativo do Azure AD e o usuário](howto-aad-app-configuration.md)