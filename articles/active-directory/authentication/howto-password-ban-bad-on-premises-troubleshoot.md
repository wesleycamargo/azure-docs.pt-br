---
title: Solução de problemas na proteção de senha do AD do Azure - Active Directory do Azure
description: Entender o Azure AD senha proteção comuns de solução de problemas
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414759"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Solução de problemas de Proteção de Senha do Azure AD

Após a implantação da Proteção de Senha do Azure AD, a solução de problemas pode ser necessária. Este artigo apresenta detalhes para ajudá-lo a entender algumas etapas de solução de problemas comuns.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>O agente de controlador de domínio não é possível localizar um proxy no diretório

O sintoma desse problema principal é 30017 eventos no log de eventos Admin do agente de controlador de domínio.

A causa comum desse problema é que um proxy ainda não foi registrado. Se um proxy tiver sido registrado, pode haver algum atraso devido à latência de replicação do AD até que um agente de controlador de domínio específico é capaz de ver esse proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>O agente de controlador de domínio não é capaz de se comunicar com um proxy

O sintoma desse problema principal é 30018 eventos no log de eventos Admin do agente de controlador de domínio. Isso pode ter várias causas possíveis:

1. O agente de controlador de domínio está localizado na parte isolada da rede que não permite a conectividade de rede para o proxy(s) registrado. Esse problema, portanto, pode ser expected\benign desde que outros agentes de controlador de domínio podem se comunicar com o proxy(s) para baixar as políticas de senha do Azure e, em seguida, será obtido pelo controlador de domínio isolado por meio da replicação dos arquivos de política no compartilhamento de sysvol.

1. A máquina de host de proxy está bloqueando o acesso à empresa de mapeador de ponto de extremidade RPC (porta 135)

   O instalador do Proxy de proteção de senha do AD do Azure cria automaticamente uma regra de entrada do Firewall do Windows que permite o acesso à porta 135. Se essa regra mais tarde for excluída ou desabilitada, agentes de controlador de domínio será capaz de se comunicar com o serviço de Proxy. Se o Firewall do Windows builtin tiver sido desabilitado no lugar de outro produto de firewall, você deve configurar o firewall para permitir o acesso à porta 135.

