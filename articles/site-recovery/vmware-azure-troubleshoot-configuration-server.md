---
title: Solucionar problemas com o servidor de configuração durante a recuperação de desastres de VMs VMware e servidores físicos para o Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações sobre solução de problemas para implantar o servidor de configuração para recuperação de desastres de VMs VMware e servidores físicos no Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: f5c8241907459a06f0a6206ae6865cdf3fe9ab89
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998958"
---
# <a name="troubleshoot-configuration-server-issues"></a>Solucionar problemas do servidor de configuração

Este artigo ajuda a solucionar problemas ao implantar e gerenciar o [Servidor de configuração do Azure Site Recovery](site-recovery-overview.md). O servidor de configuração age como um servidor de gerenciamento e é usado para configurar a recuperação de desastre para VMs de VMware no local e servidores físicos para o Azure usar o Site Recovery. As seções a seguir discutem as falhas mais comuns vistas ao mesmo tempo adicionando um novo servidor de configuração e gerenciando um servidor de configuração.

## <a name="registration-failures"></a>Falhas de registro

A máquina de origem se registra com o servidor de configuração durante a instalação do agente de mobilidade. Quaisquer falhas durante esta etapa podem ser depuradas, seguindo as diretrizes abaixo:

1. Vá para o arquivo C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. ProgramData pode ser uma pasta oculta. Se não conseguir localizar, tente desocultar a pasta. As falhas podem ser devido a vários problemas.
2. Procure a cadeia de caracteres "Nenhum endereço IP válido encontrado". Se a cadeia de caracteres for encontrada,
    - Valide se a id do host solicitado é a mesma que o computador de origem.
    - O computador de origem deve ter pelo menos um endereço IP atribuído à NIC física para o registro do agente com o CS para obter êxito.
    - Execute o comando no computador de origem `> ipconfig /all` (para SO do Windows) e `# ifconfig -a` (para SO Linux) para obter todos os endereços IP da máquina de origem.
    - Observe que o registro do agente requer um endereço válido de v4 IP atribuído à NIC física.
3. Se a cadeia de caracteres acima não for encontrada, procure a cadeia de caracteres "Motivo" = > "NULL". Se encontrada,
    - Quando o computador de origem usa um host vazio, para se registrar no servidor de configuração, esse erro ocorre.
    - Depois de resolver os problemas, siga as diretrizes fornecidas [aqui](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) para repetir o registro manualmente.
