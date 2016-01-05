<properties
 pageTitle="Gerenciamento de dispositivos IoT | Microsoft Azure"
 description="Uma visão geral de uso Hub IoT e o Pacote IoT para gerenciar os seus dispositivos IoT"
 services="iot-hub,iot-suite"
 documentationCenter=""
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/10/2015"
 ms.author="sethm"/>

# Gerenciamento de dispositivos IoT usando o Pacote IoT do Azure e o Hub IoT do Azure

O Azure IoT Suite e o Hub IoT do Azure fornecem os recursos básicos que permitem o gerenciamento de dispositivos para as soluções de IoT, em larga escala, e para um conjunto diversificado de dispositivos e topologias de dispositivo. As referências ao gerenciamento de dispositivos neste artigo estão especificamente relacionadas ao gerenciamento de dispositivos IoT.

## Introdução

Os provedores de serviço e as empresas, ou qualquer organização que mantenha uma população de dispositivos IoT, usam recursos de gerenciamento de dispositivos para realizar os seguintes processos de negócios:

* Inscreva e descubra dispositivos IoT.
* Habilite a conectividade.
* Configure e atualize remotamente o software em dispositivos. Por exemplo, ao gerenciar dispositivos em fábricas ou campos petrolíferos, há políticas para configurar e atualizar dispositivos remotamente, com cadeias de aprovação, requisitos normativos de auditoria, presença de proteções físicas e muito mais.

Ao habilitar o gerenciamento de dispositivos IoT para sua solução IoT, leve em consideração os seguintes recursos e determine a importância de cada um de acordo com os seus objetivos de negócios:

