<properties
   pageTitle="Criptografia de Disco do Azure para VMs IaaS Windows e Linux | Microsoft Azure"
   description="O documento fornece uma visão geral de Criptografia de Disco do Microsoft Azure para VMs IaaS Windows e Linux."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="devtiw"/>


#Criptografia de Disco do Azure para VMs IaaS Windows e Linux

O Microsoft Azure tem o compromisso sério de garantir a privacidade e a soberania dos seus dados e permite que você controle os dados hospedados no Azure usando uma variedade de tecnologias para criptografar, controlar e gerenciar chaves de criptografia, bem como auditar e controlar o acesso aos dados. Isso permite que os clientes do Azure tenham a flexibilidade de escolher a solução que melhor atenda às necessidades de negócios. Neste artigo, apresentaremos a você uma nova solução de tecnologia, "Criptografia de Disco do Azure para VMs IaaS Windows e Linux" para ajudá-lo a proteger seus dados e atender às obrigações de conformidade e segurança da organização. O documento fornece orientações detalhadas sobre como usar os recursos de criptografia de disco do Azure, incluindo os cenários com suporte e as experiências de usuário.

**Observação**: determinadas recomendações contidas neste documento podem causar o aumento do uso de dados, de rede ou dos recursos de computação, resultando em custos adicionais de licença ou assinatura.

## Visão geral

