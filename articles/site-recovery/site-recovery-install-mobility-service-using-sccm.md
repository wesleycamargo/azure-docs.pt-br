---
title: "Automatizar a instalação do Serviço de Mobilidade para o Azure Site Recovery usando ferramentas de implantação de software | Microsoft Docs."
description: "Este artigo ajudará você a automatizar a Instalação do Serviço de Mobilidade usando ferramentas de implantação de software, como o System Center Configuration Manager"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 310f2a2fe793601d22952bf516a812bf4867bbec
ms.lasthandoff: 03/03/2017

---
# <a name="automate-mobility-service-installation-using-software-deployment-tools"></a>Automatizar a instalação do Serviço de Mobilidade usando ferramentas de implantação de software

Este artigo fornece um exemplo de como você pode usar o SCCM (System Center Configuration Manager) para implantar o Serviço de Mobilidade do Azure Site Recovery no seu datacenter. Usar uma ferramenta de implantação de software como o SCCM oferece as seguintes vantagens
* Planejamento de implantação ‑ Novas instalações e atualizações, realizadas durante a janela de manutenção planejada para atualizações de software.
* Implantar em escala para centenas de servidores simultaneamente


> [!NOTE]
> Este artigo usa o System Center Configuration Manager 2012 R2 para demonstrar a atividade de implantação. Você também poderia automatizar a Instalação do Serviço de Mobilidade usando a [Automação do Azure e a Configuração de Estado Desejado](site-recovery-automate-mobility-service-install.md).

## <a name="prerequisites"></a>Pré-requisitos
1. Uma ferramenta de implantação de software como o SCCM (System Center Configuration Manager) já está implantada em seu ambiente.
  * Crie duas [Coleções de Dispositivos](https://technet.microsoft.com/library/gg682169.aspx), uma para todos os **Servidores Windows** e outra para todos os **Servidores Linux** que você deseja proteger com o Azure Site Recovery.
3. Um Servidor de Configuração já está registrado com o Azure Site Recovery.
4. Um compartilhamento de arquivos por rede segura (compartilhamento SMB) que pode ser acessado pelo Servidor do SCCM.

## <a name="deploy-mobility-service-on-computers-running-microsoft-windows-operating-systems"></a>Implantar o Serviço de Mobilidade em computadores que executam Sistemas Operacionais Microsoft Windows
> [!NOTE]
> Este artigo pressupõe o seguinte
> 1. O endereço IP do servidor de configuração é 192.168.3.121
> 2. O compartilhamento de arquivos por rede segura é \\\ContosoSecureFS\MobilityServiceInstallers

### <a name="step-1-prepare-for-deployment"></a>Etapa 1: Preparar para a implantação
1. Crie uma pasta no compartilhamento de rede e nomeie-a como **MobSvcWindows**
2. Faça logon no Servidor de Configuração e abra um prompt de comando Administrativo
3. Execute os seguintes comandos para gerar um arquivo de frase secreta.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. Copie o arquivo MobSvc.passphrase para a pasta MobSvcWindows no compartilhamento de rede.
5. Em seguida, navegue até o repositório do instalador no Servidor de Configuração executando o comando.

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. Copie a **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** para a pasta **MobSvcWindows** no seu compartilhamento de rede.
7. Copie o código listado abaixo e salve-o como **install.bat** para a pasta **MobSvcWindows**
> [!NOTE]
> Lembre-se de substituir os espaços reservados de [CSIP] no script abaixo pelos valores reais do endereço IP do seu Servidor de Configuração.

```
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
REM ==================================================
REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================
REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================
REM ==== Extract the installer ======================
CD %Temp%\MobSvc\Extracted
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed run install command ========
REM ==== Else run upgrade command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\temp\logfile.log
REM SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REM REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1} >> C:\Temp\logfile.log 2>&1
REM REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
REM IF NOT %ERRORLEVEL% EQU 0 (GOTO :INSTALL) ELSE GOTO :UPDATE
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UPDATE
:INSTALL
    echo "Install" >> c:\Temp\logfile.log
     UnifiedAgent.exe /Role "Agent" /CSEndpoint "10.10.20.168" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
GOTO :ENDSCRIPT
:UPDATE
    echo "Update" >> C:\Temp\logfile.log
    UnifiedAgent.exe /upgrade
:ENDSCRIPT

```

### <a name="step-2-create-a-package"></a>Etapa 2: Criar um Pacote

1. Faça logon no Console do System Center Configuration Manager
2. Navegue até **Biblioteca de Software** > **Gerenciamento de Aplicativos** > **Pacotes**
3. Clique com botão direito do mouse em **Pacotes** e selecione **Criar Pacote**
4. Forneça os valores para Nome, Descrição, Fabricante, Idioma e Versão.
5. Marque a caixa de seleção **Este pacote contém os arquivos de origem**.
6. Clique no botão **Procurar** e selecione o compartilhamento de rede em que o instalador está armazenado (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows)

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. Na página **Escolha o tipo de programa que você deseja criar**, selecione **Programa Padrão** e clique em **Avançar**

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. Na página **Especificar as informações sobre este programa padrão**, forneça as entradas a seguir e clique em **Próxima**. (As outras entradas podem ser deixadas com seus valores padrão)

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)   
| **Nome do Parâmetro** | **Valor** |
|--|--|
| Nome | Instalar o Serviço de Mobilidade do Microsoft Azure (Windows) |
| Linha de comando | install.bat |
| Programa pode ser executado | Se um usuário fez logon ou não |
9. Na página seguinte, selecione os sistemas operacionais de destino. O Serviço de Mobilidade pode ser instalado somente no Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.

  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png)   
