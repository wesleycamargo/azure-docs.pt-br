---
title: Implantar e monitorar módulos do Azure IoT Edge | Microsoft Docs
description: Gerenciar os módulos executados em dispositivos de borda
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: be52a57f10f286bded9a31d84b36a49717b94006
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029750"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implante e monitore módulos de IoT Edge em escala usando o portal do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

O Azure IoT Edge possibilita mover a análise para a borda e fornece uma interface de nuvem, de modo que você possa gerenciar e monitorar seus dispositivos IoT Edge sem a necessidade de acessar cada um deles fisicamente. A capacidade de gerenciar dispositivos remotamente é cada vez mais importante, à medida que as soluções da Internet das Coisas ficam cada vez maiores e mais complexas. O Azure IoT Edge foi projetado para dar suporte a suas metas de negócios, independentemente de quantos dispositivos você adicionar.

Gerencie dispositivos individuais e implante módulos neles um de cada vez. No entanto, se você quiser fazer alterações em dispositivos em grande escala, você pode criar uma **implantação automática de IoT Edge**, que faz parte do gerenciamento de dispositivo automático no Hub IoT. Implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos ao mesmo tempo, acompanhar o status e a integridade dos módulos e fazer alterações, quando necessário. 

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma implantação, você precisa conseguir especificar quais dispositivos você deseja afetar. O Azure IoT Edge identifica os dispositivos usando **marcações** no dispositivo gêmeo. Cada dispositivo pode ter várias marcações e você pode defini-las que qualquer modo que faça sentido para sua solução. Por exemplo, se você gerencia um campus de edifícios inteligentes, poderá adicionar as seguintes marcações a um dispositivo:

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

Para obter mais informações sobre dispositivos gêmeos e marcações, consulte [Entender e usar dispositivos gêmeos no Hub IoT][lnk-device-twin].

## <a name="create-a-deployment"></a>Criar uma implantação

1. No [Portal do Azure][lnk-portal], vá ao hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Adicionar implantação do IoT Edge**.

Há cinco etapas para criar uma implantação. As seções a seguir explicam cada uma delas. 

### <a name="step-1-name-and-label"></a>Etapa 1: Nome e rótulo

1. Dê à sua implantação um nome exclusivo com até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Adicione rótulos para ajudar a acompanhar as implantações. Rótulos são pares de **Nome** e **Valor** que descrevem a implantação. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.
1. Selecione **Avançar** para ir para a etapa 2. 

### <a name="step-2-add-modules-optional"></a>Etapa 2: Adicionar módulos (opcional)

Há dois tipos de módulos que podem ser adicionados a uma implantação. O primeiro é um módulo baseado em um serviço do Azure, como a Conta de Armazenamento ou o Stream Analytics. O segundo é um módulo baseado em seu próprio código. Adicione vários módulos de um desses tipos a uma implantação. 

Se você criar uma implantação sem módulos, ela remove todos os módulos atuais dos dispositivos. 

>[!NOTE]
>O Azure Machine Learning e o Azure Functions ainda não dão suporte à implantação automatizada do serviço do Azure. Use a implantação de módulo personalizado para adicionar esses serviços manualmente à implantação. 

Para adicionar um módulo do Azure Stream Analytics, siga estas etapas:
1. Na seção **Módulos de Implantação** da página, clique em **Adicionar**.
1. Selecione **módulo do Azure Stream Analytics**.
1. Escolha uma **Assinatura** no menu de lista suspensa.
1. Escolha um **trabalho Edge** no menu de lista suspensa.
1. Selecione **Salvar** para adicionar o módulo à implantação. 

Para adicionar um código personalizado como um módulo ou para adicionar um módulo de serviço do Azure manualmente, siga estas etapas:
1. No **as configurações do registro** seção da página, forneça os nomes e as credenciais para quaisquer registros de contêiner privado que contêm as imagens de módulo para essa implantação. O Agente Edge reportará erro 500 se ele não conseguir achar a credencial de registro de contêiner para uma imagem de docker.
1. Na seção **Módulos de Implantação** da página, clique em **Adicionar**.
1. Selecione **Módulo IoT Edge**.
1. Dê um **Nome** ao módulo.
1. No campo **URI da Imagem**, insira a imagem de contêiner para o módulo. 
1. Especifique qualquer **Opção de criação de contêiner** que deverá ser passada para o contêiner. Para obter mais informações, consulte [docker create][lnk-docker-create].
1. Use o menu suspenso para selecionar uma **Política de reinicialização**. Escolha uma das seguintes opções: 
   * **Sempre** – o módulo sempre é reiniciado se é desligado por qualquer motivo.
   * **Nunca** – o módulo nunca é reiniciado se é desligado por qualquer motivo.
   * **Se Houver Falha** – o módulo é reiniciado se ele falha, mas não se ele é desligado corretamente. 
   * **Se Não Estiver Íntegro** – o módulo é reiniciado se ele falha ou se retorna um status não íntegro. Cabe a cada módulo implementar a função de status da integridade. 
