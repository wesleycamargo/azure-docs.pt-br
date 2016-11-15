---
title: "Gerenciamento de contêiner do Serviço de Contêiner do Azure por meio da interface do usuário da Web | Microsoft Docs"
description: "Implante contêineres para um serviço de cluster do Serviço de Contêiner do Azure usando a interface do usuário da Web do Marathon."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Mesos, Azure"
ms.assetid: d148ed1e-b582-4d51-944f-1ac7ae3c4fd6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ae2945b0025da3bc933a1e4a8f10f21fd35cb51c


---
# <a name="container-management-through-the-web-ui"></a>Gerenciamento de contêiner por meio da interface do usuário da Web
O DC/OS fornece um ambiente de implantação e dimensionamento de cargas de trabalho clusterizadas e, ao mesmo tempo, abstrai o hardware subjacente. Sobre o DC/OS, há uma estrutura que gerencia o agendamento e a execução das cargas de trabalho de computação.

Embora haja estruturas disponíveis para várias cargas de trabalho populares, este documento descreverá como você pode criar e dimensionar implantações de contêiner com o Marathon. Antes de trabalhar nos exemplos, você precisará de um cluster DC/OS configurado no Serviço de Contêiner do Azure. Você também precisa ter conectividade remota com esse cluster. Para saber mais sobre esses itens, confira os artigos a seguir:

* [Implantar um cluster do Serviço de Contêiner do Azure](container-service-deployment.md)
* [Conectar a um cluster do Serviço de Contêiner do Azure](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>Explorar a interface do usuário do DC/OS
Com um túnel SSH (Secure Shell) estabelecido, navegue até http://localhost/. Isso carrega a interface do usuário da Web do DC/OS e mostrará informações sobre o cluster, como recursos usados, agentes ativos e serviços em execução.

![Interface do usuário do DC/OS](media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Explorar a interface do usuário do Marathon
Para ver a Interface do usuário do Marathon, navegue até http://localhost/Marathon. Nessa tela, você pode iniciar um novo contêiner ou outro aplicativo no cluster DC/OS do Serviço de Contêiner do Azure. Você também pode ver informações sobre a execução de aplicativos e de contêineres.  

![Interface do usuário do Marathon](media/dcos/dcos3.png)

## <a name="deploy-a-dockerformatted-container"></a>Implantar um contêiner formatado pelo Docker
Para implantar um novo contêiner usando o Marathon, clique no botão **Criar Aplicativo** e insira as informações a seguir no formulário.

| Campo | Valor |
| --- | --- |
| ID |nginx |
| Imagem |nginx |
| Rede |Com ponte |
| Porta de host |80 |
| Protocolo |TCP |

![Interface do usuário do novo aplicativo – geral](media/dcos/dcos4.png)

![Interface do usuário do novo aplicativo – contêiner do Docker](media/dcos/dcos5.png)

![Interface do usuário do novo aplicativo - descoberta de serviço e portas](media/dcos/dcos6.png)

Se você deseja mapear estaticamente a porta do contêiner para uma porta no agente, precisa usar o Modo JSON. Para fazer isso, alterne o assistente de Novo Aplicativo para **Modo JSON** usando o botão de alternância. Em seguida, digite o seguinte na seção `portMappings` da definição do aplicativo. Este exemplo associa a porta 80 do contêiner à porta 80 do agente DC/OS. Depois de fazer essa alteração, você pode remover o assistente do Modo JSON.

```none
"hostPort": 80,
```

![Interface do usuário do novo aplicativo – exemplo de porta 80](media/dcos/dcos13.png)

O cluster DC/OS é implantado com um conjunto de agentes privados e públicos. Para que o cluster possa acessar aplicativos da Internet, você precisa implantar os aplicativos em um agente público. Para fazer isso, selecione a guia **Opcional** do assistente de Novo Aplicativo e insira **slave_public** nas **Funções de Recurso Aceitas**.

![Interface do usuário do novo aplicativo - configuração do agente público](media/dcos/dcos14.png)

Novamente na página principal do Marathon, você poderá ver o status da implantação para o contêiner.

![Interface do usuário da página principal do Marathon – status da implantação do contêiner](media/dcos/dcos7.png)

Ao alternar de volta para a interface do usuário Web DC/OS (http://localhost/), você verá que uma tarefa (nesse caso, um contêiner formatado do Docker) está em execução no cluster DC/OS.

![Interface do usuário Web DC/OS – tarefa em execução no cluster](media/dcos/dcos8.png)

Você também pode ver o nó de cluster no qual a tarefa está em execução.

![Interface do usuário Web DC/OS - nó do cluster de tarefa](media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>Dimensionar seus contêineres
Você pode usar a interface do usuário do Marathon para dimensionar a contagem de instâncias de um contêiner. Para fazer isso, navegue até a página **Marathon**, selecione o contêiner que você deseja dimensionar e clique no botão **Dimensionar**. Na caixa de diálogo **Dimensionar Aplicativo**, insira o número da instâncias de contêiner desejadas e selecione **Dimensionar Aplicativo**.

![Interface do usuário do Maratona - caixa de diálogo Dimensionar Aplicativo](media/dcos/dcos10.png)

Após a conclusão da operação de dimensionamento, você verá várias instâncias da mesma tarefa espalhados pelos agentes DC/OS.

![Painel de interface do usuário Web DC/OS – espalhamento de tarefa entre agentes](media/dcos/dcos11.png)

![Interface do usuário Web DC/OS - nós](media/dcos/dcos12.png)

## <a name="next-steps"></a>Próximas etapas
* [Trabalhar com DC/SO e a API do Marathon](container-service-mesos-marathon-rest.md)

Aprofunde seus conhecimentos sobre o Serviço de Contêiner do Azure com o Mesos

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
> 
> 




<!--HONumber=Nov16_HO2-->


