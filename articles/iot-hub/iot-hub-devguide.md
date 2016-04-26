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
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Guia do desenvolvedor do Hub IoT do Azure

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo.

Com o Hub IoT do Azure, é possível:

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

O Hub IoT do Azure é um serviço multilocatário que expõe suas funcionalidades a diversos atores. O diagrama a seguir mostra os diversos pontos de extremidade que o Hub IoT expõe.

![Pontos de extremidade do Hub IoT][img-endpoints]

A seguir, uma descrição dos pontos de extremidade:

* **Provedor de recursos**: o provedor de recursos do Hub IoT expõe uma interface do [Gerenciador de Recursos do Azure][lnk-arm], que permite aos proprietários de assinatura do Azure criar Hubs IoT, atualizar propriedades do Hub IoT e excluir Hubs IoT. As propriedades do Hub IoT regem as políticas de segurança no nível do hub, ao contrário do controle de acesso no nível do dispositivo ( consulte [Controle de acesso](#accesscontrol) abaixo) e das opções funcionais para sistema de mensagens da nuvem para o dispositivo e do dispositivo para a nuvem. O provedor de recursos também permite [exportar identidades do dispositivo](#importexport).
* **Gerenciamento de identidade dos dispositivos**: cada Hub IoT expõe um conjunto de pontos de extremidade HTTP REST para gerenciamento de identidades do dispositivo (criar, recuperar, atualizar e excluir). As identidades do dispositivo são usadas para controle de acesso e autenticação do dispositivo. Consulte [Registro de identidade do dispositivo](#device-identity-registry) para saber mais.
* **Pontos de extremidade do dispositivo**: para cada dispositivo provisionado no registro de identidade do dispositivo, o Hub IoT expõe um conjunto de pontos de extremidade que pode ser usado por um dispositivo para enviar e receber mensagens:
    - *Enviar mensagens do dispositivo para a nuvem*. Use este ponto de extremidade para enviar mensagens do dispositivo para a nuvem. Para saber mais, consulte [Sistema de mensagens do dispositivo para a nuvem](#d2c).
    - *Receber mensagens da nuvem para o dispositivo*. Um dispositivo usa este ponto de extremidade para receber mensagens direcionadas da nuvem para o dispositivo. Para saber mais, consulte [Sistema de mensagens da nuvem para o dispositivo](#c2d).

    Esses pontos de extremidade são expostos usando os protocolos HTTP 1.1, [MQTT v3.1.1][lnk-mqtt] e [AMQP 1.0][lnk-amqp]. Observe que o AMQP também está disponível em [WebSockets][lnk-websockets] na porta 443.
* **Pontos de extremidade do serviço**: cada Hub IoT expõe um conjunto de pontos de extremidade que o seu back-end de aplicativo pode usar para se comunicar com dispositivos. Atualmente, esses pontos de extremidade são expostos apenas com o protocolo [AMQP][lnk-amqp].
    - *Receber mensagens do dispositivo para a nuvem*. Esse ponto de extremidade é compatível com os [Hubs de Eventos do Azure][lnk-event-hubs] e um serviço de back-end pode usá-lo para ler todas as mensagens do dispositivo para a nuvem enviadas por seus dispositivos. Para saber mais, consulte [Sistema de mensagens do dispositivo para a nuvem](#d2c).
    - *Enviar mensagens da nuvem para o dispositivo e receber confirmações de entrega*. Esses pontos de extremidade permitem que o seu back-end de aplicativo envie mensagens confiáveis da nuvem para o dispositivo e receba confirmações de entrega ou de vencimento correspondentes. Para saber mais, consulte [Sistema de mensagens da nuvem para o dispositivo](#c2d).

O artigo [APIs e SDKs do Hub IoT][lnk-apis-sdks] descreve as diversas maneiras de acessar esses pontos de extremidade.

Por fim, é importante observar que todos os pontos de extremidade do Hub IoT usam o protocolo [TLS][lnk-tls] e que nenhum ponto de extremidade é exposto em canais sem criptografia/desprotegidos.

### Como ler de pontos de extremidade compatíveis com os Hubs de Eventos. <a id="eventhubcompatible"></a>

Ao usar o [SDK do Barramento de Serviço do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) ou os [Hubs de Eventos - Host Processador de Eventos][], você pode usar cadeias de conexão do Hub IoT com as permissões corretas e usar **mensagens/eventos** como o nome do Hub de Eventos.

Ao usar os SDKs (ou integrações de produtos) que não reconhecem o Hub IoT, será necessário recuperar um ponto de extremidade compatível com os Hubs de Eventos e o nome do Hub de Evento das configurações do Hub IoT no [Portal do Azure][]\:

1. Na folha do Hub IoT, clique em **Configurações** e em **Sistema de Mensagens**,
2. Na seção **Configurações de dispositivo para nuvem**, você encontrará os valores de **Ponto de extremidade compatível com o Hub de Eventos**, de **Nome compatível com o Hub de Eventos** e de **Partições**.

    ![][img-eventhubcompatible]

> [AZURE.NOTE] Nos casos em que o SDK exige um valor de **Nome de host** ou **Namespace**, remova o esquema do **Ponto de extremidade compatível com o Hub de Eventos**. Por exemplo, se o ponto de extremidade compatível com o Hub de Eventos for **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, o **Nome do Host** será **iothub-ns-myiothub-1234.servicebus.windows.net** e o **Namespace** será **iothub-ns-myiothub-1234**.

Dessa forma, você poderá usar qualquer política de segurança de acesso compartilhado com permissões **ServiceConnect** para se conectar ao Hub de Eventos especificado.

Caso você tenha de criar uma cadeia de conexão do Hub de Eventos usando as informações anteriores, use o seguinte padrão:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

A seguir, uma lista dos SDKs e das integrações que você pode usar com os pontos de extremidade compatíveis com o Hub de Eventos expostos pelo Hub IoT:

* [Cliente Java dos Hubs de Eventos](https://github.com/hdinsight/eventhubs-client)
* [Spout do Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [fonte do spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## Registro de identidade do dispositivo

Cada Hub IoT tem um registro de identidade do dispositivo que você pode usar para criar recursos de acordo com o dispositivo no serviço, por exemplo, uma fila contendo mensagens em andamento da nuvem para o dispositivo, e permite o acesso aos pontos de extremidade voltados para o dispositivo, como explicado na seção [Controle de acesso](#accesscontrol).

Em um alto nível, o registro de identidade do dispositivo é uma coleção compatível com REST de recursos de identidade do dispositivo. As seções a seguir detalham as propriedades do recurso de identidade do dispositivo e as operações que o registro habilita em identidades.

> [AZURE.NOTE] Confira [APIs e SDKs do Hub IoT][lnk-apis-sdks] para obter mais detalhes sobre o protocolo HTTP e os SDKs que você pode usar para interagir com o registro de identidade do dispositivo.

### Propriedades de identidade do dispositivo <a id="deviceproperties"></a>

As identidades do dispositivo são representadas como documentos JSON com as seguintes propriedades.

| Propriedade | Opções | Descrição |
| -------- | ------- | ----------- |
| deviceId | obrigatória, somente leitura em atualizações | Uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | obrigatória, somente leitura | Uma cadeia de caracteres que diferencia maiúsculas de minúsculas gerada pelo hub com até 128 caracteres. Isso é usado para distinguir os dispositivos com o mesmo **deviceId** quando são excluídos e recriados. |
| etag | obrigatória, somente leitura | Uma cadeia de caracteres que representa uma etag fraca para a identidade do dispositivo, de acordo com [RFC7232][lnk-rfc7232].|
| auth | opcional | Um objeto composto que contém as informações de autenticação e os materiais de segurança. |
| auth.symkey | opcional | Um objeto composto que contém as chaves primária e secundária, armazenadas no formato base64. |
| status | obrigatório | Pode estar **Habilitado** ou **Desabilitado**. Se estiver **Habilitado**, o dispositivo terá permissão para se conectar. Se estiver **Desabilitado**, este dispositivo não poderá acessar qualquer ponto de extremidade voltado para o dispositivo. |
| statusReason | opcional | Uma cadeia de caracteres com 128 caracteres que armazena o motivo do status de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime | somente leitura | Data e hora da última atualização de status. |
| connectionState | somente leitura | **Conectado** ou **Desconectado**, representa a exibição Hub IoT do status de conexão do dispositivo. **Importante**: esse campo deve ser usado apenas para fins de desenvolvimento/depuração. O estado da conexão é atualizado somente para dispositivos que usam AMQP ou MQTT. Além disso, ele se baseia nos pings do nível de protocolo (pings MQTT ou AMQP) e pode ter um atraso de, no máximo, 5 minutos. Por esses motivos pode haver falsos positivos, como dispositivos relatados como conectados, mas que, de fato, estão desconectados. |
| connectionStateUpdatedTime | somente leitura | Data e hora da última atualização do estado da conexão. |
| lastActivityTime | somente leitura | Data e hora da última vez em que o dispositivo se conectou, recebeu ou enviou uma mensagem. |

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

Você pode desabilitar dispositivos atualizando a propriedade **status** de uma identidade no registro. Normalmente, isso é usado em dois cenários:

- Durante um processo de orquestração de provisionamento. Para saber mais, confira [Projetar sua solução - provisionamento de dispositivos][lnk-guidance-provisioning].
- Se, por algum motivo, você considerar que um dispositivo está comprometido ou que se tornou não autorizado.

### Exportar identidades do dispositivo <a id="importexport"></a>

As exportações são trabalhos de execução longa que usam um contêiner de blobs fornecido pelo cliente para salvar dados de identidade do dispositivo lidos no registro de identidade.

Você pode exportar identidades do dispositivo em massa do registro de identidade de um Hub IoT usando operações assíncronas no [ponto de extremidade do Provedor de Recursos do Hub IoT](#endpoints).

As operações a seguir podem ser executadas em trabalhos de exportação:

* Criar um trabalho de exportação
* Recuperar o status de um trabalho em execução
* Cancelar um trabalho em execução

> [AZURE.NOTE] Não importa o momento, cada hub pode ter apenas um único trabalho em execução.

Para obter informações detalhadas sobre as APIs de importação e exportação, consulte [Hub IoT do Azure - APIs do provedor de recursos][lnk-resource-provider-apis].

Para saber mais sobre a execução de trabalhos de importação e exportação, confira [Gerenciamento em massa de identidades do dispositivo do Hub IoT][lnk-bulk-identity]

### Trabalhos de exportação

Todos os trabalhos de exportação têm as propriedades a seguir:

| Propriedade | Opções | Descrição |
| -------- | ------- | ----------- |
| jobId | gerada pelo sistema, ignorada na criação | |
| creationTime | gerada pelo sistema, ignorada na criação | |
| endOfProcessingTime | gerada pelo sistema, ignorada na criação | |
| type | somente leitura | **ExportDevices** |
| status | gerada pelo sistema, ignorada na criação | **Em fila**, **Iniciado**, **Concluído**, **Falha** |
| progresso | gerada pelo sistema, ignorada na criação | Valor inteiro da porcentagem de conclusão. |
| outputBlobContainerURI | obrigatório para todos os trabalhos | URI da Assinatura de Acesso Compartilhado do Blob com acesso de gravação a um contêiner de blobs (confira [Criar e usar uma SAS com o serviço Blob][lnk-createuse-sas]). Isso é usado para mostrar o status do trabalho e os resultados. |
| excludeKeysInExport | opcional | Se for **false**, as chaves serão incluídas na saída da exportação; caso contrário, as chaves serão exportadas como **null**. O padrão é **false**. |
| failureReason | gerada pelo sistema, ignorada na criação | Se o status for **Falha**, uma cadeia de caracteres mostrará o motivo. |

Os trabalhos de exportação obtêm um URI de Assinatura de Acesso Compartilhado de blob como um parâmetro. Isso concede acesso de gravação a um contêiner de blob para habilitar o trabalho de seus resultados de saída.

O trabalho grava os resultados da saída no contêiner de blobs especificado em um arquivo chamado **devices.text**. Esse arquivo contém identidades do dispositivo serializadas como JSON, conforme especificado nas [Propriedades de identidade do dispositivo](#deviceproperties). O valor de autenticação será definido como **null** para cada dispositivo no arquivo **devices.txt** se o parâmetro **excludeKeysInExport** for definido como **true**.

**Exemplo**:

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
```

### Importar identidades do dispositivo

As exportações são trabalhos de execução longa que usam dados em um contêiner de blobs fornecido pelo cliente para gravar dados de identidade no registro de identidade do dispositivo.

Você pode importar identidades do dispositivo em massa para o registro de identidade de um Hub IoT usando operações assíncronas no [ponto de extremidade do Provedor de Recursos do Hub IoT](#endpoints).

As operações a seguir podem ser executadas em trabalhos de importação:

* Criar um trabalho de importação
* Recuperar o status de um trabalho em execução
* Cancelar um trabalho em execução

> [AZURE.NOTE] Não importa o momento, cada hub pode ter apenas um único trabalho em execução.

Para obter informações detalhadas sobre as APIs de importação e exportação, consulte [Hub IoT do Azure - APIs do provedor de recursos][lnk-resource-provider-apis].

Para saber mais sobre a execução de trabalhos de importação e exportação, confira [Gerenciamento em massa de identidades do dispositivo do Hub IoT][lnk-bulk-identity]

### Trabalhos de importação

Todos os trabalhos de importação têm as propriedades a seguir:

| Propriedade | Opções | Descrição |
| -------- | ------- | ----------- |
| jobId | gerada pelo sistema, ignorada na criação | |
| creationTime | gerada pelo sistema, ignorada na criação | |
| endOfProcessingTime | gerada pelo sistema, ignorada na criação | |
| type | somente leitura | **ImportDevices** |
| status | gerada pelo sistema, ignorada na criação | **Em fila**, **Iniciado**, **Concluído**, **Falha** |
| progresso | gerada pelo sistema, ignorada na criação | Valor inteiro da porcentagem de conclusão. |
| outputBlobContainerURI | obrigatório para todos os trabalhos | URI da Assinatura de Acesso Compartilhado do Blob com acesso de gravação a um contêiner de blobs (confira [Criar e usar uma SAS com o serviço Blob][lnk-createuse-sas]). Isso é usado para mostrar o status do trabalho. |
| inputBlobContainerURI | obrigatório | URI da Assinatura de Acesso Compartilhado do Blob com acesso de leitura a um contêiner de blobs (confira [Criar e usar uma SAS com o serviço Blob][lnk-createuse-sas]). O trabalho lê as informações do dispositivo para importar desse blob. |
| failureReason | gerada pelo sistema, ignorada na criação | Se o status for **Falha**, uma cadeia de caracteres mostrará o motivo. |

Os trabalhos de importação usam dois URIs de Assinatura de Acesso Compartilhado do blob como parâmetros. Um concede acesso de gravação a um contêiner de blobs para permitir que o trabalho mostre seu status, o outro concede acesso de leitura a um contêiner de blobs para permitir que o trabalho leia seus dados de entrada.

O trabalho lê os dados de entrada no contêiner de blobs especificado em um arquivo chamado **devices.text**. Esse arquivo contém identidades do dispositivo serializadas como JSON, conforme especificado nas [Propriedades de identidade do dispositivo](#deviceproperties). Você pode substituir o comportamento padrão de importação para cada dispositivo adicionando uma propriedade **importMode**. Essa propriedade pode usar um dos seguintes valores:

| importMode | Descrição |
| -------- | ----------- |
| **createOrUpdate** | Se não existir um dispositivo com a **id** especificada, ele foi recentemente registrado. <br/>Se o dispositivo já existir, as informações existentes serão substituídas pelos dados de entrada fornecidos sem considerar o valor de **ETag**. |
| **create** | Se não existir um dispositivo com a **id** especificada, ele foi recentemente registrado. <br/>Se o dispositivo já existir, um erro será gravado no arquivo de log. |
| **atualizar** | Se um dispositivo já existir com a **id** especificada, as informações existentes serão substituídas pelos dados de entrada fornecidos sem considerar o valor de **ETag**. <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. |
| **updateIfMatchETag** | Se um dispositivo já existir com a **id** especificada, as informações existentes serão substituídas pelos dados de entrada fornecidos somente se houver uma correspondência de **Etag**. <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. <br/>Se houver uma incompatibilidade de **ETag**, um erro será gravado no arquivo de log. |
| **createOrUpdateIfMatchETag** | Se não existir um dispositivo com a **id** especificada, ele foi recentemente registrado. <br/>Se o dispositivo já existir, as informações existentes serão substituídas pelos dados de entrada fornecidos somente se houver uma correspondência de **Etag**. <br/>Se houver uma incompatibilidade de **ETag**, um erro será gravado no arquivo de log. |
| **delete** | Se já existir um dispositivo com a **id** especificada, ele será excluído sem considerar o valor de **ETag**. <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. |
| **deleteIfMatchETag** | Se um dispositivo já existir com a **id** especificada, ele será excluído somente se houver uma correspondência de **Etag**. Se o dispositivo não existir, um erro será gravado no arquivo de log. <br/>Se houver uma incompatibilidade de ETag, um erro será gravado no arquivo de log. |

**Exemplo**:

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"delete"}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"createOrUpdate"}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"create"}
```

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

    - *iothubowner*: política com todas as permissões,
    - *service*: política com a permissão **ServiceConnect**,
    - *device*: política com a permissão **DeviceConnect**,
    - *registryRead*: política com a permissão **RegistryRead**,
    - *registryReadWrite*: política com as permissões **RegistryRead** e **RegistryWrite**.

* **Credenciais de segurança de acordo com o dispositivo**. Cada Hub IoT contém um [registro de identidade do dispositivo](#device-identity-registry). É possível configurar as credenciais de segurança para cada dispositivo nesse registro concedendo permissões de **DeviceConnect** com escopo nos pontos de extremidade correspondentes do dispositivo.

**Exemplo**. Em uma solução de IoT típica:
- O componente de gerenciamento de dispositivo usa a política *registryReadWrite*.
- O componente processador de eventos usa a política *service*.
- O componente de lógica comercial em tempo de execução dispositivo usa a política *service*.
- Os dispositivos individuais se conectam usando as credenciais armazenadas no registro de identidade do Hub IoT.

Para obter diretrizes sobre os tópicos de segurança do Hub IoT, confira a seção [Projetar sua solução][lnk-guidance-security].

### Autenticação

O Hub IoT do Azure concede acesso aos pontos de extremidade, verificando um token com base nas políticas de acesso compartilhado e nas credenciais de segurança de registro de identidade do dispositivo.

As credenciais de segurança, como as chaves simétricas, nunca são enviadas pela conexão.

> [AZURE.NOTE] O provedor de recursos do Hub IoT do Azure é protegido por meio de sua assinatura do Azure, assim como todos os provedores no [Gerenciador de Recursos do Azure][lnk-azure-resource-manager].

Consulte o artigo [Tokens de segurança do Hub IoT][lnk-sas-tokens] para obter mais informações sobre como construir e usar tokens de segurança.

#### Especificações de protocolo

Cada protocolo com suporte, como AMQP, MQTT e HTTP, transporta os tokens de maneiras diferentes.

O HTTP implementa a autenticação ao incluir um token válido no cabeçalho da solicitação **Authorization**. Um parâmetro de consulta chamado **Autorização** também pode transportar o token.

Quando usa [AMQP][lnk-amqp], o Hub IoT dá suporte ao [SASL PLAIN][lnk-sasl-plain] e à [Segurança baseada em declarações AMQP][lnk-cbs].

No caso da segurança baseada em declarações AMQP, o padrão especifica como transmitir esses tokens.

Para SASL PLAIN, o **nome de usuário** pode ser:

* `{policyName}@sas.root.{iothubName}` no caso de tokens no nível do hub.
* `{deviceId}` no caso de tokens com escopo do dispositivo.

Em ambos os casos, o campo de senha contém o token, conforme descrito no artigo [Tokens de segurança do Hub IoT][lnk-sas-tokens].

Ao usar MQTT, o pacote CONNECT tem a deviceId como a ClientId, {iothubhostname}/{deviceId} no campo Nome de Usuário e um token SAS no campo Senha. {iothubhostname} deve ser o CName completo do Hub IoT (por exemplo, contoso.azure-devices.net).

##### Exemplo: #####

Nome de usuário (DeviceId diferencia maiúsculas de minúsculas): `iothubname.azure-devices.net/DeviceId`

Senha (gerar SAS com o Gerenciador de Dispositivo): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] Os [SDKs do Hub IoT do Azure ][lnk-apis-sdks] geram tokens automaticamente durante a conexão com o serviço. Em alguns casos, os SDKs não dão suporte a todos os protocolos ou a todos os métodos de autenticação.

#### SASL PLAIN comparado ao CBS

Ao usar o SASL PLAIN, um cliente que está se conectando a um hub IoT pode usar um único token para cada conexão TCP. Quando o token expirar, a conexão TCP será desconectada do serviço, disparando uma reconexão. Esse comportamento, embora não seja problemático para um componente de back-end de aplicativo, é muito prejudicial para um aplicativo do lado do dispositivo, por estes motivos:

*  Gateways normalmente se conectam em nome de vários dispositivos. Ao usar SASL PLAIN, eles precisam criar uma conexão TCP distinta para cada dispositivo que se conecta a um hub IoT. Isso aumenta de forma considerável o consumo de energia e de recursos de rede, além de aumentar a latência de cada conexão de dispositivo.
* Os dispositivos com limitações de recursos serão afetados de forma adversa, principalmente pelo aumento do uso de recursos para se reconectar após cada expiração do token.

### Escopo das credenciais no nível do hub

Você pode definir o escopo das políticas de segurança no nível do hub por meio da criação de tokens com um URI de recursos restrito. Por exemplo, o ponto de extremidade para envio de mensagens do dispositivo para a nuvem é **/devices/{deviceId}/messages/events**. Você também pode usar uma política de acesso compartilhado no nível do hub com permissões **DeviceConnect** para assinar um token cujo resourceURI é **/devices/{deviceId}**, criando um token que só pode ser usado para enviar os dispositivos em nome da **deviceId** do dispositivo.

Esse mecanismo é parecido com a [política de editor de Hubs de Eventos][lnk-event-hubs-publisher-policy] e permite que você implemente os métodos de autenticação personalizados, como é explicado na seção de segurança [Projetar sua solução][lnk-guidance-security].

## Mensagens

O Hub IoT oferece primitivos de mensagens para comunicar:
- [Nuvem-para-dispositivo](#c2d): de um back-end de aplicativo (*serviço* ou *nuvem*).
- [Dispositivo-para-nuvem](#d2c): de um dispositivo para um back-end de aplicativo.

As propriedades básicas da funcionalidade de mensagens do Hub IoT são a confiabilidade e a durabilidade das mensagens. Isso permite a adaptação à conectividade intermitente no lado do dispositivo e a picos de carga no processamento de eventos no lado da nuvem. O Hub IoT implementa *pelo menos uma vez* as garantias de entrega de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo.

O Hub IoT dá suporte a vários protocolos voltados para o dispositivo (como MQTT, AMQP e HTTP). Para dar suporte à interoperabilidade contínua entre protocolos, o Hub IoT define um formato de mensagem comum com suporte de todos os protocolos voltados para o dispositivo.

### Formato da mensagem <a id="messageformat"></a>

As mensagens do Hub IoT são formadas por:

* Um conjunto de *propriedades do sistema*. São propriedades que o Hub IoT interpreta ou define. Esse conjunto é predeterminado.
* Um conjunto de *propriedades do aplicativo*. É um dicionário de propriedades de cadeia de caracteres que o aplicativo pode definir e acessar sem precisar desserializar o corpo. O Hub IoT nunca modifica essas propriedades.
* Um corpo de binário opaco.

Consulte [SDKs e APIs do Hub IoT][lnk-apis-sdks] para saber mais sobre como a mensagem é codificada em protocolos diferentes.

Esse é o conjunto de propriedades do sistema em mensagens do Hub IoT.

| Propriedade | Descrição |
| -------- | ----------- |
| MessageId | Um identificador configurável pelo usuário para a mensagem, normalmente usado para padrões de solicitação-resposta. Formato: uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de sequência | Um número (exclusivo por fila de dispositivos) atribuído pelo Hub IoT a cada mensagem da nuvem para o dispositivo. |
| Para | Usado em mensagens [da nuvem para o dispositivo](#c2d) para especificar o destino. |
| ExpiryTimeUtc | Data e hora de expiração da mensagem. |
| EnqueuedTime | Data e hora do recebimento da mensagem pelo Hub IoT. |
| CorrelationId | Cadeia de propriedade em uma mensagem de resposta que geralmente contém a MessageId da solicitação em padrões de solicitação-resposta. |
| UserId | Usada para especificar a origem das mensagens. Quando as mensagens são geradas pelo Hub IoT, são definidas como `{iot hub name}`. |
| Ack | Usado em mensagens da nuvem para o dispositivo para solicitar ao Hub IoT a geração de mensagens de comentários como resultado do consumo da mensagem pelo dispositivo. Valores possíveis: **nenhum** (padrão): nenhuma mensagem de comentários é gerada, **positivo**: ocorre o recebimento de uma mensagem de comentários, se a mensagem estiver completa, **negativo**: ocorre o recebimento de uma mensagem de comentários, se a mensagem expirar (ou se a contagem máxima de entrega tiver sido atingida) sem ser concluída pelo dispositivo, **total**: positivos e negativos. Para saber mais, consulte [Comentários sobre a mensagem](#feedback). |
| ConnectionDeviceId | Definida pelo Hub IoT em mensagens do dispositivo para nuvem . Contém a **deviceId** do dispositivo que enviou a mensagem. |
| ConnectionDeviceGenerationId | Definida pelo Hub IoT em mensagens do dispositivo para nuvem . Contém a **generationId** (de acordo com as [Propriedades de identidade de dispositivo](#deviceproperties)) do dispositivo que enviou a mensagem. |
| ConnectionAuthMethod | Definida pelo Hub IoT em mensagens do dispositivo para nuvem . Informações sobre o método de autenticação usado para autenticar o dispositivo que envia a mensagem. Para saber mais, confira [Antifalsificação do dispositivo para a nuvem](#antispoofing).|

### Escolhendo seu protocolo de comunicação <a id="amqpvshttp"></a>

O Hub IoT dá suporte a [AMQP][lnk-amqp], AMQP sobre protocolos WebSockets, MQTT e HTTP/1 para comunicações do lado do dispositivo. A seguir, uma lista de considerações sobre suas utilizações.

* **Padrão da nuvem para o dispositivo**. O HTTP/1 não tem uma maneira eficiente de implementar o envio do servidor. Assim, ao usarem HTTP/1, os dispositivos sondam o Hub IoT em busca de mensagens da nuvem para o dispositivo. Isso é muito ineficiente para o dispositivo e para o Hub IoT. As diretrizes atuais usam HTTP/1 para que cada dispositivo seja sondado a cada 25 minutos ou mais. Por outro lado, AMQP e MQTT permitem o envio por push do servidor ao receber mensagens da nuvem para o dispositivo, e isso habilita imediatamente os envios por push de mensagens do Hub IoT para o dispositivo. Se a latência de entrega for uma preocupação, AMQP ou MQTT será a melhor opção de protocolo. Por outro lado, para dispositivos conectados raramente, o HTTP/1 funciona bem.
* **Gateways de campo**. Ao usar HTTP/1 e MQTT, você não pode conectar vários dispositivos (cada um com suas próprias credenciais por dispositivo) usando a mesma conexão TLS. Consequentemente, esses protocolos ficam abaixo do ideal ao implementar [cenários de gateway de campo][lnk-azure-gateway-guidance], pois exigem uma conexão TLS entre o gateway de campo e o Hub IoT para cada dispositivo conectado ao gateway de campo.
* **Dispositivos com poucos recursos**. As bibliotecas de MQTT e HTTP/1 têm uma superfície menor que as bibliotecas de AMQP. Dessa forma, caso o dispositivo tenha poucos recursos (por exemplo, menos de 1 MB de RAM), esses protocolos poderão ser a única implementação de protocolo disponível.
* **Percurso da rede**. O padrão MQTT escuta na porta 8883. Isso pode causar problemas em redes fechadas para protocolos diferentes de HTTP. HTTP e AMQP (sobre WebSockets) estão disponíveis para serem usados nesse cenário.
* **Tamanho da carga**. AMQP e MQTT são protocolos binários, que são significativamente mais compactos que HTTP/1.

Em um alto nível, você deve usar o AMQP (ou AMQP sobre WebSockets) sempre que possível e usar MQTT apenas quando as restrições do recurso impedirem o uso de AMQP. HTTP/1 deverá ser usado somente se a configuração de rede e a passagem de rede impedirem o uso de MQTT e AMQP. Além disso, ao usar HTTP/1, cada dispositivo deve sondar se há mensagens da nuvem para o dispositivo a cada 25 minutos ou mais.

> [AZURE.NOTE] Claramente, durante o desenvolvimento, é aceitável sondar com mais frequência do que a cada 25 minutos.

<a id="mqtt-support">
#### Observações sobre o suporte ao MQTT
O Hub IoT implementa o protocolo MQTT v3.1.1 com as seguintes limitações e comportamento específico:

  * **Não há suporte para o QoS 2**: quando um cliente de dispositivo publica uma mensagem com o **QoS 2**, o Hub IoT fecha a conexão de rede. Quando um cliente de dispositivo assina um tópico com o **QoS 2**, o Hub IoT concede, no máximo, o nível 1 do QoS no pacote **SUBACK**.
  * **Retain**: se um dispositivo publica uma mensagem com o sinalizador RETAIN definido como 1, o Hub IoT adiciona a propriedade de aplicativo **x-opt-retain** à mensagem. Isso significa que o Hub IoT não mantém a mensagem retain, mas a transmite ao aplicativo back-end.

Como uma consideração final, você deve examinar o [Gateway de protocolo do IoT do Azure][lnk-azure-protocol-gateway], que o habilita a implantar um gateway de protocolo personalizado de alto desempenho que interage diretamente com o Hub IoT. O gateway do protocolo IoT do Azure permite que você personalize o protocolo de dispositivo para acomodar as implantações de MQTT de nível industrial ou outros protocolos personalizados. O dilema dessa abordagem é o requisito de auto-hospedar e operar um gateway de protocolo personalizado.

### Dispositivo para a nuvem <a id="d2c"></a>

Conforme detalhado na seção [Pontos de extremidade](#endpoints), as mensagens do dispositivo para a nuvem são enviadas por meio de um ponto de extremidade voltado para o dispositivo (**/devices/{deviceId}/messages/events**) e recebidas por meio de um ponto de extremidade voltado para o serviço (**/messages/events**), o que é compatível com os [Hubs de Eventos][lnk-event-hubs]. Portanto, você pode usar a integração de Hubs de Eventos Standard e os SDKs para receber mensagens do dispositivo para a nuvem.

O Hub IoT implementa o envio de mensagens do dispositivo para a nuvem de uma maneira semelhante aos [Hubs de Eventos][lnk-event-hubs], com as mensagens do dispositivo para a nuvem do Hub IoT sendo mais parecidas com os *eventos* dos Hubs de Eventos do que com as *mensagens* do [Barramento de Serviço][lnk-servicebus].

Esta implementação tem as seguintes implicações:

* Assim como nos *eventos* dos Hubs de Eventos, as mensagens do dispositivo para a nuvem são duráveis e mantidas em um hub IoT por sete dias (consulte [Opções de configuração de dispositivo para a nuvem](#d2cconfiguration)).
* As mensagens do dispositivo para a nuvem são particionadas em um conjunto fixo de partições definido no momento da criação (confira [Opções de configuração do dispositivo para a nuvem](#d2cconfiguration)).
* Assim como nos Hubs de Eventos, os clientes que leem mensagens do dispositivo para a nuvem devem lidar com partições e pontos de verificação. Consulte [Hubs de Eventos - consumindo eventos][lnk-event-hubs-consuming-events].
* Assim como os eventos dos Hubs de Eventos, as mensagens do dispositivo para a nuvem podem ter no máximo 256 Kb e podem ser agrupadas em lotes para otimizar os envios. Os lotes podem ter no máximo 256 Kb e no máximo 500 mensagens.

No entanto, há algumas distinções importantes entre as mensagens do dispositivo para a nuvem do Hub IoT e os Hubs de Eventos:

* Como explicado na seção [Segurança](#security), o Hub IoT permite a autenticação e o controle de acesso por dispositivo,
* O Hub IoT permite que milhões de dispositivos se conectem ao mesmo tempo (consulte [Cotas e limitação](#throttling)), enquanto os Hubs de Eventos estão limitados a 5000 conexões AMQP por namespace.
* O Hub IoT não permite o particionamento arbitrário usando uma **PartitionKey**. As mensagens do dispositivo para a nuvem são particionadas com base em sua **deviceId** de origem.
* O dimensionamento do Hub IoT é um pouco diferente dos Hubs de Eventos. Para saber mais, consulte [Dimensionando o Hub IoT][lnk-guidance-scale].

Observe que isso não significa que você pode substituir o Hub IoT para os Hubs de Eventos em todos os cenários. Por exemplo, em alguns cálculos de processamento de eventos, talvez seja necessário fazer a repartição de eventos com relação a um campo ou propriedade diferente antes de analisar os fluxos de dados. Neste cenário, você poderia usar um Hub de Eventos para desacoplar duas partes do pipeline de processamento do fluxo. Para obter mais informações, confira *Partições* na [Visão Geral dos Hubs de Eventos][lnk-eventhub-partitions].

Para obter detalhes sobre como usar as mensagens do dispositivo para a nuvem, consulte [SDKs e APIs do Hub IoT][lnk-apis-sdks].

> [AZURE.NOTE] Ao se usar HTTP para enviar mensagens de dispositivo para a nuvem, valores e nomes de propriedade só podem conter caracteres alfanuméricos ASCII mais ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

#### Tráfego sem telemetria

Em muitos casos, além dos pontos de dados de telemetria, os dispositivos também enviam mensagens e solicitações que exigem a execução e a manipulação da camada de lógica de negócios do aplicativo. Por exemplo, os alertas críticos que devem disparar uma ação específica no back-end ou as respostas de dispositivo a comandos enviados do back-end.

Confira [Processamento do dispositivo para a nuvem][lnk-guidance-d2c-processing] para saber mais sobre a melhor maneira de processar esses tipos de mensagem.

#### Opções de configuração do dispositivo para a nuvem <a id="d2cconfiguration"></a>

Um Hub IoT expõe as propriedades a seguir para permitir que você controle as mensagens do dispositivo para a nuvem.

* **Contagem de partição**. Defina essa propriedade no momento da criação e define o número de partições para inclusão do evento do dispositivo para a nuvem.
* **Período de retenção**. Esta propriedade especifica o período de retenção das mensagens do dispositivo para a nuvem. O padrão é de um dia, mas pode ser aumentado para sete dias.

Além disso, de maneira semelhante aos Hubs de Eventos, o Hub IoT permite o gerenciamento de Grupos de Consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem.

Você pode modificar todas essas propriedades usando o [portal do Azure][lnk-management-portal] ou programaticamente por meio do [Hub IoT do Azure - APIs do provedor de recursos][lnk-resource-provider-apis].

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

Conforme detalhado na seção [Pontos de extremidade](#endpoints), você pode enviar mensagens da nuvem para o dispositivo por meio de um ponto de extremidade voltado para o serviço (**/messages/devicebound**) e um dispositivo pode recebê-las por meio de um ponto de extremidade específico do dispositivo (**/devices/{deviceId}/messages/devicebound**).

Cada mensagem da nuvem para o dispositivo é direcionada a um único dispositivo, definindo a propriedade **to** como **/devices/{deviceId}/messages/devicebound**.

**Importante**: cada fila de dispositivo pode conter no máximo 50 mensagens da nuvem para o dispositivo. Tentar enviar mais mensagens ao mesmo dispositivo resultará em um erro.

> [AZURE.NOTE] Ao se enviar mensagens de nuvem para dispositivos, valores e nomes de propriedade só podem conter caracteres alfanuméricos ASCII mais ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

#### Ciclo de vida da mensagem <a id="message lifecycle"></a>

Para implementar *pelo menos uma vez* a garantia de entrega, as mensagens da nuvem para o dispositivo são mantidas em filas por dispositivo e os dispositivos devem reconhecer explicitamente a *conclusão* para que o Hub IoT os remova da fila. Isso garante a resiliência contra falhas de conectividade e do dispositivo.

O diagrama a seguir mostra o gráfico de estado do ciclo de vida de uma mensagem da nuvem para o dispositivo.

![Ciclo de vida de mensagens da nuvem para o dispositivo][img-lifecycle]

Quando o serviço envia uma mensagem, ela é considerada *Enfileirada*. Quando um dispositivo deseja *receber* uma mensagem, o Hub IoT *bloqueia* a mensagem (define o estado como **Invisível**) para permitir que outros threads no mesmo dispositivo comecem a receber outras mensagens. Quando um thread de dispositivo conclui o processamento de uma mensagem, ele notifica o Hub IoT ao *concluir* a mensagem.

Um dispositivo também pode:
- *Rejeitar* a mensagem, o que faz com que o Hub IoT o defina no estado **Morto**.
- *Abandonar* a mensagem, o que faz com que o Hub IoT coloque a mensagem de volta na fila com o estado definido como **Em fila**.

Um thread pode falhar ao processar uma mensagem sem notificar o Hub IoT. Nesse caso, as mensagens passam automaticamente do estado **Invisível** de volta para o estado **Enfileirada** após um *tempo limite de visibilidade (ou de bloqueio)* com um valor padrão de um minuto. Uma mensagem pode transitar entre os estados **Enfileirada** e **Invisível** pelo número máximo de vezes especificado na propriedade *contagem máxima de entrega* no Hub IoT. Após esse número de transições, o Hub IoT definirá o estado da mensagem como **Morto**. Da mesma forma, o Hub IoT define o estado de uma mensagem como **Morto** após o tempo de expiração (confira [Vida útil](#ttl)).

Para obter um tutorial sobre mensagens da nuvem para o dispositivo, consulte [Introdução às mensagens da nuvem para o dispositivo do Hub IoT do Azure][lnk-getstarted-c2d-tutorial]. Para obter tópicos de referência sobre como as APIs e os SDKs diferentes expõem a funcionalidade de nuvem para o dispositivo, consulte [APIs e SDKs do Hub IoT][lnk-apis-sdks].

> [AZURE.NOTE] Normalmente, as mensagens da nuvem para o dispositivo serão concluídas sempre que a perda da mensagem não afetar a lógica do aplicativo. Isso pode ocorrer em vários cenários diferentes. Por exemplo, o conteúdo da mensagem foi mantido com êxito no armazenamento local, ou uma operação foi executada com êxito, ou a mensagem está carregando informações temporárias cuja perda não afeta a funcionalidade do aplicativo. Às vezes, para tarefas de execução longa, você pode concluir a mensagem da nuvem para o dispositivo depois de persistir a descrição da tarefa no armazenamento local e notificar o back-end de aplicativo com uma ou mais mensagens do dispositivo para a nuvem em vários estágios do andamento da tarefa.

#### Vida útil <a id="ttl"></a>

Todas as mensagens da nuvem para o dispositivo têm um tempo de expiração. Isso pode ser definido explicitamente pelo serviço (na propriedade **ExpiryTimeUtc**) ou pelo Hub IoT usando o *tempo de vida* padrão especificado como uma propriedade do Hub IoT. Consulte [Opções de configuração da nuvem para o dispositivo](#c2dconfiguration).

> [AZURE.NOTE] Uma maneira comum de tirar proveito da expiração da mensagem é definir valores baixos de vida útil para evitar o envio de mensagens para dispositivos desconectados. Isso proporciona o mesmo resultado que a manutenção do estado de conexão do dispositivo, embora seja significativamente mais eficiente. Também é possível, por meio da solicitação de confirmações de mensagens, receber uma notificação pelo Hub IoT de quais dispositivos são capazes de receber mensagens e quais não estão online ou apresentam falha.

#### Comentários da mensagem <a id="feedback"></a>

Quando você envia uma mensagem da nuvem para o dispositivo, o serviço pode solicitar a entrega de um comentário por mensagem sobre o estado final dessa mensagem.

- Se você definir a propriedade **Ack** como **positivo**, o Hub IoT gerará uma mensagem de comentários se, e somente se, a mensagem da nuvem para o dispositivo atingir o estado **Concluído**.
- Se você definir a propriedade **Ack** como **negativo**, o Hub IoT gerará uma mensagem de comentários se, e somente se, a mensagem da nuvem para o dispositivo atingir o estado **Morto**.
- Se você definir a propriedade **Ack** como **total**, o Hub IoT gerará uma mensagem de comentários em ambos os casos.

> [AZURE.NOTE] Se **Ack** for **total** e não for recebida nenhuma mensagem de comentários, isso indicará que a mensagem de comentários expirou e o serviço não poderá saber o que aconteceu com a mensagem original. Na prática, um serviço deve garantir que possa processar os comentários antes que eles expirem. O tempo máximo de validade é de dois dias, portanto, deverá haver tempo suficiente para executar o serviço de backup se ocorrer uma falha.

Como explicado em [Pontos de extremidade](#endpoints), o Hub IoT oferece comentários por meio de um ponto de extremidade voltado para o serviço (**/messages/servicebound/feedback**) como mensagens. A semântica de recebimento dos comentários é a mesma das mensagens da nuvem para o dispositivo e tem o mesmo [ciclo de vida da mensagem](nº do ciclo de vida da mensagem). Sempre que possível, os comentários de mensagem são feitos em lotes em uma única mensagem, com o seguinte formato.

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


**Importante**. O serviço deve especificar um **MessageId** para a mensagem da nuvem para o dispositivo, a fim de poder correlacionar seus comentários com a mensagem original.

**Exemplo**. Este é um exemplo de corpo de uma mensagem de comentários.

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

Cada Hub IoT expõe as seguintes opções de configuração para mensagens da nuvem para o dispositivo:

| Propriedade | Descrição | Intervalo e padrão |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL padrão para mensagens da nuvem para o dispositivo. | Intervalo ISO\_8601 de até 2D (mínimo de 1 minuto). Padrão: 1 hora. |
| maxDeliveryCount | Contagem máxima de entrega para filas de nuvem para o dispositivo por dispositivo. | 1 a 100. Padrão: 10. |
| feedback.ttlAsIso8601 | Retenção de mensagens informativas do serviço associado. | Intervalo ISO\_8601 de até 2D (mínimo de 1 minuto). Padrão: 1 hora. |
| feedback.maxDeliveryCount | Contagem máxima de entrega para a fila de comentários. | 1 a 100. Padrão: 100. |

Para obter mais informações, confira [Gerenciar hubs IoT][lnk-manage].

## Cotas e limitação <a id="throttling"></a>

Cada assinatura do Azure pode ter no máximo 10 hubs IoT.

Cada hub IoT é provisionado com um determinado número de unidades em um SKU específico (para obter mais informações, veja [Preços do Hub IoT do Azure][lnk-pricing]). O SKU e o número de unidades determinam a cota máxima diária de mensagens que você pode enviar.

O SKU também determina os limites impostos pelo Hub IoT em todas as operações.

### Restrições de operação

As restrições de operação são limites de taxa aplicados em intervalos de minutos, e têm como objetivo evitar o abuso. O Hub IoT tenta evitar o retorno de erros sempre que possível, mas ele começará a retornar exceções se a restrição for violada por muito tempo.

A seguir, a lista de limitações impostas. Os valores referem-se a um hub individual.

| Restrição | Valor por hub |
| -------- | ------------- |
| Operações de registro de identidade (criar, recuperar, listar, atualizar, excluir) | 100/min/unidade, até 5000/min |
| Conexões do dispositivo | 120/s/unidade (para S2), 12/s/unidade (para S1). <br/>Mínimo de 100/s. <br/> Por exemplo, duas unidades de S1 têm 2 * 12 = 24/s, mas você terá pelo menos 100/s em suas unidades. Com nove unidades S1 você tem 108/s (9 * 12) em suas unidades. |
| Envios do dispositivo para a nuvem | 120/s/unidade (para S2), 12/s/unidade (para S1). <br/>Mínimo de 100/s. <br/> Por exemplo, duas unidades de S1 têm 2 * 12 = 24/s, mas você terá pelo menos 100/s em suas unidades. Com nove unidades S1 você tem 108/s (9 * 12) em suas unidades. |
| Envios da nuvem para o dispositivo | 100/min/unidade |
| Recebimentos da nuvem para o dispositivo | 1000/min/unidade |

É importante esclarecer que a restrição de *conexões de dispositivo* controla a taxa à qual novas conexões de dispositivo podem ser estabelecidas com um hub IoT, e não o número máximo de dispositivos conectados ao mesmo tempo. A restrição depende do número de unidades provisionadas para o hub.

Por exemplo, se você comprar uma única unidade de S1, obterá uma restrição de 100 conexões por segundo. Isso significa que serão necessários pelo menos 1000 segundos (aproximadamente 16 minutos) para conectar 100.000 dispositivos. No entanto, você pode conectar ao mesmo tempo todos os seus dispositivos registrados no registro de identidade do dispositivo.


**Observação**. A qualquer momento, é possível aumentar as cotas ou restrições aumentando o número de unidades provisionadas em um Hub IoT.

**Importante**: as operações de Registro de identidade são destinadas a uso no tempo de execução em cenários de provisionamento e gerenciamento de dispositivos. Há suporte para ler ou atualizar grandes números de identidades de dispositivo por meio de [trabalhos de importação/exportação](#importexport).

## Próximas etapas

Você viu uma visão geral do desenvolvimento para Hub IoT. Siga estes links para saber mais:

- [Introdução aos Hubs IoT (tutorial)][lnk-get-started]
- [Compatibilidade de plataformas de SO e hardware][lnk-compatibility]
- [Centro de Desenvolvedores do IoT do Azure][lnk-iotdev]
- [Projetar sua solução][lnk-guidance]

[Hubs de Eventos - Host Processador de Eventos]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[Portal do Azure]: https://portal.azure.com

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-manage]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0413_2016-->