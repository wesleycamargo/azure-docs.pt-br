---
title: Perguntas frequentes sobre o gerenciamento de dispositivo do Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: e6d223b386aea3ca41931c600d16c16b53765f26
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415679"
---
# <a name="azure-active-directory-device-management-faq"></a>Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory

**P: É possível registrar dispositivos Android ou iOS BYOD?**

**R:** Sim, mas apenas com o serviço de registro de dispositivos do Azure e para clientes híbridos. Não há suporte a esses dispositivos com serviço de registro de dispositivo local no AD FS.

**P: Como posso registrar um dispositivo macOS?**

**R:** Para registrar um dispositivo macOS:

1.  [Criar uma política de conformidade](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definir uma política de acesso condicional para dispositivos macOS](../active-directory-conditional-access-azure-portal.md) 

**Comentários:**

- Os usuários que estão incluídos em sua política de acesso condicional precisam de uma [versão com suporte do Office para macOS](../active-directory-conditional-access-technical-reference.md#client-apps-condition) para acessar os recursos. 

- Durante a primeira tentativa de acesso, os usuários são solicitados a registrar o dispositivo usando o portal da empresa.

---

**P: Registrei o dispositivo recentemente. Por que não consigo ver o dispositivo em minhas informações de usuário no Portal do Azure?**

**R:** Dispositivos do Windows 10 que são ingressados no Azure AD Híbrido não aparecem nos dispositivos USER.
Você precisa usar o modo de exibição Todos os dispositivos no portal do Azure. Você também pode usar o cmdlet do PowerShell [MsolDevice Get](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0).

Apenas os dispositivos a seguir estão listados sob os dispositivos USER:

- Todos os dispositivos pessoais que não são ingressados no Azure AD Híbrido. 
- Todos os dispositivos não Windows 10/Windows Server 2016.
- Todos os dispositivos não Windows 

--- 

**P: Como saber qual é o estado de registro do dispositivo do cliente?**

**R:** você pode usar o portal do Azure. Vá para Todos os dispositivos e pesquise o dispositivo usando a respectiva ID. Verifique o valor na coluna de tipo de associação.

Se você desejar verificar o estado do registro de dispositivo local de um dispositivo registrado:

- Para dispositivos Windows 10 e Windows Server 2016 ou posterior, execute dsregcmd.exe /status.
- Para versões do sistema operacional de nível inferior, execute "%programFiles%\Microsoft Workplace Join\autoworkplace.exe"

---

**P: excluí no portal do Azure ou usando o Windows PowerShell, mas o estado local no dispositivo diz que ele ainda está registrado?**

**R:** Esse comportamento é intencional. O dispositivo não terá acesso aos recursos na nuvem. 

Se você quiser registrar-se novamente, uma ação manual deverá ser executada no dispositivo. 

Para limpar o estado do ingresso do Windows 10 e do Windows Server 2016 que são ingressados no domínio do AD local:

1.  Abra o prompt de comando como administrador.

2.  Digite `dsregcmd.exe /debug /leave`

3.  Saia e entre para disparar a tarefa agendada que registra o dispositivo com o Microsoft Azure AD novamente. 

Para versões do sistema operacional do Windows de nível inferior que são ingressadas no domínio do AD local:

1.  Abra o prompt de comando como administrador.
2.  Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---
**P: Como cancelar a associação de um dispositivo ingressado no Azure AD localmente no dispositivo?**

**R:** 
- Para dispositivos associados do Azure AD híbrido, desative o registro automático para que a tarefa agendada não registre o dispositivo novamente. Agora, abra um prompt de comando como Administrador e digite `dsregcmd.exe /debug /leave`. Como alternativa, este comando pode ser executado como um script em vários dispositivos para cancelar a associação em massa.

- Para dispositivos Azure AD simplesmente associados, verifique se você tem uma conta de administrador local offline ou crie uma, pois você não poderá entrar com as credenciais de usuário do Azure AD. Em seguida, acesse **Configurações** > **Contas** > **Acessar corporativo ou de estudante**. Selecione sua conta e clique em **Desconectar**. Siga os prompts e forneça as credenciais de administrador local, quando solicitado. Reinicie o dispositivo para concluir o processo de cancelar a associação.

---

**P: Meus usuários não conseguem procurar impressoras em dispositivos ingressados no Azure AD. Como habilitar a impressão usando dispositivos ingressados no Azure AD?**

**R:** Para implantar impressoras para dispositivos ingressados no Azure AD, confira [Hybrid cloud print](https://docs.microsoft.com/en-us/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) (Impressão de nuvem híbrida). Você precisará de um Windows Server local para implantar a impressão de nuvem híbrida. Atualmente, o serviço de impressão baseado em nuvem não está disponível. 

---

**P: Como fazer para conectar um dispositivo associado do Azure AD remoto?**
**R:** Consulte o artigo https://docs.microsoft.com/en-us/windows/client-management/connect-to-remote-aadj-pc para obter mais detalhes.

---

**P: Por que vejo entradas de dispositivo duplicadas no Portal do Azure?**

**R:**

-   Para o Windows 10 e o Windows Server 2016, se houver tentativas repetidas de desunir e reingressar no mesmo dispositivo, pode haver entradas duplicadas. 

-   Se você tiver usado Adicionar Conta Corporativa ou de Estudante, cada usuário do Windows que usar Adicionar Conta Corporativa ou de Estudante criará um novo registro do dispositivo com o mesmo nome do dispositivo.

-   Para versões do sistema operacional do Windows de nível inferior que são ingressadas no domínio do AD local usando o registro automático, será criado um novo registro de dispositivo com o mesmo nome do dispositivo para cada usuário de domínio que fizer logon no dispositivo. 

-   Uma máquina conectada do Azure AD que foi limpa, reinstalada e reunida com o mesmo nome será exibida como outro registro com o mesmo nome de dispositivo.

---

**P: Por que um usuário ainda pode acessar recursos de um dispositivo que eu desabilitei no Portal do Azure?**

**R:** Pode demorar até uma hora para uma revogação ser aplicada.

>[!Note] 
>Para dispositivos inscritos, é recomendável apagar o dispositivo para garantir que usuários não possam acessar os recursos. Para obter mais informações, consulte [Inscrever dispositivos para gerenciamento no Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**P: Por que meus usuários veem "Você não pode chegar lá daqui"?**

**R:** Se você tiver configurado certas regras de acesso condicional para exigir um estado de dispositivo específico e o dispositivo não atender aos critérios, os usuários serão bloqueados e verão esta mensagem. Avalie as regras da política de acesso condicional e assegure-se de que o dispositivo possa atender aos critérios para evitar essa mensagem.

---

**P: Por que alguns dos meus usuários não recebem prompts de MFA em dispositivos ingressados no Azure AD?**

**R:** Se o usuário ingressar ou registrar um dispositivo no Azure AD usando a autenticação multifator, o próprio dispositivo se tornará um segundo fator confiável para esse usuário específico. Em seguida, sempre que o mesmo usuário entrar no dispositivo e acessar um aplicativo, o Azure AD considerará o dispositivo como um segundo fator e permitirá que o usuário acesse diretamente os aplicativos sem prompts de MFA adicionais. Esse comportamento não se aplica a nenhum outro usuário que entra no dispositivo, portanto, todos os outros usuários que acessarem esse dispositivo receberão um prompt com um desafio de MFA antes de acessar aplicativos que exigem MFA.

---

**P: Posso ver o registro de dispositivo nas informações do usuário no Portal do Azure e posso ver o estado como registrado no dispositivo. Estou configurado corretamente para usar o acesso condicional?**

**R:** O estado do ingresso do dispositivo, refletido por deviceID, deve corresponder ao do Microsoft Azure AD e atender a qualquer critério de avaliação para acesso condicional. Para obter mais informações, consulte [Exigir dispositivos gerenciados para acesso ao aplicativo em nuvem com acesso condicional](../active-directory-conditional-access-policy-connected-applications.md).

---

**P: Por que recebo uma mensagem de "nome de usuário ou senha está incorreta" para um dispositivo que acabei de ingressar no Azure AD?**

**R:** As razões comuns para esse cenário são:

- Suas credenciais de usuário não são mais válidas.

- O computador não pode se comunicar com o Azure Active Directory. Verifique se há problemas de conectividade de rede.

- Os logons federados requerem que o servidor de federação dê suporte a um ponto de extremidade WS-Trust ativo. 

- Você habilitou a Autenticação de Passagem, e o usuário tem uma senha temporária que precisa ser alterada no logon.

---

**P: Por que vejo a caixa de diálogo “Ocorreu um erro!” quando tento fazer o Microsoft Azure AD ingressar no meu computador?**

**R:** Esse é um resultado da configuração de registro do Azure Active Directory como Intune. Certifique-se de que o usuário que está tentando fazer o ingresso do Microsoft Azure AD tenha a licença correta do Intune atribuída. Para obter mais informações, consulte [Configurar o gerenciamento de dispositivos do Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**P: Por que minha tentativa de ingressar um computador falhou embora eu não tenha obtido nenhuma informação de erro?**

**R:** Uma causa provável é que o usuário está conectado ao dispositivo usando a conta de administrador interno local. Crie uma conta local diferente antes de usar o Ingresso do Azure Active Directory para concluir a configuração. 


---

**P: Onde posso encontrar informações de solução de problemas sobre o registro de dispositivo automático?**

**R:** Para encontrar informações de solução de problemas, consulte:

- [Solução de problemas de registro automático de computadores ingressados no domínio do Azure AD – Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)

- [Solução de problemas com o registro automático de computadores ingressados no domínio do Azure AD para clientes de nível inferior do Windows](troubleshoot-hybrid-join-windows-legacy.md)
 

---

