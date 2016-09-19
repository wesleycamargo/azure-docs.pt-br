<properties
 pageTitle="Tópicos do guia do desenvolvedor para Hub IoT | Microsoft Azure"
 description="Guia do desenvolvedor do Hub IoT do Azure que inclui pontos de extremidade do Hub IoT, segurança, Registro de identidade do dispositivo e mensagens"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/02/2016" 
 ms.author="dobett"/>

# Guia do desenvolvedor do Hub IoT do Azure

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo.

O Hub IoT do Azure ajuda a fornecer:

* Proteja as comunicações usando as credenciais de segurança e o controle de acesso por dispositivo.
* Envio, de forma confiável, de mensagens em larga escala do dispositivo para a nuvem e da nuvem para o dispositivo.
* Facilitar a conectividade do dispositivo com as bibliotecas de dispositivo a fim de obter as plataformas e os idiomas mais populares.

Este artigo aborda os seguintes tópicos:

- [Pontos de extremidade](#endpoints). Esta seção descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
- [Registro de identidade do dispositivo](#device-identity-registry). Esta seção descreve as informações armazenadas no registro de identidade do dispositivo de cada Hub IoT, e como elas podem ser acessadas e modificadas.
- [Segurança](#security). Esta seção descreve o modelo de segurança usado para conceder acesso à funcionalidade do Hub IoT para componentes de dispositivos e da nuvem.
- [Mensagens](#messaging). Esta seção descreve os recursos de sistema de mensagens (do dispositivo para a nuvem e da nuvem para o dispositivo) expostos pelo Hub IoT.
- [Cotas e limitação](#throttling). Esta seção resume as cotas que se aplicam ao uso do Hub IoT.

## Pontos de extremidade <a id="endpoints"></a>

O Hub IoT do Azure é um serviço multilocatário que expõe suas funcionalidades a vários atores. O diagrama a seguir mostra os diversos pontos de extremidade que o Hub IoT expõe.

![Pontos de extremidade do Hub IoT][img-endpoints]

A seguir, uma descrição dos pontos de extremidade:

* **Provedor de recursos**. O provedor de recursos do Hub IoT expõe uma interface do [Azure Resource Manager][lnk-arm] que permite aos proprietários de assinaturas do Azure criar e excluir Hubs IoT e atualizar propriedades do Hub IoT. As propriedades do Hub IoT regem as políticas de segurança no nível do hub, ao contrário do controle de acesso no nível do dispositivo (consulte a seção [Controle de acesso](#accesscontrol) posteriormente neste artigo) e das opções funcionais para o sistema de mensagens da nuvem para o dispositivo e do dispositivo para a nuvem. O provedor de recursos também permite [exportar identidades do dispositivo](#importexport).
* **Gerenciamento de identidades dos dispositivos**. Cada Hub IoT expõe um conjunto de pontos de extremidade HTTP REST para o gerenciamento de identidades do dispositivo (criar, recuperar, atualizar e excluir). As identidades do dispositivo são usadas para controle de acesso e autenticação do dispositivo. Para saber mais, consulte [Registro de identidade do dispositivo](#device-identity-registry).
* **Pontos de extremidade do dispositivo**. Para cada dispositivo provisionado no registro de identidade do dispositivo, o Hub IoT expõe um conjunto de pontos de extremidade que pode ser usado por um dispositivo para enviar e receber mensagens:
    - *Enviar mensagens do dispositivo para a nuvem*. Use este ponto de extremidade para enviar mensagens do dispositivo para a nuvem. Para saber mais, consulte [Sistema de mensagens do dispositivo para a nuvem](#d2c).
    - *Receber mensagens da nuvem para o dispositivo*. Um dispositivo usa este ponto de extremidade para receber mensagens direcionadas da nuvem para o dispositivo. Para saber mais, consulte [Sistema de mensagens da nuvem para o dispositivo](#c2d).
    - *Inicie os uploads de arquivos*. Um dispositivo usa esse ponto de extremidade para receber um URI de SAS do Armazenamento do Azure do Hub IoT para carregar um arquivo. Para saber mais, veja [Carregamentos de arquivo](#fileupload).

    Estes pontos de extremidade são expostos usando os protocolos [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 e [AMQP 1.0][lnk-amqp]. Observe que o AMQP também está disponível sobre [WebSockets][lnk-websockets] na porta 443.
* **Pontos de extremidade do serviço**. Cada Hub IoT expõe um conjunto de pontos de extremidade que o seu back-end de aplicativo pode usar para se comunicar com seus dispositivos. Atualmente, esses pontos de extremidade são expostos apenas com o protocolo [AMQP][lnk-amqp].
    - *Receber mensagens do dispositivo para a nuvem*. Esse ponto de extremidade é compatível com os [Hubs de Eventos do Azure][lnk-event-hubs]. Um serviço de back-end pode usá-lo para ler todas as mensagens do dispositivo para a nuvem enviadas por seus dispositivos. Para saber mais, consulte [Sistema de mensagens do dispositivo para a nuvem](#d2c).
    - *Enviar mensagens da nuvem para o dispositivo e receber confirmações de entrega*. Esses pontos de extremidade permitem que o seu back-end de aplicativo envie mensagens confiáveis da nuvem para o dispositivo e receba confirmações de entrega ou de vencimento correspondentes. Para saber mais, consulte [Sistema de mensagens da nuvem para o dispositivo](#c2d).
    - *Receba notificações de arquivo*. Esse ponto de extremidade de mensagens permite que você receba notificações quando os dispositivos carregarem com êxito um arquivo.

O artigo [APIs e SDKs do Hub IoT][lnk-sdks] descreve as várias maneiras de acessar esses pontos de extremidade.

Por fim, é importante observar que todos os pontos de extremidade do Hub IoT usam o protocolo [TLS][lnk-tls] e que nenhum ponto de extremidade é exposto em canais sem criptografia/desprotegidos.

### Como ler de pontos de extremidade compatíveis com os Hubs de Eventos. <a id="eventhubcompatible"></a>

Ao usar o [SDK do Barramento de Serviço do Azure para .NET][lnk-servicebus-sdk] ou [Hubs de eventos – Host Processador de Evento][lnk-eventprocessorhost], você pode usar qualquer cadeia de conexão do Hub IoT com as permissões corretas. Em seguida, use **mensagens/eventos** como o nome do Hub de Eventos.

Ao usar os SDKs (ou integrações de produtos) que não reconhecem o Hub IoT, será necessário recuperar um ponto de extremidade compatível com os Hubs de Eventos e o nome do Hub de Evento das configurações do Hub IoT no [Portal do Azure][lnk-management-portal]\:

1. Na folha de hub IoT, clique em **Mensagens**.
2. Na seção **Configurações de dispositivo para a nuvem**, você encontra os seguintes valores: **Ponto de extremidade compatível com o Hub de Eventos**, **Nome compatível com o Hub de Eventos** e **Partições**.

    ![Configurações de dispositivo para a nuvem][img-eventhubcompatible]

> [AZURE.NOTE] Se o SDK exigir um valor de **Nome do host** ou **Namespace**, remova o esquema do **Ponto de extremidade compatível com o Hub de Eventos**. Por exemplo, se o ponto de extremidade compatível com Hub de Eventos for **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, o **Nome do host** será **iothub-ns-myiothub-1234.servicebus.windows.net** e o **Namespace** será **iothub-ns-myiothub-1234**.

Dessa forma, você poderá usar qualquer política de segurança de acesso compartilhado com permissões **ServiceConnect** para se conectar ao Hub de Eventos especificado.

Caso você precise criar uma cadeia de conexão do Hub de Eventos usando as informações anteriores, use o seguinte padrão:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

A seguir, uma lista dos SDKs e das integrações que você pode usar com os pontos de extremidade compatíveis com o Hub de Eventos expostos pelo Hub IoT:

* [Cliente Java dos Hubs de Eventos](https://github.com/hdinsight/eventhubs-client)
* [Spout do Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [fonte do spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## Registro de identidade do dispositivo

Cada Hub IoT tem um registro de identidade do dispositivo. Você pode usar esse registro para criar recursos por dispositivo no serviço, como uma fila que contém mensagens de nuvem para o dispositivo em andamento. Você também pode usar o registro para permitir o acesso aos pontos de extremidade voltados para o dispositivo, conforme explicado na seção [Controle de acesso](#accesscontrol).

Em um alto nível, o registro de identidade do dispositivo é uma coleção compatível com REST de recursos de identidade do dispositivo. As seções a seguir detalham as propriedades do recurso de identidade do dispositivo e as operações que o registro habilita em identidades.

> [AZURE.NOTE] Para obter mais detalhes sobre o protocolo HTTP e os SDKs que podem ser usados para interagir com o registro de identidade do dispositivo, veja [APIs e SDKs do Hub IoT][lnk-sdks].

### Propriedades de identidade de dispositivo <a id="deviceproperties"></a>

As identidades do dispositivo são representadas como documentos JSON com as seguintes propriedades.

| Propriedade | Opções | Descrição |
| -------- | ------- | ----------- |
| deviceId | obrigatória, somente leitura em atualizações | Uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | obrigatória, somente leitura | Uma cadeia de caracteres que diferencia maiúsculas de minúsculas com até 128 caracteres gerada pelo hub. Isso é usado para distinguir os dispositivos com o mesmo **deviceId** quando são excluídos e recriados. |
| etag | obrigatória, somente leitura | Uma cadeia de caracteres que representa uma etag fraca para a identidade do dispositivo, de acordo com [RFC7232][lnk-rfc7232].|
| auth | opcional | Um objeto composto que contém as informações de autenticação e os materiais de segurança. |
| auth.symkey | opcional | Um objeto composto que contém as chaves primária e secundária, armazenadas no formato base64. |
| status | obrigatório | Um indicador de acesso. Pode estar **Habilitado** ou **Desabilitado**. Se estiver **Habilitado**, o dispositivo terá permissão para se conectar. Se estiver **Desabilitado**, este dispositivo não poderá acessar qualquer ponto de extremidade voltado para o dispositivo. |
| statusReason | opcional | Uma cadeia de caracteres com 128 caracteres que armazena o motivo do status de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime | somente leitura | Um indicador temporal, mostrando a data e hora da última atualização de status. |
| connectionState | somente leitura | Um campo indicando o status da conexão: **Conectado** ou **Desconectado**. Esse campo representa a exibição do Hub IoT do status de conexão do dispositivo. **Importante**: esse campo deve ser usado apenas para fins de desenvolvimento/depuração. O estado da conexão é atualizado somente nos dispositivos que usam MQTT ou AMQP. Além disso, ele se baseia nos pings do nível de protocolo (pings MQTT ou AMQP) e pode ter um atraso máximo de apenas cinco minutos. Por esses motivos pode haver falsos positivos, como dispositivos relatados como conectados, mas que na verdade estão desconectados. |
| connectionStateUpdatedTime | somente leitura | Um indicador temporal, mostrando a data e a hora da última atualização do estado da conexão. |
| lastActivityTime | somente leitura | Um indicador temporal, mostrando a data e hora da última vez em que o dispositivo se conectou, recebeu ou enviou uma mensagem. |

> [AZURE.NOTE] O estado da conexão pode representar apenas a visão do Hub IoT do status da conexão. As atualizações para esse estado podem ser atrasadas, dependendo das configurações e das condições da rede.

### Operações de identidade do dispositivo

O registro de identidade do dispositivo do Hub IoT expõe as seguintes operações:

* Criar identidade do dispositivo
* Atualizar identidade do dispositivo
* Recuperar a identidade do dispositivo por ID
* Excluir identidade do dispositivo
* Listar até 1000 identidades
* Exportar todas as identidades para o armazenamento de blobs
* Importar todas as identidades do armazenamento de blobs

Todas essas operações permitem o uso da simultaneidade otimista como especificado na [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] A única maneira de recuperar todas as identidades em um registro de identidade do hub é usar a funcionalidade [Exportar](#importexport).

Um registro de identidade do dispositivo do Hub IoT:

- Não contém metadados de aplicativo.
- Pode ser acessado como um dicionário usando o **deviceId** como a chave.
- Não permite consultas expressivas.

Normalmente, uma solução de IoT tem um armazenamento específico da solução separado que contém metadados específicos do aplicativo. Por exemplo, o armazenamento específico da solução em uma solução de construção inteligente seria registrar a sala na qual um sensor de temperatura está implantado.

> [AZURE.IMPORTANT] Você deve usar o registro de identidade do dispositivo apenas para operações de provisionamento e gerenciamento de dispositivos. As operações de alta produtividade no tempo de execução não devem depender da execução de operações no registro de identidade do dispositivo. Por exemplo, verificar o estado da conexão de um dispositivo antes de enviar um comando não é um padrão permitido. Lembre-se de verificar as [taxas de limitação](#throttling) do registro de identidade do dispositivo e o padrão de [pulsação do dispositivo][lnk-guidance-heartbeat].

### Desabilitando dispositivos

Você pode desabilitar dispositivos atualizando a propriedade **status** de uma identidade no registro. Normalmente, você deve usar essa propriedade em dois cenários:

- Durante um processo de orquestração de provisionamento. Para saber mais, confira [Projetar sua solução – provisionamento de dispositivos][lnk-guidance-provisioning].
- Se, por algum motivo, você considerar que um dispositivo está comprometido ou que se tornou não autorizado.

### Importar e exportar identidades de dispositivo <a id="importexport"></a>

É possível exportar identidades de dispositivo em massa do registro de identidade de um Hub IoT usando operações assíncronas no [ponto de extremidade do provedor de recursos do Hub IoT](#endpoints). As exportações são trabalhos de execução longa que usam um contêiner de blobs fornecido pelo cliente para salvar dados de identidade do dispositivo lidos no registro de identidade.

É possível importar identidades de dispositivo em massa para um registro de identidade de um Hub IoT usando operações assíncronas no [ponto de extremidade do provedor de recursos do Hub IoT](#endpoints). As exportações são trabalhos de execução longa que usam dados em um contêiner de blobs fornecido pelo cliente para gravar dados de identidade do dispositivo no registro de identidade do dispositivo.

- Para obter informações detalhadas sobre as APIs de importação e exportação, consulte [Hub IoT do Azure - APIs do provedor de recursos][lnk-resource-provider-apis].
- Para saber mais sobre como executar trabalhos de importação e exportação, veja [Gerenciamento em massa de identidades de dispositivo do Hub IoT][lnk-bulk-identity].

## Segurança <a id="security"></a>

Esta seção descreve as opções para proteger o Hub IoT do Azure.

### Controle de acesso <a id="accesscontrol"></a>

O Hub IoT usa o conjunto de *permissões* a seguir para conceder acesso aos pontos de extremidade de cada Hub IoT. As permissões limitam o acesso a um hub IoT com base na funcionalidade.

* **RegistryRead**. Concede acesso de leitura ao registro de identidade do dispositivo. Para saber mais, consulte [Registro de identidade do dispositivo](#device-identity-registry).
* **RegistryReadWrite**. Concede acesso de leitura e gravação ao registro de identidade do dispositivo. Para saber mais, consulte [Registro de identidade do dispositivo](#device-identity-registry).
* **ServiceConnect**. Concede acesso aos pontos de extremidade de comunicação e de monitoramento voltados para o serviço de nuvem. Por exemplo, ela concede permissão para que os serviços de nuvem de back-end recebam mensagens do dispositivo para a nuvem, enviem mensagens da nuvem para o dispositivo e obtenham as confirmações de entrega correspondentes.
* **DeviceConnect**. Concede acesso aos pontos de extremidade de comunicação voltados para o dispositivo. Por exemplo, ela concede permissão de envio de mensagens do dispositivo para a nuvem e de recebimento de mensagens da nuvem para o dispositivo. Essa permissão é usada por dispositivos.

Você pode conceder permissões das seguintes maneiras:

* **Políticas de acesso compartilhado no nível do Hub**. As políticas de acesso compartilhado podem conceder todas as combinações das permissões listadas na seção anterior. Você pode definir políticas no [Portal do Azure][lnk-management-portal] ou de forma programática usando as [APIs do provedor de recursos do Hub IoT do Azure][lnk-resource-provider-apis]. Um hub IoT recém-criado tem as seguintes políticas padrão:

    - **iothubowner**: política com todas as permissões.
    - **service**: política com a permissão ServiceConnect.
    - **device**: política com a permissão DeviceConnect.
    - **registryRead**: política com a permissão RegistryRead.
    - **registryReadWrite**: política com as permissões RegistryRead e RegistryWrite.


* **Credenciais de segurança de acordo com o dispositivo**. Cada Hub IoT contém um [registro de identidade do dispositivo](#device-identity-registry). É possível configurar as credenciais de segurança para cada dispositivo nesse registro concedendo permissões de **DeviceConnect** com escopo nos pontos de extremidade correspondentes do dispositivo.

Por exemplo, em uma solução de IoT típica:
- O componente de gerenciamento de dispositivo usa a política *registryReadWrite*.
- O componente de processador de eventos usa a política *service*.
- O componente de lógica de negócios do dispositivo em tempo de execução usa a política *service*.
- Os dispositivos individuais se conectam usando as credenciais armazenadas no registro de identidade do Hub IoT.

Para obter diretrizes sobre os tópicos de segurança do Hub IoT, veja a seção sobre segurança em [Projetar sua solução][lnk-guidance-security].

### Autenticação

O Hub IoT do Azure concede acesso aos pontos de extremidade, verificando um token com base nas políticas de acesso compartilhado e nas credenciais de segurança de registro de identidade do dispositivo.

As credenciais de segurança, como as chaves simétricas, nunca são enviadas pela conexão.

> [AZURE.NOTE] O provedor de recursos do Hub IoT do Azure é protegido por meio de sua assinatura do Azure, assim como todos os provedores no [Gerenciador de Recursos do Azure][lnk-azure-resource-manager].

Para saber mais sobre como construir e usar os tokens de segurança, veja o artigo [Tokens de segurança do Hub IoT][lnk-sas-tokens].

#### Especificações de protocolo

Cada protocolo com suporte, como MQTT, AMQP e HTTP, transporta os tokens de maneiras diferentes.


O HTTP implementa a autenticação incluindo um token válido no cabeçalho da solicitação **Authorization**.


Quando usa [AMQP][lnk-amqp], o Hub IoT dá suporte ao [SASL PLAIN][lnk-sasl-plain] e à [Segurança baseada em declarações AMQP][lnk-cbs].

Se você usar a segurança baseada em declarações AMQP, o padrão especificará como transmitir esses tokens.

Para SASL PLAIN, o **nome de usuário** pode ser:

* `{policyName}@sas.root.{iothubName}` se forem usados tokens de nível do hub.
* `{deviceId}` se forem usados tokens no escopo do dispositivo.

Em ambos os casos, o campo de senha contém o token, conforme descrito no artigo [Tokens de segurança do Hub IoT][lnk-sas-tokens].

Ao usar MQTT, o pacote CONNECT tem a deviceId como a ClientId, {iothubhostname}/{deviceId} no campo Nome de Usuário e um token SAS no campo Senha. {iothubhostname} deve ser o CName completo do Hub IoT (por exemplo, contoso.azure-devices.net).

##### Exemplo: #####

Nome de usuário (a DeviceId diferencia maiúsculas de minúsculas): `iothubname.azure-devices.net/DeviceId`

Senha (gerar SAS com o Gerenciador de Dispositivo): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] Os [SDKs do Hub IoT do Azure ][lnk-sdks] geram tokens automaticamente durante a conexão com o serviço. Em alguns casos, os SDKs não dão suporte a todos os protocolos ou a todos os métodos de autenticação.

#### Considerações especiais para SASL PLAIN

Ao usar o SASL PLAIN, um cliente que está se conectando a um hub IoT pode usar um único token para cada conexão TCP. Quando o token expirar, a conexão TCP será desconectada do serviço, disparando uma reconexão. Esse comportamento, embora não seja problemático para um componente de back-end de aplicativo, é muito prejudicial para um aplicativo do lado do dispositivo, por estes motivos:

*  Gateways normalmente se conectam em nome de vários dispositivos. Ao usar SASL PLAIN, eles precisam criar uma conexão TCP distinta para cada dispositivo que se conecta a um hub IoT. Esse cenário aumenta de forma considerável o consumo de energia e de recursos de rede, além de aumentar a latência de cada conexão de dispositivo.
* Os dispositivos com limitações de recursos são afetados de forma adversa pelo aumento do uso de recursos para se reconectar após cada expiração do token.

### Escopo das credenciais no nível do hub

Você pode definir o escopo das políticas de segurança no nível do hub por meio da criação de tokens com um URI de recursos restrito. Por exemplo, o ponto de extremidade para enviar mensagens do dispositivo para a nuvem é **/devices/{deviceId}/messages/events**. Você também pode usar uma política de acesso compartilhado no nível do hub com permissões **DeviceConnect** para assinar um token cujo resourceURI é **/devices/{deviceId}**. Essa abordagem cria um token que pode ser usado somente para enviar os dispositivos em nome do **deviceId** do dispositivo.

Esse mecanismo é semelhante à [política de editor dos Hubs de Eventos][lnk-event-hubs-publisher-policy] e permite implementar métodos de autenticação personalizados. Para saber mais, veja a seção de segurança [Projetar sua solução][lnk-guidance-security].

## Mensagens

O Hub IoT oferece primitivos de mensagens para comunicar:

- [Da nuvem para o dispositivo](#c2d) de um back-end de aplicativo (*serviço* ou *nuvem*).
- [Do dispositivo para a nuvem](#d2c) de um dispositivo para um back-end de aplicativo.
- [Carregamentos de arquivos](#fileupload) de um dispositivo para uma conta do Armazenamento do Azure associada.

As propriedades básicas da funcionalidade de mensagens do Hub IoT são a confiabilidade e a durabilidade das mensagens. Essas propriedades habilitam a adaptação à conectividade intermitente no lado do dispositivo e a picos de carga no processamento de eventos no lado da nuvem. O Hub IoT implementa *pelo menos uma vez* as garantias de entrega de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo.

O Hub IoT dá suporte a vários protocolos voltados para o dispositivo (como MQTT, AMQP e HTTP). Para dar suporte à interoperabilidade contínua entre protocolos, o Hub IoT define um formato de mensagem comum com suporte de todos os protocolos voltados para o dispositivo.

### Formato da mensagem <a id="messageformat"></a>

As mensagens do Hub IoT são formadas por:

* Um conjunto de *propriedades do sistema*. São propriedades que o Hub IoT interpreta ou define. Esse conjunto é predeterminado.
* Um conjunto de *propriedades do aplicativo*. É um dicionário de propriedades de cadeia de caracteres que o aplicativo pode definir e acessar sem precisar desserializar o corpo da mensagem. O Hub IoT nunca modifica essas propriedades.
* Um corpo de binário opaco.

Para saber mais sobre como a mensagem é codificada em protocolos diferentes, veja [APIs e SDKs do Hub IoT][lnk-sdks].

Esse é o conjunto de propriedades do sistema em mensagens do Hub IoT.

| Propriedade | Descrição |
| -------- | ----------- |
| MessageId | Um identificador configurável pelo usuário para a mensagem, usado para padrões de solicitação-resposta. Formato: uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de sequência | Um número (exclusivo por fila de dispositivos) atribuído pelo Hub IoT a cada mensagem da nuvem para o dispositivo. |
| Para | Um destino especificado em mensagens [Da nuvem para o dispositivo](#c2d). |
| ExpiryTimeUtc | Data e hora de expiração da mensagem. |
| EnqueuedTime | Data e hora do recebimento da mensagem pelo Hub IoT. |
| CorrelationId | Uma cadeia de propriedade em uma mensagem de resposta que geralmente contém a MessageId da solicitação em padrões de solicitação-resposta. |
| UserId | Uma ID usada para especificar a origem das mensagens. Quando as mensagens são geradas pelo Hub IoT, são definidas como `{iot hub name}`. |
| Ack | Um gerador de mensagem de comentários. Essa propriedade é usada em mensagens da nuvem para o dispositivo para solicitar ao Hub IoT a geração de mensagens de comentários como resultado do consumo da mensagem pelo dispositivo. Valores possíveis: **nenhum** (padrão): nenhuma mensagem de comentários é gerada, **positivo**: ocorrerá o recebimento de uma mensagem de comentários, se a mensagem estiver completa, **negativo**: ocorrerá o recebimento de uma mensagem de comentários, se a mensagem expirar (ou se a contagem máxima de entrega tiver sido atingida) sem ser concluída pelo dispositivo ou **total**: positivos e negativos. Para saber mais, consulte [Comentários sobre a mensagem](#feedback). |
| ConnectionDeviceId | Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Contém a **deviceId** do dispositivo que enviou a mensagem. |
| ConnectionDeviceGenerationId | Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Contém a **generationId** (de acordo com as [Propriedades de identidade de dispositivo](#deviceproperties)) do dispositivo que enviou a mensagem. |
| ConnectionAuthMethod | Um método de autenticação definido pelo Hub IoT em mensagens do dispositivo para a nuvem. Essa propriedade contém informações sobre o método de autenticação usado para autenticar o dispositivo que envia a mensagem. Para saber mais, veja [Antifalsificação do dispositivo para a nuvem](#antispoofing).|

### Escolha seu protocolo de comunicação <a id="amqpvshttp"></a>

O Hub IoT dá suporte aos protocolos MQTT, [AMQP][lnk-amqp], AMQP sobre WebSockets e HTTP/1 para comunicações do lado do dispositivo. A tabela a seguir fornece as recomendações de alto nível para sua escolha de protocolo:

| Protocolo | Quando você deve escolher este protocolo |
| -------- | ------------------------------------ |
| MQTT | Use todos os dispositivos que não exigem o uso de WebSockets. |
| AMQPS | Use em gateways de campo e de nuvem para tirar proveito da multiplexação de conexão entre dispositivos. <br/> Use quando você precisar se conectar à porta 443. |
| HTTPS | Use para dispositivos que não dão suporte a outros protocolos. |

Você deve considerar os seguintes pontos ao escolher seu protocolo de comunicação do lado do dispositivo:

* **Padrão da nuvem para o dispositivo**. O HTTP/1 não tem uma maneira eficiente de implementar o envio do servidor. Assim, ao usar HTTP/1, os dispositivos sondam o Hub IoT em busca de mensagens da nuvem para o dispositivo. Essa abordagem é muito ineficiente para o dispositivo e para o Hub IoT. De acordo com as diretrizes atuais de HTTP/1, cada dispositivo deve sondar mensagens a cada 25 minutos ou mais. Por outro lado, o MQTT e o AMQP dão suporte ao envio por push do servidor quando recebem mensagens de nuvem para o dispositivo. Eles permitem envios por push imediatos de mensagens do Hub IoT para o dispositivo. Se a latência de entrega for uma preocupação, o AMQP ou o MQTT serão as melhores opções de protocolo. Para dispositivos conectados raramente, o HTTP/1 funciona também.
* **Gateways de campo**. Ao usar HTTP/1 e MQTT, você não pode conectar vários dispositivos (cada um com suas próprias credenciais por dispositivo) usando a mesma conexão TLS. Assim, para [Cenários de gateway de campo][lnk-azure-gateway-guidance], esses protocolos ficam abaixo do ideal, pois exigem uma conexão TLS entre o gateway de campo e o Hub IoT para cada dispositivo conectado ao gateway de campo.
* **Dispositivos com poucos recursos**. As bibliotecas de MQTT e HTTP/1 têm uma superfície menor que as bibliotecas de AMQP. Dessa forma, caso o dispositivo tenha recursos limitados (por exemplo, menos de 1 MB de RAM), esses protocolos poderão ser a única implementação de protocolo disponível.
* **Percurso da rede**. O MQTT padrão escuta na porta 8883, o que pode causar problemas em redes que estão fechadas para protocolos não HTTP. HTTP e AMQP (sobre WebSockets) estão disponíveis para serem usados nesse cenário.
* **Tamanho da carga**. O AMQP e o MQTT são protocolos binários, que resultam em cargas significativamente mais compactas que o HTTP/1.

> [AZURE.NOTE] Ao usar o HTTP/1, cada dispositivo deverá sondar se há mensagens da nuvem para o dispositivo a cada 25 minutos ou mais. No entanto, durante o desenvolvimento, é aceitável sondar com mais frequência do que a cada 25 minutos.

<a id="mqtt-support">
#### Observações sobre o suporte ao MQTT
O Hub IoT implementa o protocolo MQTT v3.1.1 com as seguintes limitações e comportamento específico:

  * **Não há suporte para QoS 2**. Quando um cliente de dispositivo publica uma mensagem com o **QoS 2**, o Hub IoT fecha a conexão de rede. Quando um cliente de dispositivo assina um tópico com o **QoS 2**, o Hub IoT concede, no máximo, o nível 1 do QoS no pacote **SUBACK**.
  * **Mensagens marcadas como Retain não permanecem**. Se um dispositivo publica uma mensagem com o sinalizador RETAIN definido como 1, o Hub IoT adiciona a propriedade de aplicativo **x-opt-retain** à mensagem. Nesse caso, o Hub IoT não mantém a mensagem retain, mas a transmite ao aplicativo back-end.

Para saber mais, veja [Suporte ao MQTT do Hub IoT][lnk-mqtt-support].

Como uma consideração final, você deve examinar o [Gateway de protocolo do IoT do Azure][lnk-azure-protocol-gateway], que o habilita a implantar um gateway de protocolo personalizado de alto desempenho que interage diretamente com o Hub IoT. O gateway do protocolo IoT do Azure permite que você personalize o protocolo de dispositivo para acomodar as implantações de MQTT de nível industrial ou outros protocolos personalizados. Essa abordagem exige, no entanto, que você hospede internamente e opere um gateway de protocolo personalizado.

### Dispositivo para a nuvem <a id="d2c"></a>

Conforme detalhado na seção [Pontos de extremidade](#endpoints), as mensagens de dispositivo para a nuvem são enviadas por um ponto de extremidade voltado para o dispositivo (**/devices/{deviceId}/messages/events**). As mensagens são recebidas por meio de um ponto de extremidade voltado para o serviço (**/messages/events**) compatível com [Hubs de Eventos][lnk-event-hubs]. Portanto, você pode usar a integração de Hubs de Eventos Standard e os SDKs para receber mensagens do dispositivo para a nuvem.

O Hub IoT implementa um sistema de mensagens do dispositivo para a nuvem de maneira semelhante aos [Hubs de eventos][lnk-event-hubs]. As mensagens de dispositivo para a nuvem do Hub IoT são mais semelhantes a *eventos* de Hubs de Eventos do que *mensagens* do [Barramento de Serviço][lnk-servicebus].

Esta implementação tem as seguintes implicações:

* Assim como nos eventos dos Hubs de Eventos, as mensagens do dispositivo para a nuvem são duráveis e mantidas em um Hub IoT por sete dias (veja [Opções de configuração do dispositivo para a nuvem](#d2cconfiguration)).
* As mensagens do dispositivo para a nuvem são particionadas em um conjunto fixo de partições definido no momento da criação (confira [Opções de configuração do dispositivo para a nuvem](#d2cconfiguration)).
* Assim como nos Hubs de Eventos, os clientes que leem mensagens do dispositivo para a nuvem devem lidar com partições e pontos de verificação. Consulte [Hubs de Eventos - consumindo eventos][lnk-event-hubs-consuming-events].
* Assim como os eventos dos Hubs de Eventos, as mensagens do dispositivo para a nuvem podem ter no máximo 256 KB e podem ser agrupadas em lotes para otimizar os envios. Os lotes podem ter no máximo 256 KB e no máximo 500 mensagens.

No entanto, há algumas distinções importantes entre as mensagens do dispositivo para a nuvem do Hub IoT e os Hubs de Eventos:

* Como explicado na seção [Segurança](#security), o Hub IoT permite a autenticação e o controle de acesso por dispositivo.
* O Hub IoT permite que milhões de dispositivos se conectem ao mesmo tempo (consulte [Cotas e limitação](#throttling)), enquanto os Hubs de Eventos estão limitados a 5000 conexões AMQP por namespace.
* O Hub IoT não permite o particionamento arbitrário usando uma **PartitionKey**. As mensagens do dispositivo para a nuvem são particionadas com base em sua **deviceId** de origem.
* O dimensionamento do Hub IoT é um pouco diferente do dimensionamento de Hubs de Eventos. Para saber mais, consulte [Dimensionando o Hub IoT][lnk-guidance-scale].

Observe que isso não significa que você pode substituir o Hub IoT para os Hubs de Eventos em todos os cenários. Por exemplo, em alguns cálculos de processamento de eventos, talvez seja necessário fazer a repartição de eventos com relação a um campo ou propriedade diferente antes de analisar os fluxos de dados. Neste cenário, você poderia usar um Hub de Eventos para desacoplar duas partes do pipeline de processamento do fluxo. Para saber mais, confira *Partições* na [Visão geral dos Hubs de Eventos do Azure][lnk-eventhub-partitions].

Para obter detalhes sobre como usar as mensagens do dispositivo para a nuvem, consulte [SDKs e APIs do Hub IoT][lnk-sdks].

> [AZURE.NOTE] Ao usar o HTTP para enviar mensagens do dispositivo para a nuvem, os nomes e valores de propriedade poderão conter apenas caracteres alfanuméricos ASCII e ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

#### Tráfego sem telemetria

Muitos vezes, além dos pontos de dados de telemetria, os dispositivos também enviam mensagens e solicitações que exigem a execução e a manipulação da camada de lógica de negócios do aplicativo. Por exemplo, os alertas críticos que devem disparar uma ação específica no back-end ou as respostas de dispositivo a comandos enviados do back-end.

Para saber mais sobre a melhor maneira de processar esse tipo de mensagem, veja [Processamento do dispositivo para a nuvem][lnk-guidance-d2c-processing].

#### Opções de configuração do dispositivo para a nuvem <a id="d2cconfiguration"></a>

Um Hub IoT expõe as propriedades a seguir para permitir que você controle as mensagens do dispositivo para a nuvem.

* **Contagem de partição**. Defina essa propriedade no momento da criação e define o número de partições para inclusão do evento do dispositivo para a nuvem.
* **Período de retenção**. Esta propriedade especifica o período de retenção das mensagens do dispositivo para a nuvem. O padrão é de um dia, mas pode ser aumentado para sete dias.

Além disso, de maneira semelhante aos Hubs de Eventos, o Hub IoT permite que você gerencie grupos de consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem.

Você pode modificar todas essas propriedades, seja de maneira programática por meio de [Hub IoT do Azure – APIs do provedor de recursos][lnk-resource-provider-apis] ou usando o [Portal do Azure][lnk-management-portal].

#### Propriedades anti-falsificação <a id="antispoofing"></a>

Para evitar a falsificação em mensagens do dispositivo para a nuvem, o Hub IoT carimba todas as mensagens com as seguintes propriedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

As duas primeiras contêm a **deviceId** e a **generationId** do dispositivo de origem, conforme as [Propriedades de identidade do dispositivo](#deviceproperties).

A propriedade **ConnectionAuthMethod** contém um objeto JSON serializado com as seguintes propriedades:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

### Nuvem para o dispositivo <a id="c2d"></a>

Conforme detalhado na seção [Pontos de extremidade](#endpoints), você pode enviar mensagens da nuvem para o dispositivo por meio de um ponto de extremidade voltado para o serviço (**mensagens/devicebound**). Um dispositivo pode recebê-las por meio de um ponto de extremidade específico de dispositivo (**/devices/{deviceId}/messages/devicebound**).

Cada mensagem da nuvem para o dispositivo é direcionada a um único dispositivo, definindo a propriedade **to** como **/devices/{deviceId}/messages/devicebound**.

>[AZURE.IMPORTANT] Cada fila de dispositivo pode conter no máximo 50 mensagens da nuvem para o dispositivo. Tentar enviar mais mensagens para o mesmo dispositivo resultará em um erro.

> [AZURE.NOTE] Ao enviar mensagens da nuvem para o dispositivo, os nomes e valores de propriedade poderão conter apenas caracteres alfanuméricos ASCII e ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

#### Ciclo de vida da mensagem <a id="message lifecycle"></a>

Para implementar a garantia de entrega de mensagem pelo menos uma vez, as mensagens da nuvem para o dispositivos são mantidas em filas por dispositivo. Os dispositivos devem reconhecer explicitamente a *conclusão* para que o Hub IoT as remova da fila. Isso garante a resiliência contra falhas de conectividade e do dispositivo.

O diagrama a seguir mostra o gráfico de estado do ciclo de vida de uma mensagem da nuvem para o dispositivo.

![Ciclo de vida de mensagens da nuvem para o dispositivo][img-lifecycle]

Quando o serviço envia uma mensagem, ela é considerada *Enfileirada*. Quando um dispositivo deseja *receber* uma mensagem, o Hub IoT *bloqueia* a mensagem (define o estado como **Invisível**) para permitir que outros threads no mesmo dispositivo comecem a receber outras mensagens. Quando um thread de dispositivo conclui o processamento de uma mensagem, ele notifica o Hub IoT *concluindo* a mensagem.

Um dispositivo também pode:

- *Rejeitar* a mensagem, o que faz com que o Hub IoT a defina no estado **Morto**.
- *Abandonar* a mensagem, o que faz com que o Hub IoT coloque a mensagem de volta na fila com o estado definido como **Enfileirada**.

Um thread pode falhar ao processar uma mensagem sem notificar o Hub IoT. Nesse caso, as mensagens passam automaticamente do estado **Invisível** de volta para o estado **Enfileirada** após um *tempo limite de visibilidade (ou de bloqueio)*. O valor padrão desse tempo limite é um minuto.

Uma mensagem pode transitar entre os estados **Enfileirada** e **Invisível**, no máximo, pelo número de vezes especificado na propriedade **contagem máxima de entrega** no Hub IoT. Após esse número de transições, o Hub IoT definirá o estado da mensagem como **Morto**. Da mesma forma, o Hub IoT define o estado de uma mensagem como **Morto** após o tempo de expiração (confira [Vida útil](#ttl)).

Para obter um tutorial sobre mensagens da nuvem para o dispositivo, consulte [Introdução às mensagens da nuvem para o dispositivo do Hub IoT do Azure][lnk-getstarted-c2d-tutorial]. Para obter tópicos de referência sobre como as APIs e os SDKs diferentes expõem a funcionalidade de nuvem para o dispositivo, veja [APIs e SDKs do Hub IoT][lnk-sdks].

> [AZURE.NOTE] Normalmente, as mensagens da nuvem para o dispositivo serão concluídas sempre que a perda da mensagem não afetar a lógica do aplicativo. Por exemplo, o conteúdo da mensagem foi mantido com êxito no armazenamento local ou uma operação foi executada com êxito. A mensagem também pode transportar informações temporárias, cuja perda não afeta a funcionalidade do aplicativo. Às vezes, para tarefas de longa duração, você pode concluir a mensagem do dispositivo para a nuvem depois de manter a descrição da tarefa no armazenamento local. Em seguida, você pode notificar o back-end de aplicativo com uma ou mais mensagens de dispositivo para a nuvem em vários estágios de progresso da tarefa.

#### Expiração da mensagem (vida útil) <a id="ttl"></a>

Todas as mensagens da nuvem para o dispositivo têm um tempo de expiração. Esse tempo pode ser definido explicitamente pelo serviço (na propriedade **ExpiryTimeUtc**) ou pelo Hub IoT usando o *tempo de vida* padrão especificado como uma propriedade do Hub IoT. Consulte [Opções de configuração da nuvem para o dispositivo](#c2dconfiguration).

> [AZURE.NOTE] Uma maneira comum de tirar proveito da expiração da mensagem é definir valores baixos de vida útil para evitar o envio de mensagens para dispositivos desconectados. Essa abordagem proporciona o mesmo resultado que a manutenção do estado de conexão do dispositivo, embora seja significativamente mais eficiente. Por meio da solicitação de confirmações de mensagens, você pode receber uma notificação pelo Hub IoT de quais dispositivos são capazes de receber mensagens e quais não estão online ou apresentam falha.

#### Comentários da mensagem <a id="feedback"></a>

Quando você envia uma mensagem da nuvem para o dispositivo, o serviço pode solicitar a entrega de um comentário por mensagem sobre o estado final dessa mensagem.

- Se você definir a propriedade **Ack** como **positivo**, o Hub IoT gerará uma mensagem de comentários se, e apenas nesse caso, a mensagem da nuvem para o dispositivo atingir o estado **Concluído**.
- Se você definir a propriedade **Ack** como **negativo**, o Hub IoT gerará uma mensagem de comentários se, e apenas nesse caso, a mensagem da nuvem para o dispositivo atingir o estado **Morto**.
- Se você definir a propriedade **Ack** como **total**, o Hub IoT gerará uma mensagem de comentários em ambos os casos.

> [AZURE.NOTE] Se **Ack** estiver definida como **total** e você não receber uma mensagem de comentários, isso significará que a mensagem de comentários expirou. O serviço não pode saber o que aconteceu com a mensagem original. Na prática, um serviço deve garantir que possa processar os comentários antes que eles expirem. O tempo máximo de expiração é de dois dias, o que permite tempo suficiente para restabelecer a execução do problema caso ocorra uma falha.

Como explicado em [Pontos de extremidade](#endpoints), o Hub IoT fornece comentários por meio de um ponto de extremidade voltado para o serviço (**/messages/servicebound/feedback**) como mensagens. A semântica de recebimento dos comentários é a mesma das mensagens da nuvem para o dispositivo e tem o mesmo [ciclo de vida da mensagem](nº do ciclo de vida da mensagem). Sempre que possível, os comentários de mensagem são feitos em lotes em uma única mensagem, com o seguinte formato.

Cada mensagem recuperada por um dispositivo do ponto de extremidade dos comentários tem estas propriedades:

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTime | Carimbo de data/hora que indica quando a mensagem foi criada. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

O corpo é uma matriz de registros serializada em JSON, cada um com as seguintes propriedades:

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTimeUtc | Carimbo de data e hora que indica quando ocorreu a saída da mensagem. Por exemplo, o dispositivo foi concluído ou a mensagem expirou. |
| OriginalMessageId | **MessageId** da mensagem da nuvem para o dispositivo a qual essas informações de comentários pertencem. |
| StatusCode | Inteiro necessário. Usado em mensagens de comentários geradas pelo Hub IoT. <br/> 0 = êxito <br/> 1 = mensagem expirou <br/> 2 = contagem de entrega máxima excedida <br/> 3 = mensagem rejeitada |
| Descrição | Valores de cadeia de caracteres para **StatusCode**. |
| DeviceId | **DeviceId** do dispositivo de destino da mensagem da nuvem para o dispositivo a qual pertence esses comentários. |
| DeviceGenerationId | **DeviceGenerationId** do dispositivo de destino da mensagem da nuvem para o dispositivo a qual pertence esses comentários. |


>[AZURE.IMPORTANT] O serviço deve especificar uma **MessageId** para a mensagem da nuvem para o dispositivo, para poder correlacionar seus comentários com a mensagem original.

O exemplo a seguir mostra o corpo de uma mensagem de comentários.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

#### Opções de configuração da nuvem para o dispositivo <a id="c2dconfiguration"></a>

Cada hub IoT expõe as seguintes opções de configuração para mensagens da nuvem para o dispositivo.

| Propriedade | Descrição | Intervalo e padrão |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL padrão para mensagens da nuvem para o dispositivo. | Intervalo ISO\_8601 de até 2D (mínimo de 1 minuto). Padrão: 1 hora. |
| maxDeliveryCount | Contagem máxima de entrega para filas de nuvem para o dispositivo por dispositivo. | 1 a 100. Padrão: 10. |
| feedback.ttlAsIso8601 | Retenção de mensagens informativas do serviço associado. | Intervalo ISO\_8601 de até 2D (mínimo de 1 minuto). Padrão: 1 hora. |
| feedback.maxDeliveryCount | Contagem máxima de entrega para a fila de comentários. | 1 a 100. Padrão: 100. |

Para saber mais, veja [Gerenciar Hubs IoT][lnk-portal].

### Carregamentos de arquivos <a id="fileupload"></a>

Como detalhado na seção [Pontos de extremidade](#endpoints), os dispositivos podem iniciar uploads enviando uma notificação por meio de um ponto de extremidade voltado para o dispositivo (**/devices/{Iddodispositivo}/arquivos**). Quando um dispositivo notifica o Hub IoT de um upload concluído, o Hub IoT gera notificações de upload de arquivo que você pode receber por meio de um ponto de extremidade voltado para o serviço (**/messages/servicebound/filenotifications**) como mensagens.

Em vez da corretagem mensagens por meio do próprio Hub IoT, o Hub IoT age como um distribuidor para uma conta do Armazenamento do Azure associada. Um dispositivo solicita um token de armazenamento do Hub IoT específico para o arquivo que o dispositivo deseja carregar. O dispositivo usa o URI de SAS para carregar o arquivo de armazenamento e, quando o upload for concluído, o dispositivo enviará uma notificação de conclusão para o Hub IoT. O Hub IoT verifica se o arquivo foi carregado e adiciona uma notificação de upload de arquivo ao novo ponto de extremidade de mensagens de notificação de arquivo voltado para o serviço.

#### Associação de uma conta do Armazenamento do Azure com o Hub IoT

Para usar a funcionalidade de upload de arquivos, primeiro você deve vincular uma conta do Armazenamento do Azure para o Hub IoT. Você pode fazer isso por meio do [Portal do Azure][lnk-management-portal] ou programaticamente por meio do [Hub IoT do Azure – APIs do provedor de recursos][lnk-resource-provider-apis]. Depois de ter associado a uma conta de armazenamento ao Hub IoT, o serviço retorna um URI de SAS para um dispositivo quando o dispositivo inicia uma solicitação de upload de arquivos.

> [AZURE.NOTE] Os [SDKs do Hub IoT do Azure][lnk-sdks] tratam automaticamente da recuperação do URI de SAS, do upload do arquivo e da notificação do Hub IoT de um upload concluído.

#### Inicializar um upload de arquivo

O Hub IoT tem dois pontos de extremidade REST para dar suporte ao upload de arquivo, um para obter o URI de SAS para armazenamento e o outro para notificar o hub IoT de um upload concluído. O dispositivo inicia o processo de upload de arquivo, enviando um GET para o hub IoT em `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. O hub retorna um URI de SAS específico para o arquivo a ser carregado, bem como uma ID de correlação a ser usada quando o upload for concluído.

#### Notificar o Hub IoT de um upload de arquivo concluído

O dispositivo é responsável por carregar o arquivo para o armazenamento usando os SDKs do Armazenamento do Azure. Quando o carregamento for concluído, o dispositivo enviará um POST para o hub IoT em `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications/{correlationId}` usando a ID de correlação recebida do GET inicial.

#### Notificações de upload de arquivo

Quando um dispositivo carrega um arquivo e notifica o Hub IoT da conclusão do upload, o serviço gera opcionalmente uma mensagem de notificação com o local de armazenamento e o nome do arquivo.

Como explicado em [Pontos de extremidade](#endpoints), o Hub IoT fornece notificações de upload de arquivos por meio de um ponto de extremidade voltado para o serviço (**/messages/servicebound/feedback**) como mensagens. A semântica de recebimento das notificações de upload de arquivos é a mesma das mensagens da nuvem para o dispositivo e tem o mesmo [ciclo de vida da mensagem](nº do ciclo de vida da mensagem). Cada mensagem recuperada do ponto de extremidade de notificação de upload de arquivos é um registro JSON com as seguintes propriedades:

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTimeUtc | Carimbo de data/hora que indica quando a notificação foi criada. |
| DeviceId | **DeviceId** do dispositivo que carregou o arquivo. |
| BlobUri | URI do arquivo carregado. |
| BlobName | Nome do arquivo carregado. |
| LastUpdatedTime | Carimbo de data/hora que indica quando o arquivo foi atualizado pela última vez. |
| BlobSizeInBytes | Tamanho do arquivo carregado. |

**Exemplo**. Este é um exemplo de corpo de uma mensagem de notificação de upload de arquivos.

```
{
	"deviceId":"mydevice",
	"blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
	"blobName":"mydevice/myfile.jpg",
	"lastUpdatedTime":"2016-06-01T21:22:41+00:00",
	"blobSizeInBytes":1234,
	"enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

#### Opções de configuração de notificação de upload de arquivo <a id="c2dconfiguration"></a>

Cada hub IoT expõe as seguintes opções de configuração para notificações de upload de arquivos:

| Propriedade | Descrição | Intervalo e padrão |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | Controla se as notificações de upload de arquivos serão gravadas no ponto de extremidade de notificações de arquivo. | Bool. Padrão: True. |
| **fileNotifications.ttlAsIso8601** | TTL padrão para notificações de upload de arquivos. | Intervalo ISO\_8601 de até 48H (mínimo de um minuto). Padrão: 1 hora. |
| **fileNotifications.lockDuration** | Duração de bloqueio para a fila de notificações de upload de arquivos. | 5 a 300 segundos (mínimo de cinco segundos). Padrão: 60 segundos. |
| **fileNotifications.maxDeliveryCount** | Contagem máxima de entregas para a fila de notificação de upload de arquivos. | 1 a 100. Padrão: 100. |

Para saber mais, confira [Gerenciar hubs IoT][lnk-portal].

## Cotas e limitação <a id="throttling"></a>

Cada assinatura do Azure pode ter no máximo 10 hubs IoT.

Cada hub IoT é provisionado com um determinado número de unidades em um SKU específico (para obter mais informações, veja [Preços do Hub IoT do Azure][lnk-pricing]). O SKU e o número de unidades determinam a cota máxima diária de mensagens que você pode enviar.

O SKU também determina os limites impostos pelo Hub IoT em todas as operações.

### Restrições de operação

As restrições de operação são limites de taxa aplicados em intervalos de minutos, e têm como objetivo evitar o abuso. O Hub IoT tenta evitar o retorno de erros sempre que possível, mas ele começará a retornar exceções se a restrição for violada por muito tempo.

A seguir, a lista de limitações impostas. Os valores referem-se a um hub individual.

| Restrição | Valor por hub |
| -------- | ------------- |
| Operações de registro de identidade (criar, recuperar, listar, atualizar, excluir) | 5000/min/unidade (para S3) <br/> 100/min/unidade (para S1 e S2). |
| Conexões do dispositivo | 6000/s/unidade (para S3), 120/s/unidade (S2), 12/s/unidade (para S1). <br/>Mínimo de 100/s. <br/> Por exemplo, duas unidades de S1 são 2*12 = 24/s, mas você tem pelo menos 100/s em suas unidades. Com nove unidades S1 você tem 108/s (9*12) em suas unidades. |
| Envios do dispositivo para a nuvem | 6000/s/unidade (para S3), 120/s/unidade (S2), 12/s/unidade (para S1). <br/>Mínimo de 100/s. <br/> Por exemplo, duas unidades de S1 são 2*12 = 24/s, mas você tem pelo menos 100/s em suas unidades. Com nove unidades S1 você tem 108/s (9*12) em suas unidades. |
| Envios da nuvem para o dispositivo | 5000/min/unidade (para S3), 100/min/unidade (para S1 e S2). |
| Recebimentos da nuvem para o dispositivo | 50000/min/unidade (para S3) 1000/min/unidade (para S1 e S2). |
| Operações de upload de arquivo | 5000 notificações de upload de arquivos/min/unidade (para S3), 100 notificações de upload de arquivos/min/unidade (para S1 e S2). <br/> 10000 URIs de SAS podem estar fora de uma conta de armazenamento ao mesmo tempo.<br/> 10 URIs de SAS/dispositivo podem estar fora ao mesmo tempo. | 

É importante esclarecer que a restrição de *conexões de dispositivo* controla a taxa em que novas conexões de dispositivo podem ser estabelecidas com um Hub IoT e não o número máximo de dispositivos conectados simultaneamente. A restrição depende do número de unidades provisionadas para o hub.

Por exemplo, se você comprar uma única unidade S1, obterá uma restrição de 100 conexões por segundo. Isso significa que serão necessários pelo menos 1000 segundos (aproximadamente 16 minutos) para conectar 100.000 dispositivos. No entanto, você pode conectar ao mesmo tempo todos os seus dispositivos registrados no registro de identidade do dispositivo.

Para uma discussão aprofundada do comportamento de limitação do Hub IoT, veja a postagem do blog [Hub IoT throttling and you][lnk-throttle-blog] (A limitação do Hub IoT e você).

>[AZURE.NOTE] A qualquer momento, é possível aumentar as cotas ou restrições aumentando o número de unidades provisionadas em um Hub IoT.

>[AZURE.IMPORTANT] As operações de registro de identidade são destinadas ao uso no tempo de execução em cenários de provisionamento e gerenciamento de dispositivos. Há suporte para leitura ou atualização de grandes números de identidades de dispositivo por meio de [trabalhos de importação/exportação](#importexport).

## Próximas etapas

Agora que você viu uma visão geral do desenvolvimento para Hub IoT, consulte o seguinte para saber mais:

- [Upload de arquivo de dispositivos (tutorial)][lnk-file upload]
- [Criar um hub IoT programaticamente][lnk-create-hub]
- [Introdução ao SDK de C][lnk-c-sdk]
- [SDKs do Hub IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
- [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
- [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]
- [Proteger sua solução de IoT desde o início][lnk-securing]



[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0907_2016-->