<properties
	pageTitle="Visão geral do Microsoft Azure IoT Suite | Microsoft Azure"
	description="Isso fornece uma visão geral do pacote de IoT do Azure incluindo o empacotamento e as soluções pré-configuradas."
	services=""
	documentationCenter=""
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/06/2015"
     ms.author="araguila"/>

# Visão geral do Azure IoT Suite

A Microsoft oferece uma ampla variedade de recursos com os serviços do Azure IoT. Esses serviços oferecem recursos de nível enterprise para coletar dados dos dispositivos, analisar transmissões de dados em movimento, armazenamento e consulta de grandes conjuntos de dados, visualização tanto de dados em tempo real como históricos, e integram com importantes sistemas de back office. O Azure IoT Suite compacta um conjunto de serviços normalmente utilizados e recursos estendidos para reduzir o tempo de implantação para os clientes. Essas extensões incluem soluções pré-configuradas que oferecem a implementação de base dos padrões mais comuns de solução. Combinado com os kits de desenvolvimento de software (software development kit, SDKs) IoT, os clientes podem personalizar facilmente as soluções pré-configuradas ou aproveitá-las como exemplos para o desenvolvimento de novas soluções.

## Serviços do Azure IoT no Azure IoT Suite

O principal serviço do pacote do IoT do Azure é o serviço de Hub IoT. Este serviço fornece um sistema de mensagens de dispositivo para nuvem e de nuvem para serviço. Ele atua como gateway para a nuvem e os outros principais serviços do Pacote IoT.

A análise de dados em movimento é fornecido pelo Azure Stream Analytics. O Pacote IoT utiliza esse serviço para processar mensagens de entrada da telemetria, executar agregação e detectar eventos. Ele também é usado para processar mensagens informativas que podem ser usadas para metadados do dispositivo ou resposta de comandos.

Recursos de armazenamento de dados são habilitados por meio de uma combinação do Armazenamento do Azure e do Banco de Dados de Documentos do Azure. O Armazenamento do Azure permite o armazenamento de blob de telemetria para preservação e análise futura. O Banco de Dados de Documentos é utilizado por sua capacidade de armazenamento indexado em dados semiestruturados para gerenciar metadados de dispositivo. Isso permite o gerenciamento de dispositivos heterogêneos, permitindo que diferentes dispositivos tenham um conteúdo diferente.

A visualização dos dados é fornecida por uma combinação de sites do Azure e o Microsoft Power BI. A flexibilidade do Power BI permite que os clientes criem seus próprios painéis interativos rapidamente por meio de dados do Pacote IoT.

Mais detalhes sobre a arquitetura e como esses serviços são usados podem ser encontrados no artigo [Microsoft Azure e a Internet das Coisas (IoT)](iot-suite-what-is-azure-iot.md).

## Soluções pré-configuradas

Soluções pré-configuradas são incluídas no Pacote IoT do Azure para permitir que os clientes tenham uma introdução rápida e explorem cenários possibilitados pelo Pacote IoT do Azure.

A primeira solução pré-configurada disponível é o [Monitoramento Remoto](iot-suite-what-are-preconfigured-solutions.md).

<!---HONumber=Oct15_HO3-->