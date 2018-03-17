---
title: Implantar a pilha do Azure - PowerShell | Microsoft Docs
description: "Neste tutorial, você pode instalar o ASDK da linha de comando."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 48ccccaba6b7f5780f1d42dfbe5d9747c5e30292
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-deploy-the-asdk-from-the-command-line"></a>Tutorial: implantar o ASDK da linha de comando
Neste tutorial, você deve implantar o Azure pilha Development Kit (ASDK) na linha de comando em um ambiente de não produção. 

O ASDK é um ambiente de teste e desenvolvimento que você pode implantar para avaliar e demonstrar os serviços e recursos da pilha do Azure. Para colocá-lo em funcionamento, você precisa preparar o ambiente hardware e executar alguns scripts (Isso levará várias horas). Depois disso, você pode entrar portais de administrador e usuário para começar a usar a pilha do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Baixe e extraia o pacote de implantação
> * Preparar o computador de host do kit de desenvolvimento 
> * Executar as configurações de pós-implantação
> * Registrar com o Azure

## <a name="prerequisites"></a>Pré-requisitos 
Prepare o computador de host do kit de desenvolvimento. Planeje o hardware, software e rede. O computador que hospeda o kit de desenvolvimento (o host do kit de desenvolvimento) deve atender aos requisitos de rede, software e hardware. Você também deve escolher entre usar o Azure Active Directory (AD do Azure) ou os serviços de Federação do Active Directory (AD FS). Certifique-se de estar em conformidade com esses pré-requisitos antes de iniciar a implantação para que o processo de instalação seja executado sem problemas. 

Antes de implantar o ASDK, certifique-se de que hardware do computador planejado development kit host, sistema operacional, conta e configurações de rede atenderem aos requisitos mínimos para instalar o ASDK.

**[Examine a considerações de planejamento de implantação de ASDK](asdk-deploy-considerations.md)**

> [!TIP]
> Você pode usar o [ferramenta de verificação de requisitos de implantação do Azure pilha](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) depois de instalar o sistema operacional para confirmar se o hardware atende todos os requisitos.

## <a name="download-and-extract-the-deployment-package"></a>Baixe e extraia o pacote de implantação
Depois de garantir que o computador de host do kit de desenvolvimento atenda aos requisitos básicos para instalar o ASDK, a próxima etapa é fazer o download e extraia o pacote de implantação ASDK. O pacote de implantação inclui o arquivo Cloudbuilder.vhdx, que é um disco rígido virtual que inclui um sistema operacional inicializável e os arquivos de instalação de pilha do Azure.

Você pode baixar o pacote de implantação para o host do kit de desenvolvimento ou para outro computador. Os arquivos extraídos implantação levarem até 60 GB de espaço livre em disco, por isso usando outro computador pode ajudar a reduzir os requisitos de hardware para o host do kit de desenvolvimento.

