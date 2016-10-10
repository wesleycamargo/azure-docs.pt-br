<properties
   pageTitle="Autenticar o acesso do cliente a um cluster | Microsoft Azure"
   description="Descreve como autenticar o acesso do cliente a um cluster do Service Fabric usando certificados e como proteger a comunicação entre clientes e um cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# Conectar a um cluster seguro sem AAD
Quando um cliente se conecta a um nó de cluster do Service Fabric, o cliente pode ser autenticado e comunicação segura pode ser estabelecida usando segurança de certificado. Essa autenticação garante que somente usuários autorizados possam acessar o cluster e os aplicativos implantados e executar tarefas de gerenciamento. A segurança de certificado deve ter sido previamente habilitada no cluster quando o cluster foi criado. Pelo menos dois certificados devem ser usados para proteger o cluster, um para o cluster e certificado do servidor e outro para acesso para cliente. É recomendável que você também use certificados secundários e certificados de acesso para cliente adicionais. Para obter mais informações sobre cenários de segurança de cluster, consulte [Segurança de cluster](service-fabric-cluster-security.md).

Para proteger a comunicação entre um cliente e um nó de cluster usando a segurança de certificado, você precisa primeiro obter e instalar o certificado do cliente. O certificado pode ser instalado no repositório Pessoal (Meu) do computador local ou o usuário atual. Você também precisa da impressão digital do certificado do servidor para que o cliente possa autenticar o cluster.


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
<a id="connectsecureclustercli"></a>
## Conectar-se a um cluster seguro usando a CLI do Azure sem AAD

Os seguintes comandos da CLI do Azure descrevem como se conectar a um cluster seguro. Os detalhes do certificado devem corresponder a um certificado em nós do cluster.
 
Se o certificado tiver ACs (autoridades de certificação), você precisará adicionar o parâmetro `--ca-cert-path`, como no seguinte exemplo:

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Se você tiver várias ACs, use vírgulas como o delimitador.

 
Se o Nome Comum no certificado não coincidir com o ponto de extremidade de conexão, você poderá usar o parâmetro `--strict-ssl-false` para ignorar a verificação.

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```
 
Se você quiser ignorar a verificação de AC, poderá adicionar o parâmetro ``--reject-unauthorized-false``, conforme no seguinte comando:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```
 
Para se conectar a um cluster protegido com um certificado autoassinado, use o comando a seguir removendo a verificação de AC e a verificação de Nome Comum:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

Depois de se conectar, você poderá executar outros comandos da CLI para interagir com o cluster.

<a id="connectsecurecluster"></a>
## Conectar a um cluster seguro usando o PowerShell sem AAD

Execute o seguinte comando do PowerShell para se conectar a um cluster seguro. Os detalhes do certificado devem corresponder a um certificado em nós do cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* é a impressão digital do certificado do servidor instalado nos nós do cluster. *FindValue* é a impressão digital do certificado de cliente do administrador. Quando os parâmetros são preenchidos, o comando se parece com o exemplo a seguir:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```




## Conectar-se a um cluster seguro usando as APIs de FabricClient

Para obter mais informações sobre APIs do FabricClient, consulte [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Os nós no cluster devem ter certificados válidos cujo nome comum ou nome DNS no SAN aparece na [propriedade RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx) definida em [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Seguir esse processo habilita a autenticação mútua entre o cliente e o nó de cluster.

```csharp
string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
FabricClient fc = new FabricClient(xc, connection);
Task<bool> t = fc.PropertyManager.NameExistsAsync(new Uri("fabric:/any"));
try
{
    bool result = t.Result;
    Console.WriteLine("Cluster is connected");
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();

    // Client certificate
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;

    // Server certificate
    xc.RemoteCertThumbprints.Add(thumb);
    xc.RemoteCommonNames.Add(name);

    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```


## Próximas etapas

- [Processo de atualização de Cluster de Malha do Serviço e as suas expectativas](service-fabric-cluster-upgrade.md)
- [Gerenciando seu aplicativo da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introdução ao modelo de Integridade da Malha de Serviço](service-fabric-health-introduction.md)
- [Segurança do aplicativo e RunAs](service-fabric-application-runas-security.md)

<!---HONumber=AcomDC_0928_2016-->