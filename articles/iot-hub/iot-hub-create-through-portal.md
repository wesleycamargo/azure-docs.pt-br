---
title: Usar o Portal do Azure para criar um Hub IoT | Microsoft Docs
description: "Como criar, gerenciar e excluir Hubs IoT do Azure por meio do Portal do Azure. Inclui informações sobre tipos de preço, escala, segurança e configurações de mensagens."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 7a68a2764d17b53164ded5096ddee68ab8c723ed
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
<a id="create-an-iot-hub-using-the-azure-portal" class="xliff"></a>
# Criar um Hub IoT usando o portal do Azure
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

<a id="introduction" class="xliff"></a>
## Introdução
Este artigo descreve como localizar o serviço de Hub IoT no portal do Azure e como criar e gerenciar hubs IoT.

<a id="where-to-find-iot-hubs" class="xliff"></a>
## Onde encontrar Hubs IoT:
Existem vários lugares onde você pode encontrar hubs IoT.

1. **+ Novo**: o **Hub IoT do Azure** é um serviço IoT e pode ser encontrado na categoria **Internet das Coisas** em **+ Novo**, semelhante a outros serviços.
2. Os Hubs IoT também podem ser acessados por meio do Marketplace como o serviço hero em **Internet das Coisas**.

<a id="create-an-iot-hub" class="xliff"></a>
## Crie um hub IoT
Você pode criar um Hub IoT usando os métodos a seguir:

* A criação de um Hub IoT por meio da opção **+ Novo** leva à folha mostrada na captura de tela a seguir. As etapas para a criação do Hub IoT usando este método e pelo marketplace são idênticas.
* Criação de um Hub IoT por meio do Marketplace: clicar em **Criar** abre uma folha idêntica à folha anterior da experiência de **+Novo**. As próximas seções listam as várias etapas envolvidas na criação de um Hub IoT.

<a id="choose-the-name-of-the-iot-hub" class="xliff"></a>
### Escolher o nome do Hub IoT
Para criar um Hub IoT, você deve dar um nome a ele. Esse nome deve ser exclusivo entre os Hubs IoT. Não é permitida a duplicação de hubs no back-end da solução, portanto é recomendável que esse hub tenha o nome mais exclusivo possível.

<a id="choose-the-pricing-tier" class="xliff"></a>
### Escolher a camada de preços
Você pode escolher entre quatro camadas: **Gratuita**, **Padrão 1**, **Padrão 2** e **Padrão S3**. O tipo gratuito só permite a conexão de 500 dispositivos ao Hub IoT e até 8000 mensagens por dia.

**Padrão S1**: a edição S1 de Hubs IoT foi projetada para soluções IoT com um grande número de dispositivos gerando quantidades relativamente pequenas de dados por dispositivo. Cada unidade da edição S1 permite até 400.000 mensagens por dia entre todos os dispositivos conectados.

**Padrão S2**: a edição S2 de Hub IoT foi projetada para soluções IoT nas quais os dispositivos geram grandes quantidades de dados. Cada unidade da edição S2 permite a transmissão de até seis milhões de mensagens por dia entre todos os dispositivos conectados.

**S3 Padrão**: a edição S3 do Hub IoT é projetada para soluções de IoT que geram grandes quantidades de dados. Cada unidade da edição S3 permite a transmissão de até 300 milhões de mensagens por dia entre todos os dispositivos conectados.

![][4]

> [!NOTE]
> O Hub IoT só permite um hub gratuito por assinatura do Azure.
> 
> 

<a id="iot-hub-units" class="xliff"></a>
### Unidades do Hub IoT
O número de mensagens permitidas por unidade ao dia depende do tipo de preço do seu hub. Por exemplo, se você quiser que o Hub IoT dê suporte à entrada de 700.000 mensagens, escolha duas unidades da camada S1.

<a id="device-to-cloud-partitions-and-resource-group" class="xliff"></a>
### Dispositivo para partições de nuvem e grupo de recursos
Você pode alterar o número de partições para um Hub IoT. As partições padrão são definidas como 4; no entanto, você pode escolher um número diferente de partições em uma lista suspensa.