**[Baixe e extraia o Kit de desenvolvimento de pilha do Azure (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Preparar o computador de host do kit de desenvolvimento
Antes de instalar o ASDK no computador host, o ambiente deve ser preparado e o sistema é configurado para inicialização do VHD. Após essa etapa, o host do kit de desenvolvimento será inicializado a Cloudbuilder.vhdx (um disco rígido virtual que inclui um sistema operacional inicializável e os arquivos de instalação de pilha do Azure).

Use o PowerShell para configurar o computador de host ASDK para inicializar a partir de CloudBuilder.vhdx. Esses comandos configurar seu computador de host ASDK para inicializar o baixados e extraído pilha Azure virtual disco rígido (CloudBuilder.vhdx). Depois de concluir essas etapas, reinicie o computador de host ASDK.

Para configurar o computador host ASDK CloudBuilder.vhdx de inicialização:

  1. Inicie um prompt de comando como administrador.
  2. Execute o `bcdedit /copy {current} /d "Azure Stack"`
  3. Copiar (CTRL + C) o valor CLSID retornado, incluindo o {} necessária ' s. Esse valor é conhecido como {CLSID} e precisará ser colado (CTRL + V ou o botão direito do mouse) nas etapas restantes.
  4. Execute o `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
  5. Execute o `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Execute o `bcdedit /set {CLSID} detecthal on` 
  7. Execute o `bcdedit /default {CLSID}`
  8. Para verificar as configurações de inicialização, execute `bcdedit`. 
  9. Certifique-se de que o CloudBuilder.vhdx arquivo foi movido para a raiz da unidade C:\ (C:\CloudBuilder.vhdx) e reinicie o computador de host do kit de desenvolvimento. Quando o computador host ASDK for reiniciado, ele deve ser inicializado do disco rígido de máquina virtual CloudBuilder.vhdx para iniciar a implantação de ASDK. 

> [!IMPORTANT]
> Certifique-se de que você tem acesso KVM ao computador de host do kit de desenvolvimento ou direto físico antes de reiniciá-lo. Quando a máquina virtual é iniciado, ele solicita que você concluir a instalação do Windows Server. Fornece as mesmas credenciais de administrador usada para fazer logon no computador de host do kit de desenvolvimento. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Preparar o host do kit de desenvolvimento usando o PowerShell 
Após o kit de desenvolvimento computador host for inicializado com sucesso na imagem CloudBuilder.vhdx, faça logon com as mesmas credenciais de administrador local usada para fazer logon no computador de host do kit de desenvolvimento (e fornecido como parte da finalização do Windows Server Instalação quando o computador host iniciada no VHD). 

> [!NOTE]
> Opcionalmente, você também pode configurar [as configurações de telemetria do Azure pilha](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *antes de* instalando o ASDK.

Abra um console do PowerShell com privilégios elevados e execute os comandos nesta seção para implantar o ASDK no host do kit de desenvolvimento.

> [!IMPORTANT] 
> Instalação ASDK dá suporte a exatamente uma placa de interface de rede (NIC) de rede. Se você tiver várias placas de rede, certifique-se de que somente um está habilitado (e todos os outros são desativados) antes de executar o script de implantação.

Você pode implantar a pilha do Azure com o Azure AD ou o Windows Server AD FS como o provedor de identidade. A pilha do Azure, provedores de recursos e outros aplicativos funcionam da mesma maneira com ambos.

> [!TIP]
> Se você não fornecer quaisquer parâmetros de configuração (consulte parâmetros opcionais InstallAzureStackPOC.ps1 e os exemplos abaixo), você será solicitado para os parâmetros necessários.

### <a name="deploy-azure-stack-using-azure-ad"></a>Implantar a pilha do Azure usando o Azure AD 
Para implantar o Azure pilha **usando o AD do Azure como o provedor de identidade**, você deve ter conectividade com a internet diretamente ou através de um proxy transparente. 

Execute os seguintes comandos do PowerShell para implantar o kit de desenvolvimento usando o Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Alguns minutos para instalação ASDK que você será solicitado para credenciais do Azure AD. Você deve fornecer credenciais de administrador global para seu locatário do AD do Azure. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Implantar a pilha do Azure usando o AD FS 
Para implantar o kit de desenvolvimento **usando o AD FS como o provedor de identidade**, execute os seguintes comandos do PowerShell (basta adicionar o parâmetro - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Em implantações do AD FS, o serviço de diretório do carimbo de padrão é usado como o provedor de identidade. É a conta padrão para entrar com azurestackadmin@azurestack.local, e a senha será definida como o que é fornecido como parte dos comandos de instalação do PowerShell.

O processo de implantação pode levar algumas horas, durante o qual o sistema é reinicializado automaticamente uma vez. Quando a implantação for bem-sucedido, exibe o console do PowerShell: **concluir: ação 'Implantação'**. Se a implantação falhar, você poderá tentar executar o script novamente usando o parâmetro-execute. Ou, você pode [reimplantar ASDK](asdk-redeploy.md) do zero.

> [!IMPORTANT]
> Se você quiser monitorar o progresso da implantação após a reinicialização do host ASDK, você deve entrar como AzureStack\AzureStackAdmin. Se você entrar como um administrador local depois que o computador host é reiniciado (e ingressar no domínio azurestack.local), você não verá o progresso da implantação. Não execute novamente a implantação, em vez disso, entre como azurestack validar que está sendo executado.


#### <a name="azure-ad-deployment-script-examples"></a>Exemplos de script de implantação de AD do Azure
Você pode gerar o script de toda a implantação do AD do Azure. Aqui estão alguns exemplos de comentários que incluem alguns parâmetros opcionais.

Se sua identidade do AD do Azure só é associada **um** diretório AD do Azure:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Se sua identidade do AD do Azure está associada com **maior do que um** diretório AD do Azure:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Se seu ambiente tiver DHCP habilitado, você deve incluir os seguintes parâmetros adicionais para uma das opções acima (uso de exemplo fornecidos): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parâmetros opcionais ASDK InstallAzureStackPOC.ps1
|Parâmetro|Obrigatório/Opcional|DESCRIÇÃO|
|-----|-----|-----|
|AdminPassword|Obrigatório|Define a conta de administrador local e todas as outras contas de usuário em todas as máquinas virtuais criadas como parte da implantação do kit de desenvolvimento. Esta senha deve corresponder a senha de administrador local atual no host.|
|InfraAzureDirectoryTenantName|Obrigatório|Define o diretório do locatário. Use esse parâmetro para especificar um diretório específico em que a conta do AAD tem permissões para gerenciar vários diretórios. Nome de um locatário de diretório do AAD no formato completo. c o m ou do AD do Azure verificar o nome de domínio personalizado.|
|TimeServer|Obrigatório|Use esse parâmetro para especificar um servidor de tempo específico. Esse parâmetro deve ser fornecido como um endereço IP do servidor de tempo válido. Não há suporte para nomes de servidor.|
|InfraAzureDirectoryTenantAdminCredential|Opcional|Define o nome de usuário do Active Directory do Azure e a senha. Essas credenciais do Azure devem ser um ID. Org|
|InfraAzureEnvironment|Opcional|Selecione o ambiente do Azure com o qual você deseja registrar essa implantação de pilha do Azure. As opções incluem pública do Azure, Azure - China, Azure - governo dos EUA.|
|DNSForwarder|Opcional|Um servidor DNS é criado como parte da implantação da pilha do Azure. Para permitir que os computadores dentro da solução para resolver nomes fora o carimbo, forneça o servidor DNS de infraestrutura existente. O servidor DNS no carimbo encaminha solicitações de resolução de nome desconhecido para o servidor.|
|NatIPv4Address|Necessário para suporte de NAT DHCP|Define um endereço IP estático para MAS BGPNAT01. Use esse parâmetro apenas se o DHCP não puder atribuir um endereço IP válido para acessar a Internet.|
|NatIPv4Subnet|Necessário para suporte de NAT DHCP|Prefixo de sub-rede IP usado para DHCP sobre suporte NAT. Use esse parâmetro apenas se o DHCP não puder atribuir um endereço IP válido para acessar a Internet.|
|PublicVlanId|Opcional|Define a ID de VLAN. Somente use esse parâmetro se o host e BGPNAT01 MAS deverá configurar o ID de VLAN para acessar a rede física (e Internet). For example, .\InstallAzureStackPOC.ps1 -Verbose -PublicVLan 305|
|Executar novamente|Opcional|Use este sinalizador para executar novamente a implantação. Todas as entradas anteriores é usada. Inserir novamente dados previamente fornecidos não são suportados porque vários valores exclusivos são gerados e usados para implantação.|


## <a name="perform-post-deployment-configurations"></a>Executar as configurações de pós-implantação
Depois de instalar o ASDK, há algumas verificações de pós-instalação recomendadas e alterações de configuração que devem ser feitas. Você pode validar a instalação tiver sido instalado com êxito usando o cmdlet test-AzureStack e instalar as ferramentas do PowerShell do Azure pilha e GitHub. 

Você também deve redefinir a política de expiração de senha para certificar-se de que a senha para o host do kit de desenvolvimento não expira antes de seu período de avaliação terminar.

> [!NOTE]
> Opcionalmente, você também pode configurar [as configurações de telemetria do Azure pilha](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *depois* instalando o ASDK.

**[Tarefas de implantação do ASDK](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrar com o Azure
Você deve registrar a pilha do Azure com o Azure para que você possa [baixar itens do marketplace do Azure](asdk-marketplace-item.md) a pilha do Azure.

**[Registrar a pilha do Azure com o Azure](asdk-register.md)**

## <a name="next-steps"></a>Próximas etapas
Parabéns! Depois de concluir essas etapas, você terá um ambiente de kit de desenvolvimento com [administrador](https://adminportal.local.azurestack.external) e [usuário](https://portal.local.azurestack.external) portais. 

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Baixe e extraia o pacote de implantação
> * Preparar o computador de host do kit de desenvolvimento 
> * Executar as configurações de pós-implantação
> * Registrar com o Azure

Avança para o próximo tutorial para aprender a adicionar um item do marketplace de pilha do Azure.

> [!div class="nextstepaction"]
> [Adicionar um item do marketplace de pilha do Azure](asdk-marketplace-item.md)