10. Clique em Avançar duas vezes para concluir o assistente.

> [!NOTE]
> O script dá suporte tanto a instalações novas dos Agentes do Serviço de Mobilidade quanto à atualização de Agentes já instalados.

### <a name="step-3-deploy-the-package"></a>Etapa 3: Implantar o Pacote
1. No Console do SCCM, clique com botão direito do mouse no seu pacote e selecione **Distribuir Conteúdo**
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Selecione os **[Pontos de Distribuição](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** para os quais os pacotes devem ser copiados.
3. Depois de concluir o assistente, o pacote começará a ser replicado para os pontos de distribuição especificados
4. Quando a distribuição do pacote for concluída, clique com botão direito do mouse do pacote e selecione **Implantar**
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Selecione a coleção de dispositivos do Windows Server criada na seção de pré-requisitos como a coleção de destino para implantação.

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)
6. Na página **Especificar o destino de conteúdo**, selecione seus **Pontos de Distribuição**
7. Na página **Especificar configuração para controlar como este software é implantado**, verifique se a finalidade foi selecionada conforme necessário.

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. Especifique uma agenda em **Especificar a agenda para essa implantação**. Leia mais sobre [agendar pacotes](https://technet.microsoft.com/library/gg682178.aspx)
9. Configure as propriedades na página **Pontos de Distribuição** de acordo com as necessidades de seu Datacenter e conclua o assistente.

> [!TIP]
> Para evitar reinicializações desnecessárias, agende a instalação do pacote durante sua janela de manutenção mensal ou de Atualizações de Software.

Você pode monitorar o andamento da implantação usando o console do SCCM acessando **Monitoramento** > **Implantações** > *[nome do pacote]*
  ![monitor-sccm](./media/site-recovery-install-mobility-service-using-sccm/report.PNG)

## <a name="deploy-mobility-service-on-computers-running-linux-operating-systems"></a>Implantar o Serviço de Mobilidade em computadores que executam Sistemas Operacionais Linux
> [!NOTE]
> Este artigo pressupõe o seguinte
> 1. O endereço IP do servidor de configuração é 192.168.3.121
> 2. O compartilhamento de arquivos por rede segura é \\\ContosoSecureFS\MobilityServiceInstallers

### <a name="step-1-prepare-for-deployment"></a>Etapa 1: Preparar para a implantação
1. Crie uma pasta no compartilhamento de rede e nomeie-a como **MobSvcLinux**
2. Faça logon no Servidor de Configuração e abra um prompt de comando Administrativo
3. Execute os seguintes comandos para gerar um arquivo de frase secreta.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. Copie o arquivo MobSvc.passphrase para a pasta MobSvcWindows no compartilhamento de rede.
5. Em seguida, navegue até o repositório do instalador no Servidor de Configuração executando o comando.

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. Copie os seguintes arquivos para a pasta **MobSvcLinux** no compartilhamento de rede
  * Microsoft-ASR\_UA\_*version*\_OEL-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL6-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL7-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_SLES11-SP3-64\_GA\_*date*\_Release.tar.gz

7. Copie o código listado abaixo e salve-o como **install_linux.sh** para a pasta **MobSvcLinux**
> [!NOTE]
> Lembre-se de substituir os espaços reservados de [CSIP] no script abaixo pelos valores reais do endereço IP do seu Servidor de Configuração.

```
#!/bin/sh

rm -rf /tmp/MobSvc

mkdir -p /tmp/MobSvc

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
        cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            cp *RHEL7*.tar.gz /tmp/MobSvc
    fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
        echo $OS >> /tmp/MobSvc/sccm.log
        cp *SLES11*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
       cp *UBUNTU*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi
if [ "${OS}" ==  "" ]; then
    exit 1
fi
cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz


if [ -e /usr/local/.vx_version ];
then
    ./install -A u
    echo "Errorcode:$?"
    Error=$?

else
    ./install -t both -a host -R Agent -d /usr/local/ASR -i [CS IP] -p 443 -s y -c https -P MobSvc.passphrase >> /tmp/MobSvc/sccm.log 2>&1 && echo "Install Progress"
    Error=$?
fi
cd /tmp
rm -rf /tm/MobSvc
exit ${Error}
```

### <a name="step-2-create-a-package"></a>Etapa 2: Criar um Pacote

1. Faça logon no Console do System Center Configuration Manager
2. Navegue até **Biblioteca de Software** > **Gerenciamento de Aplicativos** > **Pacotes**
3. Clique com botão direito do mouse em **Pacotes** e selecione **Criar Pacote**
4. Forneça os valores para Nome, Descrição, Fabricante, Idioma e Versão.
5. Marque a caixa de seleção **Este pacote contém os arquivos de origem**.
6. Clique no botão **Procurar** e selecione o compartilhamento de rede em que o instalador está armazenado (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux)

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. Na página **Escolha o tipo de programa que você deseja criar**, selecione **Programa Padrão** e clique em **Avançar**

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. Na página **Especificar as informações sobre este programa padrão**, forneça as entradas a seguir e clique em **Próxima**. (As outras entradas podem ser deixadas com seus valores padrão)

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)   
| **Nome do Parâmetro** | **Valor** |
|--|--|
| Nome | Instalar o Serviço de Mobilidade do Microsoft Azure (Linux) |
| Linha de comando | ./install_linux.sh |
| Programa pode ser executado | Se um usuário fez logon ou não |

