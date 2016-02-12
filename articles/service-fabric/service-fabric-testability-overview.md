<properties
   pageTitle="Visão geral da Possibilidade de Teste | Microsoft Azure"
   description="Este artigo descreve o subsistema de Possibilidade de Teste no Service Fabric para induzir falhas e executar cenários de teste em seus serviços."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="rsinha"/>

# Visão geral da Possibilidade de Teste

O subsistema de Possibilidade de Teste foi desenvolvido para testar serviços criados com base no Service Fabric do Microsoft Azure. Com a Possibilidade de Teste, é possível induzir falhas significativas e executar cenários. Esses cenários e falhas praticam e validam os vários estados e transições pelos quais um serviço passa durante seu tempo de vida, tudo de maneira consistente, segura e controlada.

A Possibilidade de Teste fornece ações e cenários que habilitam essas capacidades. Ações são as falhas individuais visando um serviço com o objetivo de testá-lo. Um desenvolvedor de serviço pode usá-las como blocos de construção para escrever cenários complicados. Por exemplo:

  * Reinicie um nó para simular qualquer quantidade de situações nas quais um computador ou VM é reinicializada.

  * Mova uma réplica de seu serviço com estado para simular o balanceamento de carga, failover ou atualização do aplicativo.

  * Invoque a perda de quorum em um serviço com estado para criar uma situação na qual não é possível realizar operações de gravação, pois não há réplicas "secundárias" ou de "backup" suficientes para aceitar novos dados.

  * Invoque a perda de dados em um serviço com estado para criar uma situação na qual todo o estado na memória é completamente apagado.

Os cenários são operações complexas compostas de uma ou mais ações. Por serem comandos do PowerShell e chamadas à API do C#, essas ações podem assumir qualquer forma: serviços de longa execução, comandos do PowerShell, aplicativos de linha de comando e assim por diante. Na Possibilidade de Teste, fornecemos dois cenários prontos:

  * Cenário de caos
  * Cenário de failover

A Possibilidade de Teste expõe as APIs do PowerShell e do C#. Assim, o desenvolvedor de serviço tem mais agilidade com scripts do PowerShell e maior controle de APIs do C#, conforme o necessário.

## Importância da Possibilidade de Teste

A Malha do Serviço facilita muito o trabalho de escrever e gerenciar aplicativos escalonáveis distribuídos. O subsistema de Possibilidade de Teste no Service Fabric facilita o teste de um aplicativo distribuído. Há três problemas principais que precisam ser resolvidos durante o teste:

1. Simulação/geração de falhas que podem ocorrer em cenários reais: um dos aspectos importantes do Service Fabric é que ele permite que os aplicativos distribuídos se recuperem de várias falhas. No entanto, a fim de testar se o aplicativo é capaz de recuperar-se dessas falhas, precisamos de um mecanismo para simular/gerar essas falhas reais em um ambiente de teste controlado.

2. A capacidade de gerar as falhas correlacionadas: falhas básicas no sistema, tais como falha de rede e falhas do computador, sejam fáceis de produzir individualmente. A geração de uma quantidade considerável de cenários que podem ocorrer no mundo real como resultado de interações dessas falhas individuais não é algo simples.

3. Experiência unificada em vários níveis de desenvolvimento e implantação: há muitos sistemas de injeção de falha que podem executar vários tipos de falhas. No entanto, a experiência em todos eles é ruim ao mudar de um cenário de desenvolvedor one box para a execução dos mesmos testes em ambientes de teste de grande porte a fim de usá-los para teste em produção.

Embora haja muitos mecanismos para resolver esses problemas, falta um sistema que faça o mesmo com as garantias exigidas completamente, desde o ambiente de desenvolvedor one box até o teste em clusters de produção. O subsistema de Possibilidade de Teste ajuda o desenvolvedor de aplicativos a se concentrar no teste de sua lógica de negócios. A Possibilidade de Teste fornece todos os recursos necessários para testar a interação do serviço com o sistema distribuído subjacente.

### Simulação/geração de cenários reais de falha

A fim de testar a robustez de um sistema distribuído contra falhas, precisamos de um mecanismo para gerar falhas. Embora, teoricamente, seja fácil gerar uma falha, a desativação de um nó, por exemplo, isso começará a atingir o mesmo conjunto de problemas de consistência que o Service Fabric está tentando resolver. Por exemplo, se quisermos desativar um nó, o fluxo de trabalho necessário será o seguinte:

1. No cliente, emita uma solicitação de desligamento do nó.

2. Envie a solicitação ao nó correto.

    a. Se o nó não for encontrado, a solicitação falhará.

    b. Se o nó for encontrado, haverá retorno apenas se ele for desligado.

