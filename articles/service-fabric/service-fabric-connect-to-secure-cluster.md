---
title: "Conectar com segurança a um cluster do Azure Service Fabric | Microsoft Docs"
description: "Descreve como autenticar o acesso do cliente a um cluster do Service Fabric e como proteger a comunicação entre clientes e um cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: cb1c9734ef7745274095e5b3840698a76045bc91
ms.openlocfilehash: 44dec18e40f5693e32a99cbea0bf0d1530dddb44


---
# <a name="connect-to-a-secure-cluster"></a>Conectar a um cluster seguro
Quando um cliente se conecta a um nó de cluster do Service Fabric, ele pode ser autenticado e uma comunicação segura pode ser estabelecida com a segurança de certificado ou o AAD (Azure Active Directory). Essa autenticação garante que somente usuários autorizados possam acessar o cluster e os aplicativos implantados e executar tarefas de gerenciamento.  A segurança de certificado ou do AAD deve ter sido previamente habilitada no cluster quando o cluster foi criado.  Para obter mais informações sobre cenários de segurança de cluster, consulte [Segurança de cluster](service-fabric-cluster-security.md). Se você estiver se conectando a um cluster protegido com certificados, [configure o certificado do cliente](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) no computador que se conecta ao cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-cli"></a>Conectar-se a um cluster seguro usando a CLI do Azure
Os seguintes comandos da CLI do Azure descrevem como se conectar a um cluster seguro. 

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conectar-se a um cluster seguro usando um certificado de cliente
Os detalhes do certificado devem corresponder a um certificado em nós do cluster. 

Se o certificado tiver ACs (autoridades de certificação), você precisará adicionar o parâmetro `--ca-cert-path` , como no seguinte exemplo: 

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Se você tiver várias ACs, use vírgulas como o delimitador. 

Se o Nome Comum no certificado não coincidir com o ponto de extremidade de conexão, você poderá usar o parâmetro `--strict-ssl-false` para ignorar a verificação. 

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```

Se você quiser ignorar a verificação de AC, poderá adicionar o parâmetro ``--reject-unauthorized-false`` , conforme no seguinte comando:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

Para se conectar a um cluster protegido com um certificado autoassinado, use o comando a seguir removendo a verificação de AC e a verificação de Nome Comum:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

Depois de se conectar, você poderá [executar outros comandos da CLI](service-fabric-azure-cli.md) para interagir com o cluster. 

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Conectar a um cluster usando o PowerShell
Antes de executar operações em um cluster por meio do PowerShell, primeiro estabeleça uma conexão com o cluster. A conexão do cluster é usada para todos os comandos posteriores na sessão do PowerShell específica.

### <a name="connect-to-an-unsecure-cluster"></a>Conectar a um cluster não seguro

Para se conectar a um cluster não seguro, forneça o endereço do ponto de extremidade do cluster ao comando **Connect-ServiceFabricCluster**:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Conectar-se a um cluster seguro usando o Azure Active Directory

Para se conectar a um cluster seguro que usa o Azure Active Directory para autorizar o acesso de administrador de cluster, forneça a impressão digital do certificado do cluster e use o sinalizador *AzureActiveDirectory*.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conectar-se a um cluster seguro usando um certificado de cliente
Execute o comando do PowerShell a seguir para se conectar a um cluster seguro que usa certificados de cliente para autorizar o acesso do administrador. Forneça a impressão digital do certificado do cluster, bem como a impressão digital do certificado de cliente que recebeu permissões para o gerenciamento de cluster. Os detalhes do certificado devem corresponder a um certificado em nós do cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* é a impressão digital do certificado do servidor instalado nos nós do cluster. *FindValue* é a impressão digital do certificado de cliente do administrador.
Quando os parâmetros são preenchidos, o comando se parece com o exemplo a seguir: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```


