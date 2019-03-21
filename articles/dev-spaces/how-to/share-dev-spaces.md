---
title: Como compartilhar espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: 'Docker, Kubernetes, Azure, AKS, serviço Kubernetes do Azure, contêineres, Helm, malha de serviço, o roteamento de malha do serviço, kubectl, k8s '
ms.openlocfilehash: 62d4affa5ef49de7600f9ccc800ea6bf83e4bd49
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099569"
---
# <a name="share-azure-dev-spaces"></a>Compartilhar Azure Dev Spaces

Com o Azure Dev Spaces, você pode compartilhar o espaço de desenvolvimento com outras pessoas de sua equipe. Cada desenvolvedor pode trabalhar em seu próprio espaço sem receio de interromper outros. Além disso, trabalhar em conjunto em um espaço pode permitir que você teste o código de ponta a ponta sem precisar criar simulações ou simular dependências. Consulte o guia [Saiba mais sobre o desenvolvimento em equipe](../team-development-nodejs.md).

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Configurar um espaço de desenvolvimento para vários desenvolvedores

1. Crie um Dev Space no Azure. Escolha [.NET Core 3 VS Code](../get-started-netcore.md), [.NET Core e Visual Studio](../get-started-netcore-visualstudio.md) ou [Node.js e VS Code](../get-started-nodejs.md). É necessário ter acesso de Colaborador ou Proprietário para a assinatura do Azure selecionada.
1. Configure o **grupo de recursos** do Azure Dev Space para [conceder acesso de Colaborador](/azure/active-directory/role-based-access-control-configure) a cada membro da equipe. É possível verificar o grupo de recursos de um espaço de desenvolvimento executando este comando: `azds list-up`
1. Solicite aos membros da equipe para **selecionar o espaço de desenvolvimento** para desenvolver nele.
   * **Linha de comando ou VS Code**: Para ver o Azure Dev Spaces existente ao qual você tem acesso: `azds space list`. Para selecionar um espaço de desenvolvimento: `azds space select`.
   * **IDE do Visual Studio**: Abra um projeto no Visual Studio, selecione **Azure Dev Spaces** no menu suspenso de configurações de inicialização. Na caixa de diálogo que é aberta, selecione um cluster existente.

     ![Menu suspenso de configurações de inicialização do Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Próximas etapas

Consulte [Saiba mais sobre o desenvolvimento em equipe](../team-development-nodejs.md) para obter mais informações.
