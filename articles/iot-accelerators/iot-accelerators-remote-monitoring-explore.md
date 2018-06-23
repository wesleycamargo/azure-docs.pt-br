---
title: Introdução à solução de monitoramento remoto – Azure | Microsoft Docs
description: Este tutorial usa cenários simulados para apresentar o acelerador de solução de monitoramento remoto. Esses cenários são criados quando você implanta o acelerador de solução de monitoramento remoto pela primeira vez.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: e354e110df8d168e153c50de6f7aad08dd416464
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627800"
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-solution-accelerator"></a>Explorar os recursos do acelerador da solução de Monitoramento Remoto

Este tutorial mostra os principais recursos de solução de Monitoramento Remoto. Para apresentar esses recursos, o tutorial mostra cenários comuns de cliente usando um aplicativo de IoT simulado para uma empresa chamada Contoso.

O tutorial ajuda a compreender os cenários típicos de IoT que a solução de Monitoramento Remoto fornece para uso imediato.

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Visualizar e filtrar os dispositivos no painel
> * Responder a um alerta
> * Atualizar o firmware em seus dispositivos
> * Organizar seus ativos
> * Parar e iniciar os dispositivos simulados

O vídeo a seguir mostra um passo a passo da solução de Monitoramento Remoto:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-28-An-introduction-to-Azure-IoT-through-the-new-Remote-Monitoring-Preconfigured-Solution/Player]

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisa de uma instância da solução de Monitoramento Remoto implantada na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Implantar o acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-deploy.md).

## <a name="the-contoso-sample-iot-deployment"></a>A implantação de IoT de exemplo da Contoso

Você pode usar a implantação de IoT de exemplo da Contoso para compreender os cenários básicos que a solução de Monitoramento Remoto fornece para uso imediato. Esses cenários são baseados em implantações de IoT reais. Provavelmente, você vai optar por personalizar a solução de Monitoramento Remoto para atender aos seus requisitos específicos, mas o exemplo da Contoso ajuda a aprender os conceitos básicos.

> [!NOTE]
> Se você usou o CLI para implantar o acelerador de solução, o arquivo `deployment-{your deployment name}-output.json` contém informações sobre implantação, como a URL para acessar a amostra implantada.

O exemplo da Contoso provisiona um conjunto de dispositivos simulados e as regras que agem sobre eles. Depois de compreender os cenários básicos, você poderá continuar explorando mais os recursos da solução em [Perform advanced device monitoring using the Remote Monitoring solution](iot-accelerators-remote-monitoring-monitor.md) (Executar o monitoramento avançado de dispositivos usando a solução de Monitoramento Remoto).

A Contoso é uma empresa que gerencia uma variedade de ativos em diferentes ambientes. A Contoso planeja usar a eficiência dos aplicativos de IoT baseados em nuvem para monitorar e gerenciar remotamente vários ativos de um aplicativo centralizado. As seções a seguir fornecem um resumo da configuração inicial do exemplo da Contoso:

> [!NOTE]
> A demonstração da Contoso é apenas uma maneira de provisionar dispositivos simulados e criar regras. Outras opções de provisionamento incluem a criação de seus próprios dispositivos personalizados. Para saber mais sobre como criar seus próprios dispositivos e regras, consulte [Manage and configure your devices](iot-accelerators-remote-monitoring-manage.md) (Gerenciar e configurar seus dispositivos) e [Detect issues using threshold-based rules](iot-accelerators-remote-monitoring-automate.md) (Detectar problemas usando regras baseadas em limite).

### <a name="contoso-devices"></a>Dispositivos da Contoso

A Contoso usa diferentes tipos de dispositivos inteligentes. Esses dispositivos atendem a diferentes funções para a empresa, enviando diversos fluxos de telemetria. Além disso, cada tipo de dispositivo tem diferentes propriedades e métodos com suporte.

A tabela a seguir mostra um resumo dos tipos de dispositivos provisionados:

