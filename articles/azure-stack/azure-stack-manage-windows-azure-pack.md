---
title: "Gerenciar máquinas virtuais de pacote do Windows Azure da pilha do Azure | Microsoft Docs"
description: "Saiba como gerenciar máquinas virtuais do Windows Azure Pack (WAP) do portal do usuário na pilha do Azure."
services: azure-stack
documentationcenter: 
author: walterov
manager: byronr
editor: 
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: walterov
ms.openlocfilehash: b07a18055d149e20cd605a892063eccecf3df8a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Gerenciar máquinas virtuais de pacote do Windows Azure da pilha do Azure

*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

No Kit de desenvolvimento de pilha do Azure, você pode habilitar o acesso o pilha do Azure no portal do usuário para locatários de máquinas virtuais em execução no Windows Azure Pack. Os usuários podem usar o portal de pilha do Azure para gerenciar suas existentes máquinas virtuais e redes virtuais. Esses recursos são disponibilizados no Windows Azure Pack através dos componentes de Service Provider Foundation (SPF) e o Virtual Machine Manager (VMM) subjacentes. Especificamente, os usuários podem:

* Procurar recursos
* Examine os valores de configuração
* Interromper ou iniciar uma máquina virtual
* Conectar a uma máquina virtual por meio de protocolo de área de trabalho remota (RDP)
* Criar e gerenciar pontos de verificação
* Excluir redes virtuais e máquinas virtuais

Essa funcionalidade é fornecida pelo Windows Azure Pack Connector pilha do Azure (visualização). Este artigo mostra como configurar o conector para um ambiente do Azure pilha de nó único.

Para esta versão de visualização do conector, lembre-se das seguintes opções:
* Use o conector do Windows Azure Pack apenas em ambientes de teste (para a pilha do Azure e do Windows Azure Pack) e não em implantações de produção.
* Você deve ter o Windows Azure Pack Update Rollup (UR) 9.1 ou posterior e o SPF do System Center e o VMM UR 9 ou posterior.
* Os componentes do VMM e SPF podem ser System Center 2012 R2 ou System Center 2016.
* Você deve executar etapas de configuração em ambos os pilha do Azure e no Windows Azure Pack.
* As instruções se aplicam a ambientes de não - plataforma de nuvem sistema CPS.
* Para examinar os problemas conhecidos, consulte [solução de problemas do Microsoft Azure pilha](azure-stack-troubleshooting.md).


## <a name="architecture"></a>Arquitetura
O diagrama a seguir mostra os componentes principais do Windows Azure Pack Connector.

![Os componentes do conector do Windows Azure Pack](media/azure-stack-manage-wap/image1.png)

Observe os seguintes detalhes:
* O portal do usuário do Azure pilha acessa as informações de recurso de ambas as nuvens (pilha do Azure e do Windows Azure Pack).
* O usuário deve ter uma conta que seja válida em ambos os ambientes.
* O portal do usuário do Azure pilha deve ter acesso à rede para os componentes em execução no Windows Azure Pack.
* No **WAP** seção do diagrama, você pode ver os novos módulos do conector do Windows Azure Pack (WAP extensão e conector) e existente API do Windows Azure Pack locatário com componentes SPF e do VMM.


## <a name="identity-management"></a>Gerenciamento de identidades
A API de locatário do Windows Azure Pack devem confiar que o serviço de token de segurança (STS) de pilha do Azure.

Quando um usuário executa uma ação por meio do portal do Azure pilha que tem como destino recursos do Windows Azure Pack, o portal usa a API de locatário do Windows Azure Pack. Portanto, o token de autenticação de usuário fornecido deve vir de um STS confiáveis. Consulte o diagrama a seguir:

![Diagrama de autenticação do conector do Windows Azure Pack](media/azure-stack-manage-wap/image2.png)

No ambiente do kit de desenvolvimento, do Windows Azure Pack e pilha de Azure têm provedores de identidade independentes. Os usuários que acessam a ambos os ambientes do portal do Azure pilha devem ter o mesmo nome de nome principal (UPN) do usuário em ambos os provedores de identidade. Por exemplo, a conta  *azurestackadmin@azurestack.local*  também deve existir no STS para Windows Azure Pack. Onde o AD FS não está configurado para dar suporte a relações de confiança de saída, você estabelecerá confiança dos componentes do Windows Azure Pack (API de locatário) para a instância de pilha do Azure do AD FS.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="download-the-windows-azure-pack-connector"></a>Baixe o conector do Windows Azure Pack
Sobre o [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), baixe o arquivo .exe e extraia-o para o computador local. Posteriormente, você deve copiar o conteúdo para um computador que possa acessar seu ambiente do Windows Azure Pack.

