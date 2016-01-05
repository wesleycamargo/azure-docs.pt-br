<properties
   pageTitle="Atualização de aplicativos: serialização de dados | Microsoft Azure"
   description="Práticas recomendadas para a serialização de dados e como ela afeta as atualizações de aplicativo sem interrupção."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/15/2015"
   ms.author="jesseb"/>


# Como a serialização de dados afeta a atualização de aplicativo

Em uma [atualização de aplicativo sem interrupção](service-fabric-application-upgrade.md), a atualização é aplicada a um subconjunto de nós, um domínio de atualização de cada vez. Durante esse processo, alguns domínios de atualização estarão na versão mais recente do seu aplicativo, e alguns domínios de atualização terão a versão mínima do seu aplicativo. Nesse momento, a nova versão do seu aplicativo deve ser capaz de ler a versão antiga dos dados, e a versão antiga do seu aplicativo deve ser capaz de ler a nova versão dos dados. Se o formato de dados não for compatível de uma para outra, a atualização pode falhar ou dados podem ser perdidos. Este artigo discute o que constitui o formato de dados e as práticas recomendadas para garantir que seus dados sejam compatíveis de uma versão para outra.


## O que compõe o seu formato de dados?

Na Malha do Serviço, os dados que são persistidos e replicados vêm de suas classes C#. Para aplicativos que usam [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md), são os objetos nos dicionários e filas confiáveis. Para aplicativos que usam [Reliable Actors](service-fabric-reliable-actors-introduction.md), esse é o estado de backup do ator. Essas classes C# devem ser serializáveis para que possam ser persistidas e replicadas. Portanto, o formato de dados é definido por campos e propriedades que são serializados e por como eles são serializados. Por exemplo, em um `IReliableDictionary<int, MyClass>` os dados são `int` serializados e `MyClass` serializadas.

### Alterações de código que resultam em uma alteração no formato dos dados

Uma vez que o formato de dados é determinado pelas classes C#, alterações nas classes podem causar uma alteração no formato de dados. Deve-se ter cuidado para garantir que uma atualização sem interrupção possa lidar com a alteração do formato de dados. Exemplos que podem causar alterações no formato de dados:

- Adicionar ou remover campos ou propriedades
- Renomear campos ou propriedades
- Alterar os tipos de campo ou propriedades
- Alterar o nome de classe ou namespace

### O Contrato de Dados é o serializador padrão

O serializador é geralmente responsável por ler os dados e desserializá-los para a versão atual mesmo se os dados estiverem em uma versão mais antiga ou *mais recente*. O serializador padrão é o [serializador de contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx), que tem regras de controle de versão bem definidas. As coleções confiáveis permitem que o serializador seja substituído, mas não os atores confiáveis atualmente. O serializador de dados desempenha um papel importante na habilitação de atualizações sem interrupção. O serializador de Contrato de Dados é o serializador recomendado para os aplicativos do Service Fabric.


## Como o formato de dados afeta a atualização sem interrupção

Durante uma atualização sem interrupção, há dois cenários principais em que o serializador pode encontrar versão mais antiga ou *mais recente* dos dados:

1. Depois que um nó é atualizado e inicia o backup, o novo serializador carrega os dados que foram mantidos em disco pela versão antiga.
2. Durante a atualização sem interrupção, o cluster conterá uma combinação das versões antiga e nova do seu código. Como as réplicas podem ser colocadas em diferentes domínios de atualização, e as réplicas enviam dados entre si, a versão nova e/ou antiga de seus dados pode ser encontrada pela versão nova e/ou antiga do seu serializador.

> [AZURE.NOTE]A "nova versão" e "versão antiga" aqui se referem à versão do seu código que está em execução. "Novo serializador" refere-se ao código do serializador que está sendo executado na nova versão do seu aplicativo. "Novos dados" refere-se à classe C# serializada da nova versão do seu aplicativo.

As duas versões do formato de dados e código devem ser compatíveis uma com a outra. Se não forem compatíveis, a atualização sem interrupção pode falhar ou dados podem ser perdidos. A atualização sem interrupção pode falhar porque o código ou o serializador pode acionar exceções ou falhas ao encontrar outra versão. Dados podem ser perdidos se, por exemplo, uma nova propriedade foi adicionada, mas o serializador antigo a descartar durante a desserialização.


## Contrato de dados

O contrato de dados é a solução recomendada para garantir que seus dados sejam compatíveis. Ele possui regras de versão bem definidas para adição, remoção e alteração de campos. Ele também tem suporte para lidar com campos desconhecidos, conectando-se ao processo de serialização, desserialização e herança de classe. Para saber mais, confira [Usando o contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx).


## Próximas etapas

[Atualização do Aplicativo Service Fabric usando o Visual Studio](service-fabric-application-upgrade.md)

[Atualizar o aplicativo Service Fabric usando o PowerShell](service-fabric-application-upgrade-powershell.md)

[Parâmetros de atualização](service-fabric-application-upgrade-parameters.md)

[Atualização Manual e Atualização com um Pacote Diff](service-fabric-application-upgrade-advanced.md)

<!---HONumber=AcomDC_1125_2015-->