Para grupos de recursos, não será necessário criar explicitamente um grupo de recursos vazio. Ao criar um recurso, você pode optar por criar um novo grupo de recursos ou usar um grupo de recursos existente.

![][5]

<a id="choose-subscriptions" class="xliff"></a>
### Escolher assinaturas
O Hub IoT do Azure mostra automaticamente a lista de assinaturas do Azure à qual a conta do usuário está vinculada. Você pode escolher uma dessas opções para associar o Hub IoT à assinatura do Azure.

<a id="choose-the-location" class="xliff"></a>
### Escolher o local
A opção de local oferece uma lista das regiões em que o Hub IoT é oferecido. O Hub IoT está disponível para implantação nos seguintes locais: Leste da Austrália, Sudeste da Austrália, Leste da Ásia, Sudeste da Ásia, Norte da Europa, Oeste da Europa, Leste do Japão, Oeste do Japão, Leste dos EUA, Oeste dos EUA.

<a id="create-the-iot-hub" class="xliff"></a>
### Criar o Hub IoT
Quando todas as etapas anteriores forem concluídas, o Hub IoT estará pronto para ser criado. Clique em **Criar** para iniciar o processo de back-end de criação desse Hub IoT com as opções específicas e o implantar no local especificado.

Pode levar alguns minutos para que o Hub IoT seja criado, já que a implantação de back-end é demorada nos servidores do local apropriado.

<a id="change-the-settings-of-the-iot-hub" class="xliff"></a>
## Alterar as configurações do Hub IoT
Você poderá alterar as configurações de um Hub IoT existente depois que ele for criado na folha Hub IoT.

![][8]

**Políticas de acesso compartilhado**: essas políticas definem as permissões para que dispositivos e serviços se conectem ao Hub IoT. Você pode acessar essas políticas clicando em **Políticas de acesso compartilhado** em **Geral**. Nessa folha, você pode modificar as políticas existentes ou adicionar uma nova política.

<a id="create-a-policy" class="xliff"></a>
### Criar uma política
* Clique em **Adicionar** para abrir uma folha. Aqui, você poderá inserir o nome da nova política e as permissões que quer associar a essa política, como mostrado na seguinte figura:
  
    Há várias permissões que podem ser associadas a essas políticas compartilhadas. As duas primeiras políticas, **Leitura do Registro** e **Gravação do Registro**, concedem direitos de acesso de leitura e gravação ao repositório de identidade do dispositivo ou ao registro de identidade. A escolha da opção de gravação escolhe automaticamente a opção de leitura.
  
     A política **Conexão de serviço** concede permissão para acessar os pontos de extremidade do lado da nuvem, como o grupo de consumidores para serviços que se conectam ao Hub IoT. A política **Conexão de dispositivo** concede permissões para enviar e receber mensagens nos pontos de extremidade do lado do dispositivo do Hub IoT.
* Clique em **Criar** para adicionar essa política recém-criada à lista existente.

![][10]

<a id="endpoints" class="xliff"></a>
## Pontos de extremidade
Clique em **Pontos de extremidade** para exibir uma lista de pontos de extremidade do Hub IoT que está sendo alterado. Há dois tipos principais de ponto de extremidade: aqueles que são criados no Hub IoT e aqueles que você adicionou ao Hub IoT depois de sua criação.

![][11]

<a id="built-in-endpoints" class="xliff"></a>
### Pontos de extremidade internos
Há dois pontos de extremidade internos principais: **Comentários da nuvem para dispositivo** e **Eventos**.

