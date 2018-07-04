---
title: Configurar um modelo de dispositivo em um aplicativo Azure IoT Central | Microsoft Docs
description: Saiba como configurar um modelo de dispositivo com medidas, configurações, propriedades, regras e um painel.
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f71d4c7dc94fedfd598ab87c51366ba9fb1f184a
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063028"
---
# <a name="set-up-a-device-template"></a>Configurar um modelo de dispositivo

Um modelo de dispositivo é um blueprint que define as características e os comportamentos de um tipo de dispositivo que conecta a um aplicativo Microsoft Azure IoT Central.

Por exemplo, um construtor pode criar um modelo de dispositivo para um fã conectado por IoT que possui:

- Medição de telemetria de temperatura

- Medição de evento de erro de motor da ventoinha

- Medição do estado operacional da ventoinha

- Ajuste de velocidade da ventoinha

- Propriedade de localização

- Regras que enviam alertas

- Painel que oferece uma visão geral do dispositivo

A partir desse modelo de dispositivo, um operador pode criar e conectar dispositivos de ventoinhas reais com nomes como **ventoinha-1** e **ventoinha-2**. Todos esses fãs possuem medidas, configurações, propriedades, regras e um painel que os usuários do seu aplicativo podem monitorar e gerenciar.

> [!NOTE]
> Somente construtores e administradores podem criar, editar e excluir modelos de dispositivo. Qualquer usuário pode criar dispositivos na página **Device Explorer** a partir de modelos de dispositivos existentes.

## <a name="create-a-device-template"></a>Crie um modelo de dispositivo

1. Vá para a página **Application Builder**.

2. Para criar um modelo em branco, selecione **Criar modelo de dispositivo** e, em seguida, selecione **Personalizar**.

3. Digite um nome para o novo modelo de dispositivo e selecione **Criar**.

   ![Página de detalhes do dispositivo com "Geladeira" como o nome do modelo](./media/howto-set-up-template/devicedetailspage.png)

4. Agora você está na página **Detalhes do Dispositivo** de um novo dispositivo simulado. Um dispositivo simulado é criado automaticamente quando você cria um modelo de dispositivo. Ele relata dados e pode ser controlado como um dispositivo real.

Agora, observe cada uma das guias na página **Detalhes do Dispositivo**.

## <a name="measurements"></a>Medidas

Medidas são os dados que vêm do seu dispositivo. É possível adicionar várias medidas ao modelo de dispositivo para corresponder aos recursos do dispositivo.

- **Telemetria** são os pontos de dados numéricos que o seu dispositivo coleta ao longo do tempo. Eles são representados como um fluxo contínuo. Um exemplo é a temperatura.
- **As medições do evento** são dados point-in-time que representam algo significativo no dispositivo. Um nível de severidade representa a importância de um evento. Um exemplo é um erro no motor do ventilador.
- Medidas de **Estado** representam o estado do dispositivo ou os respectivos componentes durante um período de tempo. Por exemplo, um modo de ventilador pode ser definido como tendo **Operating** e **Stopped** como os dois estados possíveis.

### <a name="create-a-telemetry-measurement"></a>Criar uma medição de telemetria
Para adicionar uma nova medição de telemetria, selecione o botão **+ New Measurement**. Selecione **Telemetria** como o tipo de medição e insira os detalhes no formulário **Criar Telemetria**.

> [!NOTE]
> Quando um dispositivo real estiver conectado, preste atenção ao nome da medida que o dispositivo reporta. O nome deve corresponder exatamente à entrada **Nome do campo** para uma medida.

Por exemplo, é possível adicionar uma nova medida de telemetria de temperatura:

![Formulário "Criar Telemetria" com detalhes para medição de temperatura](./media/howto-set-up-template/measurementsform.png)

Depois de selecionar **Salvar**, a medição **Temperatura** aparece na lista de medições. Um operador pode ver a visualização dos dados de temperatura que o dispositivo está coletando.

