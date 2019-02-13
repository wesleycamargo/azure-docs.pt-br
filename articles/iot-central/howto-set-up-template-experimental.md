---
title: Configurar um modelo de dispositivo em um aplicativo Azure IoT Central | Microsoft Docs
description: Saiba como configurar um modelo de dispositivo com medidas, configurações, propriedades, regras e um painel.
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b5ec8df9ff08aace69680c188f9ab05e944ce891
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734565"
---
# <a name="set-up-a-device-template-new-ui-design"></a>Configurar um modelo de dispositivo (novo design da interface do usuário)

Um modelo de dispositivo é um blueprint que define as características e os comportamentos de um tipo de dispositivo que conecta a um aplicativo do Azure IoT Central.

Por exemplo, um construtor pode criar um modelo de dispositivo para um ventilador conectado por IoT que tenha uma:

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

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="create-a-device-template"></a>Crie um modelo de dispositivo

1. Navegue para a página **Modelos de Dispositivo**.

2. Para criar um modelo em branco, clique em **+** e, em seguida, insira um nome, como **Refrigerador** para o novo modelo de dispositivo. Em seguida, selecione **Criar**:

   ![Página de detalhes do dispositivo com "Geladeira" como o nome do modelo](./media/howto-set-up-template-experimental/devicedetailspage.png)

4. Agora você está na página **Detalhes do Dispositivo** de um novo modelo de dispositivo. O IoT Central cria automaticamente um dispositivo simulado quando você cria um modelo de dispositivo. Um dispositivo simulado permite testar o comportamento do aplicativo antes de você conectar um dispositivo real.

As seções a seguir descrevem cada uma das guias da página **Modelo de Dispositivo**.

## <a name="measurements"></a>Medidas

Medidas são os dados que vêm do seu dispositivo. É possível adicionar várias medidas ao modelo de dispositivo para corresponder aos recursos do dispositivo.

- **Telemetria** são os pontos de dados numéricos que o seu dispositivo coleta ao longo do tempo. Eles são representados como um fluxo contínuo. Um exemplo é a temperatura.
- **As medições do evento** são dados point-in-time que representam algo significativo no dispositivo. Um nível de severidade representa a importância de um evento. Um exemplo é um erro no motor do ventilador.
- Medidas de **Estado** representam o estado do dispositivo ou os respectivos componentes durante um período de tempo. Por exemplo, um modo de ventilador pode ser definido como tendo **Operating** e **Stopped** como os dois estados possíveis.

### <a name="create-a-telemetry-measurement"></a>Criar uma medição de telemetria

Para adicionar uma nova medida de telemetria, clique em **+ Nova Medida**, escolha **Telemetria** como o tipo de medida e insira os detalhes no formulário.

> [!NOTE]
> Os nomes de campo no modelo de dispositivo precisarão corresponder aos nomes de propriedade no código do dispositivo correspondente para que a medida de telemetria seja exibida no aplicativo quando um dispositivo real estiver conectado. Faça o mesmo ao definir configurações, propriedades do dispositivo e comandos enquanto você continua a definir o modelo de dispositivo nas seções a seguir.

Por exemplo, é possível adicionar uma nova medida de telemetria de temperatura:

| Nome de exibição        | Nome do campo    |  Unidades    | Min   |max|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | temp          |  degC     |  0    |100|

![Formulário "Criar Telemetria" com detalhes para medição de temperatura](./media/howto-set-up-template-experimental/measurementsform.png)

Depois que você clicar em **Salvar**, a medida **Temperatura** será exibida na lista de medidas. Em alguns instantes, você verá a visualização dos dados de temperatura do dispositivo simulado.

> [!NOTE]
> O tipo de dados da medição de telemetria é um número de ponto flutuante.

### <a name="create-an-event-measurement"></a>Crie uma medição de evento

Para adicionar uma nova medida de evento, clique em **+ Nova Medida** e selecione **Evento** como o tipo de medida. Insira os detalhes no formulário **Criar Evento**.

Forneça os detalhes **Nome para exibição**, **Nome do campo** e **Gravidade** para o evento. Escolha um entre os três níveis de gravidade disponíveis: **Erro**, **Aviso** e **Informações**.

Por exemplo, você pode adicionar um novo evento **Fan Motor Error**.

