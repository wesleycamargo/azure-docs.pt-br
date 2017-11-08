---
title: "Introdução à solução de monitoramento remoto – Azure | Microsoft Docs"
description: "Este tutorial usa cenários simulados para apresentar a solução de monitoramento remoto pré-configurada. Esses cenários são criados quando você implanta a solução de monitoramento remoto pré-configurada pela primeira vez."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 222050410bfc809991f03a452eb6640e5f9e1802
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-preconfigured-solution"></a>Explorar os recursos da solução de monitoramento remoto pré-configurada

Este tutorial mostra os principais recursos de solução de monitoramento remoto. Para apresentar esses recursos, o tutorial mostra cenários comuns de cliente usando um aplicativo de IoT simulado para uma empresa chamada Contoso.

O tutorial ajuda a compreender os cenários típicos de IoT que a solução de monitoramento remoto fornece para uso imediato.

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Visualizar e filtrar os dispositivos no painel
> * Responder a um alarme
> * Atualizar o firmware em seus dispositivos
> * Organizar seus ativos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma instância da solução de monitoramento remoto implantada na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Deploy the remote monitoring preconfigured solution](iot-suite-remote-monitoring-deploy.md) (Implantar a solução de monitoramento remoto pré-configurada).

## <a name="the-contoso-sample-iot-deployment"></a>A implantação de IoT de exemplo da Contoso

Você pode usar a implantação de IoT de exemplo da Contoso para compreender os cenários básicos que a solução de monitoramento remoto fornece para uso imediato. Esses cenários são baseados em implantações de IoT reais. Provavelmente, você vai optar por personalizar a solução de monitoramento remoto para atender aos seus requisitos específicos, mas o exemplo da Contoso ajuda a aprender os conceitos básicos.

> [!NOTE]
> Se você tiver usado o CLI para implantar a solução pré-configurada, o arquivo `deployment-{your deployment name}-output.json` conterá informações sobre a implantação, como a URL para acessar o exemplo implantado.

O exemplo da Contoso provisiona um conjunto de dispositivos simulados e as regras que agem sobre eles. Depois de compreender os cenários básicos, você poderá continuar explorando mais os recursos da solução em [Perform advanced device monitoring using the remote monitoring solution](iot-suite-remote-monitoring-monitor.md) (Executar o monitoramento de dispositivos avançado usando a solução de monitoramento remoto).

A Contoso é uma empresa que gerencia uma variedade de ativos em diferentes ambientes. A Contoso planeja usar a eficiência dos aplicativos de IoT baseados em nuvem para monitorar e gerenciar remotamente vários ativos de um aplicativo centralizado. As seções a seguir fornecem um resumo da configuração inicial do exemplo da Contoso:

> [!NOTE]
> A demonstração da Contoso é apenas uma maneira de provisionar dispositivos simulados e criar regras. Outras opções de provisionamento incluem a criação de seus próprios dispositivos personalizados. Para saber mais sobre como criar seus próprios dispositivos e regras, consulte [Manage and configure your devices](iot-suite-remote-monitoring-manage.md) (Gerenciar e configurar seus dispositivos) e [Detect issues using threshold-based rules](iot-suite-remote-monitoring-automate.md) (Detectar problemas usando regras baseadas em limite).

### <a name="contoso-devices"></a>Dispositivos da Contoso

A Contoso usa diferentes tipos de dispositivos inteligentes. Esses dispositivos atendem a diferentes funções para a empresa, enviando diversos fluxos de telemetria. Além disso, cada tipo de dispositivo tem diferentes propriedades e métodos com suporte.

A tabela a seguir mostra um resumo dos tipos de dispositivos provisionados:

