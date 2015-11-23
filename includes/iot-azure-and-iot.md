# Azure e IoT

Bem vindo ao Microsoft Azure e a Internet das Coisas (IoT). Este artigo apresenta uma arquitetura da solução típica IoT que descreve as características de uma solução de IoT, que você pode implantar usando os serviços do Azure. Uma solução de IoT típica exige segurança, comunicação bidirecional entre dispositivos, numeração possivelmente milhões e um solução back-end que, por exemplo, usa análise preditiva automatizada para descobrir ideias do seu fluxo de eventos de dispositivo para a nuvem.

## Arquitetura da solução de IoT

O diagrama abaixo mostra uma arquitetura comum da solução IoT. Observe que ela não inclui os nomes de todos os serviços específicos do Azure, mas descreve os principais elementos em uma arquitetura da solução de IoT genérica. As seções a seguir fornecem mais informações sobre os elementos nesta solução.

![Arquitetura da solução de IoT][img-solution-architecture]

### Conectividade do dispositivo

Em um cenário típico de IoT, os dispositivos enviam telemetria como leituras de temperatura para um ponto de extremidade na nuvem para o armazenamento e processamento. Os dispositivos também podem receber e responder aos comandos de nuvem para o dispositivo lendo mensagens de um ponto de extremidade de nuvem. Por exemplo, um dispositivo pode recuperar um comando que o instrui a alterar a frequência na qual ele realiza a amostra dos dados.

Um dos maiores desafios enfrentados por projetos IoT é sobre conectar dispositivos de forma confiável e segura para a solução de back-end. Geralmente, os dispositivos IoT têm características diferentes em comparação a outros clientes, como navegadores e aplicativos móveis. Dispositivos IoT:

- Com frequência, são sistemas internos sem operadores humanos.
- Eles podem estar em locais remotos, onde o acesso físico é muito caro.
- Só podem ser acessados por meio do back-end da solução.
- Podem ter recursos de energia e de processamento limitados.
- Podem ter conectividade de rede intermitente, lenta ou cara.
- Talvez precisem de protocolos de aplicativo proprietários, personalizados ou específicos do setor.
- Podem ser criados usando um grande conjunto de plataformas de hardware e de software.

Além dos requisitos acima, todas as soluções IoT também deverão oferecer escala, segurança e confiabilidade. O conjunto de requisitos de conectividade resultante tem implementação difícil e demorada por meio das tecnologias tradicionais, como contêineres da Web e agentes de mensagens.

Um dispositivo pode se comunicar diretamente com um ponto de extremidade de gateway de nuvem ou se o dispositivo não puder usar qualquer um dos protocolos de comunicação ao qual o gateway de nuvem dá suporte, ele pode se conectar por meio de um gateway intermediário que executa a conversão de protocolo.

### Processamento de dados e análise

Na nuvem, um backend de solução IoT:

- Recebe telemetria em escala de seus dispositivos e determina como processar e armazenar os dados. 
- Pode também permitir enviar comandos da nuvem para o dispositivo específico.
- Fornece capacidades de registro de dispositivo que possibilitam que você provisione os dispositivos para controlar quais dispositivos são permitidos para conectar à sua infraestrutura.
- Permite acompanhar o estado de seus dispositivos e monitorar suas atividades.

As soluções de IoT podem incluir loops automáticos de comentários. Por exemplo, um módulo de aprendizado de máquina pode identificar por meio da telemetria do dispositivo para a nuvem, que a temperatura de um dispositivo específico está acima do níveis operacionais normais e enviar um comando para o dispositivo, permitindo que ele execute uma ação corretiva.

### Apresentação

Muitas soluções de IoT permitem aos usuários exibir e analisar os dados coletados de seus dispositivos. Essas exibições podem assumir a forma de painéis ou de relatórios de BI.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot

<!---HONumber=Nov15_HO3-->