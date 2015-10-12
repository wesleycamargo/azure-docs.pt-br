<properties
 pageTitle="Quais são as soluções pré-configuradas do Azure IoT | Microsoft Azure"
 description="Uma descrição das soluções pré-configuradas IoT Azure e sua arquitetura com links para recursos adicionais."
 services=""
 documentationCenter=".net"
 authors="aguilaaj"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/22/2015"
 ms.author="araguila"/>

# Quais são as soluções do Azure IoT pré-configuradas?
Você pode implantar soluções pré-configuradas que implementam cenários comuns de Internet das coisas (IoT) ao Microsoft Azure usando sua assinatura do Azure. Você pode usar essas soluções pré-configuradas: - como ponto de partida para suas próprias soluções de IoT. -Para aprender sobre os padrões mais comuns vistos no desenvolvimento e design da solução IoT.

Cada solução pré-configurada implementa um cenário comum de IoT e é uma implementação completa de ponta a ponta.

Além de implantar e executar as soluções pré-configuradas no Azure, você pode baixar o código-fonte completo para personalizar e estender a solução para atender às suas necessidades específicas.

As soluções pré-configuradas disponíveis são: - Monitoramento Remoto - Manutenção preditiva (em breve)

A tabela a seguir mostra como essas soluções pré-configuradas são mapeadas para recursos específicos de IoT:

| Solução | Ingestão de dados | Identidade do dispositivo | Comando e controle | Regras e ações | Análise preditiva |
|------------------------|----------------|-----------------|---------------------|-------------------|----------------------|
| Monitoramento remoto | Sim | Sim | Sim | Sim | - | | Manutenção preditiva | Sim | Sim | Sim | Sim | Sim |

## Visão geral do exemplo de monitoramento remoto

O monitoramento remoto é o mais simples das soluções pré-configuradas com funcionalidade completa. Esta seção descreve alguns dos principais recursos da solução pré-configurada de monitoramento remoto por meio de uma introdução ao conjunto completo de soluções pré-configuradas.

O diagrama a seguir ilustra os principais recursos da solução e as seções seguintes. As seções a seguir fornecem mais informações sobre os elementos neste diagrama.

![Arquitetura da solução pré-configurada de monitoramento remoto][img-remote-monitoring-arch]

### Dispositivo
O dispositivo que vem previamente configurado com a solução pré-configurada de monitoramento remoto é uma simulação de software de um cooler que envia os dados de telemetria de temperatura e umidade. O dispositivo também pode responder a um conjunto de comandos enviados do portal de solução por meio de Hub IoT. Os comandos que já foram implementados no simulador são: dispositivo de Ping, Iniciar Telemetria; Parar Telemetria; Alterar a Temperatura do ponto de ajuste; Telemetria do diagnóstico; e Alterar o estado do dispositivo.

Os coolers nesta solução pré-configurada correspondem aos **dispositivos e fontes de dados** em uma arquitetura da solução típica IoT.

### Hub IoT
Um hub IoT recebe dados de telemetria dos coolers em um único ponto de extremidade e mantém os pontos de extremidade específicos do dispositivo nos quais dispositivos podem recuperar comandos como o comando PingDevice.

O hub IoT expõe os dados de telemetria que recebe através de um ponto de extremidade do grupo de consumidor.

A instância do Hub IoT nesta solução pré-configurada corresponde ao **aplicativo de back-end IoT** em uma arquitetura da solução típica IoT.

### Stream Analytics do Azure
A solução pré-configurada usa trabalhos de Stream Analytics do Azure para filtrar o fluxo de eventos dos coolers. Um trabalho envia todos os dados de telemetria para os blobs de armazenamento do Azure para armazenamento frio. O segundo trabalho filtra o fluxo de eventos para mensagens de resposta de comando e mensagens de atualização de status do dispositivo e envia essas mensagens específicas para um ponto de extremidade do Hub de eventos do Azure. O terceiro trabalho filtra alarmes disparados e exibe esses alarmes na tabela de histórico de alarme na exibição do painel do portal de solução.


### Host do processador
Uma instância do processador de eventos, em execução em um trabalho de Web, processa os dados de status de resposta e o dispositivo de comando e armazena essas informações em um banco de dados do Banco de Dados de Documentos do Azure.


### Portal de solução
O Portal de solução é uma interface de usuário baseada na Web que permite: - Exibir a telemetria e alarme histórico no painel. -Provisionar novos dispositivos. -Gerenciar e monitorar dispositivos. -Enviar comandos para dispositivos específicos. -Gerenciar regras e ações.

Observação: a exibição de dispositivos do portal também mantém o Registro de identidade de dispositivo do HuB IoT sincronizado com o armazenamento de informações mais detalhadas de estado do dispositivo no banco de dados do Banco de Dados de Documentos.


## Próximas etapas
Para começar a usar as soluções pré-configuradas do Azure IoT, explore estes recursos:- [Visão geral das soluções pré-configuradas do Azure IoT](iot-suite-overview.md) - [Começar com as soluções pré-configuradas de IoT](iot-suite-getstarted-preconfigured-solutions.md)

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png

<!---HONumber=Oct15_HO1-->