| Tipo de dispositivo        | Telemetria                                  | propriedades                                  | Marcas                    | Métodos                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| Resfriador            | Temperatura, umidade, pressão            | Tipo, versão de firmware, modelo               | Localização, piso, campus | Reiniciar, atualização de firmware, versão da válvula de emergência, aumento de pressão                          |
| Dispositivo de protótipo | Temperatura, pressão, localização geográfica        | Tipo, versão de firmware, modelo               | Localização, modo          | Reiniciar, atualização de firmware, mover dispositivo, parar dispositivo, liberação de temperatura, aumento de temperatura |
| Motor             | Nível do tanque de combustível, sensor de refrigeração, vibração | Tipo, versão de firmware, modelo               | Localização, piso, campus | Atualização de firmware, tanque vazio, tanque cheio                                              |
| Caminhão              | Localização geográfica, velocidade, temperatura da carga     | Tipo, versão de firmware, modelo               | Localização, carregamento          | Reduzir temperatura da carga, aumentar temperatura da carga, atualização de firmware                         |
| Elevador           | Piso, vibração, temperatura              | Tipo, versão de firmware, modelo, localização geográfica | Localização, campus        | Parar elevador, iniciar elevador, atualização de firmware                                               |

> [!NOTE]
> O exemplo de demonstração da Contoso provisiona dois dispositivos por tipo. Para cada tipo, um funciona corretamente dentro dos limites definidos como normais pela Contoso e o outro tem algum tipo de mau funcionamento. Na próxima seção, você aprenderá sobre as regras definidas pela Contoso para os dispositivos. Essas regras definem os limites do comportamento correto.

### <a name="contoso-rules"></a>Regras da Contoso

Os operadores na Contoso sabem os limites que determinam se um dispositivo está funcionando corretamente. Por exemplo, um resfriador não estará funcionando corretamente se a pressão informada for maior que 250 PSI. A tabela a seguir mostra as regras baseadas em limites que a Contoso definiu para cada tipo de dispositivo:

| Nome da Regra | DESCRIÇÃO | Limite | Severity | Dispositivos afetados |
| --------- | ----------- | --------- | -------- | ---------------- |
| Pressão de resfriador muito alta | Alerta quando os resfriadores alcançam níveis de pressão maiores que os normais   |P > 250 psi       | Crítico | Resfriadores            |
| Temp. do dispositivo de protótipo muito alta  | Alerta quando os dispositivos de protótipo alcançam níveis de temperatura maiores que os normais  |T>80&deg; F |Crítico | Dispositivos de protótipo |
| Tanque do motor vazio  | Alerta quando o tanque de combustível do motor fica vazio                     | F < 5 galões | Informações     | Motores             |
| Temperatura da carga maior que a normal | Alerta quando a temperatura da carga do caminhão está acima do normal                 | T<45&deg; F |Aviso  | Caminhões              |
| Vibração do elevador interrompida      | Alerta quando o elevador é completamente parado (com base no nível de vibração)                     | V < 0,1 mm |Aviso  | Elevadores           |

### <a name="operate-the-contoso-sample-deployment"></a>Operar a implantação de exemplo da Contoso

Agora você já viu a configuração inicial no exemplo da Contoso. As seções a seguir descrevem os três cenários no exemplo da Contoso que ilustram como um operador pode usar o acelerador de solução.

## <a name="respond-to-a-pressure-alert"></a>Responder a um alerta de pressão

Este cenário mostra como identificar e responder a um alerta que é disparado por um dispositivo resfriador. O Resfriador está localizado em Redmond, no prédio 43, piso 2.

Como operador, você vê no painel que há um alerta relacionado à pressão de um resfriador. Você pode exibir uma panorâmica e ampliar o mapa para ver mais detalhes.

1. Na página **Painel**, na grade **Alertas**, você pode ver o alerta **Pressão do resfriador muito alta**. O Resfriador também é realçado no mapa:

    ![O painel mostra o alerta de pressão e o dispositivo no mapa](./media/iot-accelerators-remote-monitoring-explore/dashboardalarm.png)

1. Para navegar até a página de **Manutenção**, escolha **Manutenção** no menu de navegação. Na página **Manutenção**, você pode exibir os detalhes da regra que disparou o alerta de pressão do resfriador.

1. A lista de alertas mostra o número de vezes que o alerta foi disparado, as confirmações e os alertas abertos e fechados:

    ![A página manutenção mostra a lista de alertas que foram disparados](./media/iot-accelerators-remote-monitoring-explore/maintenancealarmlist.png)