| Nome de exibição        | Nome do campo    |  Gravidade padrão |
| --------------------| ------------- |-----------|
| Erro do motor do ventilador     | fanmotorerror |  Erro    |

![Formulário "Criar Evento" com detalhes para um evento de motor de ventilador](./media/howto-set-up-template-experimental/eventmeasurementsform.png)

Depois que você clicar em **Salvar**, a medida **Erro do Motor do Ventilador** será exibida na lista de medidas. Em alguns instantes, você verá a visualização dos dados de evento do dispositivo simulado.

Para exibir mais detalhes sobre um evento, clique no ícone do evento no gráfico:

![Detalhes para o evento "Fan Motor Error"](./media/howto-set-up-template-experimental/eventmeasurementsdetail.png)

> [!NOTE]
> O tipo de dados da medida de evento é cadeia de caracteres.

### <a name="create-a-state-measurement"></a>Crie uma medida de estado

Para adicionar uma nova medida de estado, clique no botão **+ Nova Medida** e selecione **Estado** como o tipo de medida. Insira os detalhes no formulário **Criar Estado**.

Forneça os detalhes para **Nome de exibição**, **Nome do campo** e **Valores** do estado. Cada valor também pode ter um nome de exibição que será usado quando o valor aparecer em gráficos e tabelas.

Por exemplo, você pode adicionar um novo **estado do Modo de fãs** que tenha dois valores possíveis que o dispositivo pode enviar, **Em funcionamento** e **Parado**.

| Nome de exibição | Nome do campo    |  Valor 1   | Nome de exibição | Valor 2    |Nome de exibição  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Modo ventilação     | fanmode       |  1         | Operacional    |     0      | Parado      |

![Formulário "Editar estado" com detalhes para o modo de ventilador](./media/howto-set-up-template-experimental/statemeasurementsform.png)

Depois que você clicar em **Salvar**, a medida de estado **Modo Ventilador** será exibida na lista de medidas. Em alguns instantes, você verá a visualização dos dados de estado do dispositivo simulado.

Se o dispositivo enviar muitos pontos de dados durante um curto período, a medida de estado será exibida com um visual diferente. Clique no gráfico para exibir todos os pontos de dados dentro desse período em ordem cronológica. Também é possível reduzir o intervalo de tempo para ver a medida plotada no gráfico.

> [!NOTE]
> O tipo de dados da medida de estado é cadeia de caracteres.

## <a name="settings"></a>Configurações

As configurações controlam um dispositivo. Elas permitem que os operadores forneçam entradas ao dispositivo. É possível adicionar várias configurações ao modelo de dispositivo que aparecem como blocos na guia **Configurações** para os operadores usarem. Você pode adicionar vários tipos de configurações: número, texto, data, alternância, lista de seleção e rótulo da seção.

As configurações podem estar em um dos três estados. O dispositivo informa esses estados.

- **Sincronizado**: O dispositivo foi alterado para refletir o valor da configuração.

- **Pendente**: O dispositivo está sendo alterado para o valor de configuração.

- **Erro**: O dispositivo retornou um erro.

Por exemplo, adicione uma nova configuração de velocidade do ventilador clicando em **Configurações** e inserindo a nova configuração de **Número**:

| Nome de exibição  | Nome do campo    |  Unidades  | Decimais |Inicial|
| --------------| ------------- |---------| ---------|---- |
| Velocidade da ventoinha     | fanSpeed      | RPM     | 2        | 0   |

![Formulário "Configurar Número" com detalhes para configurações de velocidade](./media/howto-set-up-template-experimental/settingsform.png)

Depois que você selecionar **Salvar**, a configuração **Velocidade do Ventilador** será exibida como um bloco. Um operador pode usar a configuração na página **Device Explorer** para alterar a velocidade do ventilador do dispositivo.

## <a name="properties"></a>propriedades

Propriedades são os metadados associados ao dispositivo, como localização do dispositivo e número de série. Adicione várias propriedades ao modelo de dispositivo que são exibidas como blocos na guia **Propriedades**. Uma propriedade pode ter um tipo, como número, texto, data, alternância, propriedade do dispositivo, rótulo ou localização. Um operador pode especificar os valores das propriedades quando cria um dispositivo e pode editar esses valores a qualquer momento. As propriedades de dispositivo são somente leitura e são enviadas do dispositivo para o aplicativo. Um operador não pode alterar as propriedades de dispositivo. Quando um dispositivo real é conectado, o bloco de propriedade de dispositivo é atualizado no aplicativo.

