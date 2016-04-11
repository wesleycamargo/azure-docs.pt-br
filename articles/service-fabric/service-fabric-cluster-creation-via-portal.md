<properties
   pageTitle="Criar um cluster do Service Fabric no Portal do Azure | Microsoft Azure"
   description="Crie um cluster do Service Fabric no Portal do Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/28/2016"
   ms.author="chackdan"/>


# Criar um cluster do Service Fabric no Portal do Azure

Esta página ajuda você a configurar um cluster do Service Fabric. Sua assinatura precisa ter uma quantidade suficiente de núcleos para implantar as VMs de IaaS que comporão esse cluster.


## Pesquise o recurso de cluster do Service Fabric

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Clique em **+ Novo** para adicionar um novo modelo de recurso. Procure o modelo no **Marketplace** em **Tudo**. Ele é chamado de **Cluster do Service Fabric**.

    a. No nível superior, clique em **Marketplace**.

    b. Em **Tudo**, insira "Fabric" e pressione Enter. Às vezes, o filtro automático não funciona, portanto, é necessário pressionar Enter. ![Captura de tela da pesquisa pelo modelo de cluster do Service Fabric no Portal do Azure.][SearchforServiceFabricClusterTemplate]

3. Selecione **Cluster do Service Fabric** na lista.

4. Navegue até a folha **Cluster do Service Fabric** e clique em **Criar**.

5. Você agora verá uma folha **Criar cluster do Service Fabric** que lista quatro etapas.

## Etapa 1: Básico

Na folha Básico, você precisa fornecer os detalhes básicos do seu cluster.

1. Insira o nome do seu cluster.

2. Escolha o **Nome de Usuário** e a **Senha** da área de trabalho remota da VM.

3. Selecione a **Assinatura** desejada para a implantação do cluster, especialmente se você tiver várias assinaturas.

4. Crie um **novo grupo de recursos**. É melhor dar a ele o mesmo nome do cluster, pois ajuda a encontrá-los mais tarde, especialmente quando você estiver tentando fazer alterações em sua implantação ou excluir o cluster.

    >[AZURE.NOTE] Embora você possa optar por usar um grupo de recursos existente, é uma boa prática criar um novo grupo de recursos. Isso facilita a exclusão dos clusters que não são necessários.

 	![Captura de tela da criação de um novo grupo de recursos.][CreateRG]


5. Selecione um **Local** na lista suspensa. O valor padrão é **Oeste dos EUA**. Pressione OK.

## Etapa 2: Configurar o cluster

10. Deixe-me primeiro lhe dizer o que é um **tipo de nó**. O tipo de nó pode ser visto como algo equivalente a funções nos serviços de nuvem. Os tipos de nó definem os tamanhos e o número de VMs e suas propriedades. O cluster pode ter mais de um tipo de nó, mas o tipo de nó primário (o primeiro que você define no portal) deve ter pelo menos cinco VMs. Esse é o tipo de nó onde os serviços do sistema do Service Fabric são colocados. Considere o seguinte ao decidir sobre a necessidade de vários tipos de nó:

	* O aplicativo que você quer implantar contém um serviço front-end e um serviço back-end. Você quer colocar o serviço front-end em VMs menores (tamanhos como D2) e elas têm portas abertas para a Internet, mas você quer colocar o serviço de back-end, que usa computação de forma intensiva, em VMs maiores (com tamanhos como D4, D6, D15 e assim por diante) que não são voltadas para a Internet.

	* Embora seja possível colocar os dois serviços em um tipo de nó, recomendamos que você os coloque em um cluster com dois tipos de nós. Cada tipo de nó pode ter propriedades distintas, por exemplo, conectividade com a Internet, tamanho da VM e o número de VMs, que podem ser dimensionadas de forma independente.

	* Defina primeiro um tipo de nó que terá pelo menos cinco VMs. Os outros tipos de nó podem ter um mínimo de uma VM.

