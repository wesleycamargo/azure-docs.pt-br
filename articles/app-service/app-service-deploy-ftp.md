---
title: Implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP/S | Microsoft Docs
description: Saiba como implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP ou FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin;dariac
ms.openlocfilehash: 561f317cd7afd740b83709efc8a75ed515626192
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP/S

Este artigo mostra como usar o FTP ou FTPS para implantar seu aplicativo web, back-end do aplicativo móvel ou aplicativo de API [o serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

O ponto de extremidade FTP/S para seu aplicativo já está ativo. Nenhuma configuração é necessária para habilitar a implantação de FTP/S.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Etapa 1: Definir credenciais de implantação

Para acessar o servidor FTP para o seu aplicativo, você primeiro precisa de credenciais de implantação. 

Para definir ou redefinir suas credenciais de implantação, consulte [credenciais de implantação do serviço de aplicativo do Azure](app-service-deployment-credentials.md). Este tutorial demonstra o uso de credenciais de nível de usuário.

## <a name="step-2-get-ftp-connection-information"></a>Etapa 2: Obter informações de conexão de FTP

1. No [Portal do Azure](https://portal.azure.com), abra a [página de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) do seu aplicativo.
2. Selecione **Visão geral** no painel de navegação à esquerda, em seguida, observe os valores de **Usuário FTP/implantação**, **Nome do Host FTP**, e **Nome de Host FTPS**. 

    ![Informações de conexão de FTP](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Para fornecer o contexto apropriado para o servidor FTP, o valor de **usuário de FTP/implantação** exibido pelo portal do Azure inclui o nome do aplicativo.
    > Você pode encontrar as mesmas informações quando você seleciona **Propriedades** no painel de navegação à esquerda. 
    >
    > Além disso, a senha de implantação nunca é mostrada. Se você esquecer sua senha de implantação, vá até [etapa 1](#step1) e redefinir a senha de implantação.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Etapa 3: Implantar arquivos para o Azure

1. De seu cliente FTP (por exemplo, [Visual Studio](https://www.visualstudio.com/vs/community/) ou [FileZilla](https://filezilla-project.org/download.php?type=client)), use as informações de conexão coletadas para conectar-se ao seu aplicativo.
3. Copie seus arquivos e a respectiva estrutura de diretórios para o diretório [**/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou o diretório **/site/wwwroot/App_Data/Jobs/** para Trabalhos Web).
4. Navegue até a URL do aplicativo para verificar se ele está sendo executado corretamente. 

> [!NOTE] 
> Ao contrário de [implantações com base em Git](app-service-deploy-local-git.md), implantação de FTP não oferece suporte as automações de implantação a seguir: 
>
> - restauração de dependência (como automações NuGet, NPM, PIP e de criador)
> - compilação de binários do .NET
> - geração de Web. config (eis uma [Node. js exemplo](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Gere esses arquivos necessários manualmente no computador local e implante-os junto com seu aplicativo.
>
>

## <a name="enforce-ftps"></a>Impor o FTPS

Para maior segurança, você deve permitir somente FTP por SSL. Você também pode desabilitar FTP e FTPS se não usa a implantação de FTP.

Na página de recursos do seu aplicativo no [Portal do Azure](https://portal.azure.com), selecione **Configurações do aplicativo** no painel de navegação à esquerda.

Para desabilitar o FTP não criptografado, selecione **Somente FTPS**. Para desabilitar completamente o FTP e o FTPS, selecione **Desabilitar**. Ao terminar, clique em **Salvar**.

![Desabilitar FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="troubleshoot-ftp-deployment"></a>Solucionar problemas de implantação de FTP

- [Como soluciono problemas de implantação de FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Não consigo usar FTP e publicar meu código. Como resolvo esse problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Como faço para me conectar ao FTP no Serviço de Aplicativo do Azure por meio do modo passivo?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Como soluciono problemas de implantação de FTP?

A primeira etapa para solucionar problemas de implantação de FTP é isolar um problema de implantação de um problema de aplicativo de tempo de execução.

Um problema de implantação normalmente resulta em nenhum arquivo ou arquivos incorretos implantados em seu aplicativo. Ele pode ser resolvido investigando a implantação de FTP ou selecionando um caminho de implantação alternativo (como, por exemplo, controle de origem).

Um problema de aplicativo de tempo de execução normalmente resulta no conjunto de arquivos correto implantado no seu aplicativo, mas em comportamento incorreto do aplicativo. Ele pode ser resolvido se concentrando no comportamento do código no tempo de execução e investigando os caminhos de falha específicos.

Para determinar um problema de implantação ou de tempo de execução, consulte [Problemas de implantação versus problemas de tempo de execução](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

 
### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Não consigo usar FTP e publicar meu código. Como resolvo esse problema?
Verifique se você inseriu corretamente o nome de host e as [credenciais](#step-1--set-deployment-credentials). Verifique também se as seguintes portas de FTP no seu computador não estão bloqueadas por um firewall:

- Porta de conexão de controle FTP: 21
- Porta de conexão de dados FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Como faço para me conectar ao FTP no Serviço de Aplicativo do Azure por meio do modo passivo?
O Serviço de Aplicativo do Azure é compatível com a conexão por modo Ativo e Passivo. O modo Passivo é preferencial porque seus computadores de implantação geralmente estão atrás de um firewall (no sistema operacional ou como parte de uma rede residencial ou comercial). Veja um [exemplo na documentação do WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Próximas etapas

Para cenários mais avançados de implantação, tente [implantação no Azure com Git](app-service-deploy-local-git.md). Implantação baseada em Git no Azure permite o controle de versão, restauração do pacote, MSBuild e muito mais.

## <a name="more-resources"></a>Mais Recursos

* [Credenciais de implantação do Serviço de Aplicativo do Azure](app-service-deploy-ftp.md)
