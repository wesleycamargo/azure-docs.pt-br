---
title: "Azure AD Connect: autenticação de passagem | Microsoft Docs"
description: "Este artigo descreve a autenticação de passagem do Azure AD (Azure Active Directory) e como isso permite entradas do Azure AD por meio da validação de senhas de usuários no seu Active Directory local."
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
ms.date: 04/21/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 0f54fb7d2d8cf010baf79409bc6a528d34982500
ms.lasthandoff: 04/22/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Configurar entrada do usuário com Autenticação de Passagem do Azure Active Directory

## <a name="what-is-azure-active-directory-azure-ad-pass-through-authentication"></a>O que é a Autenticação de Passagem do Azure AD (Azure Active Directory)?

Permitir que os usuários usem as mesmas credenciais (senhas) para entrar tanto nos recursos locais quanto nos serviços baseados em nuvem é benéfico para os usuários e para a organização. Os usuários terão uma senha a menos para lembrar. Isso proporciona uma melhor experiência de usuário e reduz as chances de os usuários esquecerem como entrar. Isso por sua vez reduz os custos de suporte técnico, já que problemas relacionados à senha normalmente consomem a maior parte dos recursos de suporte.

Muitas organizações usam a [sincronização de senha do Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), uma funcionalidade do [Azure AD Connect](active-directory-aadconnect.md) que sincroniza as senhas de usuários do Active Directory local ao Azure AD como uma maneira de fornecer aos usuários as mesmas credenciais em funcionalidades locais e serviços baseados em nuvem. No entanto, outras organizações requerem que as senhas, mesmo em um formulário de hash, não deixem seus limites organizacionais internos.

A autenticação de Passagem do Azure AD fornece uma solução simples para essas organizações. Quando os usuários entram no Azure AD, ele garante que as senhas dos usuários sejam validadas diretamente no seu Active Directory local. Essa funcionalidade também oferece os seguintes benefícios:

- Fácil de usar
  - A validação de senha é realizada sem a necessidade de implantações locais ou configuração de rede complexas.
  - Ele utiliza somente um conector local leve que escuta e responde a solicitações de validação de senha.
  - Ele pode ser configurado com o [Azure AD Connect](active-directory-aadconnect.md). O conector local leve é instalado no mesmo servidor que o Azure AD Connect.
- Segurança
  - Senhas locais nunca são armazenadas na nuvem, em formato nenhum.
  - O conector local leve estabelece conexões de saída apenas de dentro de sua rede. Portanto, não há nenhum requisito para instalar o conector em um DMZ.
  - A autenticação de passagem funciona perfeitamente com a Autenticação Multifator do Azure.
- Confiável e escalonável
  - Conectores locais leves adicionais podem ser instalados em vários servidores para obter alta disponibilidade de solicitações de entrada.

