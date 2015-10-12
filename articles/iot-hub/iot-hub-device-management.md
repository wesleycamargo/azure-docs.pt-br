<properties
 pageTitle="Gerenciamento de dispositivos IoT | Microsoft Azure"
 description="Uma visão geral do gerenciamento de dispositivos do Hub IoT do Azure"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="juanpere"/>

# Gerenciamento de dispositivos IoT usando o Pacote IoT do Azure e o Hub IoT do Azure

## Introdução
O Pacote IoT do Azure e o Hub IoT do Azure fornecem os recursos básicos que permitem o gerenciamento de dispositivos para soluções IoT, em larga escala, e para uma conjunto diversificado de dispositivos e topologias de dispositivo. A referência ao gerenciamento de dispositivos neste artigo está especificamente relacionada ao gerenciamento de dispositivos IoT.

Os provedores de serviço e as grandes empresas, ou qualquer organização que mantenha uma população de dispositivos IoT, usam recursos de gerenciamento de dispositivo para realizar os processos de negócios a seguir: inscrição e descoberta de dispositivos IoT, habilitação de conectividade, configuração remota e atualização de software em dispositivos. Por exemplo, ao gerenciar dispositivos em instalações fabris ou campos petrolíferos, haverá políticas para configurar e atualizar dispositivos remotamente, com cadeias de aprovação, requisitos normativos de auditoria, presença de proteções físicas e muito mais.

Ao habilitar o gerenciamento de dispositivos IoT para sua solução IoT, leve em consideração os seguintes recursos e determine a importância de cada um de acordo com os seus objetivos de negócios:

* Provisionamento e descoberta de dispositivo: o processo pelo qual um dispositivo é inscrito no sistema.
* Registro do dispositivo e modelos de dispositivo: o método pelo qual os modelos de dispositivo representam o uso estruturado de metadados para relações de dispositivo, função no sistema, métodos de validação.
* Gerenciamento de acesso ao dispositivo: o método pelo qual os dispositivos controlam o acesso aos recursos do dispositivo dos serviços de nuvem.
* Controle remoto: o método pelo qual os usuários ganham acesso aos dispositivos e orientam os dispositivos na mudança.
* Administração remota: processo pelo qual um administrador define a integridade dos dispositivos.  
* Configuração remota: método pelo qual os administradores mudam a configuração do dispositivo.
* Atualização remota de firmware e software: processo pelo qual os administradores podem atualizar o firmware e o software do dispositivo.

As seções a seguir fornecem uma perspectiva detalhada de cada um desses recursos de gerenciamento de dispositivos, bem como um modelo de alto nível para implementar esses recursos usando o Hub IoT do Azure.

## Provisionamento e descoberta de dispositivos
O provisionamento de um dispositivo com o Hub IoT do Azure é feito pela API de registro. Depois de registrar o dispositivo e fornecer ou receber uma chave, você poderá habilitar o dispositivo para se conectar ao Hub IoT usando essa chave. O Hub IoT do Azure se comunicará apenas com dispositivos registrados que apresentem credencial autorizada. Os administradores podem desabilitar o acesso de dispositivos ao Hub IoT do Azure por meio do portal de administração do dispositivo.

Um processo de inicialização pode ser usado dependendo de como os dispositivos IoT são fabricados, provisionados e implantados. Você pode criar um serviço de inicialização como parte de sua solução para fornecer conectividade simples e atrasar o processo de atribuição de um dispositivo a um Hub IoT do Azure específico. O local onde o dispositivo vai operar pode ser desconhecido no momento em que o dispositivo é fabricado. Esse é apenas um exemplo de um grande número de fluxos de trabalho potencialmente complexos que ajudam a tornar um dispositivo conhecido para o Hub IoT do Azure e que também se integra aos processos de negócios existentes.

Ao usar um serviço de inicialização, um dispositivo IoT é inicializado, o que pode acabar fornecendo acesso a um Hub IoT do Azure atribuído. A solicitação deve incluir as credenciais de inicialização do dispositivo e todos os outros dados necessários dos dispositivos autorizados, o serviço de inicialização deve registrar o dispositivo em um Hub IoT do Azure atribuído e fornecer detalhes da conectividade ao dispositivo que está solicitando uma inicialização. O Hub IoT fornece detalhes da conectividade ao dispositivo que está solicitando uma inicialização. Para dispositivos autorizados, o serviço de inicialização deve registrar o dispositivo em um Hub IoT do Azure atribuído e fornecer detalhes da conectividade ao dispositivo que está solicitando uma inicialização. O Hub IoT fornece detalhes da conectividade ao dispositivo que está solicitando uma inicialização.

## Modelos de dispositivo e registro de dispositivos

