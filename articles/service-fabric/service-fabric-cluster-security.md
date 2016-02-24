<properties
   pageTitle="Proteger um cluster do Service Fabric | Microsoft Azure"
   description="Como proteger um cluster do Service Fabric. Quais são as opções?"
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
   ms.date="02/01/2016"
   ms.author="chackdan"/>

# Proteger um cluster do Service Fabric

Um cluster do Azure Service Fabric é um recurso que pertence a você. Para impedir o acesso não autorizado ao recurso, você deverá protegê-lo, especialmente quando ele tiver cargas de trabalho de produção em execução. Este artigo explica o processo para proteger um cluster do Service Fabric.

## Cenários de segurança do cluster

O Service Fabric oferece segurança para os seguintes cenários:

1. **Segurança de nó para nó:** esse cenário protege a comunicação entre as VMs e os computadores no cluster. Isso faz com que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster.

	![Diagrama de comunicação de nó para nó][Node-to-Node]

2. **Segurança de cliente para o nó:** esse cenário protege a comunicação entre um cliente do Service Fabric e nós individuais no cluster. Esse tipo de segurança autentica e protege as comunicações do cliente, o que garante que somente usuários autorizados possam acessar o cluster e os aplicativos implantados no cluster. Os clientes são identificados exclusivamente por meio de suas credenciais de segurança do Windows ou pelas credenciais de segurança do certificado deles.

	![Diagrama de comunicação de cliente para nó][Client-to-Node]

	Para qualquer um desses cenários, você pode usar o [Certificado de segurança](https://msdn.microsoft.com/library/ff649801.aspx) ou a [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx). As opções de segurança em nó para nó ou de cliente para nó são independentes entre si, e podem ser iguais ou diferentes.

	O Azure Service Fabric usa os certificados do servidor X.509 que você especifica como parte das configurações de tipo de nó ao criar um cluster. Confira no final deste artigo uma visão geral rápida do que são esses certificados e como você pode adquirir ou criá-los.

3. **RBAC (Controle de acesso com base da função):** isso restringe as operações de administração no cluster a um determinado conjunto de certificados.

## Proteger um cluster do Service Fabric usando certificados.

Para configurar um cluster seguro do Service Fabric, você precisa de pelo menos um certificado X.509 do servidor, que será carregado no Cofre da Chave do Azure e usado no processo de criação do cluster.

Há três etapas distintas:

1. Adquirir o certificado X.509.
2. Carregar o certificado no Cofre da Chave do Azure.
3. Fornecer o local e os detalhes do certificado ao processo de criação de cluster do Service Fabric.

### Etapa 1: adquirir o(s) certificado(s) X.509.

1. Para clusters que estão executando cargas de trabalho de produção, você deve usar um certificado X.509 assinado pela [AC (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority) para proteger o cluster. Para obter detalhes sobre como obter esses certificados, vá para [Como obter um certificado](http://msdn.microsoft.com/library/aa702761.aspx).
2. Para clusters que usados apenas para fins de teste, você pode optar por usar um certificado assinado automaticamente. A Etapa 2.5 abaixo explica como fazer isso.

### Etapa 2: Carregar o certificado X.509 no Cofre da Chave

Esse é um processo complicado; portanto, carregamos um módulo do PowerShell em um repositório Git que faz isso para você.

**Etapa 2.1**: copiar esta pasta em seu computador deste [repositório Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Etapa 2.2**: verificar se o Azure PowerShell 1.0+ está instalado em seu computador. Se não tiver feito isso antes, sugerimos veementemente que você execute as etapas descritas em [Como instalar e configurar o Azure PowerShell.](../powershell-install-configure.md)

**Etapa 2.3**: abrir uma janela do PowerShell e importar o ServiceFabricRPHelpers.psm. (Este é o módulo que você baixou na Etapa 2.1.)

```
Remove-Module ServiceFabricRPHelpers
```

Copie o exemplo a seguir e altere o caminho para o .psm1 a fim de corresponder ao caminho em seu computador.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

**Etapa 2.4**: se está usando um certificado que já adquiriu, execute o procedimento nesta etapa. Caso contrário, pule para a Etapa 2.5, que explica como criar e implantar o certificado autoassinado no cofre da chave.

Entre na sua conta do Azure. Se o comando do PowerShell falhar por algum motivo, verifique se o Azure PowerShell foi instalado corretamente.

```
Login-AzureRmAccount
```

O script a seguir criará um novo grupo de recursos e/ou um cofre de chave, caso eles ainda não esteja presentes.

```
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
Veja um script preenchido como exemplo.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

Após a conclusão bem-sucedida do script, você receberá uma saída semelhante à mostrada abaixo. Ela será necessária na Etapa 3 (Configurar um cluster seguro).

- **Impressão Digital do Certificado**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

- **SourceVault** /ID de recurso do cofre de chave: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **URL do Certificado**/URL para o local do Certificado no cofre de chave: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea

Agora você tem as informações necessárias para configurar um cluster seguro. Vá para a Etapa 3.

**Etapa 2.5**: se você *não* tem um certificado e quer criar um novo certificado autoassinado e carregá-lo no cofre de chave, execute estas etapas.

Faça logon na sua conta do Azure. Se o comando do PowerShell falhar por algum motivo, verifique se o Azure PowerShell foi instalado corretamente.

```
Login-AzureRmAccount
```

O script a seguir criará um novo grupo de recursos e/ou um cofre de chave, caso eles não existam.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```

O OutputPath que você atribuiu ao script conterá o novo certificado autoassinado que o script carregou no cofre e chave.

>[AZURE.NOTE] A cadeia de caracteres DnsName especifica um ou mais nomes DNS a serem colocados na extensão de nome alternativo do assunto do certificado quando um certificado a ser copiado não for especificado por meio do parâmetro CloneCert. O primeiro nome DNS também é salvo como o Nome da assunto. Se nenhum certificado de autenticação for especificado, o primeiro nome DNS também será salvo como o Nome do emissor.

Leia mais sobre como criar um certificado autoassinado em [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx).

Veja um script preenchido como exemplo.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Como é um certificado autoassinado, você precisará importá-lo no repositório de "pessoas confiáveis" do seu computador antes de poder usá-lo para se conectar a um cluster seguro.

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

Após a conclusão bem-sucedida do script, você receberá uma saída, como a mostrada abaixo. Você precisa dela para a Etapa 3.

- **Impressão Digital do Certificado**: 64881409F4D86498C88EEC3697310C15F8F1540F

- **SourceVault** /ID de recurso do cofre de chave: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **URL do Certificado**/URL para o local do certificado no cofre de chave: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea

### Etapa 3: Configurar um cluster seguro

Execute as etapas descritas em [Processo de criação de cluster do Service Fabric](service-fabric-cluster-creation-via-portal.md), até chegar à seção Configurações de segurança. Em seguida, ignore as instruções exibidas aqui para definir as configurações de segurança:

>[AZURE.NOTE]
Os certificados necessários são especificados no nível do tipo de nó nas Configurações de Segurança. É necessário especificar isso para cada tipo de nó que você tem no cluster. Embora este documento explique como fazer isso usando o portal, você pode fazer o mesmo usando um modelo do Gerenciador de Recursos do Azure.

![Captura de tela das Configurações de Segurança no portal do Azure][SecurityConfigurations_01]

Parâmetros obrigatórios:

- **Modo de Segurança.** Selecione **Certificado X509**. Que indica ao Service Fabric que você pretende configurar um cluster seguro.
- **Nível de proteção do cluster.** Consulte este [documento sobre o nível de proteção](https://msdn.microsoft.com/library/aa347692.aspx) para compreender o que cada um desses valores significa. Apesar de permitirmos três valores aqui (EncryptAndSign, Sign e None), é melhor manter o padrão de EncryptAndSign, a menos que você saiba o que está fazendo.
- **Cofre de Origem.** Isso se refere à ID de Recurso do cofre de chave. Ele deve estar no seguinte formato:

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **URL do certificado.** Refere-se à URL de local em seu cofre de chave em que o certificado foi carregado. Ele deve estar no seguinte formato:

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **Impressão digital do certificado.** Refere-se à impressão digital do certificado, pode ser encontrada na URL especificada anteriormente.

Parâmetros opcionais:

 - Você pode especificar, como opção, outros certificados para as máquinas do cliente que você usa para executar operações no cluster. Por padrão, a impressão digital que você especificou nos parâmetros obrigatórios é adicionada à lista autorizada de impressões digitais com permissão para executar operações do cliente.

Cliente de Administração: essas informações são usadas para validar se o cliente que está se conectando ao ponto de extremidade de gerenciamento do cluster está apresentando a credencial correta para execução de ações de administrador e somente de leitura no cluster. Você pode especificar mais de um certificado para autorização para operações de administrador.

- **Autorizar por.** Indica ao Service Fabric se ele precisa buscar esse certificado usando o nome do assunto ou a impressão digital. O uso do nome do assunto para autorizar não é uma boa prática de segurança, mais acrescenta flexibilidade.
- **Nome da Entidade.** Isso será necessário apenas se você tiver especificado que a autorização ocorre pelo nome do assunto.
- **Impressão Digital do Emissor** possibilita um nível adicional de verificação que o servidor pode executar quando um cliente apresenta suas credenciais ao servidor.

Cliente Somente Leitura: essas informações são usadas para validar se o cliente que está se conectando ao ponto de extremidade de gerenciamento do cluster está apresentando a credencial correta para execução de ações somente leitura no cluster. Você pode especificar mais de um certificado para autorização para operações somente leitura.

- **Autorizar por.** Indica ao Service Fabric se ele precisa buscar esse certificado usando o nome do assunto ou a impressão digital. O uso do nome do assunto para autorização não é uma boa prática de segurança, mais acrescenta flexibilidade.
- **Nome da Entidade.** Isso será necessário apenas se você tiver especificado que a autorização ocorre pelo nome do assunto.
- **Clique em Impressão digital do Emissor.** Isso possibilita um nível adicional de verificação executada pelo servidor quando um cliente apresenta suas credenciais ao servidor.

## Atualizar os certificados no cluster

O Service Fabric permite que você especifique dois certificados, um principal e um secundário. Por padrão, aquele que você especifica no momento da criação é o certificado principal. Para adicionar outro certificado, você precisa implantá-lo nas VMs no cluster. A Etapa 2 (acima) descreve como você pode carregar um novo certificado no cofre de chave. Você pode usar o mesmo cofre de chave para isso, como você fez com o primeiro certificado. Para saber mais, confira [Implantar certificados para VMs de um cofre de chave gerenciado pelo cliente](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

Após a conclusão da operação, use o portal ou o Gerenciador de Recursos para indicar ao Service Fabric que você possui um certificado secundário que também pode ser usado. Tudo o que você precisa é uma impressão digital.

Este é o processo para adicionar um certificado secundário:

1. Vá até o portal e navegue até o recurso de clusters ao qual você deseja adicionar esse certificado.
2. Em **Configurações**, clique na configuração do certificado e insira a impressão digital do certificado secundário.
3. Clique em **Salvar**. Uma implantação será iniciada e, após a conclusão bem sucedida da implantação, você poderá usar o certificado principal ou secundário para realizar operações de gerenciamento no cluster.

![Captura de tela das impressões digitais do certificado no portal][SecurityConfigurations_02]

Este é o processo para remover um certificado antigo para que o cluster não o use:

1. Vá para o portal e navegue até as configurações de segurança do cluster.
2. Remova um dos certificados.
3. Clique em **Salvar**, o que inicia uma nova implantação. Após a conclusão da implantação, o certificado que você removeu não poderá ser usado para se conectar ao cluster.

>[AZURE.NOTE] Para um cluster seguro, você sempre precisará de pelo menos um certificado (primário ou secundário) válido (não revogado ou expirado) implantado ou não poderá acessar o cluster.


## Tipos de certificados usados pelo Service Fabric

### Certificados X.509

Os certificados digitais X.509 são usados normalmente para autenticar clientes e servidores, criptografar e assinar mensagens digitalmente. Para obter mais detalhes sobre esses certificados, acesse [Trabalhando com certificados](http://msdn.microsoft.com/library/ms731899.aspx) na biblioteca MSDN.

>[AZURE.NOTE]
-Certificados usados em clusters que executam cargas de trabalho de produção devem ser criados usando um serviço de certificado do Windows Server configurado corretamente ou obtidos de uma [AC (autoridade de certificação)](https://en.wikipedia.org/wiki/Certificate_authority) aprovada. - Nunca use na produção certificados de teste ou temporários criados com ferramentas como MakeCert.exe. - Para clusters que usa apenas para fins de teste, você pode optar por usar um certificado autoassinado.

### Certificados do servidor e certificados do cliente

#### Certificados X.509 do servidor

Os certificados de servidor têm a tarefa principal de autenticar o servidor (nó) para clientes ou autenticar um servidor (nó) para um servidor (nó). Uma das verificações iniciais quando um cliente ou um nó autentica um nó é verificar o valor do nome comum no campo Entidade. Esse nome comum ou um dos nomes alternativos do assunto dos certificados deve estar presente na lista de nomes comuns permitidos.

O artigo a seguir descreve como gerar certificados com SAN (nomes alternativos da entidade): [Como adicionar um nome alternativo da entidade a um certificado LDAP seguro](http://support.microsoft.com/kb/931351).

>[AZURE.NOTE] O campo Entidade pode conter vários valores, cada um prefixado com iniciais para indicar o tipo de valor. Geralmente, a inicialização é "CN" para o nome comum, por exemplo, "CN = www.contoso.com". Também é possível deixar o campo Assunto em branco. Se o campo opcional Nome alternativo da entidade estiver preenchido, ele deverá conter o nome comum do certificado e uma entrada por nome alternativo da entidade. Eles são inseridos como valores de Nome DNS.

O valor do campo Finalidades pretendidas do certificado deve incluir um valor apropriado, como "Autenticação do servidor" ou "Autenticação do cliente".

#### Certificados do cliente

Normalmente, os certificados de cliente não são emitidos por uma autoridade de certificação de terceiros. Em vez disso, o repositório Pessoal do local atual do usuário geralmente contém os certificados colocados lá por uma autoridade raiz, com uma finalidade de "Autenticação do cliente". O cliente pode usar esse certificado quando a autenticação mútua é necessária.

>[AZURE.NOTE] Todas as operações de gerenciamento no cluster do Service Fabric exigem certificados de servidor. Certificados de cliente não podem ser usados para gerenciamento.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Próximas etapas

- [Processo de atualização de Cluster de Malha do Serviço e as suas expectativas](service-fabric-cluster-upgrade.md)
- [Gerenciando seu aplicativo da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introdução ao modelo de Integridade da Malha de Serviço](service-fabric-health-introduction.md)
- [Segurança do aplicativo e RunAs](service-fabric-application-runas-security.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_0211_2016-->