<properties
   pageTitle="Visão geral da Possibilidade de Teste"
   description="Este artigo descreve o recurso de Possibilidade de Teste na Malha do Serviço do Microsoft Azure."
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
   ms.date="03/17/2015"
   ms.author="rsinha"/>

# Visão geral da Possibilidade de Teste

A Possibilidade de Teste é um conjunto de ferramentas desenvolvido especificamente para testar serviços criados com base na Malha do Serviço do Microsoft Azure. As ferramentas permitem que você induza com facilidade falhas significativas e execute cenários de teste para praticar e validar os diferentes estados e transições pelos quais seus serviços passam durante o ciclo de vida, tudo de uma maneira segura e controlada.

A Possibilidade de Teste fornece ações e cenários que permitem a realização do que foi mencionado acima. Ações são as falhas individuais visando um serviço com o objetivo de testá-lo. Um desenvolvedor de serviço pode usá-las como blocos de construção para escrever cenários complicados. Por exemplo:

  + Reinicie um nó para simular qualquer quantidade de situações nas quais uma máquina ou VM é reinicializada.
  + Mova uma réplica de seu serviço com estado para simular o balanceamento de carga, failover ou atualização do aplicativo.
  + Invoque a perda de quorum em um serviço com estado para criar uma situação na qual não é possível realizar operações de gravação, pois não há réplicas "secundárias" ou de "backup" suficientes para aceitar novos dados.
  + Invoque a perda de dados em um serviço com estado para criar uma situação na qual todo o estado na memória é completamente apagado.

Cenários são testes completos compostos por uma ou mais ações e, como ações são simplesmente funções de C# e comandos do PowerShell, podem assumir qualquer forma: serviços de longa duração, comandos do PowerShell, aplicativos de linha de comando e assim por diante. Na Possibilidade de Teste, fornecemos dois cenários prontos:

  + Teste de Caos
  + Teste de Failover

A Possibilidade de Teste expõe as APIs do PowerShell e do C#. Isso permite que o desenvolvedor de serviço tenha mais agilidade com scripts do PowerShell e maior controle de APIs do C#, quando e conforme o necessário.

## Importância da Possibilidade de Teste

A Malha do Serviço facilita muito o trabalho de escrever e gerenciar aplicativos escalonáveis distribuídos. O componente da Possibilidade de Teste na Malha do Serviço tem como objetivo facilitar o teste de um aplicativo distribuído. Há três problemas principais que precisam ser resolvidos durante o teste:

1. Simulação/Geração de falhas que podem ocorrer em cenários do mundo real: um dos aspectos importantes da Malha do Serviço é que ela permite que os aplicativos distribuídos se recuperem de várias falhas. No entanto, para testar se o aplicativo é capaz de recuperar-se dessas falhas, precisamos de um mecanismo para simular/gerar essas falhas reais em um ambiente de teste controlado.
2. A capacidade de gerar as falhas correlacionadas: embora as falhas básicas no sistema, por exemplo, falha de rede, falhas de máquina, sejam fáceis de produzir individualmente. A geração de uma quantidade considerável de cenários que podem ocorrer no mundo real como resultado de interações dessas falhas individuais não é algo simples.
3. Experiência unificada em vários níveis de desenvolvimento e implantação: há muitos sistemas de injeção de falha que permitem a execução de vários tipos de falhas. No entanto, a experiência em todos eles é irregular ao mudar de um cenário de desenvolvedor one box para a execução dos mesmos testes ambientes de testes de grande porte a fim de usá-los para teste em produção.

Embora haja muitos mecanismos para resolver os problemas mencionados acima, falta um sistema que faça o mesmo com as garantias exigidas: completamente, desde o ambiente de desenvolvedor one box até o teste em clusters de produção. O componente de Possibilidade de Teste permite que o desenvolvedor de aplicativos se concentre no teste de sua lógica de negócios. O recurso de Possibilidade de Teste fornece todos os recursos necessários para testar a interação do serviço com o sistema distribuído subjacente.

### Simulação/Geração de cenários reais de falha
Para testar a robustez de um sistema distribuído contra falhas, precisamos de um mecanismo para gerar falhas. Embora teoricamente seja fácil gerar uma falha, a desativação de um nó, por exemplo, isso começará a atingir o mesmo conjunto de problemas de consistência que a Malha do Serviço está tentando resolver. Por exemplo, se quisermos desativar um nó, o fluxo de trabalho necessário será o seguinte:

1. Do cliente, emita uma solicitação de desligamento do nó.
2. Envie a solicitação ao nó correto.
	1. Se o nó não for encontrado, a solicitação falhará.
	2. Se o nó for encontrado, haverá retorno apenas se o nó for desligado.

