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
 ms.date="04/29/2016"
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

As [APIs do registro de identidade do Hub IoT][lnk-devguide-identityregistry] permitem a integração do Hub IoT em seu processo de provisionamento.

## Gateways de campo

Em uma solução IoT, um *gateway de campo* fica entre os dispositivos e o hub IoT. Normalmente, ele se localiza perto dos dispositivos. Os dispositivos se comunicam diretamente com o gateway de campo usando um protocolo com suporte dos dispositivos. O gateway de campo se comunica com o IoT Hub usando um protocolo que tem suporte do Hub IoT. Um gateway de campo pode ser um hardware altamente especializado ou um computador com baixo consumo de energia que executa um software que realiza o cenário de ponta a ponta ao qual o gateway se destina.

Um gateway de campo é diferente de um dispositivo de roteamento de tráfego simples (como um dispositivo NAT [conversão de endereços de rede] ou firewall), pois geralmente ele executa uma função ativa no gerenciamento de acesso e no fluxo de informações da solução. Por exemplo, um gateway de campo pode:

- **Adicionar suporte para dispositivos novos e herdados**: há milhões de sensores e atuadores novos e herdados que não podem enviar dados diretamente para a nuvem. Esses dispositivos usam um protocolo que não é adequado para a Internet, não implementam a criptografia ou não podem armazenar certificados de identidade. O uso de um gateway reduz a carga e o custo de conexão desses dispositivos.
- **Executar análise de borda**: há várias operações que podem ser feitas localmente para reduzir a quantidade de dados trocados com a nuvem. Entre os exemplos estão a filtragem de dados, o envio em lote e a compactação. Talvez também seja conveniente executar alguns cálculos, tais como limpeza de dados ou pontuação de um modelo de aprendizado de máquina com dados em tempo real localmente.
- **Minimizar a latência**: até os milissegundos são importantes quando você está tentando prevenir desligamentos na linha de fabricação ou restaurar um serviço elétrico. A análise dos dados próximo ao dispositivo que coletou esses dados pode fazer a diferença entre prevenir um desastre e uma falha em cascata do sistema.
- **Conservar largura de banda de rede**: uma plataforma de petróleo offshore típica gera entre 1 e 2 TB de dados por dia. Um Boeing 787 cria metade de um terabyte de dados por voo. Não é prático transportar grandes quantidades de dados de milhares ou centenas de milhares de dispositivos de borda para a nuvem. E isso nem é necessário, pois muitas análises críticas não exigem um processamento e armazenamento em escala de nuvem.
- **Operar de forma confiável**: cada vez mais, os dados do IoT são usados para decisões que afetam a segurança dos cidadãos e a infraestrutura crítica. A integridade e a disponibilidade da infraestrutura e dos dados não poderão ser comprometidas por conexões intermitentes de nuvem. O uso de funcionalidades, tais como armazenar e encaminhar para coletar e agir sobre os dados localmente e, em seguida, enviá-los para a nuvem quando apropriado, o ajudam criar soluções confiáveis.
- **Abordar as preocupações de segurança e privacidade**: os dispositivos IoT e os dados gerados por eles precisam ser protegidos. Os gateways podem fornecer serviços como isolar dispositivos da Internet aberta, fornecendo serviços de identidade e criptografia para dispositivos que não podem fornecer esses serviços por conta própria, protegendo os dados armazenados em buffer localmente ou armazenados e removendo informações de identificação pessoal antes de enviá-las pela Internet.

### Outras considerações

É possível usar os [SDKs do Gateway IoT do Azure][lnk-gateway-sdk] para implementar um gateway de campo. Estes SDKs oferecem uma funcionalidade específica, tal como a capacidade de multiplexar a comunicação de vários dispositivos na mesma conexão para o Hub IoT.

## Autenticação personalizada de dispositivo

Você pode usar o [registro de identidade de dispositivo][lnk-devguide-identityregistry] do Hub IoT para configurar credenciais de segurança e controle de acesso por dispositivo. No entanto, se uma solução IoT já tiver um investimento considerável em um registro de identidade de dispositivo personalizado e/ou em um esquema de autenticação, você poderá integrar essa infraestrutura existente ao Hub IoT por meio da criação de um *serviço de token*. Dessa forma, você pode usar outros recursos de IoT em sua solução.

Um serviço de token é um serviço de nuvem personalizado. Ele usa uma *política de acesso compartilhado* do Hub IoT com permissões **DeviceConnect** para criar tokens *device-scoped*. Esses tokens permitem que um dispositivo se conecte ao seu Hub IoT.

  ![Etapas do padrão do serviço de token][img-tokenservice]

Estas são as principais etapas do padrão de serviço do token:

1. Crie uma [Política de acesso compartilhado do Hub IoT][lnk-devguide-security] com permissões **DeviceConnect** para seu Hub IoT. Você pode criar essa política no [portal do Azure][lnk-portal] ou de forma programática. O serviço de token usa essa política para assinar os tokens criados.
2. Quando um dispositivo precisar acessar o Hub IoT, ele solicitará um token assinado ao serviço de token. O dispositivo pode autenticar com o seu esquema personalizado de registro/autenticação de identidade de dispositivos para determinar a identidade do dispositivo usada pelo serviço de token para criar o token.
3. O serviço de token retorna um token. O token é criado de acordo com a [seção de segurança do guia do desenvolvedor do Hub IoT][lnk-devguide-security] usando `/devices/{deviceId}` como `resourceURI`, com `deviceId` como o dispositivo que está sendo autenticado. O serviço de token usa a política de acesso compartilhado para construir o token.
4. O dispositivo usa o token diretamente com o Hub IoT.