13.  Para configurar seu tipo de nó:

	a. Escolha um nome para o tipo de nó (um a 12 caracteres contendo somente letras e números).

	b. O tamanho mínimo das VMs para o tipo de nó primário é determinado pela camada de durabilidade que você escolhe para o cluster. O padrão para a camada de durabilidade é Bronze. Leia mais no documento sobre como [escolher a durabilidade e a confiabilidade do cluster do Service Fabric](service-fabric-cluster-capacity.md).

	b. Selecione o tamanho/tipo de preço da VM. O padrão é o Padrão D4, mas se você for usar esse cluster apenas para testar seu aplicativo, selecione D2 ou qualquer tamanho menor de VM.

	c. O número mínimo de VMs para o tipo de nó primário é determinado pela camada de confiabilidade que você escolhe. O padrão para a camada de confiabilidade é Prata. Leia mais no documento sobre como [escolher a durabilidade e a confiabilidade do cluster do Service Fabric](service-fabric-cluster-reliability-and-durability.md).

	c. Escolha o número de VMs para o tipo de nó. Você pode expandir ou reduzir o número de VMs em um tipo de nó posteriormente, mas no tipo de nó primário, o mínimo é determinado pelo nível de confiabilidade que você escolheu. Outros tipos de nó podem ter um mínimo de uma VM.


  	![Captura de tela de criação de um tipo de nó.][CreateNodeType]

9. Se você planeja implantar seus aplicativos no cluster imediatamente, adicione portas que você queira abrir para seus aplicativos em um tipo de nó **Portas de aplicativo** (ou nos tipos de nó que você criou). Posteriormente, você pode adicionar portas ao tipo de nó modificando o balanceador de carga que está associado a esse tipo de nó. (Adicione uma investigação e, em seguida, adicione a investigação às regras do balanceador de carga). Fazer isso nesse momento é um pouco mais fácil, já que a automação do portal adicionará as investigações e as regras necessárias ao balanceamento de carga:

	a. Você pode encontrar as portas do aplicativo em seus manifestos de serviço, que fazem parte do pacote de aplicativos. Acesse cada um dos aplicativos, abra os manifestos de serviço e anote todos os pontos de extremidade de entrada que seus aplicativos precisam para se comunicar com o mundo exterior.

	b. Adicione todas as portas, separadas por vírgulas, no campo **Pontos de extremidade de entrada do aplicativo**. Por padrão, o ponto de extremidade de conexão do cliente TCP é 19000, portanto não é necessário especificá-lo. Por exemplo, o aplicativo de exemplo WordCount precisa da porta 83 aberta. Você encontrará isso no arquivo servicemanifest.xml em seu pacote de aplicativos. (Pode haver mais de um arquivo servicemanifest.xml.)

    c. A maioria dos aplicativos de exemplo usa as portas 80 e 8081. Portanto, adicione-as se você planeja implantar exemplos para esse cluster. ![Portas][Ports]

10. Não é necessário configurar as **Propriedades de Posicionamento**, pois uma propriedade de posicionamento padrão de "NodeTypeName" é adicionada pelo sistema. Você pode adicionar mais se o aplicativo exigir.

11. Você não precisa configurar **Propriedades de Capacidade**, mas é recomendável, pois você pode usar essa opção em seus aplicativos para relatar a carga ao sistema e, assim, influenciar as decisões de posicionamento e balanceamento de recursos que o sistema faz no cluster do Service Fabric. Leia mais sobre o balanceamento de recursos do Service Fabric começando por [este documento](service-fabric-cluster-resource-manager-architecture.md).

12. Continue as etapas acima para todos os tipos de nó.

14. Configure o **diagnóstico** do cluster. Por padrão, os diagnósticos são habilitados no cluster para ajudar na solução de problemas. Se quiser desabilitar o diagnóstico, alterne o **Status** para **Desligado**. **Não** é recomendável desligar o diagnóstico.

15. Opcionalmente: defina as **configurações do cluster do Service Fabric**. Com essa opção avançada, você pode alterar as configurações padrão do cluster do Service Fabric. Recomendamos que você não altere os padrões, a menos que tenha certeza de que seu aplicativo ou cluster exige isso.



## Etapa 3: Configurar a segurança

Neste momento, o Service Fabric oferece suporte à proteção de clusters apenas por meio de um certificado X509. Antes de iniciar esse processo, será necessário carregar seu certificado no Cofre da Chave. Consulte [Segurança do cluster do Service Fabric](service-fabric-cluster-security.md) para saber como fazer isso.

Proteger seu cluster é opcional, mas é altamente recomendável. Se você optar por não proteger o cluster, alterne o **Modo de Segurança** para **Não Seguro**. Observação - você **não** poderá atualizar um cluster não seguro para seguro em um momento posterior.

As considerações e instruções de segurança estão documentadas em [Segurança do cluster do Service Fabric](service-fabric-cluster-security.md).

![Captura de tela das configurações de segurança no Portal do Azure][SecurityConfigs]


## Etapa 4: Concluir a criação do cluster