* Configurações de **Comentários da nuvem para dispositivo**: essa configuração tem duas subconfigurações: **TTL (vida útil) da Nuvem para Dispositivo** e **Período de retenção** (em horas) para as mensagens. Ao criar um Hub IoT, ambas essas configurações têm o valor padrão de uma hora. Para ajustar essas configurações, use os controles deslizantes ou digite os valores.
* Configurações de **Eventos**: essa configuração tem várias subconfigurações, algumas das quais são somente leitura. A lista a seguir descreve cada uma:

    * **Partições**: defina um valor padrão durante a criação do Hub IoT. Você pode alterar a quantidade de partições nesta configuração.

    * **Nome compatível e ponto de extremidade do Hub de Eventos**: quando o Hub IoT é criado, um Hub de Eventos é criado internamente e talvez você tenha que acessá-lo sob determinadas circunstâncias. Não é possível personalizar os valores de nome e ponto de extremidade compatíveis com o Hub de evento, mas você pode copiá-los clicando em **Copiar**.

    * **Período de retenção**: definido como um dia por padrão, mas pode ser personalizado para outros valores usando a lista suspensa. Esse valor é em dias para a configuração de dispositivo para a nuvem.

    * **Grupos de consumidores**: os Grupos de consumidores são uma configuração semelhante a outros sistemas de mensagens que podem ser usados para extrair dados de maneiras específicas para a conexão de outros aplicativos ou serviços ao Hub IoT. Todos os Hub IoT são criados com um grupo de consumidores padrão. No entanto, você pode adicionar ou excluir grupos de consumidores em seus Hubs IoT com esta configuração.

    > [!NOTE]
    > O grupo de consumidores padrão não pode ser editado ou excluído.
    > 
    > 

<a id="custom-endpoints" class="xliff"></a>
### Pontos de extremidade personalizados
Você pode adicionar pontos de extremidade personalizados ao Hub IoT usando o portal. Na folha **Pontos de extremidade**, clique em **Adicionar** na parte superior para abrir a folha **Adicionar ponto de extremidade**. Insira as informações necessárias e clique em **OK**. O ponto de extremidade personalizado é exibido na folha **Pontos de extremidade** principal.

![][13]

Você pode ler mais sobre pontos de extremidade personalizados em [Referência — Pontos de extremidade do Hub IoT][lnk-devguide-endpoints].

<a id="routes" class="xliff"></a>
## Rotas
Clique em **Rotas** para gerenciar como o Hub IoT envia suas mensagens do dispositivo para a nuvem.

![][14]

Você pode adicionar rotas ao Hub IoT clicando em **Adicionar** na parte superior da folha **Rotas*** inserindo as informações necessárias e clicando em **OK**. A rota é listada na folha **Rotas** principal. Para editar uma rota, clique nela na lista de rotas. Para habilitar uma rota, clique nela na lista de rotas e defina o botão **Habilitar/Desabilitar** como **Desabilitar**. Clique em **OK** na parte inferior da folha para salvar a alteração.

![][15]

<a id="pricing-and-scale" class="xliff"></a>
## Preços e dimensionamento
O preço de um Hub IoT existente pode ser alterado por meio das configurações de **Preço** , com as seguintes exceções:

* Na implementação atual, um Hub IoT com um SKU gratuito não pode trocar camadas com um dos SKUs pagos ou vice-versa.
* Só pode haver um Hub IoT de camada gratuita na assinatura do Azure.

![][12]

Mudar de uma camada alta (S2 ou S3) para uma camada baixa (S1 ou S2) só é permitido quando o número de mensagens enviadas naquele dia não está em conflito. Por exemplo, se o número de mensagens por dia exceder 400.000, a camada do Hub IoT poderá ser alterada. No entanto, se você alterar para a camada S1, o Hub IoT estará limitado para esse dia.

<a id="delete-the-iot-hub" class="xliff"></a>
## Excluir o Hub IoT
Você pode navegar até o Hub IoT que deseja excluir clicando em **Procurar**e escolhendo o hub apropriado para excluir. Clique no botão **Excluir** abaixo do nome do Hub IoT para excluí-lo.

<a id="next-steps" class="xliff"></a>
## Próximas etapas
Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciamento em massa de dispositivos IoT][lnk-bulk]
* [Métricas do Hub IoT][lnk-metrics]
* [Monitoramento de operações][lnk-monitor]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Simulando um dispositivo com IoT Edge][lnk-iotedge]
* [Proteger sua solução de IoT desde o início][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md

