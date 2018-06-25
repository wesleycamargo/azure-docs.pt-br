---
title: Configurar um modelo de dispositivo em um aplicativo Azure IoT Central | Microsoft Docs
description: Saiba como configurar um modelo de dispositivo com medidas, configurações, propriedades, regras e painel.
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bda056a75ae9d696dab389b85fe1bfb2935ee1a8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261977"
---
# <a name="set-up-a-device-template"></a>Configurar um modelo de dispositivo

Um modelo de dispositivo é um blueprint que define as características e os comportamentos de um tipo de dispositivo que conecta a um aplicativo Microsoft Azure IoT Central.

Por exemplo, um construtor pode criar um modelo de dispositivo para uma ventoinha conectada ao IoT que tenha:

- Medição de telemetria de temperatura

- Medição de evento de erro de motor da ventoinha

- Medição do estado operacional da ventoinha

- Ajuste de velocidade da ventoinha

- Propriedade de localização

- Regras que enviam alertas

- Painel de controle que oferece uma exibição completa do dispositivo

A partir desse modelo de dispositivo, um operador pode criar e conectar dispositivos de ventoinhas reais com nomes como **ventoinha-1** e **ventoinha-2**. Todas essas ventoinhas têm medidas, configurações e propriedades, regras e um painel que os usuários do aplicativo podem monitorar e gerenciar.

> [!NOTE]
Somente construtores e administradores podem criar, editar e excluir modelos de dispositivo. Qualquer usuário pode criar dispositivos na página **Device Explorer** a partir de modelos de dispositivos existentes.

## <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo

1. Navegue até a página **Construtor de Aplicativos**.

1. Para criar um modelo em branco, escolha **Criar Modelo de Dispositivo** e, em seguida, escolha **Personalizar**.

1. Insira um nome para o novo modelo de dispositivo e escolha **Criar**.

    ![Página Detalhes do Dispositivo](./media/howto-set-up-template/devicedetailspage.png)

1. Agora você está na página **Detalhes do Dispositivo** de um novo dispositivo simulado. Um dispositivo simulado é criado automaticamente quando você cria um novo modelo de dispositivo. Ele relata dados e pode ser controlado como um dispositivo real.

Agora, observe cada uma das guias na página **Detalhes do Dispositivo**.

## <a name="measurements"></a>Medidas

Medidas são os dados provenientes do dispositivo. É possível adicionar várias medidas ao modelo de dispositivo para corresponder aos recursos do dispositivo. Atualmente, a telemetria e o evento são os tipos de medidas com suporte.

- Medidas de **Telemetria** são os pontos de dados numéricos que o dispositivo coleta ao longo do tempo e são representados por um fluxo contínuo. Por exemplo, temperatura.
- Medidas de **Evento** são dados pontuais que representam algo significativo no dispositivo. Os eventos têm gravidade associada a eles, o que representa a importância do evento. Por exemplo, erro de motor da ventoinha
- Medidas de **Estado** representam o estado do dispositivo ou os respectivos componentes durante um período de tempo. Por exemplo, para os dois estados possíveis o modo de ventoinha pode ser definido como operacional e interrompido.

### <a name="create-a-telemetry-measurement"></a>Criar uma medida de telemetria
Para adicionar uma nova medida de telemetria, clique no botão **+ Nova Medida** que abre um formulário com opções para selecionar o tipo de medida. Selecione **Telemetria** e insira os detalhes no formulário **Criar Telemetria**.

> [!NOTE]
> Quando um dispositivo real estiver conectado, atente-se ao nome da medida que o dispositivo relata. O nome deve corresponder exatamente ao **Nome de Campo**  de uma medida.

Por exemplo, é possível adicionar uma nova medida de telemetria de temperatura:

![Formulário das medidas](./media/howto-set-up-template/measurementsform.png)

Após escolher **Salvar**, a **Temperatura** aparecerá na lista de medidas e um operador poderá ver a exibição dos dados de temperatura que o dispositivo está coletando.

