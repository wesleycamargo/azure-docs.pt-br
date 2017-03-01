---
title: " Gerenciar um servidor de configuração no Azure Site Recovery | Doc da Microsofts"
description: "Este artigo descreve como configurar e gerenciar um servidor de configuração."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/14/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 96e6696818a0de2fadd55ff7e0ccee350d2666ad
ms.openlocfilehash: 0e49b7dded14ab6b76c7c73af714af5f5c854bbc
ms.lasthandoff: 02/22/2017

---

# <a name="manage-a-configuration-server"></a>Gerenciar um Servidor de Configuração

Servidor de configuração atua como um coordenador entre os serviços do Site Recovery e sua infraestrutura local. Este artigo descreve como você pode definir, configurar e gerenciar o servidor de configuração.

## <a name="prerequisites"></a>Pré-requisitos
A seguir está os mínimos de hardware, software e configuração de rede necessária para configurar um servidor de configuração.

> [!NOTE]
> [Planejamento de capacidade](site-recovery-capacity-planner.md) é uma etapa importante para garantir que você implantar o servidor de configuração com uma configuração que conjuntos de seus requisitos de carga. Leia mais sobre [requisitos para um servidor de configuração de dimensionamento](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>Baixar o software de servidor de configuração
1. Faça logon no portal do Azure e navegue até seu Cofre de serviços de recuperação.
2. Navegue até **Infraestrutura do Site Recovery** > **Servidores de Configuração** (sob Para Máquinas VMware e Físicas).

  ![Página Adicionar Servidores](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. Clique no botão **+Servidores**.
4. Na página **Adicionar Servidor**, clique no botão Baixar para baixar a chave de Registro. Você precisa dessa chave durante a instalação do servidor de configuração para registrá-lo no serviço Azure Site Recovery.
5. Clique no link **Baixar a instalação do Microsoft Azure Site Recovery Unified** para baixar a versão mais recente do Servidor de Configuração.

  ![Página Download](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  A versão mais recente do servidor de configuração pode ser baixada diretamente da [página de download do Microsoft Download Center](http://aka.ms/unifiedsetup)

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>Instalar e registrar um servidor de configuração da GUI
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>Instalar e registrar um servidor de configuração usando a linha de comando

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Exemplo de uso
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>Argumentos de linha de comando do instalador do servidor de configuração.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>Criar um arquivo de credenciais do MySql
Parâmetro MySQLCredsFilePath utiliza um arquivo como entrada. Crie o arquivo usando o formato a seguir e passá-lo como parâmetro de entrada MySQLCredsFilePath.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>Criar um arquivo de configuração de configurações de proxy
Parâmetro ProxySettingsFilePath utiliza um arquivo como entrada. Crie o arquivo usando o formato a seguir e passá-lo como parâmetro de entrada ProxySettingsFilePath.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>Modificando configurações de proxy de servidor de configuração
1. Faça logon no Servidor de Configuração.
2. Inicie o cspsconfigtool.exe usando o atalho.
3. Clique na guia **Registro do Cofre**.
4. Download de um novo arquivo de registro do cofre no portal e fornecê-la como entrada para a ferramenta.

  ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. Forneça os detalhes do novo servidor Proxy e clique no botão **Registrar**.
6. Abra uma janela de comando do PowerShell do Administrador.
7. Execute o comando a seguir
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Se você tiver anexados a este servidor de configuração de servidores de processo de expansão, você precisa [corrigir as configurações de proxy em todos os servidores de processo de expansão](site-recovery-vmware-to-azure-manage-scaleout-process-server.md) em sua implantação.

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>Registrar um servidor de configuração com um cofre de serviços de recuperação diferente.
1. Faça logon no Servidor de Configuração.
2. em um prompt de comando de administrador, execute o comando
```
reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
net stop dra
```
3. Inicie o cspsconfigtool.exe usando o atalho.
4. Clique na guia **Registro do Cofre**.
5. Download de um novo arquivo de registro no portal e fornecê-la como entrada para a ferramenta.

    ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. Forneça os detalhes do servidor Proxy e clique no **registrar** botão.  
7. Abra uma janela de comando do PowerShell do Administrador.
8. Execute o comando a seguir
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="decommissioning-a-configuration-server"></a>Encerramento de um Servidor de Configuração
Verifique o seguinte antes de iniciar, encerrar o servidor de configuração.
1. Desabilite a proteção para todas as máquinas virtuais por este servidor de configuração.
2. Desassocie todas as políticas de replicação do servidor de configuração.
3. Exclua todos os servidores vCenter/hosts vSphere associados ao Servidor de Configuração.

### <a name="delete-the-configuration-server-from-azure-portal"></a>Excluir o servidor de configuração do portal do Azure
1. No portal do Azure, navegue até **Infraestrutura do Site Recovery** > **Servidores de Configuração** no menu Cofre.
2. Clique no servidor de configuração que você deseja encerrar.
3. Na página de detalhes de configuração do servidor, clique no botão Excluir.

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. Clique em **Sim** para confirmar a exclusão do servidor.

  >[!WARNING]
  Se você tiver máquinas virtuais, políticas de replicação ou hosts de servidores/vSphere vCenter associados a este servidor de configuração, você não pode excluir o servidor. Exclua essas entidades antes de tentar excluir o cofre.

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>Desinstalar o software do servidor de configuração e suas dependências
  > [!TIP]
  Se você planejar reutilizar o servidor de configuração com o Azure Site Recovery novamente, poderá ignorar a etapa 4 diretamente

1. Faça logon no Servidor de Configuração como um Administrador.
2. Abra o painel de controle > programa > desinstalar programas
3. Desinstale os programas na sequência a seguir:
  * Servidor de destino do Microsoft Azure Site Recovery mobilidade mestra do serviço
  * Servidor de processo/servidor de configuração do Microsoft Azure Site Recovery
  * Dependências de servidor de configuração do Microsoft Azure Site Recovery
  * Agente de Serviços de Recuperação do Microsoft Azure
  * Provedor do Microsoft Azure Site Recovery
  * MySQL Server 5.5
4. Execute o seguinte comando de um prompt de comando de administrador.
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>Renovar certificados de configuração de servidor Secure Socket Layer(SSL)
O servidor de configuração tem uma servidor Web embutida, que coordena as atividades dos servidores de destino mestre, servidores de processo e serviço de mobilidade conectados ao servidor de configuração. Servidor Web do servidor de configuração usa um certificado SSL para autenticar seus clientes. Este certificado tem uma expiração de três anos e pode ser renovado a qualquer momento usando o seguinte método:

> [!WARNING]
Expiração do certificado pode ser executada somente na versão 9.4.XXXX. X ou superior. Atualizar todos os componentes do Azure Site Recovery (servidor de configuração, servidor de processo, servidor de destino mestre, serviço de mobilidade) antes de iniciar o fluxo de trabalho renovar certificados.

1. No portal do Azure, navegue até seu Cofre > Infraestrutura do Site Recovery > Servidor de Configuração.
2. Clique no Servidor de Configuração para o qual você precisa renovar o Certificado SSL.
3. Sob a integridade do Servidor de Configuração, você pode ver a data de expiração do certificado SSL.
4. Renove o certificado clicando na ação **Renovar Certificados** conforme mostrado na imagem a seguir:

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>Aviso de expiração de certificado SSL

> [!NOTE]
A validade do certificado SSL para todas as instalações que ocorreram antes de maio de 2016 foi definida como um ano. Você começou a ver notificações de expiração do certificado aparecendo no portal do Azure.

1. Se o certificado SSL do Servidor de Configuração expirar nos próximos dois meses, o serviço começa a notificar os usuários por meio do portal do Azure e por email (você precisa ter assinado as notificações do Azure Site Recovery). Você começa a ver uma faixa de notificação na página de recursos do Cofre.

  ![certificate-notification](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. Clique na faixa para obter detalhes adicionais sobre a expiração do certificado.

  ![certificate-details](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  Se em vez de um botão **Renovar Agora** você vir um botão **Atualizar Agora**. Isso significa que há alguns componentes em seu ambiente que ainda não foram atualizados para a versão 9.4.xxxx.x ou posteriores.

## <a name="sizing-requirements-for-a-configuration-server"></a>Requisitos de dimensionamento para um servidor de configuração

| **CPU** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 soquetes * 4 núcleos @ 2,5 GHz) |16 GB |300 GB |500 GB ou menos |Replicar máquinas menos de 100. |
| 12 vCPUs (2 soquetes * 6 núcleos @ 2,5 GHz) |18 GB |600 GB |500 GB a 1 TB |Replique entre 100 e 150 computadores. |
| 16 vCPUs (2 soquetes * 8 núcleos @ 2,5 GHz) |32 GB |1 TB |1 TB a 2 TB |Replique entre 150 e 200 computadores. |

  >[!TIP]
  Se o diário rotatividade de dados excede 2 TB ou você deseja replicar mais de 200 máquinas virtuais, é recomendável implantar servidores de processo adicionais para equilibrar o tráfego de replicação. Saiba mais sobre como implantar o processo de expansão servidores.


## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

