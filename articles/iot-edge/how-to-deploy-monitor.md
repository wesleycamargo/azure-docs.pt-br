---
title: "Implantar e monitorar módulos do Azure IoT Edge | Microsoft Docs"
description: "Gerenciar os módulos executados em dispositivos de borda"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/07/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cc7d1e290465d9254cbd7fe9e8ba71cc740b0368
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale---preview"></a>Implantar e monitorar módulos do IoT Edge em escala – versão prévia

O Azure IoT Edge possibilita mover a análise para a borda e fornece uma interface de nuvem, de modo que você possa gerenciar e monitorar seus dispositivos IoT Edge sem a necessidade de acessar cada um deles fisicamente. A capacidade de gerenciar dispositivos remotamente é cada vez mais importante, à medida que as soluções da Internet das Coisas ficam cada vez maiores e mais complexas. O Azure IoT Edge foi projetado para dar suporte a suas metas de negócios, independentemente de quantos dispositivos você adicionar.

Gerencie dispositivos individuais e implante módulos neles um de cada vez. No entanto, se desejar fazer alterações nos dispositivos em grande escala, crie uma **implantação do IoT Edge**. Implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos ao mesmo tempo, acompanhar o status e a integridade dos módulos e fazer alterações, quando necessário. 

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma implantação, você precisa conseguir especificar quais dispositivos você deseja afetar. O Azure IoT Edge identifica os dispositivos usando **marcações** no dispositivo gêmeo. Cada dispositivo pode ter várias marcações e você pode defini-las que qualquer modo que faça sentido para sua solução. Por exemplo, se você gerenciar um campus de edifícios inteligente, poderá adicionar as seguintes marcações a um dispositivo:

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
1. Selecione **IoT Edge (versão prévia)**.
1. Selecione **Adicionar implantação do IoT Edge**.

Há cinco etapas para criar uma implantação. As seções a seguir explicam cada uma delas. 

### <a name="step-1-name-and-label"></a>Etapa 1: Nome e rótulo

1. Dê à implantação um nome exclusivo. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Adicione rótulos para ajudar a acompanhar as implantações. Rótulos são pares de **Nome** e **Valor** que descrevem a implantação. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.
1. Selecione **Avançar** para ir para a etapa 2. 

### <a name="step-2-add-modules-optional"></a>Etapa 2: Adicionar módulos (opcional)

Há dois tipos de módulos que podem ser adicionados a uma implantação. O primeiro é um módulo baseado em um serviço do Azure, como a Conta de Armazenamento ou o Stream Analytics. O segundo é um módulo baseado em seu próprio código. Adicione vários módulos de um desses tipos a uma implantação. 

Se você criar uma implantação sem módulos, ela remove todos os módulos existentes dos dispositivos. 

>[!NOTE]
>O Azure Machine Learning e o Azure Functions ainda não dão suporte à implantação automatizada do serviço do Azure. Use a implantação de módulo personalizado para adicionar esses serviços manualmente à implantação. 

Para adicionar um módulo do Azure Stream Analytics, siga estas etapas:
1. Selecione **Importar módulo IoT Edge do Azure Stream Analytics**.
1. Use os menus suspensos para selecionar as instâncias de serviço do Azure que você deseja implantar.
1. Selecione **Salvar** para adicionar o módulo à implantação. 

Para adicionar um código personalizado como um módulo ou para adicionar um módulo de serviço do Azure manualmente, siga estas etapas:
1. Selecione **Adicionar módulo do IoT Edge**.
1. Dê um **Nome** ao módulo.
1. No campo **URI da Imagem**, insira a imagem de contêiner do Docker para o módulo. 
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
1. Selecione **Salvar** para adicionar o módulo à implantação. 

Depois de configurar todos os módulos para uma implantação, selecione **Avançar** para ir para a etapa 3.

### <a name="step-3-specify-routes-optional"></a>Etapa 3: Especificar rotas (opcional)

As rotas definem como os módulos se comunicam entre si em uma implantação. Especifique as rotas para a implantação e, em seguida, selecione **Avançar** para ir para a etapa 4. 

### <a name="step-4-target-devices"></a>Etapa 4: Direcionar dispositivos

Use a propriedade tags dos dispositivos para direcionar os dispositivos específicos que devem receber essa implantação. 