| Tipo de dispositivo        | Telemetria                                  | Propriedades                                  | Marcas                    | Métodos                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| Resfriador            | Temperatura, umidade, pressão            | Tipo, versão de firmware, modelo               | Localização, piso, campus | Reiniciar, atualização de firmware, versão da válvula de emergência, aumento de pressão                          |
| Dispositivo de protótipo | Temperatura, pressão, localização geográfica        | Tipo, versão de firmware, modelo               | Localização, modo          | Reiniciar, atualização de firmware, mover dispositivo, parar dispositivo, liberação de temperatura, aumento de temperatura |
| Motor             | Nível do tanque de combustível, sensor de refrigeração, vibração | Tipo, versão de firmware, modelo               | Localização, piso, campus | Reiniciar, atualização de firmware, tanque vazio, tanque cheio                                              |
| Caminhão              | Localização geográfica, velocidade, temperatura da carga     | Tipo, versão de firmware, modelo               | Localização, carregamento          | Reduzir temperatura da carga, aumentar temperatura da carga, atualização de firmware                         |
| Elevador           | Piso, vibração, temperatura              | Tipo, versão de firmware, modelo, localização geográfica | Localização, campus        | Parar elevador, iniciar elevador, atualização de firmware                                               |

> [!NOTE]
> O exemplo de demonstração da Contoso provisiona dois dispositivos por tipo. Para cada tipo, um funciona corretamente dentro dos limites definidos como normais pela Contoso e o outro tem algum tipo de mau funcionamento. Na próxima seção, você aprenderá sobre as regras definidas pela Contoso para os dispositivos. Essas regras definem os limites do comportamento correto.

### <a name="contoso-rules"></a>Regras da Contoso

Os operadores na Contoso sabem os limites que determinam se um dispositivo está funcionando corretamente. Por exemplo, um resfriador não estará funcionando corretamente se a pressão informada for maior que 250 PSI. A tabela a seguir mostra as regras baseadas em limites que a Contoso definiu para cada tipo de dispositivo:

| Nome da Regra | Descrição | Limite | Severity | Dispositivos afetados |
| --------- | ----------- | --------- | -------- | ---------------- |
| Pressão de resfriador muito alta | Alerta quando os resfriadores alcançam níveis de pressão maiores que os normais   |P > 250 psi       | Crítico | Resfriadores            |
| Temp. do dispositivo de protótipo muito alta  | Alerta quando os dispositivos de protótipo alcançam níveis de temperatura maiores que os normais  |T>80&deg; F |Crítico | Dispositivos de protótipo |
| Tanque do motor vazio  | Alerta quando o tanque de combustível do motor fica vazio                     | F < 5 galões | Informações     | Motores             |
| Temperatura da carga maior que a normal | Alerta quando a temperatura da carga do caminhão está acima do normal                 | T<45&deg; F |Aviso  | Caminhões              |
| Vibração do elevador interrompida      | Alerta quando o elevador é completamente parado (com base no nível de vibração)                     | V < 0,1 mm |Aviso  | Elevadores           |

### <a name="operate-the-contoso-sample-deployment"></a>Operar a implantação de exemplo da Contoso

Agora você já viu a configuração inicial no exemplo da Contoso. As seções a seguir descrevem os três cenários no exemplo da Contoso que ilustram como um operador pode usar a solução pré-configurada.

## <a name="respond-to-a-pressure-alarm"></a>Responder a um alarme de pressão

Este cenário mostra como identificar e responder a um alarme que é disparado por um dispositivo Resfriador. O Resfriador está localizado em Redmond, no prédio 43, piso 2.

Como operador, você vê no painel que há um alarme relacionado à pressão de um resfriador. Você pode exibir uma panorâmica e ampliar o mapa para ver mais detalhes.

1. Na página **Painel**, na grade **Alarmes do Sistema**, você pode ver o alarme **Pressão do resfriador muito alta**. O Resfriador também é realçado no mapa:

    ![O painel mostra o alarme de pressão e o dispositivo no mapa](media/iot-suite-remote-monitoring-explore/dashboardalarm.png)

1. Para exibir os detalhes e a telemetria do dispositivo, clique no Resfriador realçado no mapa. A telemetria mostra um pico de pressão:

    ![Escolher o dispositivo no mapa para exibir detalhes](media/iot-suite-remote-monitoring-explore/dashboarddetail.png)

1. Feche **Detalhes do dispositivo**.

1. Para navegar até a página **Manutenção**, clique em **...** na coluna **Explorar Alarme** ao lado do alarme na grade de alarme.

