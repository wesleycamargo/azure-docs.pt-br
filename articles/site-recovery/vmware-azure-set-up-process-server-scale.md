---
title: Configurar um servidor em processo no Azure para o failback de servidor físico e da VM do VMware com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar um servidor em processo no Azure para fazer failback de VMs do Azure para o VMware.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 6d3fe519729bd56dafd11720a3662eb00b916a98
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056602"
---
# <a name="set-up-additional-process-servers-for-scalability"></a>Configurar servidores de processo adicional para escalabilidade

Por padrão, quando você estiver replicando VMs do VMware ou servidores físicos no Azure usando o [Site Recovery](site-recovery-overview.md), um servidor de processo será instalado no computador do servidor de configuração e usado para coordenar a transferência de dados entre o Site Recovery e sua infraestrutura local. Para aumentar a capacidade e escalar horizontalmente a implantação de replicação, você pode adicionar outros servidores de processo autônomos. Este artigo descreve como fazer isso.

## <a name="before-you-start"></a>Antes de começar

### <a name="capacity-planning"></a>planejamento de capacidade

Verifique se você realizou o [planejamento de capacidade](site-recovery-plan-capacity-vmware.md) para replicação de VMware. Isso ajuda a identificar como e quando você deve implantar servidores de processo adicionais.

### <a name="sizing-requirements"></a>Requisitos de dimensionamento 

Verifique os requisitos de dimensionamento resumidos na tabela. Em geral, se for necessário escalar horizontalmente sua implantação para além de 200 computadores de origem ou se você tiver uma taxa de rotatividade diária total acima de 2 TB, serão necessários servidores de processo adicionais para lidar com o volume do tráfego.

| **Servidor de processo adicional** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos** |
| --- | --- | --- | --- |
|4 vCPUs (2 soquetes * 2 núcleos \@ 2,5 GHz), 8 GB de memória |300 GB |250 GB ou menos |Replique 85 computadores ou menos. |
|8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz), 12 GB de memória |600 GB |250 GB a 1 TB |Replique entre 85 e 150 computadores. |
|12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz), 24 GB de memória |1 TB |1 TB a 2 TB |Replique entre 150 e 225 computadores. |

### <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para o servidor de processo adicional são resumidos na tabela a seguir.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]


## <a name="download-installation-file"></a>Baixar o arquivo de instalação

Baixe o arquivo de instalação para o servidor de processo da seguinte maneira:

1. Faça logon no portal do Azure e navegue até seu Cofre dos Serviços de Recuperação.
2. Abra **Infraestrutura do Site Recovery** > **VMWare e Computadores Físicos** > **Servidores de Configuração** (sob Para VMware e Computadores Físicos).
3. Selecione o servidor de configuração para fazer drill down nos detalhes do servidor. Clique em **+ Servidor de processo**.
4. Em **Adicionar servidor de processo** >  **, escolha onde você quer implantar um servidor de processo** e selecione **Implantar uma expansão de servidor de processo local**.

  ![Página Adicionar Servidores](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Clique em **Baixar a Configuração Unificada do Microsoft Azure Site Recovery**. A versão mais recente do arquivo de instalação será baixada.

  > [!WARNING]
  A versão de instalação do servidor de processo deve ser a mesma ou anterior à versão do servidor de configuração executado. Uma maneira simples de garantir a compatibilidade de versão é usar o mesmo instalador usado recentemente para instalar ou atualizar o servidor de configuração.

## <a name="install-from-the-ui"></a>Instalar da interface do usuário

Instale da seguinte maneira. Depois de configurar o servidor, você migrará os computadores de origem para usá-lo.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalar usando a linha de comando

Instale o executando o seguinte comando:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Em que os parâmetros de linha de comando são os seguintes:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Por exemplo: 

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Criar um arquivo de configurações de proxy

Se for necessário configurar um proxy, o parâmetro ProxySettingsFilePath utilizará um arquivo como entrada. É possível criar o arquivo da seguinte maneira, bem como passá-lo como parâmetro de entrada ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [gerenciar as configurações do servidor de processo](vmware-azure-manage-process-server.md)
