---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc
ms.lasthandoff: 02/17/2017


---



### <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade
A primeira etapa da habilitação da proteção para máquinas virtuais e servidores físicos é a instalação do Serviço de mobilidade. Isso pode ser feito de duas maneiras:

* **Processar envio por push de servidor**: ao habilitar a replicação em um computador, envie por push e instale o componente do serviço de Mobilidade pelo servidor de processo. 
*Observe* que a instalação por push não ocorrerá se os computadores já estiverem executando uma versão atualizada do componente.
* **Envio por push corporativo**: instale automaticamente o componente usando o processo de envio por push corporativo, como o WSUS ou o System Center Configuration Manager ou [Configuração do estado desejado e Automação do Azure](site-recovery-automate-mobility-service-install.md). Defina o servidor de configuração antes de fazer isso.
* **Instalação manual**: instale o componente manualmente em cada computador que você deseja replicar. Defina o servidor de configuração antes de fazer isso.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparar para o envio por push automático em computadores com Windows
Veja como preparar os computadores com Windows para que o Serviço de mobilidade possa ser instalado automaticamente pelo servidor de processo.

1. Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ter privilégios de administrador (local ou domínio) e é usada somente para a instalação por push.

   > [!NOTE]
   > Se você não estiver usando uma conta de domínio, precisará desabilitar o controle de Acesso de Usuário Remoto no computador local. Para fazer isso, no registro, em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicione a entrada DWORD LocalAccountTokenFilterPolicy com um valor de 1. Para adicionar a entrada de Registro de um tipo CLI **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. No Firewall do Windows do computador que você deseja proteger, selecione **Permitir um aplicativo ou recurso pelo Firewall**. Habilite o **Compartilhamento de Arquivo e Impressora** e a **Instrumentação de Gerenciamento do Windows**. Para computadores que pertencem a um domínio, é possível definir as configurações do firewall com um GPO.

   ![Configurações de firewall](./media/site-recovery-vmware-to-azure/mobility1.png)
3. Adicione a conta que você criou:

   * Abra **cspsconfigtool**. Ela está disponível como um atalho na área de trabalho e localizada na pasta [INSTALL LOCATION]\home\svsystems\bin.
   * Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.
   * Adicione a conta que você criou. Depois de adicionar a conta, você precisa fornecer as credenciais ao habilitar a replicação em um computador.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparar para o envio por push automático em servidores Linux
1. Certifique-se de que o computador com Linux que você deseja proteger tem suporte, conforme descrito em [pré-requisitos de computador protegido](#protected-machine-prerequisites). Verifique se há conectividade de rede entre o computador Linux e o servidor de processo.
2. Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ser um usuário raiz no servidor Linux de origem e é usada somente para a instalação por push.

   * Abra **cspsconfigtool**. Ela está disponível como um atalho na área de trabalho e localizada na pasta [INSTALL LOCATION]\home\svsystems\bin.
   * Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.
   * Adicione a conta que você criou. Depois de adicionar a conta, você precisa fornecer as credenciais ao habilitar a replicação em um computador.
3. Verifique se o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.
4. Instale os últimos pacotes openssh, openssh-server e openssl no computador que você deseja replicar.
5. Verifique se SSH está habilitado e em execução na porta 22.
6. Habilite a autenticação de subsistema e senha SFTP no arquivo sshd_config, como se segue:

   * Faça logon como raiz.
   * No arquivo /etc/ssh/sshd_config, localize a linha que começa com **PasswordAuthentication**.
   * Remova a marca de comentário da linha e altere o valor de **no** para **yes**.
   * Localize a linha que começa com **Subsystem** e remova a marca de comentário existente nessa linha.

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>Instalar o Serviço de Mobilidade manualmente
Os instaladores estão disponíveis no servidor de Configuração em **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

| Sistema operacional de origem | Arquivo de instalação do Serviço de mobilidade |
| --- | --- |
| Windows Server (somente&64; bits) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (somente 64 bits) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (somente 64 bits) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>Instalar Serviço de Mobilidade em um Windows Server
1. Baixe e execute o instalador relevante.
2. Em **Antes de começar**, selecione **Serviço de mobilidade**.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure/mobility3.png)
3. Em **Detalhes do Servidor de Configuração**, especifique o endereço IP do servidor de configuração e a frase secreta que foi gerada quando você executou a Configuração Unificada. Você pode recuperar a frase secreta executando: **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –v** no servidor de configuração.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure/mobility6.png)
4. Em **Localização de Instalação**, mantenha a configuração padrão e clique em **Avançar** para iniciar a instalação.
5. Em **Progresso da Instalação**, monitore a instalação e, se for solicitado, reinicie o computador. Depois de instalar o serviço, pode levar cerca de 15 minutos para que o status seja atualizado no portal.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Instalar o Serviço de Mobilidade em um Windows Server usando o prompt de comando
1. Copie o instalador para uma pasta local (digamos, C:\Temp) no servidor que você deseja proteger. O instalador pode ser encontrado no servidor de configuração em **[Install Location]\home\svsystems\pushinstallsvc\repository**. O pacote para Sistemas Operacionais Windows terá um nome semelhante a Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. Renomeie esse arquivo como MobilitySvcInstaller.exe
3. Execute o comando a seguir para extrair o instalador MSI:

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>Sintaxe de linha de comando completa
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Parâmetros**

* **/Role:** obrigatório. Especifica se o Serviço de mobilidade deve ser instalado. Agente de valores de entrada | MasterTarget
* **/InstallLocation:** obrigatório. Especifica onde instalar o serviço.
* **/PassphraseFilePath:** obrigatório. A frase secreta do servidor de configuração.
* **/LogFilePath:** obrigatório. Local onde os arquivos de log de instalação devem ser criados.

#### <a name="uninstall-the-mobility-service-manually"></a>Desinstalar o serviço de Mobilidade manualmente
O serviço de mobilidade pode ser desinstalado usando a opção Adicionar ou Remover Programas no Painel de Controle ou esta instrução de linha de comando: MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="install-the-mobility-service-on-a-linux-server"></a>Instalar o Serviço de Mobilidade em um servidor Linux
1. Copie o arquivo tar apropriado, baseado na tabela acima, no computador Linux que você deseja replicar.
2. Abra um programa do shell e extraia o arquivo tar compactado para um caminho local executando: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crie um arquivo passphrase.txt no diretório local para o qual você extraiu o conteúdo do arquivo tar. Para fazer isso, copie a frase secreta de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de configuração e salve-a em passphrase.txt executando *`echo <passphrase> >passphrase.txt`* no shell.
4. Instale o Serviço de Mobilidade executando *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique o endereço IP interno do servidor de configuração e verifique se a porta 443 está selecionada. Após instalar o serviço, pode levar cerca de 15 minutos para o status ser atualizado no portal.

**Você também pode instalar por meio da linha de comando**:

Copie a senha em C:\Program Files (x86)\InMage Systems\private\connection no servidor de configuração e salve-a como "passphrase.txt" no servidor de configuração. Em seguida, execute estes comandos. Em nosso exemplo, o endereço IP do servidor de configuração é 104.40.75.37 e a porta HTTPS deve ser 443:


Para instalar em um servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar no servidor mestre de destino:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt




