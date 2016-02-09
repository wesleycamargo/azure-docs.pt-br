<properties
   pageTitle="Usando o Elasticsearch como um repositório de rastreamento do aplicativo do Service Fabric | Microsoft Azure"
   description="Descreve como os aplicativos do Service Fabric podem usar o Elasticsearch e o Kibana para armazenar, indexar e pesquisar os rastreamentos do aplicativo (logs)"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="karolz@microsoft.com"/>

# Usar o ElasticSearch como um repositório de rastreamento do aplicativo do Service Fabric
## Introdução
Este artigo descreve como os aplicativos do [Service Fabric do Azure](https://azure.microsoft.com/documentation/services/service-fabric/) podem usar o **Elasticsearch** e o **Kibana** para armazenamento de rastreamento do aplicativo, indexação e pesquisa. O [Elasticsearch](https://www.elastic.co/guide/index.html) é um mecanismo de análise e pesquisa em tempo real escalonável, distribuído de software livre adequado para essa tarefa. Ele pode ser instalado em máquinas virtuais Windows ou Linux em execução no Microsoft Azure. O Elasticsearch pode processar de maneira muito eficiente rastreamentos *estruturados* produzidos usando tecnologias como **ETW (Rastreamento de Eventos para Windows)**.

O ETW é usado pelo tempo de execução do Service Fabric para originar informações de diagnóstico (rastreamentos). É o método recomendado para aplicativos de Service Fabric para a originar suas informações de diagnóstico também. Isso possibilita a correlação entre os rastreamentos fornecidos pelo aplicativo e pelo tempo de execução e facilita a solução de problemas. Os modelos de projeto de Malha de Serviço no Visual Studio incluem uma API de log (baseada na classe .NET **EventSource**) que emite rastreamentos ETW por padrão. Para obter uma visão geral do rastreamento de aplicativo do Service Fabric usando o ETW, consulte [Monitorar e diagnosticar serviços em uma configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Para os rastreamentos que aparecerão em Elasticsearch, eles precisam ser capturados nos nós de cluster do Service Fabric em tempo real (enquanto o aplicativo estiver em execução) e enviados ao ponto de extremidade Elasticsearch. Há duas opções principais para a captura de rastreamento:

+ **Captura de rastreamento dentro do processo** 
o aplicativo, ou mais precisamente, o processo de serviço, é responsável por enviar os dados de diagnóstico para o repositório de rastreamento (Elasticsearch).

+ **Captura de rastreamento fora do processo**
um agente separado captura rastreamentos dos processos de serviço e os envia para o repositório de rastreamento.

A seguir, descreveremos como configurar o Elasticsearch no Azure, discutiremos os prós e contras de ambas as opções de captura e explicaremos como configurar um serviço do Service Fabric para enviar dados para o Elasticsearch.


## Configurar o Elasticsearch no Azure
A maneira mais simples de configurar o serviço Elasticsearch no Azure é por meio de [**modelos do Gerenciador de Recursos do Azure**](../resource-group-overview.md). Um [modelo do Gerenciador de Recursos do Azure de início rápido para Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) abrangente está disponível no repositório de modelos de início rápido do Azure. Este modelo usa contas de armazenamento separada para as unidades de escala (grupos de nós). Ele também pode provisionar nós de cliente e servidor separados com configurações diferentes e vários números de discos de dados anexados.

Aqui, usaremos outro modelo chamado **ES-MultiNode** da [ramificação de ELK de padrões e práticas da Microsoft](https://github.com/mspnp/semantic-logging/tree/elk/). Esse modelo é um pouco mais fácil de usar e cria um cluster do Elasticsearch protegido pela autenticação HTTP básica por padrão. Antes de continuar, baixe o [Repositório de ELK de padrões e práticas da Microsoft](https://github.com/mspnp/semantic-logging/tree/elk/) do GitHub em seu computador (clonando o repositório ou baixando um arquivo zip). O modelo ES-MultiNode está localizado na pasta com o mesmo nome.
>[AZURE.NOTE] O modelo ES-MultiNode e os scripts associados no momento dão suporte à versão 1.7 do Elasticsearch. O suporte para Elasticsearch 2.0 será incluído em uma data posterior.

### Preparar um computador para executar scripts de instalação ElasticSearch
A maneira mais fácil de usar o modelo ES-MultiNode é por meio de um script do Azure PowerShell fornecido chamado `CreateElasticSearchCluster`. Para usar esse script, você precisa instalar os módulos do PowerShell e uma ferramenta chamada **openssl**. Essa ferramenta é necessária para criar uma chave SSH que pode ser usada para administrar o cluster do Elasticsearch remotamente.

Observe que o script `CreateElasticSearchCluster` foi desenvolvido para facilitar o uso com o modelo ES-MultiNode em um computador Windows. É possível usar o modelo em um computador diferente do Windows, mas esse cenário está além do escopo deste artigo.

1. Se você ainda não instalou, instale os [**módulos do Azure PowerShell**](http://go.microsoft.com/fwlink/p/?linkid=320376). Quando solicitado, clique em **Executar**, depois em **Instalar**.
>[AZURE.NOTE] O Azure PowerShell está passando por uma grande mudança com a versão 1.0 do Azure PowerShell. O CreateElasticSearchCluster é projetado atualmente para funcionar com o Azure PowerShell 0.9.8 e não dá suporte ao Azure PowerShell 1.0 Preview. Um script compatível com o Azure PowerShell 1.0 será fornecido posteriormente.

2. A ferramenta **openssl** está incluída na distribuição de [**Git para Windows**](http://www.git-scm.com/downloads). Se você não tiver feito isso, instale o [Git para Windows](http://www.git-scm.com/downloads) agora. (As opções de instalação padrão estão OK.)

3. Supondo que o Git foi instalado, mas não incluído no caminho do sistema, abra uma janela do Microsoft Azure PowerShell e execute os seguintes comandos:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Substitua o `<Git installation folder>` pelo local do Git em seu computador; o padrão é **"C:\\Program Files\\Git"**. Observe o caractere ponto e vírgula no início do primeiro caminho.

4. Certifique-se de que você está conectado no Azure (através do cmdlet [**Add-AzureAccount**](https://msdn.microsoft.com/library/azure/dn790372.aspx)) e que você selecionou a assinatura que deve ser usada para criar o cluster do Elasticsearch ([**Select-AzureSubscription**](https://msdn.microsoft.com/library/azure/dn790367.aspx)).

5. Se você ainda não fez isso, altere o diretório atual para a pasta ES-MultiNode.

### Executar o script CreateElasticSearchCluster
Antes de executar o script, abra o arquivo `azuredeploy-parameters.json` e verifique ou forneça valores para os parâmetros do script. Os parâmetros a seguir são fornecidos:

|Nome do parâmetro |Descrição|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Esse é o nome que será usado para criar o nome DNS (Sistema de Nomes de Domínio) publicamente visível para o cluster do Elasticsearch (anexando o domínio da região do Azure ao nome fornecido). Por exemplo, se esse valor de parâmetro for "myBigCluster" e a região do Azure escolhida for Oeste dos EUA, o nome DNS resultante do cluster será **myBigCluster.westus.cloudapp.azure.com**. <br /><br />Esse nome também servirá como uma raiz para nomes de vários artefatos associados com o cluster do Elasticsearch, como nomes de nó de dados.|
|storageAccountPrefix |O prefixo das contas de armazenamento que será criado para o cluster do Elasticsearch. <br /><br /> A versão atual do modelo usa uma conta de armazenamento compartilhada, mas isso pode mudar no futuro.|
|adminUsername |O nome da conta de administrador para gerenciar o cluster do Elasticsearch (as chaves SSH correspondentes serão geradas automaticamente).|
|dataNodeCount |O número de nós no cluster do Elasticsearch. A versão atual do script não faz distinção entre nós de dados e consultas; todos os nós executarão as duas funções.|
|dataDiskSize |O tamanho dos discos de dados (em gigabytes) que será alocado para cada nó de dados. Cada nó receberá quatro discos de dados, dedicados exclusivamente ao serviço Elasticsearch.|
|region |O nome da região do Azure em que o cluster do Elasticsearch deve estar localizado.|
|esClusterName |O nome interno do cluster do Elasticsearch. <br /><br />Esse valor precisa ser alterado do padrão, a menos que você planeje executar mais de um cluster do Elasticsearch na mesma rede virtual. Atualmente o modelo ES-MultiNode não dá suporte a isso.|
|esUserName esPassword |Credenciais do usuário que serão configuradas para ter acesso ao cluster do Elasticsearch (sujeito à autenticação básica HTTP).|

Agora você está pronto para executar os aplicativos. Emita o seguinte comando: ```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name>
``` onde `<es-group-name>` é o nome do grupo de recursos do Azure que irá conter todos os recursos de cluster.

>[AZURE.NOTE] Se você receber uma NullReferenceException do cmdlet Test-AzureResourceGroup, você esqueceu de fazer logon no Azure (`Add-AzureAccount`).

Se você receber um erro de execução do script e você determinar que o erro foi causado por um valor de parâmetro de modelo errado, corrija o arquivo de parâmetro e executar o script novamente com um nome de grupo de recursos diferente. Você também pode reutilizar o mesmo nome de grupo de recursos e fazer com que o script limpe o antigo adicionando o parâmetro `-RemoveExistingResourceGroup` à chamada de script.

### Resultado da execução do script CreateElasticSearchCluster
Depois de executar o script `CreateElasticSearchCluster` os seguintes artefatos principais serão criados. Para fins de clareza, vamos supor que você usou "myBigCluster" para o valor do parâmetro `dnsNameForLoadBalancerIP` e a região onde você criou o cluster é Oeste dos EUA.

|Artefato|Nome, local e comentários|
|----------------------------------|----------------------------------|
|Chave SSH para administração remota |arquivo myBigCluster.key (no diretório onde o CreateElasticSearchCluster foi executado). <br /><br />Esta é a chave que pode ser usada para conectar o nó de administração e (por meio do nó de administração) a nós de dados do cluster.|
|Nó de Admin |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />É uma VM dedicada para a administração remota do cluster do Elasticsearch, a única que permite conexões de SSH externas. Ela é executada na mesma rede virtual que todos os nós de cluster Elasticsearch, mas não executa serviços de Elasticsearch.|
|Nós de dados |myBigCluster1 ... myBigCluster*N* <br /><br />Nós de dados que estão executando serviços Elasticsearch e Kibana. Você pode se conectar via SSH para cada nó, mas somente via o nó de administração.|
|Cluster Elasticsearch |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Acima é o ponto de extremidade principal para o cluster do Elasticsearch (observe o sufixo /es). Ele é protegido por autenticação HTTP básica (as credenciais eram os parâmetros esUserName/esPassword especificados do modelo ES-MultiNode). O cluster também tem o plug-in principal instalado (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) para administração de cluster básica.|
|Serviço Kibana |http://myBigCluster.westus.cloudapp.azure.com <br /><br />O serviço Kibana está configurado para mostrar dados do cluster do Elasticsearch criado. Ele é protegido pelas mesmas credenciais de autenticação como o próprio cluster.|

## Captura de rastreamento dentro do processo versus fora do processo
Na introdução mencionamos duas maneiras fundamentais de coleta de dados de diagnóstico: dentro do processo e fora de processo. Cada uma tem suas vantagens e desvantagens.

As vantagens da **captura de rastreamento dentro do processo** incluem:

1. *Fácil Configuração e implantação*

    * A configuração de coleta de dados de diagnóstico é apenas uma parte da configuração do aplicativo. É fácil sempre mantê-la "sincronizada" com o restante do aplicativo.

    * A configuração por aplicativo ou por serviço é facilmente realizável.

    * A captura de rastreamento fora do processo normalmente requer a implantação e a configuração separadas do agente de diagnóstico, que é uma tarefa administrativa extra e uma possível fonte de erros. Geralmente, a tecnologia de agente específico permite apenas uma instância do agente de por máquina virtual (nó). Isso significa que a configuração para a coleção da configuração de diagnóstico é compartilhada entre todos os aplicativos e serviços em execução nesse nó.

2. *Flexibilidade*

    * O aplicativo pode enviar os dados sempre que eles forem necessários, desde que exista uma biblioteca de cliente que ofereça suporte ao sistema de armazenamento de dados de destino. Novos coletores podem ser adicionados conforme desejado.

    * Regras de captura complexa, filtragem e agregação de dados podem ser implementadas.

    * Uma capturar de rastreamento fora do processo geralmente é limitada pelos coletores de dados para os quais o agente oferece suporte. Alguns agentes são extensíveis.

3. *Acesso a dados de aplicativos internos e contexto*

    * O subsistema de diagnóstico em execução dentro do processo de serviço/do aplicativo pode facilmente aumentar os rastreamentos com informações contextuais.

    * Na abordagem fora de processo, os dados devem ser enviados a um agente por meio de um mecanismo de comunicação entre processos, como Rastreamento de Eventos para Windows. Isso pode impor limitações adicionais.

As vantagens da **captura de rastreamento fora do processo** incluem:

1. *A capacidade de monitorar aplicativos e coletar despejos de memória*

    * A captura de rastreamento dentro do processo pode não ser bem-sucedida se o aplicativo não puder ser iniciado ou falhar. Um agente independente tem muito mais chances de capturar informações de solução de problemas cruciais.<br /><br />

2. *Maturidade, eficiência e desempenho comprovados*

    * Um agente desenvolvido pelo fornecedor da plataforma (como o agente do Diagnóstico do Microsoft Azure) foi submetido a rigorosos testes em situações adversas.

    * Com a captura de rastreamento dentro do processo deve ser tomado cuidado para garantir que a atividade de envio de dados de diagnóstico de um processo de aplicativo não interfere nas tarefas principais do aplicativo ou apresenta problemas de desempenho ou intervalo. Um agente independente em execução é menos propenso a esses problemas e é geralmente especificamente concebido para limitar seu impacto no sistema.

Obviamente, é possível combinar e se beneficiar de ambas as abordagens. Na verdade, essa pode ser a melhor solução para muitos aplicativos.

Aqui, utilizaremos a **biblioteca Microsoft.Diagnostic.Listeners** e a captura de rastreamento dentro do processo para enviar dados de um aplicativo do Service Fabric para um cluster do Elasticsearch.

## Usar a biblioteca Listeners para enviar dados de diagnóstico para o Elasticsearch
A biblioteca Microsoft.Diagnostic.Listeners faz parte do aplicativo Service Fabric de exemplo PartyCluster. Para usá-lo:

1. Baixe [o exemplo PartyCluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) do GitHub.

2. Copie os projetos Microsoft.Diagnostics.Listeners e Microsoft.Diagnostics.Listeners.Fabric (pastas inteiras) do diretório do exemplo PartyCluster para a pasta de solução do aplicativo que deve enviar os dados para o Elasticsearch.

3. Abra a solução de destino, clique com o botão direito do mouse no nó de solução no Gerenciador de Soluções e escolha **Adicionar Projeto Existente**. Adicione o projeto Microsoft.Diagnostics.Listeners à solução. Repita o mesmo para o projeto Microsoft.Diagnostics.Listeners.Fabric.

4. Adicione uma referência de projeto de seus projetos de serviço aos dois projetos adicionados. (Cada serviço que deve enviar dados para o Elasticsearch deve referenciar Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric).

    ![Referências de projeto às bibliotecas Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric][1]

### Preview de novembro de 2015 do Service Fabric e do pacote NuGet Microsoft.Diagnostics.Tracing
Os aplicativos criados com a preview de novembro de 2015 do Service Fabric são voltados para **.NET Framework 4.5.1**. Esta é a versão mais recente do .NET Framework com suporte do Azure no momento da versão de preview. Infelizmente, essa versão do Framework não tem determinadas APIs EventListener que a biblioteca Microsoft.Diagnostics.Listeners precisa. Como o EventSource (o componente que forma a base de APIs de registro em aplicativos do Fabric) e o EventListener são rigidamente integrados, cada projeto que usa a biblioteca Microsoft.Diagnostics.Listeners deve usar uma implementação alternativa de EventSource. Isso é fornecido pelo **pacote do Microsoft.Diagnostics.Tracing NuGet** criado pela Microsoft. O pacote é totalmente compatível com versões anteriores do EventSource incluído na estrutura, portanto, não deve ser necessária nenhuma alteração de código que não as alterações de namespace referenciado.

Para começar a usar a implementação Microsoft.Diagnostics.Tracing da classe EventSource, siga estas etapas para cada projeto de serviço que precisa para enviar dados ao Elasticsearch:

1. Clique com o botão direito do mouse no projeto de serviço e escolha **Gerenciar Pacotes NuGet**.

2. Alterne para a origem do pacote nuget.org (se ainda não estiver selecionada) e pesquise **Microsoft.Diagnostics.Tracing**.

3. Instale o pacote `Microsoft.Diagnostics.Tracing.EventSource` (e suas dependências).

4. Abra arquivo **ServiceEventSource.cs** ou **ActorEventSource.cs** no seu projeto de serviço e substitua a diretiva `using System.Diagnostics.Tracing` na parte superior do arquivo pela diretiva `using Microsoft.Diagnostics.Tracing`.

Essas etapas não serão necessárias uma vez que o **.NET Framework 4.6** tem suporte pelo Microsoft Azure.

### Configuração e instanciação de ouvinte de Elasticsearch
A etapa final para enviar dados de diagnóstico para o Elasticsearch é criar uma instância de `ElasticSearchListener` e configurá-la com dados de conexão do Elasticsearch. O ouvinte capturará automaticamente todos os eventos gerados por meio de classes EventSource definidas no projeto de serviço. Ele precisa estar ativo durante o tempo de vida do serviço, portanto, o melhor lugar para criá-lo é no código de inicialização do serviço. Aqui está como o código de inicialização para um serviço sem estado pode parecer após as alterações necessárias (as adições estão destacadas nos comentários começando com `****`):

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    public class Program
    {
        public static void Main(string[] args)
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {

                    // **** Instantiate ElasticSearchListener
                    var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                    ElasticSearchListener esListener = null;
                    if (configProvider.HasConfiguration)
                    {
                        esListener = new ElasticSearchListener(configProvider);
                    }

                    // This is the name of the ServiceType that is registered with FabricRuntime.
                    // This name must match the name defined in the ServiceManifest. If you change
                    // this name, please change the name of the ServiceType in the ServiceManifest.
                    fabricRuntime.RegisterServiceType("Stateless1Type", typeof(Stateless1));

                    ServiceEventSource.Current.ServiceTypeRegistered(
						Process.GetCurrentProcess().Id,
						typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);

                    // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                    GC.KeepAlive(esListener);

                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Os dados de conexão do Elasticsearch devem ser colocados em uma seção separada no arquivo de configuração de serviço (**PackageRoot\\Config\\Settings.xml**). O nome da seção deve corresponder ao valor passado para o construtor `FabricConfigurationProvider`, por exemplo:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Os valores de `serviceUri`, `userName` e `password` correspondem ao endereço do ponto de extremidade do cluster Elasticsearch, nome de usuário e senha do Elasticsearch, respectivamente. `indexNamePrefix` é o prefixo para índices Elasticsearch; a biblioteca Microsoft.Diagnostics.Listeners cria um novo índice para seus dados diariamente.

### Verificação
É isso! Agora sempre que o serviço for executado, será iniciado enviando rastreamentos para o serviço de Elasticsearch especificado na configuração. Você pode verificar isso abrindo a interface do usuário Kibana associada à instância do Elasticsearch de destino (no nosso exemplo o endereço da página seria http://myBigCluster.westus.cloudapp.azure.com/) e a verificando se os índices com o nome do prefixo escolhido para a instância `ElasticSearchListener` foram realmente criados e preenchidos com dados.

![Kibana mostrando os eventos do aplicativo PartyCluster][2]

## Próximas etapas
- [Saiba mais sobre o diagnóstico e monitoramento de um serviço da Malha de Serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!---HONumber=AcomDC_0128_2016-->