Existem duas categorias de propriedades:

- _Propriedades de dispositivo_ que o dispositivo relata ao aplicativo do IoT Central. As propriedades de dispositivo são valores somente leitura relatados pelo dispositivo e são atualizadas no aplicativo quando um dispositivo real é conectado.
- _Propriedades do aplicativo_ que são armazenadas no aplicativo e que podem ser editadas pelo operador. O dispositivo não reconhece as propriedades do aplicativo.

Por exemplo, adicione a última data de manutenção do dispositivo como uma nova propriedade **Data** (uma propriedade do aplicativo) à guia **Propriedades**:

| Nome de exibição  | Nome do campo | Valor inicial   |
| --------------| -----------|-----------------|
| Atendido por último      | lastServiced        | 29/01/2019     |

![Formulário "Configurar a Última Manutenção" na guia "Propriedades"](./media/howto-set-up-template-experimental/propertiesform.png)

Depois que você selecionar **Salvar**, a última data de manutenção do dispositivo será exibida como um bloco.

Depois de criar o bloco, você poderá alterar o valor da propriedade do aplicativo no **Device Explorer**.

### <a name="create-a-location-property-through-azure-maps"></a>Criar uma propriedade de localização por meio dos mapas do Azure

Você pode fornecer o contexto geográfico aos dados de localização no Azure IoT Central e mapear as coordenadas de latitude e longitude de um endereço. Se preferir, mapeie as coordenadas de latitude e longitude. O Azure Maps habilita esse recurso na IoT Central.

Você pode adicionar dois tipos de propriedades de localização:

- **Localização como uma propriedade do aplicativo**, que é armazenada no aplicativo. O dispositivo não reconhece as propriedades do aplicativo.
- **Localização como uma propriedade de dispositivo**, relatada pelo dispositivo ao aplicativo.

#### <a name="add-location-as-an-application-property"></a>Adicionar local como uma propriedade do aplicativo

Você pode criar uma propriedade de local como uma propriedade de aplicativo usando o Azure Maps no seu aplicativo IoT Central. Por exemplo, adicione o endereço de instalação do dispositivo:

1. Navegue para a guia **Propriedades**.

2. Na biblioteca, selecione **Local**.

3. Configure **Nome de exibição**, **Nome do campo** e (opcionalmente) **Valor inicial** para o local.

    | Nome de exibição  | Nome do campo | Valor inicial |
    | --------------| -----------|---------| 
    | Endereço de instalação | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formulário "Configurar localização" com detalhes para localização](./media/howto-set-up-template-experimental/locationcloudproperty2.png)

   Há dois formatos com suporte para adicionar um local:
   - **Local como um endereço**
   - **Local como coordenadas**

4. Clique em **Salvar**. Um operador pode atualizar o valor de localização no **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Adicionar local como uma propriedade de dispositivo

Você pode criar uma propriedade de localização como uma propriedade de dispositivo que o dispositivo reporta. Por exemplo, se você quiser rastrear a localização do dispositivo:

1. Navegue para a guia **Propriedades**.

2. Selecione **Propriedade do Dispositivo** na biblioteca.

3. Configure o nome de exibição e o nome de campo e selecione **Localização** como o tipo de dados:

    | Nome de exibição  | Nome do campo | Tipo de Dados |
    | --------------| -----------|-----------|
    | Localização do dispositivo | deviceLocation | location  |

   > [!NOTE]
   > Os nomes de campo devem corresponder aos nomes de propriedade no código do dispositivo correspondente

   ![Formulário "Configurar propriedades do dispositivo" com detalhes para localização](./media/howto-set-up-template-experimental/locationdeviceproperty2.png)