1. Use o menu suspenso para selecionar o **Status** desejado do módulo. Escolha uma das seguintes opções:
   * **Em Execução** – essa é a opção padrão. O módulo será iniciado imediatamente depois de ser implantado.
   * **Parado** – depois de ser implantado, o módulo permanecerá ocioso até que seja chamado para ser iniciado por você ou por outro módulo.
1. Selecione **Habilitar** se desejar adicionar marcações ou as propriedades desejadas ao módulo gêmeo. 
1. Entre **variáveis Ambientais** para este módulo. Variáveis ambientais fornecem informação suplementar a um módulo facilitando o processo de configuração.
1. Selecione **Salvar** para adicionar o módulo à implantação. 

Depois de configurar todos os módulos para uma implantação, selecione **Avançar** para ir para a etapa 3.

### <a name="step-3-specify-routes-optional"></a>Etapa 3: Especificar rotas (opcional)

As rotas definem como os módulos se comunicam entre si em uma implantação. Por padrão, o assistente fornece uma rota chamada **rota** e definida como **FROM /* INTO $upstream**, que significa que quaisquer mensagens de saída por quaisquer módulos são enviadas para o seu Hub IoT.  

Adicionar ou atualizar as rotas com informações de [declarar rotas](module-composition.md#declare-routes), em seguida, selecione **próximo** para continuar para a seção de revisão.


### <a name="step-4-target-devices"></a>Etapa 4: Direcionar dispositivos

Use a propriedade tags dos dispositivos para direcionar os dispositivos específicos que devem receber essa implantação. 

Como várias implantações podem direcionar o mesmo dispositivo, você deve atribuir a cada implantação um número de prioridade. Se houver um conflito, a implantação com a prioridade mais alta ganhará (maiores valores indicam maiores prioridades). Se duas implantações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará. 

1. Insira um inteiro positivo para a **Prioridade** da implantação. No caso de duas ou mais implantações serem direcionadas ao mesmo dispositivo, será aplicada a implantação com o maior valor numérico para Prioridade.
1. Insira uma **Condição de destino** para determinar quais dispositivos serão direcionados com essa implantação. A condição é baseada nas marcações do dispositivo gêmeo ou nas propriedades desejadas do dispositivo gêmeo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test'` ou `properties.desired.devicemodel='4000x'`. 
1. Selecione **Avançar** para ir para a etapa final.

### <a name="step-5-review-template"></a>Etapa 5: Examinar o modelo

Examine as informações da implantação e, em seguida, selecione **Enviar**.

## <a name="monitor-a-deployment"></a>Monitorar uma implantação

Para exibir os detalhes de uma implantação e monitorar os dispositivos que a executam, use as seguintes etapas:

1. Entre no [Portal do Azure][lnk-portal] e navegue até o seu Hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge][1]

1. Inspecione a lista de implantação. Para cada implantação, você pode exibir os seguintes detalhes:
   * **ID** – o nome da implantação.
   * **Condição de destino** – a marcação usada para definir os dispositivos direcionados.
   * **Prioridade** – o número de prioridade atribuído à implantação.
   * **Métricas de sistema** - **Desejados** especifica o número de dispositivos gêmeos em Hub IoT que batem com a condição desejada, e **Aplicados** especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos seus módulos gêmeos no Hub IoT. 
   * **Métricas de dispositivo** - Uma métrica de dispositivo que especifica o número de dispositivos do Edge no êxito do relatório de implantação do tempo de execução ou erros do cliente do IoT Edge.
   * **Hora de criação** – o carimbo de data/hora de quando a implantação foi criada. Esse carimbo de data/hora é usado para desempate quando duas implantações têm a mesma prioridade. 
2. Selecione a implantação que deseja monitorar.  
3. Inspecione os detalhes da implantação. Você pode usar guias para revisar os detalhes da implantação.

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:
* Caso um dispositivo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa implantação tiver a prioridade mais alta para o dispositivo, essa implantação será aplicada ao dispositivo. 
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação com a prioridade mais alta. 
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino e não atenda à condição de destino de todas as outras implantações, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Depois que ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e usa a nova. 

Para modificar uma implantação, use as seguintes etapas: 

1. Entre no [Portal do Azure][lnk-portal] e navegue até o seu Hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge][1]

1. Selecione a implantação que deseja modificar. 
1. Faça atualizações nos seguintes campos: 
   * Condição de destino 
   * Rótulos 
   * Prioridade 
1. Clique em **Salvar**.
1. Siga as etapas em [Monitorar uma implantação][anchor-monitor] para observar as alterações serem distribuídas. 

## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos usam sua próxima implantação com a prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída. 

1. Entre no [Portal do Azure][lnk-portal] e navegue até o seu Hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge][1]

1. Use a caixa de seleção para selecionar a implantação que deseja excluir. 
1. Selecione **Excluir**.
1. Um prompt informará que essa ação excluirá essa implantação e reverterá para o estado anterior de todos os dispositivos.  Isso significa que uma implantação com uma prioridade mais baixa será aplicada.  Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se você quiser remover todos os módulos de seu dispositivo, uma implantação com zero módulos e implante-a para os mesmos dispositivos. Escolha **Sim** para continuar. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [Implantar módulos em dispositivos do Edge][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