O uso do termo modelo de dispositivo faz referência ao modelo que inclui propriedades do dispositivo que podem ser lidas/gravadas por um serviço de nuvem e comandos de dispositivo que podem ser executados remotamente por um serviço de nuvem. Em um modelo orientado ao serviço, descrito abaixo, o dispositivo não tem conhecimento do modelo, mas os serviços de nuvem ainda podem rastrear e usar metadados sobre dispositivos.

Armazenar as informações do dispositivo e os metadados associados é essencial para o sistema IoT e a função do registro de dispositivo. Especialmente para dispositivos herdados que não podem ser alterados ou para dispositivos que não usam um modelo de dispositivo, um modelo orientado ao serviço pode permitir que um serviço IoT interaja com uma população de dispositivos. Quando os dispositivos operam usando um modelo definido, o registro de dispositivo atua como uma exibição consistente dos dados do dispositivo, em que o dispositivo atende ao mestre. Nesse caso, o serviço informa ao dispositivo sobre as alterações desejadas, que serão válidas apenas depois que o dispositivo confirmar a alteração.

### Modelo orientado ao serviço
No caso em que um dispositivo se conectar ao Hub IoT do Azure e não fornecer um modelo de dispositivo, é importante implementar um registro de dispositivo que rastreie os dispositivos registrados e seus metadados associados. Nesse caso, o registro de dispositivo é o único local de armazenamento dos metadados do dispositivo. Os exemplos de metadados de dispositivo que talvez você queira rastrear são: a topologia lógica (por exemplo, 4º andar, edifício 109), a função do dispositivo (por exemplo, sensor da porta de entrada) ou qualquer outra informação de identificação.

### Modelo orientado ao dispositivo
Para habilitar sua solução IoT para aproveitar os recursos do dispositivo IoT, os dispositivos precisam se descrever na nuvem depois de se conectarem ao Hub IoT do Azure. Veja a seguir os dois tipos de modelo de dispositivo que podem ser usados em uma solução IoT:

#### Modelo de dispositivo autodefinido
Um engenheiro de dispositivo (ou desenvolvedor usando um simulador de dispositivos) usa o modelo de dispositivo autodefinido por meio do processo de iteração em recursos do dispositivo à medida que cria o dispositivo. Um engenheiro de dispositivo pode começar a criar um dispositivo que tenha poucas propriedades e comandos compatíveis e, posteriormente, adicionar mais. Da mesma forma, esse engenheiro de dispositivo pode ter muitos dispositivos que fornecem recursos exclusivos cada um e, usando o modelo autodefinido, o engenheiro de dispositivo não precisa registrar a estrutura do modelo de dispositivo. Uma variação significativa nos modelos autodefinidos pode apresentar desafios no dimensionamento para um número maior de dispositivos.

#### Modelo de dispositivo predefinido
Uma implantação IoT de produção que opera com restrições de rede e energia/processamento se beneficia enormemente de um modelo de dispositivo predefinido, em que são utilizados o uso mínimo de processamento e consumo de energia do dispositivo. Da mesma forma, o tráfego de rede mínimo permite que dispositivos transmitam dados por redes heterogêneas (Wi-Fi, 2G/3G/4G, BLE, Sat, etc.) especialmente ao usar a infraestrutura limitada e cara (por exemplo, Satellite). Ao implementar um modelo de dispositivo predefinido, um engenheiro de dispositivo pode enviar informações de dispositivo codificadas em um ou dois bytes que atuam como uma chave no modelo de dispositivo predefinido. A brevidade dessa abordagem resulta em eficiências de uma a duas ordens de grandeza em comparação com o modelo de dispositivo autodefinido.

### A solução pré-configurada para monitoramento remoto e seu modelo de dispositivo
A solução pré-configurada para Monitoramento Remoto do Pacote IoT do Azure implementa um modelo de dispositivo autodefinido. Usar esse modelo permite iteração rápida enquanto você define e aprimora os recursos do dispositivo.

É possível localizar o código em que a solução pré-configurada para monitoramento remoto cria um objeto de dispositivo e enviá-lo ao serviço em Simulator/Simulator.WorkerRole/SimulatorCore/Devices/DeviceBase.cs. Analisando os métodos SendDeviceInfo e GetDeviceInfo, você pode ver como o modelo de dispositivo descrito automaticamente é criado e enviado ao Hub IoT do Azure.

É possível localizar o código em que o serviço de nuvem processa os eventos relacionados ao modelo de dispositivo em EventProcessor/EventProcessor.WorkerRole/Processors/DeviceManagementProcessor.cs. O método denominado ProcessJToken é o núcleo de atuação em mensagens relacionadas ao modelo de dispositivo enviadas ao lado do serviço da solução pré-configurada.

