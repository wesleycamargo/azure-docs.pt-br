<properties
   pageTitle="Usando o ElasticSearch como um repositório de rastreamento do aplicativo da Malha de Serviço | Microsoft Azure"
   description="Este artigo descreve como os aplicativos da Malha de Serviço podem usar o ElasticSearch e o Kibana para armazenar, indexar e pesquisar os rastreamentos do aplicativo (logs)"
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
   ms.date="11/02/2015"
   ms.author="karolz@microsoft.com"/>

# Usando ElasticSearch como um repositório de rastreamento do aplicativo de Malha de Serviços
## Introdução
Este artigo descreve como os aplicativos do [Serviço de Malha](http://azure.microsoft.com/documentation/services/service-fabric/) podem usar **ElasticSearch** e **Kibana** para armazenamento de rastreamento do aplicativo, indexação e pesquisa. [ElasticSearch](https://www.elastic.co/guide/index.html) é um uma engine de código-fonte aberto, distribuída e dimensionável em tempo real de pesquisa e análise adequada para esta tarefa e pode ser instalado em máquinas virtuais Windows ou Linux em execução no Microsoft Azure. ElasticSearch pode processar de maneira muito eficiente rastreamentos *estruturados* produzidos usando tecnologias como **Event Tracing for Windows (ETW)**.

O ETW é usado pelo tempo de execução de Malha de Serviço para saber a origem das informações de diagnóstico (rastreamentos) e é o método recomendado para os aplicativos da Malha de Serviço fazerem o mesmo com as suas informações de diagnóstico. Isso possibilita a correlação entre os rastreamentos fornecidos pelo aplicativo e pelo tempo de execução e facilita a solução de problemas. Os modelos de projeto de Malha de Serviço no Visual Studio incluem uma API de log (baseada na classe .NET **EventSource**) que emite rastreamentos ETW por padrão. Para obter uma visão geral de rastreamento do aplicativo de Service Fabric usando o ETW, consulte [este artigo](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Para os rastreamentos que aparecerão em ElasticSearch, eles precisam ser capturados em nós de cluster de Malha de Serviços em tempo real (enquanto o aplicativo estiver sendo executado) e enviada ao ponto de extremidade ElasticSearch. Há duas opções principais para a captura de rastreamento:

+ **Captura de rastreamento dentro do processo**  
o aplicativo ou, mais precisamente, o processo de serviço é responsável por enviar os dados de diagnóstico para o repositório de rastreamento (ElasticSearch).

+ **Captura de rastreamento fora do processo**  
um agente separado captura rastreamentos de processo(s) de serviço e envia-os para o repositório de rastreamento.

No restante do artigo, descreveremos como configurar o ElasticSearch no Azure, discutir os prós e contras de ambas as opções de captura e explicar como configurar um serviço de Malha para enviar dados para o ElasticSearch.


## Configurando o Elasticsearch no Azure
A maneira mais simples de configurar o serviço ElasticSearch no Azure é por meio de [**modelos de ARM do Azure**](../resource-group-overview.md). Um [modelo de ARM de início rápido para ElasticSearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) abrangente está disponível no repositório de modelos de início rápido do Azure. Esse modelo usa contas de armazenamento separadas para unidades de escala (grupos de nós) e pode provisionar nós de servidor e de cliente separados com configurações diferentes, com vários números de discos de dados anexados.

Neste artigo, usaremos outro modelo chamado **ES-MultiNode** da [ramificação Microsoft Patterns & Practices ELK](https://github.com/mspnp/semantic-logging/tree/elk/). Esse modelo é um pouco mais fácil de usar e cria um cluster ElasticSearch protegido pela autenticação básica HTTP por padrão. Antes de prosseguir, baixe o [Repositório Microsoft P&P "elk"](https://github.com/mspnp/semantic-logging/tree/elk/) do GitHub para seu computador (clonando o repositório ou baixar um arquivo ZIP). O modelo ES-MultiNode está localizado na pasta com o mesmo nome.  
>[AZURE.NOTE] O modelo ES-MultiNode e os scripts associados no momento oferecem suporte à versão 1.7 do ElasticSearch. O suporte para ElasticSearch 2.0 será incluído em uma data posterior.

### Preparando uma máquina para executar scripts de instalação ElasticSearch
É a maneira mais fácil de usar o modelo ES-MultiNode por meio de um script do PowerShell fornecido chamado `CreateElasticSearchCluster`. Para usar esse script, você precisa instalar os módulos do Azure PowerShell e uma ferramenta chamada openssl. Essa ferramenta é necessária para criar uma chave SSH que pode ser usada para administrar o cluster ElasticSearch remotamente.

Observação: o script `CreateElasticSearchCluster` foi desenvolvido para facilitar o uso do modelo ES-MultiNode a partir de um computador Windows. É possível usar o modelo em um computador diferente do Windows, mas esse cenário está além do escopo deste artigo.

1. Se você ainda não instalou, instale os [**módulos do Azure Powershell**](http://go.microsoft.com/fwlink/p/?linkid=320376). Quando solicitado, clique em Executar, depois em Instalar.  
>[AZURE.NOTE]O Azure PowerShell está passando por uma grande mudança com a versão 1.0 do Azure PowerShell. O CreateElasticSearchCluster é projetado atualmente para funcionar com o Azure PowerShell 0.9.8 e não oferece suporte à visualização do Azure PowerShell 1.0. Um script compatível com o Azure PowerShell 1.0 será fornecido posteriormente.

2. A ferramenta **openssl** está incluída na distribuição de [**Git para Windows**](http://www.git-scm.com/downloads). Se você não tiver feito isso, instale o [Git para Windows](http://www.git-scm.com/downloads) agora (opções de instalação padrão estão OK).

3. Supondo que o Git foi instalado, mas não incluído no caminho do sistema, abra a janela do Microsoft Azure PowerShell e execute os seguintes comandos:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Substitua o `<Git installation folder>` pelo local do Git em seu computador; o padrão é *"C:\\Program Files\\Git"*. Observe o caractere ponto e vírgula no início do primeiro caminho.

4. Certifique-se de que você está conectado no Azure (através do cmdlet [*Add-AzureAccount*](https://msdn.microsoft.com/library/azure/dn790372.aspx)) e que você selecionou a assinatura deve ser usada para criar o cluster ElasticSearch ([*Select-AzureSubscription*](https://msdn.microsoft.com/library/azure/dn790367.aspx)).

5. Se você ainda não fez isso, altere o diretório atual para a pasta ES-MultiNode.

### Executando o script CreateElasticSearchCluster
Antes de executar o script, abra o arquivo `azuredeploy-parameters.json` e verifique ou forneça valores para os parâmetros do script. Os parâmetros a seguir são fornecidos:

|Nome do Parâmetro |Descrição|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Esse é o nome que será usado para criar o nome DNS publicamente visível para o cluster ElasticSearch (anexando o domínio da região do Azure ao nome fornecido). Por exemplo, se esse valor de parâmetro é "myBigCluster" e a região do Azure escolhida é Oeste dos EUA, o nome DNS resultante para o cluster será myBigCluster.westus.cloudapp.azure.com. <br /><br />Esse nome também servirá como uma raiz para nomes de vários artefatos associados com o cluster ElasticSearch, como nomes de nó de dados.|
|storageAccountPrefix |O prefixo para a(s) conta(s) de armazenamento que será criado para o cluster ElasticSearch. <br /><br /> A versão atual do modelo usa uma conta de armazenamento compartilhado, mas que pode ser alterada no futuro.|
|adminUsername |O nome da conta de administrador para gerenciar o cluster ElasticSearch (as chaves SSH correspondentes serão geradas automaticamente)|
|dataNodeCount |O número de nós no cluster ElasticSearch. A versão atual do script não faz distinção entre nós de dados e consultas; todos os nós executarão as duas funções.|
|dataDiskSize |O tamanho dos discos de dados (em GB) que será alocado para cada nó de dados. Cada nó receberá 4 discos de dados, dedicados exclusivamente ao serviço ElasticSearch.|
|region |O nome da região do Azure onde o cluster ElasticSearch deve estar localizado.|
|esClusterName |O nome interno do cluster ElasticSearch. <br /><br />Esse valor precisa ser alterado do padrão, a menos que você planeje executar mais de um cluster de ElasticSearch na mesma rede virtual, que atualmente não é suportada pelo modelo ES-MultiNode.|
|esUserName esPassword |Credenciais do usuário que será configurado para ter acesso ao cluster de ES (sujeito à autenticação básica HTTP).|

Agora você está pronto para executar os aplicativos. Emita o seguinte comando: ```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name>
``` onde `<es-group-name>` é o nome do grupo de recursos do Azure que irá conter todos os recursos de cluster.

>[AZURE.NOTE]Se você receber uma NullReferenceException do cmdlet teste-AzureResourceGroup, você esqueceu de fazer logon no Azure (`Add-AzureAccount`).

Se você receber um erro de execução do script e você determinar que o erro foi causado por um valor de parâmetro de modelo errado, corrija o arquivo de parâmetro e executar o script novamente com um nome de grupo de recursos diferente. Você também pode reutilizar o mesmo nome de grupo de recursos e fazer com que o script limpe o antigo adicionando o parâmetro `-RemoveExistingResourceGroup` para a chamada de script.

### Resultado da execução do script CreateElasticSearchCluster
Depois de executar o script `CreateElasticSearchCluster` os seguintes artefatos principais serão criados. Para fins de clareza supomos que você usou 'myBigCluster' para o valor do parâmetro `dnsNameForLoadBalancerIP` e a região onde você criou o cluster é Oeste dos EUA.

|Artefato|Nome, Local e Comentários|
|----------------------------------|----------------------------------|
|Chave SSH para administração remota |arquivo myBigCluster.key (no diretório onde o CreateElasticSearchCluster foi executado). <br /><br />Esta é a chave que pode ser usada para conectar o nó de administração e (por meio do nó de administração) a nós de dados do cluster.|
|Nó de Admin |myBigCluster admin.westus.cloudapp.azure.com <br /><br />É uma VM dedicada para administração de cluster ElasticSearch remota, o único que permite conexões externas do SSH. Ele é executado na mesma rede virtual que todos os nós de cluster ElasticSearch, mas não executa serviços de ElasticSearch.|
|Nós de dados |myBigCluster1 ... myBigCluster*N* <br /><br />Nós de dados que estão executando serviços ElasticSearch e Kibana. Você pode se conectar via SSH para cada nó, mas somente via o nó de administração.|
|Cluster Elasticsearch |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Acima é o ponto de extremidade principal para o cluster ElasticSearch (observe o sufixo /es). Ele é protegido por autenticação HTTP básica (as credenciais eram parâmetros esUserName/esPassword especificados do modelo ES-MultiNode). O cluster também tem o plug-in principal instalado (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) para administração de cluster básico.|
|Serviço Kibana |http://myBigCluster.westus.cloudapp.azure.com <br /><br />O serviço Kibana está configurado para mostrar dados do cluster ElasticSearch criado; ele é protegido pelas mesmas credenciais de autenticação que o cluster em si.|

## Captura de rastreamento dentro do processo versus fora do processo
Na introdução mencionamos duas maneiras fundamentais de coleta de dados de diagnóstico: dentro do processo e fora de processo. Cada uma tem suas vantagens e desvantagens.

As vantagens da **captura de rastreamento dentro do processo** incluem:

1. *Fácil Configuração e implantação*

    * A configuração de coleta de dados de diagnóstico é apenas parte da configuração do aplicativo e ele é fácil de manter sempre "sincronizado" com o restante do aplicativo.

    * A configuração por aplicativo ou por serviço é facilmente realizável.

    * A captura de rastreamento fora do processo normalmente requer a implantação e a configuração separadas do agente de diagnóstico, que é a tarefa administrativa extra e uma fonte de erros. Geralmente, a tecnologia de agente específico permite apenas uma instância do agente por máquina virtual (nó), o que significa que a configuração para a coleta da configuração de diagnóstico é compartilhada entre todos os aplicativos e serviços em execução nesse nó.

2. *Flexibilidade*

    * O aplicativo pode enviar os dados sempre que eles forem necessários, desde que exista uma biblioteca de cliente que ofereça suporte ao sistema de armazenamento de dados de destino. Novos coletores podem ser adicionados conforme desejado.

    * Regras de captura complexa, filtragem e agregação de dados podem ser implementadas.

    * Uma capturar de rastreamento fora do processo geralmente é limitada pelos coletores de dados para os quais o agente oferece suporte. Alguns agentes são extensíveis.

3. *Acesso a dados de aplicativos internos e contexto*

    * O subsistema de diagnóstico em execução dentro do processo de serviço/do aplicativo pode facilmente aumentar os rastreamentos com informações contextuais.

    * Na abordagem fora de processo, os dados devem ser enviados a um agente por meio de um mecanismo de comunicação entre processos, como Event Tracing for Windows (ETW). Isso pode impor limitações adicionais.

As vantagens da **captura de rastreamento fora do processo** incluem:

1. *Capacidade de monitorar aplicativos e coletar despejos de memória*

    * A captura de rastreamento dentro do processo não ser bem sucedida se o aplicativo falha ao iniciar ou falhar. Um agente independente tem muito mais chances de capturar informações de solução de problemas cruciais.<br /><br />

2. *Maturidade, eficiência e desempenho comprovados*

    * Um agente desenvolvido pelo fornecedor da plataforma (como o agente do Microsoft Azure Diagnostics) foi submetido a rigorosos testes em situações adversas.

    * Com a captura de rastreamento dentro do processo deve ser tomado cuidado para garantir que a atividade de envio de dados de diagnóstico a partir de um processo de aplicativo não interfere com as tarefas principais do aplicativo e não apresenta problemas de desempenho ou intervalo. Um agente independente em execução é menos propenso a esses problemas e é geralmente especificamente concebido para limitar seu impacto no sistema.

Certamente é possível combinar e se beneficiar das duas abordagens; na verdade é a melhor solução para muitos aplicativos.

Neste artigo utilizaremos a **biblioteca Microsoft.Diagnostic.Listeners** e a captura de rastreamento dentro do processo para enviar dados de um aplicativo de Malha de Serviço para um cluster ElasticSearch.

## Usando a biblioteca Listeners para enviar dados de diagnóstico para o ElasticSearch
A biblioteca Microsoft.Diagnostic.Listeners faz parte do aplicativo de Malha de Serviço de exemplo de Cluster de Terceiros. Para usá-lo:

1. Baixe [o exemplo de Cluster de Terceiros](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) do GitHub.

2. Copie os projetos Microsoft.Diagnostics.Listeners e Microsoft.Diagnostics.Listeners.Fabric (pastas inteiras) do diretório de exemplo de Cluster de Terceiros para a pasta de solução do aplicativo que deve enviar os dados para o ElasticSearch.

3. Abra a solução de destino, clique com o botão direito no nó de solução em Solution Explorer e escolha "Adicionar projeto existente". Adicione o projeto Microsoft.Diagnostics.Listeners à solução. Repita o mesmo para o projeto Microsoft.Diagnostics.Listeners.Fabric.

4. Adicione uma referência de projeto do(s) seu(s) projeto(s) de serviço aos dois projetos adicionados (cada serviço que deve enviar dados para o ElasticSearch deve referenciar Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric).

    ![Refererências de projeto às bibliotecas Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric][1]

### Visualização de novembro de 2015 da Malha de Serviço e do pacote NuGet Microsoft.Diagnostics.Tracing
Os aplicativos criados com a visualização de novembro de 2015 da Malha de Serviço são voltadas para o **.NET Framework 4.5.1** pois esta é a versão mais recente do .NET Framework com suporte pelo Azure no momento da versão de visualização. Infelizmente, essa versão do framework não tem determinadas APIs EventListener que a biblioteca Microsoft.Diagnostics.Listeners precisa. Como o EventSource (o componente que forma a base de APIs de registro em aplicativos de Malha) e o EventListener são rigidamente integrados, cada projeto que usa a biblioteca Microsoft.Diagnostics.Listeners deve usar uma implementação alternativa de EventSource, que é fornecido pelo **pacote Nuget Microsoft.Diagnostics.Tracing NuGet**, criado pela Microsoft. Esse pacote é totalmente compatível com o EventSource incluído na estrutura, portanto, nenhuma alteração de código deve ser necessária que não sejam as alterações de namespace referenciado.

Para começar a usar a implementação Microsoft.Diagnostics.Tracing da classe EventSource siga estas etapas para cada projeto de serviço que precisa para enviar dados ao ElasticSearch:

1. Clique com o botão direito do mouse no projeto de serviço e escolha “Gerenciar Pacotes NuGet”

2. Alterne para a origem do pacote nuget.org (se ainda não estiver selecionada) e procure por "Microsoft.Diagnostics.Tracing"

3. Instale o pacote `Microsoft.Diagnostics.Tracing.EventSource` (e suas dependências)

4. Abra arquivo ServiceEventSource.cs ou ActorEventSource.cs no seu projeto de serviço e substitua a diretiva `using System.Diagnostics.Tracing` na parte superior do arquivo pela diretiva `using Microsoft.Diagnostics.Tracing`.

Essas etapas não será necessárias uma vez que o **.NET Framework 4.6** é suportado pelo Microsoft Azure.

### Configuração e a instanciação de ouvinte de ElasticSearch
A etapa final necessária para enviar dados de diagnóstico para o ElasticSearch é criar uma instância de `ElasticSearchListener` e configurá-la com dados de conexão do ElasticSearch. O ouvinte capturará automaticamente todos os eventos gerados por meio de classes EventSource definidas no projeto de serviço. Ele precisa estar ativo durante o tempo de vida do serviço, portanto, o melhor lugar para criá-lo é no código de inicialização do serviço. Aqui está como o código de inicialização para um serviço sem estado pode parecer após as alterações necessárias (as adições estão destacadas nos comentários começando com `****`):

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

Os dados de conexão do ElasticSearch devem ser colocados em uma seção separada no arquivo de configuração de serviço (PackageRoot\\Config\\Settings.xml). O nome da seção deve corresponder ao valor passado para o construtor `FabricConfigurationProvider`, por exemplo:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Os valores de `serviceUri`, `userName` e `password` correspondem ao endereço de ponto de extremidade do cluster ElasticSearch, do nome de usuário e senha do ElasticSearch, respectivamente. `indexNamePrefix` é o prefixo para índices ElasticSearch; a biblioteca Microsoft.Diagnostics.Listeners cria um novo índice para seus dados diariamente.

### Verificação
É isso! Agora sempre que o serviço for executado, será iniciado enviando rastreamentos para o serviço de ElasticSearch especificado na configuração. Você pode verificar isso abrindo a interface de usuário Kibana associada à instância de ElasticSearch de destino (no nosso exemplo o endereço da página seria http://myBigCluster.westus.cloudapp.azure.com/) e a verificação de índices com o nome do prefixo escolhido para a instância `ElasticSearchListener` são na verdade criados e preenchidos com dados.

![Kibana mostrando os eventos do aplicativo PartyCluster][2]

## Próximas etapas
- [Saiba mais sobre o diagnóstico e monitoramento de um serviço da Malha de Serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!---HONumber=AcomDC_1217_2015-->
