---
title: Instale o serviço de mobilidade manualmente para recuperação de desastres de VMs e servidores físicos VMware com o Azure Site Recovery | Microsoft Docs
description: Saiba como instalar o agente do Serviço de Mobilidade para recuperação de desastre de VMs do VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 06430bf476c2e9f3af2102272fb54d201a3f1066
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790802"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>Instale o serviço Mobility manualmente em VMs e servidores físicos VMware

Quando você configura a recuperação de desastre para VMs VMware e servidores físicos usando [Azure Site Recovery](site-recovery-overview.md), instale o [Serviço Mobilidade de Recuperação do Site](vmware-physical-mobility-service-overview.md) em cada VM VMware local e servidor físico.  O serviço Mobility captura gravações de dados na máquina e as encaminha para o servidor do processo de Recuperação do Site.

Este artigo descreve como instalar o serviço Mobility manualmente em cada máquina que você deseja proteger.

## <a name="create-a-passphrase"></a>Criar uma frase secreta

Antes de instalar, crie uma frase secreta que será usada durante a instalação.

1. Entrar no servidor de configuração.
2. Abra um prompt de comando como administrador.
3. Altere o diretório para a pasta bin e, em seguida, crie um arquivo de frase secreta.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Armazene o arquivo de senha em uma localização segura. 


## <a name="install-the-service-from-the-ui"></a>Instalar o serviço da interface do usuário

>[!IMPORTANT]
> Se você estiver replicando a VM do Azure IaaS de uma região do Azure para outra, não use esse método. Use o método de instalação baseado em linha de comando em vez disso.

1. Localize a versão do instalador que você precisa para o sistema operacional da máquina. Os instaladores estão localizados na pasta% ProgramData% \ ASR \ home \ svsystems \ pushinstallsvc \ repository. [Verifique](vmware-physical-mobility-service-overview.md#installer-files) qual instalador você precisa.
2. Copie o arquivo de instalação para a máquina e execute-o.
3. Na **Opção de Instalação**, selecione **Instalar o serviço de mobilidade**.
4. Selecione o local de instalação > **instalar**.

    ![Página de opção de instalação do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. Monitore a instalação em **Progresso da Instalação**. Após a conclusão da instalação, selecione **Prossiga para a Configuração** para registrar o serviço no servidor de configuração.

    ![Página de registro do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  em **Detalhes do servidor de configuração**, especifique o endereço IP e a senha que você configurou.  

    ![Página de registro do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. Selecione **Registrar** para concluir o registro.

    ![Página final de registro do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>Instale o serviço no prompt de comando

### <a name="on-a-windows-machine"></a>Em uma máquina Windows

1. Copie o instalador para uma pasta local (digamos, C:\Temp) no servidor que você deseja proteger. 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Instale da seguinte maneira:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. Registre o agente no servidor de configuração.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Configurações de instalação
**Configuração** | **Detalhes**
--- | ---
Uso | UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
Logs de instalação | Sob %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Parâmetro de instalação obrigatório. Especifica se o serviço Mobility (MS) ou o destino mestre (MT) deve ser instalado.
/InstallLocation| Parâmetro opcional. Especifica o local de instalação do serviço Mobility (qualquer pasta).
/Platform | Obrigatório. Especifica a plataforma onde o Serviço de Mobilidade está instalado. **VMware** para VMs do Mware/servidores físicos; **Azure** para VMs do Azure. 
/Silent| Opcional. Especifica se deve executar o instalador no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro
**Configuração** | **Detalhes**
--- | ---
Uso | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Logs de configuração do agente | Sob %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
/PassphraseFilePath |  Obrigatório. Local da frase secreta. Use qualquer caminho UNC ou arquivo local válido.


### <a name="on-a-linux-machine"></a>Em um computador Linux

1. Copie o instalador para uma pasta local (digamos, /tmp) no servidor que você deseja proteger. Execute os seguintes comandos em um terminal:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Instale da seguinte maneira:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Após concluir a instalação, o Serviço de Mobilidade precisa ser registrado no servidor de configuração. Execute o seguinte comando para registrar o Serviço de Mobilidade com o servidor de configuração:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```


#### <a name="installation-settings"></a>Configurações de instalação
**Configuração** | **Detalhes**
--- | ---
Uso | ./install -d <Install Location> -r <MS|MT> -v VmWare -q
-r | Parâmetro de instalação obrigatório. Especifica se o serviço Mobility (MS) ou o destino mestre (MT) deve ser instalado.
-d | Parâmetro opcional. Especifica o local de instalação do serviço Mobility: / usr / local / ASR.
-v | Obrigatório. Especifica a plataforma onde o Serviço de Mobilidade está instalado. **VMware** para VMs do Mware/servidores físicos; **Azure** para VMs do Azure. 
-q | Opcional. Especifica se deve executar o instalador no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro
**Configuração** | **Detalhes**
--- | ---
Uso | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
-P |  Obrigatório. Caminho de arquivo completo do arquivo no qual a frase secreta é salvo. Use qualquer pasta válida

## <a name="next-steps"></a>Próximas etapas
- [Configure a recuperação de desastre para VMs do VMware](vmware-azure-tutorial.md)
- [Configure a recuperação de desastre para servidores físicos](physical-azure-disaster-recovery.md)
