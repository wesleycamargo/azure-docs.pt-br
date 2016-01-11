<properties
 pageTitle="Orientação sobre a solução do Hub IoT | Microsoft Azure"
 description="Tópicos de orientação sobre gateways, provisionamento de dispositivo e autenticação para desenvolvimento de soluções IoT usando o Azure Hub IoT."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/10/2015"
 ms.author="dobett"/>

# Projetar sua solução

Este artigo fornece instruções sobre como projetar os seguintes recursos em sua solução de IoT (Internet das Coisas):

- Provisionamento de dispositivos
- Gateways de campo
- Autenticação de dispositivo

## Provisionamento de dispositivos

As soluções de IoT armazenam dados sobre dispositivos individuais, como:

- Chaves de autenticação e identidade do dispositivo
- Tipo e versão de hardware do dispositivo
- Status do dispositivo
- Recursos e versões de software
- Histórico de comando do dispositivo

Os dados de dispositivo que uma determinada solução IoT armazena dependem dos requisitos específicos dessa solução. Porém, no mínimo, uma solução deve armazenar identidades e chaves de autenticação. O Hub IoT do Azure inclui um [registro de identidades][lnk-devguide-identityregistry] que pode armazenar valores para cada dispositivo, como IDs, chaves de autenticação e códigos de status. Uma solução pode usar outros serviços do Azure, como tabelas, blobs ou Banco de Dados de Documentos do Azure para armazenar dados de dispositivo adicionais.

O *provisionamento do dispositivo* é o processo de adição dos dados iniciais do dispositivo para as lojas em sua solução. Para permitir que um dispositivo se conecte ao hub, você deve adicionar uma nova ID e chaves de dispositivo ao [registro de identidades do Hub IoT][lnk-devguide-identityregistry]. Como parte do processo de provisionamento, talvez seja necessário inicializar dados específicos do dispositivo em outros repositórios da solução.

O artigo [Orientações sobre o gerenciamento de dispositivo do Hub IoT][lnk-device-management] descreve algumas estratégias comuns para o provisionamento de dispositivo. As [APIs do registro de identidade do Hub IoT][lnk-devguide-identityregistry] permitem a integração do Hub IoT em seu processo de provisionamento.

## Gateways de campo

Em uma solução IoT, um *gateway de campo* fica entre os dispositivos e o hub IoT. Normalmente, ele se localiza perto dos dispositivos. Os dispositivos se comunicam diretamente com o gateway de campo usando um protocolo com suporte dos dispositivos. O gateway de campo se comunica com o IoT Hub usando um protocolo que tem suporte do Hub IoT. Um gateway de campo pode ser um dispositivo autônomo especializado ou um software executado em um hardware existente.

Um gateway de campo é diferente de um dispositivo de roteamento de tráfego simples (como um dispositivo NAT [conversão de endereços de rede] ou firewall), pois geralmente ele executa uma função ativa no gerenciamento de acesso e no fluxo de informações da solução. Por exemplo, um gateway de campo pode:

- Gerencie dispositivos locais. Por exemplo, um gateway de campo poderia executar o processamento da regra de evento e enviar comandos para dispositivos em resposta a dados de telemetria específicos.
- Filtre ou agregue dados de telemetria antes de encaminhá-los ao Hub IoT. Isso pode reduzir a quantidade de dados enviados ao Hub IoT, e possivelmente reduzir os custos em sua solução.
- Ajude a provisionar os dispositivos.
- Transforme dados de telemetria para facilitar o processamento em sua solução back-end.
- Execute a conversão de protocolo para permitir que os dispositivos se comuniquem com o Hub IoT, mesmo quando não usam os protocolos de transporte que têm suporte do Hub IoT.

> [AZURE.NOTE]Embora você normalmente implante um gateway de campo local para seus dispositivos, em alguns cenários, é possível implantar um [gateway de protocolo][lnk-gateway] na nuvem.

### Tipos de gateways de campo

Um gateway de campo pode ser *transparente* ou *opaco*:

| &nbsp; | Gateway transparente | Gateway opaco|
|--------|-------------|--------|
| Identidades armazenadas no registro de identidades do Hub IoT | Identidades de todos os dispositivos conectados | Apenas a identidade do gateway de campo |
| O Hub IoT pode fornecer [antifalsificação de identidade de dispositivo][lnk-devguide-antispoofing] | Sim | Não |
| [Cotas e limitações][lnk-throttles-quotas] | Aplicar a cada dispositivo | Aplicar ao gateway de campo |

### Outras considerações