A Criptografia de Disco do Azure é um novo recurso que lhe permite criptografar os discos de suas máquinas virtuais IaaS Windows e Linux. A Criptografia de Disco do Azure aproveita o recurso padrão da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) do Windows e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o SO e os discos de dados. A solução é integrada ao [Cofre da Chave do Azure](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de cofre da chave, garantindo que todos os dados nos discos de máquina virtual sejam criptografados em repouso no armazenamento do Azure.

O Azure Disk Encryption para VMs IaaS do Windows agora está em [Disponibilidade geral](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/15/azure-disk-encryption-for-windows-virtual-machines-reaches-general-availability/) na região da Austrália. A disponibilidade geral para VMs IaaS do Windows para outras regiões ocorrerá em breve.

### Cenários de criptografia

A solução Azure Disk Encryption dá suporte aos seguintes cenários do cliente:

- Habilitar a criptografia na nova VM IaaS criada usando chaves de criptografia e VHD previamente criptografado
- Habilitar a criptografia na nova VM IaaS criada das imagens da Galeria do Azure 
- Ativar a criptografia em VMs IaaS existentes já em execução no Azure 
- Desabilitar a criptografia em VMs IaaS do Windows  

A solução dá suporte aos seguintes itens para VMs IaaS quando habilitada no Microsoft Azure:

- Integração com o Cofre da Chave do Azure

- [VMs de IaaS das séries Standard A, D e G](https://azure.microsoft.com/pricing/details/virtual-machines/)

- Habilitar a criptografia em VMs IaaS Windows e Linux

- Desabilitar a criptografia em VMs IaaS do Windows

- Habilitar a criptografia em VMs IaaS executando o sistema operacional Windows Client

- Habilitar a criptografia em volumes com caminhos de montagem

- Todas as regiões públicas do Azure tem suporte na preview pública e a região da Austrália do Azure tem suporte em GA para VMs de IaaS do Windows

A solução não dá suporte aos seguintes cenários, recursos e tecnologia na versão:

- VMs IaaS Basic e Standard DS (Armazenamento Premium)

- VMs IaaS criadas usando o método de criação de VM clássico

- Habilitar a criptografia de disco do sistema operacional em VMs IaaS Linux já em execução no Azure

- Desabilitar a criptografia em VM IaaS Linux, habilitada por meio do Azure Disk Encryption

- Integração com o Serviço de Gerenciamento de Chaves no local

- Windows Server 2016 Technical Preview 3 e acima

- Arquivos do Azure (compartilhamento de arquivos do Azure), NFS (Network File System), Volumes dinâmicos, Sistemas RAID com base em software


### Recursos de criptografia

Quando você habilita e a implanta a criptografia de disco do Azure para VMs IaaS do Azure, os seguintes recursos são habilitados, dependendo da configuração fornecida:

- Criptografia do volume do sistema operacional para proteger o volume de inicialização em repouso no armazenamento cliente

	- A criptografia do volume do sistema operacional VM IaaS do Linux já em execução no Azure não tem suporte no momento. A criptografia do volume do sistema operacional para VM IaaS do Linux tem suporte somente para o cenário VHD previamente criptografado
	
- Criptografia de volume de dados/s para proteger os volumes de dados em repouso no armazenamento cliente

- Desabilitar a criptografia em VMs IaaS do Windows

- Proteção das chaves de criptografia e segredos na assinatura do cofre da chave do Azure cliente

- Comunicando o status de criptografia da VM IaaS criptografada

- Remoção de definições de configuração de criptografia de disco da máquina virtual IaaS

A criptografia de disco do Azure para VMs IaaS para solução Windows e Linux inclui a extensão de criptografia de disco para Windows, a extensão de criptografia de disco para Linux, os cmdlets de criptografia de disco do PowerShell, os cmdlets CLI de criptografia de disco e os modelos de Gerenciador de Recursos do Azure de criptografia de disco. Há suporte para a solução de criptografia de disco do Azure em VMs IaaS executando o Windows ou o sistema operacional Linux. Para obter mais detalhes sobre os sistemas operacionais com suporte, consulte a seção de pré-requisitos abaixo.

**Nota:** não há nenhuma taxa para criptografar discos de VM com o Azure Disk Encryption.

### Proposta de valor

A solução de Gerenciamento de Criptografia de Disco do Azure habilita as seguintes necessidades de negócios na nuvem:

-   As VMs IaaS ficam protegidas em repouso usando a tecnologia de criptografia padrão da indústria para tratar da segurança organizacional e de requisitos de conformidade.

-   A inicialização da VM IaaS é de acordo com chaves e políticas controladas pelo cliente, e este pode auditar o uso no Cofre da Chave.


### Fluxo de trabalho de criptografia
As etapas de alto nível necessárias para habilitar a criptografia de disco de VMs Windows e Linux são:

1. O cliente escolhe o cenário de criptografia dos três cenários de criptografia acima

2. O cliente aceita a criptografia de disco por meio do modelo ARM de criptografia de disco do Azure, cmdlets do PS ou comando CLI e especifica a configuração de criptografia

    - Para o cenário VHD criptografado do cliente, o cliente carrega o VHD criptografado para sua conta de armazenamento, o material de criptografia de chave para seu cofre da chave e fornece a configuração de criptografia para habilitar a criptografia em uma nova VM IaaS

    - Para novas VMs criadas na galeria do Azure e de VMs existente já em execução no Azure, o cliente fornece a configuração de criptografia para habilitar a criptografia na VM IaaS

3. O cliente concede acesso à plataforma Azure para ler o material de chave de criptografia (chaves de criptografia BitLocker para sistemas Windows e senha para Linux) de seu cofre da chave para habilitar a criptografia na VM IaaS

4. O cliente fornece a identidade do AD do Azure para gravar o material de chave de criptografia em seu cofre da chave para habilitar a criptografia na VM IaaS para os cenários 2 e 3 acima.

5.  O gerenciamento de serviços do Azure atualiza o modelo de serviço da VM com criptografia e configuração do cofre da chave, além de provisionar a VM criptografada para o cliente

![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.JPG)

### Fluxo de trabalho de descriptografia

As etapas de alto nível necessárias para desabilitar a criptografia de disco de VMs IaaS do Windows são:

1. O cliente opta por desabilitar a criptografia (descriptografia) em uma VM IaaS do Windows em execução no Azure por meio do modelo ARM do Azure Disk Encryption ou cmdlets PS e especifica a configuração de descriptografia.

2. A etapa de desabilitar a criptografia tem suporte apenas em VM IaaS do Windows e não tem suporte em VM IaaS do Linux.

3. A etapa de desabilitar a criptografia desabilita a criptografia do sistema operacional ou volume de dados na VM IaaS do Windows em execução.

4. O gerenciamento de serviços do Azure atualiza o modelo de serviço da VM e a VM IaaS do Windows é marcada como descriptografada. Os conteúdos da VM não estão mais criptografados no rest.

5. A desabilitação da criptografia não exclui o cofre de chaves do cliente e o material de chave de criptografia, que são as Chaves de Criptografia BitLocker para Windows e a Senha para Linux.

## Pré-requisitos

Abaixo estão os pré-requisitos para habilitar a Criptografia de Disco do Azure nas VMs IaaS do Azure para os cenários com suporte destacados na seção Visão geral

- O usuário deve ter uma assinatura ativa válida do Azure para criar recursos no Azure nas regiões com suporte

- A Criptografia de Disco do Azure tem suporte nas seguintes SKUs do Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. A solução não tem suporte no sistema operacional Windows Server 2008. Não há suporte para o Windows Server 2016 Technical Preview 3 nesta versão.

O Azure Disk Encryption tem suporte nos seguintes SKUs de cliente Windows: cliente Windows 8 e cliente Windows 10.

**Observação**: no caso do Windows Server 2008 R2, o .NET Framework 4.5 deve ser instalado antes de habilitar a criptografia no Azure. Você pode instalá-lo com o Windows Update, instalando a atualização opcional “Microsoft .NET Framework 4.5.2 para sistemas Windows Server 2008 R2 baseados em x64 ([KB2901983](https://support.microsoft.com/kb/2901983))”

- O Azure Disk Encryption tem suporte nos seguintes SKUs de servidor Linux: Ubuntu, CentOS, SUSE e SLES (SUSE Linux Enterprise Server) e Red Hat Enterprise Linux.

- Todos os recursos (por exemplo, Cofre da Chave, Conta de armazenamento, VM, Rede Virtual, etc.) devem pertencer à mesma região do Azure e assinatura.

**Observação** - A criptografia de disco do Azure exige que o Cofre da Chave e as VMs residam na mesma região do Azure. Configurá-los na região separada causará uma falha na habilitação do recurso de criptografia de disco do Azure.

- Para instalar e configurar o Cofre de Chaves do Azure para uso do Azure Disk Encryption, veja a seção **Configurando Cofre da Chave do Azure para uso de criptografia de disco do Azure** na seção de *Pré-requisitos* deste artigo.

- Para instalar e configurar o aplicativo do Azure AD no Azure Active Directory para uso do Azure Disk Encryption, veja a seção **Configurar o aplicativo Azure AD no Azure Active Directory** na seção de *Pré-requisitos* deste artigo.

- Para instalar e configurar a política de Acesso do Cofre de Chaves para o aplicativo do Azure AD, veja a seção **Configurando a política de acesso ao Cofre da Chave para o aplicativo Azure AD** na seção de *Pré-requisitos* deste artigo.

- Para preparar um VHD do Windows pré-criptografado, veja a seção **Preparando um VHD do Windows pré-criptografado** no Apêndice deste artigo.

- Para preparar um VHD do Linux pré-criptografado, veja a seção **Preparando um VHD do Linux previamente criptografado** no Apêndice deste artigo.

- A plataforma Azure precisa acessar as chaves de criptografia ou segredos no Cofre da Chave do Azure do cliente para disponibilizá-los para a VM a fim de que ela possa inicializar e descriptografar o volume do sistema operacional da máquina virtual. Para conceder permissões à plataforma Azure para acessar o Cofre da Chave, a propriedade **enabledForDiskEncryption** deve ser definida no Cofre da Chave para esse requisito. Veja a seção **Configurando Cofre da Chave do Azure para uso de criptografia de disco do Azure** no Apêndice deste artigo para obter mais detalhes.

- O segredo do Cofre da Chave e as URLs da KEK (chave de criptografia de chave) devem ter a versão controlada. O gerenciamento de serviços do Azure impõe essa restrição de controle de versão. Veja a seguir exemplos de segredo válido e URL de KEK:

	- Exemplo de URL de segredo válida:

		**https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- Exemplo de KEK de KRK válida:

		**https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- A criptografia de disco do Azure não dá suporte a números de porta especificadas como parte do segredo do Cofre da Chave e das URLs de KEK. Veja a seguir exemplos de URL de Cofre da Chave com suporte:

 	- URL do Cofre da Chave inaceitável

		**https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- URL do Cofre da Chave aceitável:

		**https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Para habilitar o recurso de Criptografia de Disco do Azure, as VMs IaaS devem atender aos seguintes requisitos de configuração do ponto de extremidade de rede:

	- A VM IaaS deve ser capaz de se conectar ao ponto de extremidade do Active Directory do Azure [Login.windows.net] para obter um token para conectar-se ao cofre da chave do Azure

	- A VM IaaS deve ser capaz de se conectar ao ponto de extremidade do Azure chave cofre para gravar as chaves de criptografia do cofre da chave do cliente

	- A VM IaaS deve ser capaz de se conectar ao ponto de extremidade de armazenamento do Azure que hospeda o repositório de extensão do Azure e a conta de armazenamento do Azure que hospeda os arquivos VHD

**Observação:** se a sua política de segurança limitar o acesso das VMs do Azure à Internet, você poderá resolver o URI acima ao qual você precisa de conectividade e configurar uma regra específica para permitir a conectividade de saída para o IPs.

- Use a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a versão mais recente do [Azure PowerShell versão 1.3.0](https://github.com/Azure/azure-powershell/releases/download/v1.3.0-March2016/azure-powershell.1.3.0.msi) e acima

**Observação:**não há suporte para o Azure Disk Encryption no [SDK do Azure PowerShell versão 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Se você estiver recebendo um erro relacionado ao uso do PowerShell 1.1.0, consulte o artigo [Azure Disk Encryption Error Related to Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx) (Erro do Azure Disk Encryption relacionado ao Azure PowerShell 1.1.0).

- Para executar qualquer um dos comandos do Azure CLI e associá-lo a sua assinatura do Azure, você primeiro deve instalar a versão do Azure CLI:

	- Para instalar a CLI e associá-la à sua assinatura do Azure, veja [Como instalar e configurar a CLI do Azure](../xplat-cli-install.md)

	- Com a CLI do Azure para Mac, Linux e Windows com o Gerenciador de Recursos do Azure, clique [aqui](azure-cli-arm-commands.md)

- A solução de criptografia de disco do Azure usa protetor de chave externa BitLocker para VMs IaaS do Windows. Se suas VMs estão ingressadas no mesmo domínio, não use políticas de grupo que imponham protetores TPM. Veja [este artigo](https://technet.microsoft.com/library/ee706521) para obter detalhes sobre a política de grupo “Permitir BitLocker sem um TPM compatível”.

- O script do PowerShell de pré-requisito do Azure Disk Encryption para criar o aplicativo do Azure AD, criar o novo cofre de chaves ou instalar o cofre de chaves existente e habilitar a criptografia está localizado [aqui](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).

### Configurando Cofre da Chave do Azure para uso de criptografia de disco do Azure

A criptografia de disco do Azure protege as chaves de criptografia de disco e os segredos em seu Cofre da Chave do Azure. Siga as etapas em cada uma das seções abaixo para configurar o Cofre da Chave para uso de criptografia de disco do Azure.

#### Criar um novo Cofre da Chave
Para criar um novo Cofre da Chave, use uma das duas opções listadas abaixo:

- Use o modelo do ARM “101-Create-KeyVault” localizado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)
- Use os cmdlets do Cofre de Chaves do Azure PowerShell.

**Observação:** se você já tiver uma configuração de Cofre da Chave para a sua assinatura, vá para a próxima seção.

#### Provisionamento de uma chave de criptografia de chave (opcional)

Se quiser usar uma chave de criptografia de chave (KEK) para uma camada adicional de segurança a fim de encapsular as chaves de criptografia do BitLocker, você deve adicionar uma KEK ao seu Cofre da Chave para uso no processo de provisionamento. Use o cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) para criar uma nova Chave de criptografia de Chave no Cofre da Chave. Para obter mais detalhes, confira a [documentação do Cofre da Chave](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

#### Definir permissões do Cofre da Chave para permitir o acesso da plataforma Azure a chaves e segredos

A plataforma Azure precisa acessar as chaves de criptografia ou segredos no seu Cofre da Chave do Azure para disponibilizá-los para a VM a fim de que ela possa inicializar e descriptografar os volumes. Para conceder permissões à plataforma Azure para acessar o Cofre da Chave, a propriedade *enabledForDiskEncryption* deve ser definida no Cofre da Chave. Você pode definir a propriedade enabledForDiskEncryption em seu cofre da chave usando o cmdlet PS do Cofre da Chave:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Você precisa definir a propriedade *enabledForDiskEncryption* no Cofre da Chave, como indicado anteriormente. Você pode definir a propriedade visitando https://resources.azure.com. Verifique se as propriedades detalhadas acima estão definidas corretamente; do contrário, haverá falha na implantação.

#### Configurar o aplicativo Azure AD no Active Directory do Azure

Quando a criptografia precisa ser habilitada em uma VM em execução no Azure, a criptografia de disco do Azure gera e grava as chaves de criptografia no Cofre da Chave. O gerenciamento de chaves de criptografia de Cofre da Chave precisa de autenticação do AD do Azure.

Para essa finalidade, um aplicativo Azure AD deve ser criado. As etapas detalhadas para registrar um aplicativo podem ser encontradas aqui, na seção “Obter uma identidade para o aplicativo” desta [postagem no blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). A postagem também contém um número de exemplos úteis de provisionamento e configuração do Cofre da Chave. Para fins de autenticação, tanto a autenticação baseada em segredo do cliente quanto a autenticação Azure AD baseada em certificado do cliente podem ser utilizadas.

##### Autenticação do AD do Azure baseada em segredo do cliente

As seções a seguir têm as etapas necessárias para configurar uma autenticação do Azure AD baseada em segredo do cliente.

##### Criar um novo aplicativo Azure AD usando o Azure PowerShell

Use o cmdlet do PowerShell abaixo para criar um novo aplicativo Azure AD:

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**Observação:** $azureAdApplication.ApplicationId é o ClientID do AD do Azure e $aadClientSecret é o Segredo do cliente que deve ser usado posteriormente para habilitar o ADE. Você deve proteger de forma adequada o segredo do cliente do Azure AD.


##### Provisionando a ID do cliente e o segredo do AD do Azure usando o Portal de Gerenciamento de Serviço do Azure

A ID do Cliente e o segredo do Azure AD também podem ser provisionados pelo Portal de Gerenciamento de Serviços do Azure em https://manage.windowsazure.com; siga as etapas abaixo para realizar essa tarefa:

1\. Clique na guia do Active Directory conforme mostrado na figura abaixo:

![Criptografia de Disco do Azure](./media/azure-security-disk-encryption\disk-encryption-fig3.JPG)

2\. Clique em Adicionar Aplicativo e digite o nome do aplicativo, conforme mostrado abaixo:

![Criptografia de Disco do Azure](./media/azure-security-disk-encryption\disk-encryption-fig4.JPG)

3\. Clique no botão de seta e configure as propriedades do aplicativo conforme mostrado abaixo:

![Criptografia de Disco do Azure](./media/azure-security-disk-encryption\disk-encryption-fig5.JPG)

4\. Clique na marca de seleção no canto inferior esquerdo para concluir. A página de configuração do aplicativo é exibida. Observe que a ID do cliente do AD do Azure está localizada na parte inferior da página, conforme mostrado na figura a seguir.

![Criptografia de Disco do Azure](./media/azure-security-disk-encryption\disk-encryption-fig6.JPG)

5\. Salve o segredo do cliente do Azure AD clicando no botão Salvar. Clique no botão Salvar e observe o segredo da caixa de texto de chaves. Ele é o segredo do cliente do Azure AD. Você deve proteger adequadamente o segredo do cliente no Azure AD.

![Criptografia de Disco do Azure](./media/azure-security-disk-encryption\disk-encryption-fig7.JPG)


**Observação:** não há suporte para o fluxo acima no Portal.

##### Usa um aplicativo existente

Para executar os comandos a seguir, você precisa do módulo do PowerShell do Azure AD, que pode ser obtido [aqui](https://technet.microsoft.com/library/jj151815.aspx).

**Observação:** os comandos abaixo devem ser executados em uma nova janela do PowerShell. NÃO use o Azure PowerShell ou a janela do Gerenciador de Recursos do Azure para executar esses comandos. A razão para essa recomendação é que esses cmdlets estão no módulo MSOnline ou no PowerShell do AD do Azure.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### Autenticação baseada em certificado do AD do Azure

As seções a seguir têm as etapas necessárias para configurar uma autenticação do Azure AD baseada em certificado.

##### Criar um novo aplicativo Azure AD

Execute os cmdlets do PowerShell abaixo para criar um novo aplicativo Azure AD:

**Observação:** substitua a cadeia de caracteres “suasenha” abaixo com sua senha segura e proteja a senha.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Depois de concluir essa etapa, carregue um arquivo. pfx para o Cofre da Chave e habilite a política de acesso necessária para implantar esse certificado em uma máquina virtual.

##### Usar um aplicativo Azure AD existente
Se você estiver configurando a autenticação baseada em certificado para um aplicativo existente, use os cmdlets do PowerShell abaixo. Execute-os em uma nova janela do PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Depois de concluir essa etapa, carregue um arquivo. pfx para o Cofre da Chave e habilite a política de acesso necessária para implantar esse certificado em uma máquina virtual.

##### Carregar um arquivo PFX para a Chave do Cofre
Leia esta [postagem no blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) para obter uma explicação detalhada sobre como esse processo funciona. No entanto, os cmdlets do PowerShell abaixo são tudo de que você precisa para esta tarefa. Execute-os no console do Azure PowerShell:

**Observação:** substitua a cadeia de caracteres “suasenha” abaixo com sua senha segura e proteja a senha.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### Implante um certificado no Cofre da Chave em uma VM existente
Depois de concluir o carregamento de PFX, use as etapas abaixo para implantar um certificado no Cofre da Chave em uma VM existente:

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### Configurando a política de acesso ao Cofre da Chave para o aplicativo Azure AD

Seu aplicativo Azure AD precisa de direitos para acessar as chaves ou os segredos no cofre. Use o cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) para conceder permissões para o aplicativo usando a ID do cliente (que foi gerada quando o aplicativo foi registrado) como o valor do parâmetro –ServicePrincipalName. Você pode ler [esta postagem no blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) para obter alguns exemplos disso. A seguir, você também tem um exemplo de como executar essa tarefa por meio do PowerShell:

    $keyVaultName = ‘yourKeyVaultName’
    $aadClientID = '<youAadAppClientID>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys all -PermissionsToSecrets all

## Terminologia

Use a tabela de terminologia como referência para entender alguns dos termos comuns usados por essa tecnologia:


| Terminologia | Definição |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AD do Azure | Azure AD significa [Active Directory do Azure](https://azure.microsoft.com/documentation/services/active-directory/). Uma conta do AD do Azure é um pré-requisito para autenticar, armazenar e recuperar segredos do Cofre da Chave. |
| Cofre da Chave do Azure [AKV] | O Cofre da Chave do Azure é um serviço de gerenciamento de chaves de criptografia baseado em Módulos de Segurança de Hardware validados pelo FIPS para proteger suas chaves criptográficas e segredos confidenciais com segurança. Veja a documentação [Cofre da Chave](https://azure.microsoft.com/services/key-vault/) para obter mais detalhes. |
| ARM | Gerenciador de Recursos do Azure |
| BitLocker | O [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma tecnologia de criptografia de volume do Windows reconhecida pela indústria e usada para habilitar a criptografia de disco em VMs de IaaS do Windows |
| BEK | As chaves de criptografia do BitLocker são usadas para criptografar o volume de inicialização do sistema operacional e os volumes de dados. As chaves do BitLocker são protegidas no cofre da chave do Azure do cliente como segredos. |
| CLI | [Interface de linha de comando do Azure](../xplat-cli-install.md) |
| DM-Crypt | [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) é o subsistema de criptografia de disco transparente baseado em Linux usado para habilitar a criptografia de disco nas VMs de IaaS do Linux |
| KEK | Chave de criptografia de chave é a chave assimétrica (RSA 2048) usada para proteger ou encapsular o segredo, se desejado. Você pode fornecer uma tecla protegida por HSM ou por software. Para obter mais detalhes, confira a documentação do [Cofre da Chave do Azure](https://azure.microsoft.com/services/key-vault/) para obter mais detalhes |
| Cmdlets de DNS | [Cmdlets do PowerShell do Azure](powershell-install-configure.md) |

## Cenários de implantação de criptografia de disco e experiências de usuário

Há muitos cenários para habilitar a criptografia de disco e as etapas podem variar de acordo com o cenário. As seções a seguir abordarão esses cenários com mais detalhes.

### Habilite a criptografia na nova VM IaaS criada da galeria do Azure

A criptografia de disco pode ser habilitada em VMs novas de IaaS do Windows na galeria do Azure no Azure usando o modelo do ARM publicado [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image). Clique no botão "Implantar no Azure" no modelo de início rápido do Azure, insira a configuração de criptografia na folha de parâmetros e clique em OK. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os termos legais e o contrato e clique no botão Criar para habilitar a criptografia na VM IaaS nova.

**Observação:** esse modelo cria uma nova VM do Windows criptografada usando a imagem de galeria do Windows Server 2012.

Você pode ver os detalhes de parâmetros do modelo do ARM para uma nova VM do cenário de galeria do Azure que usa a ID do cliente do AD do Azure na tabela a seguir:

| Parâmetro | Descrição|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName | Especifique um nome de usuário para a máquina virtual |
| adminPassword | Senha de usuário de administrador para a máquina virtual |
| newStorageAccountName | Nome da conta de armazenamento para armazenar sistema operacional e VHDs de dados |
| vmSize | O tamanho da VM. Atualmente, há suporte somente para séries Standard A, D e G |
| virtualNetworkName | Nome da VNet à qual a NIC da VM deve pertencer. |
| subnetName | Nome da sub-rede na vNet à qual a NIC da VM deve pertencer. |
| AADClientID | ID de cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre da chave |
| AADClientSecret | Segredo do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre da chave |
| keyVaultResourceID, ResourceID | Identificar o recurso de cofre da chave no ARM. Você pode obtê-lo usando o cmdlet do PowerShell: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).ResourceId |
| keyVaultURL | URL do cofre da chave no qual a chave do BitLocker deve ser carregada. Você pode obtê-lo usando o cmdlet: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI |
| keyEncryptionKeyURL | URL da chave de criptografia de chave que é usada para criptografar a chave gerada do BitLocker. Isso é opcional. |
| vmName | Nome da VM em que a operação de criptografia deve ser realizada


**Observação:** KeyEncryptionKeyURL é um parâmetro opcional. Você pode usar seu próprio KEK para proteger ainda mais a chave de criptografia de dados (senha secreta) no Cofre da Chave.

### Habilite a criptografia na nova VM IaaS criada usando VHD criptografado pelo cliente e chaves de criptografia

Nesse cenário, você pode habilitar a criptografia usando o modelo do ARM, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicarão em mais detalhes os comandos CLI e modelo ARM.

#### Usando modelo ARM

A criptografia de disco pode ser habilitada no VHD criptografado pelo cliente usando o modelo ARM publicado [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm). Clique no botão "Implantar no Azure" no modelo de início rápido do Azure, insira a configuração de criptografia na folha de parâmetros e clique em OK. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os termos legais e o contrato e clique no botão Criar para habilitar a criptografia na VM IaaS nova.

Os detalhes de parâmetro de modelo ARM para cenário de VHD criptografado do cliente estão descritos na tabela abaixo:

| Parâmetro | Descrição|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | Nome da conta de armazenamento para armazenar VHDs de sistema operacional Esta conta de armazenamento já deve ter sido criada no mesmo grupo de recursos e no mesmo local da VM |
| osVhdUri | URI do VHD do sistema operacional da conta de armazenamento |
| osType | Tipo de produto do sistema operacional (Windows/Linux) |
| virtualNetworkName | Nome da VNet à qual a NIC da VM deve pertencer. Isso já deve ter sido criado no mesmo grupo de recursos e no mesmo local da VM |
| subnetName | Nome da sub-rede na vNet à qual a NIC da VM deve pertencer. |
| vmSize | O tamanho da VM. Atualmente, há suporte somente para séries Standard A, D e G |
| keyVaultResourceID | ResourceID que identifica o recurso de cofre da chave no ARM. Você pode obtê-lo usando o cmdlet do PowerShell: (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId |
| keyVaultSecretUrl | ​URL da chave de criptografia de disco provisionada no cofre da chave |
| keyVaultKekUrl | URL da chave de criptografia de chave que serve para criptografar a chave de criptografia de disco gerada. |
| ​vmName | ​Nome da VM IaaS   



####Usando os cmdlets do PowerShell

A criptografia de disco pode ser habilitada no VHD criptografado pelo cliente usando os cmdlets do PS publicados [aqui](https://msdn.microsoft.com/library/azure/mt603746.aspx).

####Usando comandos da CLI

Siga as etapas abaixo para habilitar a criptografia de disco para esse cenário usando comandos CLI:

1. Definir políticas de acesso no cofre de chave:
	- Defina o sinalizador ‘EnabledForDiskEncryption’: “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Defina as permissões do aplicativo Azure AD para gravar segredos no Cofre da Chave: “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. Para habilitar a criptografia em uma VM existente/em execução, digite: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Obtenha o status de criptografia: *“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”*
4. Para habilitar a criptografia em uma nova VM usando VHD criptografado do cliente, use os parâmetros abaixo com o comando "azure vm create”:
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Habilitar a criptografia na VM IaaS do Windows existente ou em execução no Azure

Nesse cenário, você pode habilitar a criptografia usando o modelo do ARM, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicarão em mais detalhes como habilitá-la usando comandos CLI e modelo ARM.

#### Usando modelo ARM

A criptografia de disco pode ser habilitada em VMs de IaaS do Windows existentes/em execução no Azure usando o modelo ARM publicado [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm). Clique no botão "Implantar no Azure" no modelo de início rápido do Azure, insira a configuração de criptografia na folha de parâmetros e clique em OK. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os termos legais e o contrato e clique no botão Criar para habilitar a criptografia em VM IaaS existente/em execução.

Os detalhes de parâmetros do modelo do ARM para um cenário de VM existente/em execução que usa a ID do cliente do AD do Azure na tabela abaixo:

| Parâmetro | Descrição|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​ID de cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre da chave |
| AADClientSecret | ​Segredo do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre da chave |
| keyVaultName | Nome do cofre da chave no qual a chave do BitLocker deve ser carregada. É possível obtê-lo usando o cmdlet: (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL | URL da chave de criptografia de chave que é usada para criptografar a chave gerada do BitLocker. Opcional se você selecionar "nokek" na lista suspensa UseExistingKek. Se selecionar "kek" na lista suspensa UseExistingKek, você deverá digitar o valor de keyEncryptionKeyURL |
| ​volumeType | ​Tipo de volume em que a operação de criptografia é executada. Os valores válidos são "OS", "Data", "All" |
| sequenceVersion | Versão de sequência da operação de BitLocker. Aumente esse número de versão cada vez que uma operação de criptografia de disco for executada na mesma VM |
| ​vmName | ​Nome da VM em que a operação de criptografia deve ser realizada


**Observação:** KeyEncryptionKeyURL é um parâmetro opcional. Você pode usar seu próprio KEK para proteger ainda mais a chave de criptografia de dados (segredo de criptografia BitLocker) no Cofre da Chave.

#### Usando os cmdlets do PowerShell

Veja a postagem no blog **Explorar a criptografia de disco do Azure com o Azure PowerShell** [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) para obter detalhes sobre como habilitar a criptografia usando a Criptografia de Disco do Azure usando cmdlets do PS.

#### Usando comandos da CLI

Siga as etapas abaixo para ativar a criptografia em VM Windows IaaS existente/em execução no Azure usando comandos CLI:

1. Definir políticas de acesso no cofre de chave:
	- Defina o sinalizador ‘EnabledForDiskEncryption’: “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Defina as permissões do aplicativo Azure AD para gravar segredos no Cofre da Chave: “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. Para habilitar a criptografia em uma VM existente/em execução, digite: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Obtenha o status de criptografia: *“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”*
4. Para habilitar a criptografia em uma nova VM usando VHD criptografado do cliente, use os parâmetros abaixo com o comando "azure vm create”:
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Habilitar a criptografia na VM IaaS do Linux existente ou em execução no Azure

A criptografia de disco pode ser habilitada em VMs de IaaS do Linux existentes/em execução no Azure usando o modelo ARM publicado [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). Clique no botão "Implantar no Azure" no modelo de início rápido do Azure, insira a configuração de criptografia na folha de parâmetros e clique em OK. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os termos legais e o contrato e clique no botão Criar para habilitar a criptografia em VM IaaS existente/em execução.

Os detalhes de parâmetros do modelo do ARM para um cenário de VM existente/em execução que usa a ID do cliente do AD do Azure descrita na tabela abaixo:

| Parâmetro | Descrição|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​ID de cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre da chave |
| AADClientSecret | ​Segredo do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre da chave |
| keyVaultName | Nome do cofre da chave no qual a chave do BitLocker deve ser carregada. É possível obtê-lo usando o cmdlet: (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL | URL da chave de criptografia de chave que é usada para criptografar a chave gerada do BitLocker. Opcional se você selecionar "nokek" na lista suspensa UseExistingKek. Se selecionar "kek" na lista suspensa UseExistingKek, você deverá digitar o valor de keyEncryptionKeyURL |
| ​volumeType | ​Tipo de volume em que a operação de criptografia é executada. O valor com suporte é "Data". A VM do Linux não dá suporte à habilitação de criptografia no volume do sistema operacional em VM do Linux em execução |
| sequenceVersion | Versão de sequência da operação de BitLocker. Aumente esse número de versão cada vez que uma operação de criptografia de disco for executada na mesma VM |
| ​vmName | ​Nome da VM em que a operação de criptografia deve ser realizada
| Senha | Digite uma senha forte como chave de criptografia de dados |                                                                                                                                                                                                                                                      

**Observação:** KeyEncryptionKeyURL é um parâmetro opcional. Você pode usar seu próprio KEK para proteger ainda mais a chave de criptografia de dados (senha secreta) no Cofre da Chave.

#### Comandos de CLI

A criptografia de disco pode ser habilitada no VHD criptografado pelo cliente usando o comando da CLI instalado [daqui](../xplat-cli-install.md). Siga as etapas abaixo para ativar a criptografia em VM Linux IaaS existente/em execução no Azure usando comandos CLI:

1. Definir políticas de acesso no cofre de chave:
	- Defina o sinalizador ‘EnabledForDiskEncryption’: “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Defina as permissões do aplicativo Azure AD para gravar segredos no Cofre da Chave: “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. Para habilitar a criptografia em uma VM existente/em execução, digite: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Obtenha o status de criptografia: “azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”
4. Para habilitar a criptografia em uma nova VM usando VHD criptografado do cliente, use os parâmetros abaixo com o comando "azure vm create”:
	- *disk-encryption-key-vault-id <disk-encryption-key-vault-id>*
	- *disk-encryption-key-url <disk-encryption-key-url>*
	- *key-encryption-key-vault-id <key-encryption-key-vault-id>*
	- *key-encryption-key-url <key-encryption-key-url>*

### Obter status da criptografia de uma VM IaaS criptografada

É possível obter o status de criptografia usando o Portal de Gerenciamento do Azure, os [cmdlets do PowerShell](https://msdn.microsoft.com/library/azure/mt622700.aspx) ou os comandos da CLI. As seções abaixo explicarão como usar o portal do Azure e os comandos da CLI e para obter o status de criptografia.

#### Obter status da criptografia de uma VM IaaS criptografada usando o Portal de Gerenciamento do Azure

Você pode obter o status da criptografia da VM IaaS no portal de gerenciamento do Azure. Faça logon no portal do Azure em https://portal.azure.com/ e clique no link de máquinas virtuais no menu à esquerda para ver uma exibição resumida das máquinas virtuais em sua assinatura. Você pode filtrar a exibição de máquinas virtuais selecionando o nome da assinatura no menu suspenso de assinatura. Clique em colunas na parte superior do menu da página Máquinas virtuais. Selecione a coluna de Criptografia de Disco da folha Escolher coluna e clique em Atualizar. Você deve ver a coluna de criptografia de disco mostrando o estado de criptografia "Habilitado" ou "Desabilitado" para cada VM conforme mostrado na figura abaixo.

![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.JPG)

#### Obter status da criptografia de uma VM IaaS criptografada usando o cmdlet de PS de criptografia de disco
Você pode obter o status da criptografia da VM IaaS usando o cmdlet PS de criptografia de disco "Get-AzureRmVMDiskEncryptionStatus". Para obter as configurações de criptografia de sua VM, digite na sessão do Azure PowerShell:

    PS C:\Windows\System32\WindowsPowerShell\v1.0> Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName <yourResourceGroupName> -VMName <yourVMName>

    OsVolumeEncrypted: True
    OsVolumeEncryptionSettings : {
      "DiskEncryptionKey": {
       SecretUrl":"https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
            "SourceVault": {
            "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxxx/providers/Mi                            crosoft.KeyVault/vaults/xxxxxxx"
                                }
                            },
                    "KeyEncryptionKey": null
                             }
    DataVolumesEncrypted: True

O valor das configurações OSVolumeEncrypted e DataVolumesEncrypted são definidos como "True", mostrando que ambos os volumes são criptografados usando a criptografia de disco do Azure. Veja a postagem no blog **Explorar a criptografia de disco do Azure com o Azure PowerShell** [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) para obter detalhes sobre como habilitar a criptografia usando a Criptografia de Disco do Azure usando cmdlets do PS.

#### Obter status da criptografia da VM IaaS do comando CLI de criptografia de disco

Você pode obter o status da criptografia da VM de IaaS usando o comando da CLI de criptografia de disco *azure vm show-disk-encryption-status*. Para obter as configurações de criptografia de sua VM, digite na sessão do Azure CLI:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### Desabilitar a criptografia em VMs IaaS do Windows em execução

Você pode desabilitar a criptografia em uma VM IaaS do Windows em execução por meio do modelo ARM do Azure Disk Encryption ou cmdlets PS e especifica a configuração de descriptografia. A etapa de desabilitar a criptografia tem suporte apenas em VM IaaS do Windows e não tem suporte em VM IaaS do Linux. A etapa de desabilitar a criptografia desabilita a criptografia do sistema operacional ou volume de dados na VM IaaS do Windows em execução. Você não pode desabilitar o volume do sistema operacional e deixar o volume de dados criptografado. Quando a etapa de desabilitar a criptografia é executada, o gerenciamento de serviços do Azure atualiza o modelo de serviço da VM e a VM IaaS do Windows é marcada como descriptografada. Os conteúdos da VM não estão mais criptografados no rest. A desabilitação da criptografia não exclui o cofre de chaves do cliente e o material de chave de criptografia, que são as Chaves de Criptografia BitLocker para Windows e a Senha para Linux.

##### Desabilitar a criptografia em IaaS do Windows existente/em execução no Azure usando o modelo ARM

A criptografia de disco pode ser desabilitada na VM IaaS do Windows usando o modelo ARM publicado [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm). Clique no botão "Implantar no Azure" no modelo de início rápido do Azure, insira a configuração de descriptografia na folha de parâmetros e clique em OK. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os termos legais e o contrato e clique no botão Criar para habilitar a criptografia na VM IaaS nova.

Detalhes de parâmetros do modelo ARM para desabilitar a criptografia na VM IaaS do Windows em execução:

| ​vmName | ​Nome da VM em que a operação de criptografia deve ser realizada |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​volumeType | ​Tipo de volume em que a operação de descriptografia é executada. Os valores válidos são "OS", "Data", "All". **Observação:** não é possível desabilitar a criptografia no volume de inicialização/sistema operacional da VM IaaS do Windows em execução sem desabilitar a criptografia no volume “Data”. |
| sequenceVersion | Versão de sequência da operação de BitLocker. Aumente esse número de versão cada vez que uma operação de descriptografia de disco for executada na mesma VM |

##### Desabilitar a criptografia em IaaS do Windows existente/em execução no Azure usando o cmdlet do PS

Para desabilitar usando o cmdlet do PS, o cmdlet [Disable-AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx) desabilita a criptografia em uma máquina virtual IaaS (infraestrutura como serviço). Esse cmdlet tem suporte apenas em máquinas virtuais do Windows e não as máquinas virtuais do Linux. Esse cmdlet instala uma extensão na máquina virtual para desabilitar a criptografia. Se o parâmetro Name não for especificado, será criada uma extensão com o nome padrão "AzureDiskEncryption for Windows VMs".

**Observação**: este cmdlet reinicia a máquina virtual.

## Apêndice

### Conecte-se as suas assinaturas

Examine a seção de pré-requisitos deste documento antes de continuar. Depois de garantir que todos os pré-requisitos foram atendidos, siga as etapas abaixo para conectar-se à sua assinatura:

1\. Inicie uma sessão do Azure PowerShell e entre em sua conta do Azure com o seguinte comando:

    Login-AzureRmAccount

2\. Se você tiver várias assinaturas e quiser especificar uma a ser usada, digite o seguinte para ver as assinaturas da sua conta:

    Get-AzureRmSubscription

3\. Para especificar a assinatura que você quer usar, digite:

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4\. Para verificar se a assinatura configurada está correta, digite:

    Get-AzureRmSubscription

5\. Para confirmar que os cmdlets de Criptografia de Disco do Azure estão instalados, digite:

    Get-command *diskencryption*

6\. Você verá a saída abaixo confirmando a instalação do PowerShell de Criptografia de Disco do Azure:

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                               	 Version    Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus       1.1.0      AzureRM.Compute                                                    
    Cmdlet       Remove-AzureRmVMDiskEncryptionExtension 1.1.0      AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension    1.1.0      AzureRM.Compute                                                     

### Preparando um VHD do Windows previamente criptografado
As seções a seguir são necessárias para preparar um VHD do Windows previamente criptografado para implantação como um VHD criptografado no Azure IaaS. As etapas são usadas para preparar e inicializar uma nova máquina virtual (VHD) Windows no Hyper-V ou no Azure

#### Atualizar a política de grupo para permitir não TPM na proteção do sistema operacional
Você precisa definir a configuração de Política de Grupo do BitLocker chamada Criptografia de Unidade BitLocker, localizada em Política Local do Computador\\Configuração do Computador\\Modelos Administrativos\\Componentes do Windows. Altere essa configuração para: *Unidades do Sistema Operacional - Exigir autenticação adicional na inicialização - Permitir BitLocker sem TPM compatível*, como mostrado na seguinte figura:

![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.JPG)

#### Instalar componentes de recursos do BitLocker
Para o Windows Server 2012 e versões posteriores, use o comando abaixo:

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Para o Windows Server 2008 R2, use o comando abaixo:

    ServerManagerCmd -install BitLockers

#### Preparar o volume do sistema operacional para o BitLocker usando bdehdcfg

Execute o comando a seguir para compactar a partição do sistema operacional e preparar o computador para o BitLocker.

    bdehdcfg -target c: shrink -quiet

#### Usando o BitLocker para proteger o volume do sistema operacional
Use o comando [manage-bde](https://technet.microsoft.com/library/ff829849.aspx) para habilitar a criptografia no volume de inicialização usando um protetor de chave externo e coloque a chave externa (arquivo .bek) na unidade ou no volume externo. A criptografia será habilitada no volume de inicialização/sistema após a próxima reinicialização.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Observação:** a VM precisa ser preparada com um VHD de dados/recursos separado para obter a chave externa usando o BitLocker.

#### Preparando um VHD do Linux previamente criptografado

##### Ubuntu 14.

1\. Crie um arquivo em /usr/local/sbin/azure\_crypt\_key.sh com o conteúdo no script a seguir. Preste atenção ao KeyFileName, pois ele é o nome do arquivo de senha colocado pelo Azure.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    sleep 2
    OPENED=0
    for SFS in /sys/block/sd*; do
        DEV=`basename $SFS`
        F=$SFS/${DEV}1/dev
        echo "> Trying device: $DEV ..." >&2
        mount /dev/${DEV}1 $MountPoint -t vfat -r >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2\. Altere a configuração de criptografia em */etc/crypttab*. O resultado deve ser assim:

    Sda5_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3\. Se você estiver editando o *azure\_crypt\_key.sh* no Windows e o copiou no Linux, não se esqueça de executar *dos2unix /usr/local/sbin/azure\_crypt\_key.sh*. 4. Execute *update-initramfs -u -k all* para atualizar o initramfs para que o keyscript tenha efeito.

##### openSUSE 13.2.

1\.Edite o /etc/dracut.conf add\_drivers+="vfat nls\_cp437 nls\_iso8859-1"

2\. Comente essas linhas ao final do arquivo “/usr/lib/dracut/modules.d/90crypt/module-setup.sh”:

    #    inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator


3\. Anexe DRACUT\_SYSTEMD = 0 no início do arquivo “/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh” and change all “if [ -z "$DRACUT\_SYSTEMD" ]; then” to “if [ 1 ]; then”

4\. Edite /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e acrescente-o depois de “# Open LUKS device”

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
       echo "> Trying device:$SFS..." >&2
       mount ${SFS}1 $MountPoint -t vfat -r >&2
       if [ -f $MountPoint/$KeyFileName ]; then
          echo "> keyfile got..." >&2
          luksfile=$MountPoint/$KeyFileName
          break
       fi
    done

5\. Execute o “dracut –f -v” para atualizar o initrd

##### CentOS 7
1\.Edite o /etc/dracut.conf add\_drivers+=" vfat nls\_cp437 nls\_iso8859-1"

2\. Comente essas linhas ao final do arquivo “/usr/lib/dracut/modules.d/90crypt/module-setup.sh”:

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3\. Anexe DRACUT\_SYSTEMD = 0 no início do arquivo “/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh” and change all “if [ -z "$DRACUT\_SYSTEMD" ]; then” to “if [ 1 ]; then”

4\. Edite /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e acrescente-o depois de “# Open LUKS device”

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        luksfile=$MountPoint/$KeyFileName
        break
    fi
    done


5\. Execute o “/usr/sbin/dracut -f -v” para atualizar o initrd.

###Carregue o VHD criptografado para uma conta de armazenamento do Azure
Quando a criptografia pr DM-Crypt da criptografia do BitLocker estiver habilitada, o VHD criptografado local precisará ser carregado para a sua conta de armazenamento.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### Carregar o segredo de criptografia de disco da VM previamente criptografada para o Cofre da Chave
O segredo de criptografia de disco obtido anteriormente precisa ser carregado como um segredo no Cofre da Chave.

#### Segredo de criptografia de disco não criptografado com uma KEK
Use [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) para provisionar o segredo no cofre da chave. No caso de uma máquina virtual do Windows, o arquivo bek é codificado como uma cadeia de caracteres base64 e carregado no cofre da chave usando o cmdlet Set-AzureKeyVaultSecret. Para o Linux, a senha é codificada como uma cadeia de caracteres base64 e carregada para o Cofre da Chave. Além disso, verifique se as seguintes marcas são definidas ao criar o segredo no cofre da chave.

    "tags":
    {
       “DiskEncryptionKeyEncryptionAlgorithm”: “RSA-OAEP (optional)”
       "DiskEncryptionKeyFileName": "Bek file name (windows) or Passphrase filename (linux)"
    }

    param(
      [Parameter(Mandatory=$True)]
      [String]$BekFilePath = "C:\vm\nbox\2640EE52-41B3-426C-87B9-484232452CE4.BEK",
      [String]$VaultName = "DiskEncryptionTestAus",
      [String]$SecretName = "BitLockerKey"
      )

    #"EAN//ojeIQk="
    $bekFileName = split-path $BekFilePath -leaf
    echo "Bek file name = $bekFileName"

    $secretBytes = [System.IO.File]::ReadAllBytes($BekFilePath);
    $secret = [Convert]::ToBase64String($secretBytes);
    echo "Secret = $secret"

    $secureSecret = ConvertTo-SecureString $secret -AsPlainText -Force
    $tags = @{"DiskEncryptionKeyFileName" = "$bekFileName"}

    echo "Tags = $tags"
    echo "Vault = $VaultName"
    echo "Secret name = $SecretName"
    echo "Adding secret to Key vault"

    Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secureSecret -tags $tags


#### Segredo de criptografia de disco criptografado com uma KEK

O segredo pode ser opcionalmente criptografado com uma chave de criptografia de chave antes do carregamento para o Cofre da Chave. Use a [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de encapsulamento para primeiro criptografar o segredo usando a Chave de Criptografia de Chave. A saída dessa operação de encapsulamento é uma cadeia de caracteres de URL codificada em base64 que é carregada como um segredo usando o cmdlet [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx).


##Baixar este guia
Você pode baixar este guia na [Galeria do TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).


## Para obter mais informações
[Explorar a Criptografia de Disco do Azure com o Azure PowerShell](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Explorar a Criptografia de Disco do Azure com o Azure PowerShell - Parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

<!---HONumber=AcomDC_0525_2016-->