Assim que uma mensagem relacionada ao modelo de dispositivo é recebida, os métodos UpdateDeviceFromSimulatedDeviceInfoPacketAsync e UpdateDeviceFromRealDeviceInfoPacketAsync em DeviceManagement/Infrastructure/BusinessLogic/DeviceLogic.cs são responsáveis por atualizar o registro do dispositivo. A API de registro do dispositivo na solução pré-configurada para monitoramento remoto pode ser encontrada em DeviceManagement/Web/WebApiControllers/DeviceApiController.cs.

### Modelos de dispositivo de gateway de campo
Os gateways de campo muitas vezes são usados para permitir conectividade e conversão de protocolo para dispositivos que não podem ou não devem ser conectados diretamente à Internet. Se o dispositivo que você está criando for um gateway de campo, ele poderá representar os dispositivos que são conectados por meio dele (o gateway de campo) em todas as interações com o Hub IoT do Azure. Como o fabricante do gateway de campo, é sua responsabilidade implementar a conversão entre protocolos de dispositivo e protocolos com suporte do serviço IoT. Se deseja permitir que o gateway de campo se conecte a dispositivos BLE (Bluetooth de Baixa Energia), você precisará implementar a interface BLE nos dispositivos e a interface no Hub IoT do Azure.

## Gerenciamento do acesso ao dispositivo
A solução pré-configurada Pacote IoT do Azure controla o acesso a diferentes aspectos do dispositivo, incluindo direitos de acesso de leitura/gravação para propriedades do dispositivo e direitos de execução para comandos do dispositivo. Nas soluções pré-configuradas, esse acesso é controlado por meio do uso do AAD (Active Directory do Azure) no portal de administração do dispositivo. Você pode habilitar a segurança de acesso baseada em função estendendo o uso do AAD na solução pré-configurada.

## Controle remoto
O controle remoto está fora do escopo como um cenário para soluções pré-configuradas do Pacote IoT do Azure. No entanto, veja a seguir uma análise das opções de alto nível que você tem para habilitar o controle remoto em sua solução IoT.

Em cenários de TI, o controle remoto muitas vezes é usado para auxiliar usuários remotos ou configurar remotamente servidores remotos. Em cenários de IoT, a maioria dos dispositivos não envolve usuários, portanto, o controle remoto é usado em cenários de diagnóstico e configuração remota. O controle remoto pode ser implementado usando dois modelos diferentes:

* Conexão direta: para habilitar o controle remoto por meio de uma conexão direta com um dispositivo (por exemplo, SSH no Linux, Área de Trabalho Remota no Windows ou por meio de ferramentas de depuração remota), você precisa ser capaz de criar uma conexão com o dispositivo. Devido ao risco à segurança de expor um dispositivo à Internet aberta, é recomendável usar um serviço de retransmissão (por exemplo, serviço de Retransmissão do Barramento de Serviço do Azure) para habilitar a conexão e o tráfego bidirecional do dispositivo. Uma vez que uma conexão de retransmissão é uma conexão de saída do dispositivo, ela ajuda a limitar a superfície de ataque das portas TCP no dispositivo.

* Comando do dispositivo: o controle remoto por meio do comando do dispositivo aproveita a conexão e o canal de comunicação existentes estabelecidos entre o dispositivo e o Hub IoT do Azure. Para habilitar o controle remoto baseado no comando do dispositivo, os seguintes requisitos precisam ser implementados:
  * O software que é executado no dispositivo precisa informar o serviço IoT que os comandos do dispositivo estão disponíveis no dispositivo. Geralmente, isso é definido como parte do modelo do dispositivo.
  * O software que é executado no dispositivo precisa implementar os comandos do controle remoto. Esses comandos de dispositivo devem seguir um padrão de solicitação (do serviço IoT para o dispositivo) e de resposta (do dispositivo para o serviço IoT). Executar comandos do dispositivo pode afetar o estado do dispositivo e, esse novo estado precisará ser atualizado no registro de dispositivo.

Quando o dispositivo for o repositório mestre da configuração e do estado do dispositivo, o registro de dispositivo vai, por fim, usar um modelo consistente e mudar para o estado do dispositivo necessário para ser enviado por push ao registro de dispositivo. A atualização do estado do registro de dispositivo pode ser forçada por uma solicitação do serviço IoT para o dispositivo ou o dispositivo pode atualizar automaticamente o serviço mediante reconhecimento de uma mudança no estado do sistema. A atualização automática do serviço no dispositivo pode gerar tráfego intenso de rede e aumentar o uso do processador do dispositivo e da energia disponível.

