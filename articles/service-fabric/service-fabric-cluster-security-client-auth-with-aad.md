
<properties
   pageTitle="Segurança de cluster do Service Fabric: autenticação de cliente com o Azure Active Directory | Microsoft Azure"
   description="Este artigo descreve como cria um cluster do Service Fabric usando o AAD (Azure Active Directory) para autenticação de cliente"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/13/2016"
   ms.author="seanmck"/>

# Criar um cluster do Service Fabric usando o Azure Active Directory para autenticação de cliente

Você pode proteger o acesso aos pontos de extremidade de gerenciamento de um cluster do Service Fabric usando o AAD (Azure Active Directory). Este artigo aborda como criar os artefatos do AAD necessários, como preenchê-los durante a criação do cluster e como se conectar a esses grupos posteriormente.

## Modelar um cluster do Service Fabric no AAD

O AAD permite às organizações (conhecidas como locatários) gerenciarem o acesso de usuários a aplicativos, os quais são divididos em aplicativos com uma interface do usuário de logon baseada na Web e aplicativos com uma experiência de cliente nativo. Neste documento, vamos pressupor que você já tenha criado um locatário. Se não, comece lendo [Como obter um locatário do Azure Active Directory](../active-directory/active-directory-howto-tenant.md).

Os clusters do Service Fabric oferecem uma variedade de pontos de entrada para a funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) baseado na Web e o [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Como resultado, você criará dois aplicativos do AAD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.

Para simplificar algumas das etapas envolvidas na configuração do AAD com um cluster do Service Fabric, criamos um conjunto de scripts do Windows PowerShell.

>[AZURE.NOTE] Você deve executar essas etapas *antes* de criar o cluster, por isso em casos em que os scripts esperam nomes e pontos de extremidade de cluster, eles devem ser os valores planejados, não aqueles que você já criou.

1. [Baixe os scripts][sf-aad-ps-script-download] em seu computador.

2. Clique com o botão direito do mouse no arquivo zip, escolha **Propriedades**, marque a caixa de seleção **Desbloquear** e clique em Aplicar.

3. Extraia o arquivo zip.

4. Execute `SetupApplications.ps1`, fornecendo TenantId, ClusterName e WebApplicationReplyUrl como parâmetros. Por exemplo:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Você pode encontrar a **TenantId** observando a URL do locatário no portal clássico do Azure. O GUID incorporado na URL é a TenantId. Por exemplo:

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    O **ClusterName** será usado para prefixar os aplicativos do AAD criados pelo script. Ele não precisa corresponder ao nome do cluster real exatamente, pois ele se destina apenas a facilitar o mapeamento dos artefatos do AAD para o cluster do Service Fabric com o qual eles estão sendo usados.

    A **WebApplicationReplyUrl** é o ponto de extremidade padrão que o AAD retornará aos usuários depois de concluir o processo de entrada. Você deve definir isso para o ponto de extremidade do Service Fabric Explorer para o seu cluster, que por padrão é:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Você precisará entrar em uma conta que tenha privilégios administrativos para o locatário do AAD. Depois que você fizer isso, o script continuará a criar aplicativos Web e nativos para representar seu cluster do Service Fabric. Se você observar os aplicativos do locatário no [portal clássico do Azure][azure-classic-portal], você verá duas novas entradas:

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    O script imprimirá o Json necessário para o modelo do ARM (Azure Resource Manager) ao criar o cluster na próxima seção por isso mantenha a janela do PowerShell aberta.

    ![A saída do script SetupApplication inclui o Json exigido pelo modelo de ARM][setupapp-script-output]

## Criar o cluster

Agora que você criou os aplicativos do AAD, você pode criar o cluster do Service Fabric. Neste momento, o portal do Azure não dá suporte à configuração da autenticação do AAD para clusters do Service Fabric, por isso você precisará fazer isso usando um modelo do ARM no PowerShell ou no Visual Studio.

Observe que o AAD só é usado para autenticação de cliente do cluster. Para criar um cluster seguro, é necessário também fornecer um certificado, que será usado para proteger a comunicação entre os nós do cluster e para fornecer autenticação de servidor para pontos de extremidade de gerenciamento do cluster. Você pode encontrar um [modelo ARM para um cluster seguro na galeria de início rápido do Azure][secure-cluster-arm-template] ou seguir as instruções fornecidas no arquivo Leiame do [projeto do grupo de recursos do Service Fabric no Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Adicione a saída do trecho de código do modelo ARM do script `SetupApplication` como um par de fabricSettings, managementEndpoint etc. Se você fechou a janela, o trecho de código também é mostrado abaixo:

```json
  "azureActiveDirectory": {
    "tenantId": "<your_tenant_id>",
    "clusterApplication": "<your_cluster_application_client_id>",
    "clientApplication": "<your_native_application_client_id>"
  }
```

O clusterApplication refere-se ao aplicativo Web criado na seção anterior. É possível encontrar a ID na saída do script SetupApplication, em que ele é conhecido como `WebAppId`. O clientApplication refere-se ao aplicativo nativo e sua ID do cliente está disponível na saída SetupApplication como NativeClientAppId.

## Atribuir usuários a funções

Depois de criar os aplicativos para representar seu cluster, você precisará atribuir os usuários às funções com suporte no Service Fabric: somente leitura e administrador. Você pode fazer isso usando o [portal clássico do Azure][azure-classic-portal].

1. Navegue até seu locatário e escolha Aplicativos.
2. Escolha o aplicativo Web, que terá um nome como `myTestCluster_Cluster`.
3. Clique na guia Usuários.
4. Escolha um usuário para atribuir e clique no botão **Atribuir** na parte inferior da tela.

    ![Botão Atribuir usuários a funções][assign-users-to-roles-button]

5. Selecione a função para atribuir ao usuário.

    ![Atribuir usuários a funções][assign-users-to-roles-dialog]

>[AZURE.NOTE] Para saber mais sobre as funções no Service Fabric, veja [Controle de acesso baseado em função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).

## Conectando ao cluster

Quando você navegar para o Service Fabric Explorer em um cluster habilitado para AAD, você será redirecionado automaticamente para uma página de logon seguro.

Para conectar-se de um cliente nativo, como o Windows PowerShell ou o Visual Studio, você precisará indicar o AAD como o mecanismo de entrada e, em seguida, fornecer uma impressão digital de certificado do servidor, que serve para validar a identidade do ponto de extremidade. Os detalhes para esses dois pontos de entrada são mostrados abaixo.

### Conectando do Visual Studio

No Visual Studio, você pode modificar o perfil de publicação para adicionar os atributos necessários, conforme mostrado abaixo:

```xml
<ClusterConnectionParameters     
    ConnectionEndpoint="<your_cluster_endpoint>:19000"  
    AzureActiveDirectory="true"
    ServerCertThumbprint="<your_cert_thumbprint>"
    />
```

Quando você publica em cluster, o Visual Studio exibe uma janela de logon na qual você pode se autenticar no cluster.

![Janela de logon do AAD durante a publicação do Visual Studio][vs-publish-aad-login]

### Conectando do Windows PowerShell

No PowerShell, você pode fornecer os parâmetros necessários para o cmdlet Connect-ServiceFabricCluster, conforme mostrado abaixo:

```PowerShell
Connect-ServiceFabricCluster -AzureActiveDirectory -ConnectionEndpoint <cluster_endpoint>:19000 -ServerCertThumbprint <server_cert_thumbprint>
```

Como no Visual Studio, o PowerShell apresentará uma janela de logon seguro para autenticação.

>[AZURE.NOTE] Por padrão, o gateway de TCP do Service Fabric usado pelo PowerShell e pelo Visual Studio escuta a porta 19000. Se tiver configurado uma porta diferente, você deverá usá-la ao especificar o ponto de extremidade de conexão.

## Problemas conhecidos

### Erro de autenticação de cliente nativo devido ao endereço de resposta incompatível

Durante a autenticação de um cliente nativo, como o Visual Studio ou o PowerShell, você verá uma mensagem de erro como esta:

O *endereço de resposta http://localhost/ não corresponde ao endereço de resposta configurado para o GUID&lt; do aplicativo cliente do cluster&gt; do aplicativo*

Para resolver esse problema, adicione **http://<i></i>localhost** como um URI de redirecionamento para a definição de aplicativo cliente do cluster no AAD, além do endereço “urn:ietf:wg:oauth:2.0:oob” que já está lá.

## Próximas etapas

- Leia mais sobre a [segurança do cluster do Service Fabric](service-fabric-cluster-security.md)
- Saiba como [publicar em um cluster remoto usando o Visual Studio](service-fabric-publish-app-remote-cluster.md)

<!-- Links -->
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[secure-cluster-arm-template]: https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad
[aad-graph-api-docs]: https://msdn.microsoft.com/pt-BR/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com

<!-- Images -->
[assign-users-to-roles-button]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles.png
[setupapp-script-output]: ./media/service-fabric-cluster-security-client-auth-with-aad/setupapp-script-arm-json-output.png
[vs-publish-aad-login]: ./media/service-fabric-cluster-security-client-auth-with-aad/vs-login-prompt.png

<!---HONumber=AcomDC_0615_2016-->