Como várias implantações podem direcionar o mesmo dispositivo, você deve atribuir a cada implantação um número de prioridade. Se houver um conflito, a implantação com a prioridade mais alta ganhará. Se duas implantações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará. 

1. Insira um inteiro positivo para a **Prioridade** da implantação.
1. Insira uma **Condição de destino** para determinar quais dispositivos serão direcionados com essa implantação. A condição se baseia nas marcações do dispositivo gêmeo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test'`. 
1. Selecione **Avançar** para ir para a etapa final.

### <a name="step-5-review-template"></a>Etapa 5: Examinar o modelo

Examine as informações da implantação e, em seguida, selecione **Enviar**.

## <a name="monitor-a-deployment"></a>Monitorar uma implantação

Para exibir os detalhes de uma implantação e monitorar os dispositivos que a executam, use as seguintes etapas:

1. Entre no [Portal do Azure][lnk-portal] e navegue até o seu Hub IoT. 
1. Selecione **IoT Edge (versão prévia)**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge][1]

1. Inspecione a lista de implantação. Para cada implantação, você pode exibir os seguintes detalhes:
   * **ID** – o nome da implantação.
   * **Condição de destino** – a marcação usada para definir os dispositivos direcionados.
   * **Prioridade** – o número de prioridade atribuído à implantação.
   * **Status do agente do IoT Edge** – o número de dispositivos que recebeu a implantação e seu status de integridade. 
   * **Módulos não íntegros** – o número de módulos nos erros do relatório de implantação. 
   * **Hora de criação** – o carimbo de data/hora de quando a implantação foi criada. Esse carimbo de data/hora é usado para desempate quando duas implantações têm a mesma prioridade. 
1. Selecione a implantação que deseja monitorar.  
1. Inspecione os detalhes da implantação. Use guias para exibir detalhes específicos sobre os dispositivos que receberam a implantação: 
   * **Direcionado** – os dispositivos do Edge que correspondem à condição de destino. 
   * **Aplicado** – os dispositivos direcionados do Edge que não são direcionados por outra implantação de prioridade mais alta. Esses são os dispositivos que realmente recebem a implantação. 
   * **Relatando êxito** – os dispositivos aplicados do Edge que relataram novamente para o serviço de que os módulos foram implantados com êxito. 
   * **Relatando falha** – os dispositivos aplicados do Edge que relataram novamente para o serviço de que um ou mais módulos não foram implantados com êxito. Para investigar o erro mais detalhadamente, você precisará se conectar remotamente a esses dispositivos e exibir os arquivos de log. 
   * **Relatando módulos não íntegros** – os dispositivos aplicados do Edge que relataram novamente para o serviço de que um ou mais módulos foram implantados com êxito, mas que agora estão relatando erros. 

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:
* Caso um dispositivo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa implantação tiver a prioridade mais alta para o dispositivo, essa implantação será aplicada ao dispositivo. 
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação com a prioridade mais alta. 
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino e não atenda à condição de destino de todas as outras implantações, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Depois que ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e usa a nova. 

Para modificar uma implantação, use as seguintes etapas: 

1. Entre no [Portal do Azure][lnk-portal] e navegue até o seu Hub IoT. 
1. Selecione **IoT Edge (versão prévia)**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge][1]

1. Selecione a implantação que deseja modificar. 
1. Faça atualizações nos seguintes campos: 
   * Condição de destino 
   * Rótulos 
   * Prioridade 
1. Selecione **Salvar**.
1. Siga as etapas em [Monitorar uma implantação][anchor-monitor] para observar as alterações serem distribuídas. 

## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos usam sua próxima implantação com a prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída. 

1. Entre no [Portal do Azure][lnk-portal] e navegue até o seu Hub IoT. 
1. Selecione **IoT Edge (versão prévia)**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge][1]

1. Use a caixa de seleção para selecionar a implantação que deseja excluir. 
1. Selecione **Excluir**.
1. Um prompt informará que essa ação excluirá essa implantação e reverterá para o estado anterior de todos os dispositivos.  Isso significa que uma implantação com uma prioridade mais baixa será aplicada.  Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se os clientes desejarem fazer isso, eles precisarão criar uma implantação sem nenhum módulo e implantá-la nos mesmos dispositivos. Selecione **Sim** se desejar continuar. 

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
