---
title: "Implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP/S | Microsoft Docs"
description: "Saiba como implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP ou FTPS."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: cephalin;dariac
ms.translationtype: Human Translation
ms.sourcegitcommit: 816113f7635a003e22a5172113e5039dbcc1ceac
ms.openlocfilehash: 5dc546849bd02ccf4d02f3e6363a3e2fc3898259
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017


---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP/S
Este artigo mostra como usar o FTP ou FTPS para implantar seu aplicativo web, back-end do aplicativo móvel ou aplicativo de API [o serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

O ponto de extremidade FTP/S para seu aplicativo já está ativo. Nenhuma configuração é necessária para habilitar a implantação de FTP/S. 

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Etapa 1: Definir credenciais de implantação

Para acessar o servidor FTP para o seu aplicativo, você primeiro precisa de credenciais de implantação. 

Para definir ou redefinir suas credenciais de implantação, consulte [credenciais de implantação do serviço de aplicativo do Azure](app-service-deployment-credentials.md). Este tutorial demonstra o uso de credenciais de nível de usuário.

## <a name="step-2-get-ftp-connection-information"></a>Etapa 2: Obter informações de conexão de FTP

1. No [portal do Azure](https://portal.azure.com), abra a [folha de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) de seu aplicativo.
2. Selecione **visão geral** no menu à esquerda, em seguida, observe os valores de **usuário FTP/implantação**, **nome do Host FTP**, e **nome de Host FTPS**. 

    ![Informações de conexão de FTP](./media/web-sites-deploy/FTP-Connection-Info.PNG)

    > [!NOTE]
    > O valor de usuário **FTP/Usuário de Implantação** como exibido pelo Portal do Azure, incluindo o nome do aplicativo a fim de fornecer contexto adequado ao servidor FTP.
    > Você pode encontrar as mesmas informações quando você seleciona **propriedades** no menu à esquerda. 
    >
    > Além disso, a senha de implantação nunca é mostrada. Se você esquecer sua senha de implantação, vá até [etapa 1](#step1) e redefinir a senha de implantação.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Etapa 3: Implantar arquivos para o Azure

1. De seu cliente FTP ([Visual Studio](https://www.visualstudio.com/vs/community/), [FileZilla](https://filezilla-project.org/download.php?type=client) etc), use as informações de conexão coletadas para conectar-se ao seu aplicativo.
3. Copie seus arquivos e a respectiva estrutura de diretórios para o diretório [**/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou o diretório **/site/wwwroot/App_Data/Jobs/** para Trabalhos Web).
4. Navegue até a URL do aplicativo para verificar se ele está sendo executado corretamente. 

> [!NOTE] 
> Ao contrário de [implantações com base em Git](app-service-deploy-local-git.md), implantação de FTP não oferece suporte as automações de implantação a seguir: 
>
> - restauração de dependência (como automações NuGet, NPM, PIP e criador)
> - compilação de binários do .NET
> - geração de Web. config (eis uma [Node. js exemplo](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Você deve restaurar, criar e gerar esses arquivos necessários manualmente no computador local e implantá-los junto com seu aplicativo.
>
>

## <a name="next-steps"></a>Próximas etapas

Para cenários mais avançados de implantação, tente [implantação no Azure com Git](app-service-deploy-local-git.md). Implantação baseada em Git no Azure permite o controle de versão, restauração do pacote, MSBuild e muito mais.

## <a name="more-resources"></a>Mais Recursos

* [Criar um aplicativo Web do PHP-MySQL e implantá-lo usando o FTP](web-sites-php-mysql-deploy-use-ftp.md).
* [Credenciais de implantação do Serviço de Aplicativo do Azure](app-service-deploy-ftp.md)

