---
title: "Azure AD Connect: autenticação de passagem | Microsoft Docs"
description: "Este artigo descreve a autenticação de passagem do Azure AD (Azure Active Directory) e como isso permite entradas do Azure AD por meio da validação de senhas de usuários no Active Directory local."
services: active-directory
keywords: "o que é a autenticação de passagem do Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4fc3c822e657ce1a66a59e15c1a7d636a9f699e6
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Configurar a conexão do usuário com autenticação de passagem do Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>O que é a autenticação de passagem do Azure Active Directory?

Permitir que os usuários usem as mesmas credenciais (senhas) para entrar tanto nos recursos locais quanto nos serviços baseados em nuvem é benéfico para eles e para a organização. Os usuários têm menos uma senha para lembrar, o que proporciona uma melhor experiência do usuário. Isso também reduz as chances os usuários esquecerem de como entrar. Os custos de suporte técnico diminuem porque os problemas relacionados à senha normalmente consomem a maior parte dos recursos de suporte.

Muitas organizações usam a [sincronização de senha do Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), um recurso do [Azure AD Connect](active-directory-aadconnect.md) que sincroniza os hashes de senhas de usuários do Active Directory local com o Azure AD (Azure Active Directory) como uma maneira de fornecer aos usuários as mesmas credenciais em recursos locais e serviços baseados em nuvem. No entanto, outras organizações requerem que as senhas, mesmo em um formulário de hash, não deixem seus limites organizacionais internos.

O recurso de autenticação de passagem do Azure AD fornece uma solução simples para essas organizações. Quando os usuários entram no Azure AD, esse recurso garante que as senhas dos usuários sejam validadas diretamente no Active Directory local. Essa funcionalidade também oferece os seguintes benefícios:

- Fácil de usar
  - A validação de senha é realizada sem a necessidade de implantações locais ou configuração de rede complexas.
  - O conector local leve que escuta e responde a solicitações de validação de senha.
  - O conector local recebe automaticamente melhorias do recurso e correções de bug.
  - Ele pode ser configurado com o [Azure AD Connect](active-directory-aadconnect.md). O conector local leve é instalado no mesmo servidor que o Azure AD Connect.
- Segurança
  - Senhas locais nunca são armazenadas na nuvem, em formato nenhum.
  - O conector local leve estabelece apenas conexões de saída de dentro de sua rede. Portanto, não há nenhum requisito para instalar o conector em uma rede de perímetro, também conhecida como uma DMZ.
  - A autenticação de passagem funciona perfeitamente com a Autenticação Multifator do Azure.
- Confiável e escalonável
  - Conectores locais leves adicionais podem ser instalados em vários servidores para obter alta disponibilidade de solicitações de entrada.