![O que é a Autenticação de Passagem do Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Quando combinado com a função [Logon Único Contínuo](active-directory-aadconnect-sso.md), faz com que usuários em computadores corporativos em sua rede corporativa não precisem nem mesmo digitar as respectivas senhas para entrar no Azure AD, uma experiência totalmente integrada.

## <a name="whats-available-during-preview"></a>O que está disponível durante a visualização?

>[!NOTE]
>A autenticação de passagem do Azure AD está atualmente em visualização. Essa é uma funcionalidade gratuita e você não precisa de nenhuma edição paga do Azure AD para usá-la. 

Os cenários a seguir têm suporte total durante a visualização:

- Todos os aplicativos baseados em navegador da Web.
- Aplicativos cliente do Office 365 que dão suporte a [autenticação moderna](https://aka.ms/modernauthga).

Os cenários a seguir NÃO têm suporte durante a visualização:

- Aplicativos cliente herdados do Office e do Exchange ActiveSync (ou seja, aplicativos de email nativos em dispositivos móveis).
  - As organizações são incentivadas a mudar para autenticação moderna se possível. Isso permite o suporte à autenticação de passagem, mas também ajuda a proteger suas identidades usando funcionalidades de [acesso condicional](../active-directory-conditional-access.md) como autenticação multifator.
- Ingresso do Azure AD para dispositivos Windows 10.

>[!IMPORTANT]
>Como alternativa para cenários aos quais a autenticação de passagem não dá suporte atualmente (aplicativos cliente do Office herdados, Ingresso do Azure AD e Exchange ActiveSync para dispositivos Windows 10), a sincronização de senha também é habilitada por padrão quando você habilita a Autenticação de Passagem. A sincronização de senha funciona como um fallback apenas nesses cenários específicos. Se isso não for necessário, você poderá desligar a sincronização de senha na página [Recursos Opcionais](active-directory-aadconnect-get-started-custom.md#optional-features) no Azure AD Connect.

## <a name="how-to-enable-azure-ad-pass-through-authentication"></a>Como habilitar a Autenticação de Passagem do Azure AD?

### <a name="pre-requisites"></a>Pré-requisitos

Antes que você possa habilitar a autenticação de passagem do Azure AD, você precisa satisfazer os seguintes pré-requisitos:

- Um locatário do Azure AD para o qual você é um administrador global.

>[!NOTE]
>É recomendável que a conta seja uma conta de administrador global somente de nuvem, para que você possa gerenciar a configuração do locatário se os serviços locais falharem ou ficarem não disponíveis. Você pode adicionar uma conta de Administrador Global somente de nuvem conforme demonstrado [aqui](../active-directory-users-create-azure-portal.md).

- Azure AD Connect versão 1.1.484.0 ou superior. É recomendável que você use o [versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
- Um servidor executando o Windows Server 2012 R2 ou superior no qual executar a ferramenta Azure AD Connect.
  - Esse servidor deve ser um membro da mesma floresta do AD que os usuários cujas senhas precisam ser validadas.
  - Observe que um conector é instalado no mesmo servidor que o Azure AD Connect.

>[!NOTE]
>Ambientes de várias florestas têm suporte se há relações de confiança entre as florestas do AD e o roteamento de sufixo de nome está corretamente configurado.

- Se desejar alta disponibilidade, você precisará de mais servidores executando o Windows Server 2012 R2 ou superior para instalar os conectores autônomos.
- Se houver um firewall entre qualquer um dos conectores e o Azure AD, certifique-se do seguinte:
    - Se a filtragem de URL estiver habilitada, verifique se o conector pode se comunicar com as URLs a seguir:
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - Os conectores também estabelecem conexões IP diretas com os [intervalos de IP do data center do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Verifique se o firewall não executa a inspeção SSL, pois os conectores usam certificados de cliente para se comunicar com o Azure AD.
    - Certifique-se de que o conector possa fazer solicitações HTTPS (TCP) para o Azure AD nas portas 80 e 443.
      - Se o firewall impõe as regras de acordo com os usuários de origem, abra essas portas para o tráfego proveniente de serviços do Windows em execução como um serviço de rede.

>[!NOTE]
>Recentemente, fizemos melhorias para reduzir o número de portas exigidas pelos conectores para comunicação com o serviço. Se você estiver executando versões mais antigas dos conectores autônomos e/ou do Azure AD Connect, você deverá continuar a manter as portas adicionais (5671, 8080, 9090, 9091, 9350, 9352, 10100 10120) abertas.

### <a name="enabling-azure-ad-pass-through-authentication"></a>Habilitando a Autenticação de Passagem do Azure AD

A autenticação de passagem do Azure AD pode ser habilitada por meio do Azure AD Connect.

Se você estiver executando uma instalação nova do Azure AD Connect, escolha o [caminho de instalação personalizado](active-directory-aadconnect-get-started-custom.md). Na página "Entrada do usuário", selecione "Autenticação de passagem". Após uma conclusão bem-sucedida, isso instala o primeiro conector no mesmo servidor que o Azure AD Connect. Além disso, isso habilita a funcionalidade de autenticação de passagem em seu locatário.

![Azure AD Connect – Entrada do usuário](./media/active-directory-aadconnect-sso/sso3.png)

Se você já tiver uma instalação do Azure AD Connect, instale usando o caminho de [instalação expressa](active-directory-aadconnect-get-started-express.md) ou [instalação personalizada](active-directory-aadconnect-get-started-custom.md), escolha "Alterar página de entrada do usuário" no Azure AD Connect e clique em "Avançar". Em seguida, selecione "Autenticação de passagem" para instalar um conector de autenticação de passagem no mesmo servidor que o Azure AD Connect e habilitar a funcionalidade em seu locatário.

![Azure AD Connect – Alterar entrada do usuário](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>A Autenticação de Passagem do Azure AD é uma funcionalidade em nível de locatário. Ele afeta a entrada do usuário em todos os domínios gerenciados no seu locatário. No entanto, os usuários de domínios federados continuarão a entrar usando os Serviços de Federação do Active Directory (AD FS) ou qualquer outro provedor de federação que você tiver configurado anteriormente. Se você converter um domínio de federados gerenciados, todos os usuários nesse domínio passarão automaticamente a entrar usando a autenticação de passagem. Os usuários somente na nuvem não são afetados por autenticação de passagem.

### <a name="ensuring-high-availability"></a>Garantindo alta disponibilidade

Se você estiver planejando usar a autenticação de passagem em uma implantação de produção, é altamente recomendável que você instale um segundo conector em um servidor separado (diferente de uma execução do Azure AD Connect e do primeiro conector) para garantir que você tenha alta disponibilidade de solicitações de entrada. Você pode instalar quantos conectores adicionais precisar; isso se baseia nos números médio e de pico de solicitações de entrada manipuladas por seu locatário.

Siga as instruções a seguir para implantar um conector autônomo:

#### <a name="step-1-download-and-install-the-connector"></a>Etapa 1: baixar e instalar o conector

Nesta etapa, você pode baixar e instalar o software do conector no servidor.

1.    [Baixe](https://go.microsoft.com/fwlink/?linkid=837580) o conector mais recente.
2.    Abra um prompt de comando como administrador.
3.    Execute o seguinte comando (/q significa instalação silenciosa: a instalação não solicitará que você aceite o Contrato de Licença de Usuário Final):

`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`
>[!NOTE]
>Você só pode instalar um único conector por servidor.

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Etapa 2: registrar o conector no Azure AD para autenticação de passagem

Nesta etapa, você registrar o conector instalado em seu servidor com nosso serviço e torná-lo disponível para receber solicitações de entrada.

1.    Abra uma janela do PowerShell como administrador.
2.    Navegue até **C:\Program Files\Microsoft AAD App Proxy Conector** e execute o script conforme demonstrado a seguir: `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Quando solicitado, insira as credenciais de sua conta de administrador global no locatário do Azure AD.

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>Como funciona a autenticação de passagem do Azure AD?

Quando um usuário tenta entrar no Azure AD (e a autenticação de passagem está habilitada no locatário), ocorre o seguinte:

1. O usuário insere o nome de usuário e senha dele na página de entrada do Azure AD. Nosso serviço coloca o nome de usuário e a senha (criptografados usando uma chave pública) em uma fila para validação.
2. Um dos conectores locais disponíveis faz uma chamada para a fila de saída e recupera o nome de usuário e senha.
3. O conector, em seguida, valida o nome de usuário e senha no Active Directory usando APIs padrão do Windows (um mecanismo semelhante ao usado pelo ADFS). Observe que o nome de usuário pode ser que o nome de usuário local padrão (normalmente, "userPrincipalName") ou outro atributo (conhecido como "ID Alternativa") configurado no Azure AD Connect.
4. O Controlador de Domínio local avalia a solicitação e retorna uma resposta (êxito ou falha) ao conector.
5. O conector, por sua vez, retorna essa resposta de volta ao Azure AD.
6. Azure AD, em seguida, avalia a resposta e responde ao usuário conforme apropriado. Por exemplo, ele emite um token de volta para o aplicativo ou solicita autenticação multifator.

O diagrama a seguir também ilustra as diversas etapas. Observe que todas as solicitações e respostas são feitas pelo canal HTTPS.

![Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="note-about-password-writeback"></a>Observação sobre o Write-back de Senha

Caso você tenha configurado [Write-back de Senha](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) em seu locatário e para um usuário específico, se o usuário entrar usando a autenticação de passagem, ele poderá alterar ou redefinir sua senha como antes. As senhas serão gravadas de volta para o Active Directory local conforme o esperado.

No entanto, se uma dessas condições não for verdadeira (o Write-back de Senha não estiver configurado no seu locatário ou o usuário não tiver uma licença válida do Azure AD atribuída a ele), o usuário não poderá atualizar a senha dele na nuvem, nem mesmo se essa senha tiver expirado. Em vez disso, o usuário verá uma mensagem conforme demonstrado a seguir: "Sua organização não permite que você atualize sua senha neste site. Atualize-a de acordo com o método recomendado pela sua organização ou peça ajuda ao seu administrador se necessário.".

## <a name="troubleshooting-pass-through-authentication"></a>Solucionar problemas de autenticação de passagem

Esta seção lhe ajudará a localizar informações de solução para alguns dos problemas comuns durante a instalação, o registro ou a desinstalação dos conectores de autenticação de passagem (sejam por meio do Azure AD Connect ou autônomos). E durante a habilitação e a operação da funcionalidade em seu locatário.

### <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>Problemas durante a instalação dos conectores (sejam por meio do Azure AD Connect ou autônomos)

#### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Já existe um conector de Proxy de Aplicativo do Azure AD

Um conector de autenticação de passagem não pode ser instalado no mesmo servidor que um conector de [Proxy de Aplicativo do Azure AD](../../active-directory/active-directory-application-proxy-get-started.md). Você precisará instalar o conector de autenticação de passagem em um servidor separado.

#### <a name="an-unexpected-error-occured"></a>Erro inesperado

[Colete logs do conector](#how-to-collect-pass-through-authentication-connector-logs?) do servidor e contate o Suporte da Microsoft com o seu problema.

### <a name="issues-during-registration-of-connectors"></a>Problemas durante o registro de conectores

#### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>O registro do conector falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual o conector foi instalado possa se comunicar com as URLs e portas de nosso serviço listadas [aqui](#pre-requisites).

#### <a name="an-unexpected-error-occurred"></a>Erro inesperado

[Colete logs do conector](#how-to-collect-pass-through-authentication-connector-logs?) do servidor e contate o Suporte da Microsoft com o seu problema.

### <a name="issues-during-un-installation-of-connectors"></a>Problemas durante a desinstalação de conectores

#### <a name="warning-message-when-un-installing-azure-ad-connect"></a>Mensagem de aviso ao desinstalar o Azure AD Connect

Se você tiver a autenticação de passagem habilitada em seu locatário e tentar desinstalar o Azure AD Connect, ele mostrará a seguinte mensagem de aviso: "Os usuários não poderão entrar no Azure AD, a menos que você tenha outros agentes de autenticação de passagem instalados em outros servidores.".

Você precisa ter uma instalação com [alta disponibilidade](#ensuring-high-availability) em vigor antes de desinstalar o Azure AD Connect para evitar interrupção na entrada de usuários.

### <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>Problemas com a habilitação da funcionalidade de autenticação de passagem

#### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>A habilitação da funcionalidade falhou porque não havia nenhum conector disponível

Você precisa ter pelo menos um servidor de conector ativo para poder habilitar a autenticação de passagem no locatário. Você pode instalar um conector instalando o Azure AD Connect ou um conector autônomo.

#### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>A habilitação da funcionalidade falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual o Azure AD Connect está instalado possa se comunicar com as URLs e portas de nosso serviço listadas [aqui](#pre-requisites).

### <a name="issues-while-operating-the-pass-through-authentication-feature"></a>Problemas ao operar a funcionalidade de autenticação de passagem

#### <a name="user-facing-sign-in-errors"></a>Erros de entrada voltada para o usuário

A funcionalidade relata os erros voltados para o usuário a seguir na tela de entrada do Azure AD. Elas são detalhadas abaixo junto com as respectivas etapas de resolução apropriada.

|Erro|Descrição|Resolução
| --- | --- | ---
|AADSTS80001|Não é possível conectar ao Active Directory|Verifique se os servidores do conector são membros da mesma floresta do AD que os usuários cujas senhas precisam ser validadas e se são capazes de se conectar ao Active Directory.  
|AADSTS8002|Um tempo limite ao estabelecer conexão com o Active Directory|Verifique se o Active Directory está disponível e respondendo a solicitações dos conectores.
|AADSTS80004|O nome de usuário passado para o conector não era válido|Verifique se o usuário está tentando fazer logon com o nome de usuário correto.
|AADSTS80005|Validação encontrou WebException imprevisível|Esse é provavelmente um problema temporário. Tente novamente a solicitação. Caso a falha persista, contate o Suporte da Microsoft.
|AADSTS80007|Erro na comunicação com o Active Directory|Confira os logs de conector para obter mais informações e verifique se o Active Directory está funcionando conforme o esperado.

### <a name="how-to-collect-pass-through-authentication-connector-logs"></a>Como coletar logs do conector de autenticação de passagem?

Dependendo do tipo de problema você tiver, você precisará examinar locais diferentes em busca de logs de autenticação de passagem do conector.

#### <a name="connector-event-logs"></a>Logs de eventos do conector

Para erros relacionados ao conector, abra o aplicativo Visualizador de Eventos e verifique em **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**.

Para análises detalhadas e logs de depuração, você pode habilitar o log de "Sessão". Não execute o conector com esse log habilitado durante operações normais; use somente para solução de problemas. Observe que o conteúdo do log é visível apenas depois que o log está desabilitado novamente.

#### <a name="detailed-trace-logs"></a>Logs de rastreamento detalhados

Para solucionar problemas de falhas de conexão do usuário, procure logs de rastreamento em **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Esses logs incluem os motivos pelos quais um usuário específico falha ao entrar usando a funcionalidade de autenticação de passagem. Abaixo está um exemplo de entrada de log:

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Você pode obter detalhes descritivos do erro ('1328' no exemplo acima) abrindo o prompt de comando e executando o comando a seguir. Observação: Você precisará substituir '1328' pelo número real do erro que você vê em seus logs.

`Net helpmsg 1328`

O resultado deve ser semelhante a este:

![Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

#### <a name="domain-controller-logs"></a>Logs do controlador de domínio

Se o log de auditoria estiver habilitado, será possível encontrar informações adicionais no logs de segurança dos controladores de domínio. Uma maneira simples de consultar solicitações de entrada enviadas pelos conectores de autenticação de passagem de consulta é a mostrada a seguir:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="feedback"></a>Comentários

Seus comentários são importantes para nós. Você pode nos enviar email para [aadopauthfeedback@microsoft.com](mailto:aadopauthfeedback@microsoft.com). Se você tiver solicitações de novas funcionalidades, use nosso [fórum UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – estamos ouvindo.

