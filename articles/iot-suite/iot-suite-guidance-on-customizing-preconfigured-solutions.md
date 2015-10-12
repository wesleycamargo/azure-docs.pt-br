<properties
	pageTitle="Guia do Microsoft Azure IoT Suite sobre personalizar as soluções pré-configuradas | Microsoft Azure"
	description="Isso fornecerá orientação sobre como personalizar soluções pré-configuradas do Azure IoT Suite."
	services=""
	documentationCenter=".net"
	authors="stevehob"
	manager="kevinmil"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/22/2015"
     ms.author="stevehob"/>

# Personalizar soluções pré-configuradas
As soluções pré-configuradas fornecidas com o Azure IoT permitem que os clientes vejam os serviços no pacote trabalhando juntos para fornecer uma solução de ponta a ponta. Do ponto de partida, há uma variedade de lugares onde a personalização e extensão podem ocorrer para personalizar a solução para cenários específicos. As seções a seguir descrevem esses pontos comuns de personalização.

## Localizando o código-fonte
O código-fonte para sua solução pré-configurada está disponível no Github nos seguintes repositórios:

- Monitoramento remoto: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)

Essa fonte é fornecida para demonstrar um padrão para implementar a funcionalidade principal de monitoramento remoto usando o Azure IoT Suite.

## Alterando as regras predefinidas
A solução de monitoramento remota inclui dois trabalhos de [Stream Analytics do Azure](http://azure.microsoft.com/documentation/services/stream-analytics) para implementar a lógica de telemetria e alarme exibida no painel.

O primeiro trabalho seleciona todos os dados do fluxo de entrada de telemetria e cria duas saídas diferentes. O trabalho será denominado [nome da solução]-telemetria.

- A primeira saída pega todos os dados por `SELECT *` e os armazenamentos as saídas para o blob. O armazenamento de blob está onde o painel lê os valores brutos para criar seus gráficos.
- A segunda saída executa um cálculo `AVG()`, `MIN()`, e `MAX()` em uma janela deslizante 5 minutos. Esses dados são mostrados no visores no painel.

Usando a interface do usuário de Stream Analytics, será possível editar esses trabalhos diretamente para alterar a lógica ou adicionar lógica específica para seu cenário.

O segundo trabalho opera nos valores de Dispositivo para limite criado na página de Regras da solução. Esse trabalho consome Dados de Referência, o valor de limite definido para cada dispositivo. Compara o valor de limite para ver se é maior do que (`>`) o valor real. Esse trabalho pode ser modificado, por exemplo, para alterar o operador de comparação.

***Observe que o painel de monitoramento remoto depende de dados específicos, para alterar os trabalhos pode fazer com que o painel falhe.***

## Adicionando suas próprias regras
Além disso, para alterar os trabalhos de Stream Analytics do Azure, você pode usar o Portal do Azure para adicionar novos trabalhos ou adicionar novas consultas para trabalhos existentes.

## Personalizando dispositivos
Uma das atividades mais comuns de extensão é o trabalho com dispositivos específicos ao seu cenário. Há vários métodos para trabalhar com dispositivos. Isso inclui alterar um dispositivo simulado de acordo com seu cenário, ou usar o SDK do Dispositivo IoT do Azure para conectar o seu dispositivo físico à solução.

Consulte o documento a seguir para ter um guia passo a passo para adicionar dispositivos à solução pré-configurada de monitoramento remoto [Dispositivos de conexão IoT Suite](iot-suite-connecting-devices.md)

### Criando seu próprio dispositivo simulado
Incluído no código-fonte da solução de monitoramento remota (referenciada acima), é um simulador .Net. Esse simulador é provisionado como parte da solução e pode ser alterado para enviar metadados diferentes, telemetria ou responder a comandos diferentes.

Além disso, fornecemos um [Exemplo SDK C](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) que foi projetado para funcionar com a solução pré-configurada de monitoramento remoto.

### Compilando e usando seu próprio dispositivo (físico)
Os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks) fornecem bibliotecas para conectar a vários tipos de dispositivo (linguagens e sistemas operacionais) em soluções de IoT.


Para obter mais informações sobre dispositivos IoT, consulte o [Site de desenvolvedores do Azure IoT](http://azure.microsoft.com/develop/iot) para encontrar links e documentação.

<!---HONumber=Oct15_HO1-->