### <a name="deployment-option-requirement"></a>Requisito de opção de implantação
Para integrar com o Windows Azure Pack, você pode implantar a pilha do Azure usando a opção AD FS ou o Active Directory do Azure.

### <a name="connectivity-requirements"></a>Requisitos de conectividade
1. O computador no qual você acessar o portal do usuário de pilha do Azure, certifique-se de que você pode acessar o portal de locatário do Windows Azure Pack por meio do navegador da web.
2. A máquina virtual de AzS WASP01 na pilha do Azure deve ser capaz de se conectar ao computador de portal de locatário do Windows Azure Pack. Use Ping.exe para verificar a conectividade de rede. 
3.  Você deve ter certificados válidos para os novos serviços de conector. Esses certificados SSL devem ser emitidos por uma autoridade de certificação confiável (CA). Você não pode usar certificados autoassinados. Os certificados SSL devem ser confiável pelo Azure pilha (especificamente, a VM AzS WASP01) e qualquer outro computador que o locatário pode usar para acessar o portal do usuário de pilha do Azure.
 
    >[!NOTE]
    Porque AzS WASP01 executa o Windows Server com a opção de instalação Server Core, você pode usar uma ferramenta de linha de comando como Certutil.ext para importar o certificado. Por exemplo, você pode copiar o arquivo. cer c:\Temp em AzS WASP01 e, em seguida, execute o comando **certutil - addstore "CA" "c:\temp\certname.cer"**.

4.  Para estabelecer a conectividade RDP para máquinas virtuais do Windows Azure Pack locatário por meio do portal de pilha do Azure, o ambiente do Windows Azure Pack deve permitir o tráfego de área de trabalho remota para VMs do locatário.
5.  Para a conectividade entre máquinas de virtuais de locatário de pilha do Azure e máquinas virtuais do Windows Azure Pack locatário, seus endereços IP externos devem ser roteáveis entre os dois ambientes. Essa conectividade também pode incluir a associação de um servidor DNS para resolver nomes de máquina virtual entre os ambientes.