9. Na página seguinte, selecione **Esse programa pode ser executado em qualquer plataforma**
  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)   

10. Clique em **Avançar** duas vezes para concluir o assistente.
> [!NOTE]
> O script dá suporte tanto a instalações novas dos Agentes do Serviço de Mobilidade quanto à atualização de Agentes já instalados.

### <a name="step-3-deploy-the-package"></a>Etapa 3: Implantar o Pacote
1. No Console do SCCM, clique com botão direito do mouse no seu pacote e selecione **Distribuir Conteúdo**
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Selecione os **[Pontos de Distribuição](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** para os quais os pacotes devem ser copiados.
3. Depois de concluir o assistente, o pacote começará a ser replicado para os pontos de distribuição especificados.
4. Quando a distribuição do pacote for concluída, clique com botão direito do mouse do pacote e selecione **Implantar**
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Selecione a coleção de dispositivos do Linux Server criada na seção de pré-requisitos como a coleção de destino para implantação.

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)
6. Na página **Especificar o destino de conteúdo**, selecione seus **Pontos de Distribuição**
7. Na página **Especificar configuração para controlar como este software é implantado**, verifique se a finalidade foi selecionada conforme necessário.

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. Especifique uma agenda em **Especificar a agenda para essa implantação**. Leia mais sobre [agendar pacotes](https://technet.microsoft.com/library/gg682178.aspx)
9. Configure as propriedades na página **Pontos de Distribuição** de acordo com as necessidades de seu Datacenter e conclua o assistente.

O Serviço de Mobilidade é instalado na Coleção de Dispositivos do Linux Server de acordo com o agendamento configurado.

## <a name="other-methods-to-install-mobility-services"></a>Outros métodos para instalar os serviços de mobilidade
Leia mais sobre outras maneiras de instalar os serviços de mobilidade.
* [Instalação manual usando GUI](http://aka.ms/mobsvcmanualinstall)
* [Instalação manual usando linha de comando](http://aka.ms/mobsvcmanualinstallcli)
* [Instalação por push usando o Servidor de Configuração ](http://aka.ms/pushinstall)
* [Instalação automatizada usando a Automação do Azure e a Configuração de Estado Desejado ](http://aka.ms/mobsvcdscinstall)

## <a name="uninstall-mobility-service"></a>Desinstalar o Serviço de Mobilidade
Assim como ocorre com a instalação, é possível criar pacotes do SCCM para desinstalar o Serviço de Mobilidade. Use o script abaixo para desinstalar o Serviço de Mobilidade.

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>Próximas etapas
Agora você está pronto para [Habilitar a proteção](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications) para as máquinas virtuais.