Na página **Manutenção**, você pode exibir os detalhes da regra que disparou o alarme de pressão do Resfriador.

1. Você pode ver o número de vezes que o alarme foi disparado, as confirmações e os alarmes abertos e fechados:

    ![A página manutenção mostra a lista de alarmes que foram disparados](media/iot-suite-remote-monitoring-explore/maintenancealarmlist.png)

1. O primeiro alarme na lista é o mais recente. Clique no alarme **Pressão do Resfriador** para exibir os dispositivos associados e a telemetria. A telemetria mostra um pico de pressão para o Resfriador:

    ![A página de manutenção mostra a telemetria para o alarme selecionado](media/iot-suite-remote-monitoring-explore/maintenancetelemetry.png)

Você identificou o problema que disparou o alarme e o dispositivo associado. Como operador, as próximas etapas serão para confirmar o alarme e mitigar o problema.

1. Para indicar que você está trabalhando no alarme agora, altere o **Status do alarme** para **Confirmado**:

    ![Selecionar e confirmar o alarme](media/iot-suite-remote-monitoring-explore/maintenanceacknowledge.png)

1. Para agir no Resfriador, selecione-o e, em seguida, escolha **Agendar**. Selecione **EmergencyValveRelease**, adicione um nome de trabalho **ChillerPressureRelease** e escolha **Aplicar**. Essas configurações criam um trabalho que é executado imediatamente:

    ![Selecionar o dispositivo e agendar uma ação](media/iot-suite-remote-monitoring-explore/maintenanceschedule.png)

1. Para exibir o status do trabalho, retorne à página **Manutenção** e exiba a lista de trabalhos na exibição **Status do Sistema**. Você pode ver que o trabalho foi executado para liberar a pressão da válvula no Resfriador:

    ![O status dos trabalhos no Status do Sistema](media/iot-suite-remote-monitoring-explore/maintenancerunningjob.png)

Por fim, confirme se os valores de telemetria do Resfriador voltaram ao normal.

1. Para exibir a grade de alarmes, navegue até a página **Painel**.

1. Para exibir a telemetria do dispositivo, selecione o dispositivo do alarme original no mapa e confirme se ele voltou ao normal.

