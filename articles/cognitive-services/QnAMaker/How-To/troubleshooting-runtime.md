---
title: Solução de problemas do QnAMaker | Microsoft Docs
titleSuffix: Azure
description: Como solucionar problemas do tempo de execução do QnAMaker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 06/04/2018
ms.author: saneppal
ms.openlocfilehash: 23847c81a39ea392b6e64575406c9dc338b852de
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "35364894"
---
# <a name="qnamaker-troubleshooting"></a>Solução de problemas do QnAMaker
O QnAMaker inclui componentes hospedados na conta do usuário do Azure. A depuração pode exigir que os usuários manipulem os recursos do QnAMaker Azure ou forneçam à equipe de suporte do QnAMaker informações adicionais sobre a configuração.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Como obter as últimas atualizações do tempo de execução do QnAMaker
O tempo de execução do QnAMaker é parte do Serviço de Aplicativo do Azure implantado ao [criar um serviço do QnAMaker](./set-up-qnamaker-service-azure.md) no portal do Azure. Atualizações são feitas periodicamente para o tempo de execução. Para aplicar as últimas atualizações a serem aplicadas à configuração do QnAMaker, é necessário reiniciar o Serviço de Aplicativo.
1. Acesse o serviço do QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Clique no Serviço de Aplicativo e abra a seção Visão Geral

     ![Serviço de Aplicativo do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Reinicie o serviço de Aplicativo. Isso deve concluir dentro de alguns segundos. Observe que os bots/aplicativos de downstream compilados nesse serviço do QnAMaker estarão indisponíveis para os usuários finais durante esse período de reinicio.

    ![Reinício do serviço de aplicativo do QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Como obter o nome de host de serviço do QnAMaker
O nome do host de serviço do QnAMaker será útil para finalidades de depuração quando você contatar o Suporte do QnAMaker ou UserVoice. O nome do host é uma URL deste formulário: https://*{nome do host}*.azurewebsites.net.
    
1. Acesse o serviço do QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Clique no Serviço de Aplicativo

     ![Serviço de Aplicativo do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. A URL do nome do host está disponível na seção Visão Geral

    ![Nome do host do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar API do QnAMaker](./upgrade-qnamaker-service.md)
