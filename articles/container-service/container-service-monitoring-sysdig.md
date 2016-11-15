---
title: "Monitorar um cluster do Serviço de Contêiner do Azure com Sysdig | Microsoft Docs"
description: "Monitore um cluster do Serviço de Contêiner do Azure com Sysdig."
services: container-service
documentationcenter: 
author: rbitia
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Contêineres, DC/OS, Azure"
ms.assetid: 91d9a28a-3a52-4194-879e-30f2fa3d946b
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2016
ms.author: t-ribhat
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4407bc49bf11cada012015f340a9995a26374d00


---
# <a name="monitor-an-azure-container-service-cluster-with-sysdig"></a>Monitorar um cluster do Serviço de Contêiner do Azure com Sysdig
Neste artigo, implantaremos agentes de Sysdig para todos os nós de agente em seu cluster do Serviço de Contêiner do Azure. Você precisa de uma conta com Sysdig para essa configuração. 

## <a name="prerequisites"></a>Pré-requisitos
[Implantar](container-service-deployment.md) e [conectar](container-service-connect.md) um cluster configurado pelo Serviço de Contêiner do Azure. Explorar a [interface do usuário do Marathon](container-service-mesos-marathon-ui.md). Acesse [http://app.sysdigcloud.com](http://app.sysdigcloud.com) para configurar uma conta de nuvem de Sysdig. 

## <a name="sysdig"></a>Sysdig
Sysdig é um serviço de monitoramento que permite monitorar os contêineres no cluster. Sysdig ajuda a solucionar problemas, mas também tem métricas de monitoramentos básicas para CPU, rede, memória e E/S. Com Sysdig, é mais fácil ver quais contêineres estão trabalhando mais ou, essencialmente, usando mais memória e CPU. Esse modo de exibição está na seção "Visão geral", que está atualmente na versão beta. 

![Interface do usuário de Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurar uma implantação de Sysdig com Marathon
Estas etapas mostram como configurar e implantar aplicativos Sysdig no cluster com Marathon. 

Acesse a interface do usuário de DC/SO via [http://localhost:80/](http://localhost:80/). Quando estiver na interface do usuário de DC/SO, navegue até o "Universo", na parte inferior esquerda, e procure por "Sysdig".

![Sysdig no universo de DC/OS](./media/container-service-monitoring-sysdig/sysdig1.png)

Agora, para concluir a configuração, é necessária uma conta de nuvem de Sysdig ou uma conta de avaliação gratuita. Quando estiver conectado ao site de nuvem de Sysdig, clique em seu nome de usuário e, na página, você deverá ver a "Chave de acesso". 

![Chave de API d Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Em seguida, digite sua Chave de Acesso para a configuração de Sysdig no universo de DC/OS. 

![Configuração de Sysdig no Universo de DC/OS](./media/container-service-monitoring-sysdig/sysdig3.png)

Agora defina as instâncias como 10000000 para que sempre que um novo nó for adicionado ao cluster, Sysdig implante automaticamente um agente para o novo nó. Essa é uma solução temporária para garantir que Sysdig implante todos os novos agentes no cluster. 

![Configuração de Sysdig nas instâncias de Universo de DC/OS](./media/container-service-monitoring-sysdig/sysdig4.png)

Após instalar o pacote, navegue até a IU de Sysdig e você poderá explorar as diferentes métricas de uso para os contêineres no cluster. 




<!--HONumber=Nov16_HO2-->