Você pode usar os [SDKs de dispositivo do IoT do Azure][lnk-device-sdks] para implementar um gateway de campo. Alguns SDKs de dispositivo oferecem uma funcionalidade específica que ajuda você a implementar um gateway de campo, como a capacidade de multiplexar a comunicação de vários dispositivos para a mesma conexão com o Hub IoT. Como explicado no [Guia do desenvolvedor do Hub IoT - Escolhendo seu protocolo de comunicação][lnk-devguide-protocol], você deve evitar usar HTTP/1 como protocolo de transporte para um gateway de campo.

## Autenticação personalizada de dispositivo

Você pode usar o [registro de identidade de dispositivo][lnk-devguide-identityregistry] do Hub IoT para configurar credenciais de segurança e controle de acesso por dispositivo. No entanto, se uma solução IoT já tiver um investimento considerável em um registro de identidade de dispositivo personalizado e/ou em um esquema de autenticação, você poderá integrar essa infraestrutura existente ao Hub IoT por meio da criação de um *serviço de token*. Dessa forma, você pode usar outros recursos de IoT em sua solução.

Um serviço de token é um serviço de nuvem personalizado. Ele usa uma *política de acesso compartilhado* do Hub IoT com permissões **DeviceConnect** para criar tokens *device-scoped*. Esses tokens permitem que um dispositivo se conecte ao seu Hub IoT.

  ![Etapas do padrão do serviço de token][img-tokenservice]

Estas são as principais etapas do padrão de serviço do token:

1. Crie uma [Política de acesso compartilhado do Hub IoT][lnk-devguide-security] com permissões **DeviceConnect** para seu Hub IoT. Você pode criar essa política no [portal do Azure][lnk-portal] ou de forma programática. O serviço de token usa essa política para assinar os tokens criados.
2. Quando um dispositivo precisar acessar o Hub IoT, ele solicitará um token assinado ao serviço de token. O dispositivo pode autenticar com o seu esquema personalizado de registro/autenticação de identidade de dispositivos para determinar a identidade do dispositivo usada pelo serviço de token para criar o token.
3. O serviço de token retorna um token. O token é criado de acordo com a [seção de segurança do guia do desenvolvedor do Hub IoT][lnk-devguide-security] usando `/devices/{deviceId}` como `resourceURI`, com `deviceId` como o dispositivo que está sendo autenticado. O serviço de token usa a política de acesso compartilhado para construir o token.
4. O dispositivo usa o token diretamente com o Hub IoT.

> [AZURE.NOTE]Você pode usar a classe do .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] ou a classe Java [IotHubServiceSasToken][lnk-java-sas] para criar um token no serviço de token.

O serviço de token pode definir a expiração do token como desejado. Quando o token expira, o Hub IoT rompe a conexão do dispositivo. Em seguida, o dispositivo deve solicitar um novo token ao serviço de token. Se você usar um tempo de expiração curto, aumentará a carga no dispositivo e no serviço de token.

Para que um dispositivo se conecte ao seu hub, você ainda deve adicioná-lo ao registro de identidades do dispositivo do Hub IoT, mesmo se o dispositivo estiver usando um token e não uma chave de dispositivo para se conectar. Portanto, você pode continuar a usar o controle de acesso por dispositivo habilitando ou desabilitando as identidades de dispositivo no [registro de identidade do Hub IoT][lnk-devguide-identityregistry] quando o dispositivo for autenticado com um token. Isso reduz os riscos de usar tokens com tempos de expiração longos.

### Comparação com um gateway personalizado

O padrão de serviço do token é a maneira recomendada de implementar um esquema personalizado de registro/autenticação de identidade com o Hub IoT. É recomendável porque o Hub IoT continua tratando a maior parte do tráfego da solução. No entanto, há casos nos quais o esquema de autenticação personalizado está tão entremeado com o protocolo que é necessário ter um serviço que processe todo o tráfego (*gateway personalizado*). Um exemplo disso é o [TLS (Transport Layer Security) e as PSKs (chaves pré-compartilhadas)][lnk-tls-psk]. Para saber mais, confira o tópico [Gateway de protocolo][lnk-gateway].

## Próximas etapas

Para saber mais sobre o Hub IoT do Azure, siga estes links:

- [Introdução ao Hub IoT (Tutorial)][lnk-get-started]
- [O que é o Hub IoT do Azure?][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md

[lnk-device-sdks]: iot-hub-sdks-summary.md
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-gateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md
[lnk-portal]: https://portal.azure.com
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html

<!---HONumber=AcomDC_1223_2015-->