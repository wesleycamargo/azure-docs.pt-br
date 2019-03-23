---
title: Perguntas frequentes sobre o gerenciamento de dispositivo do Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f41e18b0ab546da87ea7a4a6d53bad370fefe670
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351738"
---
# <a name="azure-active-directory-device-management-faq"></a>Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory

###<a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>P: Registrei o dispositivo recentemente. Por que não consigo ver o dispositivo nas informações do meu usuário no portal do Azure? Ou, por que é o proprietário do dispositivo marcado como n/a para o Azure Active Directory (Azure AD) híbrido ingressado em dispositivos?

**R:** Dispositivos do Windows 10 que são ingressados no Azure AD híbrido não aparecem nos **dispositivos do usuário**.
Use o modo de exibição **Todos os dispositivos** no portal do Azure. Você também pode usar um cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) do PowerShell.

Apenas os dispositivos a seguir estão listados sob os **dispositivos do usuário**:

- Todos os dispositivos pessoais que não são ingressados no Azure AD híbrido. 
- Todos os dispositivos não Windows 10 ou Windows Server 2016.
- Todos os dispositivos não Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>P: Como saber o que é o estado de registro de dispositivo do cliente?

**R:** No portal do Azure, acesse **Todos os dispositivos**. Procure o dispositivo usando a identificação do dispositivo. Verifique o valor na coluna de tipo de associação. Às vezes, o dispositivo pode ter sido redefinido ou recriado. Portanto, é essencial verificar também o estado do registro do dispositivo no dispositivo:

- Para dispositivos Windows 10 e Windows Server 2016 ou posterior, execute `dsregcmd.exe /status`.
- Para versões de sistema operacional de nível inferior, execute `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>P: Eu vejo o registro do dispositivo nas informações do usuário no portal do Azure. E posso ver o estado como registrado no dispositivo. Estou configurar corretamente para usar o acesso condicional?

**R:** O estado do ingresso do dispositivo, refletido por **deviceID**, deve corresponder ao estado no Azure AD e atender a qualquer critério de avaliação para acesso condicional. Para obter mais informações, consulte [Exigir dispositivos gerenciados para acesso ao aplicativo em nuvem com acesso condicional](../conditional-access/require-managed-devices.md).

---

### <a name="q-i-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered"></a>P: Excluí meu dispositivo no portal do Azure ou usando o Windows PowerShell. Mas o estado local no dispositivo diz que ainda está registrado.

**R:** Essa operação ocorre por design. O dispositivo não possui acesso aos recursos na nuvem. 

Se você quiser registrar-se novamente, uma ação manual deverá ser executada no dispositivo. 

Para limpar o estado do ingresso do Windows 10 e do Windows Server 2016 que são ingressados no domínio do Active Directory local, execute as seguintes etapas:

1.  Abra o prompt de comando como administrador.

2.  Digite `dsregcmd.exe /debug /leave`.

3.  Saia e entre para disparar a tarefa agendada que registra o dispositivo com o Azure AD novamente. 

Para versões de sistema operacional do Windows de nível inferior que ingressaram no domínio do Active Directory local, execute as seguintes etapas:

1.  Abra o prompt de comando como administrador.
2.  Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>P: Por que vejo entradas de dispositivo duplicadas no portal do Azure?

**R:**

-   Para o Windows 10 e o Windows Server 2016, tentativas repetidas de desunir e reingressar no mesmo dispositivo podem resultar em entradas duplicadas. 

-   Cada usuário do Windows que usar **Adicionar Conta Corporativa ou de Estudante** cria um novo registro do dispositivo com o mesmo nome do dispositivo.

-   Para versões do sistema operacional do Windows de nível inferior que são ingressadas no domínio do Azure Directory local, o registro automático cria um novo registro de dispositivo com o mesmo nome do dispositivo para cada usuário de domínio que entra no dispositivo. 

-   Uma máquina conectada do Azure AD que foi limpa, reinstalada e reunida com o mesmo nome é exibida como outro registro com o mesmo nome de dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>P: O registro de dispositivos Windows 10 no Azure AD dá suporte à TPMs no modo FIPS?

**R:** Não, atualmente, o registro de dispositivos no Windows 10 para todos os estados de dispositivo - junção do Azure AD híbrido, ingresso no Azure AD e registrados no Azure AD - não suporta os TPMs no modo FIPS. Para ingressar ou registre-se ao AD do Azure com êxito, o modo FIPS precisa ser desativado para os TPMs nesses dispositivos

---

**P: Por que um usuário ainda pode acessar recursos de um dispositivo que eu desabilitei no portal do Azure?**

**R:** Demora até uma hora para uma revogação ser aplicada.

>[!NOTE] 
>Para dispositivos registrados, é recomendável apagar o dispositivo para garantir que os usuários não possam acessar os recursos. Para obter mais informações, confira [O que é o registro de dispositivo?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Perguntas frequentes sobre ingresso no Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>P: Como eu sair um dispositivo ingressado do AD do Azure localmente no dispositivo?

**R:** 
- Para dispositivos ingressados no Azure AD híbrido, certifique-se de desativar o registro automático. A tarefa agendada não registrará o dispositivo novamente. Agora, abra um prompt de comando como administrador e digite `dsregcmd.exe /debug /leave`. Ou execute este comando como um script em vários dispositivos para fazer um cancelamento de associação em massa.

- Para dispositivos Azure AD simplesmente associados, verifique se você tem uma conta de administrador local offline ou crie uma. Você não pode entrar com as credenciais de usuário do Azure AD. Em seguida, acesse **Configurações** > **Contas** > **Acessar corporativo ou de estudante**. Selecione sua conta e, em seguida, **Desconectar**. Siga os prompts e forneça as credenciais de administrador local, quando solicitado. Reinicie o dispositivo para concluir o processo de cancelar a associação.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>P: Meus usuários podem entrar para dispositivos ingressados no Azure AD que são excluídos ou desabilitados no Azure AD?

**R:** Sim. O Windows tem uma funcionalidade de armazenamento em cache de nome de usuário e senha que permite aos usuários que se conectaram anteriormente acessarem a área de trabalho rapidamente, mesmo sem conectividade de rede. 

Quando um dispositivo é excluído ou desabilitado no Azure AD, ele não é reconhecido pelo dispositivo do Windows. Portanto, os usuários que se conectaram anteriormente continuam a acessar a área de trabalho com o nome de usuário e a senha armazenados em cache. No entanto, assim que o dispositivo for excluído ou desabilitado, os usuários não poderão acessar nenhum recurso protegido pelo acesso condicional com base em dispositivo. 

Os usuários que não se conectaram anteriormente não podem acessar o dispositivo. Não há nenhum nome de usuário e senha armazenados em cache habilitados para eles. 

---

### <a name="q-can-disabled-or-deleted-users-sign-in-to-azure-ad-joined-devices"></a>P: Os usuários desabilitados ou excluídos podem entrar em dispositivos ingressados no Azure AD?

**R:** Sim, mas apenas por um período limitado. Quando um usuário é excluído ou desabilitado no Azure AD, ele não é imediatamente conhecido para o dispositivo do Windows. Portanto, os usuários que se conectaram anteriormente podem acessar a área de trabalho com o nome de usuário e a senha armazenados em cache. 

Normalmente, o dispositivo está ciente do estado do usuário em menos de quatro horas. Em seguida, o Windows bloqueia o acesso desses usuários à área de trabalho. Como o usuário é excluído ou desabilitado no Azure AD, todos os seus tokens são revogados. Portanto, eles não podem acessar nenhum recurso. 

Os usuários excluídos ou desabilitados que não se conectaram anteriormente não podem acessar um dispositivo. Não há nenhum nome de usuário e senha armazenados em cache habilitados para eles. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>P: Por que meus usuários têm problemas em dispositivos ingressados no Azure AD depois de alterar seu UPN?

**R:** Atualmente, as alterações de UPN não têm suporte total em dispositivos ingressados no Azure AD. Portanto, a autenticação com o Azure AD falha após o UPN mudar. Como resultado, os usuários têm problemas de SSO e Acesso Condicional em seus dispositivos. Neste momento, os usuários precisam entrar no Windows por meio do bloco "Outro usuário" usando o novo UPN para resolver esse problema. Estamos trabalhando para solucionar esse problema. No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>P: Meus usuários não conseguem procurar impressoras em dispositivos ingressados no Azure AD. Como habilitar a impressão a partir desses dispositivos?

**R:** Para implantar impressoras para dispositivos ingressados no Azure AD, confira [Implantar nuvem híbrida do Windows Server com pré-autenticação](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Você precisa de um Windows Server local para implantar a impressão de nuvem híbrida. Atualmente, o serviço de impressão baseado em nuvem não está disponível. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>P: Como me conecto a um remoto do Azure AD dispositivo ingressado no?

**R:** Confira [Conectar-se ao computador remoto ingressado no Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>P: Por que meus usuários veem *você não pode ir daqui até lá*?

**R:** Você configurou regras de acesso condicional específicas para exigir um estado de dispositivo específico? Se o dispositivo não atender aos critérios, os usuários são bloqueados e veem essa mensagem. Avalie as regras de política de acesso condicional. Verifique se o dispositivo atende aos critérios para evitar a mensagem.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>P: Por que alguns dos meus usuários não obtenham prompts de autenticação multifator do Azure em dispositivos ingressados no Azure AD?

**R:** Um usuário pode ingressar ou registrar um dispositivo com o Azure AD usando a autenticação multifator. Em seguida, o próprio dispositivo torna-se um segundo fator confiável para esse usuário. Sempre que o mesmo usuário faz logon no dispositivo e acessa um aplicativo, o Azure AD considera o dispositivo como um segundo fator. Ele permite que o usuário acesse diretamente os aplicativos sem prompts de autenticação multifator adicionais. 

Esse comportamento:

- É aplicável aos dispositivos associados do Azure AD e aos dispositivos registrados do Azure AD, mas não aos dispositivos associados híbridos do Azure AD.

- Não é aplicável a qualquer outro usuário que se conecte a esse dispositivo. Portanto, todos os outros usuários que acessam esse dispositivo serão apresentados a um desafio de autenticação multifator. Só então eles poderão acessar os aplicativos que exigem a autenticação multifator.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>P: Por que recebo uma *nome de usuário ou senha está incorreta* mensagem para um dispositivo que eu acabei de ingressar no Azure AD?

**R:** As razões comuns para esse cenário são as seguintes:

- Suas credenciais de usuário não são mais válidas.

- O computador não consegue se comunicar com o Azure Active Directory. Verifique se há problemas de conectividade de rede.

- Credenciais federadas requerem que o servidor de federação dê suporte a pontos de extremidade do WS-Trust que estejam habilitados e acessíveis. 

- Você habilitou a autenticação de passagem. Portanto, sua senha temporária precisa ser alterada quando você entrar.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>P: Por que vejo o *Opa... Ocorreu um erro!* caixa de diálogo quando tento ao Azure AD ingressar no meu computador?

**R:** Esse erro ocorre quando o registro do Azure Active Directory é configurado com o Intune. Certifique-se de que o usuário que tenta fazer o ingresso do Azure AD tenha a licença correta do Intune atribuída. Para obter mais informações, confira [Configurar registro para dispositivos Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>P: Por que minha tentativa para o Azure AD ingressar um falha de PC, embora eu não tenha obtido nenhuma informação de erro?

**R:** Uma causa provável é que você se conectou ao dispositivo usando a conta de administrador interno local. Crie uma conta local diferente antes de usar o ingresso do Azure Active Directory para concluir a configuração. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>P: quais são os certificados de organização MS-Access P2P presentes em nossos dispositivos Windows 10?

**R:** Os certificados MS-Organization-P2P-Access são emitidos pelo Azure AD para os dispositivos ingressados do Azure AD e do Azure AD híbrido. Esses certificados são usados para habilitar a confiança entre os dispositivos no mesmo locatário para cenários de área de trabalho remotos. Um certificado é emitido para o dispositivo e o outro é emitido para o usuário. O certificado do dispositivo está presente no `Local Computer\Personal\Certificates` e é válido por um dia. Esse certificado é renovado (emitindo um novo certificado) se o dispositivo ainda estiver ativo no Azure AD. O certificado de usuário está presente no `Current User\Personal\Certificates` e esse certificado também é válido por um dia, mas é emitido sob demanda quando um usuário tenta uma sessão de área de trabalho remota para outro dispositivo ingressado do Azure AD. Ele não é renovado após a expiração. Esses dois certificados são emitidos usando o certificado MS-Organization-P2P-Access presente no `Local Computer\AAD Token Issuer\Certificates`. Esse certificado é emitido pelo Azure AD durante o registro do dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why vejo vários certificados expirados emitidos pelo MS-organização P2P do acesso em nossos dispositivos Windows 10? Como exclui-los?

**R:** Houve um problema identificado no Windows 10 versão 1709 e inferior em que certificados MS-Organization-P2P-Access expirados continuaram a existir no repositório do computador devido a problemas de criptografia. Os usuários podem enfrentar problemas com conectividade de rede, se você estiver usando qualquer tipo de cliente VPN (por exemplo, o Cisco AnyConnect) que não consegue manipular um grande número de certificados expirados. Esse problema foi corrigido no Windows 10 versão 1803 para excluir automaticamente esses certificados MS-Organization-P2P-Access expirados. Você pode resolver esse problema atualizando seus dispositivos para o Windows 10 versão 1803. Se você não conseguir atualizar, você pode excluir esses certificados sem que seja causado nenhum impacto negativo.  

---


## <a name="hybrid-azure-ad-join-faq"></a>Perguntas frequentes sobre ingresso no Azure AD Híbrido

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>P: Onde encontrar a solução de problemas de informações para diagnosticar falhas de junção do Azure AD híbrido?

**R:** Para encontrar informações sobre soluções de problemas, consulte os seguintes artigos:

- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)

- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>P: Por que vejo um Azure AD duplicado registro registrado para meu Azure AD híbrido de Windows 10 ingressados no dispositivo na lista de dispositivos do Azure AD?

**R:** Quando os usuários adicionam a respectiva conta aos aplicativos em um dispositivo incluído no domínio, eles podem ser solicitados a **Adicionar a conta ao Windows?** Se o usuário escolher **Sim** no prompt, o dispositivo registra com o Azure AD. O tipo de relação de confiança é marcado como registrado no Azure AD. Depois de habilitar o ingresso do Azure AD híbrido na organização, o dispositivo também será incluído no Azure AD híbrido. Em seguida, dois estados de dispositivo aparecem para o mesmo dispositivo. 

O ingresso do Azure AD híbrido tem precedência sobre o estado de registrado pelo Azure AD. Desse modo, o dispositivo é considerado ingressado do Azure AD híbrido para qualquer autenticação e avaliação de acesso condicional. Você pode excluir com segurança o registro do dispositivo registrado pelo Azure AD no portal do Azure AD. Saiba como [evitar ou limpar esse estado duplo no computador com Windows 10](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 


---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>P: Por que meus usuários têm problemas em dispositivos do Windows 10 híbridos adicionados ao Azure AD depois de alterar seu UPN?

**R:** No momento, as alterações de UPN não têm suporte total em dispositivos ingressados no Azure AD híbrido. Embora os usuários possam entrar dispositivo e acessar seus aplicativos locais, a autenticação com o Azure AD falhará após a alteração de um UPN. Como resultado, os usuários têm problemas de SSO e Acesso Condicional em seus dispositivos. Neste momento, você precisa cancelar o ingresso do dispositivo no Azure AD (execute "dsregcmd /leave" com privilégios elevados) e ingressar novamente (ocorre automaticamente) para resolver o problema. Estamos trabalhando para solucionar esse problema. No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>P: Dispositivos adicionados ao Azure AD do Windows 10 híbridos exigem a linha de visão para o controlador de domínio para obter acesso aos recursos de nuvem?

**R:** Geralmente, não, exceto quando a senha do usuário é alterada. Após a conclusão do ingresso no Azure AD Híbrido do Windows 10, quando o usuário tiver se conectado pelo menos uma vez, o dispositivo não exigirá linha de visão com o controlador de domínio para acessar os recursos de nuvem. O Windows 10 pode ter logon único para aplicativos do Azure AD em qualquer lugar onde haja uma conexão com a Internet, exceto em caso de alteração de senha. Os usuários que entram com o Windows Hello para empresas continuar a obter um único logon para aplicativos do Azure AD mesmo depois que uma alteração de senha, mesmo se eles não têm uma linha de visão para o controlador de domínio. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>P: O que acontece se um usuário altera sua senha e tenta efetuar logon no seu Azure AD híbrido de Windows 10 ingressados no dispositivo fora da rede corporativa?

**R:** Se uma senha for alterada fora da rede corporativa (por exemplo, usando o Azure AD SSPR), o logon do usuário com a nova senha falhará. Para dispositivos de ingressados no Azure AD híbrido, do Active Directory local é a autoridade principal. Quando um dispositivo não tem linha de visão para o controlador de domínio, é não é possível validar a nova senha. Portanto, o usuário precisa estabelecer conexão com o controlador de domínio (seja via VPN ou sendo na rede corporativa) antes que elas possam entrar para o dispositivo com sua nova senha. Caso contrário, eles só podem entrar com a senha antiga devido à funcionalidade de logon armazenado em cache no Windows. No entanto, a senha antiga é invalidada pelo AD do Azure durante solicitações de token e, portanto, impede que o logon único em e falha quaisquer políticas de acesso condicional com base no dispositivo. Esse problema não ocorre se você usar o Windows Hello para empresas. 

---


## <a name="azure-ad-register-faq"></a>Perguntas frequentes sobre o registro do Azure AD

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>P: Posso registrar os dispositivos BYOD iOS ou Android?

**R:** Sim, mas apenas com o serviço de registro de dispositivos do Azure e para clientes híbridos. Ele não é compatível com o serviço de registro de dispositivo local nos Serviços de Federação do Active Directory (AD FS).

### <a name="q-how-can-i-register-a-macos-device"></a>P: Como registrar um dispositivo macOS?

**R:** Siga estas etapas:

1.  [Criar uma política de conformidade](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definir uma política de acesso condicional para dispositivos macOS](../active-directory-conditional-access-azure-portal.md) 

**Comentários:**

- Os usuários incluídos em sua política de acesso condicional precisam de uma [versão com suporte do Office para macOS](../conditional-access/technical-reference.md#client-apps-condition) para acessar os recursos. 

- Durante a primeira tentativa de acesso, os usuários são solicitados a registrar o dispositivo usando o portal da empresa.

