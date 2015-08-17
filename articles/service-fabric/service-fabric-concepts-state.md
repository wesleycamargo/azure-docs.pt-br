<properties
   pageTitle="Definir e gerenciar o estado"
   description="Como definir e gerenciar o estado do serviço na malha de serviço"
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

# Estado do serviço
**Estado do serviço** são os dados que o serviço requer para funcionar. São as estruturas de dados e variáveis que o serviço lê e grava para poder funcionar.

Por exemplo: Considere um serviço de calculadora simples. Esse serviço usa dois números e retorna a soma. Este é um serviço puramente sem monitoração de estado que não possui dados associados a ele.

Agora, considere a mesma calculadora mas além da soma de computação também possui um método para retornar a última soma computada. Esse serviço agora é com monitoração de estado - contém algum estado que é gravado (quando calcula uma nova soma) e lido (quando retorna a última soma calculada).

Na malha de serviço, o primeiro serviço é chamado de serviço sem monitoração de estado. O segundo serviço é chamado de serviço com monitoração de estado.

## Armazenar o estado do serviço
Estado pode ser externalizado ou localizado em conjunto com o código que está manipulando o estado. A externalização de estado normalmente é feita por meio de um armazenamento ou banco de dados externo. Em nosso exemplo de calculadora, isso pode ser um banco de dados SQL onde o resultado atual é armazenado em uma tabela. Todas as solicitações para calcular a soma executa uma atualização nesta linha.

O estado também pode ser localizado com o código que manipula esse código. Serviços com monitoração de estado na malha de serviço são criados usando esse modelo. A malha de serviço fornece a infra-estrutura para garantir que esse estado é altamente disponível e tolerante a falhas no caso de falhas.

## Próximas etapas

Para obter informações sobre os conceitos de malha do serviço, consulte:

- [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)

- [Escalabilidade dos serviços da malha de serviço](service-fabric-concepts-scalability.md)

- [Particionamento dos serviços da malha de serviço](service-fabric-concepts-partitioning.md)
 

<!---HONumber=August15_HO6-->