Para concluir a criação do cluster, clique em **Resumo** para ver as configurações que você forneceu ou baixe o modelo do Azure Resource Manager que será usado para implantar o cluster. Depois de ter fornecido as configurações obrigatórias, o botão **OK** será habilitado e você poderá começar o processo de criação do cluster clicando nele.

Você pode ver o progresso da criação nas notificações. (Clique no ícone de "Sino" próximo à barra de status no canto superior direito da tela). Se você clicou em **Fixar no Quadro Inicial** durante a criação do cluster, verá **Implantando o Cluster do Service Fabric** fixado na **Tela Inicial**.

![Captura de tela da Tela Inicial exibindo "Implantação do cluster do Service Fabric".][Notifications]

## Exibir o status do cluster

Depois que o cluster for criado, você poderá inspecioná-lo no portal:

1. Vá para **Procurar** e clique em **Clusters do Service Fabric**.

2. Localize o cluster e clique nele. ![Captura de tela da localização do cluster no portal.][BrowseCluster]

3. Agora você pode ver os detalhes do cluster no painel, inclusive o endereço IP público do cluster. Passar o mouse sobre o **Endereço IP Público do Cluster** abrirá uma área de transferência, na qual você pode clicar para copiar o endereço. ![Captura de tela dos detalhes do cluster no painel de controle.][ClusterDashboard]

  A seção **Monitor do Nó** na folha do painel do cluster indica o número de VMs íntegras e não íntegras. Você pode encontrar mais detalhes sobre a integridade do cluster em [Introdução ao modelo de integridade do Service Fabric](service-fabric-health-introduction.md).

>[AZURE.NOTE] Os clusters de Service Fabric exigem um determinado número de nós que devem estar ativos em todos os momentos para manter a disponibilidade e preservar o estado - conhecido como "manter o quórum". Consequentemente, em geral não é seguro desligar todos os computadores no cluster, a menos que você tenha executado primeiro um [backup completo do estado](service-fabric-reliable-services-backup-restore.md).

## Conectar-se ao cluster e implantar um aplicativo

Agora que a configuração do cluster foi concluída, você pode se conectar e começar a implantar aplicativos. Comece iniciando o Windows PowerShell em uma máquina que tenha o SDK do Service Fabric instalado. Em seguida, conecte-se ao cluster, execute um dos seguintes conjuntos de comandos do PowerShell, dependendo se você criou um cluster seguro ou não.

### Conectar a um cluster não seguro

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
    ```

### Conectar a um cluster seguro

    1. Run the following to set up the certificate on the machine that you are going to use to run the "Connect-serviceFabricCluster" PowerShell command.

        ```powershell
        Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
                -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
                -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
        ```

    2. Run the following PowerShell command to connect to a secure cluster. The certificate details are the same ones that you gave on the portal.

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
                  -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
                  -StoreLocation CurrentUser -StoreName My
        ```

        For example, the PowerShell command above should look similar to the following:

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
                  -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
                  -StoreLocation CurrentUser -StoreName My
        ```

### Implantar seu aplicativo
Agora que você está conectado, execute os seguintes comandos para implantar seu aplicativo, substituindo os caminhos mostrados pelos adequados em seu computador. O exemplo a seguir implanta o aplicativo de exemplo de contagem de palavras.

1. Copie o pacote no cluster conectado anteriormente.

    ```powershell
    $applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
    ```

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
    ```
2. Registre seu tipo de aplicativo no Service Fabric.

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
    ```

3. Crie uma nova instância no tipo de aplicativo que você acabou de registrar.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
    ```

4. Agora, abra o navegador de sua escolha e conecte-se ao ponto de extremidade que o aplicativo está escutando. Para o aplicativo de exemplo WordCount, a URL se parece com a seguinte.

    http://sfcluster4doc.westus.cloudapp.azure.com:31000

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Conectar remotamente a uma instância de VMSS (Conjunto de Escala de Máquinas Virtuais) ou um nó de cluster

Cada um dos NodeTypes que você especificar no cluster resultará na configuração de um VMSS. Consulte [How to RDP into your VMSS instance](service-fabric-cluster-nodetypes.md) para ver mais detalhes.

## Próximas etapas

Depois de criar um cluster, saiba mais sobre como protegê-lo e implantar aplicativos:
- [Gerenciando aplicativos da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Segurança do Cluster do Service Fabric](service-fabric-cluster-security.md)
- [Introdução ao modelo de Integridade do Service Fabric](service-fabric-health-introduction.md)


<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!---HONumber=AcomDC_0330_2016-->