---
title: Definir um novo tipo de dispositivo no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como um construtor, como definir um novo tipo de dispositivo no seu aplicativo Azure IoT Central. Você define a telemetria, estado, propriedades e configurações para o seu tipo.
author: dominicbetts
ms.author: dobett
ms.date: 10/30/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 1ed1790d9fe1cdaa8d00b45e0684531984906c7f
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55661812"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Tutorial: Definir um novo tipo de dispositivo em seu aplicativo do Azure IoT Central

Este tutorial mostra a você, como um construtor, como usar um modelo de dispositivo para definir um novo tipo de dispositivo no seu aplicativo Microsoft Azure IoT Central. Um modelo de dispositivo define a telemetria, estado, propriedades e configurações para o seu tipo de dispositivo.

Para permitir que você teste o seu aplicativo antes de conectá-lo a um dispositivo real, o IoT Central gera um dispositivo simulado a partir do modelo de dispositivo quando você o cria.

Neste tutorial, você criará um modelo de dispositivo de **Ar-condicionado conectado**. Um dispositivo de ar-condicionado conectado:

* Envia a telemetria como temperatura e umidade.
* Relata o estado, como se ele está ligado ou desligado.
* Tem propriedades de dispositivo como versão de firmware e número de série.
* Tem configurações como temperatura de destino.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um novo modelo de dispositivo
> * Adicionar telemetria ao seu dispositivo
> * Exibir telemetria simulada
> * Definir uma medida de evento
> * Exibir eventos simulados
> * Definir medida de estado
> * Exibir estado simulado
> * Usar configurações e propriedades
> * Usar comandos
> * Exibir seu dispositivo simulado no painel

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um aplicativo Azure IoT Central. Se você concluiu o início rápido [Criar um aplicativo Azure IoT Central](quick-deploy-iot-central.md), você pode reutilizar o aplicativo criado no início rápido. Caso contrário, conclua as seguintes etapas para criar um aplicativo Azure IoT Central vazio:

1. Navegue até a página do [Gerenciador de aplicativos](https://aka.ms/iotcentral) do Azure IoT Central.

2. Digite o endereço de email e a senha que você usa para acessar sua assinatura do Azure:

   ![Insira a conta da sua organização](./media/tutorial-define-device-type/sign-in.png)

3. Para começar a criar um novo aplicativo Azure IoT Central, escolha **Novo aplicativo**:

    ![Página do Gerenciador de aplicativos do Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Para criar um novo aplicativo Azure IoT Central:
    
    * Escolha **Versão de avaliação**. Você não precisa de uma assinatura do Azure para criar um aplicativo de Versão de avaliação.
    
       Para obter mais informações sobre assinaturas e diretórios, consulte o [criar um início rápido de aplicativo](quick-deploy-iot-central.md).
    
    * Escolha **Aplicativo personalizado**.
    
    * Opcionalmente, você pode escolher um nome amigável do aplicativo, como **Ar-condicionados Contoso**. O Azure IoT Central gera um prefixo de URL exclusivo para você. Você pode alterar esse prefixo de URL para algo mais fácil de lembrar.
    
    * Escolha **Criar**.

    ![Página Criar aplicativo do Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

    Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).

## <a name="create-a-new-custom-device-template"></a>Criar um novo modelo de dispositivo personalizado

Como um construtor, você pode criar e editar os modelos de dispositivo no seu aplicativo. Quando você cria um modelo de dispositivo, o Azure IoT Central gera um dispositivo simulado a partir do modelo. O dispositivo simulado gera telemetria que permite que você teste o comportamento do seu aplicativo antes de você conectar um dispositivo real.

Para adicionar um novo modelo de dispositivo ao seu aplicativo, você precisa ir até a página **Construtor do aplicativo**. Para fazer isso, escolha o **Construtor de aplicativo** no menu de navegação à esquerda.

![Página Construtor do aplicativo](./media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>Adicionar um dispositivo e definir a telemetria

As etapas a seguir mostram como criar um novo modelo de dispositivo de **Ar-condicionado conectado** para dispositivos que enviam telemetria de temperatura para o seu aplicativo:

1. Na página **Construtor do aplicativo**, escolha **Criar modelo de dispositivo**:

    ![Página Construtor do aplicativo, Criar modelo de dispositivo](./media/tutorial-define-device-type/builderhomedevices.png)

2. Na página **Modelos de dispositivo** escolha **Personalizado**. Um modelo de dispositivo **Personalizado** permite que você defina todas as características e comportamentos de seu ar-condicionado conectado:

    ![Dispositivos](./media/tutorial-define-device-type/builderhomedevicescustom.png)

3. Na página **Novo modelo de dispositivo**, insira **Ar-condicionado conectado** como o nome do seu dispositivo e, em seguida, escolha **Criar**. Você também pode carregar uma imagem do seu dispositivo que é visível para os operadores no Device Explorer:

    ![Dispositivo personalizado](./media/tutorial-define-device-type/createcustomdevice.png)

4. No modelo de dispositivo **Ar-condicionado**, verifique se você está na página **Medidas** onde você define a telemetria. Cada modelo de dispositivo que você definir possui páginas separadas para você:

    * Especifica as medidas, como telemetria, evento e estado, enviadas pelo dispositivo.
    
    * Define as configurações usadas para controlar o dispositivo.
    
    * Defina as propriedades que são os metadados do dispositivo.

    * Defina os comandos a serem executados diretamente no dispositivo.
    
    * Define as regras associadas ao dispositivo.
    
    * Personaliza o painel de dispositivo para os operadores.

    Sempre que estiver definindo o modelo de dispositivo, escolha **Editar modelo** primeiro para editar o modelo. Quando tiver terminado, escolha **Concluído**. 

    ![Medidas do ar-condicionado](./media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > Para alterar o nome do dispositivo ou do modelo de dispositivo, clique no texto na parte superior da página.

5. Para adicionar a medida de telemetria de temperatura, escolha **Nova medida**. Em seguida, escolha **Telemetria** como o tipo de medida:

    ![Medidas do ar-condicionado conectado](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Cada tipo de telemetria que você define para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template.md) como:

    * Opções de exibição.

    * Detalhes da telemetria.

    * Parâmetros de simulação.

    Para configurar sua telemetria de **Temperatura**, use as informações na tabela a seguir:

    | Configuração              | Valor         |
    | -------------------- | -----------   |
    | Nome de exibição         | Temperatura   |
    | Nome do campo           | temperatura   |
    | Unidades                | F             |
    | Min                  | 60            |
    | max                  | 110           |
    | Casas decimais       | 0             |

    Você também pode escolher uma cor para a exibição da telemetria. Para salvar a definição de telemetria, escolha **Salvar**:

    ![Configurar a simulação de temperatura](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Após um curto tempo, a página **Medidas** mostra um gráfico de telemetria de temperatura do dispositivo simulado de ar-condicionado conectado. Use os controles para gerenciar a visibilidade, agregação, ou para editar a definição de telemetria:

    ![Exibir a simulação de temperatura](./media/tutorial-define-device-type/viewsimulation.png)

8. Você também pode personalizar o gráfico usando os controles **Linha**, **Empilhado** e **Editar intervalo de tempo**:

    ![Personalizar o gráfico](./media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>Definir uma medida de evento

Você pode usar o Evento para definir dados pontuais que são enviados pelo dispositivo para indicar algo significativo, como um erro ou falha de um componente. Assim como as medidas de telemetria, o Azure IoT Central pode simular eventos de dispositivo para que você possa testar o comportamento do seu aplicativo antes de conectar um dispositivo real. Você define as medidas de evento para o tipo de dispositivo na exibição **Medidas**.

1. Para adicionar a medida de evento **Erro de motor do ventilador**, escolha **Nova medida**. Em seguida, escolha **Evento** como o tipo de medida:

    ![Medidas do ar-condicionado conectado](./media/tutorial-define-device-type/eventnew.png)

2. Cada tipo de Evento que você define para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template.md) como:

   * Nome de exibição.

   * Nome do campo.

   * Gravidade.

    Para configurar seu evento de **Erro do motor do ventilador**, use as informações na tabela a seguir:

    | Configuração              | Valor             |
    | -------------------- | -----------       |
    | Nome de exibição         | Erro do motor do ventilador   |
    | Nome do campo           | fanmotorerr       |
    | Severity             | Erro             |

    Para salvar a definição de telemetria, escolha **Salvar**:

    ![Configurar uma medida de evento](./media/tutorial-define-device-type/eventconfiguration.png)

3. Após um curto tempo, a página **Medidas** mostra um gráfico de eventos gerados aleatoriamente a partir do dispositivo simulado de ar-condicionado conectado. Use os controles para gerenciar a visibilidade ou para editar a definição de evento:

    ![Exibir evento simulado](./media/tutorial-define-device-type/eventview.png)

1. Para ver detalhes adicionais sobre o evento, clique no evento no gráfico:

    ![Exibir detalhes do evento](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>Definir medida de Estado

Você pode usar o Estado para definir e visualizar o estado do dispositivo ou seu componente ao longo de um período de tempo. Assim como as medidas de telemetria, o Azure IoT Central pode simular um estado de dispositivo para que você possa testar o comportamento do seu aplicativo antes de conectar um dispositivo real. Você define as medidas de estado para o seu tipo de dispositivo na exibição **Medidas**.

1. Para adicionar a medida **Modo ventilação**, escolha **Nova medida**. Em seguida, escolha **Estado** como o tipo de medida:

    ![Medidas de estado do ar-condicionado conectado](./media/tutorial-define-device-type/statenew.png)

2. Cada tipo de Estado que você define para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template.md) como:

   * Nome de exibição.

   * Nome do campo.

   * Valores com rótulos de exibição opcionais.

   * Cor de cada valor.

    Para configurar seu estado de **Modo ventilação**, use as informações na tabela a seguir:

    | Configuração              | Valor             |
    | -------------------- | -----------       |
    | Nome de exibição         | Modo ventilação          |
    | Nome do campo           | fanmode           |
    | Valor                | 1                 |
    | Rótulo de exibição        | Operacional         |
    | Valor                | 0                 |
    | Rótulo de exibição        | Parado           |

    Para salvar a definição de medida de estado, escolha **Salvar**:

    ![Configurar medida de Estado](./media/tutorial-define-device-type/stateconfiguration.png)

3. Após um curto tempo, a página **Medidas** mostra um gráfico de estados gerados aleatoriamente a partir do dispositivo simulado de ar-condicionado conectado. Use os controles para gerenciar a visibilidade ou para editar a definição de estado:

    ![Exibir simulação de estado](./media/tutorial-define-device-type/stateview.png)

4. No caso, há muitos pontos de dados enviados pelo dispositivo dentro de uma pequena duração, a medida de estado é mostrada com um visual diferente, conforme mostrado abaixo. Se você clicar no gráfico, todos os pontos de dados dentro desse período de tempo serão exibidos em uma ordem cronológica. Também é possível reduzir o intervalo de tempo para ver as medidas com mais detalhes.

    ![Exibir Detalhes do estado](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>Configurações, propriedades e comandos

Configurações, propriedades e comandos são valores diferentes definidos em um modelo de dispositivo e associados a cada dispositivo individual:

* Você usa _configurações_ para enviar dados de configuração para um dispositivo a partir de seu aplicativo. Por exemplo, um operador pode usar uma configuração para alterar o intervalo de telemetria do dispositivo de dois segundos para cinco segundos. Quando um operador altera uma configuração, a configuração é marcada como pendente na interface de usuário até que o dispositivo confirme que ele acionou a alteração de configuração.

* Você usa _propriedades_ para definir metadados associados ao seu dispositivo. Existem duas categorias de propriedades:
    
    * Você usa _propriedades de aplicativo_ para registrar informações sobre seu dispositivo no seu aplicativo. Por exemplo, você pode usar as propriedades do aplicativo para registrar o local de um dispositivo e sua última data de serviço. Essas propriedades são armazenadas no aplicativo e não serão sincronizadas com o dispositivo. Um operador pode atribuir valores às propriedades.

    * Você usa _propriedades do dispositivo_ para habilitar um dispositivo para envio de valores de propriedade para o seu aplicativo. Essas propriedades só podem ser alteradas pelo dispositivo. Para um operador, as propriedades do dispositivo são somente leitura. Nesse cenário de um ar-condicionado conectado, o número de série do dispositivo e a versão de firmware são propriedades do dispositivo relatadas pelo dispositivo. 
    
    Para obter mais informações, consulte [Propriedades][lnk-define-template] no guia de instruções sobre como configurar um modelo de dispositivo.

* Você usa _comandos_ para gerenciar remotamente seu dispositivo a partir de seu aplicativo. Você pode executar comandos diretamente no dispositivo da nuvem para controlar os dispositivos. Por exemplo, um operador pode executar comandos como reinicialização para reinicializar o dispositivo instantaneamente.

## <a name="use-settings"></a>Use as configurações

Você usa *configurações* para permitir que um operador envie dados de configuração para um dispositivo. Nesta seção, você adiciona uma configuração para o seu modelo de dispositivo de **Ar-condicionado conectado** que permite que um operador defina a temperatura de destino do ar-condicionado conectado.

1. Navegue até a página **Configurações** para o seu modelo de dispositivo de **Ar-condicionado conectado**:

    ![Preparar para adicionar uma configuração](./media/tutorial-define-device-type/deviceaddsetting.png)

    Você pode criar configurações de tipos diferentes, como números ou texto.

2. Escolha **Número** para adicionar uma configuração de número em seu dispositivo.

3. Para definir sua configuração de **Temperatura definida**, use as informações na tabela a seguir:

    | Campo                | Valor           |
    | -------------------- | -----------     |
    | Nome de exibição         | Temperatura definida |
    | Nome do campo           | setTemperature  |
    | Unidade de medida      | F               |
    | Casas Decimais       | 1               |
    | Valor mínimo        | 20              |
    | Valor máximo        | 200             |
    | Valor inicial        | 80              |
    | DESCRIÇÃO          | Defina a temperatura de destino para o ar-condicionado |

    Depois escolha **Salvar**:

    ![Definir configuração de Temperatura definida](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Quando o dispositivo confirmar a alteração da configuração, o status da configuração mudará para **sincronizado**.

4. Você pode personalizar o layout da página **Configurações** movendo e redimensionando blocos de configurações:

    ![Personalizar layout das configurações](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Usar propriedades 

Você usa *propriedades do aplicativo* para registrar informações sobre seu dispositivo no seu aplicativo. Nesta seção, você adiciona as propriedades do aplicativo para seu modelo de dispositivo **Ar-Condicionado Conectado** para armazenar o local do dispositivo e a última data de serviço. Observe que ambas são propriedades editáveis do dispositivo. Também há propriedades somente leitura do dispositivo relatadas pelo dispositivo que não podem ser alteradas, como o número de série do dispositivo e a versão de firmware.
 
1. Navegue até a página **Propriedades** para o seu modelo de dispositivo de **Ar-condicionado conectado**:

    ![Preparar para adicionar uma propriedade](./media/tutorial-define-device-type/deviceaddproperty.png)

    Você pode criar propriedades de dispositivo de tipos diferentes, como números ou texto. Para adicionar uma propriedade local para seu modelo de dispositivo, escolha **Local**.

1. Para configurar sua propriedade de local, use as informações na tabela a seguir:

    | Campo                | Valor                |
    | -------------------- | -------------------- |
    | Nome de exibição         | Local padrão             |
    | Nome do campo           | location             |
    | Valor inicial        | Seattle, WA          |
    | DESCRIÇÃO          | Localização do dispositivo      |

    Deixe os outros campos com os seus respectivos valores padrão.

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type/configureproperties.png)

    Escolha **Salvar**.

1. Para adicionar uma propriedade de última data de serviço ao seu modelo de dispositivo, escolha **Data**.

1. Para configurar a sua última propriedade data de serviço, use as informações na tabela a seguir:

    | Campo                | Valor                   |
    | -------------------- | ----------------------- |
    | Nome de exibição         | Data do Último Serviço       |
    | Nome do campo           | serviceDate             |
    | Valor inicial        | 01/01/2018                |
    | DESCRIÇÃO          | Atendido por último           |

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type/configureproperties2.png)

    Escolha **Salvar**.

5. Você pode personalizar o layout da página **Propriedades** movendo e redimensionando blocos de propriedade:

    ![Personalizar o layout de propriedades](./media/tutorial-define-device-type/propertieslayout.png)

1. Para adicionar uma propriedade do dispositivo como versão de firmware para o seu modelo de dispositivo, escolha **Propriedade do dispositivo**.

1.  Para configurar sua versão de firmware, use as informações na tabela a seguir:

    | Campo                | Valor                   |
    | -------------------- | ----------------------- |
    | Nome de exibição         | Versão do firmware        |
    | Nome do campo           | firmwareVersion         |
    | Tipo de Dados            | text                    |
    | DESCRIÇÃO          | A versão de firmware de ar-condicionado |

    ![Configurar versão de firmware](./media/tutorial-define-device-type/configureproperties3.png)
    
    Escolha **Salvar**.

1. Para adicionar uma propriedade do dispositivo como número de série para o seu modelo de dispositivo, escolha **Propriedade do dispositivo**.

1. Para configurar o número de série, use as informações na tabela a seguir:

    | Campo                | Valor                   |
    | -------------------- | ----------------------- |
    | Nome de exibição         | Número de série           |
    | Nome do campo           | serialNumber            |
    | Tipo de Dados            | text                    |
    | DESCRIÇÃO          | O número de série do ar-condicionado  |

    ![Configurar número de série](./media/tutorial-define-device-type/configureproperties4.png)
    
    Escolha **Salvar**.
    
    > [!NOTE]
    > A propriedade do dispositivo é enviada do dispositivo para o aplicativo. Os valores de versão de firmware e do número de série serão atualizados quando o dispositivo real se conectar ao IoT Central.

## <a name="use-commands"></a>Usar comandos

Use _comandos_ para habilitar um operador a executar comandos diretamente no dispositivo. Nesta seção, você adicionará um comando ao modelo de dispositivo de **Ar Condicionado Conectado** que permite que um operador ecoe determinada mensagem sobre a exibição do ar condicionado conectado.

1. Navegue até a página **Comandos** do seu modelo de dispositivo de **Ar Condicionado Conectado** para editar o modelo. 

1. Clique em **Novo comando** para adicionar um comando ao seu dispositivo e começar a configurar seu novo comando.

   Você pode criar comandos de tipos diferentes de acordo com suas necessidades. 

1. Para configurar seu novo comando, use as informações da tabela a seguir:

    | Campo                | Valor           |
    | -------------------- | -----------     |
    | Nome de exibição         | Comando echo    |
    | Nome do campo           | echo            |
    | Tempo limite padrão      | 30              |
    | Exibir tipo         | text            |
    | DESCRIÇÃO          | Comando do dispositivo  |  

    Você pode adicionar outras entradas no comando clicando em **+** para **Campos de entrada**.

    ![Preparar para adicionar uma configuração](media/tutorial-define-device-type/commandsecho1.png)

     Escolha **Salvar**.

1. Você pode personalizar o layout da página **Comandos** movendo e redimensionando blocos de comandos:

    ![Personalizar layout das configurações](media/tutorial-define-device-type/commandstileresize1.png)

## <a name="view-your-simulated-device"></a>Exibir seu dispositivo simulado

Agora que você definiu seu modelo de dispositivo de **Ar-condicionado conectado**, é possível personalizar seu **Painel** para incluir as medidas, configurações e propriedades que você definiu. Em seguida, você pode visualizar o painel como um operador:

1. Escolha a página **Painel** para o seu modelo de dispositivo de **Ar-condicionado conectado**:

    ![Painéis de ar-condicionado conectado](./media/tutorial-define-device-type/aircondashboards.png)

1. Escolha **Gráfico de linhas** para adicionar o componente no **Painel**:

    ![Componentes do Painel](./media/tutorial-define-device-type/dashboardcomponents1.png)

1. Configure o componente **Gráfico de linhas** usando as informações na tabela a seguir:

    | Configuração      | Valor       |
    | ------------ | ----------- |
    | Title        | Temperatura |
    | Intervalo de tempo   | Últimos 30 minutos |
    | Medidas     | temperatura (escolha **Visibilidade** ao lado de **temperatura**) |

    ![Configurações do gráfico de linhas](./media/tutorial-define-device-type/linechartsettings.png)

    Depois escolha **Salvar**.

1. Configure o componente **Histórico de Eventos** usando as informações na tabela a seguir:

    | Configuração      | Valor       |
    | ------------ | ----------- |
    | Title        | Eventos |
    | Intervalo de tempo   | Últimos 30 minutos |
    | Medidas     | Erro de motor do ventilador (escolha **Visibilidade** ao lado de **Erro de motor do ventilador**) |

    ![Configurações do gráfico de linhas](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Depois escolha **Salvar**.

1. Configure o componente **Histórico de Estado** usando as informações na tabela a seguir:

    | Configuração      | Valor       |
    | ------------ | ----------- |
    | Title        | Modo ventilação |
    | Intervalo de tempo   | Últimos 30 minutos |
    | Medidas | Modo ventilação (escolha **Visibilidade** ao lado de **Modo ventilação**) |

    ![Configurações do gráfico de linhas](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Depois escolha **Salvar**.

1. Para adicionar a configuração de temperatura definida ao painel, escolha **Configurações e propriedades**. Clique em **Adicionar/Remover** para adicionar as configurações ou propriedades que você gostaria de ver no painel. 

    ![Componentes do Painel](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Configure o componente **Configurações e propriedades** usando as informações na tabela a seguir:

    | Configuração                 | Valor         |
    | ----------------------- | ------------- |
    | Title                   | Definir temperatura de destino |
    | Configurações e propriedades | Temperatura definida |

    As configurações e propriedades que você definiu anteriormente nas páginas de Configurações e propriedades são mostradas nas Colunas disponíveis. 

    ![Definir as configurações de propriedade de temperatura](./media/tutorial-define-device-type/propertysettings4.png)

    Em seguida escolha **OK**.

1. Para adicionar o número de série do dispositivo ao painel, escolha **Configurações e propriedades**:

    ![Componentes do Painel](./media/tutorial-define-device-type/dashboardcomponents3.png)

1. Configure o componente **Configurações e propriedades** usando as informações na tabela a seguir:

    | Configuração                 | Valor         |
    | ----------------------- | ------------- |
    | Title                   | Número de série |
    | Configurações e propriedades | Número de série |

    ![Configurações de propriedade de número de série](./media/tutorial-define-device-type/propertysettings5.png)

    Em seguida escolha **OK**.

1. Para adicionar a versão de firmware do dispositivo ao painel, escolha **Configurações e propriedades**:

    ![Componentes do Painel](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Configure o componente **Configurações e propriedades** usando as informações na tabela a seguir:

    | Configuração                 | Valor            |
    | ----------------------- | ---------------- |
    | Title                   | Versão do firmware |
    | Configurações e propriedades | Versão do firmware |

    ![Configurações de propriedade de número de série](./media/tutorial-define-device-type/propertysettings6.png)

    Em seguida escolha **OK**.

1. Para visualizar o painel como um operador, desative **Editar modelo** na parte superior direita da página.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Criar um novo modelo de dispositivo
> * Adicionar telemetria ao seu dispositivo
> * Exibir telemetria simulada
> * Definir eventos de dispositivo
> * Exibir eventos simulados
> * Definir seu estado
> * Exibir estado simulado
> * Usar configurações e propriedades
> * Usar comandos
> * Exibir seu dispositivo simulado no painel

Agora que você definiu um modelo de dispositivo no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md)
* [Personalizar os modos de exibição do operador](tutorial-customize-operator.md)

[lnk-define-template]: /azure/iot-central/howto-set-up-template#properties