---
title: Solucionar problemas com o servidor de configuração durante a recuperação de desastres de VMs do VMware e servidores físicos no Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações para a solução de problemas com a implantação do servidor de configuração para recuperação de desastres de VMs do VMware e de servidores físicos no Azure usando o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/14/2019
ms.author: ramamill
ms.openlocfilehash: 0eebfd8b75f428d3b8f6024ed6ee71c18c1309f6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435967"
---
# <a name="troubleshoot-configuration-server-issues"></a>Solucionar problemas do servidor de configuração

Este artigo ajuda a solucionar problemas ao implantar e gerenciar o servidor de configuração do [Azure Site Recovery](site-recovery-overview.md). O servidor de configuração funciona como um servidor de gerenciamento. Use o servidor de configuração para configurar a recuperação de desastres de VMs do VMware local e de servidores físicos no Azure usando o Site Recovery. As seções a seguir tratam das falhas mais comuns que podem ocorrer quando você adiciona um novo servidor de configuração e quando gerencia um servidor de configuração.

## <a name="registration-failures"></a>Falhas de registro

O computador de origem se registra no servidor de configuração quando você instala o agente de mobilidade. Você pode depurar qualquer falha durante esta etapa seguindo estas diretrizes:

1. Abra o arquivo C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (A pasta ProgramData pode ser uma pasta oculta. Se você não vir a pasta ProgramData, no Explorador de Arquivos, na guia **Exibir**, na seção **Mostrar/ocultar**, marque a caixa **Itens ocultos**.) As falhas podem ser causadas por vários problemas.

2. Procure a cadeia de caracteres **Nenhum endereço IP válido encontrado**. Se a cadeia de caracteres for encontrada,
    1. verifique se a ID do host solicitada é a mesma que a ID do host do computador de origem.
    2. verifique se o computador de origem tem pelo menos um endereço IP atribuído à NIC física. Para que o registro do agente no servidor de configuração ocorra com êxito, o computador de origem deve ter pelo menos um endereço IP v4 válido atribuído à NIC física.
    3. Execute um dos seguintes comandos no computador de origem para obter todos os endereços IP da máquina de origem:
      - No Windows: `> ipconfig /all`
      - No Linux: `# ifconfig -a`

3. Se a cadeia de caracteres **Nenhum endereço IP válido encontrado** não for encontrada, procure a cadeia de caracteres **Reason=>NULL**. Esse erro ocorre se o computador de origem usar um host vazio para se registrar no servidor de configuração. Se a cadeia de caracteres for encontrada,
    - após solucionar os problemas, siga as diretrizes em [Registrar o computador de origem no servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) para repetir o registro manualmente.

4. Se a cadeia de caracteres **Reason=>NULL** não for encontrada, no computador de origem, abra o arquivo C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (A pasta ProgramData pode ser uma pasta oculta. Se você não vir a pasta ProgramData, no Explorador de Arquivos, na guia **Exibir**, na seção **Mostrar/ocultar**, marque a caixa **Itens ocultos**.) As falhas podem ser causadas por vários problemas. 

5. Procure a cadeia de caracteres **publicar solicitação: (7) – Não foi possível se conectar ao servidor**. Se a cadeia de caracteres for encontrada,
    1. resolva os problemas de rede entre o computador de origem e o servidor de configuração. Verifique se o servidor de configuração está acessível no computador de origem usando as ferramentas de rede, como ping, traceroute ou um navegador da Web. Verifique se o computador de origem é capaz de acessar o servidor de configuração usando a porta 443.
    2. Verifique se alguma regra de firewall no computador de origem está bloqueando a conexão entre o computador de origem e o servidor de configuração. Atue em conjunto com os administradores de rede para desbloquear quaisquer problemas de conexão.
    3. Verifique se as pastas listadas em [Exclusões de pasta do Site Recovery de programas antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) foram excluídas do software antivírus.
    4. Após solucionar os problemas de rede, repita o registro seguindo as diretrizes apresentadas em [Registrar o computador de origem no servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Se a cadeia de caracteres **publicar solicitação: (7) – não foi possível se conectar ao servidor** não for encontrada, no mesmo arquivo de log, procure a cadeia de caracteres **solicitação: (60) – o certificado par não pode ser autenticado com os certificados da Autoridade de Certificação fornecidos**. Esse erro pode ocorrer porque o certificado do servidor de configuração expirou ou o computador de origem não oferece suporte a TLS 1.0 ou a protocolos SSL mais recentes. Tal erro também pode ocorrer se um firewall bloquear a comunicação SSL entre o computador de origem e o servidor de configuração. Se a cadeia de caracteres for encontrada, 
    1. para resolver o problema, conecte-se ao endereço IP do servidor de configuração usando um navegador da Web no computador de origem. Use a URI https:\/\/<endereço IP do servidor de configuração\>:443/. Verifique se o computador de origem é capaz de acessar o servidor de configuração usando a porta 443.
    2. Confira se alguma regra de firewall no computador de origem precisa ser adicionada ou removida para que esse computador se comunique com o servidor de configuração. Em razão da variedade de softwares de firewall que pode estar sendo usada, não é possível listar todas as configurações de firewall necessárias. Atue em conjunto com os administradores de rede para desbloquear quaisquer problemas de conexão.
    3. Verifique se as pastas listadas em [Exclusões de pasta do Site Recovery de programas antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) foram excluídas do software antivírus.  
    4. Após solucionar os problemas, repita o registro seguindo as diretrizes em [Registrar o computador de origem no servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. No Linux, se o valor da plataforma em <INSTALLATION_DIR\>/etc/drscout.conf estiver corrompido, ocorrerá falha do registro. Para identificar esse problema, abra o arquivo /var/log/ua_install.log. Procure a cadeia de caracteres **Anular configuração pois o valor de VM_PLATFORM é nulo ou não é VmWare/Azure**. A plataforma deve ser definida como **VmWare** ou **Azure**. Se o arquivo drscout.conf estiver corrompido, é recomendável [desinstalar o agente de mobilidade](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) e, em seguida, reinstalá-lo. Se a desinstalação falhar, siga estas etapas:
    1. Abra o arquivo Installation_Directory/uninstall.sh e comente a chamada para a função **StopServices**.
    2. Abra o arquivo Installation_Directory/Vx/bin/uninstall.sh e comente a chamada para a função **stop_services**.
    3. Abra o arquivo Installation_Directory/Fx/uninstall.sh e comente a seção completa que está tentando interromper o serviço de Fx.
    4. [Desinstale](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) o agente de mobilidade. Após a desinstalação bem-sucedida, reinicialize o sistema e tente reinstalar o agente de mobilidade.

## <a name="installation-failure-failed-to-load-accounts"></a>Falha na instalação: falha ao carregar contas

Esse erro ocorre quando o serviço não pode ler os dados de conexão de transporte ao instalar o agente de mobilidade e registrar no servidor de configuração. Para resolver o problema, verifique se o TLS 1.0 está habilitado no computador de origem.

## <a name="vcenter-discovery-failures"></a>Falhas de descoberta do vCenter

Para resolver falhas de descoberta do vCenter, verifique se que esse vCenter Server está adicionado às configurações de proxy da lista de byPass. Para executar esta atividade:

- Baixe a ferramenta PsExec [aqui](https://aka.ms/PsExec) para acessar o conteúdo de usuário do sistema.
- Abra o Internet Explorer no conteúdo de usuário do sistema a seguinte linha de comando psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Adicione configurações de proxy no Internet Explorer e reinicie o serviço de tmanssvc.
- Para definir as configurações de proxy do DRA, execute    cd C:\Program Files\Microsoft Azure Site Recovery Provider
- Em seguida, execute DRCONFIGURATOR.EXE /configure /AddBypassUrls [adicione o Endereço IP/FQDN do vCenter Server fornecido durante a etapa **Configurar vCenter Server/servidor vSphere ESXi** da [implantação de Servidor de Configuração](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Alteração do endereço IP do servidor de configuração

É altamente recomendável que você não altere o endereço IP de um servidor de configuração. Verifique se todos os endereços IP atribuídos ao servidor de configuração são endereços IP estáticos. Não use endereços IP DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: o token SAML é inválido

Para evitar esse erro, verifique se não há uma diferença superior a 15 minutos entre o horário no relógio do sistema e o horário local. Execute novamente o instalador para concluir o registro.

## <a name="failed-to-create-a-certificate"></a>Falha ao criar um certificado

Não é possível criar o certificado necessário para autenticar o Site Recovery. Tente instalar novamente após verificar que você a está executando como administrador local.

## <a name="register-source-machine-with-configuration-server"></a>Registre a máquina de origem com o servidor de configuração

### <a name="if-the-source-machine-runs-windows"></a>Se o computador de origem estiver executando o Windows

Execute o comando a seguir no computador de origem:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
  ```

Configuração | Detalhes
--- | ---
Uso | UnifiedAgentConfigurator.exe  /CSEndPoint <endereço IP do servidor de configuração\> /PassphraseFilePath <caminho do arquivo de frase secreta\>
Logs de configuração do agente | Localizado em %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
/PassphraseFilePath |  Obrigatório. O local da frase secreta. Use qualquer caminho UNC ou arquivo local válido.

### <a name="if-the-source-machine-runs-linux"></a>Se o computador de origem estiver executando o Linux

Execute o comando a seguir no computador de origem:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Configuração | Detalhes
--- | ---
Uso | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <endereço IP do servidor de configuração\> -P <caminho do arquivo de frase secreta\>
-i | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
-P |  Obrigatório. Caminho completo do arquivo em que a frase secreta está salva. Use qualquer pasta válida.

