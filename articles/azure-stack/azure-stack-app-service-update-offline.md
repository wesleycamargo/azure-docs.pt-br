---
title: "Atualizar serviço de aplicativo do Azure Offline | Microsoft Docs"
description: "Orientações detalhadas para a atualização do serviço de aplicativo do Azure na pilha do Azure offline"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 61a3169229cc121c078a934f6b979bdaffafd565
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Atualização offline do serviço de aplicativo do Azure na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

> [!IMPORTANT]
> Aplicar a atualização 1802 para seu sistema de pilha do Azure integradas ou implantar o kit de desenvolvimento de pilha do Azure mais recente antes de implantar o serviço de aplicativo do Azure.
>
>

Seguindo as instruções neste artigo, você pode atualizar o [provedor de recursos do serviço de aplicativo](azure-stack-app-service-overview.md) implantado em um ambiente de pilha do Azure é:

* não conectado à Internet
* protegido pelos serviços de Federação do Active Directory (AD FS).

> [!IMPORTANT]
> Antes de executar a atualização, certifique-se de que você já tenha concluído o [implantação do serviço de aplicativo do Azure no provedor de recursos de pilha do Azure](azure-stack-app-service-deploy-offline.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Execute o instalador do provedor de recursos do serviço de aplicativo

Para atualizar o provedor de recursos do serviço de aplicativo em um ambiente de pilha do Azure, você deve concluir estas tarefas:

1. Baixe o [instalador de serviço de aplicativo](https://aka.ms/appsvcupdate1installer)
2. Crie um pacote de atualização offline.
3. Execute o instalador de serviço de aplicativo (appservice.exe) e concluir a atualização.

Durante esse processo, a atualização será:

* Detectar a implantação anterior do serviço de aplicativo
* Carregar no armazenamento
* Atualizar todas as funções de serviço de aplicativo (controladores, gerenciamento, front-end, Publisher e Worker funções)
* Atualizar definições de conjunto de escala do serviço de aplicativo
* Atualizar o manifesto do provedor de recursos de serviço de aplicativo

## <a name="create-an-offline-upgrade-package"></a>Criar um pacote de atualização offline

Para atualizar o serviço de aplicativo em um ambiente desconectado, primeiro você deve criar um pacote de atualização offline em um computador que está conectado à Internet.

1. Executar appservice.exe como um administrador

    ![Instalador de serviço de aplicativo][1]

2. Clique em **avançado** > **criar pacote offline**

    ![Instalador de serviço de aplicativo avançado][2]

3. O instalador do serviço de aplicativo cria um pacote de atualização offline e exibe o caminho para ele.  Você pode clicar em **Abrir pasta** para abrir a pasta em que o Explorador de arquivos.

4. Copie o instalador (AppService.exe) e o pacote de atualização offline para sua máquina de host de pilha do Azure.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Concluir a atualização do serviço de aplicativo na pilha do Azure

> [!IMPORTANT]
> O instalador do serviço de aplicativo deve ser executado em um computador que pode acessar o Azure pilha administrador Azure Gerenciador de recursos de ponto de extremidade.
>
>

1. Execute appservice.exe como um administrador.  

    ![Instalador de serviço de aplicativo][1]

2. Clique em **avançado** > **concluir a instalação offline ou atualização**.

    ![Instalador de serviço de aplicativo avançado][2]

3. Navegue até o local do pacote de atualização offline que você criou anteriormente e, em seguida, clique em **próximo**.

4. Revise e aceite os termos de licença de Software da Microsoft e, em seguida, clique em **próximo**.

5. Revise e aceite os termos de licença de terceiros e, em seguida, clique em **próximo**.

6. Verifique se o ponto de extremidade do Gerenciador de recursos do Azure pilha do Azure e o locatário do Active Directory informações estão corretas. Se você usou as configurações padrão durante a implantação do Kit de desenvolvimento de pilha do Azure, você pode aceitar os valores padrão aqui. No entanto, se você personalizou as opções quando você implantou a pilha do Azure, você deve editar os valores nesta janela de refletir isso. Por exemplo, se você usar o sufixo do domínio *mycloud.com*, deverá alterar o ponto de extremidade do Gerenciador de recursos do Azure pilha do Azure para *management.region.mycloud.com*. Depois de confirmar suas informações, clique em **próximo**.

    ![Informações da nuvem de pilha do Azure][3]

7. Na próxima página:

   1. Clique no **conectar** lado a **assinaturas de pilha do Azure** caixa.
        * Se você estiver usando o Azure Active Directory (AD do Azure), insira a conta de administrador do AD do Azure e a senha que você forneceu quando você implantou a pilha do Azure. Clique em **entrar**.
        * Se você estiver usando os serviços de Federação do Active Directory (AD FS), fornece sua conta de administrador. Por exemplo, *cloudadmin@azurestack.local*. Digite sua senha e clique em **entrar**.
   2. No **assinaturas do Azure pilha** , selecione sua assinatura.
   3. No **Azure pilha locais** , selecione o local que corresponde à região que você está implantando. Por exemplo, selecione **local** se sua implantação para o Kit de desenvolvimento de pilha do Azure.
   4. Se uma implantação existente do serviço de aplicativo for descoberta, em seguida, a conta de armazenamento e o grupo de recursos será preenchida e esmaecida.
   5. Clique em **próximo** para examinar o resumo da atualização.

    ![Instalação do serviço de aplicativo detectada][4]

8. Na página de resumo:
   1. Verifique se as seleções feitas por você. Para fazer alterações, use o **anterior** botões para visitar a páginas anteriores.
   2. Se as configurações estão corretas, selecione a caixa de seleção.
   3. Para iniciar a atualização, clique em **próximo**.

       ![Resumo da atualização do serviço de aplicativo][5]

9. Página progresso da atualização:
    1. Acompanhar o progresso da atualização. A duração da atualização do serviço de aplicativo no Azure pilha varia depende do número de instâncias de função implantadas.
    2. Depois que a atualização for concluída com êxito, clique em **saída**.

        ![Progresso da atualização do serviço de aplicativo][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md).

* [Provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)
