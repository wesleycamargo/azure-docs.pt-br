---
title: Atualizar serviço de aplicativo do Azure Offline | Microsoft Docs
description: Orientações detalhadas para a atualização de serviço de aplicativo do Azure no Azure Stack offline
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.openlocfilehash: 9d941c36499f851f20c41fa6dd01faf14e4192ba
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992763"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Atualização offline do serviço de aplicativo do Azure no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!IMPORTANT]
> Aplique a atualização 1901 ou posterior ao Azure Stack sistema integrado ou implantar o kit de desenvolvimento mais recente do Azure Stack antes de implantar 1.5 de serviço de aplicativo do Azure. 

Seguindo as instruções neste artigo, você pode atualizar o [provedor de recursos do serviço de aplicativo](azure-stack-app-service-overview.md) implantado em um ambiente do Azure Stack que é:

* não conectado à Internet
* protegido pelos serviços de Federação do Active Directory (AD FS).

> [!IMPORTANT]
> Antes de executar a atualização, certifique-se de que você já tenha concluído a [implantação do serviço de aplicativo do Azure no provedor de recursos do Azure Stack](azure-stack-app-service-deploy-offline.md) e que você tenha lido a [notas de versão](azure-stack-app-service-release-notes-update-five.md), que acompanham a versão 1.5, para saber mais sobre a nova funcionalidade, correções e problemas conhecidos que podem afetar sua implantação.

## <a name="run-the-app-service-resource-provider-installer"></a>Execute o instalador de provedor de recursos de serviço de aplicativo

Para atualizar o provedor de recursos do serviço de aplicativo em um ambiente do Azure Stack, você deve concluir estas tarefas:

1. Baixe o [instalador do serviço de aplicativo](https://aka.ms/appsvcupdate4installer)
2. Crie um pacote de atualização offline.
3. Execute o instalador do serviço de aplicativo (appservice.exe) e concluir a atualização.

Durante esse processo, a atualização será:

* Detectar anterior em implantação do serviço de aplicativo
* Carregar no armazenamento
* Atualizar todas as funções de serviço de aplicativo (controladores, gerenciamento, front-end, Publisher e Worker funções)
* Atualizar as definições do conjunto de dimensionamento do Serviço de Aplicativo
* Atualizar manifesto do provedor de recursos de serviço de aplicativo

## <a name="create-an-offline-upgrade-package"></a>Criar um pacote de atualização offline

Para atualizar o serviço de aplicativo em um ambiente desconectado, você deve primeiro criar um pacote de atualização offline em um computador que está conectado à Internet.

1. Execute appservice.exe como um administrador

    ![Instalador do serviço de aplicativo][1]

2. Clique em **Advanced** > **criar pacote offline**

    ![Instalador de serviço de aplicativo avançado][2]

3. O instalador do serviço de aplicativo cria um pacote de atualização offline e exibe o caminho para ele.  Você pode clicar em **Abrir pasta** para abrir a pasta no Gerenciador de arquivos.

4. Copie o instalador (AppService.exe) e o pacote de atualização offline para sua máquina de host do Azure Stack.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Concluir a atualização do serviço de aplicativo no Azure Stack

> [!IMPORTANT]
> O instalador do serviço de aplicativo deve ser executado em um computador que pode acessar o Azure Stack administrador do Azure Resource Manager ponto de extremidade.
>
>

1. Execute appservice.exe como um administrador.

    ![Instalador do serviço de aplicativo][1]

2. Clique em **Advanced** > **concluir a instalação offline ou atualização**.

    ![Instalador de serviço de aplicativo avançado][2]

3. Navegue até o local do pacote de atualização offline que você criou anteriormente e, em seguida, clique em **próxima**.

4. Examine e aceite os termos de licença de Software da Microsoft e, em seguida, clique em **próxima**.

5. Examine e aceite os termos de licença de terceiros e, em seguida, clique em **próxima**.

6. Certifique-se de que o ponto de extremidade de pilha do Azure Resource Manager e o locatário do Active Directory informações estão corretas. Se você usou as configurações padrão durante a implantação do Kit de desenvolvimento do Azure Stack, você pode aceitar os valores padrão aqui. No entanto, se você personalizou as opções quando você implantou o Azure Stack, você deve editar os valores nessa janela. Por exemplo, se você usar o sufixo do domínio *mycloud.com*, altere para o ponto de extremidade de pilha do Azure Resource Manager *management.region.mycloud.com*. Depois de confirmar suas informações, clique em **próxima**.

    ![Informações de nuvem do Azure Stack][3]

7. Na próxima página:

   1. Clique o **Connect** lado a **assinaturas do Azure Stack** caixa.
        * Se você estiver usando o Azure Active Directory (Azure AD), insira a conta de administrador do Azure AD e a senha que você forneceu quando você implantou o Azure Stack. Clique em **entrar**.
        * Se você estiver usando os serviços de Federação do Active Directory (AD FS), forneça sua conta de administrador. Por exemplo, _cloudadmin@azurestack.local_. Insira sua senha e clique em **Sign In**.
   2. No **assinaturas do Azure Stack** caixa, selecione a **assinatura do provedor padrão**.
   3. No **locais da pilha do Azure** , selecione o local que corresponde à região em que você está implantando. Por exemplo, selecione **local** se sua implantação para o Kit de desenvolvimento do Azure Stack.
   4. Se uma implantação de serviço de aplicativo existente for detectada, em seguida, a conta de armazenamento e grupo de recursos será preenchida e esmaecida.
   5. Clique em **próxima** para examinar o resumo da atualização.

    ![Instalação do serviço de aplicativo detectada][4]

8. Na página de resumo:
   1. Verifique se as seleções feitas por você. Para fazer alterações, use o **anterior** botões para visitar a páginas anteriores.
   2. Se as configurações estão corretas, selecione a caixa de seleção.
   3. Para iniciar a atualização, clique em **próxima**.

       ![Resumo de atualização do serviço de aplicativo][5]

9. Página progresso da atualização:
    1. Acompanhe o progresso da atualização. A duração da atualização do serviço de aplicativo no Azure Stack varia de acordo com o número de instâncias de função implantadas.
    2. Depois que a atualização for concluída com êxito, clique em **Exit**.

        ![Progresso da atualização do serviço de aplicativo][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outras [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md).

* [Provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)
