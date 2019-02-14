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
ms.date: 01/30/2019
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9517ccac4006edec473e25c5e6524ce62d4e1259
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210095"
---
# <a name="azure-active-directory-device-management-faq"></a>Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory

**P: Registrei o dispositivo recentemente. Por que não consigo ver o dispositivo nas informações do meu usuário no portal do Azure? Ou, por que o proprietário do dispositivo está marcado como N/D para dispositivos ingressados no Azure Active Directory (Azure AD) híbrido?**

**R:** Dispositivos do Windows 10 que são ingressados no Azure AD híbrido não aparecem nos **dispositivos do usuário**.
Use o modo de exibição **Todos os dispositivos** no portal do Azure. Você também pode usar um cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) do PowerShell.

Apenas os dispositivos a seguir estão listados sob os **dispositivos do usuário**:

- Todos os dispositivos pessoais que não são ingressados no Azure AD híbrido. 
- Todos os dispositivos não Windows 10 ou Windows Server 2016.
- Todos os dispositivos não Windows. 

--- 

**P: Como saber qual é o estado de registro do dispositivo do cliente?**

**R:** No portal do Azure, acesse **Todos os dispositivos**. Procure o dispositivo usando a identificação do dispositivo. Verifique o valor na coluna de tipo de associação. Às vezes, o dispositivo pode ter sido redefinido ou recriado. Portanto, é essencial verificar também o estado do registro do dispositivo no dispositivo:

- Para dispositivos Windows 10 e Windows Server 2016 ou posterior, execute `dsregcmd.exe /status`.
- Para versões de sistema operacional de nível inferior, execute `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

**P: Eu vejo o registro do dispositivo nas informações do usuário no portal do Azure. E posso ver o estado como registrado no dispositivo. Estou configurado corretamente para usar o acesso condicional?**

**R:** O estado do ingresso do dispositivo, refletido por **deviceID**, deve corresponder ao estado no Azure AD e atender a qualquer critério de avaliação para acesso condicional. Para obter mais informações, consulte [Exigir dispositivos gerenciados para acesso ao aplicativo em nuvem com acesso condicional](../conditional-access/require-managed-devices.md).

---

**P: Excluí meu dispositivo no portal do Azure ou usando o Windows PowerShell. Mas o estado local no dispositivo diz que ele ainda está registrado.**

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

**P: Por que vejo entradas de dispositivo duplicadas no portal do Azure?**

**R:**

-   Para o Windows 10 e o Windows Server 2016, tentativas repetidas de desunir e reingressar no mesmo dispositivo podem resultar em entradas duplicadas. 

-   Cada usuário do Windows que usar **Adicionar Conta Corporativa ou de Estudante** cria um novo registro do dispositivo com o mesmo nome do dispositivo.

-   Para versões do sistema operacional do Windows de nível inferior que são ingressadas no domínio do Azure Directory local, o registro automático cria um novo registro de dispositivo com o mesmo nome do dispositivo para cada usuário de domínio que entra no dispositivo. 

-   Uma máquina conectada do Azure AD que foi limpa, reinstalada e reunida com o mesmo nome é exibida como outro registro com o mesmo nome de dispositivo.

---

**P: Por que um usuário ainda pode acessar recursos de um dispositivo que eu desabilitei no portal do Azure?**

**R:** Demora até uma hora para uma revogação ser aplicada.

>[!NOTE] 
>Para dispositivos registrados, é recomendável apagar o dispositivo para garantir que os usuários não possam acessar os recursos. Para obter mais informações, confira [O que é o registro de dispositivo?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Perguntas frequentes sobre ingresso no Azure AD

**P: Como cancelar a associação de um dispositivo ingressado no Azure AD localmente no dispositivo?**

**R:** 
- Para dispositivos ingressados no Azure AD híbrido, certifique-se de desativar o registro automático. A tarefa agendada não registrará o dispositivo novamente. Agora, abra um prompt de comando como administrador e digite `dsregcmd.exe /debug /leave`. Ou execute este comando como um script em vários dispositivos para fazer um cancelamento de associação em massa.

- Para dispositivos Azure AD simplesmente associados, verifique se você tem uma conta de administrador local offline ou crie uma. Você não pode entrar com as credenciais de usuário do Azure AD. Em seguida, acesse **Configurações** > **Contas** > **Acessar corporativo ou de estudante**. Selecione sua conta e, em seguida, **Desconectar**. Siga os prompts e forneça as credenciais de administrador local, quando solicitado. Reinicie o dispositivo para concluir o processo de cancelar a associação.

---

**P: Meus usuários podem entrar nos dispositivos ingressados que foram excluídos ou desabilitados no Azure AD?**

**R:** Sim. O Windows tem uma funcionalidade de armazenamento em cache de nome de usuário e senha que permite aos usuários que se conectaram anteriormente acessarem a área de trabalho rapidamente, mesmo sem conectividade de rede. 

Quando um dispositivo é excluído ou desabilitado no Azure AD, ele não é reconhecido pelo dispositivo do Windows. Portanto, os usuários que se conectaram anteriormente continuam a acessar a área de trabalho com o nome de usuário e a senha armazenados em cache. No entanto, assim que o dispositivo for excluído ou desabilitado, os usuários não poderão acessar nenhum recurso protegido pelo acesso condicional com base em dispositivo. 

Os usuários que não se conectaram anteriormente não podem acessar o dispositivo. Não há nenhum nome de usuário e senha armazenados em cache habilitados para eles. 

---

**P: Usuários desabilitados ou excluídos podem entrar nos dispositivos ingressados no Azure AD?**

**R:** Sim, mas apenas por um período limitado. Quando um usuário é excluído ou desabilitado no Azure AD, ele não é imediatamente conhecido para o dispositivo do Windows. Portanto, os usuários que se conectaram anteriormente podem acessar a área de trabalho com o nome de usuário e a senha armazenados em cache. 

Normalmente, o dispositivo está ciente do estado do usuário em menos de quatro horas. Em seguida, o Windows bloqueia o acesso desses usuários à área de trabalho. Como o usuário é excluído ou desabilitado no Azure AD, todos os seus tokens são revogados. Portanto, eles não podem acessar nenhum recurso. 

Os usuários excluídos ou desabilitados que não se conectaram anteriormente não podem acessar um dispositivo. Não há nenhum nome de usuário e senha armazenados em cache habilitados para eles. 

---

**P: Por que meus usuários têm problemas em dispositivos ingressados no Azure AD depois de alterarem o UPN?**

**R:** Atualmente, as alterações de UPN não têm suporte total em dispositivos ingressados no Azure AD. Portanto, a autenticação com o Azure AD falha após o UPN mudar. Como resultado, os usuários têm problemas de SSO e Acesso Condicional em seus dispositivos. Neste momento, os usuários precisam entrar no Windows por meio do bloco "Outro usuário" usando o novo UPN para resolver esse problema. Estamos trabalhando para solucionar esse problema. No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema. 

---

**P: Meus usuários não conseguem procurar impressoras em dispositivos ingressados no Azure AD. Como habilitar a impressão usando esses dispositivos?**

**R:** Para implantar impressoras para dispositivos ingressados no Azure AD, confira [Implantar nuvem híbrida do Windows Server com pré-autenticação](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Você precisa de um Windows Server local para implantar a impressão de nuvem híbrida. Atualmente, o serviço de impressão baseado em nuvem não está disponível. 

---

**P: Como fazer para conectar um dispositivo remoto ingressado Azure AD?**

**R:** Confira [Conectar-se ao computador remoto ingressado no Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

**P: Por que meus usuários veem *Você não pode chegar lá daqui*?**

**R:** Você configurou regras de acesso condicional específicas para exigir um estado de dispositivo específico? Se o dispositivo não atender aos critérios, os usuários são bloqueados e veem essa mensagem. Avalie as regras de política de acesso condicional. Verifique se o dispositivo atende aos critérios para evitar a mensagem.

---

**P: Por que os prompts de autenticação multifator do Azure não são exibidos para alguns dos meus usuários em dispositivos ingressados no Azure AD?**

**R:** Um usuário pode ingressar ou registrar um dispositivo com o Azure AD usando a autenticação multifator. Em seguida, o próprio dispositivo torna-se um segundo fator confiável para esse usuário. Sempre que o mesmo usuário faz logon no dispositivo e acessa um aplicativo, o Azure AD considera o dispositivo como um segundo fator. Ele permite que o usuário acesse diretamente os aplicativos sem prompts de autenticação multifator adicionais. 

Esse comportamento não é aplicável a nenhum outro usuário que se conecte a esse dispositivo. Portanto, todos os outros usuários que acessam esse dispositivo serão apresentados a um desafio de autenticação multifator. Só então eles poderão acessar os aplicativos que exigem a autenticação multifator.

---

**P: Por que recebo uma mensagem de *nome de usuário ou senha incorreta* para um dispositivo que acabei de ingressar no Azure AD?**

**R:** As razões comuns para esse cenário são as seguintes:

- Suas credenciais de usuário não são mais válidas.

- O computador não consegue se comunicar com o Azure Active Directory. Verifique se há problemas de conectividade de rede.

- Credenciais federadas requerem que o servidor de federação dê suporte a pontos de extremidade do WS-Trust que estejam habilitados e acessíveis. 

- Você habilitou a autenticação de passagem. Portanto, sua senha temporária precisa ser alterada quando você entrar.

---

**P: Por que vejo a caixa de diálogo *Ocorreu um erro!* quando tento fazer o Azure AD ingressar no meu computador?**

**R:** Esse erro ocorre quando o registro do Azure Active Directory é configurado com o Intune. Certifique-se de que o usuário que tenta fazer o ingresso do Azure AD tenha a licença correta do Intune atribuída. Para obter mais informações, confira [Configurar registro para dispositivos Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

**P: Por que minha tentativa de ingressar no Azure AD em um computador falhou, embora eu não tenha recebido nenhuma informação de erro?**

**R:** Uma causa provável é que você se conectou ao dispositivo usando a conta de administrador interno local. Crie uma conta local diferente antes de usar o ingresso do Azure Active Directory para concluir a configuração. 

---

**P: Quais são os certificados de MS-Organization-P2P-Access presentes em nossos dispositivos Windows 10?**

**R:** Os certificados MS-Organization-P2P-Access são emitidos pelo Azure AD para os dispositivos ingressados do Azure AD e do Azure AD híbrido. Esses certificados são usados para habilitar a confiança entre os dispositivos no mesmo locatário para cenários de área de trabalho remotos. Um certificado é emitido para o dispositivo e o outro é emitido para o usuário. O certificado do dispositivo está presente no `Local Computer\Personal\Certificates` e é válido por um dia. Esse certificado é renovado (emitindo um novo certificado) se o dispositivo ainda estiver ativo no Azure AD. O certificado de usuário está presente no `Current User\Personal\Certificates` e esse certificado também é válido por um dia, mas é emitido sob demanda quando um usuário tenta uma sessão de área de trabalho remota para outro dispositivo ingressado do Azure AD. Ele não é renovado após a expiração. Esses dois certificados são emitidos usando o certificado MS-Organization-P2P-Access presente no `Local Computer\AAD Token Issuer\Certificates`. Esse certificado é emitido pelo Azure AD durante o registro do dispositivo. 

---

**P: Por que vejo vários certificados expirados emitidos pelo MS-Organization-P2P-Access em nossos dispositivos Windows 10? Como exclui-los?**

**R:** Houve um problema identificado no Windows 10 versão 1709 e inferior em que certificados MS-Organization-P2P-Access expirados continuaram a existir no repositório do computador devido a problemas de criptografia. Os usuários podem enfrentar problemas com conectividade de rede, se você estiver usando qualquer tipo de cliente VPN (por exemplo, o Cisco AnyConnect) que não consegue manipular um grande número de certificados expirados. Esse problema foi corrigido no Windows 10 versão 1803 para excluir automaticamente esses certificados MS-Organization-P2P-Access expirados. Você pode resolver esse problema atualizando seus dispositivos para o Windows 10 versão 1803. Se você não conseguir atualizar, você pode excluir esses certificados sem que seja causado nenhum impacto negativo.  

---


## <a name="hybrid-azure-ad-join-faq"></a>Perguntas frequentes sobre ingresso no Azure AD Híbrido

**P: Onde posso encontrar informações sobre solução de problemas para diagnosticar falhas de ingresso no Azure AD híbrido?**

**R:** Para encontrar informações sobre soluções de problemas, consulte os seguintes artigos:

- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)

- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)
 
**P: Por que vejo um registro do Azure Active Directory duplicado para meu dispositivo incluído pelo Azure Active Directory híbrido no Windows 10 na lista de dispositivos do Azure Active Directory?**

**R:** Quando os usuários adicionam a respectiva conta aos aplicativos em um dispositivo incluído no domínio, eles podem ser solicitados a **Adicionar a conta ao Windows?** Se o usuário escolher **Sim** no prompt, o dispositivo registra com o Azure AD. O tipo de relação de confiança é marcado como registrado no Azure AD. Depois de habilitar o ingresso do Azure AD híbrido na organização, o dispositivo também será incluído no Azure AD híbrido. Em seguida, dois estados de dispositivo aparecem para o mesmo dispositivo. 

O ingresso do Azure AD híbrido tem precedência sobre o estado de registrado pelo Azure AD. Desse modo, o dispositivo é considerado ingressado do Azure AD híbrido para qualquer autenticação e avaliação de acesso condicional. Você pode excluir com segurança o registro do dispositivo registrado pelo Azure AD no portal do Azure AD. Saiba como [evitar ou limpar esse estado duplo no computador com Windows 10](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 


---

**P: Por que meus usuários têm problemas em dispositivos ingressados no Azure AD híbrido do Windows 10 depois de alterarem o UPN?**

**R:** No momento, as alterações de UPN não têm suporte total em dispositivos ingressados no Azure AD híbrido. Embora os usuários possam entrar dispositivo e acessar seus aplicativos locais, a autenticação com o Azure AD falhará após a alteração de um UPN. Como resultado, os usuários têm problemas de SSO e Acesso Condicional em seus dispositivos. Neste momento, você precisa cancelar o ingresso do dispositivo no Azure AD (execute "dsregcmd /leave" com privilégios elevados) e ingressar novamente (ocorre automaticamente) para resolver o problema. Estamos trabalhando para solucionar esse problema. No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema. 

---

**P: Os dispositivos ingressados no Azure AD Híbrido do Windows 10 exigem linha de visão com o controlador de domínio para obter acesso a recursos de nuvem?**

**R:**  Não. Após a conclusão do ingresso no Azure AD Híbrido do Windows 10, quando o usuário tiver se conectado pelo menos uma vez, o dispositivo não exigirá linha de visão com o controlador de domínio para acessar os recursos de nuvem. O Windows 10 pode ter logon único para aplicativos do Azure AD em qualquer lugar onde haja uma conexão com a Internet, exceto em caso de alteração de senha. Se uma senha for alterada fora da rede corporativa (por exemplo, usando o Azure AD SSPR), o usuário precisará ter linha de visão com o controlador de domínio antes de poder se conectar ao dispositivo com a nova senha. Caso contrário, ele só poderá entrar com a senha antiga, que é invalidada pelo Azure AD e impede o logon único. No entanto, esse problema não ocorre quando você usa o Windows Hello para Empresas. Os usuários que entram com o Windows Hello para Empresas continuam a ter logon único para aplicativos do Azure AD após uma alteração de senha, mesmo se não tiverem linha de visão com o controlador de domínio. 

---


## <a name="azure-ad-register-faq"></a>Perguntas frequentes sobre o registro do Azure AD

**P: É possível registrar dispositivos Android ou iOS BYOD?**

**R:** Sim, mas apenas com o serviço de registro de dispositivos do Azure e para clientes híbridos. Ele não é compatível com o serviço de registro de dispositivo local nos Serviços de Federação do Active Directory (AD FS).

**P: Como posso registrar um dispositivo macOS?**

**R:** Siga estas etapas:

1.  [Criar uma política de conformidade](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definir uma política de acesso condicional para dispositivos macOS](../active-directory-conditional-access-azure-portal.md) 

**Comentários:**

- Os usuários incluídos em sua política de acesso condicional precisam de uma [versão com suporte do Office para macOS](../conditional-access/technical-reference.md#client-apps-condition) para acessar os recursos. 

- Durante a primeira tentativa de acesso, os usuários são solicitados a registrar o dispositivo usando o portal da empresa.

