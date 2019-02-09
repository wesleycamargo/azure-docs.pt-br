---
title: Implantar o Azure Stack - PowerShell | Microsoft Docs
description: Neste artigo, você pode instalar o ASDK da linha de comando usando o PowerShell.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 0fb3e9cd193e570a965d6bbd3e16c86dc39de350
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984266"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>Implantar o ASDK da linha de comando
O ASDK é um ambiente de teste e desenvolvimento que você pode implantar para avaliar e demonstrar os serviços e recursos do Azure Stack. Para obtê-lo em funcionamento, você precisa preparar o ambiente hardware e executar alguns scripts (Isso levará várias horas). Depois disso, você pode entrar portais do administrador e usuário para começar a usar o Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos 
Prepare o computador de host do kit de desenvolvimento. Planeje seu hardware, software e rede. O computador que hospeda o kit de desenvolvimento (o host do kit de desenvolvimento) deve atender aos requisitos de rede, software e hardware. Você também deve escolher entre usar o Azure Active Directory (Azure AD) ou os serviços de Federação do Active Directory (AD FS). Certifique-se de estar em conformidade com esses pré-requisitos antes de iniciar sua implantação para que o processo de instalação seja executado sem problemas. 

Antes de implantar o ASDK, certifique-se de que hardware do computador de desenvolvimento planejado kit host, sistema operacional, conta e configurações de rede atendem aos requisitos mínimos para instalar o ASDK.

**[Examine as considerações de planejamento de implantação de ASDK](asdk-deploy-considerations.md)**

> [!TIP]
> Você pode usar o [ferramenta de verificação de requisitos de implantação do Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) depois de instalar o sistema operacional para confirmar que seu hardware cumpre todos os requisitos.

## <a name="download-and-extract-the-deployment-package"></a>Baixe e extraia o pacote de implantação
Depois de garantir que seu computador de host do kit de desenvolvimento atende aos requisitos básicos para instalar o ASDK, a próxima etapa é baixar e extrair o pacote de implantação ASDK. O pacote de implantação inclui o arquivo Cloudbuilder.vhdx, que é uma unidade de disco rígida virtual que inclui um sistema operacional inicializável e os arquivos de instalação do Azure Stack.

Você pode baixar o pacote de implantação para o host do kit de desenvolvimento ou em outro computador. Os arquivos de implantação extraídos levarem até 60 GB de espaço livre em disco, portanto, usando outro computador pode ajudar a reduzir os requisitos de hardware para o host do kit de desenvolvimento.