1. Para fechar o incidente, navegue até a página **Manutenção**, selecione o alarme e defina o status como **Fechado**:

    ![Selecionar e fechar o alarme](media/iot-suite-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>Atualizar o firmware do dispositivo

A Contoso está testando um novo tipo de dispositivo em campo. Como parte do ciclo de teste, você precisa garantir que as atualizações de firmware do dispositivo funcionem corretamente. As etapas a seguir mostram como usar a solução de monitoramento remoto para atualizar o firmware em vários dispositivos.

Para executar as tarefas de gerenciamento de dispositivo necessárias, use a página **Dispositivos**. Comece filtrando por todos os dispositivos de protótipo:

1. Navegue até a página **Dispositivos**. Escolha o filtro **Dispositivos de protótipo** na lista suspensa **Filtros**:

    ![Usar um filtro na página de dispositivos](media/iot-suite-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > Clique em **Gerenciar** para gerenciar os filtros disponíveis.

1. Selecione um dos dispositivos de protótipo:

    ![Selecionar um dispositivo na página de dispositivos](media/iot-suite-remote-monitoring-explore/devicesselect.png)

1. Clique no botão **Agendar** e, em seguida, escolha **Atualização de firmware**. Insira valores para **Nome do trabalho** e **URI do firmware**. Escolha **Aplicar** para agendar o trabalho para ser executado agora:

    ![Agendar atualização de firmware no dispositivo](media/iot-suite-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > Com os dispositivos simulados, você pode usar qualquer URL que desejar como o valor do **URI do firmware**. Os dispositivos simulados não acessam a URL.

1. Observe quantos dispositivos o trabalho afeta e escolha **Aplicar**:

    ![Enviar o trabalho de atualização de firmware](media/iot-suite-remote-monitoring-explore/devicessubmitupdate.png)

Você pode usar a página **Manutenção** para acompanhar o trabalho conforme ele é executado.

1. Para exibir a lista de trabalhos, navegue até a página **Manutenção** e clique em **Status do sistema**.

1. Localize o evento relacionado ao trabalho que você criou. Verifique se que o processo de atualização de firmware foi iniciado corretamente.

Você pode criar um filtro para verificar a atualização da versão de firmware corretamente.

1. Para criar um filtro, navegue até a página **Dispositivos** e selecione **Gerenciar**:

    ![Gerenciar filtros de dispositivo](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. Crie um filtro que inclua somente dispositivos com a nova versão de firmware:

    ![Criar um filtro de dispositivo](media/iot-suite-remote-monitoring-explore/devicescreatefilter.png)

1. Volte para a página **Dispositivos** e verifique se o dispositivo tem a nova versão do firmware.

## <a name="organize-your-assets"></a>Organizar seus ativos

A Contoso tem duas equipes diferentes para atividades de serviço de campo:

* A equipe Prédio Inteligente gerencia resfriadores, elevadores e mecanismos.
* A equipe Veículo Inteligente gerencia caminhões e dispositivos de protótipo.

Para facilitar seu trabalho de operador de organizar e gerenciar dispositivos, você pode marcá-los com o nome da equipe apropriada.

Você pode criar nomes de marca a serem usados com os dispositivos.

1. Para exibir todos os dispositivos, navegue até a página **Dispositivos** e selecione o filtro **Todos os dispositivos**:

    ![Mostrar todos os dispositivos](media/iot-suite-remote-monitoring-explore/devicesalldevices.png)

1. Selecione os dispositivos **Caminhões** e **Protótipos**. Em seguida, escolha **Marca**:

    ![Selecionar dispositivos de protótipo e caminhão](media/iot-suite-remote-monitoring-explore/devicesmultiselect.png)

1. Escolha **Marca** e, em seguida, crie uma nova marca de cadeia de caracteres chamada **FieldService** com o valor **ConnectedVehicle**. Escolha um nome para o trabalho. Em seguida, clique em **Aplicar**:

    ![Adicionar marcas aos dispositivos de protótipo e caminhão](media/iot-suite-remote-monitoring-explore/devicesaddtag.png)

1. Selecione os dispositivos **Resfriador**, **Elevador** e **Mecanismo**. Em seguida, escolha **Marca**:

    ![Selecionar os dispositivos Resfriador, Elevador e Mecanismo](media/iot-suite-remote-monitoring-explore/devicesmultiselect2.png)

1. Escolha **Marca** e, em seguida, criar uma nova marca chamada **FieldService** com o valor **SmartBuilding**. Escolha um nome para o trabalho. Em seguida, clique em **Salvar**:

    ![Adicionar marcas aos dispositivos resfriador, mecanismo e elevador](media/iot-suite-remote-monitoring-explore/devicesaddtag2.png)

Você pode usar os valores da marca para criar filtros.

1. Na página **Dispositivos** escolha **Gerenciar filtros**:

    ![Gerenciar filtros de dispositivo](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. Crie um novo filtro que use o nome de marca **FieldService** e o valor **SmartBuilding**. Salve o filtro como **Prédio Inteligente**.

1. Crie um novo filtro que use o nome de marca **FieldService** e o valor **ConnectedVehicle**. Salve o filtro como **Veículo Conectado**.

Agora, o operador da Contoso pode consultar dispositivos de acordo com a equipe operacional sem precisar alterar nada nos dispositivos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

>[!div class="checklist"]
> * Visualizar e filtrar os dispositivos no painel
> * Responder a um alarme
> * Atualizar o firmware em seus dispositivos
> * Organizar seus ativos

Agora que você já explorou a solução de monitoramento remoto, as próximas etapas sugeridas são saber mais sobre os recursos avançados da solução de monitoramento remoto:

* [Monitorar seus dispositivos](./iot-suite-remote-monitoring-monitor.md).
* [Gerenciar seus dispositivos](./iot-suite-remote-monitoring-manage.md).
* [Automatizar sua solução com regras](./iot-suite-remote-monitoring-automate.md).
* [Manter sua solução](./iot-suite-remote-monitoring-maintain.md).
