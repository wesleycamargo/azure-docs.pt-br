<properties
   pageTitle="Proteger um cluster do Service Fabric com certificados | Microsoft Azure"
   description="Como proteger um cluster do Service Fabric usando certificados X.509."
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
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Proteger um cluster do Service Fabric no Azure usando certificados

Um cluster do Azure Service Fabric é um recurso que pertence a você. Para impedir o acesso não autorizado ao recurso, você deverá protegê-lo, especialmente quando ele tiver cargas de trabalho de produção em execução. Para configurar um cluster seguro do Service Fabric usando certificados X.509, você precisa de pelo menos um certificado X.509 do servidor, que será carregado no Cofre de Chaves do Azure e usado no processo de criação do cluster.

Este artigo corresponde à [Etapa 3: Configurar a segurança](service-fabric-cluster-creation-via-portal.md#step-3--configure-security) do processo de criação de cluster. Para obter mais informações sobre como o Service Fabric usa certificados X.509, consulte [Cenários de segurança de Cluster](service-fabric-cluster-security.md).

Há três etapas distintas:

1. Adquirir o certificado X.509.
2. Carregar o certificado no Cofre da Chave do Azure.
3. Fornecer o local e os detalhes do certificado ao processo de criação de cluster do Service Fabric.

<a id="acquirecerts"></a>
## Etapa 1: adquirir o(s) certificado(s) X.509.

Para clusters que estão executando cargas de trabalho de produção, você deve usar um certificado X.509 assinado pela [AC (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority) para proteger o cluster. Para obter detalhes sobre como obter esses certificados, vá para [Como obter um certificado](http://msdn.microsoft.com/library/aa702761.aspx).

Para clusters que usados apenas para fins de teste, você pode optar por usar um certificado assinado automaticamente. A Etapa 2.5 abaixo explica como fazer isso.

## Etapa 2: Carregar o certificado X.509 no Cofre da Chave

Esse é um processo complicado; portanto, carregamos um módulo do PowerShell em um repositório Git que faz isso para você.

### Etapa 2.1
Verificar se o Azure PowerShell 1.0+ está instalado em seu computador. Se não tiver feito isso antes, siga as etapas descritas em [Como instalar e configurar o Azure PowerShell.](../powershell-install-configure.md)

### Etapa 2.2
Copie a pasta *ServiceFabricRPHelpers* deste [repositório Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers) para seu computador.

### Etapa 2.3
Abra uma janela do PowerShell e vá para o diretório no qual você baixou o módulo. Em seguida, importe o módulo usando o comando a seguir.

```powershell
Import-Module .\ServiceFabricRPHelpers.psm1
```

### Etapa 2.4
Se você está usando um certificado que já adquiriu, execute o procedimento nesta etapa. Caso contrário, pule para a Etapa 2.5, que explica como criar e implantar o certificado autoassinado no cofre da chave.

Você poderá usar um grupo de recursos e cofre de chaves existente para armazenar o certificado, ou você poderá criar um novo grupo de recursos e/ou um cofre de chaves, se eles ainda não estiverem presentes. Um cofre de chaves existente deverá primeiro ser configurado para dar suporte à implantação, por meio desse script.

```powershell
Login-AzureRmAccount

Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

Para carregar o certificado em seu grupo de recursos e cofre de chaves, execute o script a seguir. O grupo de recursos e o cofre de chaves serão criados se ainda não existirem.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
Veja um script preenchido como exemplo.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

Após a conclusão bem-sucedida do script, você receberá uma saída semelhante à mostrada abaixo. Ela será necessária na Etapa 3 (Configurar um cluster seguro).

```
Certificate Thumbprint: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

SourceVault /Resource ID of the key vault :  /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea
```

Agora você tem as informações necessárias para configurar um cluster seguro. Vá para a Etapa 3.

### Etapa 2.5
Se *não* tiver um certificado e desejar criar um novo certificado autoassinado e carregá-lo no cofre de chaves, execute estas etapas. Os certificados autoassinados devem ser usados somente para clusters de teste e não para clusters de produto.

Você poderá usar um grupo de recursos e cofre de chaves existente para armazenar o certificado, ou você poderá criar um novo grupo de recursos e/ou um cofre de chaves, se eles ainda não estiverem presentes. Um cofre de chaves existente deverá primeiro ser configurado para dar suporte à implantação, por meio desse script.

```powershell
Login-AzureRmAccount
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

O script a seguir criará um novo grupo de recursos e/ou cofre de chaves se eles não ainda estiverem presentes, criar e carregar um certificado autoassinado para o cofre de chaves e exportar o novo certificado para *OutputPath*.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```
A cadeia de caracteres *DnsName* especifica um ou mais nomes DNS a serem colocados na extensão de nome alternativo do assunto do certificado quando um certificado a ser copiado não for especificado por meio do parâmetro CloneCert. O primeiro nome DNS também é salvo como o Nome da assunto. Se nenhum certificado de autenticação for especificado, o primeiro nome DNS também será salvo como o Nome do emissor. O cmdlet *Invoke-AddCertToKeyVault* usa o [cmdlet New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633.aspx) para criar o certificado autoassinado.

Veja um script preenchido como exemplo.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Após a conclusão bem-sucedida do script, você receberá uma saída, como a mostrada abaixo. Você precisa dela para a Etapa 3.

```
Certificate Thumbprint: 64881409F4D86498C88EEC3697310C15F8F1540F

SourceVault /Resource ID of the key vault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea
```

## Etapa 3: Configurar um cluster seguro

Depois que os certificados forem carregados em um cofre de chaves do Azure, você pode criar um cluster protegido com esses certificados. Esta etapa corresponde à [Etapa 3: Configurar a segurança](service-fabric-cluster-creation-via-portal.md#step-3--configure-security) do processo de criação de cluster e mostra como configurar suas configurações de segurança.

>[AZURE.NOTE]
Os certificados necessários são especificados no nível do tipo de nó nas Configurações de Segurança. É necessário especificar isso para cada tipo de nó que você tem no cluster. Embora este documento explique como fazer isso usando o portal, você pode fazer o mesmo usando um modelo do Gerenciador de Recursos do Azure.

![Captura de tela das Configurações de Segurança no portal do Azure][SecurityConfigurations_01]

### Parâmetros obrigatórios

- **Modo de Segurança** selecione **Certificado X509** para configurar um cluster protegido com certificados X.509.
- **Nível de proteção de cluster** Consulte este [documento sobre o Nível de Proteção](https://msdn.microsoft.com/library/aa347692.aspx) para entender o que cada um desses valores significa. Apesar de permitirmos três valores aqui (EncryptAndSign, Sign e None), é melhor manter o padrão de EncryptAndSign, a menos que você saiba o que está fazendo.
- **Cofre de Origem** Isso se refere à ID de Recurso do cofre de chaves. Ele deve estar no seguinte formato:

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **URL do Certificado** Refere-se à URL de localização em seu cofre de chaves em que o certificado foi carregado. Ele deve estar no seguinte formato:

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **Impressão Digital do Certificado** Refere-se à impressão digital do certificado, que pode ser encontrada na URL que você especificou anteriormente.

### Parâmetros opcionais

 Você pode especificar, como opção, outros certificados para as máquinas do cliente que você usa para executar operações no cluster. Por padrão, a impressão digital que você especificou nos parâmetros obrigatórios é adicionada à lista autorizada de impressões digitais com permissão para executar operações do cliente.

**Cliente de Administração**: essas informações são usadas para validar se o cliente que está se conectando ao ponto de extremidade de gerenciamento do cluster está apresentando a credencial correta para execução de ações de administrador e somente leitura no cluster. Você pode especificar mais de um certificado para autorização para operações de administrador.

- **Autorizar por** Indica ao Service Fabric se ele precisa buscar esse certificado usando o nome do assunto ou a impressão digital. O uso do nome do assunto para autorizar não é uma boa prática de segurança, mais acrescenta flexibilidade.
- **Nome do Assunto** Só será necessário caso você tenha especificado a autorização pelo nome do assunto.
- **Impressão Digital do Emissor** Possibilita um nível adicional de verificação que pode ser executado pelo servidor quando um cliente apresenta suas credenciais a ele.

**Cliente Somente Leitura**: essas informações são usadas para validar se o cliente que está se conectando ao ponto de extremidade de gerenciamento do cluster está apresentando a credencial correta para execução de ações somente leitura no cluster. Você pode especificar mais de um certificado para autorização para operações somente leitura.

- **Autorizar por** Indica ao Service Fabric se ele precisa buscar esse certificado usando o nome do assunto ou a impressão digital. O uso do nome do assunto para autorização não é uma boa prática de segurança, mais acrescenta flexibilidade.
- **Nome do Assunto** Só será necessário caso você tenha especificado a autorização pelo nome do assunto.
- **Impressão Digital do Emissor** Possibilita um nível adicional de verificação que pode ser executado pelo servidor quando um cliente apresenta suas credenciais a ele.

## Próximas etapas
Depois de configurar a segurança de certificado no seu cluster, retome o processo de criação de cluster em [Etapa 4: Concluir a criação do cluster](service-fabric-cluster-creation-via-portal.md#step-4--complete-the-cluster-creation).

Depois de um cluster com segurança de certificado ser criado, você pode posteriormente [Atualizar um certificado](service-fabric-cluster-security-update-certs-azure.md).


<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0615_2016-->