<properties
   pageTitle="Conectar-se a um cluster privado seguro | Microsoft Azure"
   description="Este artigo descreve como proteger a comunicação no cluster autônomo ou privado, bem como entre clientes e o cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/10/2016"
   ms.author="dkshir"/>

# Proteger o cluster do Windows autônomo usando certificados

Este artigo descreve como proteger a comunicação entre os diversos nós do seu cluster do Windows autônomo, bem como autenticar os clientes que estejam se conectando a esse cluster, usando os certificados X.509. Isso garante que somente usuários autorizados possam acessar o cluster e os aplicativos implantados e executar tarefas de gerenciamento. A segurança do certificado deve ter sido habilitada no cluster quando o cluster foi criado. Para saber mais sobre a segurança de nó para nó, a segurança do cliente para nó é o controle de acesso baseado em função, veja [Cenários de segurança de cluster](service-fabric-cluster-security.md).

## De quais certificados você precisará?

Para começar, [baixe o pacote de clusters autônomos](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) para um dos nós no cluster. No pacote baixado, você encontrará um arquivo **ClusterConfig.X509.json**. Abra o arquivo no modo *Editar* e examine a seção de **segurança** sob a guia **properties**:

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
			"ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
        }
    },

Esta seção descreve todos os certificados necessários para proteger o cluster do Windows autônomo. Habilite a segurança baseada em certificado ao definir os valores de **ClusterCredentialType** e **ServerCredentialType** como *X509*.

>[AZURE.NOTE] Uma [impressão digital](https://en.wikipedia.org/wiki/Public_key_fingerprint) é a principal identidade de um certificado. Leia [How to retrieve thumbprint of a certificate (Como recuperar a impressão digital de um certificado)](https://msdn.microsoft.com/library/ms734695.aspx) para descobrir a impressão digital do certificados que você cria.

A tabela a seguir lista os certificados reais que serão necessárias na configuração do seu cluster:

|**Configuração de CertificateInformation**|**Descrição**|
|-----------------------|--------------------------|
|ClusterCertificate|Esse certificado é necessário para proteger a comunicação entre os nós em um cluster. Você pode usar dois certificados diferentes, um principal e um secundário para fallover. Defina a impressão digital do certificado principal na seção **Impressão Digital** e a do secundário nas variáveis **ThumbprintSecondary**.|
|ServerCertificate|Esse certificado é apresentado ao cliente quando ele tenta se conectar a esse cluster. Para sua conveniência, você pode optar por usar o mesmo certificado para *ClusterCertificate* e *ServerCertificate*. Você pode usar dois certificados de servidor diferentes, um principal e um secundário, para fallover. Defina a impressão digital do certificado principal na seção **Impressão Digital** e a do secundário nas variáveis **ThumbprintSecondary**. |
|ClientCertificateThumbprints|Esse é um conjunto de certificados que você deseja instalar nos clientes autenticados. Você pode ter alguns certificados de cliente diferentes instalados nos computadores para os quais você deseja permitir o acesso ao cluster e os aplicativos executados neles. Defina a impressão digital de cada certificado na variável **CertificateThumbprint**. Se você definir **IsAdmin** como *true*, o cliente com esse certificado instalado poderá executar várias atividades de gerenciamento para o cluster. Se **IsAdmin** for *false*, ele só poderá acessar os aplicativos em execução no cluster.|
|ClientCertificateCommonNames|Defina o nome comum do primeiro certificado do cliente para **CertificateCommonName**. A **CertificateIssuerThumbprint** é a impressão digital para o emissor deste certificado. Leia [Working with certificates (Trabalhando com certificados)](https://msdn.microsoft.com/library/ms731899.aspx) para saber mais sobre os nomes comuns e o emissor.|


## Instalar os certificados

Para proteger a comunicação entre o cluster, primeiro você precisará obter certificados X.509 para os nós de cluster. Além disso, para limitar a conexão a este cluster a computadores e a usuários autorizados, você precisará obter e instalar certificados para esses potenciais computadores cliente. Leia [How to obtain a certificate (Como obter um certificado)](https://msdn.microsoft.com/library/aa702761.aspx) para ver as etapas de como obter os certificados X.509. Você precisará criar um certificado **.pfx** para que possa armazenar sua chave privada. Como alternativa, se você tiver uma assinatura do Azure, siga a seção [Adquirir os certificados X.509](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts) para criar seus certificados. Assim que você tiver esse certificado, poderá instalá-lo nos nós do cluster usando as etapas a seguir. Observe que as etapas pressupõem que os nós já tenham o Windows PowerShell 3.x mais recente instalado neles. Você precisará repetir essas etapas em cada nó, para os certificados do Cluster e do Servidor e todos os certificados secundários para cada um deles.

- Copie o arquivo .pfx para o nó.

- Abra uma janela do PowerShell como administrador e insira os seguintes comandos:
	
		Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My `
		-FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $password -AsPlainText -Force)

Substitua *$password* pela senha que você usou para criar esse certificado. Substitua $PfxFilePath pelo caminho completo do .pfx copiado para esse nó.

- Defina o controle de acesso desse certificado de forma que o processo do Service Fabric possa usá-lo ao executar o script a seguir:

		$cert = get-item Cert:\LocalMachine\My<CertificateThumbprint>
		$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

		#Note down the string output by this command, this is the container name for your private key. 

		$privateKeyFilePath = 'C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys<PrivateKeyContainerName>'
	
		#Observe the access rights currently assigned to this certificate.
		get-acl $privateKeyFilePath | fl

		#Set the ACL so that the Service Fabric process can access it.
		$acl = get-acl $privateKeyFilePath
		$acl.SetAccessRule((New-Object System.Security.Accesscontrol.FileSystemAccessRule("NT AUTHORITY\NetworkService","FullControl","Allow")))
		Set-Acl $privateKeyFilePath $acl -ErrorAction Stop 
	

Use estas etapas para instalar também os certificados de cliente nos computadores para os quais você deseja permitir o acesso ao cluster.


## Próximas etapas

Depois de configurar a seção **security** do arquivo ClusterConfig.X509.json, vá para a seção [Criar seu cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) para configurar os nós e criar o cluster autônomo. Lembre-se de usar o arquivo **ClusterConfig.X509.json** durante a criação do cluster. Por exemplo, o comando pode ser semelhante ao seguinte:

	cd $ServiceFabricDeployAnywhereFolder
	.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true -Verbose


Depois que o cluster do Windows autônomo seguro estiver em execução, e que você tiver configurado os clientes autenticados para se conectarem a ele, siga a seção [Conectar a um cluster seguro usando o PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) para se conectar a ele.

<!---HONumber=AcomDC_0622_2016-->