---
title: "Solução de problemas do Azure Site Recovery por meio do VMware no Azure | Microsoft Docs"
description: "Solução de erros durante a replicação de máquinas virtuais do Azure"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: d4d6d273001642ebb8c960333a1c01977e863dbc
ms.contentlocale: pt-br
ms.lasthandoff: 09/07/2017

---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Solução de problemas da instalação por push do Serviço de Mobilidade

Este artigo descreve problemas comuns que você poderá enfrentar ao tentar instalar o Serviço de Mobilidade do Azure Site Recovery no servidor de origem para habilitar a proteção.

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(Código de erro 95107) Não foi possível habilitar a proteção
**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95107 </br>**Mensagem:** falha na instalação por push do Serviço de Mobilidade no computador de origem com o código de erro **EP0858**. <br> As credenciais fornecidas para instalar o Serviço de Mobilidade estão incorretas ou a conta de usuário não tem privilégios suficientes. | As credenciais do usuário fornecidas para instalar o Serviço de Mobilidade no computador de origem estão incorretas. | Verifique se as credenciais do usuário fornecidas para o computador de origem no servidor de configuração estão corretas. <br> Para adicionar/editar as credenciais do usuário, acesse o servidor de configuração e selecione **Cspsconfigtool** > **Gerenciar conta**. </br> Além disso, verifique os pré-requisitos a seguir para concluir com êxito a instalação por push.

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(Código de erro 95015) Não foi possível habilitar a proteção
**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95105 </br>**Mensagem:** falha na instalação por push do Serviço de Mobilidade no computador de origem com o código de erro **EP0856**. <br> O **Compartilhamento de Arquivos e Impressoras** não é permitido no computador de origem ou há problemas de conectividade de rede entre o servidor em processo e o computador de origem.| A opção **Compartilhamento de Arquivos e Impressoras** não está habilitada. | Permita o **Compartilhamento de Arquivos e Impressoras** no computador de origem no Firewall do Windows. No computador de origem, em **Firewall do Windows** > **Permitir um aplicativo ou recurso pelo Firewall**, selecione **Compartilhamento de Arquivos e Impressoras para todos os perfis**. </br> Além disso, verifique os pré-requisitos a seguir para concluir com êxito a instalação por push.

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(Código de erro 95117) Não foi possível habilitar a proteção
**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95117 </br>**Mensagem:** falha na instalação por push do Serviço de Mobilidade no computador de origem com o código de erro **EP0865**. <br> O computador de origem não está em execução ou há problemas de conectividade de rede entre o servidor em processo e o computador de origem. | Problemas de conectividade de rede entre o servidor em processo e o servidor de origem. | Verifique a conectividade entre o servidor em processo e o servidor de origem. </br> Além disso, verifique os pré-requisitos a seguir para concluir com êxito a instalação por push.

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(Código de erro 95103) Não foi possível habilitar a proteção
**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95103 </br>**Mensagem:** falha na instalação por push do Serviço de Mobilidade no computador de origem com o código de erro **EP0854**. <br> O WMI (Instrumentação de Gerenciamento do Windows) não é permitido no computador de origem ou há problemas de conectividade de rede entre o servidor em processo e o computador de origem.| O WMI está bloqueado no Firewall do Windows. | Permita o WMI no Firewall do Windows. Em **Firewall do Windows** > **Permitir um aplicativo ou recurso pelo Firewall**, selecione **WMI para todos os perfis**. </br> Além disso, verifique os pré-requisitos a seguir para concluir com êxito a instalação por push.

## <a name="check-push-installation-logs-for-errors"></a>Verificar se há erros nos logs de instalação por push

No servidor de configuração/em processo, acesse o arquivo PushinstallService localizado em <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ para compreender a origem do problema. Use as etapas de solução de problemas a seguir para resolver o problema.</br>