<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Conectar-se a um cluster usando as APIs de FabricClient
O SDK do Service Fabric fornece a classe [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) para o gerenciamento de cluster. Para usar as APIs FabricClient, obtenha o pacote do Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Conectar a um cluster não seguro

Para se conectar a um cluster não seguro remoto, crie uma instância de FabricClient e forneça o endereço do cluster:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Para o código executado por meio de um cluster, por exemplo, em um Serviço Confiável, crie um FabricClient *sem* especificar o endereço do cluster. FabricClient conecta-se ao gateway de gerenciamento local no nó em que o código está sendo executado, evitando um salto extra na rede.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conectar-se a um cluster seguro usando um certificado de cliente

Os nós no cluster devem ter certificados válidos cujo nome comum ou nome DNS no SAN aparece na [propriedade RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames) definida em [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Seguir esse processo habilita a autenticação mútua entre o cliente e o nó de cluster.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Conectar-se a um cluster seguro de forma interativa usando o Azure Active Directory

O exemplo e a seguir usa o Azure Active Directory para a identidade do cliente e o certificado do servidor para a identidade do servidor.

Uma janela de diálogo aparece automaticamente para entrada interativa após a conexão com o cluster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Conectar-se a um cluster seguro de forma não interativa usando o Azure Active Directory

O exemplo a seguir tem base no Microsoft.IdentityModel.Clients.ActiveDirectory, Versão: 2.19.208020213.

Para saber mais sobre aquisição de token do AAD, consulte [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Conectar-se a um cluster seguro sem conhecimento prévio de metadados usando o Azure Active Directory

O exemplo a seguir usa a aquisição de token não interativa, mas a mesma abordagem pode ser usada para criar uma experiência de aquisição de token interativa personalizada. Os metadados necessários do Azure Active Directory para a aquisição de token são lidos na configuração do cluster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Conectar-se a um cluster seguro usando o Service Fabric Explorer
Para alcançar o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) para determinado cluster, aponte o navegador para:

`http://<your-cluster-endpoint>:19080/Explorer`

A URL completa também está disponível no painel essencial do cluster do portal do Azure.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Conectar-se a um cluster seguro usando o Azure Active Directory

Para se conectar a um cluster protegido com o AAD, aponte seu navegador para:

`https://<your-cluster-endpoint>:19080/Explorer`

Você deverá automaticamente fazer logon no AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conectar-se a um cluster seguro usando um certificado de cliente

Para conectar-se a um cluster protegido com certificados, aponte seu navegador para:

`https://<your-cluster-endpoint>:19080/Explorer`

Você deverá automaticamente selecionar um certificado do cliente.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Configurar um certificado de cliente no computador remoto
Pelo menos dois certificados devem ser usados para proteger o cluster, um para o cluster e certificado do servidor e outro para acesso para cliente.  É recomendável que você também use certificados secundários e certificados de acesso para cliente adicionais.  Para proteger a comunicação entre um cliente e um nó de cluster usando a segurança de certificado, você precisa primeiro obter e instalar o certificado do cliente. O certificado pode ser instalado no repositório Pessoal (Meu) do computador local ou o usuário atual.  Você também precisa da impressão digital do certificado do servidor para que o cliente possa autenticar o cluster.

Execute o cmdlet do PowerShell a seguir para configurar o certificado do cliente no computador do qual você acessa o cluster.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Se o certificado for autoassinado, você precisará importá-lo para o repositório de "pessoas confiáveis" do computador antes de poder usá-lo para se conectar a um cluster seguro.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>Próximas etapas
* [Processo de atualização de Cluster de Malha do Serviço e as suas expectativas](service-fabric-cluster-upgrade.md)
* [Gerenciando seu aplicativo da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
* [Introdução ao modelo de Integridade da Malha de Serviço](service-fabric-health-introduction.md)
* [Segurança do aplicativo e RunAs](service-fabric-application-runas-security.md)




<!--HONumber=Feb17_HO1-->