**[Baixe e extraia o Azure Stack desenvolvimento ASDK (Kit de)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Preparar o computador de host do kit de desenvolvimento
Antes de instalar o ASDK no computador host, o ambiente deve ser preparado e o sistema configurado para inicialização de VHD. Após essa etapa, o host do kit de desenvolvimento será inicializado a Cloudbuilder.vhdx (um disco rígido virtual que inclui um sistema operacional inicializável e os arquivos de instalação do Azure Stack).

Use o PowerShell para configurar o computador de host ASDK para inicializar a partir de CloudBuilder.vhdx. Esses comandos configuram seu computador de host ASDK para inicializar a partir de baixados e extraído do Azure Stack virtual de disco rígido (CloudBuilder.vhdx). Depois de concluir essas etapas, reinicie o computador de host ASDK.

Para configurar o computador host ASDK para inicializar a partir de CloudBuilder.vhdx:

  1. Inicie um prompt de comando como administrador.
  2. Execute o `bcdedit /copy {current} /d "Azure Stack"`
  3. Copiar (CTRL + C) o valor CLSID retornado, incluindo o necessária {}' s. Esse valor é conhecido como {CLSID} e precisará ser colado (CTRL + V ou o botão direito do mouse) nas etapas restantes.
  4. Execute o `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
  5. Execute o `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Execute o `bcdedit /set {CLSID} detecthal on` 
  7. Execute o `bcdedit /default {CLSID}`
  8. Para verificar as configurações de inicialização, execute `bcdedit`. 
  9. Certifique-se de que o CloudBuilder.vhdx arquivo foi movido para a raiz da unidade C:\ (C:\CloudBuilder.vhdx) e reinicie o computador de host do kit de desenvolvimento. Quando o computador host ASDK é reiniciado, ele deve ser inicializado do disco rígido de máquina virtual CloudBuilder.vhdx para iniciar a implantação de ASDK. 

> [!IMPORTANT]
> Certifique-se de que você tenha acesso KVM ao computador de host do kit de desenvolvimento ou física direta antes de reiniciá-lo. Quando a VM é iniciado pela primeira vez, ele solicita que você conclua a instalação do Windows Server. Fornece as mesmas credenciais de administrador usada para fazer logon no computador de host do kit de desenvolvimento. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Preparar o host do kit de desenvolvimento usando o PowerShell 
Após o kit de desenvolvimento do computador host com êxito será inicializado em imagem CloudBuilder.vhdx, faça logon com as mesmas credenciais de administrador local usada para fazer logon no computador de host do kit de desenvolvimento (e que você forneceu como parte da finalização do Windows Server Instalação quando o computador host inicializado a partir de VHD). 

> [!NOTE]
> Opcionalmente, você também pode configurar [as configurações de telemetria do Azure Stack](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *antes de* instalando o ASDK.

Abra um console do PowerShell com privilégios elevados e execute os comandos nesta seção para implantar o ASDK no host do kit de desenvolvimento.

> [!IMPORTANT] 
> Instalação ASDK dá suporte a exatamente uma placa de interface de rede (NIC) de rede. Se você tiver várias NICs, certifique-se de que somente um está habilitado (e todos os outros estão desabilitados) antes de executar o script de implantação.

Você pode implantar a pilha do Azure com o Azure AD ou o Windows Server AD FS como o provedor de identidade. O Azure Stack, provedores de recursos e outros aplicativos funcionam da mesma maneira com ambas.

> [!TIP]
> Se você não fornecer quaisquer parâmetros de configuração (consulte os parâmetros opcionais InstallAzureStackPOC.ps1 e os exemplos abaixo), você será solicitado para os parâmetros necessários.

### <a name="deploy-azure-stack-using-azure-ad"></a>Implantar o Azure Stack usando o Azure AD 
Para implantar o Azure Stack **usando o Azure AD como o provedor de identidade**, você deve ter conectividade com a internet diretamente ou através de um proxy transparente. 

Execute os seguintes comandos do PowerShell para implantar o kit de desenvolvimento usando o Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Instalação ASDK de alguns minutos você será solicitado para credenciais do Azure AD. Você deve fornecer credenciais de administrador global para seu locatário do AD do Azure. 

Após a implantação, a permissão de administrador global do Azure Active Directory não é necessária. No entanto, algumas operações podem exigir a credencial de administrador global. Por exemplo, um script de instalador do provedor de recursos ou um novo recurso que exigem uma permissão para ser concedida. Você temporariamente pode designar novamente as permissões da conta administrador global ou usar uma conta de administrador global separado que é proprietário do *padrão de assinatura do provedor*.

### <a name="deploy-azure-stack-using-ad-fs"></a>Implantar o Azure Stack usando o AD FS 
Para implantar o kit de desenvolvimento **usando o AD FS como o provedor de identidade**, execute os seguintes comandos do PowerShell (basta adicionar o parâmetro - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Em implantações do AD FS, o serviço de diretório do carimbo de padrão é usado como o provedor de identidade. A conta padrão para entrar com é azurestackadmin@azurestack.local, e a senha será definida como o que é fornecido como parte dos comandos de instalação do PowerShell.

O processo de implantação pode levar algumas horas, período durante o qual o sistema é reinicializado automaticamente uma vez. Quando a implantação bem-sucedida, exibe o console do PowerShell: **CONCLUA: A ação 'Implantação'**. Se a implantação falhar, você pode tentar executar o script novamente usando o parâmetro-execute novamente. Ou, você pode [reimplantar ASDK](asdk-redeploy.md) do zero.

> [!IMPORTANT]
> Se você quiser monitorar o progresso da implantação após a reinicialização do host ASDK, você deve entrar como AzureStack\AzureStackAdmin. Se você entrar como um administrador local depois que o computador host é reiniciado (e ingressado no domínio azurestack. local), você não verá o progresso da implantação. Não execute novamente a implantação, em vez disso, entre como azurestack para validar que ele está em execução.


#### <a name="azure-ad-deployment-script-examples"></a>Exemplos de script de implantação do AD do Azure
Você pode criar scripts toda a implantação do AD do Azure. Aqui estão alguns exemplos comentados que incluem alguns parâmetros opcionais.

Se sua identidade do Azure AD está associada apenas **um** diretório do Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Se sua identidade do AD do Azure estiver associada **maior do que um** diretório do Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Se seu ambiente não tem o DHCP ativado, você deve incluir os seguintes parâmetros adicionais para uma das opções acima (uso de exemplo fornecidos): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parâmetros opcionais ASDK InstallAzureStackPOC.ps1
|Parâmetro|Obrigatório/Opcional|DESCRIÇÃO|
|-----|-----|-----|
|AdminPassword|Obrigatório|Define a conta de administrador local e todas as outras contas de usuário em todas as máquinas virtuais criadas como parte da implantação do kit de desenvolvimento. Essa senha deve corresponder a senha de administrador local atual no host.|
|InfraAzureDirectoryTenantName|Obrigatório|Define o diretório de locatário. Use esse parâmetro para especificar um diretório específico em que a conta do AAD tem permissões para gerenciar vários diretórios. O nome de um locatário de diretório do AAD no formato completo. onmicrosoft.com ou um Azure AD verificada nome de domínio personalizado.|
|TimeServer|Obrigatório|Use esse parâmetro para especificar um servidor de tempo específico. Esse parâmetro deve ser fornecido como um endereço IP do servidor de tempo válido. Não há suporte para nomes de servidor.|
|InfraAzureDirectoryTenantAdminCredential|Opcional|Define o nome de usuário do Active Directory do Azure e a senha. Essas credenciais do Azure devem ser um ID. Org|
|InfraAzureEnvironment|Opcional|Selecione o ambiente do Azure com o qual você deseja registrar essa implantação do Azure Stack. As opções incluem pública do Azure, Azure - China, Azure - governo dos EUA.|
|DNSForwarder|Opcional|Um servidor DNS é criado como parte da implantação do Azure Stack. Para permitir que computadores dentro da solução para resolver nomes fora o carimbo, forneça seu servidor DNS de infra-estrutura existente. O servidor DNS no carimbo de data / encaminha solicitações de resolução de nome desconhecido para este servidor.|
|Executar novamente|Opcional|Use esse sinalizador para executar novamente a implantação. Todas as entrada anterior é usada. Não há suporte para inserir novamente os dados fornecidos anteriormente porque vários valores exclusivos são gerados e usados para implantação.|


## <a name="perform-post-deployment-configurations"></a>Executar configurações pós-implantação
Depois de instalar o ASDK, há algumas verificações de pós-instalação recomendadas e as alterações de configuração que devem ser feitas. Você pode validar a instalação foi instalado com êxito usando o cmdlet test-AzureStack e instalar as ferramentas do Azure Stack PowerShell e GitHub. 

Você também deve redefinir a política de expiração de senha para certificar-se de que a senha para o host do kit de desenvolvimento não expire antes de seu período de avaliação terminar.

> [!NOTE]
> Opcionalmente, você também pode configurar [as configurações de telemetria do Azure Stack](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *depois* instalando o ASDK.

**[ASDK tarefas pós-implantação](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registre-se com o Azure
Você deve registrar o Azure Stack com o Azure para que você possa [baixar itens do marketplace do Azure](asdk-marketplace-item.md) para o Azure Stack.

**[Registre-se a pilha do Azure com o Azure](asdk-register.md)**

## <a name="next-steps"></a>Próximas etapas
Parabéns! Depois de concluir essas etapas, você terá um ambiente do kit de desenvolvimento com ambos [administrator](https://adminportal.local.azurestack.external) e [usuário](https://portal.local.azurestack.external) portais. 

[Pós-instalação do ASDK tarefas de configuração](asdk-post-deploy.md)