1. O último alerta na lista é o mais recente. Clique no alerta **Pressão do Resfriador Muito Alta** para exibir os dispositivos associados e a telemetria. A telemetria mostra os picos de pressão para o resfriador:

    ![A página de manutenção mostra a telemetria para o alerta selecionado](./media/iot-accelerators-remote-monitoring-explore/maintenancetelemetry.png)

Você identificou o problema que disparou o alerta e o dispositivo associado. Como operador, as próximas etapas serão para confirmar o alerta e mitigar o problema.

1. Para indicar que você está trabalhando no alerta agora, altere o **Status do alerta** para **Confirmado**:

    ![Selecionar e confirmar o alerta](./media/iot-accelerators-remote-monitoring-explore/maintenanceacknowledge.png)

1. Para agir no resfriador, selecione-o e, em seguida, escolha **Trabalhos**. Selecione **Executar método**, em seguida **EmergencyValveRelease**, adicione o nome de trabalho **ChillerPressureRelease** e escolha **Aplicar**. Essas configurações criam um trabalho que é executado imediatamente:

    ![Selecionar o dispositivo e agendar uma ação](./media/iot-accelerators-remote-monitoring-explore/maintenanceschedule.png)

1. Para exibir o status do trabalho, retorne à página **Manutenção** e exiba a lista de trabalhos na exibição **Trabalhos**. Você pode ver que o trabalho foi executado para liberar a pressão da válvula no Resfriador:

    ![O status dos trabalhos no modo de exibição de Trabalhos](./media/iot-accelerators-remote-monitoring-explore/maintenancerunningjob.png)

Por fim, confirme se os valores de telemetria do Resfriador voltaram ao normal.

1. Para exibir a grade de alertas, navegue até a página **Painel**.

1. Para exibir a telemetria do dispositivo, selecione o dispositivo do alerta original no mapa e confirme se ele voltou ao normal.

