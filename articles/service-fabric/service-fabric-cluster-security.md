<properties
   pageTitle="Como proteger um cluster do Service Fabric | Microsoft Azure"
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
   ms.date="11/10/2015"
   ms.author="chackdan"/>

# Protegendo um cluster do Service Fabric

Um cluster do Service Fabric é um recurso que pertence a você. Para impedir o acesso não autorizado ao recurso, você deverá protegê-lo, especialmente quando ele tiver cargas de trabalho de produção em execução. Este documento orienta você durante o processo.

##  Sobre quais cenários de segurança de cluster você deveria pensar?

O Service Fabric oferece segurança para os seguintes cenários:

1. **Segurança de Nó para Nó** ou Protegendo um cluster para comunicação de nó para nó. Protege a comunicação entre computadores/VMs no cluster. Isso faz com que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster

	![Nó para Nó][Node-to-Node]

2. **Segurança de Cliente para Nó** ou Proteger a comunicação de um cliente de malha com um nó específico no cluster. Autentica e protege as comunicações de clientes, o que garante que somente usuários autorizados possam acessar o cluster e os aplicativos implantados no cluster do Windows Fabric. Os clientes são identificados exclusivamente por meio de suas credenciais de segurança do Windows ou pelas credenciais de segurança do certificado deles.

	![Cliente para Nó][Client-to-Node]

	Para qualquer um desses cenários de comunicação (Nó para Nó ou Cliente para Nó), o Service Fabric dá suporte ao uso do [Certificado de Segurança](https://msdn.microsoft.com/library/ff649801.aspx) ou da [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx). As opções de segurança em nó para nó ou em cliente para nó são independentes umas das outras e podem ser iguais ou diferentes para cada cenário.

	No Azure, o Service Fabric utiliza os certificados do Servidor X509 que você especifica como parte das configurações do Tipo de Nó ao criar um cluster. Para obter uma visão geral rápida sobre o que esses certificados são e como você pode adquirir/criá-los, role até a parte inferior desta página.

3. **RBAC (Controle de Acesso Baseado em Função)**: capacidade de restringir as operações de administração de operações de somente leitura no cluster para um conjunto de certificados.

4. **Contas de Serviço e RunAs** : o próprio Service Fabric é executado como um processo do serviço Windows (Fabric.exe), e a conta de segurança na qual o processo do Fabric.exe é executado pode ser configurada. As contas de processo em que o Fabric.exe é executado em cada nó do cluster podem ser protegidas, bem como os processos de host de serviço que são ativados para cada serviço. Veja o documento [Segurança de aplicativo e RunAs](service-fabric-application-runas-security.md) para obter mais detalhes
  

## Como proteger um cluster de Malha de Serviço usando certificados.

Para configurar um cluster de malha de serviço seguro, você precisará de pelo menos um servidor / certificado X509. Você poderá, então, carregá-lo no Cofre da Chave do Azure e usá-lo no processo de criação do cluster

Há três etapas distintas

1. Adquirir o X509 certificado
2. Carregar o certificado no Cofre da Chave do Azure:
3. Fornecer o local e os detalhes do certificado para o processo de criação de cluster de malha de serviço.

 
## Etapa 1: Adquirir os certificados X509

1. Para os clusters que executam cargas de trabalho de produção, você deve usar um certificado x509 assinado pela [AC (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority) para proteger o cluster. Para obter detalhes sobre como obter esses certificados, vá para [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Para clusters que usados apenas para fins de teste, você pode optar por usar um certificado assinado automaticamente. A Etapa 2.5 guiará você pelas etapas do procedimento.


## Etapa 2: Carregar o certificado X509 no Cofre da Chave

Esse é um processo complicado; portanto, temos um módulo do PowerShell carregado em um repositório Git que faz isso para você.

**Etapa 2.1**: copiar esta pasta para o computador desde este [repositório Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Etapa 2.2**: verificar se o SDK do Azure 1.0 ou superior está instalado em seu computador.

**Etapa 2.3**: abrir uma janela do Powershell e importar o ServiceFabricRPHelpers.psm

```
Remove-Module ServiceFabricRPHelpers
```

Copie o seguinte e altere o caminho para o. psm1 de forma que fique igual ao do seu computador. Aqui está um exemplo

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```
  

**Etapa 2.4**: se você estiver usando um certificado já adquirido, siga estas etapas ou vá para a Etapa 2.5.


Faça logon na sua Conta do Azure

```
Login-AzureRmAccount
```

O script criará um novo grupo de recursos e/ou um cofre, se já não existirem.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file> 
```
Veja um script preenchido como exemplo.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx 
```

Depois que o script é concluído com êxito, você obtém uma saída semelhante à mostrada abaixo. Ela será necessária na Etapa 3.

1. **Impressão Digital do Certificado**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A
2. **SourceVault** /ID do Recurso do Cofre da Chave: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **URL do Certificado**/URL para o local do Certificado no Cofre da Chave: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea 

Você tem as informações necessárias para configurar um cluster seguro. Vá para a Etapa 3.


**Etapa 2.5**: se você quiser criar um novo certificado autoassinado e carregá-lo no Cofre da Chave.

Faça logon na sua Conta do Azure

```
Login-AzureRmAccount
```

O script criará um novo grupo de recursos e/ou um cofre, se já não existirem.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file> 
```
O OutputPath que você deu ao script conterá o novo certificado autoassinado carregado no Cofre da Chave.


**Observação** O DnsName <Cadeia de caracteres> especifica um ou mais nomes DNS a serem colocados na extensão de nome alternativo do assunto do certificado quando um certificado a ser copiado não for especificado por meio do parâmetro CloneCert. O primeiro nome DNS também é salvo como o Nome da assunto. Se nenhum certificado de autenticação for especificado, o primeiro nome DNS também será salvo como o Nome do emissor.

Você pode saber mais sobre como criar um certificado autoassinado em termos gerais em [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx)

eja um script preenchido como exemplo. ```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Como é um certificado autoassinado, você precisará importá-lo para seu repositório de computadores de "pessoas confiáveis" antes de usá-lo para se conectar a um cluster seguro. ```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
``` ```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

Depois que o script é concluído com êxito, você obtém uma saída semelhante à mostrada abaixo. Ela será necessária na Etapa 3.

1. **Impressão Digital do Certificado**: 64881409F4D86498C88EEC3697310C15F8F1540F
2. **SourceVault** /ID do Recurso do Cofre da Chave: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **URL do Certificado**/URL para o local do Certificado no Cofre da Chave: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea 

##Etapa 3: Configurar um cluster seguro 

Siga as etapas descritas no documento [Processo de criação de Cluster do Service Fabric](service-fabric-cluster-creation-via-portal.md) até chegar em Configurações de Segurança. A seguir, os passos para definir as Configurações de Segurança.

Os certificados que precisam ser usados são especificados no nível NodeType sob as Configurações de segurança. Você precisa especificar isso para cada NodeType no cluster. Embora este documento explique como fazer isso usando o portal, você pode fazer o mesmo usando um modelo ARM.

![SecurityConfigurations\_01][SecurityConfigurations_01]

Parâmetros obrigatórios

- **Modo de segurança** não deixe de selecionar ‘certificado x509’. Ele indica ao Service Fabric que você pretende configurar um cluster seguro. 
- **Nível de proteção de cluster** consulte este [documento de Nível de Proteção](https://msdn.microsoft.com/library/aa347692.aspx) para entender o que cada um desses valores significa. No entanto, permitimos três valores aqui: EncryptAndSign, Sign, None. É melhor manter o padrão de "EncryptAndSign", a menos que você saiba o que está fazendo.
- **Cofre de Origem** refere-se à ID do Recurso do cofre da chave e deve estar no formato do ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```

- **Certificado URL** refere-se à URL de local no seu cofre da chave para onde o certificado foi carregado e deve estar no formato do ```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```
- **Impressão Digital do Certificado** refere-se à impressão digital do certificado, que pode ser encontrada na URL especificada anteriormente.

Parâmetros opcionais - você pode opcionalmente especificar certificados adicionais que as máquinas do cliente usam para executar operações no cluster. Por padrão, a impressão digital que você especificou nos parâmetros obrigatórios é adicionada à lista autorizada de impressões digitais que são permitidas conforme as operações do cliente.

Cliente de administração - essas informações são usadas para validar se o cliente que está conectando-se ao ponto de extremidade de gerenciamento de cluster está, de fato, apresentando a credencial correta para executar ações de administrador e somente de leitura no cluster. você pode especificar mais de um certificado que você deseja autorizar para operações de Administração.


- **Autorizar por** – indica ao Service Fabric se ele precisa buscar esse certificado usando o nome do assunto ou a impressão digital. O uso do nome do assunto para autorizar é não uma boa prática de segurança, no entanto, permite mais flexibilidade.


- **Nome de assunto** só será necessário caso você tenha especificado a autorização pelo Nome do assunto
- **Impressão Digital do Emissor** possibilita um nível adicional de verificação que o servidor pode executar quando um cliente apresenta suas credenciais ao servidor.

Cliente de Somente Leitura - essas informações são usadas para validar se o cliente que está conectando-se ao ponto de extremidade de gerenciamento de cluster está, de fato, apresentando a credencial correta para executar ações de administrador e somente de leitura no cluster. você pode especificar mais de um certificado que você deseja autorizar para operações de somente leitura.


- **Autorizar por** – indica ao Service Fabric se ele precisa buscar esse certificado usando o nome do assunto ou a impressão digital. O uso do nome do assunto para autorizar é não uma boa prática de segurança, no entanto, permite mais flexibilidade.

- **Nome de assunto** só será necessário caso você tenha especificado a autorização pelo Nome do assunto
- **Impressão Digital do Emissor** possibilita um nível adicional de verificação que o servidor pode executar quando um cliente apresenta suas credenciais ao servidor.


## Como atualizar os certificados no cluster

A malha de serviços permite que você especifique dois certificados, um principal e um secundário. O que você especificou no momento da criação é, por padrão, o principal Para adicionar outro certificado, você precisa implantar esse certificado para as VMs no cluster. A Etapa 2 acima descreve como você pode carregar um novo certificado para o cofre da chave. Você pode usar o mesmo cofre da chave usado para o primeiro certificado.

Consulte o documento [Implantar certificados para VMs do cofre da chave gerenciado pelo cliente](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

Depois que a operação for concluída com êxito, vá para o portal ou por meio do ARM, indique na Malha de serviço que você possui um certificado secundário que pode ser usado também. Tudo o que você precisa é uma impressão digital.

Eis o processo: no portal, navegue até o recurso de cluster para o qual você deseja adicionar esse certificado, clique na configuração de certificado, insira a impressão digital do certificado secundário e pressione Salvar. Uma implantação será iniciada e após uma conclusão bem sucedida da implantação, você pode usar o certificado principal ou o secundário para realizar operações de gerenciamento no cluster.

![SecurityConfigurations\_02][SecurityConfigurations_02]

se você deseja remover um certificado agora, você pode fazer isso. Certifique-se de pressionar salvar após removê-lo, para que uma nova implantação seja iniciada. Depois que a implantação for concluída, o certificado que é removido não pode ser usado para conectar-se ao cluster. Para um cluster seguro, você sempre precisará de pelo menos um certificado válido (não revogado ou expirado) implantado ou você não poderá acessar o cluster.

Há um evento de diagnóstico que permite que você saiba se qualquer um dos certificados estão próximos de expirar.



## O que são certificados X509?

Os certificados digitais X509 são usados para autenticar clientes e servidores, criptografar e assinar mensagens digitalmente. Para obter mais detalhes sobre esses certificados, vá para [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx)

**Observação**

1. Os certificados usados em clusters que executam cargas de trabalho de produção. Eles devem ser criados usando um serviço de certificado do Windows Server configurado corretamente ou por meio de uma [AC (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority) aprovada.
2. Nunca use certificados temporários ou de teste criados com ferramentas como MakeCert.exe em produção
3. Para clusters que usados apenas para fins de teste, você pode optar por usar um certificado assinado automaticamente. 


## O que são Certificados do servidor e Certificados do cliente?

**Certificados do Servidor/X509**

Os Certificados de servidor têm a tarefa principal de autenticar o servidor (nó) para clientes ou servidor (nó) para servidor (nó). Uma das verificações iniciais quando um cliente ou um nó autentica um nó é comparar o valor do nome comum no campo Assunto, para garantir que ele esteja presente na lista de nomes comuns permitidos que tenha sido configurada. Esse nome comum ou um dos nomes alternativos do assunto dos certificados deve estar presente na lista de nomes comuns permitidos.

O artigo a seguir descreve como gerar certificados com nomes alternativos de assunto (SAN). [http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351)
 
**Observação:** o campo assunto pode conter vários valores, cada um prefixado com iniciais para indicar o valor. Geralmente, a inicialização é "CN" para o nome comum, por exemplo, "CN = www.contoso.com". Também é possível deixar o campo Assunto em branco. Observe também o campo de Nome alternativo da assunto opcional; se usado, deve conter o nome comum do certificado e uma entrada por nome alternativo do assunto. Eles são inseridos como valores de Nome DNS.

Além disso, observe que o valor do campo Finalidades pretendidas do certificado deve incluir um valor apropriado, como "Autenticação do servidor" ou "Autenticação do cliente".

**Certificados do cliente**

Normalmente, os certificados de cliente não são emitidos por uma autoridade de certificação de terceiros. Em vez disso, o repositório Pessoal do local atual do usuário geralmente contém os certificados colocados lá por uma autoridade raiz, com uma finalidade de "Autenticação do cliente". O cliente pode usar esse certificado quando a autenticação mútua é necessária. Todas as operações de gerenciamento no cluster de malha de serviço exigem certificados de Servidor. Certificados de cliente não devem ser usados.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
- [Processo de atualização de Cluster de Malha do Serviço e as suas expectativas](service-fabric-cluster-upgrade.md)
- [Gerenciando seu aplicativo da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introdução ao modelo de Integridade da Malha de Serviço](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_1217_2015-->