Depois que o dispositivo real é conectado, a localização que você adicionou como uma propriedade de dispositivo é atualizada com o valor enviado pelo dispositivo. Agora que você configurou sua propriedade de localização, é possível [adicionar um mapa para visualizar o local no painel do dispositivo](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Comandos

Os comandos são usados para gerenciar remotamente um dispositivo. Eles permitem que os operadores executem comandos no dispositivo. Você pode adicionar vários comandos ao seu modelo de dispositivo que aparecem como blocos na guia **Comandos** para os operadores usarem. Como construtor do dispositivo, você tem a flexibilidade de definir comandos de acordo com seus requisitos.

Como um comando é diferente de um cenário?

* **Configuração**: Uma configuração que você deseja aplicar a um dispositivo. Você deseja que o dispositivo persista essa configuração até você alterá-la. Por exemplo, você deseja definir a temperatura do seu freezer e desejar essa configuração mesmo quando o freezer reiniciar.

* **Comando**: Use comandos para executar de forma instantânea um comando no dispositivo remotamente por meio do IoT Central. Se um dispositivo não estiver conectado, o comando atingirá o tempo limite e falhará. Por exemplo, você deseja reiniciar um dispositivo.

Por exemplo, adicione um novo comando **Eco** selecionando a guia **Comandos** e, em seguida, clicando em **+ Novo Comando** e inserindo os detalhes do novo comando:

| Nome de exibição  | Nome do campo | Tempo limite padrão | Tipo de Dados |
| --------------| -----------|---------------- | --------- |
| Comando echo  | echo       |  30             | text      |

![Formulário "Configurar Comando" com detalhes para o echo](./media/howto-set-up-template-experimental/commandsecho.png)

Depois que você selecionar **Salvar**, o comando **Eco** será exibido como um bloco e estará pronto para ser usado no **Device Explorer** quando o dispositivo real for conectado. Os nomes de campo de seu comando precisam corresponder aos nomes de propriedade no código do dispositivo correspondente para que os comandos sejam executados com êxito.

## <a name="rules"></a>Regras

As regras permitem que os operadores monitorem os dispositivos quase em tempo real. As regras invocam automaticamente ações como o envio de um email quando a regra é acionada. Um tipo de regra está disponível hoje:

- **Regra de telemetria**, que é acionada quando a telemetria do dispositivo selecionado ultrapassa um limite especificado. [Saiba mais sobre as regras de telemetria](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="dashboard"></a>painel

O painel é onde um operador pode acessar para visualizar as informações de um dispositivo. Como construtor, você pode adicionar blocos nessa página para ajudar os operadores a entender como o dispositivo está se comportando. É possível adicionar vários blocos de painel ao modelo de dispositivo. Você pode adicionar muitos tipos de blocos do painel, como imagem, gráfico de linhas, gráfico de barras, KPI (indicador chave de desempenho), configurações, propriedades e rótulo.

Por exemplo, adicione um bloco **Configurações e Propriedades** para mostrar uma seleção dos valores atuais de configurações e propriedades selecionando a guia **Painel** e o bloco na Biblioteca:

![Formulário "Configurar detalhes do dispositivo" com detalhes para configurações e propriedades](./media/howto-set-up-template-experimental/dashboardsettingsandpropertiesform.png)

Agora, quando um operador exibir o painel no **Device Explorer**, ele poderá ver o bloco.

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Adicionar um local do Azure Maps no painel

Se você configurou uma propriedade de localização, visualize a localização usando um mapa no painel do dispositivo.

1. Navegue para a guia **Painel**.

1. No painel do dispositivo, selecione **Mapa** da biblioteca.

1. Dê um título ao mapa. O exemplo a seguir tem o título **Localização de Instalação**. Em seguida, escolha a propriedade de localização configurada anteriormente na guia **Propriedades**. No exemplo a seguir, **Endereço de instalação** está selecionado.

   ![Formulário "Configurar Mapa" com detalhes para título e propriedades](./media/howto-set-up-template-experimental/locationcloudproperty5map.png)

4. Clique em **Salvar**. O bloco do mapa agora exibe o local selecionado.

Você pode redimensionar o mapa para o tamanho desejado. Agora, quando um operador exibir o painel no **Device Explorer**, todos os blocos do painel configurados, incluindo um mapa de localização, ficarão visíveis.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a configurar um modelo de dispositivo no aplicativo Central do IoT do Azure, é possível:

> [!div class="nextstepaction"]
> [Criar uma nova versão do modelo de dispositivo](howto-version-devicetemplate.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
