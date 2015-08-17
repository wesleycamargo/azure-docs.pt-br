<properties
   pageTitle="Particionamento dos serviços da malha de serviço"
   description="Descreve como particionar os serviços de malha do serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="aprameyr"/>

# Particionamento dos serviços da malha de serviços
A malha de serviço facilita a criação de serviços escalonáveis de monitoração de estado através do suporte ao particionamento do estado do serviço e tendo cada partição operando em um subconjunto do estado total. Cada partição torna-se uma unidade que fica [altamente disponível](service-fabric-availability-services.md). As réplicas das partições são distribuídas entre os nós no cluster e são balanceadas.

> [AZURE.NOTE]Enquanto os serviços sem monitoração de estado também podem ser particionados esse cenário é raro e desnecessário para a maioria dos serviços de malha do serviço.

Há três diferentes esquemas de particionamento disponíveis.

## Esquema de particionamento de singleton
Usado para especificar que o serviço não precisa de particionamento.

## Esquema de particionamento nomeado
Usado para especificar um conjunto fixo de nomes para cada partição de serviço. As partições individuais podem ser pesquisadas pelo nome.

## Esquema de particionamento por intervalos
Usado para especificar um intervalo inteiro (identificado por uma chave alta e baixa) e um número de partições (n). Cria n partições, cada uma responsável por um subintervalo não sobrepostas. Exemplo: Um esquema de particionamento por intervalos (para um serviço com três réplicas) com uma chave baixa de 0, uma chave de alta de 99 e uma contagem de 4 criaria 4 partições conforme mostrado abaixo.

![Particionamento por intervalos](./media/service-fabric-concepts-partitioning/range-partitioning.png)

O mais frequente é criar um hash para uma chave exclusiva dentro de um conjunto de dados. Alguns exemplos comuns de chaves seriam um número de identificação de veículo (VIN), ID do funcionário ou uma cadeia de caracteres exclusiva. Usando essa chave exclusiva você criaria um código de hash longo, o módulo do intervalo de chaves, para usar como sua chave. Você pode especificar os limites superior e inferior do intervalo de chave permitido.

Além disso, você deve estimar um número de partições altas o suficiente para lidar com recursos de pior caso de carga (por exemplo, limitações de memória ou largura de banda de rede), mas não tanto que as partições fiquem extremamente esparsas.

### Selecionando um algoritmo de hash
Uma parte importante de hash é selecionar o algoritmo de hash. Uma consideração importante é se o objetivo é agrupar chaves semelhantes próximas umas das outras (hash sensível à localidade) ou se a atividade deve ser distribuída amplamente em todas as partições (hash de distribuição).

Um bom recurso para opções gerais de algoritmo de código de hash é [a página do Wikipedia sobre funções de hash](http://en.wikipedia.org/wiki/Hash_function).

> [AZURE.NOTE]Não é possível alterar o número de partições ou o tipo de esquema de particionamento usado para um serviço após ele ter sido criado.

## Próximas etapas

Para obter informações sobre os conceitos de malha do serviço, consulte:

- [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)

- [Escalabilidade dos serviços da malha de serviço](service-fabric-concepts-scalability.md)
 

<!---HONumber=August15_HO6-->