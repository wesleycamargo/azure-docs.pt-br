<properties
	pageTitle="Visão geral do Microsoft Azure IoT Suite | Microsoft Azure"
	description="Isso fornece uma visão geral do pacote de IoT do Azure incluindo o empacotamento e as soluções pré-configuradas."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/19/2016"
     ms.author="dobett"/>

# Visão geral do Azure IoT Suite

Os serviços de IoT do Azure oferecem uma ampla variedade de recursos. Esses serviços de nível corporativo permitem que você:

- Colete dados de dispositivos
- Analise fluxos de dados em movimento
- Armazene e consulte grandes conjuntos de dados
- Visualize dados históricos e em tempo real
- Realize a integração com sistemas de back-office

Para fornecer esses recursos, os pacotes do Azure IoT Suite reúnem diversos serviços do Azure com extensões personalizadas como *soluções pré-configuradas.* Essas soluções pré-configuradas são implementações básicas dos padrões comuns de solução de IoT que ajudam a reduzir o tempo necessário para entregar suas soluções de IoT. Usando os [Kits de desenvolvimento de software IoT][lnk-sdks], você pode personalizar e estender essas soluções para atender a seus requisitos. Você também pode usar essas soluções como exemplos ou modelos no desenvolvimento de novas soluções de IoT.

O vídeo a seguir oferece uma introdução ao Azure IoT Suite:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Serviços do Azure IoT no Azure IoT Suite

As soluções pré-configuradas normalmente usam os seguintes serviços:

- O principal serviço do Pacote IoT do Azure é o serviço de [Hub IoT do Azure][lnk-iot-hub]. Esse serviço fornece os recursos de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo e age como o gateway para a nuvem e para outros serviços importantes do Pacote IoT. O serviço permite que você receba mensagens de seus dispositivos em escala e envie comandos para seus dispositivos.

- O [Azure Stream Analytics][lnk-asa] fornece análise de dados em movimento. O Pacote IoT utiliza esse serviço para processar telemetria de entrada, executar a agregação e detectar eventos. As soluções pré-configuradas também usam a análise de fluxo para processar mensagens informativas que contêm dados como metadados ou respostas de comando de dispositivos. As soluções usam o Stream Analytics para processar mensagens de seus dispositivos e entregar as mensagens para outros serviços.

- O [Armazenamento do Azure][lnk-azure-storage] e o [Banco de Dados de Documentos do Azure][lnk-document-db] fornecem os recursos de armazenamento de dados. As soluções pré-configuradas usam o armazenamento de blobs para armazenar telemetria e disponibilizá-la para análise. As soluções usam o Banco de Dados de Documentos para armazenar os metadados de dispositivos e habilitar os recursos de gerenciamento de dispositivo das soluções.

- Os [Aplicativos Web do Azure][lnk-web-apps] e o [Microsoft Power BI][lnk-power-bi] fornecem os recursos de visualização de dados. A flexibilidade do Power BI permite que você crie seus próprios painéis interativos rapidamente por meio de dados do Pacote IoT.

Para obter uma visão geral da arquitetura de uma solução de IoT típica, consulte [Microsoft Azure e a IoT (Internet das Coisas)][iot-suite-what-is-azure-iot].

## Soluções pré-configuradas

O Pacote IoT inclui soluções pré-configuradas que o habilitam a começar rapidamente e explorar os cenários comuns da IoT, como o *Monitoramento remoto* e a *Manutenção preditiva*, possibilitados pelo Azure IoT Suite. Você pode implantar as soluções em sua assinatura do Azure e executar um cenário IoT completo e de ponta a ponta.

## Próximas etapas

Agora que tem uma visão geral do que o IoT Suite pode fazer e quais são seus principais componentes, você pode:

- Saber mais sobre as soluções pré-configuradas do IoT Suite, veja [Quais são as soluções pré-configuradas de IoT do Azure?][lnk-what-are-preconfig]

- Começar a usar uma das soluções pré-configuradas, consulte [Introdução às soluções pré-configuradas de IoT][lnk-preconfig-start].

- Saber mais sobre o serviço Hub IoT do Azure, veja a [documentação do Hub IoT][lnk-iot-hub].


[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-preconfig-start]: iot-suite-getstarted-preconfigured-solutions/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/

<!---HONumber=AcomDC_0224_2016-->