1. Para fechar o incidente, navegue até a página **Manutenção**, selecione o alerta e defina o status como **Fechado**:

    ![Selecionar e fechar o alerta](./media/iot-accelerators-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>Atualizar o firmware do dispositivo

A Contoso está testando um novo tipo de dispositivo em campo. Como parte do ciclo de teste, você precisa garantir que as atualizações de firmware do dispositivo funcionem corretamente. As etapas a seguir mostram como usar a solução de Monitoramento Remoto para atualizar o firmware em vários dispositivos.

Para executar as tarefas de gerenciamento de dispositivo necessárias, use a página **Dispositivos**. Comece filtrando por todos os dispositivos de protótipo:

1. Navegue até a página **Dispositivos**. Escolha o filtro **Dispositivos de protótipo** na lista suspensa **Filtros**:

    ![Usar um filtro na página de dispositivos](./media/iot-accelerators-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > Clique em **Gerenciar** para gerenciar os filtros disponíveis.

1. Selecione um dos dispositivos de protótipo:

    ![Selecionar um dispositivo na página de dispositivos](./media/iot-accelerators-remote-monitoring-explore/devicesselect.png)

1. Clique no botão **Trabalhos**, escolha **Executar método** e, em seguida, escolha **Atualização de Firmware**. Insira valores para **Nome do trabalho**, **Versão do Firmware**, e **URI do firmware**. Escolha **Aplicar** para agendar o trabalho para ser executado agora:

    ![Agendar atualização de firmware no dispositivo](./media/iot-accelerators-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > Com os dispositivos simulados, você pode usar qualquer URL que desejar como o valor do **URI do firmware** e qualquer valor que desejar para a **Versão do Firmware**. Os dispositivos simulados não acessam a URL.

1. Observe quantos dispositivos o trabalho afeta e escolha **Aplicar**:

    ![Enviar o trabalho de atualização de firmware](./media/iot-accelerators-remote-monitoring-explore/devicessubmitupdate.png)

Você pode usar a página **Manutenção** para acompanhar o trabalho conforme ele é executado.

1. Para exibir a lista de trabalhos, navegue até a página **Manutenção** e clique em **Trabalhos**.

1. Localize o evento relacionado ao trabalho que você criou. Verifique se que o processo de atualização de firmware foi iniciado corretamente.

<!-- 05/01 broken 
You can create a filter to verify the firmware version updated correctly.

1. To create a filter, navigate to the **Devices** page and select **Manage device groups**:

    ![Manage device groups](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. Create a filter that includes only devices with the new firmware version:

    ![Create device filter](./media/iot-accelerators-remote-monitoring-explore/devicescreatefilter.png)

1. Return to the **Devices** page and verify that the device has the new firmware version. -->

## <a name="organize-your-assets"></a>Organizar seus ativos

A Contoso tem duas equipes diferentes para atividades de serviço de campo:

* A equipe Prédio Inteligente gerencia resfriadores, elevadores e mecanismos.
* A equipe Veículo Inteligente gerencia caminhões e dispositivos de protótipo.

Para facilitar seu trabalho de operador de organizar e gerenciar dispositivos, você pode marcá-los com o nome da equipe apropriada.

Você pode criar nomes de marca a serem usados com os dispositivos.

1. Para exibir todos os dispositivos, navegue até a página **Dispositivos** e selecione o filtro **Todos os dispositivos**:

    ![Mostrar todos os dispositivos](./media/iot-accelerators-remote-monitoring-explore/devicesalldevices.png)

1. Selecione os dispositivos **Caminhões** e **Protótipos**. Em seguida, escolha **Trabalhos**:

    ![Selecionar dispositivos de protótipo e caminhão](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect.png)

1. Escolha **Marca** e, em seguida, crie uma nova marca de texto chamada **FieldService** com o valor **ConnectedVehicle**. Escolha um nome para o trabalho. Em seguida, clique em **Aplicar**:

    ![Adicionar marcas aos dispositivos de protótipo e caminhão](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag.png)

1. Selecione os dispositivos **Resfriador**, **Elevador** e **Mecanismo**. Em seguida, escolha **Trabalhos**:

    ![Selecionar os dispositivos Resfriador, Elevador e Mecanismo](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect2.png)

1. Escolha **Marca** e, em seguida, criar uma nova marca de texto chamada **FieldService** com o valor **SmartBuilding**. Escolha um nome para o trabalho. Em seguida, clique em **Aplicar**:

    ![Adicionar marcas aos dispositivos resfriador, mecanismo e elevador](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag2.png)

Você pode usar os valores da marca para criar filtros.

1. Na página **Dispositivos**, escolha **Gerenciar grupos de dispositivo**:

    ![Gerenciar grupos de dispositivo](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. Crie um novo filtro que use o nome de marca **FieldService** e o valor **SmartBuilding**. Salve o filtro como **Prédio Inteligente**.

1. Crie um novo filtro que use o nome de marca **FieldService** e o valor **ConnectedVehicle**. Salve o filtro como **Veículo Conectado**.

Agora, o operador da Contoso pode consultar dispositivos de acordo com a equipe operacional sem precisar alterar nada nos dispositivos.

## <a name="stop-simulated-devices"></a>Interromper os dispositivos simulados

Você pode usar o menu de configurações para interromper os dispositivos simulados. Isso ajuda a reduzir os custos de testar e explorar a solução. Para iniciar ou interromper os dispositivos simulados:

1. Escolha o ícone **Configurações**.

1. Em seguida, ative ou desative **Fluxo normal**:

    ![Menu Configurações](./media/iot-accelerators-remote-monitoring-explore/settings.png)

## <a name="customize-the-ui"></a>Personalizar a interface do usuário

No menu de configurações, você pode aplicar personalizações básicas para o acelerador de solução de monitoramento remoto. Você pode:

- Alternar entre os temas claro e escuro.
- Alterar o nome da solução.
- Carregar uma logo personalizada.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

>[!div class="checklist"]
> * Visualizar e filtrar os dispositivos no painel
> * Responder a um alerta
> * Atualizar o firmware em seus dispositivos
> * Organizar seus ativos
> * Parar e iniciar os dispositivos simulados

Agora que você já explorou a solução de Monitoramento Remoto, as próximas etapas sugeridas são saber mais sobre os recursos avançados da solução de Monitoramento Remoto:

* [Monitorar seus dispositivos](./iot-accelerators-remote-monitoring-monitor.md).
* [Gerenciar seus dispositivos](./iot-accelerators-remote-monitoring-manage.md).
* [Automatizar sua solução com regras](./iot-accelerators-remote-monitoring-automate.md).
* [Manter sua solução](iot-accelerators-remote-monitoring-maintain.md).
