# Azure e Internet das Coisas

Bem vindo ao Microsoft Azure e à IoT (Internet das Coisas). Este artigo apresenta uma arquitetura de solução IoT que descreve as características comuns de uma solução IoT, que você pode implantar usando os serviços do Azure. As soluções IoT exigem segurança, comunicação bidirecional entre dispositivos, possivelmente milhões deles, e um back-end de solução que, por exemplo, use análise preditiva automatizada para revelar informações do fluxo de eventos do seu dispositivo para a nuvem.

O Hub IoT do Azure é um bloco de construção fundamental quando você implementa a arquitetura da solução IoT usando os serviços do Azure. O IoT Suite fornece implementações completas de ponta a ponta dessa arquitetura para especificar cenários de IoT, como *monitoramento remoto* e *manutenção preditiva*.

## Arquitetura da solução de IoT

O diagrama abaixo mostra uma arquitetura comum da solução IoT. Observe que ela não inclui os nomes de todos os serviços específicos do Azure, mas descreve os principais elementos em uma arquitetura da solução de IoT genérica. Nessa arquitetura, os dispositivos IoT coletam dados que eles enviam para um gateway de nuvem. O gateway de nuvem torna os dados disponíveis para processamento por outros serviços de back-end, por meio dos quais os dados são entregues para outros aplicativos de linha de negócios ou para operadores humanos através de um painel ou de outros dispositivos de apresentação.

![Arquitetura da solução de IoT][img-solution-architecture]

> [AZURE.NOTE] Para uma discussão detalhada da arquitetura de IoT, consulte o [Microsoft Azure IoT services: Reference Architecture][lnk-refarch].

### Conectividade do dispositivo

Nesta arquitetura da solução IoT, os dispositivos enviam telemetria, como leituras de temperatura, para um ponto de extremidade da nuvem para armazenamento e processamento. Os dispositivos também podem receber e responder aos comandos de nuvem para o dispositivo lendo mensagens de um ponto de extremidade de nuvem. Por exemplo, um dispositivo pode recuperar um comando que o instrui a alterar a frequência na qual ele realiza a amostra dos dados.

Um dos maiores desafios enfrentados por projetos IoT é sobre conectar dispositivos de forma confiável e segura com o back-end da solução. Os dispositivos IoT têm características diferentes em comparação com outros clientes, como navegadores e aplicativos móveis. Dispositivos IoT:

- Com frequência, são sistemas internos sem operadores humanos.
- Eles podem estar em locais remotos, onde o acesso físico é muito caro.
- Só podem ser acessados por meio do back-end da solução. Não há outra maneira de interagir com o dispositivo.
- Podem ter recursos de energia e de processamento limitados.
- Podem ter conectividade de rede intermitente, lenta ou cara.
- Talvez precisem de protocolos de aplicativo proprietários, personalizados ou específicos do setor.
- Podem ser criados usando um grande conjunto de plataformas de hardware e de software conhecidas.

Além dos requisitos acima, todas as soluções IoT também deverão oferecer escala, segurança e confiabilidade. O conjunto de requisitos de conectividade resultante tem implementação difícil e demorada por meio das tecnologias tradicionais, como contêineres da Web e agentes de mensagens. O Hub IoT do Azure e os SDKs do dispositivo IoT facilitam a implementação de soluções que atendam a esses requisitos.

Um dispositivo poderá se comunicar diretamente com um ponto de extremidade de gateway de nuvem ou, se o dispositivo não puder usar nenhum dos protocolos de comunicação aos quais o gateway de nuvem dá suporte, ele poderá se conectar por meio de um gateway intermediário, como o [gateway de protocolo Hub IoT][lnk-protocol-gateway], que executa a conversão de protocolo. O

### Processamento de dados e análise

Na nuvem, um back-end da solução IoT é onde ocorre a maior parte do processamento de dados na solução, em particular, filtrando e agregando telemetria e roteando-a para outros serviços. O back-end da solução IoT:

- Recebe telemetria em escala de seus dispositivos e determina como processar e armazenar os dados. 
- Pode permitir enviar comandos da nuvem para o dispositivo específico.
- Fornece capacidades de registro de dispositivo que possibilitam que você provisione dispositivos e controle quais dispositivos têm permissão para se conectar à sua infraestrutura.
- Permite acompanhar o estado de seus dispositivos e monitorar suas atividades.

As soluções de IoT podem incluir loops automáticos de comentários. Por exemplo, um módulo de análise no back-end pode identificar por meio da telemetria que a temperatura de um dispositivo específico está acima dos níveis operacionais normais e enviar um comando para o dispositivo, permitindo que ele execute uma ação corretiva.

### Conectividade de negócios e apresentação

A camada de conectividade de negócios e apresentação permite que os usuários finais interajam com os dispositivos e solução IoT. Ela permite aos usuários exibir e analisar os dados coletados de seus dispositivos. Essas exibições podem assumir a forma de painéis ou de relatórios de BI. Por exemplo, um operador pode verificar o status de caminhões de transporte específicos e ver quaisquer alertas gerados pelo sistema. Essa camada também permite a integração do back-end da solução IoT com aplicativos de linhas de negócios existentes para ligar fluxos de trabalho e processos de negócios.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf

<!-----------HONumber=AcomDC_0330_2016-->