A partir de uma perspectiva de teste, é necessário saber que quando essa falha é induzida, a falha realmente acontece a fim de verificá-la. A garantia que a malha do Windows fornece é que ou o nó ficará inativo ou já estava inativo quando o comando atingiu o nó. Em ambos os casos, o teste deve conseguir justificar corretamente o estado e obter êxito ou falhar em sua validação corretamente. Um sistema implementado fora da Malha do Serviço que realize o mesmo conjunto de falhas pode gerar uma infinidade de problemas de rede, hardware e software, o que impediria o fornecimento das garantias indicadas acima. Na presença dos problemas mencionados anteriormente, a Malha do Serviço reconfigurará o estado do cluster para solucionar os problemas e, portanto, a Possibilidade de Teste ainda será capaz de fornecer o conjunto certo de garantias.

### Gerando os eventos e o cenário necessários
Apesar da dificuldade de simular uma falha de mundo real de forma consistente, a capacidade de gerar falhas correlacionadas é ainda mais difícil. Por exemplo, uma perda de dados ocorre em um serviço persistente com estado quando ocorre o seguinte:

1. Somente um quorum de gravação das réplicas é capturado na replicação. Todas as réplicas secundárias ficam atrasadas com relação à primária.
2. O quorum de gravação falha devido à falha das réplicas (devido ao pacote de códigos ou desativação do nó).
3. O quorum de gravação não consegue voltar, pois ocorre a perda dos dados para as réplicas (devido à corrupção do disco nova imagem da máquina).

Esses falha correlacionados ocorrem (embora não com tanta frequência quanto as falhas individuais) no mundo real. A Possibilidade de Teste desses cenários antes que eles ocorram na produção é essencial. Além disso, a habilidade de simulá-los na carga de trabalho de produção em circunstâncias controladas (no meio do dia com todos os engenheiros a postos) é bem melhor do acontecer isso pela primeira vez durante a produção às 2:00.

### Experiência unificada em ambientes diferentes
Tradicionalmente, a prática tem sido criar três conjuntos diferentes de experiências, um para o ambiente de desenvolvimento, um para testes e outro para produção. O modelo era:

1. No ambiente de desenvolvimento, produzir as transições de estado que permitem testes unitários de métodos individuais.
2. No ambiente de teste, produzir falhas a fim de permitir testes completos usando vários cenários de falha.
3. Manter o ambiente de produção original, não permitindo qualquer falha não natural e garantindo a existência de uma resposta humana extremamente rápida às falhas.

Na Malha do Serviço, passando pelo módulo de Possibilidade de Teste e serviço, estamos propondo a solução desse problema a fim de usar a mesma metodologia desde o ambiente de desenvolvimento até a produção. Há duas maneiras de fazer isso: 1. Para induzir as falhas controladas, use as APIs de Possibilidade de Teste de um ambiente one box até os clusters de produção. 2. Para agitar cluster, gerando a indução automática de falhas, use o serviço de Possibilidade de Teste para gerar falhas automáticas. O controle da taxa de falhas por meio da configuração permite que o mesmo serviço seja testado de maneira diferente em ambientes diferentes.

Com a Malha do Serviço, embora haja diferença das falhas em ambientes diferentes, o mecanismo real é idêntico. Isso permite um pipeline de código até implantação muito mais rápido e a capacidade de testar os serviços durante a carga real.

## Usando a Possibilidade de Teste
### Usando a Possibilidade de Teste em C# 
Os diversos recursos de Possibilidade de Teste estão presentes em System.Fabric.Testability.dll. Essa dll pode ser encontrada no pacote nuget Microsoft.ServiceFabric.Testability.nupack. Para usar os recursos da Possibilidade de Teste, inclua o pacote nuget em como uma referência em seu projeto.

## Usando a Possibilidade de Teste no PowerShell
Para usar o PowerShell da Possibilidade de Teste, é necessário ter uma instalação do MSI em tempo de execução. Após a instalação do MSI, o módulo ServiceFabricTestability PowerShell é automaticamente carregado para uso dos desenvolvedores.

## Conclusão
Para criar serviços realmente em nuvem, é muito importante garantir que esses serviços possam suportar falhas reais antes da implantação (e também na implantação de produção). Além disso, no mundo dos serviços atual, a capacidade de inovar e mover rapidamente o código para produção é muito importante. O recurso de Possibilidade de Teste na Malha do Serviço permite aos desenvolvedores de serviço executar com precisão as ações indicadas acima.

## Próximas etapas

- [Ações da Possibilidade de Teste](service-fabric-testability-actions.md)
- [Cenários da Possibilidade de Teste](service-fabric-testability-actions.md)
- Como testar seu serviço
	- [Simular falhas durante cargas de trabalho de serviço](service-fabric-testability-workload-tests.md)
   - [Falhas de comunicação entre serviços](service-fabric-testability-scenarios-service-communication.md)

 

<!---HONumber=July15_HO2-->