Para verificar a falha de uma perspectiva de teste, é necessário saber que quando essa falha é induzida, ela realmente acontece. A garantia que o Service Fabric fornece é que ou o nó ficará inativo ou já estava inativo quando foi atingido pelo comando. Em ambos os casos, o teste deve conseguir justificar corretamente o estado e obter êxito ou falhar corretamente em sua validação. Um sistema implementado fora do Service Fabric que realize o mesmo conjunto de falhas pode gerar muitos problemas de rede, hardware e software, o que impediria o fornecimento das garantias precedentes. Na presença dos problemas mencionados anteriormente, o Service Fabric reconfigurará o estado do cluster para solucionar os problemas e, portanto, o subsistema de Possibilidade de Teste ainda será capaz de fornecer o conjunto certo de garantias.

### Gerando os eventos e cenários necessários

Apesar da dificuldade de simular uma falha real de forma consistente, a capacidade de gerar falhas correlacionadas é ainda mais difícil. Por exemplo, uma perda de dados ocorre em um serviço persistente com estado quando isto acontece:

1. Somente um quorum de gravação das réplicas é capturado na replicação. Todas as réplicas secundárias ficam atrasadas com relação à primária.

2. O quorum de gravação falha devido à falha das réplicas (devido a um pacote de códigos ou desativação do nó).

3. O quorum de gravação não pode voltar, pois ocorre a perda dos dados para as réplicas (devido à corrupção do disco nova imagem do computador).

Essas falhas correlacionadas ocorrem no mundo real, mas não com tanta frequência quanto as falhas individuais. A Possibilidade de Teste desses cenários antes que eles ocorram na produção é essencial. Ainda mais importante é a capacidade de simular esses cenários com cargas de trabalho de produção em circunstâncias controladas (no meio do dia com todos os engenheiros a postos). Isso é muito melhor do que ocorrer pela primeira vez em produção às 2h00 da manhã.

### Experiência unificada em ambientes diferentes

Tradicionalmente, a prática tem sido criar três conjuntos diferentes de experiências, um para o ambiente de desenvolvimento, um para testes e outro para produção. O modelo era:

1. No ambiente de desenvolvimento, produzir as transições de estado que permitem testes de unidade de métodos individuais.

2. No ambiente de teste, produzir falhas a fim de permitir testes completos que usem vários cenários de falha.

3. Manter o ambiente de produção original para impedir qualquer falha não natural e garantindo a existência de uma resposta humana extremamente rápida às falhas.

No Service Fabric, passando pelo subsistema de Possibilidade de Teste, estamos propondo a solução desse problema a fim de usar a mesma metodologia desde o ambiente de desenvolvimento até a produção. Há duas maneiras de fazer isso:

1. Para induzir as falhas controladas, use as APIs de Possibilidade de Teste de um ambiente one box até os clusters de produção.

2. Para agitar o cluster, gerando a indução automática de falhas, use o subsistema de Possibilidade de Teste para gerar falhas automáticas. O controle da taxa de falhas por meio da configuração possibilita que o mesmo serviço seja testado de maneira diferente em ambientes diferentes.

Com o Service Fabric, embora haja diferença na escala das falhas em ambientes diferentes, os mecanismos reais seriam idênticos. Isso permite um pipeline de código até implantação muito mais rápido e a capacidade de testar os serviços com cargas reais.

## Usando a Possibilidade de Teste

### Usando a Possibilidade de Teste em C#

Os recursos de Possibilidade de Teste estão em System.Fabric.Testability.dll. Essa dll está no pacote nuget Microsoft.ServiceFabric.Testability.nupack. Para usar os recursos da Possibilidade de Teste, inclua o pacote nuget em como uma referência em seu projeto.

### Usando a Possibilidade de Teste no PowerShell

Para usar o PowerShell de Possibilidade de Teste, você deve instalar o MSI do tempo de execução. Depois da instalação do MSI, o módulo ServiceFabricTestability PowerShell é automaticamente carregado para uso dos desenvolvedores.

## Conclusão

Para realmente criar serviços em escala de nuvem, é essencial garantir, antes e após a implantação, que os serviços possam dar suporte a falhas reais. No mundo dos serviços atual, a capacidade de inovar e mover rapidamente o código para produção é muito importante. A Possibilidade de Teste do Service Fabric ajuda os desenvolvedores de serviço a fazer exatamente isso.

## Próximas etapas

- [Ações da Possibilidade de Teste](service-fabric-testability-actions.md)
- [Cenários da possibilidade de teste](service-fabric-testability-actions.md)
- Como testar seu serviço
  - [Simular falhas durante cargas de trabalho de serviço](service-fabric-testability-workload-tests.md)
  - [Falhas de comunicação entre serviços](service-fabric-testability-scenarios-service-communication.md)

<!---HONumber=AcomDC_0204_2016-->