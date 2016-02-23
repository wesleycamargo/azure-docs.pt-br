<properties
 pageTitle="Passo a passo da manutenção preditiva | Microsoft Azure"
 description="Um passo a passo da solução pré-configurada de manutenção preditiva do Azure IoT."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="12/02/2015"
 ms.author="araguila"/>

# Passo a passo da solução pré-configurada de manutenção preditiva

## Introdução

A solução pré-configurada de manutenção preditiva do IoT Suite é uma solução de ponta a ponta para um cenário comercial e prevê o ponto no qual há a probabilidade de ocorrer uma falha. Você pode aproveitar essa solução pré-configurada de forma pró-ativa para atividades como manutenção de otimização. A solução combina os principais serviços do Azure IoT Suite, incluindo um espaço de trabalho do [Aprendizado de Máquina do Azure][lnk_machine_learning] completo com testes para prever o RUL (Tempo restante de vida) de um motor de aeronave com base em um conjunto de dados de exemplo públicos. A solução fornece uma implementação completa do cenário comercial como um ponto de partida para o planejamento e implementação desse tipo de solução de IoT, a fim de atender aos seus requisitos de negócios específicos.

## Arquitetura lógica

O seguinte diagrama descreve os componentes lógicos da solução pré-configurada:

![][img-architecture]

Os itens em azul são os serviços do Azure provisionados no local selecionado no provisionamento da solução pré-configurada. Você pode provisionar a solução pré-configurada na região Leste dos EUA, Norte da Europa ou na Ásia Oriental.

Alguns recursos não estão disponíveis em regiões nas quais você provisiona a solução pré-configurada. Os itens em laranja no diagrama representam os serviços do Azure provisionados na região mais próxima disponível (Centro-Sul dos EUA, Europa Ocidental e Sudeste Asiático) com base na região selecionada.

O item em verde é um dispositivo simulado que representa um motor de aeronave. Você pode aprender mais sobre esses dispositivos simulados logo abaixo.

Os itens em cinza representam os componentes que implementam os recursos de *administração do dispositivo*. A versão atual da solução pré-configurada de manutenção preditiva não provisiona esses recursos. Para saber mais sobre administração do dispositivo, consulte a [solução pré-configurada de monitoramento remoto][lnk-remote-monitoring].

## Dispositivos simulados

Na solução pré-configurada, um dispositivo simulado representa um motor de aeronave. A solução é provisionada com dois motores mapeados para uma única aeronave. Cada motor emite quatro tipos de telemetria: Sensor 9, Sensor 11, Sensor 14 e Sensor 15. Todos eles fornecem os dados necessários para o modelo de Aprendizado de Máquina calcular o RUL (Restante de Vida Útil) desse motor. Cada dispositivo simulado envia as seguintes mensagens de telemetria ao Hub IoT:

*Contagem de ciclos*. Um ciclo representa um voo completo com duração variada de 2 a 10 horas, durante as quais os dados de telemetria são capturados a cada meia hora.

*Telemetria* Há quatro sensores que representam atributos do motor. Os sensores são rotulados genericamente de Sensor 9, Sensor 11, Sensor 14 e Sensor 15. Esses quatro sensores representam telemetria suficiente para obter resultados úteis do modelo de Aprendizado de Máquina para o RUL. Esse modelo é criado de um conjunto de dados públicos que inclui dados de sensores de um motor real. Para saber mais sobre como o modelo foi criado no conjunto de dados original, confira o [Modelo de manutenção preditiva da Galeria da Análise da Cortana][lnk-cortana-analytics].

Os dispositivos simulados podem lidar com os seguintes comandos enviados de um hub IoT:

| Command | Descrição |
|---------|-------------|
| StartTelemetry | Controla o estado da simulação.<br/>Inicia o dispositivo que envia a telemetria |
| StopTelemetry | Controla o estado da simulação.<br/>Para o dispositivo que envia a telemetria |

O Hub IoT fornece reconhecimento de comando do dispositivo.

## Trabalho do Stream Analytics do Azure

**Trabalho: Telemetria** funciona no fluxo de entrada da telemetria do dispositivo usando duas instruções. A primeira seleciona toda a telemetria dos dispositivos e envia esses dados ao armazenamento de blobs de onde são visualizados no aplicativo Web. A segunda instrução calcula os valores médios do sensor em uma janela deslizante de dois minutos e envia esses dados por meio do Hub de Eventos para um **processador de eventos**.

## Processador de eventos

O **processador de eventos** usa os valores médios do sensor para um ciclo completo e passa esses valores para uma API que expõe o modelo treinado de Aprendizado de Máquina para calcular o RUL de um mecanismo.

## Aprendizado de Máquina do Azure

Para saber mais sobre como o modelo foi criado no conjunto de dados original, confira o [Modelo de manutenção preditiva da Galeria da Análise da Cortana][lnk-cortana-analytics].

## Vamos começar a praticar

Esta seção apresenta os componentes da solução, descreve o caso de uso pretendido e fornece exemplos.

### Painel Manutenção Preditiva

Esta página no aplicativo Web usa controles JavaScript do PowerBI (confira o [repositório de elementos visuais do PowerBI][lnk-powerbi]) para visualizar:

- Os dados de saída de trabalhos do Stream Analytics no armazenamento de blobs.
- O RUL e a contagem de ciclo por motor da aeronave.

### Observando o comportamento da solução de nuvem

Você pode exibir seus recursos provisionados acessando o portal do Azure e navegando até o grupo de recursos com o nome da solução escolhido por você.

![][img-resource-group]

Quando você provisiona a solução pré-configurada, recebe um email com um link para o espaço de trabalho de Aprendizado de Máquina. Você também pode navegar até este espaço de trabalho de Aprendizado de Máquina da página [azureiotsuite.com][lnk-azureiotsuite] para sua solução provisionada quando ela estiver no estado **Pronto**.

![][img-machine-learning]

No portal da solução, você pode ver que a amostra é configurada com quatro dispositivos simulados a fim de representar duas aeronaves com dois motores por aeronaves e quatro sensores por motor. Quando você navega pela primeira vez até o portal de solução, a simulação é interrompida.

![][img-simulation-stopped]

Clique em **Iniciar simulação** para iniciar a simulação e ver o histórico de sensores, o RUL, os Ciclos e o histórico de RUL preenchidos no painel.

![][img-simulation-running]

Quando o RUL for menor do que 160 (um limite aleatório escolhido para fins de demonstração), o portal da solução exibirá um símbolo de aviso ao lado da exibição do RUL e aplicará a cor amarela ao motor da aeronave na imagem. Você perceberá que os valores de RUL têm uma tendência descendente geral, mas tendem a subir e a descer. Isso é resultado de durações variáveis de ciclo e da precisão do modelo.

![][img-simulation-warning]

A simulação completa leva cerca de 35 minutos para concluir 148 ciclos. O limite de 160 do RUL é atingido pela primeira vez aproximadamente aos cinco minutos, e os dois motores atingem o limite aproximadamente aos oito minutos.

A simulação percorre todo o conjunto de dados dos 148 ciclos e estabelece nos valores finais de RUL e de ciclo.

Você pode parar a simulação a qualquer momento, mas clicar em **Iniciar Simulação** repetirá a simulação desde o início do conjunto de dados.

## Próximas etapas

Agora que você executou a solução pré-configurada de manutenção preditiva, convém modificá-la. Confira a [Orientação sobre como personalizar soluções pré-configuradas][lnk-customize].

  
[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

<!---HONumber=AcomDC_0218_2016-->