<properties
   pageTitle="Visão geral técnica do Service Fabric | Microsoft Azure"
   description="Uma visão geral técnica da Malha do Serviço. Aborda os conceitos principais e a arquitetura."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# Visão geral técnica da Malha do Serviço

Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis.

## Principais conceitos de terminologia
Esta seção fornece detalhes da terminologia usada no Service Fabric para entender os termos usados em outro lugar na documentação.

**Cluster**: um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Os clusters podem ser dimensionados para milhares de máquinas.

**Nó**: uma máquina ou VM que faz parte de um cluster é chamado de nó. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características como propriedades de posicionamento. Cada máquina ou VM tem um serviço do Windows de início automático, `FabricHost.exe` que começa a ser executado na inicialização, e esse serviço inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Esses dois executáveis compõem o nó. Para cenários de teste, você pode hospedar vários nós em um único PC ou VM, executando várias instâncias do `Fabric.exe` e do `FabricGateway.exe`.

**Tipo de Aplicativo**: o nome/versão atribuído a uma coleção de tipos de serviço. Essas informações são definidas em um arquivo `ApplicationManifest.xml`, inserido em um diretório de pacote de aplicativos que é então copiado para o armazenamento de imagens do cluster do Service Fabric. Assim, você pode criar um aplicativo nomeado desse tipo de aplicativo dentro do cluster.

Leia o artigo [Modelo de aplicativo](service-fabric-application-model.md) para saber mais.

**Pacote de Aplicativos**: um diretório de disco contendo o arquivo `ApplicationManifest.xml` do tipo de aplicativo. Esse arquivo faz referência aos pacotes de serviço de cada tipo de serviço que compõe o tipo de aplicativo. Os arquivos no diretório do pacote de aplicativos são copiados no armazenamento de imagens do cluster do Service Fabric. Por exemplo, um pacote de aplicativos para um tipo de aplicativo de email pode conter referências a um pacote de serviços Fila, um pacote de serviços front-end e um pacote de serviços de banco de dados.

**Aplicativo Nomeado**: depois de copiar um pacote de aplicativos para o armazenamento de imagens, você pode criar uma instância do aplicativo no cluster, especificando o tipo de aplicativo do pacote de aplicativos (usando seu nome/versão). Cada instância do tipo de aplicativo recebe um nome URI com esta aparência: `"fabric:/MyNamedApp"`. Em um cluster, você pode criar vários aplicativos nomeados a partir de um tipo de aplicativo único. Você também pode criar aplicativos nomeados a partir de diferentes tipos de aplicativos. Cada aplicativo nomeado é gerenciado e possui controle de versão independente.

**Tipo de Serviço**: o nome/versão atribuída aos pacotes de códigos, pacotes de dados e pacotes de configuração de um serviço. Essas informações são definidas em um arquivo `ServiceManifest.xml`, inserido em um diretório de pacote de serviços, e o diretório de pacote de serviços é referenciado pelo arquivo `ApplicationManifest.xml` de um pacote de aplicativos. Dentro do cluster, depois de criar um aplicativo nomeado, você pode criar um serviço nomeado a partir de um dos tipos de serviço do tipo de aplicativo. O arquivo `ServiceManifest.xml` do tipo de serviço descreve o serviço.

Leia o artigo [Modelo de aplicativo](service-fabric-application-model.md) para saber mais.

Há dois tipos de serviço:

- **Sem estado:** use um serviço sem estado quando o estado persistente do serviço for armazenado em um serviço de armazenamento externo, como o Armazenamento do Azure, Banco de Dados SQL do Azure ou Banco de Dados de Documentos do Azure. Você também deve usar um serviço sem estado quando o serviço não tiver qualquer armazenamento persistente. Por exemplo, um serviço de calculadora no qual os valores são passados para o serviço, um cálculo é realizado usando esses valores e um resultado é retornado.

- **Com estado:** use um serviço com estado quando quiser que o Service Fabric gerencie o estado de seu serviço por meio de seus modelos de programação Reliable Collections ou Reliable Actors. Ao criar um serviço nomeado, você especifica em quantas partições deseja distribuir seu estado (para escalabilidade) e especifica quantas vezes replicar o estado entre os nós (para confiabilidade). Cada serviço nomeado tem uma única réplica primária e várias réplicas secundárias. Modifique o estado do serviço nomeado escrevendo para a réplica primária; em seguida, o Service Fabric replica esse estado em todas as réplicas secundárias, mantendo seu estado em sincronia. Quando uma réplica primária falha, o Service Fabric detecta automaticamente, promove uma réplica secundária existente a réplica primária e, em seguida, cria uma nova réplica secundária.

**Pacote de Serviços**: um diretório de disco contendo o arquivo `ServiceManifest.xml` do tipo de serviço. Este arquivo faz referência ao código, aos dados estáticos e a pacotes de configuração para o tipo de serviço. Os arquivos no diretório do pacote de serviços são referenciados pelo arquivo `ApplicationManifest.xml` do tipo de aplicativo. Por exemplo, um pacote de serviços pode fazer referência ao código, aos dados estáticos e aos pacotes de configuração que compõem um serviço de banco de dados.

**Serviço Nomeado**: depois de criar um aplicativo nomeado, você pode criar uma instância de um de seus tipos de serviço no cluster, especificando o tipo de serviço (usando seu nome/versão). Cada instância de tipo de serviço recebe um nome de URI com escopo dentro do URI de seu aplicativo nomeado. Por exemplo, se você criar um serviço chamado "MyDatabase" dentro de um aplicativo chamado "MyNamedApp”, o URI terá esta aparência: `"fabric:/MyNamedApp/MyDatabase"`. Dentro de um aplicativo nomeado, você pode criar vários serviços nomeados. Cada serviço nomeado pode ter seu próprio esquema de partição e contagens de instância/réplica.

