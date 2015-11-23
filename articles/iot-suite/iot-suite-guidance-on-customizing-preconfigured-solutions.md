<properties
	pageTitle="Personalizando soluções pré-configuradas | Microsoft Azure"
	description="Fornece orientação sobre como personalizar as soluções pré-configuradas do Pacote IoT do Azure."
	services=""
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="stevehob"/>

# Personalizar soluções pré-configuradas

As soluções pré-configuradas fornecidas com o Pacote IoT do Azure demonstram os serviços no pacote trabalhando juntos para fornecer uma solução de ponta a ponta. Do ponto de partida, há uma variedade de lugares em que você pode estender e personalizar a solução para cenários específicos. As seções a seguir descrevem esses pontos comuns de personalização.

## Localizando o código-fonte

O código-fonte para sua solução pré-configurada está disponível no GitHub nos seguintes repositórios:

- Monitoramento remoto: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)

Essa fonte é fornecida para demonstrar um padrão para implementar a funcionalidade principal de monitoramento remoto usando o Azure IoT Suite.

## Alterando as regras predefinidas

A solução de monitoramento remota inclui dois trabalhos de [Stream Analytics do Azure](http://azure.microsoft.com/services/stream-analytics) para implementar a lógica de telemetria e alarme exibida no painel.

O primeiro trabalho seleciona todos os dados do fluxo de entrada de telemetria e cria duas saídas diferentes. O trabalho será denominado **[solution name]-Telemetry**.

- A primeira saída pega todos os dados usando `SELECT *` e os gera para o armazenamento de blob. O armazenamento de blob está onde o painel lê os valores brutos para criar seus gráficos.
- A segunda saída executa um cálculo `AVG()`, `MIN()` e `MAX()` em uma janela deslizante de 5 minutos. Esses dados são mostrados no visores no painel.

Usando a interface do usuário de Stream Analytics, será possível editar esses trabalhos diretamente para alterar a lógica ou adicionar lógica específica para seu cenário.

O segundo trabalho opera nos valores de Dispositivo para limite criados na página de **Regras** da solução. Esse trabalho consome Dados de Referência, o valor de limite definido para cada dispositivo. Compara o valor de limite para ver se é maior do que (`>`) o valor real. Esse trabalho pode ser modificado, por exemplo, para alterar o operador de comparação.

> [AZURE.NOTE]O painel de monitoramento remoto depende de dados específicos, por isso, alterar os trabalhos pode fazer com que o painel falhe.

## Adicionando suas próprias regras

Além de alterar os trabalhos do Stream Analytics do Azure, você pode usar o portal de visualização do Azure para adicionar novos trabalhos ou adicionar novas consultas a trabalhos existentes.

## Personalizando dispositivos

Uma das atividades mais comuns de extensão é o trabalho com dispositivos específicos ao seu cenário. Há vários métodos para trabalhar com dispositivos. Esses métodos incluem alterar um dispositivo simulado de acordo com seu cenário, ou usar o [SDK do Dispositivo IoT][] para conectar o seu dispositivo físico à solução.

Para ter um guia passo a passo para adicionar dispositivos à solução pré-configurada de monitoramento remoto, consulte [Dispositivos de conexão do Pacote IoT](iot-suite-connecting-devices.md).

### Criando seu próprio dispositivo simulado

Um simulador .NET é incluído no código-fonte da solução de monitoramento remota (referenciada acima). Esse simulador é provisionado como parte da solução e pode ser alterado para enviar metadados diferentes, telemetria ou responder a comandos diferentes.

Além disso, o Azure IoT fornece um [Exemplo SDK C](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) que foi projetado para funcionar com a solução pré-configurada de monitoramento remoto.

### Compilando e usando seu próprio dispositivo (físico)

Os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks) fornecem bibliotecas para conectar a vários tipos de dispositivo (linguagens e sistemas operacionais) em soluções de IoT.

## Próximas etapas

Para obter mais informações sobre dispositivos IoT, consulte o [Site de desenvolvedores do Azure IoT](http://azure.microsoft.com/develop/iot) para encontrar links e documentação.

[SDK do Dispositivo IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=Nov15_HO3-->