![Gráfico das medidas](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Criar uma medida de evento
Para adicionar uma nova medida de Evento, clique no botão **+ Nova Medida** que abre um formulário com escolhas para selecionar o tipo de medida. Selecione **Evento** e insira os detalhes no formulário **Criar Evento**.

Neste formulário, forneça o **Nome para Exibição**, **Nome de Campo** e a **Gravidade** do evento. É possível escolher entre os três níveis de gravidade disponíveis - **Erro**, **Aviso** e **Informações**.  

Por exemplo, você pode adicionar um novo evento 'Erro de Motor da Ventoinha'.

![Formulário das Medidas de Evento](./media/howto-set-up-template/eventmeasurementsform.png)

Após escolher **Salvar**, o **Erro de Motor da Ventoinha** aparecerá na lista de medidas e um operador poderá exibir a visualização dos dados do evento que o dispositivo está enviando.

![Gráfico das Medidas de Evento](./media/howto-set-up-template/eventmeasurementschart.png)

Para exibir detalhes adicionais sobre o evento, clique no ícone do evento no gráfico:

![Detalhe das Medidas de Evento](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Criar uma medida de Estado
Para adicionar uma nova medida de estado, clique no botão **+ Nova Medida** que abre um formulário com opções para selecionar o tipo de medida. Selecione **Estado** e insira os detalhes no formulário **Criar Estado**.

Neste formulário, forneça o **Nome para Exibição**, **Nome de Campo** e os possíveis**Valores** do estado. Cada **valor** também pode ter um nome para exibição que será usado ao mostrar o valor nos gráficos e tabelas.

Por exemplo, é possível adicionar um novo estado de "Modo de Ventoinha" que tenha dois valores possíveis que o dispositivo poderá enviar, **Operacional** e **Interrompido**.

![Formulário das Medidas de Estado](./media/howto-set-up-template/statemeasurementsform.png)

Após escolher **Salvar**, o **Modo de Ventoinha** aparecerá na lista de medidas e o operador poderá exibir a visualização dos dados de estado que o dispositivo está enviando.

![Tabela das Medidas de Estado](./media/howto-set-up-template/statemeasurementschart.png)

No caso, há muitos pontos de dados enviados pelo dispositivo dentro de uma pequena duração, a medida de estado é mostrada com um visual diferente, conforme mostrado abaixo. Se você clicar no gráfico, todos os pontos de dados dentro desse período de tempo serão exibidos em uma ordem cronológica. Também é possível reduzir o intervalo de tempo para ver a medida plotada no gráfico.

![Detalhe das Medidas de Estado](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Configurações

As configurações controlam um dispositivo. Elas permitem que os operadores do aplicativo forneçam entradas ao dispositivo. É possível adicionar várias configurações ao modelo de dispositivo que aparecem como blocos na guia **Configurações** para os operadores usarem. Há seis tipos de configurações que podem ser adicionadas: número, texto, data, alternância, lista de separação e rótulo de seção.

> [!NOTE]
> Quando um dispositivo real estiver conectado, atente-se ao nome da configuração que o dispositivo relata. O nome deve corresponder exatamente ao **Nome de Campo**  de uma configuração.

As configurações podem estar em um dos três estados. Esses estados são relatados pelo dispositivo.

- **Sincronizado**: o dispositivo foi alterado para refletir o valor da configuração.

- **Pendente**: no momento, o dispositivo está alterando para o valor da configuração.

- **Erro**: o dispositivo retornou um erro.

Por exemplo, é possível adicionar uma nova configuração de velocidade da ventoinha:

![Formulário de configurações](./media/howto-set-up-template/settingsform.png)

Após escolher **Salvar**, a **Velocidade da ventoinha** aparecerá como uma peça e estará pronta a ser usada para alterar a velocidade da ventoinha do dispositivo.

> [!NOTE]
> Após criar um novo bloco, você poderá experimentar a nova configuração. Primeiro, desligue o modo de design no canto superior direito da tela:

![Bloco de configurações](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>propriedades

As propriedades são os metadados do dispositivo associados ao dispositivo, como localização do dispositivo e número de série. É possível adicionar várias propriedades ao modelo de dispositivo, as quais aparecem como blocos na guia **Propriedades**. Um operador poderá especificar os valores das propriedades ao criar um novo dispositivo e poderá editar esses valores a qualquer momento. Há seis tipos de propriedades que podem ser adicionadas: número, texto, data, alternância, propriedade de dispositivo e rótulo.

Há dois tipos de propriedades:

- As **propriedades de dispositivo** são propriedades relatadas pelo dispositivo.
- As **propriedades do aplicativo** são propriedades armazenadas totalmente no aplicativo. O dispositivo não tem conhecimento das propriedades do aplicativo.

> [!NOTE]
> Para propriedades do dispositivo, quando um dispositivo real estiver conectado, atente-se ao nome da propriedade que o dispositivo reporta. O nome deve corresponder exatamente ao **Nome de Campo** da propriedade. Para propriedades do aplicativo, o nome de campo poderá ser aquele que você desejar, desde que o nome seja exclusivo no modelo de dispositivo.

Por exemplo, é possível adicionar a localização do dispositivo como uma nova propriedade:

![Formulário de propriedades](./media/howto-set-up-template/propertiesform.png)

Após escolher **Salvar**, a localização do dispositivo aparecerá como um bloco:

![Bloco de propriedades](./media/howto-set-up-template/propertiestile.png)

> [!NOTE]
> Após criar um novo bloco, você poderá alterar o valor da propriedade. Primeiro, desligue o modo de design no canto superior direito da tela.

### <a name="create-a-location-property-powered-by-azure-maps"></a>Criar uma Propriedade de localização desenvolvida pelo Azure Mapas
É possível fornecer contexto geográfico para seus dados de local no Azure IoT Central e mapear coordenadas de latitude e de longitude de um endereço ou simplesmente coordenadas de latitude e de longitude. Essa funcionalidade no Azure IoT Central é desenvolvida pelo Azure Mapas.

Há dois tipos de propriedades de local que é possível adicionar:
- **Local como uma propriedade de aplicativo** que será armazenado somente no aplicativo. O dispositivo não tem conhecimento das propriedades do aplicativo.
- **Local como uma propriedade do dispositivo** que será relatado pelo dispositivo.

####<a name="adding-location-as-an-application-property"></a>Adicionando local como uma propriedade de aplicativo 
É possível criar uma propriedade de localização como uma propriedade de aplicativo usando o Azure Mapas em seu aplicativo Azure IoT Central. Por exemplo, é possível adicionar o endereço de instalação do dispositivo. 

1. Navegue até a guia Propriedade do dispositivo; verifique se o modo de design está ativado.

![Propriedade de localização](./media/howto-set-up-template/locationcloudproperty1.png)

2. Na guia Propriedade, clique no Local.
3. Configure o Nome de exibição, Nome do campo e o valor inicial do local opcionalmente. 

![Formulário Propriedade de localização](./media/howto-set-up-template/locationcloudproperty2.png)

Há dois formatos com suporte para adicionar um local:
- **Local como um endereço**
- **Local como coordenadas** 

4. Clique em Salvar. 

![Campo Propriedade de localização](./media/howto-set-up-template/locationcloudproperty3.png)

Agora um operador pode atualizar o valor do local no formulário do campo de local. 

####<a name="adding-location-as-a-device-property"></a>Adicionando local como uma propriedade do dispositivo 

É possível criar uma propriedade de localização como uma propriedade do dispositivo relatada pelo dispositivo.
Por exemplo, controle o local do dispositivo.

1.  Navegue até a guia Propriedade do dispositivo; verifique se o modo de design está ativado.
2.  Clique em Propriedade do dispositivo na biblioteca.

![Campo Propriedade de localização](./media/howto-set-up-template/locationdeviceproperty1.png)

3.  Configure o nome de exibição, o nome do campo e escolha "local" como um tipo de dados. 

> [!NOTE]
O nome do campo deve corresponder exatamente ao nome da propriedade relatada pelo dispositivo. 

![Campo Propriedade de localização](./media/howto-set-up-template/locationdeviceproperty2.png)

![Exibição do operador Propriedade de localização](./media/howto-set-up-template/locationdeviceproperty2.png)

Agora que você configurou sua propriedade de localização, será possível adicionar um Mapa para visualizar a localização no painel do dispositivo. Confira como [Adicionar um Mapa do Azure de localização no painel](howto-set-up-template.md).




## <a name="rules"></a>Regras

As regras permitem que os operadores monitorem os dispositivos quase em tempo real. As regras invocam **Ações** automaticamente, como enviar um email quando a regra for acionada. Atualmente, há um tipo de regra disponível:

- **Regra de telemetria:** uma regra de telemetria é acionada quando a telemetria do dispositivo selecionado ultrapassa um limite especificado. Saiba mais sobre as [regras de telemetria](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>painel

O painel é onde um operador pode acessar para visualizar as informações de um dispositivo. Como um construtor, é possível adicionar blocos a essa página para ajudar os operadores a reconhecerem o comportamento do dispositivo. É possível adicionar vários blocos de painel ao modelo de dispositivo. Há seis tipos de blocos de painel que podem ser adicionados: imagem, gráfico de linhas, gráfico de barras, KPI, configurações e propriedades e rótulo.

Por exemplo, é possível adicionar um bloco **Configurações e Propriedades** para mostrar uma seleção dos valores atuais de configurações e propriedades:

![Formulário de detalhes do dispositivo do painel](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Quando um operador visualizar o painel, ele poderá ver esse bloco que exibirá as propriedades e configurações do dispositivo:

![Bloco do painel](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-location-azure-map-in-dashboard"></a>Adicionar um Mapa do Azure de localização no painel

Se tiver configurado uma propriedade de localização como nas etapas [Criar uma Propriedade de localização desenvolvida pelo Azure Mapas]((howto-set-up-template.md), será possível visualizar a localização usando um mapa no painel do seu dispositivo.

1.  Navegue até a guia Painel do dispositivo; certifique-se de que o modo de design está ativado.
2.  No Painel do dispositivo, selecione o Mapa na Biblioteca. 

![Selecionar Mapa do Azure de localização no Painel](./media/howto-set-up-template/locationcloudproperty4map.png)

3.  Dê um título e escolha a propriedade de localização que você configurou anteriormente como parte da Propriedade de seu dispositivo.

![Configurar Mapa do Azure de localização no Painel](./media/howto-set-up-template/locationcloudproperty5map.png)

4.  Salve e você verá a peça de mapa exibindo a localização selecionada. 

![Visualizar Mapa do Azure de localização no Painel](./media/howto-set-up-template/locationcloudproperty6map.png) 

Será possível redimensionar o mapa para o tamanho desejado.

Agora, quando um operador exibe o painel, ele pode ver todos os blocos do painel configurados, incluindo um Mapa de localização!

![Painel do Mapa do Azure de localização no Painel](./media/howto-set-up-template/locationcloudproperty7map.png) 



## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar um modelo de dispositivo no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Criar uma nova versão do modelo de dispositivo](howto-version-devicetemplate.md)