### <a name="iis-requirements"></a>Requisitos do IIS
O computador que hospeda o portal de locatário do Windows Azure Pack (que pode ser um host físico, uma máquina virtual ou várias máquinas virtuais) deve ter a extensão de URL de reconfiguração de IIS instalada. Se ele não estiver instalado, você pode baixá-lo do [aqui](https://www.iis.net/downloads/microsoft/url-rewrite). Se várias máquinas virtuais hospedar o portal de locatário, instale a extensão em cada máquina virtual.

## <a name="configure-azure-stack"></a>Configurar a pilha do Azure
Antes de configurar o conector do Windows Azure Pack, você deve habilitar o modo de várias nuvens no portal do usuário de pilha do Azure. Esse modo permite que os usuários selecionem quais de nuvem para acessar os recursos.

Para habilitar o modo de várias nuvens, você deve executar o script Add-AzurePackConnector.ps1 após a implantação da pilha do Azure. A tabela a seguir descreve os parâmetros do script.


|  Parâmetro | Descrição | Exemplo |   
| -------- | ------------- | ------- |  
| AzurePackClouds | URIs do Windows Azure Pack conectores. Esses URIs deve corresponder aos portais de locatário do Windows Azure Pack. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (Por padrão, o valor da porta é 40005.) |  
| AzureStackCloudName | Rótulo para representar a nuvem do Azure pilha local.| "AzureStack" |
| DisableMultiCloud | Uma opção para desabilitar o modo de várias nuvens.| N/D |
| | |

Você pode executar o script Add-AzurePackConnector.ps1 imediatamente após a implantação, ou posterior. Para executar o script imediatamente após a implantação, use a mesma sessão do Windows PowerShell em que a implantação de pilha do Azure concluída. Caso contrário, você pode abrir uma nova sessão do Windows PowerShell como administrador (conectado como a conta de azurestackadmin).

1. Execute o script Add-AzurePackConnector.ps1 usando os comandos a seguir (com valores específicos de seu ambiente). Observe que o script Add-AzurePackConnector permite que você adicionar mais de um ponto de extremidade do conector do Windows Azure Pack.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> Há um problema na compilação atual onde depois que o script Add-AzurePackConnector termina, ele permanecerá em um loop de sondagem por um longo período de tempo (alguns minutos) até que ele termina. Depois que você vir a mensagem **VERBOSE: etapa 'Configurar conector de pacote do Azure' status: 'Sucesso'**, você pode interromper o script ou aguardar até que ela pare por si só. Ele não faça diferença porque a configuração já foi bem-sucedida.

2. Anote os arquivos de saída que são produzidos por esse script, uma para cada ambiente do Windows Azure Pack que você especificou. Os arquivos estão localizados em: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput. Esses arquivos contêm as informações necessárias para configurar os ambientes de destino do Windows Azure Pack. Você pode passar esse arquivo como um parâmetro de um script estas instruções. Esse arquivo contém as seguintes informações:

    * **AzurePackConnectorEndpoint**: contém o ponto de extremidade para o serviço do conector do Windows Azure Pack.
    * **AuthenticationIdentityProviderPartner**: contém o par de valor a seguir:
        * Token de autenticação, assinatura de certificado que a API de locatário do Windows Azure Pack precisa confia para aceitar as chamadas da extensão do portal de pilha do Azure.

        * "Território" associado com o certificado de autenticação. Por exemplo: https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/.

3.  Navegue até a pasta que contém os arquivos de saída (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) e copie os arquivos para o computador local. Os arquivos serão semelhante a este: AzurePack-06-27-15-50.txt.

4.  Teste a configuração.

    a. Abra seu navegador e entrar no portal do usuário do Azure pilha (https://portal.local.azurestack.external/).
    
    b. Depois que você entrar como um locatário e carrega o portal, você verá erros sobre não poder ser buscar extensões ou assinaturas da nuvem do Azure Pack. Clique em **Okey** para fechar essas mensagens. (Essas mensagens de erro desaparecerá depois de configurar o Windows Azure Pack.)

    c. Observe o **nuvem** lista suspensa no canto superior esquerdo do portal.

    ![O seletor de nuvem na interface do usuário de pilha do Azure](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Configurar o Windows Azure Pack
Para o conector visualização somente nesta versão, o Windows Azure Pack requer configuração manual.

>[!IMPORTANT]
Para esta versão de visualização, use o conector do Windows Azure Pack apenas em ambientes de teste e não em implantações de produção.

1.  Instalar arquivos MSI do conector a máquina virtual portal do locatário de pacote do Windows Azure e instalar certificados. (Se você tiver várias máquinas virtuais do portal de locatário, você deve concluir esta etapa em cada máquina virtual.)

    a. No Explorador de arquivos, copie o **WAPConnector** pasta (o que você baixou anteriormente) para uma pasta chamada **c:\temp** na máquina virtual de portal locatário.

    b. Abra uma conexão de Console ou RDP para a máquina virtual portal de locatário.

    c. Altere os diretórios para **c:\temp\wapconnector\setup\scripts**e execute o **Connector.ps1 instalar** script para instalar três arquivos MSI. Nenhum parâmetro é necessário.

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. Altere os diretórios para **c:\inetpub** e verificar se os três novos sites estão instalados:

       * Conector de MgmtSvc

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. Da mesma **c:\temp\wapconnector\setup\scripts** pasta, execute o **Certificates.ps1 configurar** script para instalar certificados. Por padrão, ele usará o mesmo certificado que está disponível para o site do Portal de locatário no Windows Azure Pack. Verifique se que este é um certificado válido (confiável por máquina virtual do Azure pilha AzS-WASP01 e qualquer computador cliente que acessa o portal de pilha do Azure). Caso contrário, a comunicação não funcionará. (Como alternativa, você pode passar explicitamente uma impressão digital do certificado como um parâmetro usando o parâmetro-impressão digital.)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. Para concluir a configuração desses três serviços, execute o **WapConnector.ps1 configurar** script para atualizar os parâmetros do arquivo Web. config.

    |  Parâmetro | Descrição | Exemplo |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Portal de locatário do Windows Azure Pack FQDN. | tenant.contoso.com | 
    | TenantAPIFQDN | O FQDN de API do Windows Azure Pack locatário. | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | O portal do usuário do Azure pilha FQDN. | Portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. Se você tiver várias máquinas virtuais do portal de locatário, repita a etapa 1 para cada uma dessas máquinas virtuais.

2. Instale o novo MSI de API de locatário em cada máquina virtual de API de locatário do Windows Azure Pack.

    a. Se um balanceador de carga está em uso, convém marcar a máquina virtual como off-line.

    b. No Explorador de arquivos, copie o **WAPConnector** para uma pasta denominada **c:\temp** em cada computador da API de locatário.

    c. Copie o arquivo AzurePackConnectorOutput.txt que você salvou anteriormente, para **c:\temp\WAPConnector**.

    d. Abra uma conexão de Console ou RDP para a VM de API de locatário copiar os arquivos.
    
    e. Altere os diretórios para **c:\temp\wapconnector\setup\scripts**e execute **TenantAPI.ps1 atualização**. Essa nova versão da API do locatário WAP contém uma alteração para habilitar uma relação de confiança, não apenas com o STS atual, mas também com a instância do AD FS na pilha do Azure.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  Repita a etapa 2 em outra máquina virtual executando a API de locatário.
3. De **apenas um** das VMs de API de locatário, execute o script TrustAzureStack.ps1 configurar para adicionar uma relação de confiança entre a API de locatário e a instância do AD FS na pilha do Azure. Você deve usar uma conta com acesso de sysadmin no banco de dados de Store. Esse script tem os seguintes parâmetros:

    | Parâmetro | Descrição | Exemplo |
    | --------- | ------------| ------- |
   | SQL Server | O nome do SQL Server que contém o banco de dados de Store. Esse parâmetro é necessário. | SQL Server | 
   | arquivo de dados | O arquivo de saída que foi gerado durante a configuração do modo pilha do Azure multi-nuvem anteriormente. Esse parâmetro é necessário. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | Indica que o script solicitará que você interativamente para uma credencial de autenticação do SQL usar ao se conectar à instância do SQL Server. A credencial fornecida deve ter permissões suficientes para desinstalar os bancos de dados, esquemas e excluir os logons de usuário. Se nenhum for fornecido, o script supõe que contexto do usuário atual tem acesso. | Nenhum valor é necessário. |
   |  |  |

    Se você não souber o SQL Server para usar, você poderá descobri-lo. Conecte-se ao computador de API de locatário, use o comando de MgmtSvc Desproteger para desproteger o arquivo Web. config de API de locatário e procure o nome do servidor na cadeia de conexão. Lembre-se de executar MgmtSvc de proteger novamente para proteger o arquivo Web. config de API de locatário.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>Exemplo
O exemplo a seguir mostra uma implantação completa do conector do Windows Azure Pack em uma configuração de nó único pilha do Azure e duas instalações do Windows Azure Pack Express. (Cada instalação expressa é em um único computador, com os nomes de exemplo *wapcomputer1* e*wapcomputer2*.)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
Baixe o arquivo .exe o [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), extraia-o e copie a pasta WAPConnector para um **c:\temp** pasta no computador do Windows Azure Pack. Copie os arquivos que foram gerados como saída no script anterior (localizado em \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) para o **c:\temp\WAPConnector** pasta. (Os arquivos serão parece semelhante a este: AzurePack-06-27-15-50.txt.) Em seguida, execute o script a seguir, uma vez por instância do Windows Azure Pack:

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>Dicas de solução de problemas
1.  Verifique se há conectividade de rede entre a pilha do Azure e o Windows Azure Pack. Essa conectividade deve estar entre qualquer computador do locatário que acessa o portal do Azure pilha e a máquina virtual portal do locatário de pacote do Windows Azure, onde os novos serviços de conector estão em execução.
2.  Certifique-se de que todos os especificado que FQDNs estão corretas.
3.  Certifique-se de que os certificados SSL usados nos serviços do conector novo sejam confiáveis pela pilha do Azure (especificamente, a VM AzS WASP01) e qualquer outro computador o locatário pode usar para acessar o portal do usuário de pilha do Azure.
4. Para problemas conhecidos, consulte [solução de problemas do Microsoft Azure pilha](azure-stack-troubleshooting.md).


## <a name="next-steps"></a>Próximas etapas
[Usando os portais de administrador e usuário na pilha do Azure](azure-stack-manage-portals.md)
