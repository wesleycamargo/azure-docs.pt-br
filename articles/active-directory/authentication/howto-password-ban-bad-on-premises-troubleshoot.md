---
title: Solução de problemas e registro em log na versão prévia da proteção por senha do Azure AD
description: Reconhecer o registro em log da proteção por senha do Azure AD e solução de problemas comuns
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: e5b3dc1bfa7c7890be83529e863907ec056f188f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292011"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Versão prévia: solução de problemas, relatório, monitoramento de proteção por senha do Azure AD

|     |
| --- |
| A lista de senhas proibidas personalizada e a proteção por senha do Azure AD são recursos de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Após a implantação da proteção por senha do Azure AD, o monitoramento e relatório são tarefas essenciais. Este artigo apresenta detalhes para ajudá-lo a reconhecer onde cada serviço registra as informações e como relatar sobre o uso da proteção por senha do Azure AD.

## <a name="on-premises-logs-and-events"></a>Eventos e logs locais

### <a name="dc-agent-service"></a>Serviço do agente DC

Em cada controlador de domínio, o software de serviço do agente DC grava os resultados de suas validações de senha (e outro status) em um log de eventos local: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

Os eventos são registrados pelos vários componentes do agente DC usando os intervalos a seguir:

|Componente |Intervalo de ID de evento|
| --- | --- |
|Dll do filtro de senha do Agente DC| 10000-19999|
|Processo de hospedagem de serviço do agente DC| 20000-29999|
|Lógica de validação da política de serviço do agente DC| 30000-39999|

Para uma operação de validação de senha com êxito, geralmente há um evento registrado da dll do filtro de senha do agente DC. Para uma operação de validação de senha com falha, geralmente há dois eventos registrados, um do serviço do agente DC e um da dll do filtro de senha do Agente DC.

Eventos discretos para capturar essas situações são registrados com base nos fatores a seguir:

* Se uma determinada senha está sendo definida ou alterada.
* Se a validação de uma determinada senha foi aprovada ou falhou.
* Se a validação falhou devido à política global da Microsoft versus a política organizacional.
* Se o modo somente auditoria está atualmente ativado ou desativado para a política de senha atual.

Os principais eventos relacionados à validação de senha são os seguintes:

|   |Alteração de senha |Definição de senha|
| --- | :---: | :---: |
|Aprovado |10014 |10015|
|Falha (não aprovou a política de senha do cliente)| 10016, 30002| 10017, 30003|
|Falha (não aprovou a política de senha da Microsoft)| 10016, 30004| 10017, 30005|
|Aprovado somente auditoria (teria falhado a política de senha do cliente)| 10024, 30008| 10025, 30007|
|Aprovado somente auditoria (teria falhado a política de senha da Microsoft)| 10024, 30010| 10025, 30009|

> [!TIP]
> Senhas de entrada são validadas primeiro na lista de senhas globais da Microsoft e, se isso falhar, nenhum processamento adicional será executado. Esse é o mesmo comportamento executado em alterações de senha no Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Mensagem de log de eventos de exemplo para definição de senha com êxito da ID do Evento 10014

A senha alterada para o usuário especificado foi validada como compatível com a política de senha atual do Azure.

 UserName: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Mensagem de log de eventos de exemplo para ID do Evento 10017 e definição de senha com falha 30003

10017:

A senha de redefinição do usuário especificado foi rejeitada porque não estava em conformidade com a política de senha atual do Azure. Consulte a mensagem de log de eventos correlacionada para obter mais detalhes.

 UserName: BPL_03283841185 FullName:

30003:

A senha de redefinição do usuário especificado foi rejeitada porque correspondia a pelo menos um dos tokens presentes na lista de senhas proibidas por locatário da política de senha atual do Azure.

 UserName: BPL_03283841185 FullName:

Algumas outras mensagens de log de eventos importantes que você deve conhecer são:

#### <a name="sample-event-log-message-for-event-id-30001"></a>Mensagem de log de eventos de exemplo para ID do Evento 30001

A senha do usuário especificado foi aceita porque uma política de senha do Azure ainda não está disponível

UserName: <user> FullName: <user>

Essa condição pode ser causada por um ou mais dos seguintes motivos:% n

1. A floresta ainda não foi registrada no Azure.

   Etapas de resolução: um administrador deve registrar a floresta usando o cmdlet Register-AzureADPasswordProtectionForest.

2. Um Proxy de proteção por senha do Azure AD ainda não está disponível em pelo menos um computador na floresta atual.

   Etapas de resolução: um administrador deve instalar e registrar um proxy usando o cmdlet Register-AzureADPasswordProtectionProxy.

3. Esse DC não tem conectividade de rede com nenhuma instância do Proxy de proteção por senha do Azure AD.

   Etapas de resolução: garanta que exista conectividade de rede em pelo menos uma instância do Proxy de proteção por senha do Azure AD.

4. Este DC não tem conectividade com outros controladores de domínio no domínio.

   Etapas de resolução: garanta que exista conectividade de rede no domínio.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Mensagem de log de eventos de exemplo para ID do Evento 30006

O serviço agora está impondo a seguinte política de senha do Azure.

 AuditOnly: 1

 Data da política global: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z

 Data da política do locatário: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z

 Impor política de locatário: 1

#### <a name="dc-agent-log-locations"></a>Locais de log do Agente DC

O serviço do agente DC também registrará eventos relacionados à operação no seguinte log: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

O serviço do agente DC também pode registrar eventos de rastreamento de nível de depuração detalhados no log a seguir: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

> [!WARNING]
> O log de Rastreamento é desabilitado por padrão. Quando habilitado, esse log recebe um alto volume de eventos e pode afetar o desempenho do controlador de domínio. Portanto, esse log aprimorado somente deverá ser habilitado quando um problema exigir uma investigação mais profunda e apenas por um período mínimo.

### <a name="azure-ad-password-protection-proxy-service"></a>Serviço de proxy de proteção por senha do Azure AD

O serviço de Proxy de proteção por senha emite um conjunto mínimo de eventos para o log de eventos a seguir: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational

O serviço de Proxy de proteção por senha também pode registrar eventos de rastreamento em nível de depuração detalhados no log a seguir: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace

> [!WARNING]
> O log de Rastreamento é desabilitado por padrão. Quando habilitado, esse log recebe um grande volume de eventos e isso pode afetar o desempenho do host de proxy. Portanto, esse log deverá ser habilitado apenas quando um problema exigir uma investigação mais profunda e apenas por um período mínimo.

### <a name="dc-agent-discovery"></a>Descoberta do Agente DC

O cmdlet `Get-AzureADPasswordProtectionDCAgent` pode ser usado para exibir informações básicas sobre os vários agentes DC em execução em um domínio ou floresta. Essas informações são recuperadas dos objetos serviceConnectionPoint registrados pelo(s) serviço(s) do agente DC em execução. Um exemplo de saída deste cmdlet é o seguinte:

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

As várias propriedades são atualizadas por cada serviço do agente DC em uma base horária aproximada. Os dados ainda estão sujeitos à latência de replicação do Active Directory.

O escopo da consulta do cmdlet pode ser influenciado usando os parâmetros –Forest ou –Domain.

### <a name="emergency-remediation"></a>Correção de emergência

Se ocorrer uma situação lamentável quando o serviço do agente DC estiver causando problemas, o serviço do agente DC poderá ser desligado imediatamente. A dll do filtro de senha do agente DC tenta chamar o serviço que não está em execução e registrará eventos de aviso (10012, 10013), mas todas as senhas de entrada serão aceitas durante esse período. O serviço do agente DC poderá, então, também ser configurado através do Gerenciador de Controle de Serviço do Windows com um tipo de inicialização “Desabilitado”, conforme necessário.

### <a name="performance-monitoring"></a>Monitoramento de desempenho

O software de serviço do Agente DC instala um objeto do contador de desempenho denominado **Proteção por senha do Azure AD**. Atualmente, estão disponíveis os contadores de desempenho a seguir:

|Nome do contador de desempenho | DESCRIÇÃO|
| --- | --- |
|Senhas processadas |Esse contador exibe o número total de senhas processadas (aceitas ou rejeitadas) desde o último reinício.|
|Senhas aceitas |Esse contador exibe o número total de senhas aceitas desde o último reinício.|
|Senhas rejeitadas |Esse contador exibe o número total de senhas que foram rejeitadas desde o último reinício.|
|Solicitações de filtro de senha em andamento |Esse contador exibe o número de solicitações de filtro de senha atualmente em andamento.|
|Solicitações de filtro de senha de pico |Esse contador exibe o número de pico das solicitações de filtro de senha simultâneas desde o último reinício.|
|Erros de solicitação de filtro de senha |Este contador exibe o número total de solicitações de filtro de senha que falharam devido a um erro desde o último reinício. Erros podem ocorrer quando o serviço do agente DC de proteção por senha do Azure AD não estiver em execução.|
|Solicitações de filtro de senha/s |Esse contador exibe a taxa na qual as senhas estão sendo processadas.|
|Tempo de processamento de solicitação de filtro de senha |Esse contador exibe o tempo médio necessário para processar uma solicitação de filtro de senha.|
|Tempo de processamento da solicitação de filtro de senha de pico |Esse contador exibe o tempo de processamento da solicitação do filtro de senha de pico desde o último reinício.|
|Senhas aceitas devido ao modo de auditoria |Esse contador exibe o número total de senhas que normalmente teriam sido rejeitadas, mas foram aceitas porque a política de senha foi configurada para estar no modo de auditoria (desde o último reinício).|

## <a name="directory-services-repair-mode"></a>Modo de Reparo de Serviços de Diretório

Se o controlador de domínio for inicializado no Modo de Reparo de Serviços de Diretório, o serviço do agente DC detectará isso, fazendo com que todas as atividades de validação ou imposição de senha sejam desabilitadas, independentemente da configuração de política ativa no momento.

## <a name="domain-controller-demotion"></a>Rebaixamento do controlador de domínio

Há suporte para rebaixar um controlador de domínio que ainda esteja executando o software do agente DC. No entanto, os administradores devem estar cientes de que o software do agente DC mantém em execução e continua aplicando a política de senha atual durante o procedimento de rebaixamento. A nova senha da conta de Administrador local (especificada como parte da operação de rebaixamento) é validada como qualquer outra senha. A Microsoft recomenda que senhas seguras sejam escolhidas para contas de Administrador local como parte de um procedimento de rebaixamento DC, no entanto, a validação da nova senha da conta de Administrador Local pelo software do agente DC pode ser interrompido para procedimentos operacionais de rebaixamento pré-existentes.
Depois que o rebaixamento tiver ocorrido com êxito e o controlador de domínio for reiniciado e estiver em execução novamente como um servidor membro normal, o software do agente DC será revertido para execução em modo passivo. Então, poderá ser desinstalado a qualquer momento.

## <a name="removal"></a>Remoção

Se for decidido desinstalar o software de visualização pública e limpar todo o estado relacionado do(s) domínio(s) e floresta, essa tarefa poderá ser executada usando as seguintes etapas:

> [!IMPORTANT]
> É importante executar essas etapas na ordem. Se qualquer instância do serviço do Proxy de proteção por senha for deixada em execução, ele recriará periodicamente o objeto serviceConnectionPoint, bem como recriará periodicamente o estado sysvol.

1. Desinstale o software do Proxy de proteção por senha de todos os computadores. Esta etapa **não** exige um reinício.
2. Desinstale o software do Agente DC de todos os controladores de domínio. Essa etapa **exige** um reinício.
3. Remova manualmente todos os pontos de conexão do serviço de proxy em cada contexto de nomeação de domínios. O local desses objetos pode ser descoberto com o seguinte comando do PowerShell do Active Directory:
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   Não omita o asterisco (“*”) no final do valor da variável $keywords.

   O objeto resultante encontrado através do comando `Get-ADObject` pode ser redirecionado para `Remove-ADObject` ou excluído manualmente. 

4. Remova manualmente todos os pontos de conexão do agente DC em cada contexto de nomeação de domínios. Pode haver um desses objetos por controlador de domínio na floresta, dependendo de como o software de visualização pública foi implantado. O local desse objeto pode ser descoberto com o seguinte comando do PowerShell do Active Directory:

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   O objeto resultante encontrado através do comando `Get-ADObject` pode ser redirecionado para `Remove-ADObject` ou excluído manualmente.

5. Remova manualmente o estado de configuração no nível da floresta. O estado de configuração da floresta é mantido em um contêiner no contexto de nomeação de configuração do Active Directory. É possível descobrir e excluir da seguinte forma:

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Remova manualmente todo o estado relacionado ao sysvol excluindo manualmente a seguinte pasta e todo o conteúdo:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Se necessário, esse caminho também poderá ser acessado localmente em um determinado controlador de domínio e o local padrão seria semelhante ao caminho a seguir:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Esse caminho será diferente se o compartilhamento sysvol foi configurado em um local não padrão.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as listas de senhas proibidas globais e personalizadas, consulte o artigo [Proibir senhas incorretas](concept-password-ban-bad.md)