![O que é a Autenticação de Passagem do Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Você pode combinar a autenticação de passagem com o recurso de [SSO (logon único) contínuo](active-directory-aadconnect-sso.md). Dessa forma, quando os usuários estiverem em seus computadores corporativos em sua rede corporativa, eles não precisarão digitar suas senhas para entrar no Azure AD, uma experiência realmente integrada.

## <a name="whats-available-during-preview"></a>O que está disponível durante a visualização?

>[!NOTE]
>A autenticação de passagem do Azure AD está atualmente em visualização. Essa é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.

Os cenários a seguir têm suporte total durante a visualização:

- Todos os aplicativos baseados em navegador da Web
- Aplicativos cliente do Office 365 que dão suporte à [autenticação moderna](https://aka.ms/modernauthga)

Os cenários a seguir _não_ têm suporte durante a versão prévia:

- Aplicativos cliente herdados do Office e do Exchange ActiveSync (ou seja, aplicativos de email nativos em dispositivos móveis). <br>As organizações são incentivadas a mudar para autenticação moderna se possível. Isso permite o suporte à autenticação de passagem, mas também ajuda a proteger suas identidades usando recursos de [acesso condicional](../active-directory-conditional-access.md) como Autenticação Multifator.
- Ingresso do Azure AD para dispositivos Windows 10.

>[!IMPORTANT]
>Como alternativa para cenários em que o recurso de autenticação de passagem não dá suporte atualmente (aplicativos cliente do Office herdados, Ingresso do Azure AD e Exchange ActiveSync para dispositivos Windows 10), a sincronização de senha também é habilitada por padrão quando você habilita a autenticação de passagem. A sincronização de senha funciona como um fallback apenas nesses cenários específicos. Se isso não for necessário, você poderá desligar a sincronização de senha na página [Recursos opcionais](active-directory-aadconnect-get-started-custom.md#optional-features) no assistente do Azure AD Connect.

### <a name="prerequisites"></a>Pré-requisitos

Antes que você possa habilitar a autenticação de passagem do Azure AD, é necessário satisfazer o seguinte:

- Um locatário do Azure AD para o qual você é um administrador global.

  >[!NOTE]
  >É altamente recomendável que a conta de Administrador Global seja uma conta somente na nuvem. Dessa forma, você pode gerenciar a configuração do seu locatário caso seus serviços locais falhem ou fiquem indisponíveis. Saiba mais sobre [adicionar uma conta de Administrador Global somente de nuvem](../active-directory-users-create-azure-portal.md).

- Azure AD Connect versão 1.1.486.0 ou superior. É recomendável que você use a [versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
- Um servidor executando o Windows Server 2012 R2 ou posterior no qual executar o Azure AD Connect.
  - Esse servidor deve ser um membro da mesma floresta do AD que os usuários cujas senhas precisam ser validadas.
  - Observe que o conector de autenticação de passagem é instalado no mesmo servidor do Azure AD Connect. Verifique se a versão do conector é a 1.5.58.0 ou posterior.

    >[!NOTE]
    >Ambientes de várias florestas têm suporte se há relações de confiança entre as florestas do AD e se o roteamento de sufixo de nome está corretamente configurado.

- Se desejar alta disponibilidade, você precisará de mais servidores executando o Windows Server 2012 R2 ou posterior para instalar os conectores autônomos. (A versão do conector deve ser 1.5.58.0 ou posterior.)
- Se houver um firewall entre qualquer um dos conectores e o Azure AD:
    - Se a filtragem de URL estiver habilitada, verifique se o conector pode se comunicar com as URLs a seguir:
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - Certifique-se de que os conectores também estabelecem conexões IP diretas com os [intervalos de IP do data center do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Verifique se o firewall não executa a inspeção SSL, pois os conectores usam certificados de cliente para se comunicar com o Azure AD.
    - Certifique-se de que o conector possa fazer solicitações de saída para o Azure AD nas portas 80 e 443.
      - Se o firewall impõe as regras de acordo com os usuários de origem, abra essas portas para o tráfego proveniente de serviços do Windows em execução como um serviço de rede.
      - Os conectores fazem solicitações HTTP pela porta 80 para baixar listas de revogação de certificado SSL. Isso também é necessário para a capacidade de atualização automática funcionar corretamente.
      - Os conectores fazem solicitações HTTPS pela porta 443 para todas as outras operações, como habilitar e desabilitar o recurso, registrar conectores, baixar atualizações do conector e manipular todas as solicitações de entrada do usuário.

     >[!NOTE]
     >Recentemente, fizemos melhorias para reduzir o número de portas exigidas pelos conectores para comunicação com o serviço. Se você estiver executando versões mais antigas dos conectores autônomos e/ou do Azure AD Connect, deverá continuar a manter as portas adicionais (5671, 8080, 9090, 9091, 9350, 9352, 10100 10120) abertas.

### <a name="enable-azure-ad-pass-through-authentication"></a>Habilitar a autenticação de passagem do Azure AD

A autenticação de passagem do Azure AD pode ser habilitada por meio do Azure AD Connect.

Se você estiver executando uma instalação nova do Azure AD Connect, escolha o [caminho de instalação personalizado](active-directory-aadconnect-get-started-custom.md). Na página **Conexão do usuário**, selecione **Autenticação de passagem**. Após uma conclusão bem-sucedida, isso instala o primeiro conector no mesmo servidor que o Azure AD Connect. Além disso, isso habilita a funcionalidade de autenticação de passagem em seu locatário.

![Azure AD Connect – conexão do usuário](./media/active-directory-aadconnect-sso/sso3.png)

Se você já tiver instalado o Azure AD Connect, configure-o usando o caminho de [instalação expressa](active-directory-aadconnect-get-started-express.md) ou de [instalação personalizada](active-directory-aadconnect-get-started-custom.md), selecione **Alterar página de conexão do usuário** no Azure AD Connect e clique em **Avançar**. Em seguida, selecione **Autenticação de passagem** para instalar um conector de autenticação de passagem no mesmo servidor que o Azure AD Connect e habilitar o recurso em seu locatário.

![Azure AD Connect – Alterar entrada do usuário](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>A Autenticação de Passagem do Azure AD é uma funcionalidade em nível de locatário. Ele afeta a entrada do usuário em todos os domínios gerenciados no seu locatário. No entanto, os usuários de domínios federados continuarão a entrar usando os Serviços de Federação do Active Directory (AD FS) ou qualquer outro provedor de federação que você tiver configurado anteriormente. Se você converter um domínio de federado para gerenciado, todos os usuários nesse domínio passarão automaticamente a entrar usando a autenticação de passagem. Os usuários somente na nuvem não são afetados por autenticação de passagem.

Se estiver planejando usar a autenticação de passagem em uma implantação de produção, é altamente recomendável que você instale um segundo conector em um servidor separado (diferente do que está executando o Azure AD Connect e o primeiro conector). Fazer isso garante que você tenha a alta disponibilidade das solicitações de conexão. Você pode instalar quantos conectores adicionais precisar. Isso se baseia nos números médio e de pico de solicitações de conexão manipuladas por seu locatário.

Siga essas instruções para implantar um conector autônomo.

#### <a name="step-1-download-and-install-the-connector"></a>Etapa 1: baixar e instalar o conector

Nesta etapa, você pode baixar e instalar o software do conector no servidor.

1.    [Baixe](https://go.microsoft.com/fwlink/?linkid=837580) o conector mais recente. Verifique se a versão do conector é a 1.5.58.0 ou posterior.
2.    Abra o prompt de comando como administrador.
3.    Execute o comando a seguir. A opção **/q** significa “instalação silenciosa”: a instalação não solicita que você aceite o Contrato de Licença de Usuário Final: `
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>Você pode instalar apenas um único conector por servidor.

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Etapa 2: registrar o conector no Azure AD para autenticação de passagem

Nesta etapa, você registrar o conector instalado em seu servidor com nosso serviço e torná-lo disponível para receber solicitações de entrada.

1.    Abra uma janela do PowerShell como administrador.
2.    Navegue até **C:\Program Files\Microsoft AAD App Proxy Conector** e execute o script conforme demonstrado a seguir: `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Quando solicitado, insira as credenciais de sua conta de Administrador Global no locatário do Azure AD.

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>Como funciona a autenticação de passagem do Azure AD?

Quando um usuário tenta entrar no Azure AD (e se a autenticação de passagem estiver habilitada no locatário), ocorre o seguinte:

1. O usuário insere o nome de usuário e senha dele na página de entrada do Azure AD. Nosso serviço coloca o nome de usuário e a senha (criptografados usando uma chave pública) em uma fila para validação.
2. Um dos conectores locais disponíveis faz uma chamada para a fila de saída e recupera o nome de usuário e senha.
3. O conector, em seguida, valida o nome de usuário e senha no Active Directory usando APIs padrão do Windows (um mecanismo semelhante ao usado pelo AD FS). Observe que o nome de usuário pode ser que o nome de usuário local padrão (normalmente `userPrincipalName`) ou outro atributo configurado no Azure AD Connect (também conhecido como `Alternate ID`).
4. O controlador de domínio local avalia a solicitação e retorna uma resposta (êxito ou falha) ao conector.
5. O conector, por sua vez, retorna essa resposta de volta ao Azure AD.
6. Azure AD, em seguida, avalia a resposta e responde ao usuário conforme apropriado. Por exemplo, ele emite um token de volta para o aplicativo ou solicita a Autenticação Multifator.

O diagrama a seguir ilustra as diversas etapas. Todas as solicitações e respostas são feitas pelo canal HTTPS.

![Autenticação de passagem](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="password-writeback"></a>Write-back de senha

Em casos em que você tiver configurado o [Write-back de Senha](../active-directory-passwords-update-your-own-password.md) em seu locatário e para um usuário específico, se o usuário entrar usando a autenticação de passagem, ele poderá alterar ou redefinir sua senha como antes. As senhas serão gravadas de volta no Active Directory local conforme o esperado.

No entanto, se o Write-back de Senha não estiver configurado no seu locatário ou o usuário não tiver uma licença válida do Azure AD atribuída a ele, o usuário não poderá atualizar a senha dele na nuvem. Isso é verdadeiro mesmo se sua senha tiver expirado. Em vez disso, o usuário verá essa mensagem: “Sua organização não permite que você atualize sua senha neste site. Atualize-a de acordo com o método recomendado pela sua organização ou peça ajuda ao seu administrador se necessário".

## <a name="next-steps"></a>Próximas etapas
- Veja como habilitar o recurso de [SSO contínuo do Azure AD](active-directory-aadconnect-sso.md) no locatário.
- Leia nosso [guia de solução de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) para saber como resolver problemas comuns com autenticação de passagem do Azure AD.

## <a name="feedback"></a>Comentários
Seus comentários são importantes para nós. Use a seção de comentários se você tiver dúvidas. Use nosso [fórum UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) para solicitações de novos recursos.


