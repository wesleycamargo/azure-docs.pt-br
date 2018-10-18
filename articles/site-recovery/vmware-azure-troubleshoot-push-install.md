---
title: Solucionar problemas de falhas de instalação do serviço de mobilidade por push durante Habilitar Replication (VMware para Azure) | Microsoft Docs
description: Solucionar problemas de erros de instalação por push do serviço/mobilidade ao replicar máquinas virtuais do Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.author: ramamill
ms.date: 09/19/2018
ms.openlocfilehash: 4c57d048f4c3222ac180355a6a700562415f601c
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49390186"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Solução de problemas da instalação por push do Serviço de Mobilidade

A instalação do serviço de mobilidade é uma etapa importante durante Habilitar Replicação. O sucesso dessa etapa depende exclusivamente dos pré-requisitos de reunião e trabalhe com as configurações com suporte. As falhas mais comuns enfrentados durante a instalação do serviço de mobilidade estão devido a

* Erros de credencial/privilégio
* Erros de conectividade
* Sistema operacional sem suporte

Quando você habilita a replicação, o Azure Site Recovery tenta enviar por push o agente de serviço de mobilidade em sua máquina virtual. Como parte desse processo, servidor de configuração tenta se conectar com a máquina virtual e copia o agente. Para habilitar a instalação com êxito, siga as diretrizes de solução de problemas passo a passo abaixo.

## <a name="credentials-check-errorid-95107--95108"></a>Verificação de credenciais (ErrorID: 95107 & 95108)

* Verificar se a conta de usuário escolhida durante a habilitar a replicação está **válida, precisa**.
* O Azure Site Recovery requer **privilégio de administrador** para executar a instalação por push.
  * Para Windows, verifique se a conta de usuário tem acesso administrativo, local ou domínio, no computador de origem.
  * Se você não estiver usando uma conta de domínio, precisará desabilitar o controle de Acesso de Usuário Remoto no computador local.
    * Para desabilitar o controle de Acesso de Usuário Remoto, na chave do Registro HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicione um novo DWORD: LocalAccountTokenFilterPolicy. Defina o valor para 1. Para executar essa etapa, execute o seguinte comando do prompt de comando:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Para o Linux, você deve escolher a conta raiz para a instalação bem-sucedida do agente de mobilidade.

Se você quiser modificar as credenciais da conta de usuário escolhido, siga as instruções fornecidas [aqui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="connectivity-check-errorid-95117--97118"></a>**Verificação de conectividade (ErrorID: 95117 & 97118)**

* Certifique-se de que você é capaz de executar o ping do servidor de configuração de sua máquina de origem. Se você tiver escolhido o servidor de processo de expansão durante a habilitação de replicação, verifique se que você é capaz de executar o ping de sua máquina de origem do servidor de processo.
  * Na linha de comando de máquina do servidor de origem, use o Telnet para executar ping no servidor do processo de expansão / servidor de configuração (padrão 9443) conforme exibido abaixo para ver se há problemas de conectividade de re3de ou problemas de bloqueio d porta do firewall.

     `telnet <CS/ scale-out PS IP address> <port>`

  * Se você não conseguir se conectar, permitir servidor de processo de porta de entrada 9443 no servidor do processo de expansão / servidor de configuração.
  * Verifique o status do serviço **InMage Scout VX Agent – Sentinel/Outpost**. Se não estiver em execução, tente iniciar o serviço.

* Além disso, para **máquina virtual Linux**,
  * Verifique se os últimos pacotes openssh, openssh-server e openssl estão instalados.
  * Verifique e garanta que o Secure Shell (SSH) esteja habilitado e em execução na porta 22.
  * Serviços SFTP devem estar em execução. Habilitar a autenticação de senha e subsistema SFTP no arquivo sshd_config,
    * Entre como raiz.
    * Vá para o arquivo /etc/ssh/sshd_config, localize a linha que começa com PasswordAuthentication.
    * Remova a marca de comentário da linha e altere o valor para yes
    * Localize a linha que começa com Subsystem e remova a marca de comentário da linha
    * Reinicie o serviço sshd.
* Uma tentativa de conexão falha se não houver nenhuma resposta adequada após um período de tempo ou a conexão estabelecida falhou porque o host conectado não respondeu.
* Pode ser o domínio/rede/conectividade relacionados ao problema. Também é possível devido ao o nome DNS resolvendo o problema ou problema de esgotamento de porta TCP. Verifique se há algum problema conhecido no seu domínio.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Verificação dos serviços de compartilhamento arquivos / impressoras (ErrorID: 95105 & 95106)

Após a verificação de conectividade, verifique se o arquivo e o serviço de compartilhamento de impressora está habilitado em sua máquina virtual.

Para **windows 2008 R2 e versões anteriores**,

* Para habilitar o compartilhamento de arquivo e impressão através do Firewall do Windows,
  * Abrir Painel de controle -> Sistema e segurança -> Firewall do Windows -> no painel esquerdo, clique em configurações Avançadas -> clique em regras de entrada na árvore de console.
  * Localize as regras de compartilhamento de arquivos e compartilhamento de impressora (NB-Sessão-entrada) e arquivo e impressora (SMB-entrada). Para cada regra, clique com botão direito na regra e, em seguida, clique em **Habilitar regra**.
* Habilitar compartilhamento de arquivo com a Política de Grupo,
  * Vá para iniciar, digite o gpmc.msc e pesquisar.
  * No painel de navegação, abra as seguintes pastas: política de computador Local, configuração do usuário, modelos administrativos, componentes do Windows e compartilhamento de rede.
  * No painel de detalhes, clique duas vezes **Impedir os usuários de compartilhamento de arquivos em seus perfis**. Para desabilitar a configuração de Política de Grupo, e habilitar a capacidade do usuário para compartilhar arquivos, clique em Desabilitado. Clique em OK para salvar as alterações. Para saber mais, clique [aqui](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Para **em versões posteriores**, siga as instruções fornecidas [aqui](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) para habilitar o compartilhamento de arquivo e impressora

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>Verificação da configuração da Instrumentação de Gerenciamento do Windows (VMI)

Depois de verificar se os serviços de arquivo e impressora, habilite o serviço WMI através do firewall.

* No painel de controle, clique em segurança e, em seguida, clique em Firewall do Windows.
* Clique em Configurações de Segurança e clique na guia Alterar configurações.
* Na janela de exceções, marque a caixa de seleção para a Instrumentação de Gerenciamento do Windows (VMI) para habilitar o tráfego WMI através do firewall. 

Você também pode habilitar o tráfego WMI através do firewall no prompt de comando. Use o comando `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes` a seguir.
Outros artigos de solução de problemas do WMI foi possível encontrar os artigos a seguir.

* [Teste de WMI básico](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Solução de problemas de WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Solucionar problemas com scripts de WMI e serviços de WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Sistema operacional sem suporte

Outro motivo mais comum para falha pode ser devido ao sistema operacional sem suporte. Verifique se que você estiver usando a versão do Kernel do sistema operacional com suporte para uma instalação bem-sucedida do serviço de mobilidade.

Para saber mais sobre quais sistemas operacionais com suporte pelo Azure Site Recovery, consulte nosso [documento da matriz de suporte](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="next-steps"></a>Próximas etapas

[Aprenda a](vmware-azure-tutorial.md) configurar uma recuperação após desastres para VMs VMware.