**Pacote de Códigos**: um diretório de disco contendo arquivos executáveis do tipo de serviço (normalmente, arquivos EXE/DLL). Os arquivos no diretório do pacote de códigos são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando um serviço nomeado é criado, os arquivos do pacote de códigos são copiados para os nós selecionados pelo Service Fabric para executar o serviço nomeado e, em seguida, o código começa a ser executado. Há dois tipos de arquivos executáveis de pacote de códigos:

- **Executáveis convidados**: executáveis que são executados “como são” no sistema operacional host (Windows ou Linux). Ou seja, esses executáveis não criam um vínculo com, ou fazem referência a, arquivos de tempo de execução do Service Fabric e, portanto, não usam qualquer um dos modelos de programação do Service Fabric. Esses executáveis não conseguem aproveitar alguns recursos do Service Fabric, como o serviço de cadastramento para a descoberta de ponto de extremidade, e não podem relatar métricas de carga específicas a cada instância do serviço.

- **Executáveis do Host do Serviço**: são executáveis que usam modelos de programação do Service Fabric criando vínculos com arquivos de tempo de execução do Service Fabric. Isso vincula partes do código do executável ao Service Fabric, permitindo recursos adicionais. Por exemplo, uma instância de serviço nomeada pode registrar pontos de extremidade com o Serviço de Nomenclatura do Service Fabric, e também pode relatar métricas de carga.

**Pacote de Dados**: um diretório de disco contendo os arquivos de dados estáticos e somente leitura do tipo de serviço (normalmente arquivos de foto, som e vídeo). Os arquivos no diretório do pacote de dados são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando um serviço nomeado é criado, os arquivos do pacote de dados são copiados para os nós selecionados pelo Service Fabric para executar o serviço nomeado e, em seguida, o código começa a ser executado; o código começa a acessar os arquivos de dados.

**Pacote de Configuração**: um diretório de disco contendo os arquivos de configuração estáticos e somente leitura do tipo de serviço (normalmente arquivos de texto). Os arquivos no diretório do pacote de configuração são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando um serviço nomeado é criado, os arquivos do pacote de configuração são copiados para os nós selecionados pelo Service Fabric para executar o serviço nomeado e, em seguida, o código começa a ser executado; o código começa a acessar os arquivos de configuração.

**Armazenamento de Imagens**: cada cluster do Service Fabric mantém um armazenamento de imagens. Você deve copiar o conteúdo de um pacote de aplicativos para o armazenamento de imagens e, em seguida, registrar o tipo de aplicativo dentro desse pacote de aplicativo. Após o provisionamento do tipo de aplicativo, você pode criar aplicativos nomeados a partir dele. Você pode cancelar o registro de um tipo de aplicativo do repositório de imagens somente após a exclusão de todos os seus aplicativos nomeados.

Leia o artigo [Implantar um aplicativo](service-fabric-deploy-remove-applications.md) para saber mais sobre como implantar no armazenamento de imagens.

**Esquema de Partição**: ao criar um serviço nomeado, você especifica um esquema de partição. Serviços com grandes quantidades de estado dividem os dados entre as partições, que se espalham pelos nós do cluster. Isso permite que o estado de seu serviço nomeado seja dimensionado. Dentro de uma partição, serviços nomeados sem estado têm instâncias, enquanto serviços nomeados com estado têm réplicas. Normalmente, os serviços nomeadas sem estado têm apenas uma partição, já que não têm estado interno. As instâncias de partição fornecem disponibilidade; se uma instância falha, outras instâncias continuam a funcionar normalmente e, em seguida, o Service Fabric cria uma nova instância. Serviços nomeados com estado mantêm seu estado dentro de réplicas, e cada partição tem sua própria réplica, com todo o estado mantido em sincronia. Caso uma réplica falhe, o Service Fabric compila uma nova réplica a partir das réplicas existentes.

Leia o artigo [Particionar Reliable Services do Service Fabric](service-fabric-concepts-partitioning.md) para saber mais.

**Modelos de Programação**: há dois modelos de programação do .NET Framework disponíveis para compilação de serviços do Service Fabric:

- Reliable Services: uma API para compilar serviços com e sem estado. O serviço com estado armazena seu estado em Reliable Collections (como um dicionário ou uma fila). Você também pode conectar várias pilhas de comunicação, como API Web e Windows Communication Foundation (WCF).

- Reliable Actors: uma API para compilar objetos com e sem estado por meio do modelo de programação de Ator virtual. Esse modelo pode ser útil quando você tem muitas unidades independentes de computação/estado. Como esse modelo usa um modelo de threading baseado em turno, é melhor evitar um código que chama outros atores ou serviços, já que um ator individual não pode processar outras solicitações de entrada até que todas as solicitações de saída tenham sido concluídas.

Leia o artigo [Escolher um modelo de programação para o serviço](service-fabric-choose-framework.md) para saber mais.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
Para saber mais sobre o Service Fabric

- [Visão geral da Malha do Serviço](service-fabric-overview.md)
- [Por que usar uma abordagem de microsserviço para construir aplicativos?](service-fabric-overview-microservices.md)
- [Cenários de aplicativos](service-fabric-application-scenarios.md)

<!---HONumber=AcomDC_0224_2016-->