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
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 1e2452ccc6d3f1859a39e1ee09cdde32f53767ba
ms.contentlocale: pt-br
ms.lasthandoff: 08/28/2017

---
# <a name="troubleshooting-mobility-service-push-install-issues"></a>Solução de problemas de instalação por push do Serviço de Mobilidade

Este artigo fornece detalhes dos problemas comuns enfrentados ao tentar instalar o Serviço de Mobilidade no servidor de origem para habilitar a proteção.

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(Código de erro 95107) Não foi possível habilitar a proteção.
**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95107 </br>***Mensagem –*** Falha na instalação por push do serviço de mobilidade no computador de origem com o código de erro ***EP0858***. <br> As credenciais fornecidas para instalar o serviço de mobilidade estão incorretas ou a conta de usuário não tem privilégios suficientes | As credenciais do usuário fornecidas para instalar o serviço de mobilidade no computador de origem estão incorretas | Verifique se as credenciais do usuário fornecidas para o computador de origem no servidor de configuração estão corretas. <br> Para adicionar/editar as credenciais do usuário: acesse o servidor de configuração > ícone da Cspsconfigtool > Gerenciar conta. </br> Além disso, garanta que os pré-requisitos abaixo foram verificados para concluir a instalação por push com êxito.

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(Código de erro 95015) Não foi possível habilitar a proteção.
**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95105 </br>***Mensagem –*** Falha na instalação por push do serviço de mobilidade no computador de origem com o código de erro ***EP0856***. <br> O “Compartilhamento de Arquivos e Impressoras” não é permitido no computador de origem ou há problemas de conectividade de rede entre o servidor em processo e o computador de origem| O compartilhamento de arquivos e impressoras não está habilitado | Permita “Compartilhamento de Arquivos e Impressoras” no computador de origem no Firewall do Windows, acesse o computador de origem > em configurações do Firewall do Windows > “Permitir um aplicativo ou recurso pelo Firewall” > selecione “Compartilhamento de Arquivos e Impressoras para todos os perfis”. </br> Além disso, garanta que os pré-requisitos abaixo foram verificados para concluir a instalação por push com êxito.

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(Código de erro 95117) Não foi possível habilitar a proteção.
**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95117 </br>***Mensagem –*** Falha na instalação por push do serviço de mobilidade no computador de origem com o código de erro ***EP0865***. <br> O computador de origem não está em execução ou há problemas de conectividade de rede entre o servidor em processo e o computador de origem | Conectividade de rede entre o servidor em processo e o servidor de origem | Verifique a conectividade entre o servidor em processo e o servidor de origem. </br> Além disso, garanta que os pré-requisitos abaixo foram verificados para concluir a instalação por push com êxito.

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(Código de erro 95103) Não foi possível habilitar a proteção.
**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95103 </br>***Mensagem –*** Falha na instalação por push do serviço de mobilidade no computador de origem com o código de erro ***EP0854***. <br> O “WMI (Instrumentação de Gerenciamento do Windows)” não é permitido no computador de origem ou há problemas de conectividade de rede entre o servidor em processo e o computador de origem| WMI (Instrumentação de Gerenciamento do Windows) bloqueado no Firewall do Windows | Permita o WMI (Instrumentação de Gerenciamento do Windows) no Firewall do Windows. Em configurações do Firewall do Windows > “Permitir um aplicativo ou recurso pelo Firewall” > “selecionar o WMI para todos os perfis”. </br> Além disso, garanta que os pré-requisitos abaixo foram verificados para concluir a instalação por push com êxito.

## <a name="check-push-install-logs-for-errors"></a>Verificar se há erros nos Logs de Instalação por Push

