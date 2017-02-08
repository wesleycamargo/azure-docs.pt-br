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
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: f91f694dc8660cc55a9aa02bb4941083441d8acf


---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Criar um Hub IoT usando o portal do Azure
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução
Este artigo descreve como localizar o serviço de Hub IoT no portal do Azure e como criar e gerenciar hubs IoT.

## <a name="where-to-find-iot-hubs"></a>Onde encontrar Hubs IoT:
Existem vários lugares onde você pode encontrar hubs IoT.

1. **+ Novo**: o **Hub IoT do Azure** é um serviço IoT e pode ser encontrado na categoria **Internet das Coisas** em **+ Novo**, semelhante a outros serviços.
2. Os Hubs IoT também podem ser acessados por meio do Marketplace como o serviço hero em **Internet das Coisas**.

## <a name="create-an-iot-hub"></a>Crie um hub IoT
Você pode criar um Hub IoT usando os métodos a seguir:

* A criação de um Hub IoT por meio da opção **+ Novo** leva à folha mostrada na captura de tela a seguir. As etapas para a criação do Hub IoT usando este método e pelo marketplace são idênticas.
* Criação de um Hub IoT por meio do Marketplace: clicar em **Criar** abre uma folha idêntica à folha anterior da experiência de **+Novo**. As próximas seções listam as várias etapas envolvidas na criação de um Hub IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Escolher o nome do Hub IoT
Para criar um Hub IoT, você deve dar um nome a ele. Esse nome deve ser exclusivo entre os Hubs IoT. Não é permitida a duplicação de hubs no back-end da solução, portanto é recomendável que esse hub tenha o nome mais exclusivo possível.

### <a name="choose-the-pricing-tier"></a>Escolher a camada de preços
Você pode escolher entre quatro camadas: **Gratuita**, **Padrão 1**, **Padrão 2** e **Padrão S3**. O tipo gratuito só permite a conexão de 500 dispositivos ao Hub IoT e até 8000 mensagens por dia.

**Padrão S1**: a edição S1 de Hubs IoT foi projetada para soluções IoT com um grande número de dispositivos gerando quantidades relativamente pequenas de dados por dispositivo. Cada unidade da edição S1 permite até 400.000 mensagens por dia entre todos os dispositivos conectados.

**Padrão S2**: a edição S2 de Hub IoT foi projetada para soluções IoT nas quais os dispositivos geram grandes quantidades de dados. Cada unidade da edição S2 permite a transmissão de até seis milhões de mensagens por dia entre todos os dispositivos conectados.

**S3 Padrão**: a edição S3 do Hub IoT é projetada para soluções de IoT que geram grandes quantidades de dados. Cada unidade da edição S3 permite a transmissão de até 300 milhões de mensagens por dia entre todos os dispositivos conectados.

![][4]

> [!NOTE]
> O Hub IoT só permite um hub gratuito por assinatura do Azure.
> 
> 

### <a name="iot-hub-units"></a>Unidades do Hub IoT
Uma unidade de Hub IoT inclui um determinado número de mensagens por dia. O número total de mensagens com suporte para esse hub é o número de unidades multiplicado pelo número de mensagens por dia nessa camada. Por exemplo, se você quiser que o Hub IoT dê suporte à entrada de 700.000 mensagens, escolha duas unidades da camada S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Dispositivo para partições de nuvem e grupo de recursos
Você pode alterar o número de partições para um Hub IoT. As partições padrão são definidas como 4; no entanto, você pode escolher um número diferente de partições em uma lista suspensa.

Para grupos de recursos, não será necessário criar explicitamente um grupo de recursos vazio. Ao criar um recurso, você pode optar por criar um novo grupo de recursos ou usar um grupo de recursos existente.

![][5]

### <a name="choose-subscriptions"></a>Escolher assinaturas
O Hub IoT do Azure mostra automaticamente a lista de assinaturas do Azure à qual a conta do usuário está vinculada. Você pode escolher uma dessas opções para associar o Hub IoT à assinatura do Azure.

### <a name="choose-the-location"></a>Escolher o local
A opção de local oferece uma lista das regiões em que o Hub IoT é oferecido. O Hub IoT está disponível para implantação nos seguintes locais: Leste da Austrália, Sudeste da Austrália, Leste da Ásia, Sudeste da Ásia, Norte da Europa, Oeste da Europa, Leste do Japão, Oeste do Japão, Leste dos EUA, Oeste dos EUA.

### <a name="create-the-iot-hub"></a>Criar o Hub IoT
Quando todas as etapas anteriores forem concluídas, o Hub IoT estará pronto para ser criado. Clique em **Criar** para iniciar o processo de back-end de criação desse Hub IoT com as opções específicas e o implantar no local especificado.

Pode levar alguns minutos para que o Hub IoT seja criado, já que a implantação de back-end é demorada nos servidores do local apropriado.

## <a name="change-the-settings-of-the-iot-hub"></a>Alterar as configurações do Hub IoT
Você poderá alterar as configurações de um Hub IoT existente depois que ele for criado na folha Hub IoT.

