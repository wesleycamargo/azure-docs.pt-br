---
title: "Criar uma investigação personalizada - Gateway de Aplicativo do Azure - Portal do Azure | Microsoft Docs"
description: "Saiba como criar uma investigação personalizada para o Application Gateway usando o portal"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 14b715013b4154a1fa079c0dc470e675d7cf4c1f
ms.lasthandoff: 01/24/2017


---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Criar uma investigação personalizada para o Application Gateway usando o portal
> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>Cenário

O cenário a seguir passa pela criação de uma investigação de integridade personalizada em um Application Gateway existente.
O cenário pressupõe que você já seguiu as etapas para [Criar um Application Gateway](application-gateway-create-gateway-portal.md).

## <a name="a-namecreateprobeacreate-the-probe"></a><a name="createprobe"></a>Criar a investigação

As investigações são configuradas em um processo de duas etapas pelo portal. A primeira etapa é criar a investigação. Em seguida, você adiciona a investigação às configurações de HTTP do back-end do Gateway de Aplicativo.

### <a name="step-1"></a>Etapa 1

Navegue até o [Portal do Azure](http://portal.azure.com) e selecione um gateway de aplicativo existente.

![Visão geral do Application Gateway][1]

### <a name="step-2"></a>Etapa 2

Clique em **Investigações** e clique no botão **Adicionar** para adicionar uma investigação.

![Folha Adicionar Investigação com as informações preenchidas][2]

### <a name="step-3"></a>Etapa 3

Preencha as informações necessárias para a investigação e ao concluir, clique em **OK**.

* **Nome** – este valor é um nome amigável para a investigação que está acessível no portal.
* **Host** – este valor é o nome do host usado para a investigação. Aplicável somente quando vários sites são configurados no Application Gateway; do contrário, use '127.0.0.1'. Este valor é diferente do nome do host de VM.
* **Caminho** : o restante da URL completa para a investigação personalizada. Um caminho válido começa com "/"
* **Intervalo (segundos)** : frequência com que a investigação é executada para verificar a integridade. Não é recomendável defini-la abaixo de 30 segundos.
* **Tempo limite (segundos)** : a quantidade de tempo que a investigação espera antes de atingir o tempo limite. O intervalo de tempo limite deve ser alto o suficiente para que uma chamada http possa ser feita a fim de garantir que a página de integridade do back-end estará disponível.
* **Limite não íntegro** : número de tentativas com falha para ser considerado não íntegro. Um limite de 0 significa que, em caso de falha de verificação de integridade, o back-end é considerado não íntegro imediatamente.

> [!IMPORTANT]
> O nome do host não é o mesmo que o nome do servidor. Esse valor é o nome do host virtual em execução no servidor de aplicativos. A investigação é enviada para http://(nome do host):(porta de httpsetting)/urlPath

![definições de configuração da investigação][3]

## <a name="add-probe-to-the-gateway"></a>Adicionar a investigação ao gateway

Agora que a investigação foi criada, é hora de adicioná-la ao gateway. As configurações da investigação são definidas nas configurações de http do back-end do Application Gateway.

### <a name="step-1"></a>Etapa 1

Clique em **Configurações de HTTP** do Gateway de Aplicativo e clique nas configurações de HTTP do back-end atual na janela para abrir a folha de configuração.

![janela de configurações de https][4]

### <a name="step-2"></a>Etapa 2

Na folha de configuração **appGatewayBackEndHttp**, clique em **Usar investigação personalizada** e selecione a investigação usada na seção [Criar a investigação](#createprobe).
Ao concluir, clique em **OK** e as configurações são aplicadas.

![folha de configurações de appgatewaybackend][5]

A investigação de padrão verifica o acesso padrão ao aplicativo Web. Agora que uma investigação personalizada foi criada, o Application Gateway usa o caminho personalizado definido para monitorar a integridade do back-end selecionado. Com base nos critérios definidos, o Application Gateway verifica o arquivo especificado na investigação. Se a chamada para host:Porta/caminho não retornar uma resposta de status HTTP 200 OK, o servidor será retirado da rotação após o limite não íntegro ser atingido. A investigação continua na instância não íntegra para determinar quando ela se torna íntegra novamente. Após a instância ser adicionada novamente ao pool de servidores íntegros, o tráfego começa a fluir para ela novamente e a investigação da instância continua normalmente no intervalo especificado pelo usuário.

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar o descarregamento SSL com o Gateway de Aplicativo do Azure, veja [Configurar descarregamento SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png