No servidor de Configuração/em Processo, navegue para o arquivo “PushinstallService” localizado em <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ para entender a origem do problema e usar as etapas de solução de problemas abaixo para resolver o problema.</br>
![pushiinstalllogs](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-install-pre-requisites-for-windows"></a>Pré-requisitos da Instalação por Push para o Windows
### <a name="ensure-file-and-printer-sharing-is-enabled"></a>Verificar se a opção “Compartilhamento de Arquivos e Impressoras” está habilitada
Permita “Compartilhamento de Arquivos e Impressoras” e “Instrumentação de Gerenciamento do Windows” no computador de origem no Firewall do Windows </br>
#### <a name="if-source-machine-is-domain-joined-br"></a>Se o computador de origem for ingressado no domínio: </br>
Defina as configurações de firewall usando o GPMC (Console de Gerenciamento de Política de Grupo).
1. Faça logon no computador de domínio do Active Directory como administrador e abra o Console de Gerenciamento de Política de Grupo (GPMC.MSC, execute em Iniciar > Executar).</br>
3. Se o GPMC não estiver instalado, siga o link para [Instalar o GPMC](https://technet.microsoft.com/library/cc725932.aspx) </br>
4. Na árvore de console do GPMC, clique duas vezes nos Objetos de Política de Grupo na floresta e no domínio e navegue para “Política de Domínio Padrão”. </br>
![gpmc1](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
5. Clique com o botão direito do mouse em “Política de Domínio Padrão” > Editar > uma nova janela “Editor de Gerenciamento de Política de Grupo” será aberta. </br>
![gpmc2](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
6. No Editor de Gerenciamento de Política de Grupo, navegue para Configuração do Computador > Políticas > Modelos Administrativos > Rede > Conexões de Rede > Firewall do Windows. </br>
![gpmc3](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
7. Habilite as seguintes configurações para o Perfil de Domínio e o Perfil Padrão </br>
a) Clique duas vezes na exceção “Firewall do Windows: permitir exceção de compartilhamento de arquivos e impressoras de entrada”. Selecione Habilitado e clique em OK. </br>
b) Clique duas vezes na exceção “Firewall do Windows: permitir exceção de administração remota de entrada”. Selecione Habilitado e clique em OK. </br>
![gpmc4](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

###### <a name="if-source-machine-is-not-domain-joined-and-part-of-workgroup-br"></a>Se o computador de origem não estiver ingressado no domínio e fizer parte de um grupo de trabalho </br>
Defina as configurações de firewall no computador remoto (para o grupo de trabalho):
1. Acesse o computador de origem,</br>
2. Em configurações do Firewall do Windows > “Permitir um aplicativo ou recurso pelo Firewall” > selecione “Compartilhamento de Arquivos e Impressoras para todos os perfis”. </br>
3. Em configurações do Firewall do Windows > “Permitir um aplicativo ou recurso pelo Firewall” > “selecionar o WMI para todos os perfis”. </br>

#### <a name="disable-remote-user-account-control-uac"></a>Desabilitar o UAC (Controle de Conta de Usuário)
Desabilite o UAC usando a chave do Registro para enviar por push o serviço de mobilidade.
1. Clique em Iniciar > Executar > digite regedit > ENTER
2. Localize e, em seguida, clique na seguinte subchave do Registro: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
3. Se a entrada do Registro LocalAccountTokenFilterPolicy não existir, siga estas etapas:
4. No menu Editar > Novo > clique em Valor DWORD.
5. Digite LocalAccountTokenFilterPolicy e, em seguida, pressione ENTER.
6. Clique com o botão direito do mouse em LocalAccountTokenFilterPolicy e, em seguida, clique em Modificar.
7. Na caixa de dados Valor, digite 1 e, em seguida, clique em OK.
8. Saia do Editor do Registro.


## <a name="push-install-pre-requisites-for-linux"></a>Pré-requisitos da Instalação por Push para o Linux:

1. Crie um usuário raiz no servidor Linux de origem. (Use essa conta somente para a instalação por push e atualizações)</br>
2. Verifique se o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede. </br>
3. Verifique se os últimos pacotes openssh, openssh-server e openssl estão instalados no servidor Linux de origem. </br>
Verifique se a porta 22 SSH está habilitada e em execução. </br>
4. Verifique se as entradas obsoletas de agentes ainda estão presentes no servidor de origem, desinstale os agentes mais antigos e reinicialize o servidor e reinstale o agente. </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>Habilitar a autenticação de senha e subsistema SFTP no arquivo sshd_config
1. No servidor de origem, entre como raiz. </br>
2. No arquivo /etc/ssh/sshd_config,</br> localize a linha que começa com PasswordAuthentication. </br>
3. d.   Remova a marca de comentário da linha e altere o valor de “no” para “yes”. </br>
![Linux1](./media/site-recovery-protection-common-errors/linux1.png)
4. Localize a linha que começa com “Subsystem” e remova a marca de comentário na linha. </br>
![Linux2](./media/site-recovery-protection-common-errors/linux2.png)
5. Salve as alterações e reinicie o serviço sshd. </br>

## <a name="push-installation-checks-on-configurationprocess-server"></a>A Instalação por Push verifica o servidor de Configuração/em Processo.
#### <a name="validate-credentials-for-discovery-and-installation"></a>Validar as credenciais para descoberta e instalação

1. No Servidor de Configuração, inicie Cspsconfigtool</br>
![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. Verifique se a conta usada para proteção tem direitos de administrador no computador de origem. </br>

#### <a name="check-connectivity-between-process-server-and-source-server"></a>Verificar a conectividade entre o servidor em processo e o servidor de origem
1. Verifique se o Servidor em Processo tem conexão com a Internet.
2. Verifique a conexão do WMI usando wbemtest.exe. </br>
No Servidor em processo, clique em Iniciar > Executar > wbemtest.exe > a janela Testador da Instrumentação de Gerenciamento do Windows será aberta, conforme mostrado.</br>
   ![WMItestConnect1](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
Clique em Conectar > Insira o IP do servidor de origem na Entrada de Namespace, Nome de Usuário e Senha (se o computador de origem estiver ingressado no domínio, forneça o nome de domínio, juntamente com o nome de usuário como “domainName\username”. Se o computador de origem estiver em um grupo de trabalho, forneça somente o nome de usuário.) Selecione o Nível de autenticação como Privacidade de pacote. </br>
![WMItestConnect2](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
   Clique em Conectar. Agora, a conexão do WMI deverá ser bem-sucedida com os dados fornecidos e a janela Testador da Instrumentação de Gerenciamento do Windows deverá ser exibida, conforme mostrado abaixo: </br>
   ![WMItestConnect3](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
</br>
   Se a conexão do WMI não for bem-sucedida, um pop-up de mensagem de erro será exibido. A captura de tela abaixo mostra uma tentativa sem êxito se a opção WMI/Administração Remota não está habilitada no aplicativo permitido pelo firewall do Windows. </br>
   ![WMItestConnect4](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
</br>

3. Verifique a conectividade e o status do WMI.</br>
No servidor de Configuração/em Processo, </br>
Clique em Iniciar > Executar > wmimgmt.msc > Ações > Mais Ações > Conectar a outro computador (computador de origem). </br>
Insira as credenciais da conta usada para proteção e verifique se a conectividade está adequada. </br>

#### <a name="verify-network-shared-folders-of-source-machine-is-accessible-from-process-server-ps-remotely-using-specified-credentials"></a>Verificar se a pastas compartilhadas de rede do computador de origem são acessíveis no PS (Servidor em Processo) remotamente usando as credenciais especificadas.
  1. Faça logon no computador do PS (Servidor em Processo), abra o Explorador de Arquivos > na barra de endereços, digite > “\\\source-machine-ip\C$” > clique em Enter. </br>
  ![Fileshare1](./media/site-recovery-protection-common-errors/fileshare1.png) </br>
  2. O Explorador de Arquivos solicitará as credenciais. Insira o nome de usuário e a senha > clique em OK.</br>
   Se o computador de origem estiver ingressado no domínio, forneça o nome de domínio, juntamente com o nome de usuário como “domainName\username”.</br>
   Se o computador de origem estiver em um grupo de trabalho, forneça somente o “username”. </br>
  ![Fileshare2](./media/site-recovery-protection-common-errors/fileshare2.png) </br>
  3. Se a conexão for bem-sucedida, você poderá exibir as pastas do computador de origem remotamente por meio do PS (Servidor em Processo) </br>
  ![Fileshare3](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> Se a conexão não for bem-sucedida, verifique se todos os pré-requisitos foram atendidos.
>

Se você não desejar abrir a “Instrumentação de Gerenciamento do Windows”, também poderá instalar o serviço de mobilidade manualmente no computador de origem.</br> [Instalar o Serviço de Mobilidade manualmente por meio da GUI](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) </br>
[Diretrizes de instalação por meio do Configuration Manager](site-recovery-install-mobility-service-using-sccm.md) </br>

## <a name="next-steps"></a>Próximas etapas
- [Habilitar a replicação em máquinas virtuais do VMware](vmware-walkthrough-enable-replication.md)