![][8]

**Políticas de acesso compartilhado**: essas políticas definem as permissões para que dispositivos e serviços se conectem ao Hub IoT. Você pode acessar essas políticas clicando em **Políticas de acesso compartilhado** em **Geral**. Nessa folha, você pode modificar as políticas existentes ou adicionar uma nova política.

### <a name="create-a-policy"></a>Criar uma política
* Clique em **Adicionar** para abrir uma folha. Aqui você poderá inserir o nome da nova política e as permissões que deseja associar a essa política, como mostrado na figura a seguir.
  
    Há várias permissões que podem ser associadas a essas políticas compartilhadas. As duas primeiras políticas, **Leitura do Registro** e **Gravação do Registro**, concedem direitos de acesso de leitura e gravação ao repositório de identidade do dispositivo ou ao registro de identidade. A escolha da opção de gravação escolhe automaticamente a opção de leitura.
  
     A política **Conexão de serviço** concede permissão para acessar os pontos de extremidade do lado da nuvem, como o grupo de consumidores para serviços que se conectam ao Hub IoT. A política **Conexão de dispositivo** concede permissões para enviar e receber mensagens nos pontos de extremidade do lado do dispositivo do Hub IoT.
* Clique em **Criar** para adicionar essa política recém-criada à lista existente.

![][10]

## <a name="messaging"></a>Mensagens
Clique em **Sistema de Mensagens** para exibir uma lista de propriedades do sistema de mensagens para o Hub IoT que está sendo modificado. Existem dois tipos principais de propriedades que você pode modificar ou copiar: **Nuvem para o Dispositivo** e **Dispositivo para a Nuvem**.

* Configurações **Nuvem para Dispositivo**: essa configuração tem duas subconfigurações: **TTL (vida útil) da Nuvem para Dispositivo** e **Tempo de retenção** para as mensagens. Quando o Hub IoT é criado, essas duas configurações são criadas com um valor padrão de uma hora. Para ajustar esses valores, use os controles deslizantes ou digite os valores.
* **Dispositivo para a Nuvem** : essa configuração tem várias subconfigurações, algumas das quais são nomeadas/atribuídas quando o Hub IoT é criado e só podem ser copiadas para outras subconfigurações que sejam personalizáveis. Essas configurações estão listadas na próxima seção.

**Partições**: esse valor é definido quando o Hub IoT é criado e pode ser alterado por meio dessa configuração.

**Nome compatível e ponto de extremidade do Hub de Eventos**: quando o Hub IoT é criado, um Hub de Eventos é criado internamente e talvez você tenha que acessá-lo sob determinadas circunstâncias. O nome e o ponto de extremidade compatíveis com esse Hub de Eventos não podem ser personalizados, mas estão disponíveis para uso por meio do botão **Copiar** .

**Tempo de Retenção**: definido para um dia por padrão, mas pode ser personalizado para outros valores usando a lista suspensa. Esse valor está em dias para Dispositivo para Nuvem, e não em horas, que é a configuração semelhante de Nuvem para Dispositivo.

**Grupos de Consumidores**: os Grupos de Consumidores são uma configuração semelhante a outros sistemas de mensagens que podem ser usados para extrair dados de maneiras específicas para a conexão de outros aplicativos ou serviços ao Hub IoT. Todos os Hub IoT são criados com um grupo de consumidores padrão. No entanto, você pode adicionar ou excluir grupos de consumidores em seus Hubs IoT.

> [!NOTE]
> O grupo de consumidores padrão não pode ser editado ou excluído.
> 
> 

![][11]

## <a name="pricing-and-scale"></a>Preços e dimensionamento
O preço de um Hub IoT existente pode ser alterado por meio das configurações de **Preço** , com as seguintes exceções:

* Na implementação atual, um Hub IoT com um SKU gratuito não pode trocar camadas com um dos SKUs pagos ou vice-versa.
* Só pode haver um Hub IoT de camada gratuita na assinatura do Azure.

![][12]

Mudar de uma camada alta (S2 ou S3) para uma camada baixa (S1 ou S2) só é permitido quando o número de mensagens enviadas naquele dia não está em conflito. Por exemplo, se o número de mensagens por dia exceder 400.000, a camada do Hub IoT poderá ser alterada. No entanto, se você alterar para a camada S1, o Hub IoT estará limitado para esse dia.

## <a name="delete-the-iot-hub"></a>Excluir o Hub IoT
Você pode navegar até o Hub IoT que deseja excluir clicando em **Procurar**e escolhendo o hub apropriado para excluir. Clique no botão **Excluir** abaixo do nome do Hub IoT para excluí-lo.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciamento em massa de dispositivos IoT][lnk-bulk]
* [Métricas do Hub IoT][lnk-metrics]
* [Monitoramento de operações][lnk-monitor]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Simulando um dispositivo com o SDK do Gateway do IoT][lnk-gateway]
* [Proteger sua solução de IoT desde o início][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md



<!--HONumber=Dec16_HO1-->


