---
title: Atualizar serviço de aplicativo do Azure na pilha do Azure | Microsoft Docs
description: Orientações detalhadas para a atualização do serviço de aplicativo do Azure na pilha do Azure
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.openlocfilehash: c822f25e25953b8709f481e51d6a63e6a912a60a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358805"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Atualizar serviço de aplicativo do Azure na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

> [!IMPORTANT]
> Aplicar a atualização 1804 a seu sistema de pilha do Azure integradas ou implantar o kit de desenvolvimento de pilha do Azure mais recente antes de implantar 1.2 de serviço de aplicativo do Azure.
>
>

Seguindo as instruções neste artigo, você pode atualizar o [provedor de recursos do serviço de aplicativo](azure-stack-app-service-overview.md) implantado em um ambiente de pilha do Azure que está conectado à Internet.

> [!IMPORTANT]
> Antes de executar a atualização, certifique-se de que você já tenha concluído o [implantação do serviço de aplicativo do Azure no provedor de recursos de pilha do Azure](azure-stack-app-service-deploy.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Execute o instalador do provedor de recursos do serviço de aplicativo

Durante esse processo, a atualização será:

* Detectar a implantação anterior do serviço de aplicativo
* Preparar todos os pacotes de atualização e novas versões de todas as bibliotecas de OSS para ser implantado
* Carregar no armazenamento
* Atualizar todas as funções de serviço de aplicativo (controladores, gerenciamento, front-end, Publisher e Worker funções)
* Atualizar as definições do conjunto de dimensionamento do Serviço de Aplicativo
* Atualizar o manifesto do provedor de recursos de serviço de aplicativo

> [!IMPORTANT]
> O instalador do serviço de aplicativo deve ser executado em um computador pode alcançar o ponto de extremidade do Gerenciador de recursos do Azure pilha administrador do Azure.
>
>

Para atualizar a implantação do serviço de aplicativo na pilha do Azure, siga estas etapas:

1. Baixe o [instalador de serviço de aplicativo](https://aka.ms/appsvcupdate2installer)

2. Executar appservice.exe como um administrador

    ![Instalador de serviço de aplicativo][1]

3. Clique em **implantar o serviço de aplicativo ou atualização para a versão mais recente.**

4. Revise e aceite os termos de licença de Software da Microsoft e, em seguida, clique em **próximo**.

5. Revise e aceite os termos de licença de terceiros e, em seguida, clique em **próximo**.

6. Verifique se o ponto de extremidade do Gerenciador de recursos do Azure pilha do Azure e o locatário do Active Directory informações estão corretas. Se você usou as configurações padrão durante a implantação do Kit de desenvolvimento de pilha do Azure, você pode aceitar os valores padrão aqui. No entanto, se você personalizou as opções quando você implantou a pilha do Azure, você deve editar os valores nesta janela de refletir isso. Por exemplo, se você usar o sufixo do domínio *mycloud.com*, deverá alterar o ponto de extremidade do Gerenciador de recursos do Azure pilha do Azure para *management.region.mycloud.com*. Depois de confirmar suas informações, clique em **próximo**.

    ![Informações da nuvem de pilha do Azure][2]

7. Na próxima página:

   1. Clique no **conectar** lado a **assinaturas de pilha do Azure** caixa.
        * Se você estiver usando o Azure Active Directory (AD do Azure), insira a conta de administrador do AD do Azure e a senha que você forneceu quando você implantou a pilha do Azure. Clique em **entrar**.
        * Se você estiver usando os serviços de Federação do Active Directory (AD FS), fornece sua conta de administrador. Por exemplo, *cloudadmin@azurestack.local*. Digite sua senha e clique em **entrar**.
   2. No **assinaturas do Azure pilha** caixa, selecione a **assinatura do provedor padrão**.
   3. No **Azure pilha locais** , selecione o local que corresponde à região que você está implantando. Por exemplo, selecione **local** se sua implantação para o Kit de desenvolvimento de pilha do Azure.
   4. Se uma implantação existente do serviço de aplicativo for descoberta, em seguida, a conta de armazenamento e o grupo de recursos será preenchida e esmaecida.
   5. Clique em **próximo** para examinar o resumo da atualização.

    ![Instalação do serviço de aplicativo detectada][3]

8. Na página de resumo:
   1. Verifique se as seleções feitas por você. Para fazer alterações, use o **anterior** botões para visitar a páginas anteriores.
   2. Se as configurações estão corretas, selecione a caixa de seleção.
   3. Para iniciar a atualização, clique em **próximo**.

       ![Resumo da atualização do serviço de aplicativo][4]

9. Página progresso da atualização:
    1. Acompanhar o progresso da atualização. A duração da atualização do serviço de aplicativo no Azure pilha varia depende do número de instâncias de função implantadas.
    2. Depois que a atualização for concluída com êxito, clique em **saída**.

        ![Progresso da atualização do serviço de aplicativo][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md).

* [Provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)