4. Se a cadeia de caracteres acima não for encontrada, vá para o computador de origem e verifique se o log C:\ProgramData\ASRSetupLogs\UploadedLogs\* ASRUnifiedAgentInstaller.log ProgramData pode ser uma pasta oculta. Se não conseguir localizar, tente desocultar a pasta. As falhas podem ser devido a vários problemas. Pesquise cadeia de caracteres "publicar solicitação: (7) - Não foi possível se conectar ao servidor”. Se encontrada,
    - Resolva os problemas de rede entre o computador de origem e o servidor de configuração. Verifique se esse servidor de configuração está acessível do computador de origem usando as ferramentas de rede, como ping, traceroute, navegador da web etc., verifique se esse computador de origem é capaz de alcançar o servidor de configuração usando a porta 443.
    - Verifique se há que quaisquer regras de firewall no computador de origem que esteja bloqueando a conexão entre o servidor de configuração e do computador de origem. Trabalhe com o seu administrador para desbloquear os problemas de conexão de rede.
    - Verifique se as pastas mencionadas [aqui](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) são excluídas do software antivírus.
    - Depois de resolver os problemas de rede, repita o registro pelas diretrizes fornecidas [aqui](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
5. Se não for encontrado, na mesma aparência de log para a cadeia de caracteres "solicitação: (60) - certificado par não pode ser autenticado com certificado de Autoridade de Certificação fornecido”. Se encontrado, 
    - Esse erro pode ocorrer porque o certificado do servidor de configuração expirou ou computador de origem não dá suporte a TLS 1.0 e SSL protocolos acima ou há um firewall que está bloqueando a comunicação SSL entre o servidor de configuração e o computador de origem.
    - Para resolver, conecte-se ao endereço IP de servidor de configuração usando um navegador da web no computador de origem com a ajuda do URI https://<CSIPADDRESS>: 443 /. Verifique se que essa máquina de origem é capaz de alcançar o servidor de configuração usando a porta 443.
    - Verifique se há que quaisquer regras de firewall no computador de origem que esteja bloqueando a conexão entre o servidor de configuração e do computador de origem. Trabalhe com o seu administrador para desbloquear os problemas de conexão de rede.
    - Verifique se as pastas mencionadas [aqui](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) são excluídas do software antivírus.  
    - Depois de resolver os problemas, repita o registro pelas diretrizes fornecidas [aqui](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
6. No Linux, se o valor de plataforma de /etc/drscout.conf < INSTALLATION_DIR > estiver corrompido, em seguida, o registro falhará. Para identificar, vá para o log /var/log/ua_install.log. Você encontrará a cadeia de caracteres "Anular configuração pois o valor de VM_PLATFORM é null ou não é VmWare/Azure." A plataforma deve ser definida como "VmWare" ou "Azure". Como o arquivo drscout. conf está corrompido, é recomendável [desinstalar](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) o agente de mobilidade e reinstalar. Se a desinstalação falhar, siga as etapas a seguir:
    - Abra o arquivo Installation_Directory/uninstall.sh e comente a chamada para a função *StopServices*
    - Abra o arquivo Installation_Directory/Vx/bin/desinstalação e comente a chamada para a função `stop_services`
    - Abra o arquivo Installation_Directory/Fx/desinstalação e comente a seção completa que está tentando interromper o serviço de Fx.
    - Agora, tente [desinstalar](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) o agente de mobilidade. Após a instalação de desinstalação bem-sucedida, reinicialize o sistema e tente instalar o agente novamente.

## <a name="installation-failure---failed-to-load-accounts"></a>Falha na instalação - falha ao carregar contas

Esse erro ocorre quando o serviço não pode ler dados de conexão de transporte ao instalar o agente de mobilidade e registrar com o servidor de configuração. Para resolver, certifique-se que TLS 1.0 está habilitado em seu computador de origem.

## <a name="change-ip-address-of-configuration-server"></a>Altere o endereço IP do servidor e configuração

É altamente recomendável não alterar o endereço IP de um servidor de configuração. Certifique-se de que todos os IPs atribuídos ao servidor de configuração são IPs estáticos e não IPs DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: o token SAML é inválido

Para evitar esse erro, verifique se o horário no relógio do sistema não está mais de 15 minutos antes/depois do horário local. Execute novamente o instalador para concluir o registro.

## <a name="failed-to-create-certificate"></a>Falha ao criar certificado

Não é possível criar um certificado necessário para autenticar a Recuperação de Site. Execute a instalação novamente depois de verificar que está executando a instalação como administrador local.

## <a name="register-source-machine-with-configuration-server"></a>Registre a máquina de origem com o servidor de configuração

### <a name="if-source-machine-has-windows-os"></a>Se o computador de origem tiver um sistema operacional do Windows

Execute o comando a seguir no computador de origem

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**Configuração** | **Detalhes**
--- | ---
Uso | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Logs de configuração do agente | Sob %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
/PassphraseFilePath |  Obrigatório. Local da frase secreta. Use qualquer caminho UNC ou arquivo local válido.

### <a name="if-source-machine-has-linux-os"></a>Se a máquina de origem tiver sistema operacional Linux

Execute o comando a seguir no computador de origem

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**Configuração** | **Detalhes**
--- | ---
Uso | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
-P |  Obrigatório. Caminho de arquivo completo do arquivo no qual a frase secreta é salvo. Use qualquer pasta válida