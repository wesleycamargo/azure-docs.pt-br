# Suporte ao AMQP 1.0 no Barramento de Serviço


O serviço de nuvem do barramento de serviço do Azure e o barramento de serviço para o Windows Server (Service Bus 1.1) oferecem suporte ao Advanced Message Queueing Protocol (AMQP) 1.0. O AMQP permite que você crie várias plataformas, aplicativos híbridos usando um protocolo de padrão aberto. Você pode criar aplicativos que usam componentes que são criados usando estruturas e linguagens diferentes e que são executados em sistemas operacionais diferentes. Todos esses componentes podem se conectar ao Barramento de Serviço e perfeitamente trocam mensagens de negócios estruturados com eficiência e fidelidade total.

## Introdução: Introdução: O que é AMQP 1.0 e por que é importante?

Tradicionalmente, os produtos de middleware orientados à mensagens têm usado protocolos proprietários para comunicação entre os aplicativos cliente e os agentes. Isso significa que, depois que você selecionou o agente de mensagens de um determinado fornecedor, é necessário usar bibliotecas do fornecedor para conectar os aplicativos de cliente para esse agente. Isso resulta em um grau de "lock-in" para esse fornecedor, uma vez que a portabilidade de um aplicativo para um produto diferente exige recodificação de todos os aplicativos conectados. 

Além disso, a conexão de corretores de mensagens de diferentes fornecedores juntos é complicada. Isso geralmente requer uma ponte no nível de aplicativo para mover as mensagens de um sistema para outro e para converter entre os formatos de mensagem proprietários. Esse é um requisito comum. Por exemplo, quando você deve fornecer uma nova interface unificada diferentes sistemas mais antigos para ou integrar sistemas de TI após uma fusão.

A indústria de software é uma empresa de avanço rápido. Novas linguagens de programação e estruturas de aplicativo são inventadas em um ritmo muitas vezes desconcertante. Da mesma forma, os requisitos dos sistemas de TI evoluem ao longo do tempo e os desenvolvedores querem tirar proveito dos recursos da plataforma mais recentes. No entanto, às vezes o fornecedor de mensagens selecionado não suporta essas plataformas. Como os protocolos de mensagens do produto são proprietários, não é possível para outras pessoas fornecerem bibliotecas para essas novas plataformas. Portanto, você deve usar abordagens como criar gateways ou pontes para que você possa continuar a usar o produto do sistema de mensagens.

O desenvolvimento do AMQP (Advanced Message Queuing Protocol) 1.0 foi motivado por esses problemas. Ele se originou no JP Morgan Chase, que, como a maioria das empresas de serviços financeiros, são usuários mais frequentes de middleware orientado a mensagens. O objetivo era simples: criar um protocolo de mensagens de padrão aberto que tornasse possível a criação de aplicativos com base em mensagem de uso dos componentes incorporados usando diferentes linguagens, estruturas e sistemas operacionais, tudo isso usando componentes de ponta em uma variedade de fornecedores.

## Recursos técnicos do AMQP 1.0

O AMQP (Advanced Message Queuing Protocol) 1.0 é um protocolo de mensagens eficiente, confiável e conectado que pode ser usado para criar aplicativos de mensagens avançados entre plataformas. O protocolo tem um simples objetivo: definir a mecânica da transferência segura, confiável e eficiente de mensagens entre duas partes. As mensagens em si são codificadas usando uma representação de dados portáteis que permite heterogêneos remetentes e receptores trocar mensagens comerciais estruturados com fidelidade total. A seguir está um resumo dos recursos mais importantes:

*    **Eficiente**: o AMQP 1.0 é um protocolo orientado a conexões que usa uma codificação binária para as instruções de protocolo e as mensagens de negócios transferidas. Ele incorpora esquemas sofisticadas de controle de fluxo para maximizar a utilização da rede e os componentes conectados. Dito isso, o protocolo foi projetado para obter um equilíbrio entre a eficiência, a flexibilidade e a interoperabilidade.
*    **Confiável**: o protocolo AMQP 1.0 permite que mensagens sejam trocadas com uma variedade de garantias de confiabilidade de disparar e esquecer para confiar na entrega confirmada de uma vez.
*    **Flexível**: o AMQP 1.0 é um protocolo flexível que pode ser usado para oferecer suporte a topologias diferentes. O mesmo protocolo pode ser usado para comunicações de cliente a cliente, agente de cliente e agente de agente.
*    **Independente do modelo de agente**: a especificação do AMQP 1.0 não exige quaisquer requisitos no modelo de mensagens usado por um agente. Isso significa que é possível adicionar facilmente suporte AMQP 1.0 para corretores de mensagens existentes.