> [AZURE.NOTE] Você pode usar a classe do .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] ou a classe Java [IotHubServiceSasToken][lnk-java-sas] para criar um token no serviço de token.

O serviço de token pode definir a expiração do token como desejado. Quando o token expira, o Hub IoT rompe a conexão do dispositivo. Em seguida, o dispositivo deve solicitar um novo token ao serviço de token. Se você usar um tempo de expiração curto, aumentará a carga no dispositivo e no serviço de token.

Para que um dispositivo se conecte ao seu hub, você ainda deve adicioná-lo ao registro de identidades do dispositivo do Hub IoT, mesmo se o dispositivo estiver usando um token e não uma chave de dispositivo para se conectar. Portanto, você pode continuar a usar o controle de acesso por dispositivo habilitando ou desabilitando as identidades de dispositivo no [registro de identidade do Hub IoT][lnk-devguide-identityregistry] quando o dispositivo for autenticado com um token. Isso reduz os riscos de usar tokens com tempos de expiração longos.

### Comparação com um gateway personalizado

O padrão de serviço do token é a maneira recomendada de implementar um esquema personalizado de registro/autenticação de identidade com o Hub IoT. É recomendável porque o Hub IoT continua tratando a maior parte do tráfego da solução. No entanto, há casos nos quais o esquema de autenticação personalizado está tão entremeado com o protocolo que é necessário ter um serviço que processe todo o tráfego (*gateway personalizado*). Um exemplo disso é o [TLS (Transport Layer Security) e as PSKs (chaves pré-compartilhadas)][lnk-tls-psk]. Para saber mais, confira o tópico [Gateway de protocolo][lnk-gateway].

## Pulsação do dispositivo <a id="heartbeat"></a>

O [registro de identidade do Hub IoT][lnk-devguide-identityregistry] contém um campo chamado **connectionState**. É necessário usar o campo **connectionState** somente durante o desenvolvimento e a depuração. As soluções de IoT não devem consultar o campo no tempo de execução (por exemplo, para verificar se um dispositivo está conectado, a fim de decidir se uma mensagem da nuvem para o dispositivo ou um SMS deve ser enviada). Se a solução de IoT precisar saber se um dispositivo está conectado (em tempo de execução ou com mais precisão do que a fornecida pela propriedade **connectionState**), a solução deverá implementar o *padrão de pulsação*.

No padrão de pulsação, o dispositivo envia mensagens do dispositivo para a nuvem pelo menos uma vez a cada período de tempo fixo (por exemplo, pelo menos uma vez a cada hora). Isso significa que mesmo quando um dispositivo não tiver dados para enviar, ele enviará uma mensagem vazia do dispositivo para a nuvem (geralmente com uma propriedade que a identifique como uma pulsação). No lado do serviço, a solução mantém um mapa com a última pulsação recebida para cada dispositivo e supõe que haja um problema com um dispositivo se ela não receber uma mensagem de pulsação dentro do tempo esperado.

Uma implementação mais complexa pode incluir as informações do [monitoramento de operações][lnk-devguide-opmon] para identificar dispositivos que estão tentando se conectar ou se comunicar, mas falham. Ao implementar o padrão de pulsação, verifique as [Cotas e restrições do Hub IoT][].

> [AZURE.NOTE] Se uma solução IoT precisar do estado de conexão do dispositivo apenas para determinar se deve enviar mensagens da nuvem para o dispositivo, e as mensagens não forem transmitidas para conjuntos grandes de dispositivos, um padrão muito mais simples a ser considerado será usar um tempo de validade mais curto. Isso proporciona o mesmo resultado que a manutenção de um registro de estado de conexão do dispositivo usando o padrão de pulsação, embora seja significativamente mais eficiente. Também é possível, por meio da solicitação de confirmações de mensagens, receber uma notificação pelo Hub IoT de quais dispositivos são capazes de receber mensagens e quais não estão online ou apresentam falha. Consulte o [Guia do Desenvolvedor do Hub IoT][lnk-devguide-messaging] para obter mais informações sobre mensagens C2D.

## Próximas etapas

Para saber mais sobre como planejar sua implantação do Hub IoT, consulte:

- [Suporte ao MQTT][lnk-mqtt]
- [Dispositivos com suporte][lnk-devices]
- [Suporte a protocolos adicionais][lnk-protocols]
- [Comparar com Hubs de Eventos][lnk-compare]
- [Escala, alta disponibilidade e recuperação de desastre][lnk-scaling]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Guia do desenvolvedor][lnk-devguide]
- [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
- [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
- [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal-manage]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

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
[lnk-devguide-messaging]: iot-hub-devguide.md#messaging
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[Cotas e restrições do Hub IoT]: iot-hub-devguide.md#throttling
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[lnk-mqtt]: iot-hub-mqtt-support.md
[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal-manage]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->