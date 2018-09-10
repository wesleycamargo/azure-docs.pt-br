---
title: Definir um novo tipo de dispositivo no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como um construtor, como definir um novo tipo de dispositivo no seu aplicativo Azure IoT Central. Você define a telemetria, estado, propriedades e configurações para o seu tipo.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 4c850cd98280940c53a07c70c9c478f9e20e2963
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41920248"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Tutorial: Definir um novo tipo de dispositivo em seu aplicativo do Azure IoT Central

Este tutorial mostra a você, como um construtor, como usar um modelo de dispositivo para definir um novo tipo de dispositivo no seu aplicativo Microsoft Azure IoT Central. Um modelo de dispositivo define a telemetria, estado, propriedades e configurações para o seu tipo de dispositivo.

Para permitir que você teste o seu aplicativo antes de conectá-lo a um dispositivo real, o Azure IoT Central gera um dispositivo simulado a partir do modelo de dispositivo quando você o cria.

Neste tutorial, você criará um modelo de dispositivo de **Ar-condicionado conectado**. Um dispositivo de ar-condicionado conectado:

* Envia a telemetria como temperatura e umidade.
* Relata o estado, como se ele está ligado ou desligado.
* Tem propriedades como versão de firmware e número de série.
* Tem configurações como temperatura de destino e velocidade do ventilador.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um novo modelo de dispositivo
> * Adicionar telemetria ao seu dispositivo
> * Exibir telemetria simulada
> * Definir uma medida de evento
> * Exibir eventos simulados
> * Definir medida de estado
> * Exibir estado simulado
> * Usar propriedades do dispositivo
> * Usar configurações do dispositivo
> * Usar comandos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um aplicativo Azure IoT Central. Se você concluiu o início rápido [Criar um aplicativo Azure IoT Central](quick-deploy-iot-central.md), você pode reutilizar o aplicativo criado no início rápido. Caso contrário, conclua as seguintes etapas para criar um aplicativo Azure IoT Central vazio:

1. Navegue até a página do [Gerenciador de aplicativos](https://aka.ms/iotcentral) do Azure IoT Central.

2. Digite o endereço de email e a senha que você usa para acessar sua assinatura do Azure:

   ![Insira a conta da sua organização](./media/tutorial-define-device-type/sign-in.png)

3. Para começar a criar um novo aplicativo Azure IoT Central, escolha **Novo aplicativo**:

    ![Página do Gerenciador de aplicativos do Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Para criar um novo aplicativo Azure IoT Central:

    * Escolha um nome amigável do aplicativo, como **Ar-condicionados Contoso**. O Azure IoT Central gera um prefixo de URL exclusivo para você. Você pode alterar esse prefixo de URL para algo mais fácil de lembrar.
    
    * Escolha uma assinatura do Azure Active Directory e do Azure para serem usadas. Para obter mais informações sobre assinaturas e diretórios, consulte [Criar um aplicativo Azure IoT Central](howto-create-application.md).
    
    * Selecione um grupo de recursos existente ou crie um novo grupo de recursos com um nome de sua escolha. Por exemplo, **contoso-rg**.
    
    * Escolha a região geograficamente mais próxima de você.
    
    * Escolha o modelo de aplicativo **Aplicativo personalizado**.
    
    * Escolha o plano de pagamento **Avaliação gratuita de 30 dias de aplicativo**.
    
    * Escolha **Criar**.

    ![Página Criar aplicativo do Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

Para obter mais informações, consulte [Como criar o aplicativo Azure IoT Central](howto-create-application.md).

## <a name="create-a-new-custom-device-template"></a>Criar um novo modelo de dispositivo personalizado

Como um construtor, você pode criar e editar os modelos de dispositivo no seu aplicativo. Quando você cria um modelo de dispositivo, o Azure IoT Central gera um dispositivo simulado a partir do modelo. O dispositivo simulado gera telemetria que permite que você teste o comportamento do seu aplicativo antes de você conectar um dispositivo físico.

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
    
    * Define as propriedades usadas para registrar informações sobre o dispositivo.
    
    * Define as regras associadas ao dispositivo.
    
    * Personaliza o painel de dispositivo para os operadores.

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

Você pode usar o Evento para definir dados pontuais que são enviados pelo dispositivo para indicar algo significativo, como um erro ou falha de um componente. Assim como as medidas de telemetria, o Azure IoT Central pode simular eventos de dispositivo para que você possa testar o comportamento do seu aplicativo antes de conectar um dispositivo físico. Você define as medidas de evento para o tipo de dispositivo na exibição **Medidas**.

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

Você pode usar o Estado para definir e visualizar o estado do dispositivo ou seu componente ao longo de um período de tempo. Assim como as medidas de telemetria, o Azure IoT Central pode simular o estado do dispositivo para que você possa testar o comportamento do seu aplicativo antes de conectar um dispositivo físico. Você define as medidas de estado para o seu tipo de dispositivo na exibição **Medidas**.

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

4. No caso, há muitos pontos de dados enviados pelo dispositivo dentro de uma pequena duração, a medida de estado é mostrada com um visual diferente, conforme mostrado abaixo. Se você clicar no gráfico, todos os pontos de dados dentro desse período de tempo serão exibidos em uma ordem cronológica. Também é possível reduzir o intervalo de tempo para ver a medida plotada no gráfico.

    ![Exibir Detalhes do estado](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>Configurações, propriedades e comandos

Configurações, propriedades e propriedades e comandos do dispositivo são valores diferentes definidos em um modelo de dispositivo e associados a cada dispositivo individual:

* Você usa _configurações_ para enviar dados de configuração para um dispositivo a partir de seu aplicativo. Por exemplo, um operador pode usar uma configuração para alterar o intervalo de telemetria do dispositivo de dois segundos para cinco segundos. Quando um operador altera uma configuração, a configuração é marcada como pendente na interface de usuário até que o dispositivo confirme que ele acionou a alteração de configuração.

* Você usa _propriedades_ para registrar informações sobre seu dispositivo no seu aplicativo. Por exemplo, você pode usar propriedades para registrar o número de série do dispositivo ou o número de telefone do fabricante do dispositivo. Propriedades são armazenadas no aplicativo e não serão sincronizadas com o dispositivo. Um operador pode atribuir valores às propriedades.

* Você usa _propriedades do dispositivo_ para habilitar um dispositivo para envio de valores de propriedade para o seu aplicativo. Essas propriedades só podem ser alteradas pelo dispositivo. Para um operador, as propriedades do dispositivo são somente leitura.

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
    | Unidade de medida  | F               |
    | Casas decimais       | 1               |
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

## <a name="use-properties--device-properties"></a>Propriedades de uso/propriedades de dispositivo

Você usa *propriedades* para registrar informações sobre seu dispositivo no seu aplicativo. Nesta seção, você adicionará propriedades de dispositivo ao seu modelo de dispositivo de **Ar-condicionado conectado** para armazenar o número de série do dispositivo e a versão do firmware. Observe que ambos são propriedades somente leitura relatadas pelo dispositivo — não é possível atribuir valores a elas. Propriedades que você pode usar às quais podem ser atribuídos valores para incluir o local do dispositivo, as informações de propriedade e a última data/hora do serviço para o dispositivo.
 
1. Navegue até a página **Propriedades** para o seu modelo de dispositivo de **Ar-condicionado conectado**:

    ![Preparar para adicionar uma propriedade](./media/tutorial-define-device-type/deviceaddproperty.png)

    Você pode criar propriedades de dispositivo de tipos diferentes, como números ou texto. Para adicionar uma propriedade de número de série para o modelo de dispositivo, escolha **Texto**.

2. Para configurar sua propriedade de número de série, use as informações na tabela a seguir:

    | Campo                | Valor                |
    | -------------------- | -------------------- |
    | Nome de exibição         | Número de série        |
    | Nome do campo           | serialNumber         |
    | Valor inicial        | cac00001             |
    | DESCRIÇÃO          | Número de série do dispositivo |

    Deixe os outros campos com os seus respectivos valores padrão.

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type/configureproperties.png)

    Escolha **Salvar**.

3. Para adicionar uma versão de firmware às propriedades de dispositivo do seu modelo de dispositivo, escolha **Texto**.

4. Para configurar sua propriedade de dispositivo da versão de firmware, use as informações da tabela a seguir:

    | Campo                | Valor                   |
    | -------------------- | ----------------------- |
    | Nome de exibição         | Versão do firmware        |
    | Nome do campo           | firmwareVersion         |
    | Valor inicial        | 0,1                     |
    | DESCRIÇÃO          | Versão do firmware do dispositivo |

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type/configureproperties2.png)

    Escolha **Salvar**.

5. Você pode personalizar o layout da página **Propriedades** movendo e redimensionando blocos de propriedade:

    ![Personalizar o layout de propriedades](./media/tutorial-define-device-type/propertieslayout.png)


## <a name="use-commands"></a>Usar comandos

Use _comandos_ para habilitar um operador a executar comandos diretamente no dispositivo. Nesta seção, você adicionará um comando ao modelo de dispositivo de **Ar Condicionado Conectado** que permite que um operador ecoe determinada mensagem sobre a exibição do ar condicionado conectado (isso funciona com o código de exemplo MxChip).

1. Navegue até a página **Comandos** do seu modelo de dispositivo de **Ar Condicionado Conectado**:

    ![Preparar para adicionar uma configuração](media/tutorial-define-device-type/commandsecho.png)

    Você pode criar comandos de tipos diferentes de acordo com suas necessidades. 

1. Clique em **Novo Comando** para adicionar um comando ao seu dispositivo.

1. Para configurar seu novo comando, use as informações da tabela a seguir:

    | Campo                | Valor           |
    | -------------------- | -----------     |
    | Nome de exibição         | Comando echo    |
    | Nome do campo           | echo            |
    | Tempo limite padrão      | 30              |
    | Exibir tipo         | text            |
    | DESCRIÇÃO          | Comando do dispositivo  |  

Você pode adicionar outras entradas no comando clicando em **+** para entradas.

2. Escolha **Salvar**.

3. Você pode personalizar o layout da página **Comandos** movendo e redimensionando blocos de comandos:

    ![Personalizar layout das configurações](media/tutorial-define-device-type/commandstileresize.png)

## <a name="view-your-simulated-device"></a>Exibir seu dispositivo simulado

Agora que você definiu seu modelo de dispositivo de **Ar-condicionado conectado**, é possível personalizar seu **Painel** para incluir as medidas, configurações e propriedades que você definiu. Em seguida, você pode visualizar o painel como um operador:

1. Escolha a página **Painel** para o seu modelo de dispositivo de **Ar-condicionado conectado**:

    ![Painéis de ar-condicionado conectado](./media/tutorial-define-device-type/aircondashboards.png)

2. Escolha **Gráfico de linhas** para adicionar o componente no **Painel**:

    ![Componentes do Painel](./media/tutorial-define-device-type/dashboardcomponents1.png)

3. Configure o componente **Gráfico de linhas** usando as informações na tabela a seguir:

    | Configuração      | Valor       |
    | ------------ | ----------- |
    | Title        | Temperatura |
    | Intervalo de tempo   | Últimos 30 minutos |
    | Medidas | temperatura (escolha **Visibilidade** ao lado de **temperatura**) |

    ![Configurações do gráfico de linhas](./media/tutorial-define-device-type/linechartsettings.png)

    Depois escolha **Salvar**.

4. Configure o componente **Gráfico de Eventos** usando as informações na tabela a seguir:

    | Configuração      | Valor       |
    | ------------ | ----------- |
    | Title        | Eventos |
    | Intervalo de tempo   | Últimos 30 minutos |
    | Medidas | Erro de motor do ventilador (escolha **Visibilidade** ao lado de **Erro de motor do ventilador**) |

    ![Configurações do gráfico de linhas](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Depois escolha **Salvar**.

5. Configure o componente **Gráfico de Estado** usando as informações na tabela a seguir:

    | Configuração      | Valor       |
    | ------------ | ----------- |
    | Title        | Modo ventilação |
    | Intervalo de tempo   | Últimos 30 minutos |
    | Medidas | Modo ventilação (escolha **Visibilidade** ao lado de **Modo ventilação**) |

    ![Configurações do gráfico de linhas](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Depois escolha **Salvar**.

6. Para adicionar a configuração de temperatura definida ao painel, escolha **Configurações e propriedades**:

    ![Componentes do Painel](./media/tutorial-define-device-type/dashboardcomponents4.png)

7. Configure o componente **Configurações e propriedades** usando as informações na tabela a seguir:

    | Configuração                 | Valor         |
    | ----------------------- | ------------- |
    | Title                   | Definir temperatura de destino |
    | Configurações e propriedades | Temperatura definida |

    ![Configurações de propriedade de número de série](./media/tutorial-define-device-type/propertysettings3.png)

    Depois escolha **Salvar**.

8. Para adicionar o número de série do dispositivo ao painel, escolha **Configurações e propriedades**:

    ![Componentes do Painel](./media/tutorial-define-device-type/dashboardcomponents3.png)

9. Configure o componente **Configurações e propriedades** usando as informações na tabela a seguir:

    | Configuração                 | Valor         |
    | ----------------------- | ------------- |
    | Title                   | Número de série |
    | Configurações e propriedades | Número de série |

    ![Configurações de propriedade de número de série](./media/tutorial-define-device-type/propertysettings1.png)

    Depois escolha **Salvar**.

10. Para adicionar a versão de firmware do dispositivo ao painel, escolha **Configurações e propriedades**:

    ![Componentes do Painel](./media/tutorial-define-device-type/dashboardcomponents4.png)

11. Configure o componente **Configurações e propriedades** usando as informações na tabela a seguir:

    | Configuração                 | Valor            |
    | ----------------------- | ---------------- |
    | Title                   | Versão do firmware |
    | Configurações e propriedades | Versão do Firmware |

    ![Configurações de propriedade de número de série](./media/tutorial-define-device-type/propertysettings2.png)

    Depois escolha **Salvar**.

12. Para visualizar o painel como um operador, desative **Modo de Design** na parte superior direita da página.

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
> * Usar propriedades do dispositivo
> * Usar configurações do dispositivo

Agora que você definiu um modelo de dispositivo no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md)
* [Personalizar os modos de exibição do operador](tutorial-customize-operator.md)