![Logs de instalação por push](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-installation-prerequisites-for-windows"></a>Pré-requisitos de instalação por push para o Windows
### <a name="ensure-that-file-and-printer-sharing-is-enabled"></a>Verificar se a opção **Compartilhamento de Arquivos e Impressoras** está habilitada
Permitir **Compartilhamento de Arquivos e Impressoras** e **Instrumentação de Gerenciamento do Windows** no computador de origem no Firewall do Windows. </br>
#### <a name="if-the-source-machine-is-domain-joined-br"></a>Se o computador de origem estiver ingressado no domínio </br>
Defina as configurações de firewall usando o Console de Gerenciamento de Política de Grupo.

1. Entre no computador de domínio do Azure Active Directory como administrador. Abra o Console de Gerenciamento de Política de Grupo (GPMC.MSC, execute em **Iniciar** > **Executar**).</br>

2. Se o Console de Gerenciamento de Política de Grupo não estiver instalado, consulte [Instalar o Console de Gerenciamento de Política de Grupo](https://technet.microsoft.com/library/cc725932.aspx). </br>

3. Na árvore do Console de Gerenciamento de Política de Grupo, clique duas vezes em **Objetos de Política de Grupo** na floresta e no domínio. Acesse a **Política de Domínio Padrão**. </br>

    ![Política de Domínio Padrão](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
4. Clique com o botão direito do mouse em **Política de Domínio Padrão** > **Editar**. Uma nova janela **Editor de Gerenciamento de Política de Grupo** será aberta. </br>

    ![Editor de Gerenciamento de Política de Grupo](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
5. No **Editor de Gerenciamento de Política de Grupo**, acesse **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Rede** > **Conexões de Rede** > **Firewall do Windows**. </br>

    ![Firewall do Windows](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
6. Habilite as seguintes configurações para o **Perfil de Domínio** e o **Perfil Padrão**: </br>

    a. Clique duas vezes em **Firewall do Windows: permitir exceção de compartilhamento de arquivos e impressoras de entrada**. Selecione **Habilitado** e, em seguida, **OK**. </br>

    b. Clique duas vezes em **Firewall do Windows: permitir exceção de administração remota de entrada**. Selecione **Habilitado** e, em seguida, **OK**. </br>

    ![Perfil de Domínio](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

#### <a name="if-the-source-machine-isnt-domain-joined-and-part-of-a-workgroup-br"></a>Se o computador de origem não estiver ingressado no domínio e fizer parte de um grupo de trabalho </br>
Defina as configurações de firewall em um computador remoto (para um grupo de trabalho).

1. Acesse o computador de origem.</br>

2. Em **Firewall do Windows** > **Permitir um aplicativo ou recurso pelo Firewall**, selecione **Compartilhamento de Arquivos e Impressoras** para todos os perfis. </br>

3. Em **Firewall do Windows** > **Permitir um aplicativo ou recurso pelo Firewall**, selecione **WMI** para todos os perfis. </br>

#### <a name="disable-remote-user-account-control"></a>Desabilitar o Controle de Conta de Usuário remoto
Desabilite o Controle de Conta de Usuário usando a chave do Registro para enviar o Serviço de Mobilidade por push.

1. Selecione **Iniciar** > **Executar**, digite *regedit* e selecione **Enter**.

2. Localize e, em seguida, selecione a seguinte subchave do Registro: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System

3. Se a entrada do Registro LocalAccountTokenFilterPolicy não existir, siga estas etapas:

    a. No menu **Editar**, selecione **Novo** e, em seguida, selecione **Valor DWORD**.

    b. Digite *LocalAccountTokenFilterPolicy* e, em seguida, selecione **Enter**.

    c. Clique com o botão direito do mouse em **LocalAccountTokenFilterPolicy** e, em seguida, selecione **Modificar**.

    d. Na caixa de dados **Valor**, digite *1* e, em seguida, selecione **OK**.

4. Feche o Editor do Registro.


## <a name="push-installation-prerequisites-for-linux"></a>Pré-requisitos de instalação por push para o Linux

1. Crie um usuário raiz no servidor Linux de origem. (Use essa conta somente para a instalação por push e atualizações.)</br>

2. Verifique se o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede. </br>

3. Verifique se os últimos pacotes openssh, openssh-server e openssl estão instalados no servidor Linux de origem. Verifique se a porta 22 SSH está habilitada e em execução. </br>

4. Verifique se as entradas obsoletas de agentes ainda estão presentes no servidor de origem, desinstale os agentes mais antigos e reinicialize o servidor. Reinstale o agente. </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>Habilitar a autenticação de senha e subsistema SFTP no arquivo sshd_config
1. No servidor de origem, entre como raiz. </br>

2. No arquivo /etc/ssh/sshd_config, localize a linha que começa com “PasswordAuthentication”. </br>

3. Remova a marca de comentário da linha e altere o valor de “no” para “yes”. </br>

    ![PasswordAuthentication](./media/site-recovery-protection-common-errors/linux1.png)

4. Localize a linha que começa com “Subsystem” e remova a marca de comentário da linha. </br>

    ![Subsistema](./media/site-recovery-protection-common-errors/linux2.png)

5. Salve as alterações e reinicie o serviço sshd. </br>


## <a name="push-installation-checks-on-the-configurationprocess-server"></a>A instalação por push verifica o servidor de configuração/em processo
#### <a name="validate-credentials-for-discovery-and-installation"></a>Validar as credenciais para descoberta e instalação

1. No servidor de configuração, inicie Cspsconfigtool.</br>

    ![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. Verifique se a conta usada para proteção tem direitos de administrador no computador de origem. </br>

#### <a name="check-connectivity-between-the-process-server-and-the-source-server"></a>Verificar a conectividade entre o servidor em processo e o servidor de origem
1. Verifique se o servidor em processo tem conexão com a Internet.

2. Verifique a conexão do WMI usando wbemtest.exe. </br>

    a. No servidor em processo, selecione **Iniciar** > **Executar** > **wbemtest.exe**. A janela **Testador da Instrumentação de Gerenciamento do Windows** será aberta, conforme mostrado:</br>

      ![Testador do WMI](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
    b. Selecione **Conectar** e insira o IP do servidor de origem no **Namespace**.

    c. Insira o nome de **Usuário** e a **Senha**. (Se o computador de origem estiver ingressado no domínio, forneça o nome de domínio, juntamente com o nome de usuário como *domainName\username*. Se o computador de origem estiver em um grupo de trabalho, forneça somente o nome de usuário.)

    d. Selecione o nível de **Autenticação** como **Privacidade de pacote**. </br>

      ![Privacidade de pacote](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
    e. Selecione **Conectar**. Agora, a conexão do WMI exibe os dados fornecidos e a janela **Testador da Instrumentação de Gerenciamento do Windows** é exibida, conforme mostrado: </br>

      ![Dados do Testador do WMI](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
   </br>
      Se a conexão do WMI não for bem-sucedida, uma mensagem de erro será exibida. A captura de tela a seguir mostra uma tentativa sem êxito se a opção **WMI/Administração Remota** não está habilitada no aplicativo permitido pelo Firewall do Windows: </br>

    ![Mensagem de erro do Testador do WMI](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
   </br>

3. Verifique a conectividade e o status do WMI.</br>

    a. No servidor de configuração/em processo, selecione **Iniciar** > **Executar** > **wmimgmt.msc** > **Ações** > **Mais Ações** > **Conectar-se a outro computador (computador de origem)**. </br>

    b. Insira as credenciais da conta usada para proteção e verifique se a conectividade está adequada. </br>

#### <a name="verify-that-network-shared-folders-of-the-source-machine-are-accessible-from-the-process-server-remotely-by-using-specified-credentials"></a>Verificar se as pastas compartilhadas de rede do computador de origem estão acessíveis no servidor em processo remotamente usando as credenciais especificadas

  1. Entre no computador do servidor em processo e abra o Explorador de Arquivos. Na barra de endereços, digite *"\\\source-machine-ip\C$"*. Selecione **Inserir**. </br>

      ![Explorador de Arquivos](./media/site-recovery-protection-common-errors/fileshare1.png) </br>

  2. O Explorador de Arquivos solicitará as credenciais. Insira o nome de usuário e a senha e selecione **OK**.</br>

      * Se o computador de origem estiver ingressado no domínio, insira o nome de domínio, juntamente com o nome de usuário como *domainName\username*.</br>

      * Se o computador de origem estiver em um grupo de trabalho, insira somente o nome de usuário. </br>

      ![Caixa de diálogo Credenciais](./media/site-recovery-protection-common-errors/fileshare2.png) </br>

  3. Se a conexão for bem-sucedida, você poderá exibir as pastas do computador de origem remotamente por meio do servidor em processo. </br>

      ![Modo de exibição Pastas](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> Se a conexão não for bem-sucedida, verifique se todos os pré-requisitos foram atendidos.
>

Se você não desejar abrir a **Instrumentação de Gerenciamento do Windows**, também poderá instalar o Serviço de Mobilidade manualmente no computador de origem.</br> 

Para obter mais informações, consulte [Instalar o Serviço de Mobilidade manualmente por meio da GUI](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) e [Diretrizes de instalação por meio do Configuration Manager](site-recovery-install-mobility-service-using-sccm.md). </br>

## <a name="next-steps"></a>Próximas etapas
- [Habilitar a replicação em máquinas virtuais do VMware](vmware-walkthrough-enable-replication.md)