1. A máquina de host de proxy está bloqueando o acesso ao ponto de extremidade RPC (dinâmico ou estático) a que escuta pelo serviço de Proxy

   O instalador do Proxy de proteção de senha do AD do Azure cria automaticamente um Firewall do Windows regra de entrada que permite o acesso a portas de entrada escuta pelo serviço de Proxy de proteção de senha do Azure AD. Se essa regra mais tarde for excluída ou desabilitada, agentes de controlador de domínio será capaz de se comunicar com o serviço de Proxy. Se o Firewall do Windows builtin tiver sido desabilitado no lugar de outro produto de firewall, você deve configurar que o firewall para permitir o acesso a portas de entrada escuta pelo serviço de Proxy de proteção de senha do Azure AD. Essa configuração pode ser feita mais específica, se o serviço de Proxy foi configurado para escutar em uma determinada porta estática de RPC (usando o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>O serviço de Proxy pode receber chamadas de agentes do controlador de domínio no domínio, mas não consegue se comunicar com o Azure

1. Verifique se o computador proxy tem conectividade com os pontos de extremidade listados na [requisitos de implantação](howto-password-ban-bad-on-premises-deploy.md).

1. Certifique-se de que a floresta e o proxy de todos os servidores são registrados no mesmo locatário do Azure.

   Você pode verificar isso executando o `Get-AzureADPasswordProtectionProxy` e `Get-AzureADPasswordProtectionDCAgent` cmdlets do PowerShell, em seguida, compare o `AzureTenant` propriedade de cada item retornado. Para a operação correta, eles devem ser o mesmo dentro de uma floresta, em todos os agentes do controlador de domínio e servidores proxy.

   Se existir uma condição de incompatibilidade de registro de locatário do Azure, isso pode ser reparado, executando o `Register-AzureADPasswordProtectionProxy` e/ou `Register-AzureADPasswordProtectionForest` cmdlets do PowerShell conforme necessário, certificando-se de usar as credenciais de locatário do Azure mesmo para todos os registros.

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>O agente de controlador de domínio não consegue criptografar ou descriptografar arquivos de política de senha e outro estado

Esse problema pode manifestar com uma variedade de sintomas, mas normalmente tem uma causa raiz comum.

A proteção de senha do AD do Azure tem uma dependência crítica da funcionalidade de criptografia e descriptografia fornecida pelo serviço de distribuição de chave da Microsoft, que está disponível nos controladores de domínio executando o Windows Server 2012 e posterior. O serviço do KDS deve estar habilitado e funcional em todos os Windows Server 2012 e posteriores controladores de domínio em um domínio.

Por padrão o KDS o modo de início do serviço do serviço é configurado como Manual (início do gatilho). Essa configuração significa que na primeira vez que um cliente tenta usar o serviço, ele é iniciado sob demanda. Este modo de inicialização de serviço padrão é aceitável para a proteção de senha do AD do Azure trabalhar.

Se o modo de início de serviço do KDS tiver sido configurado como desabilitado, essa configuração deve ser corrigida antes que a proteção por senha do AD do Azure funcionará corretamente.

Um teste simples para esse problema é iniciar manualmente o serviço do KDS, por meio do console do MMC de gerenciamento de serviço, ou usando outras ferramentas de gerenciamento de serviço (por exemplo, execute "net start kdssvc" em um console de prompt de comando). O serviço do KDS deve iniciar com êxito e permaneça em execução.

A causa mais comum para o serviço do KDS não ser capaz de iniciar é que o objeto de controlador de domínio do Active Directory está localizado fora do padrão OU controladores de domínio. Essa configuração não é compatível com o serviço do KDS e não é uma restrição imposta pela proteção de senha do Azure AD. A correção para essa condição é mover o objeto de controlador de domínio para um local sob a OU controladores de domínio padrão.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Senhas fracas estão sendo aceitas, mas não devem ser

Esse problema pode ter várias causas.

1. Seus agentes de controlador de domínio não é possível baixar uma política ou não consegue descriptografar as políticas existentes. Verifique se há possíveis causas nos tópicos acima.

1. O modo de Imposição de política de senha ainda está definido para Auditoria. Se essa configuração estiver em vigor, reconfigurá-lo a impor usando o portal de proteção de senha do Azure AD. Ver [proteção por senha habilitar](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. A política de senha foi desabilitada. Se essa configuração estiver em vigor, reconfigurá-lo habilitado usando o portal da proteção de senha do Azure AD. Ver [proteção por senha habilitar](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Você não tiver instalado o software do agente de controlador de domínio em todos os controladores de domínio no domínio. Nessa situação, é difícil garantir que os clientes remotos do Windows determinado controlador de domínio de destino durante uma operação de alteração de senha. Se você tiver achar que você direcionou um controlador de domínio específico em que o software do agente de controlador de domínio está instalado com êxito, você pode verificar conferindo o log de eventos do controlador de domínio agente admin: independentemente do resultado, haverá pelo menos um evento para documentar o resultado da senha validação. Se não houver nenhum evento presente para o usuário cuja senha é alterada, a alteração da senha provavelmente foi processada por um controlador de domínio diferente.

   Como um teste de alternativo, tente setting\changing senhas enquanto estiver conectado diretamente a um controlador de domínio em que o software do agente de controlador de domínio está instalado. Essa técnica não é recomendada para domínios do Active Directory de produção.

   Embora a implantação incremental do software agente DC tenha suporte sujeito a essas limitações, a Microsoft recomenda que o software do agente de controlador de domínio é instalado em todos os controladores de domínio em um domínio assim que possível.

1. O algoritmo de validação de senha, na verdade, pode estar funcionando conforme o esperado. Ver [como as senhas são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Modo de Reparo de Serviços de Diretório

Se o controlador de domínio é inicializado no modo de reparo de serviços de diretório, o serviço de agente de controlador de domínio detecta essa condição e fará com que todas as validação de senha ou atividades de imposição a ser desabilitada, independentemente da configuração de política ativa no momento.

## <a name="emergency-remediation"></a>Correção de emergência

Se ocorrer uma situação em que o serviço do agente DC esteja causando problemas, o serviço do agente DC poderá ser encerrado imediatamente. O dll do filtro de senha do agente DC ainda tentará chamar o serviço que não está em execução e registrará eventos de aviso (10012, 10013), mas todas as senhas de entrada serão aceitas durante esse período. O serviço do agente DC poderá, então, também ser configurado através do Gerenciador de Controle de Serviço do Windows com um tipo de inicialização “Desabilitado”, conforme necessário.

Outra medida de correção seria definir para o modo Habilitar para Não no portal de Proteção de Senha do Azure AD. Depois que a política atualizada tiver sido baixada, cada serviço do agente do DC entrará no modo inativo em que todas as senhas são aceitas no estado em que se encontram. Para mais informações, consulte [Impor o modo](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Rebaixamento do controlador de domínio

Há suporte para rebaixar um controlador de domínio que ainda esteja executando o software do agente DC. No entanto, os administradores devem estar cientes de que o software do agente do DC continua aplicando a política de senha atual durante o procedimento de rebaixamento. A nova senha da conta de Administrador local (especificada como parte da operação de rebaixamento) é validada como qualquer outra senha. A Microsoft recomenda que senhas seguras sejam escolhidas para contas de Administrador local como parte de um procedimento de rebaixamento DC, no entanto, a validação da nova senha da conta de Administrador Local pelo software do agente DC pode ser interrompido para procedimentos operacionais de rebaixamento pré-existentes.

Depois que o rebaixamento tiver ocorrido com êxito e o controlador de domínio for reiniciado e estiver em execução novamente como um servidor membro normal, o software do agente DC será revertido para execução em modo passivo. Então, poderá ser desinstalado a qualquer momento.

## <a name="removal"></a>Remoção

Se você optar por desinstalar o software de proteção de senha do AD do Azure e a limpeza do estado todas relacionado na floresta e domínio (s), essa tarefa pode ser realizada usando as seguintes etapas:

> [!IMPORTANT]
> É importante executar essas etapas na ordem. Se qualquer instância do serviço Proxy for deixada em execução, ele recriará periodicamente seu objeto serviceConnectionPoint. Se qualquer instância do serviço do agente DC for deixada em execução, ele recriará periodicamente seu objeto serviceConnectionPoint e o estado sysvol.

1. Desinstale o software do Proxy de todos os computadores. Esta etapa **não** exige um reinício.
2. Desinstale o software do Agente DC de todos os controladores de domínio. Essa etapa **exige** um reinício.
3. Remova manualmente todos os pontos de conexão do serviço Proxy em cada contexto de nomenclatura do domínio. O local desses objetos pode ser descoberto com o seguinte comando do PowerShell do Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Não omita o asterisco (“*”) no final do valor da variável $keywords.

   O (s) objeto (s) resultante (s) encontrado (s) através do comando `Get-ADObject` pode então ser canalizado para `Remove-ADObject`, ou deletado manualmente.

4. Remova manualmente todos os pontos de conexão do agente DC em cada contexto de nomeação de domínios. Pode haver um desses objetos por controlador de domínio na floresta, dependendo de quão amplamente o software foi implantado. O local desse objeto pode ser descoberto com o seguinte comando do PowerShell do Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   O (s) objeto (s) resultante (s) encontrado (s) através do comando `Get-ADObject` pode então ser canalizado para `Remove-ADObject`, ou deletado manualmente.

   Não omita o asterisco (“*”) no final do valor da variável $keywords.

5. Remova manualmente o estado de configuração no nível da floresta. O estado de configuração da floresta é mantido em um contêiner no contexto de nomeação de configuração do Active Directory. É possível descobrir e excluir da seguinte forma:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Remova manualmente todo o estado relacionado ao sysvol excluindo manualmente a seguinte pasta e todo o conteúdo:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Se necessário, esse caminho também poderá ser acessado localmente em um determinado controlador de domínio e o local padrão seria semelhante ao caminho a seguir:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Esse caminho será diferente se o compartilhamento sysvol foi configurado em um local não padrão.

## <a name="next-steps"></a>Próximas etapas

[Perguntas frequentes sobre a Proteção de Senha do Azure AD](howto-password-ban-bad-on-premises-faq.md)

Para obter mais informações sobre as listas de senhas proibidas globais e personalizadas, consulte o artigo [Proibir senhas incorretas](concept-password-ban-bad.md)