![Gráfico de medição](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Crie uma medição de evento
Para adicionar uma nova medição de evento, selecione o botão **+ New Measurement**. Selecione **Evento** como o tipo de medição e insira os detalhes no formulário **Criar Evento**.

Forneça os detalhes **Nome para exibição**, **Nome do campo** e **Gravidade** para o evento. Você pode escolher entre os três níveis de gravidade disponíveis: **Erro**, **Aviso** e **Informação**.  

Por exemplo, você pode adicionar um novo evento **Fan Motor Error**.

![Formulário "Criar Evento" com detalhes para um evento de motor de ventilador](./media/howto-set-up-template/eventmeasurementsform.png)

Depois de selecionar **Gravar**, a medição **Erro do Motor do Ventilador** aparece na lista de medições. Um operador pode ver a visualização dos dados do evento que o dispositivo está enviando.

![Gráfico de medição de eventos](./media/howto-set-up-template/eventmeasurementschart.png)

Para ver mais detalhes sobre o evento, selecione o ícone do evento no gráfico:

![Detalhes para o evento "Fan Motor Error"](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Crie uma medida de estado
Para adicionar uma nova medida de estado, selecione o botão **+ New Measurement**. Selecione **State** como o tipo de medição e insira os detalhes no formulário **Create State**.

Forneça os detalhes para **Nome de exibição**, **Nome do campo** e **Valores** do estado. Cada valor também pode ter um nome de exibição que será usado quando o valor aparecer em gráficos e tabelas.

Por exemplo, você pode adicionar um novo **estado do Modo de fãs** que tenha dois valores possíveis que o dispositivo pode enviar, **Em funcionamento** e **Parado**.

![Formulário "Editar estado" com detalhes para o modo de ventilador](./media/howto-set-up-template/statemeasurementsform.png)

Depois de selecionar **Gravar**, a **medição do estado do Fan Mode** aparece na lista de medições. O operador pode ver a visualização dos dados de estado que o dispositivo está enviando.

![Carta de medição de estado](./media/howto-set-up-template/statemeasurementschart.png)

Se o dispositivo enviar muitos pontos de dados em uma duração pequena, a medição do estado aparecerá com um visual diferente, conforme mostrado na captura de tela a seguir. Se você clicar no gráfico, todos os pontos de dados dentro desse período serão exibidos em ordem cronológica. Também é possível reduzir o intervalo de tempo para ver a medida plotada no gráfico.

![Detalhes para a medição do estado "Static Fan Mode"](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Configurações

As configurações controlam um dispositivo. Elas permitem que os operadores do aplicativo forneçam entradas ao dispositivo. É possível adicionar várias configurações ao modelo de dispositivo que aparecem como blocos na guia **Configurações** para os operadores usarem. Você pode adicionar seis tipos de configurações: número, texto, data, alternância, lista de seleção e rótulo da seção.

> [!NOTE]
> Quando um dispositivo real estiver conectado, preste atenção ao nome da configuração que o dispositivo informa. O nome deve corresponder exatamente à entrada **Nome do campo** para uma configuração.

As configurações podem estar em um dos três estados. O dispositivo informa esses estados.

- **Sincronizado**: o dispositivo foi alterado para refletir o valor da configuração.

- **Pendente**: o dispositivo está mudando para o valor de configuração.

- **Erro**: o dispositivo retornou um erro.

Por exemplo, é possível adicionar uma nova configuração de velocidade da ventoinha:

![Formulário "Configurar Número" com detalhes para configurações de velocidade](./media/howto-set-up-template/settingsform.png)

Depois de selecionar **Salvar**, a configuração **Velocidade da ventoinha** aparece como uma peça e está pronta para ser usada para alterar a velocidade do ventilador do dispositivo.

Depois de criar um bloco, você pode experimentar sua nova configuração. Primeiro, desative o modo de design na parte superior direita da tela.

![Aba "Configurações" com a opção "Modo de Design" para o ladrilho](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>propriedades

Propriedades são os metadados do dispositivo associados ao dispositivo, como localização do dispositivo e número de série. Você pode adicionar várias propriedades ao seu modelo de dispositivo que aparecem como blocos na guia **Propriedades**. Um operador pode especificar os valores das propriedades quando cria um dispositivo e pode editar esses valores a qualquer momento. Você pode adicionar seis tipos de propriedades: número, texto, data, alternância, propriedade do dispositivo e rótulo.

Existem duas categorias de propriedades:

- **Dispositivo** propriedades relatadas pelo dispositivo.
- **Propriedades do aplicativo** que são armazenadas apenas no aplicativo. O dispositivo não reconhece as propriedades do aplicativo.

> [!NOTE]
> Para propriedades do dispositivo, quando um dispositivo real estiver conectado, preste atenção ao nome da propriedade que o dispositivo reporta. O nome deve corresponder exatamente à entrada **Nome do campo** da propriedade. Para propriedades do aplicativo, o nome de campo poderá ser aquele que você desejar, desde que o nome seja exclusivo no modelo de dispositivo.

Por exemplo, é possível adicionar a localização do dispositivo como uma nova propriedade:

![Formulário "Configurar texto" na guia "Propriedades"](./media/howto-set-up-template/propertiesform.png)

Depois de selecionar **Salvar**, a localização do dispositivo aparecerá como um bloco:

![Azulejo de localização](./media/howto-set-up-template/propertiestile.png)

Depois de criar um bloco, você pode alterar o valor da propriedade. Primeiro, desative o modo de design na parte superior direita da tela.

### <a name="create-a-location-property-through-azure-maps"></a>Criar uma propriedade de localização por meio dos mapas do Azure
Você pode fornecer o contexto geográfico aos dados de localização no Azure IoT Central e mapear as coordenadas de latitude e longitude de um endereço. Ou você pode simplesmente mapear coordenadas de latitude e longitude. O Azure Maps habilita esse recurso na IoT Central.

Você pode adicionar dois tipos de propriedades de localização:
- **Localização como uma propriedade de aplicativo**, que é armazenada apenas no aplicativo. O dispositivo não reconhece as propriedades do aplicativo.
- **Localização como uma propriedade de dispositivo**, relatada pelo dispositivo.

#### <a name="add-location-as-an-application-property"></a>Adicionar local como uma propriedade do aplicativo 
Você pode criar uma propriedade de local como uma propriedade de aplicativo usando o Azure Maps no seu aplicativo IoT Central. Por exemplo, é possível adicionar o endereço de instalação do dispositivo. 

1. Na guia **Propriedades**, verifique se o **Modo de Design** é **em**.

   ![Aba "Propriedades" com o modo de design ativado](./media/howto-set-up-template/locationcloudproperty1.png)

2. Na biblioteca, selecione **Local**.
3. Configure **Nome de exibição**, **Nome do campo** e (opcionalmente) **Valor inicial** para o local. 

   ![Formulário "Configurar localização" com detalhes para localização](./media/howto-set-up-template/locationcloudproperty2.png)

   Há dois formatos com suporte para adicionar um local:
   - **Local como um endereço**
   - **Local como coordenadas** 

4. Clique em **Salvar**. 

   ![Propriedade de localização com endereço de instalação adicionado](./media/howto-set-up-template/locationcloudproperty3.png)

Agora um operador pode atualizar o valor do local no formulário do campo de local. 

#### <a name="add-location-as-a-device-property"></a>Adicionar local como uma propriedade de dispositivo 

Você pode criar uma propriedade de localização como uma propriedade de dispositivo que o dispositivo reporta. Por exemplo, se você quiser rastrear a localização do dispositivo:

1. Na guia **Propriedades**, verifique se o **Modo de Design** é **em**.

   ![Aba "Propriedades" com o modo de design ativado](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Selecione **Propriedade do Dispositivo** na biblioteca.
3. Configure o nome de exibição e o nome do campo e selecione **Local** como o tipo de dados. 

   > [!NOTE]
   > O nome do campo deve corresponder exatamente ao nome da propriedade que o dispositivo informa. 

   ![Formulário "Configurar propriedades do dispositivo" com detalhes para localização](./media/howto-set-up-template/locationdeviceproperty2.png)

Agora que você configurou sua propriedade de localização, é possível [adicionar um mapa para visualizar o local no painel do dispositivo](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Comandos

Os comandos são usados para gerenciar remotamente um dispositivo. Eles permitem que os operadores do seu aplicativo executem comandos instantaneamente no dispositivo. Você pode adicionar vários comandos ao seu modelo de dispositivo que aparecem como blocos na guia **Comandos** para os operadores usarem. Como construtor do dispositivo, você tem a flexibilidade de definir comandos de acordo com seus requisitos.

Como um comando é diferente de um cenário? 

* **Configuração**: uma configuração é uma configuração que você deseja aplicar a um dispositivo e deseja que o dispositivo persista nessa configuração até que você a altere. Por exemplo, você deseja definir a temperatura do seu freezer e desejar essa configuração mesmo quando o freezer reiniciar. 

* **Comando**: Você usa comandos para executar instantaneamente um comando no dispositivo remotamente a partir do IoT Central. Se um dispositivo não estiver conectado, o comando atingirá o tempo limite e falhará. Por exemplo, você deseja reiniciar um dispositivo.  

Quando você executa um comando, ele pode estar em um dos três estados, dependendo se o dispositivo recebeu o comando. 

Por exemplo, você pode adicionar um novo comando **Echo**:

![Formulário "Configurar Comando" com detalhes para o echo](./media/howto-set-up-template/commandsecho.png)

Depois de selecionar **Salvar**, o comando **Echo** aparece como uma peça e está pronto para ser usado para ecoar o dispositivo.

Depois de criar um bloco, você pode testar seu novo comando.

## <a name="rules"></a>Regras

As regras permitem que os operadores monitorem os dispositivos quase em tempo real. As regras invocam automaticamente ações como o envio de um email quando a regra é acionada. Um tipo de regra está disponível hoje:

- **Regra de telemetria**, que é acionada quando a telemetria do dispositivo selecionado ultrapassa um limite especificado. [Saiba mais sobre as regras de telemetria](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>painel

O painel é onde um operador pode acessar para visualizar as informações de um dispositivo. Como construtor, você pode adicionar blocos nessa página para ajudar os operadores a entender como o dispositivo está se comportando. É possível adicionar vários blocos de painel ao modelo de dispositivo. Você pode adicionar seis tipos de blocos de painel: imagem, gráfico de linha, gráfico de barras, KPI, configurações e propriedades e rótulo.

Por exemplo, é possível adicionar um bloco **Configurações e Propriedades** para mostrar uma seleção dos valores atuais de configurações e propriedades:

![Formulário "Configurar detalhes do dispositivo" com detalhes para configurações e propriedades](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Quando um operador visualizar o painel, ele poderá ver esse bloco que exibirá as propriedades e configurações do dispositivo:

![Guia "Painel" com as configurações e propriedades exibidas para o bloco](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Adicionar um local do Azure Maps no painel

Se você configurou uma propriedade de local anteriormente em [Crie uma propriedade de local por meio dos Mapas do Azure](#create-a-location-property-through-azure-maps), poderá visualizar o local usando um mapa no painel do dispositivo.

1. Na guia **Dashboard**, verifique se **Design Mode** está **Ligado**.

   ![Aba "Dashboard" com o modo de design ativado](./media/howto-set-up-template/locationcloudproperty4map.png)

2. No painel do dispositivo, selecione **Mapa** da biblioteca. 
3. Dê um título e escolha a propriedade de localização que você configurou anteriormente como parte das propriedades do seu dispositivo.

   ![Formulário "Configurar Mapa" com detalhes para título e propriedades](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Clique em **Salvar**. O bloco do mapa agora exibe o local selecionado. 

   ![Bloco de mapa com localização selecionada](./media/howto-set-up-template/locationcloudproperty6map.png) 

Você pode redimensionar o mapa para o tamanho desejado.

Agora, quando um operador visualiza o painel, ele pode ver todos os blocos do painel que você configurou, incluindo um mapa de localização.

![Telhas no painel](./media/howto-set-up-template/locationcloudproperty7map.png) 

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a configurar um modelo de dispositivo no aplicativo Central do IoT do Azure, é possível:

> [!div class="nextstepaction"]
> [Criar uma nova versão do modelo de dispositivo](howto-version-devicetemplate.md)