* **[Provisionamento e descoberta de dispositivo](#device-provisioning-and-discovery)**: o processo pelo qual um dispositivo é registrado no sistema.
* **[Registro do dispositivo e modelos de dispositivo](#device-registry-and-device-models)**: a forma como os modelos de dispositivo representam o uso estruturado de metadados para relações de dispositivo, função no sistema e métodos de validação.
* **[Gerenciamento de acesso ao dispositivo](#device-access-management)**: a forma como os dispositivos controlam o acesso aos recursos do dispositivo dos serviços de nuvem.
* **[Controle remoto](#remote-control)**: como os usuários ganham acesso aos dispositivos e orientam os dispositivos para alterações.
* **[Administração remota](#remote-administration-and-monitoring)**: processo pelo qual um administrador define a integridade a população do dispositivo.  
* **[Configuração remota](#remote-configuration)**: método pelo qual os administradores alteram a configuração do dispositivo.
* **[Atualização remota de firmware e software](#remote-firmware-and-software-update)**: processo pelo qual os administradores podem atualizar o firmware e o software do dispositivo.

As seções a seguir fornecem uma perspectiva detalhada em cada um desses recursos de gerenciamento de dispositivos, bem como um modelo de alto nível para implementar esses recursos usando o Hub IoT do Azure.

## Provisionamento e descoberta de dispositivos

Você provisiona um dispositivo com o Hub IoT do Azure usando a API de registro. Depois de registrar o dispositivo e fornecer ou receber uma chave, você poderá habilitar o dispositivo para se conectar ao Hub IoT usando essa chave. O Hub IoT do Azure se comunica apenas com dispositivos registrados que apresentam credenciais autorizadas. Os administradores podem desabilitar o acesso de dispositivos ao Hub IoT do Azure por meio do portal de administração do dispositivo.

Um processo de inicialização pode ser usado dependendo de como os dispositivos IoT são fabricados, provisionados e implantados. Você pode compilar um serviço de inicialização como parte de sua solução para fornecer conectividade simples e atrasar o processo de atribuição de um dispositivo a um Hub IoT específico. O local em que o dispositivo operará pode ser desconhecido no momento em que o dispositivo é fabricado. Esse é apenas um exemplo de um grande número de fluxos de trabalho potencialmente complexos que ajudam a tornar um dispositivo conhecido para o Hub IoT do Azure e que também é integrado aos processos de negócios existentes.

Ao usar um serviço de inicialização, um dispositivo IoT é inicializado e emite uma solicitação, o que pode acabar fornecendo acesso a um Hub IoT do Azure atribuído. A solicitação deve incluir credenciais de inicialização do dispositivo e todos os outros dados necessários. Para dispositivos autorizados, o serviço de inicialização deve registrar o dispositivo em um Hub IoT do Azure atribuído e fornecer detalhes de conectividade ao dispositivo que está solicitando uma inicialização. O Hub IoT fornece detalhes da conectividade ao dispositivo que está solicitando uma inicialização.

## Registro de dispositivo e modelos de dispositivo

O uso do termo *modelo do dispositivo* refere-se ao modelo que inclui as propriedades de dispositivo que podem ser lidas ou gravadas por um serviço de nuvem. Ele também inclui comandos de dispositivo que podem ser executados remotamente por um serviço de nuvem. No modelo orientado ao serviço, descrito na próxima seção, o dispositivo não tem conhecimento do modelo, mas os serviços de nuvem ainda podem acompanhar e usar metadados para dispositivos.

Armazenar as informações do dispositivo e os metadados associados é essencial para o sistema IoT e a função do registro de dispositivo. Isso é especialmente verdadeiro para dispositivos herdados que não podem ser alterados ou para dispositivos que não usam um modelo de dispositivo. Um modelo orientado a serviço pode permitir que um serviço IoT interaja com uma população de dispositivos. Quando os dispositivos funcionam de acordo com um modelo definido, o registro do dispositivo atua como uma exibição consistente dos dados do dispositivo, em que o dispositivo atende ao mestre. Nesse caso, o serviço informa ao dispositivo sobre as alterações desejadas, que serão válidas apenas depois que o dispositivo confirmar a alteração.

### Modelo orientado ao serviço

Se um dispositivo se conectar ao Hub IoT do Azure e não fornecer um modelo de dispositivo, é importante implementar um registro de dispositivo que acompanhe os dispositivos registrados e seus metadados associados. Nesse caso, o Registro do dispositivo é o único local de armazenamento dos metadados do dispositivo. Alguns exemplos de metadados de dispositivo que talvez você queira acompanhar são: a topologia lógica (por exemplo, 4º andar, edifício 109), a função do dispositivo (por exemplo, sensor da entrada) ou qualquer outra informação de identificação.

### Modelo orientado ao dispositivo

Para habilitar sua solução de IoT para aproveitar os recursos do dispositivo IoT, os dispositivos devem se descrever na nuvem depois de se conectarem ao Hub IoT. Veja a seguir os dois tipos de modelo de dispositivo que podem ser usados em uma solução IoT:

#### Modelo de dispositivo autodefinido

Um engenheiro de dispositivo (ou desenvolvedor usando um simulador de dispositivos) usa o modelo de dispositivo autodefinido ao iterar em recursos do dispositivo à medida que ele é compilado. Um engenheiro de dispositivo pode começar criando um dispositivo que tem poucas propriedades e comandos com suporte e, posteriormente, adicionar mais. Da mesma forma, o engenheiro do dispositivo pode ter muitos dispositivos, cada um deles oferece recursos exclusivos e usa o modelo autodefinido. Nesse caso, o engenheiro de dispositivo não é obrigado a registrar a estrutura do modelo de dispositivo. Uma variação significativa nos modelos autodefinidos pode apresentar desafios no dimensionamento para um número maior de dispositivos.

#### Modelo de dispositivo predefinido

Uma implantação IoT de produção que opera com restrições de rede e energia ou processamento aproveita de maneira significativa um modelo de dispositivo predefinido, em que há um uso mínimo de processamento e consumo de energia do dispositivo. Da mesma forma, o tráfego de rede mínimo permite que dispositivos transmitam dados por redes heterogêneas (Wi-Fi, 2G/3G/4G, BLE, Sat, etc.) especialmente ao usar a infraestrutura limitada e cara (por exemplo, satélite). Ao implementar um modelo de dispositivo predefinido, um engenheiro de dispositivo pode enviar informações de dispositivo codificadas em um ou dois bytes que atuam como uma chave no modelo de dispositivo predefinido. A brevidade dessa abordagem resulta em mais ganhos de eficiência de uma a duas ordens de grandeza quando comparado ao modelo de dispositivo autodefinido.

### A solução pré-configurada para monitoramento remoto e seu modelo de dispositivo

A solução pré-configurada para Monitoramento Remoto do Pacote IoT do Azure implementa um modelo de dispositivo autodefinido. Usar esse modelo permite iteração rápida enquanto você define e aprimora os recursos do dispositivo.

O código em que a solução pré-configurada de monitoramento remoto cria um objeto de dispositivo e o envia ao serviço é definido em **Simulator/Simulator.WorkerRole/SimulatorCore/Devices/DeviceBase.cs**. Analisando os métodos `SendDeviceInfo` e `GetDeviceInfo`, você pode ver como o modelo de dispositivo descrito automaticamente é criado e enviado ao Hub IoT do Azure.

O código em que o serviço de nuvem processa os eventos relacionados ao modelo de dispositivo está localizado em **EventProcessor/EventProcessor.WorkerRole/Processors/DeviceManagementProcessor.cs**. Grande parte do trabalho envolvido com a atuação em mensagens relacionadas ao modelo de dispositivo enviadas ao lado do serviço da solução pré-configurada ocorre no método `ProcessJToken`.

Assim que uma mensagem relacionada ao modelo de dispositivo é recebida, os métodos `UpdateDeviceFromSimulatedDeviceInfoPacketAsync` e `UpdateDeviceFromRealDeviceInfoPacketAsync` em **DeviceManagement/Infrastructure/BusinessLogic/DeviceLogic.cs** são responsáveis por atualizar o registro do dispositivo. As APIs de registro do dispositivo na solução pré-configurada de monitoramento remoto podem ser encontradas em **DeviceManagement/Web/WebApiControllers/DeviceApiController.cs**.

### Modelos de dispositivo de gateway de campo

Os gateways de campo muitas vezes são usados para permitir conectividade e conversão de protocolo para dispositivos que não podem ou não devem ser conectados diretamente à Internet. Se o dispositivo que você está criando for um gateway de campo, ele poderá representar os dispositivos que são conectados por meio do gateway de campo em todas as interações com o Hub IoT do Azure. Como o fabricante do gateway de campo, é sua responsabilidade implementar a conversão entre os protocolos de dispositivo e os protocolos com suporte do serviço IoT. Se você deseja permitir que o gateway de campo se conecte a dispositivos BLE (Bluetooth de Baixa Energia), você deverá implementar a interface BLE nos dispositivos e a interface no Hub IoT do Azure.

## Gerenciamento do acesso ao dispositivo

A solução pré-configurada do IoT Suite controla o acesso a diferentes aspectos do dispositivo, incluindo direitos de acesso de leitura/gravação para propriedades do dispositivo e direitos de execução para comandos do dispositivo. Nas soluções pré-configuradas, esse acesso é controlado por meio do uso do AAD (Active Directory do Azure) no portal de administração do dispositivo. Você pode habilitar a segurança de acesso baseada em função estendendo o uso do AAD na solução pré-configurada.

## Controle remoto

O controle remoto está fora do escopo como um cenário para soluções pré-configuradas do Pacote IoT do Azure. No entanto, apresentamos a seguir uma análise de alto nível das opções para habilitar o controle remoto em sua solução de IoT.

Em cenários de TI, o controle remoto muitas vezes é usado para auxiliar usuários remotos ou configurar remotamente servidores remotos. Em cenários de IoT, a maioria dos dispositivos não envolve usuários, portanto, o controle remoto é usado em cenários de diagnóstico e configuração remota. O controle remoto pode ser implementado usando dois modelos diferentes:

* **Conexão direta** Para habilitar o controle remoto por meio de uma conexão direta com um dispositivo (por exemplo, SSH no Linux, Área de Trabalho Remota no Windows ou por meio de ferramentas de depuração remota), você deve conseguir criar uma conexão com o dispositivo. Devido ao risco de segurança de expor um dispositivo à Internet aberta, é recomendável usar um serviço de retransmissão (como o [serviço de retransmissão do Barramento de Serviço do Azure](service-bus-relay-overview.md)) para habilitar a conexão e o tráfego bidirecional do dispositivo. Como uma conexão de retransmissão é uma conexão de saída do dispositivo, ela ajuda a limitar a superfície de ataque das portas TCP abertas no dispositivo.

* **Comando do dispositivo** O controle remoto por meio do comando do dispositivo utiliza a conexão e o canal de comunicação existentes estabelecidos entre o dispositivo e o Hub IoT do Azure. Para habilitar o controle remoto baseado no comando do dispositivo, você deve atender aos seguintes requisitos:
  * O software que é executado no dispositivo deve informar o serviço IoT que os comandos do dispositivo estão disponíveis no dispositivo. Geralmente, isso é definido como parte do modelo do dispositivo.
  * O software que é executado no dispositivo deve implementar os comandos do controle remoto. Esses comandos de dispositivo devem seguir um padrão de solicitação (do serviço IoT para o dispositivo) e de resposta (do dispositivo para o serviço IoT). Executar comandos do dispositivo pode afetar o estado do dispositivo, e esse novo estado precisará ser atualizado no registro do dispositivo.

Quando o dispositivo for o repositório mestre da configuração e do estado do dispositivo, o registro do dispositivo, por fim, usará um modelo consistente e alternará para o estado do dispositivo necessário para ser enviado por push ao registro de dispositivo. A atualização do estado do registro de dispositivo pode ser forçada por uma solicitação do serviço IoT para o dispositivo, ou o dispositivo pode atualizar automaticamente o serviço, após o reconhecimento de uma alteração no estado do sistema. A atualização automática do serviço no dispositivo pode gerar tráfego intenso de rede e aumentar o uso do processador do dispositivo e da energia disponível.

## Administração e monitoramento remoto

Uma vez que a maioria dos dispositivos IoT diferencia funções de gerenciamento e operacionais de usuário, a administração remota é o método pelo qual os administradores podem monitorar o estado de seus dispositivos, configurar o fluxo de dados dos dispositivos em aplicativos e processos de negócios (por exemplo, CRM, ERP, sistemas de manutenção, etc.) e atualizar remotamente o estado ou a configuração dos dispositivos por meio do uso de comandos do dispositivo.

As soluções pré-configuradas do Azure IoT Suite fornecem um site do Azure que cria um ponto de partida para uma experiência de administração de dispositivos que você pode estender aos cenários em sua solução IoT vertical.

Os administradores definem os limites de integridade de dispositivo como uma função de dados operacionais de dispositivo (geralmente de movimentação rápida) e metadados de dispositivo (geralmente de movimentação lenta). Você pode usar regras para habilitar o sistema de alerta de integridade de dispositivo, implementadas por meio de um mecanismo de processamento de transmissão (como o [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/)), em vez de uma estratégia de sondagem.

## Configuração remota

Alterar remotamente a configuração de um dispositivo pode ser importante para várias fases no ciclo de vida do dispositivo: provisionamento, diagnóstico ou integração a processos de negócios. As alterações na configuração remota são habilitadas por meio de uma combinação do modelo do dispositivo e pela capacidade do serviço IoT de atualizar remotamente o estado da instância do modelo de um dispositivo.

Na solução pré-configurada de monitoramento remoto, o dispositivo descreve o fato de que ele dá suporte aos seguintes comandos para a configuração remota:

* ChangeKey
* ChangeConfig
* ChangeSystemProperties

Esses comandos de dispositivo não aparecem como comandos de dispositivo disponíveis no portal de administração do dispositivo da solução pré-configurada, uma vez que são comandos de dispositivo e são executados remotamente por partes específicas do portal. Assim que um comando de dispositivo é recebido pelo dispositivo, uma resposta de confirmação é enviada do dispositivo para o serviço. Depois de processar o comando do dispositivo, o dispositivo envia uma resposta de resultado informando que o serviço que está executando o comando do dispositivo foi bem-sucedido (ou que ele falhou, com um código de erro). Nesse ponto, o estado desejado para o dispositivo é confirmado no dispositivo e o registro de dispositivo é atualizado.

## Atualização remota de firmware e software

As atualizações remotas de firmware e software são um recurso importante de um sistema IoT. As atualizações permitem que os dispositivos executem software mais recenge e mais seguro. A atualização remota de firmware e software em um dispositivo é um exemplo de um processo distribuído de execução longa que normalmente envolve processos de negócios. Por exemplo, atualizar o firmware em um dispositivo que controla uma bomba de combustível altamente potente pode exigir etapas em sistemas adjacentes para redirecionar o combustível enquanto a atualização é realizada e verificada.

### Uma análise das atualizações de firmware

O exemplo a seguir é uma maneira possível de implementar a atualização de firmware que pode se ajustar às suas necessidades de negócios. O objetivo desse exemplo é descrever as considerações do processo, não impor uma implementação específica. Projetar seus requisitos de atualização para considerar vários fatores técnicos e de negócios está fora do escopo deste artigo.

As atualizações de dispositivo são iniciadas no serviço IoT, e os dispositivos são informados em uma determinada hora por meio de um comando de dispositivo. Quando um dispositivo dá suporte explicitamente à atualização remota de firmware ou software, o serviço de IoT deve fornecer os comandos de atualização com base nas políticas e no processo de negócios definidos. Ao receber o comando do dispositivo para atualização, o dispositivo deverá baixar e implantar o pacote de atualização, ser reiniciado com a configuração recém-implantada (no caso de uma atualização de firmware) ou iniciar o novo pacote de software e verificar se o novo firmware ou software está sendo executando como esperado. Durante este processo de várias etapas, o dispositivo deve informar o dispositivo IoT sobre o estado do dispositivo à medida que ele avança pelas etapas.

Um serviço de armazenamento, como o Armazenamento do Azure, ou uma rede de distribuição de conteúdo (CDN) pode entregar o pacote de atualização. É importante verificar a integridade do pacote baixado antes de aplicar a atualização de firmware ou software para garantir que o pacote seja originado da fonte esperada.

Depois de concluir uma atualização de firmware, o dispositivo deve ser capaz de verificar e identificar um estado válido. Se o dispositivo não entrar com êxito nesse estado válido, o software no dispositivo deverá iniciar uma reversão para um estado válido conhecido. O estado válido conhecido pode ser o último estado válido conhecido ou uma imagem de firmware do dispositivo conhecida como *estado dourado*, armazenada em uma partição do armazenamento.

## Próximas etapas

Para saber mais sobre o Hub IoT do Azure, consulte estes links:

* [Introdução ao Hub IoT][]
* [O que é o Hub IoT do Azure?][]
* [Conectar seu dispositivo][]

[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[O que é o Hub IoT do Azure?]: iot-hub-what-is-iot-hub.md
[Conectar seu dispositivo]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_1203_2015-->