## O AMQP 1.0 é um Padrão (com um 'P' maiúsculo)

O AMQP 1.0 tem sido desenvolvido desde 2008 por um grupo central de mais de 20 empresas, fornecedores de tecnologia e empresas de usuário final. Durante esse tempo, empresas de usuário tem contribuído com suas necessidades de negócios reais e os fornecedores de tecnologia têm evoluído o protocolo para atender a essas necessidades. Durante todo o processo, os fornecedores têm participado de workshops onde colaboram para validar a interoperabilidade entre suas implementações.

Em outubro de 2011, o trabalho de desenvolvimento para um comitê técnico dentro da organização para o avanço dos Structured Information Standards (OASIS) e o Padrão OASIS AMQP 1.0 foi lançado em outubro de 2012. As seguintes empresas participaram do Comitê técnico durante o desenvolvimento do padrão:

*    **Fornecedores de tecnologia**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
*    **Empresas de usuário**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Alguns dos benefícios de padrões abertos citados com frequência incluem:

*    Menos chance de bloqueio de fornecedor
*    Interoperabilidade
*    Ampla disponibilidade de bibliotecas e ferramentas
*    Proteção contra obsolescência
*    Disponibilidade de equipe capacitada
*    Risco menor e gerenciável

## AMQP 1.0 e Barramento de Serviço

A adição de suporte do AMQP 1.0 para o barramento de serviço significa que agora você pode aproveitar o enfileiramento de mensagens e os recursos de mensagem agenciados de publicação/assinatura a partir de uma variedade de plataformas usando um protocolo binário eficiente. Além disso, você pode criar aplicativos compostos de componentes criados com o uso de uma mistura de linguagens, estruturas e sistemas operacionais.

O diagrama a seguir ilustra um exemplo de implantação nas quais clientes Java sendo executado no Linux, escrito usando o padrão Java Message Service (JMS) API e clientes .NET em execução no Windows, trocarem mensagens por meio do Barramento de Serviço usando o AMQP 1.0.

![][0]

**Figura 1: Cenário de implantação de exemplo mostrando mensagens usando o Barramento de Serviço e AMQP 1.0 em plataformas cruzadas**

Neste momento, as seguintes bibliotecas de cliente são conhecidas para trabalhar com o Barramento de Serviço:

<table>
  <tr>
    <th>Linguagem</th>
    <th>Biblioteca</th>
  </tr>
  <tr>
    <td>Java</td>
    <td>Cliente do Apache Qpid Java Message Service (JMS)<br/>
        Cliente Java IIT Software SwiftMQ</td>
  </tr>
  <tr>
    <td>C</td>
    <td>Apache Qpid Proton-C</td>
  </tr>
  <tr>
    <td>PHP</td>
    <td>Apache Qpid Proton-PHP</td>
  </tr>
  <tr>
    <td>Python</td>
    <td>Apache Qpid Proton-Python</td>
  </tr>

</table>


**Figura 2: Tabela de bibliotecas de cliente do AMQP 1.0**

Para obter mais informações sobre como obter e usar essas bibliotecas com o Barramento de Serviço, consulte o [Guia do desenvolvedor do Barramento de serviço do AMQP][]. Consulte a seção "Referências" abaixo para obter mais informações.

## Resumo

*    O AMQP (Advanced Message Queuing Protocol) 1.0 é um protocolo de mensagens aberto e confiável que pode ser usado para criar aplicativos híbridos de mensagens avançados entre plataformas. O AMQP 1.0 é um padrão OASIS.
*    O suporte do AMQP 1.0 agora está disponível no barramento de serviço do Azure, bem como para o barramento de serviço do Windows Server (Service Bus 1.1). O preço é o mesmo para os protocolos existentes.

## Referências

*    [Como usar os tópicos AMQP 1.0 com a API do Barramento de Serviço .NET](http://aka.ms/lym3vk)
*    [Como usar a API do Serviço de Mensagem Java (JMS) com Barramento de Serviço e AMQP 1.0](http://aka.ms/ll1fm3)
*    [Guia do desenvolvedor do barramento de serviço do AMQP](http://msdn.microsoft.com/library/jj841071.aspx)
*    [Especificação do protocolo de enfileiramento de mensagens avançado (AMQP) OASIS versão 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)

[0]: ./media/service-bus-amqp-overview/Example1.png
[Guia do desenvolvedor do barramento de serviço do AMQP]: http://msdn.microsoft.com/library/jj841071.aspx

<!--HONumber=47-->