## Administração e monitoramento remoto
Uma vez que a maioria dos dispositivos IoT diferencia funções de usuário, de gerenciamento dos operacionais separadamente, a administração remota é a experiência em que os administradores podem monitorar o estado de seus dispositivos, configurar o fluxo de dados dos dispositivos em aplicativos e processos de negócios (por exemplo, CRM, ERP, sistemas de manutenção, etc.) e atualizar remotamente o estado ou a configuração dos dispositivos por meio do uso de comandos do dispositivo

As soluções pré-configuradas do Pacote IoT do Azure fornecem um Site da Web do Azure que habilita um ponto de partida para uma experiência de administração de dispositivos que você pode estender a cenários em sua solução IoT vertical.

Os administradores definem os limites de integridade de dispositivo como uma função de dados operacionais de dispositivo (geralmente movimentação rápida) e metadados de dispositivo (geralmente movimentação lenta). Uma opção para habilitar o sistema de alerta de integridade de dispositivo é usar regras implementadas por meio de um mecanismo de processamento de transmissão (por exemplo, Stream Analytics do Azure), em vez de uma estratégia de sondagem.

## Configuração remota
Alterar remotamente a configuração de um dispositivo pode ser importante para várias fases no ciclo de vida de um dispositivo: provisionamento, diagnóstico ou integração a processos de negócios. As mudanças na configuração remota são habilitadas por meio de uma combinação do modelo do dispositivo e pela capacidade do serviço IoT de atualizar remotamente o estado da instância do modelo de um dispositivo.

Na solução pré-configurada para monitoramento remoto, o dispositivo descreve que ele dá suporte aos seguintes comandos de dispositivo para configuração remota:

* ChangeKey
* ChangeConfig
* ChangeSystemProperties

Esses comandos de dispositivo não são mostrados como comandos de dispositivo disponíveis no portal de administração do dispositivo da solução pré-configurada, uma vez que são comandos de dispositivo e são executados remotamente por partes específicas do portal. Assim que um comando de dispositivo é recebido pelo dispositivo, uma resposta de confirmação é enviada do dispositivo para o serviço. Depois de processar o comando do dispositivo, o dispositivo envia uma resposta resultante informando que o serviço que está executando o comando do dispositivo foi bem-sucedido ou falhou com um código de erro. Nesse ponto, o estado desejado para o dispositivo é confirmado no dispositivo e o registro de dispositivo é atualizado.

## Atualização remota de firmware e software
A atualização remota de firmware e software é um recurso importante de um sistema IoT, pois permite que dispositivos executem o software mais recente e seguro. A atualização remota de firmware e software em um dispositivo é um exemplo de um processo distribuído de execução longa que normalmente envolve processos de negócios. Por exemplo, atualizar o firmware em um dispositivo que controla uma bomba de combustível altamente potente pode exigir etapas em sistemas adjacentes para redirecionar o combustível enquanto a atualização é realizada e verificada.

### Uma análise da atualização de firmware
Veja a seguir uma maneira possível de implementar a atualização de firmware que pode se ajustar às suas necessidades de negócios. O objetivo desse exemplo é descrever as considerações do processo, não impor uma implementação específica. Projetar as necessidades de atualização para considerar muitos fatores técnicos e de negócios está fora do escopo deste artigo.

As atualizações de dispositivo são iniciadas no serviço IoT e os dispositivos são informados em uma determinada hora por meio de uma comando de dispositivo. Quando um dispositivo dá suporte explicitamente à atualização remota de firmware ou software, o serviço IoT deve fornecer os comandos de atualização com base nas políticas e no processo de negócios definidos. Ao receber o comando do dispositivo para atualização, o dispositivo precisa baixar o pacote de atualização, implantá-lo, reinicializar o pacote recentemente implantado (no caso de atualização de firmware) ou iniciar o novo pacote de software e verificar se o novo firmware ou software está executando como esperado. Durante esse processo de várias etapas, o dispositivo deve informar ao dispositivo IoT o estado do dispositivo à medida que ele avança por meio de várias etapas.

O fornecimento do pacote de atualização pode ser feito por meio de um serviço de armazenamento, como Armazenamento do Azure, ou por meio de uma CDN (rede de distribuição de conteúdo). Verificar a integridade do pacote baixado antes de aplicar a atualização de firmware ou software é importante para garantir que o pacote seja originado pela fonte esperada.

Depois de concluir uma atualização de firmware, o dispositivo deve ser capaz de verificar e identificar um estado válido. Se o dispositivo não entrar com êxito nesse estado válido, o software no dispositivo deverá iniciar uma reversão para um estado válido conhecido. O estado válido conhecido pode ser o último estado válido conhecido ou uma imagem de firmware do dispositivo conhecida como 'estado dourado', armazenada em uma partição do armazenamento.

<!---HONumber=Oct15_HO1-->