---
title: Criar um novo pacote de acesso no gerenciamento de direitos do AD do Azure (visualização) – Azure Active Directory
description: Saiba como criar um novo pacote de acesso de recursos que você deseja compartilhar no gerenciamento de direitos do Active Directory do Azure (visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad6570a3f30e40e4074502a8ce85bf739f58d3f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866432"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Criar um novo pacote de acesso no gerenciamento de direitos do AD do Azure (visualização)

> [!IMPORTANT]
> Gerenciamento de direitos do Active Directory (Azure AD) do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um pacote de acesso permite que você faça uma configuração única de recursos e as políticas que administra automaticamente acesso durante a vida útil do pacote de acesso. Este artigo descreve como criar um novo pacote de acesso.

## <a name="overview"></a>Visão geral

Todos os pacotes de acesso devem ser colocados em um contêiner chamado de catálogo. Um catálogo define quais recursos você pode adicionar ao seu pacote de acesso. Se você não especificar um catálogo, colocará seu pacote de acesso para o catálogo geral. No momento, você não pode mover um pacote existente de acesso a um catálogo diferente.

Todos os pacotes de acesso devem ter pelo menos uma política. As políticas especificam quem pode solicitar o pacote de acesso e também as configurações de aprovação e expiração. Quando você cria um novo pacote de acesso, você pode criar uma política inicial para os usuários no diretório, para que os usuários não no diretório, para que atribuições diretas administrador apenas, ou você pode optar por criar a política mais tarde.

O diagrama a seguir mostra o processo de alto nível para criar um novo pacote de acesso.

![Criar um processo de pacote de acesso](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Iniciar um novo pacote de acesso

**Função de pré-requisito:** Usuário administrador ou proprietário do catálogo

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **acessar pacotes**.

    ![Gerenciamento de direitos no portal do Azure](./media/entitlement-management-shared/elm-access-packages.png)

1. Clique em **novo pacote de acesso**.

## <a name="basics"></a>Noções básicas

Sobre o **Noções básicas sobre** guia, nomeie o pacote de acesso e especificar qual catálogo para criar o pacote de acesso no.

1. Insira um nome de exibição e uma descrição para o pacote de acesso. Os usuários verão essas informações ao enviar uma solicitação para o pacote de acesso.

1. No **catálogo** lista suspensa, selecione o catálogo que você deseja criar o acesso de pacote no. Por exemplo, você pode ter um proprietário do catálogo que gerencia todos os recursos de marketing que podem ser solicitados. Nesse caso, você pode selecionar o catálogo de marketing.

    Você só verá catálogos você tem permissão para criar pacotes de acesso no. Para criar o pacote de acesso em um catálogo existente, você deve ser pelo menos um usuário administrador, o proprietário do catálogo ou o Gerenciador de pacotes de acesso.

    ![Pacote de acesso - Noções básicas](./media/entitlement-management-access-package-create/basics.png)

    Se você quiser criar seu pacote de acesso em um novo catálogo, clique em **criar novo**. Insira o nome do catálogo e a descrição e, em seguida, clique em **criar**.

    O pacote de acesso que você está criando e todos os recursos incluídos nele serão adicionados ao novo catálogo. Além disso, você tornará automaticamente o primeiro proprietário do catálogo. Você pode adicionar os proprietários de catálogo adicionais.

    Para criar um novo catálogo, você deve ser pelo menos um usuário administrador ou criador de catálogo.

1. Clique em **Avançar**.

## <a name="resource-roles"></a>Funções de recurso

Sobre o **funções de recurso** guia, que você selecione os recursos a serem incluídos no pacote de acesso.

1. Clique no tipo de recurso que você deseja adicionar (**grupos**, **aplicativos**, ou **sites do SharePoint**).

1. No painel de seleção que aparece, selecione um ou mais recursos na lista.

    ![Pacote de acesso - funções de recurso](./media/entitlement-management-access-package-create/resource-roles.png)

    Se você estiver criando o pacote de acesso no catálogo geral ou um novo catálogo, você poderá escolher qualquer recurso do diretório que você possui. Você deve ser pelo menos um usuário administrador ou criador de catálogo.

    Se você estiver criando o pacote de acesso em um catálogo existente, você pode selecionar qualquer recurso que já está no catálogo sem ser proprietário-lo.

    Se você for um usuário administrador ou proprietário do catálogo, você tem a opção adicional de seleção de recursos que você tem e que não estão no catálogo. Se você selecionar recursos não estão atualmente no catálogo selecionado, esses recursos também serão adicionados ao catálogo de outros administradores de catálogo criar pacotes de acesso com. Se você quiser selecionar os recursos que estão atualmente no catálogo selecionado, verifique a **ver apenas** caixa de seleção na parte superior da panorâmica de Select.

1. Depois que você selecionou os recursos, o **função** lista, selecione a função que você deseja que os usuários a ser atribuído para o recurso.

    ![Pacote de acesso - seleção de função de recurso](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Clique em **Avançar**.

## <a name="policy"></a>Política

Sobre o **diretiva** guia, você cria a primeira diretiva para especificar quem pode solicitar o pacote de acesso e também as configurações de aprovação e expiração. Posteriormente, você pode criar políticas mais para permitir que grupos de usuários para solicitar o pacote de acesso com suas próprias configurações de aprovação e expiração adicionais. Você também pode optar por criar a política mais tarde.

1. Defina as **criar a primeira diretiva** alternar para o **agora** ou **mais tarde**.

    ![Package - política de acesso](./media/entitlement-management-access-package-create/policy.png)

1. Se você selecionar **Later**, pular para o [revisar + criar](#review--create) seção para criar seu pacote de acesso.

1. Se você selecionar **agora**, execute as etapas em uma das seguintes seções de política.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Examinar + criar

Sobre o **revisar + criar** guia, você pode examinar as configurações e verificar se há erros de validação.

1. Examine as configurações do pacote de acesso

    ![Pacote de acesso – habilitar política de configuração de política](./media/entitlement-management-access-package-create/review-create.png)

1. Clique em **criar** para criar o pacote de acesso.

    O novo pacote de acesso é exibido na lista de pacotes de acesso.

## <a name="next-steps"></a>Próximas etapas

- [Editar e gerenciar um pacote existente de acesso](entitlement-management-access-package-edit.md)
- [Criar e gerenciar um catálogo](entitlement-management-catalog-create.md)
