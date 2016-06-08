# Azure e Internet das Coisas

Bem vindo ao Microsoft Azure e à IoT (Internet das Coisas). Este artigo apresenta uma arquitetura de solução IoT que descreve as características comuns de uma solução IoT, que você pode implantar usando os serviços do Azure. As soluções IoT exigem segurança, comunicação bidirecional entre dispositivos, possivelmente milhões deles, e um back-end de solução que, por exemplo, use análise preditiva automatizada para revelar informações do fluxo de eventos do seu dispositivo para a nuvem.

O Hub IoT do Azure é um bloco de construção fundamental quando você implementa a arquitetura da solução IoT usando os serviços do Azure, e o IoT Suite fornece implementações completas de ponta a ponta dessa arquitetura para cenários específicos de IoT. Por exemplo, a solução de *monitoramento remoto* permite que você monitore o status dos dispositivos, como as máquinas de vendas, e a *manutenção preditiva* ajuda você a antecipar as necessidades de manutenção de dispositivos, como bombas em estações de bombeamento remotas, e evitar o tempo de inatividade não programado.

## Arquitetura da solução de IoT

O diagrama abaixo mostra uma arquitetura comum da solução IoT. Observe que ela não inclui os nomes de todos os serviços específicos do Azure, mas descreve os principais elementos em uma arquitetura da solução de IoT genérica. Nessa arquitetura, os dispositivos IoT coletam dados que eles enviam para um gateway de nuvem. O gateway de nuvem torna os dados disponíveis para processamento por outros serviços de back-end, por meio dos quais os dados são entregues para outros aplicativos de linha de negócios ou para operadores humanos através de um painel ou de outros dispositivos de apresentação.

![Arquitetura da solução de IoT][img-solution-architecture]

> [AZURE.NOTE] Para ver uma análise detalhada da arquitetura IoT, confira o [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Arquitetura de Referência da IoT do Microsoft Azure).

### Conectividade do dispositivo

Nesta arquitetura da solução IoT, os dispositivos enviam telemetria, como leituras de sensor de uma estação de bombeamento, para um ponto de extremidade da nuvem para armazenamento e processamento. Em um cenário de manutenção preditiva, o back-end pode usar a transmissão de dados do sensor para determinar quando uma bomba específica necessita de manutenção. Os dispositivos também podem receber e responder aos comandos de nuvem para o dispositivo lendo mensagens de um ponto de extremidade de nuvem. Por exemplo, no cenário de manutenção preditiva, o back-end da solução pode enviar comandos para que outras bombas na estação de bombeamento iniciem os fluxos do novo roteamento antes da manutenção expirar para se certificar de que o engenheiro de manutenção poderá começar assim que chegar.

Um dos maiores desafios ao encarar projetos de IoT é conectar dispositivos de forma confiável e segura com o back-end da solução para habilitar o dispositivo para enviar telemetria e recuperar os comandos. Os dispositivos IoT têm características diferentes em comparação com outros clientes, como navegadores e aplicativos móveis. Dispositivos IoT:

- Com frequência, são sistemas internos sem operadores humanos.
- Eles podem ser implantados em locais remotos, nos quais o acesso físico é muito caro.
- Só podem ser acessados por meio do back-end da solução. Não há outra maneira de interagir com o dispositivo.
- Podem ter recursos de energia e de processamento limitados.
- Podem ter conectividade de rede intermitente, lenta ou cara.
- Talvez precisem de protocolos de aplicativo proprietários, personalizados ou específicos do setor.
- Podem ser criados usando um grande conjunto de plataformas de hardware e de software conhecidas.

Além dos requisitos acima, todas as soluções IoT também deverão oferecer escala, segurança e confiabilidade. O conjunto de requisitos de conectividade resultante tem implementação difícil e demorada por meio das tecnologias tradicionais, como contêineres da Web e agentes de mensagens. O Hub IoT do Azure e os SDKs do dispositivo IoT facilitam a implementação de soluções que atendam a esses requisitos.

Um dispositivo poderá se comunicar diretamente com um ponto de extremidade de gateway de nuvem ou, se o dispositivo não puder usar nenhum dos protocolos de comunicação aos quais o gateway de nuvem dá suporte, ele poderá se conectar por meio de um gateway intermediário, como o [gateway de protocolo do Hub IoT][lnk-protocol-gateway], que executa a conversão de protocolo. Por exemplo, dos Protocolos Industriais Comuns (CIP) ao AMQPS.

### Processamento de dados e análise

Na nuvem, um back-end da solução IoT é onde ocorre a maior parte do processamento de dados na solução, em particular, filtrando e agregando telemetria e roteando-a para outros serviços. O back-end da solução IoT:

- Recebe telemetria em escala de seus dispositivos e determina como processar e armazenar os dados. 
- Pode permitir enviar comandos da nuvem para o dispositivo específico.
- Fornece capacidades de registro de dispositivo que possibilitam que você provisione dispositivos e controle quais dispositivos têm permissão para se conectar à sua infraestrutura.
- Permite acompanhar o estado de seus dispositivos e monitorar suas atividades.

No cenário de manutenção preditiva, o back-end da solução armazena dados de telemetria históricos para identificar padrões e analisar a telemetria conforme ela surge para identificar os padrões que indicam que a manutenção expirou em uma bomba específica.

As soluções de IoT podem incluir loops automáticos de comentários. Por exemplo, um módulo de análise no back-end pode identificar por meio da telemetria que a temperatura de um dispositivo específico está acima dos níveis operacionais normais e enviar um comando para o dispositivo, instruindo-o para executar uma ação corretiva.

### Conectividade de negócios e apresentação

A camada de conectividade de negócios e apresentação permite que os usuários finais interajam com os dispositivos e solução IoT. Ela permite aos usuários exibir e analisar os dados coletados de seus dispositivos. Esses modos de exibição podem assumir a forma de painéis ou relatórios de BI, que podem exibir tanto os dados históricos quanto os dados quase em tempo real. Por exemplo, um operador pode verificar o status de estações de bombeamento específicas e ver quaisquer alertas gerados pelo sistema. Essa camada também permite a integração do back-end da solução IoT com aplicativos de linhas de negócios existentes para ligar fluxos de trabalho e processos de negócios. Por exemplo, a solução de manutenção preditiva pode ser integrada a um sistema de agendamento que programa a visita de um engenheiro em uma estação de bombeamento quando a solução identifica uma bomba que precisa de manutenção.

![Painel da solução IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]: ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf

<!---HONumber=AcomDC_0601_2016-->