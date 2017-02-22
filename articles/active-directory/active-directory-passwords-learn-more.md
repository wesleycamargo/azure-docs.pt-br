---
title: 'Saiba Mais: Gerenciamento de Senhas do AD do Azure | Microsoft Docs'
description: "Tópicos avançados sobre o gerenciamento de senha do Azure AD, incluindo como funciona o write-back de senha, segurança de write-back de senha, como funciona o portal de redefinição de senha e quais dados são usados na redefinição de senha."
services: active-directory
documentationcenter: 
author: asteen
manager: femila
editor: curtand
ms.assetid: d3be2912-76c8-40a0-9507-b7b1a7ce2f8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 8a4e26b7ccf4da27b58a6d0bcfe98fc2b5533df8
ms.openlocfilehash: 534373f72a4181914e3b7ea98ded507418e3d299


---
# <a name="learn-more-about-password-management"></a>Saiba mais sobre Gerenciamento de Senha
> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md).
>
>

Se você já tiver implantado o Gerenciamento de Senha ou estiver somente procurando saber mais sobre a parte técnica antes de implantá-lo, esta seção lhe dará uma boa visão geral dos conceitos técnicos por trás do serviço. Abordaremos o seguinte:

* [**Visão geral de write-back de senha**](#password-writeback-overview)
  * [Como funciona o write-back de senha](#how-password-writeback-works)
  * [Cenários com suporte para write-back de senha](#scenarios-supported-for-password-writeback)
  * [Modelo de segurança de write-back de senha](#password-writeback-security-model)
  * [Uso de largura de banda de write-back de senha](#password-writeback-bandwidth-usage)
* [**Como funciona o portal de redefinição de senha?**](#how-does-the-password-reset-portal-work)
  * [Quais dados são usados na redefinição de senha?](#what-data-is-used-by-password-reset)
  * [Como acessar os dados de redefinição de senha de seus usuários](#how-to-access-password-reset-data-for-your-users)

## <a name="password-writeback-overview"></a>Visão geral de write-back de senha
O write-back de senha é um componente do [Azure Active Directory Connect](connect/active-directory-aadconnect.md) que pode ser habilitado e usado pelos assinantes atuais do Active Directory Premium do Azure. Para saber mais, confira [Edições do Active Directory do Azure](active-directory-editions.md).

O write-back de senha permite que você configure o locatário de nuvem para gravar senhas de volta no seu Active Directory local.  Ele evita que você precise configurar e gerenciar uma solução de redefinição de senha de autoatendimento complicado no local e fornece uma maneira conveniente baseada em nuvem para que os usuários redefinam suas senhas locais onde quer que estejam.  Leia sobre alguns dos principais recursos de write-back de senha:

* **Feedback sem atraso.**   O write-back de senha é uma operação síncrona.  Os usuários serão notificados imediatamente se a senha não atender à política ou não puder ser redefinida ou alterada por qualquer motivo.
* **Com suporte à redefinição de senha para os usuários do AD FS ou outras tecnologias de federação.**   Com o write-back de senha, desde que as contas de usuário federado sejam sincronizadas com seu locatário do Azure AD, eles poderão gerenciar suas senhas do AD locais da nuvem.
* **Com suporte à redefinição de senhas de usuários usando a sincronização de hash de senha.**  Quando o serviço de redefinição de senha detecta que uma conta de usuário sincronizada está habilitada para sincronização de hash de senha, redefinimos o local dessa conta e a senha da nuvem ao mesmo tempo.
* **Com suporte à alteração de senhas do painel de acesso e do Office 365.**   Quando usuários federados ou sincronizados com senha alteram suas senhas expiradas ou não expiradas, gravamos as senhas novamente em seu ambiente do AD local.
* **Oferece suporte à gravação de senhas quando um administrador as redefine no** [**Portal de Gerenciamento do Azure**](https://manage.windowsazure.com).  Sempre que um administrador redefine uma senha de usuário no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), se esse usuário é federado ou sincronizado com senha, vamos definir a senha que o administrador selecionar no AD local também.  Isso não é suportado atualmente no Portal de Administração do Office.
* **impõe as suas políticas de senha do AD locais.**   Quando um usuário redefine a sua senha, verificamos se ele atende às suas políticas do AD locais antes de confirmá-lo nesse diretório.  Isso inclui histórico, complexidade, tempo, filtros de senha e qualquer outra restrição de senha que você tenha definido no AD local.
* **Não requer regras de firewall de entrada.**   O write-back de senha usa uma retransmissão do barramento de serviço do Azure como um canal de comunicação subjacente, o que significa que você não precisa abrir todas as portas de entrada no firewall para que esse recurso funcione.
* **Sem suporte para contas de usuário que existam em grupos protegidos no seu Active Directory local.** Para saber mais sobre grupos protegidos, confira [Contas e grupos protegidos do Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Como funciona o write-back de senha
O write-back de senha tem três componentes principais:

* O serviço de nuvem de redefinição de senha (também integrado às páginas de alteração de senha do Azure AD)
* A retransmissão do barramento de serviço do Azure específica de locatário
* O ponto de extremidade de redefinição de senha local

Elas se encaixam conforme descrito no diagrama abaixo:

  ![][001]

Quando um usuário federado ou com sincronização de hash de senha redefine ou altera a sua senha na nuvem, ocorre o seguinte:

1. Verificamos que tipo de senha o usuário tem.  Se virmos que a senha é gerenciada no local, garantimos a execução do serviço de write-back.  Se estiver em execução, permitimos que o usuário continue; se não estiver, informamos ao usuário que a senha não pode ser redefinida aqui.
2. Em seguida, o usuário passa pelas entradas de autenticação pertinentes e alcança a tela de redefinição de senha.
3. O usuário seleciona uma nova senha e a confirma.
4. Ao clicar em enviar, criptografamos a senha em texto sem formatação com uma chave simétrica que foi criada durante o processo de instalação de write-back.
5. Após criptografar a senha, nós a incluímos em uma carga que seja enviada por um canal HTTPS para a retransmissão do barramento de serviço específica do seu locatário (que também configuramos para você durante o processo de instalação de write-back).  Esta retransmissão é protegida por uma senha gerada aleatoriamente que somente a sua instalação local sabe.
6. Depois que a mensagem chega ao barramento de serviço, o ponto de extremidade de redefinição de senha é automaticamente reativado e vê que há uma solicitação de redefinição pendente.
7. O serviço procurará o usuário em questão usando o atributo de âncora de nuvem.  Para que essa pesquisa seja bem-sucedida, o objeto de usuário deve existir no espaço do conector do AD, deve ser vinculado ao objeto de MV correspondente e deve ser vinculado ao objeto de conector AAD correspondente. Finalmente, para a sincronização localizar essa conta de usuário, o link do objeto de conector do AD para MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` no link.  Isso é necessário porque quando a chamada vem da nuvem, o mecanismo de sincronização usa o atributo cloudAnchor para pesquisar o objeto de espaço do conector AAD, segue o link de volta para o objeto de MV e segue o link de volta para o objeto do AD. Como pode haver vários objetos do AD (várias florestas) para o mesmo usuário, o mecanismo de sincronização depende do `Microsoft.InfromADUserAccountEnabled.xxx` link para escolher o correto.
8. Depois que a conta de usuário é encontrada, tentamos redefinir a senha diretamente na floresta do AD pertinente.
9. Se a operação de definição de senha for bem-sucedida, dizemos ao usuário que sua senha foi modificada e que ele pode seguir o seu caminho tranquilamente.
10. Se a operação de definição de senha falhar, retornamos o erro para o usuário e o deixamos tentar novamente.  A operação pode falhar porque o serviço estava inoperante, porque a senha selecionada não atende às políticas da organização, porque não foi possível encontrar o usuário no AD local ou por vários outros motivos.  Temos uma mensagem específica para muitos desses casos e informamos ao usuário o que podem fazer para resolver o problema.

### <a name="scenarios-supported-for-password-writeback"></a>Cenários com suporte para write-back de senha
A tabela a seguir descreve quais os cenários com suporte para quais versões de nossos recursos de sincronização.  Em geral, é altamente recomendável que você instale a versão mais recente do [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect) se quiser usar o write-back de senha.

  ![][002]

### <a name="password-writeback-security-model"></a>Modelo de segurança de write-back de senha
O write-back de senha é um serviço altamente seguro e sólido.  Para garantir que as suas informações estarão protegidas, habilitamos um modelo de segurança com quatro camadas que é descrito abaixo.

* **Retransmissão de barramento de serviço específica do locatário** : quando você configurar o serviço, configuramos uma retransmissão do barramento de serviço específica do locatário que é protegida por uma senha forte gerada aleatoriamente a qual a Microsoft nunca tem acesso.
* **Chave de criptografia de senha criptograficamente forte e bloqueada** : depois que a retransmissão do barramento de serviço é criada, podemos criar uma chave simétrica forte que usamos para criptografar a senha quando ela é transmitida.  Essa chave existe apenas no repositório secreto de sua empresa na nuvem, que é amplamente bloqueado e auditado, assim como qualquer senha no diretório.
* **TLS padrão da indústria** : quando uma redefinição de senha ou a operação de alteração ocorre na nuvem, podemos levar a senha de texto sem formatação e criptografá-la com sua chave pública.  Vamos então colocá-la em uma mensagem HTTPS que é enviada por um canal criptografado usando certificados SSL da Microsoft para a sua retransmissão do barramento de serviço.  Depois que essa mensagem é recebida no Barramento de Serviço, o agente local acorda, autentica para o barramento de serviço usando a senha forte que foi gerada, recebe a mensagem criptografada, descriptografa usando a chave particular que nós geramos e tenta definir a senha por meio da API do AD DS SetPassword.  Essa etapa é o que nos permite impor a política de senha local do AD (complexidade, idade, histórico, filtros, etc.) na nuvem.
* **Políticas de expiração de mensagem** : por fim, se por algum motivo a mensagem ficar no barramento de serviço porque o serviço local está inativo, o tempo limite terá sido atingido e ela será removida após alguns minutos para aumentar ainda mais a segurança.

### <a name="password-writeback-bandwidth-usage"></a>Uso de largura de banda de write-back de senha

O write-back de senha é um serviço de largura de banda extremamente baixa que envia solicitações de volta para o agente local somente nas seguintes circunstâncias:

1. Duas mensagens são enviadas quando o recurso é habilitado ou desabilitado por meio do Azure AD Connect.
2. Uma mensagem é enviada a cada 5 minutos como uma pulsação de serviço para desde que o serviço está em execução.
3. Duas mensagens são enviadas sempre que uma nova senha é enviada, uma mensagem como solicitação para executar a operação e uma mensagem subsequente com o resultado da operação. Essas mensagens são enviadas nas seguintes circunstâncias.
4. Sempre que uma nova senha é enviada durante uma redefinição de senha de autoatendimento do usuário.
5. Sempre que uma nova senha é enviada durante uma operação de alteração de senha do usuário.
6. Sempre que uma nova senha é enviada durante uma redefinição de usuário iniciada pelo administrador (somente dos portais de administração do Azure)

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações sobre a largura de banda e o tamanho da mensagem

O tamanho de cada uma das mensagens descritas acima normalmente é de 1kb, o que significa que, mesmo sob carga extrema, o próprio serviço de write-back de senha estará consumindo no máximo alguns quilobits por segundo de largura de banda. Como cada mensagem é enviada em tempo real, somente quando for solicitado por uma operação de atualização de senha, e como o tamanho da mensagem é bem pequeno, o uso da largura de banda da capacidade de write-back será efetivamente muito pequeno para ter qualquer impacto real mensurável.

## <a name="how-does-the-password-reset-portal-work"></a>Como funciona o portal de redefinição de senha?
Quando um usuário navega até o portal de redefinição de senha, um fluxo de trabalho é iniciado para determinar se essa conta de usuário é válida, a que organização o usuário pertence, onde a senha do usuário é gerenciada e se o usuário tem licença para usar o recurso ou não.  Ler as etapas abaixo para saber mais sobre a lógica por trás da página de redefinição de senha.

1. O usuário clica no link Não consegue acessar a sua conta ou vai diretamente para [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2. O usuário insere uma id de usuário e passa  por um captcha.
3. O AD do Azure verifica se o usuário é capaz de usar esse recurso fazendo o seguinte:
   * Verifica se o usuário tem esse recurso habilitado e uma licença do Azure AD atribuída.
     * Se o usuário não tiver esse recurso habilitado ou uma licença atribuída, ele é aconselhado a entrar em contato com seu administrador para redefinir a senha.
   * Verifica se o usuário tem os dados de desafio corretos definidos na sua conta de acordo com a política do administrador.
     * Se a política exige apenas um desafio, fica garantido que o usuário tem os dados apropriados definidos em pelo menos um dos desafios habilitados pela política do administrador.
       * Se o usuário não estiver configurado, ele é aconselhado a entrar em contato com seu administrador para redefinir a senha.
     * Se a política exige dois desafios, fica garantido que o usuário tem os dados apropriados definidos em pelo menos dois dos desafios habilitados pela política do administrador.
       * Se o usuário não estiver configurado, ele é aconselhado a entrar em contato com seu administrador para redefinir a senha.
   * Verifica se a senha do usuário é gerenciada no local (federada ou com sincronização de hash de senha).
     * Se o write-back estiver implantado e a senha do usuário for gerenciada no local, o usuário tem permissão para prosseguir, autenticar e redefinir a senha.
     * Se o write-back não estiver implantado e a senha do usuário for gerenciada no local, o usuário é aconselhado a entrar em contato com seu administrador para redefinir a senha.
4. Se ficar estabelecido que o usuário é capaz de redefinir a senha com êxito, ele será guiado através do processo de redefinição.

Saiba mais sobre como implantar o write-back de senha em [Introdução: gerenciamento de senhas do Azure AD](active-directory-passwords-getting-started.md).

### <a name="what-data-is-used-by-password-reset"></a>Quais dados são usados na redefinição de senha?
A tabela a seguir descreve onde e como esses dados são usados durante a redefinição de senha e foi projetada para ajudá-lo a decidir quais opções de autenticação são adequadas para a sua organização. Esta tabela também mostra os requisitos de formatação quando você fornecer dados em nome dos usuários de caminhos de entrada que não validarem esses dados.

> [!NOTE]
> O Telefone comercial não aparece no portal de registro porque os usuários atualmente não podem editá-lo no diretório.
>
>

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nome do Método de Contato</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento de Dados do Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Usados/Definíveis Onde?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Requisitos do formato</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Telefone comercial</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>por exemplo, Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Usado em:</p>
              <p>Portal de redefinição de senha</p>
              <p>Configurável de:</p>
              <p>PhoneNumber é configurável do PowerShell, do DirSync, do Portal de Gerenciamento do Azure e do Portal de administração do Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (por exemplo, +1 1234567890)</p>
              <ul>
                <li class="unordered">
Forneça um código de país<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Forneça um código de área (quando aplicável)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Coloque um + antes do país de código<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve haver um espaço entre o código do país e o resto do número<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Não há suporte para extensões; se você especificar extensões, nós a removeremos do número antes de expedir a chamada.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Telefone celular</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OU</p>
              <p>MobilePhone</p>
              <p>(O telefone de autenticação é usado se não houver dados presentes; caso contrário, ele reverterá para o campo do telefone celular.)</p>
              <p>por exemplo, Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Usado em:</p>
              <p>Portal de redefinição de senha</p>
              <p>Portal de registro</p>
              <p>Configurável de: </p>
              <p>AuthenticationPhone é configurável do portal de registro de redefinição de senha ou do portal de registro MFA.</p>
              <p>MobilePhone é configurável do PowerShell, do DirSync, do Portal de Gerenciamento do Azure e do Portal de administração do Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (por exemplo, +1 1234567890)</p>
              <ul>
                <li class="unordered">
Forneça um código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Forneça um código de área (quando aplicável).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Coloque um + antes do país de código.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve ter um espaço entre o código do país e o resto do número.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Não há suporte para extensões; se você especificar extensões, nós a ignoraremos ao expedir a chamada.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Email alternativo</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OU</p>
              <p>AlternateEmailAddresses[0] </p>
              <p>(A autenticação de que email é usada se não houver dados presentes; caso contrário, ele reverterá para o campo de email alternativo.)</p>
              <p>Observação: o campo de email alternativo é especificado como uma matriz de cadeias de caracteres no diretório.  Usamos a primeira entrada nessa matriz.</p>
              <p>por exemplo, Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com"</p>
            </td>
            <td>
              <p>Usado em:</p>
              <p>Portal de redefinição de senha</p>
              <p>Portal de registro</p>
              <p>Configurável de: </p>
              <p>AuthenticationEmail é configurável do portal de registro de redefinição de senha ou do portal de registro MFA.</p>
              <p>AlternateEmail é configurável do PowerShell, do Portal de Gerenciamento do Azure e do Portal de administração do Office</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> ou 甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
Os emails devem seguir a formatação padrão de acordo com .<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Os emails Unicode têm suporte.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Perguntas de segurança e respostas</p>
            </td>
            <td>
              <p>Não disponível para modificação diretamente no diretório.</p>
            </td>
            <td>
              <p>Usado em:</p>
              <p>Portal de redefinição de senha</p>
              <p>Portal de registro </p>
              <p>Configurável de: </p>
              <p>A única maneira de definir perguntas de segurança é através do Portal de Gerenciamento do Azure.</p>
              <p>A única maneira de definir respostas para perguntas de segurança para um determinado usuário é através do Portal de Registro.</p>
            </td>
            <td>
              <p>As perguntas de segurança têm um máximo de 200 e um mínimo de três caracteres</p>
              <p>As respostas têm um máximo de 40 e um mínimo de três caracteres</p>
            </td>
          </tr>
        </tbody></table>

### <a name="how-to-access-password-reset-data-for-your-users"></a>Como acessar os dados de redefinição de senha de seus usuários
#### <a name="data-settable-via-synchronization"></a>Dados configuráveis por meio da sincronização
Os campos a seguir podem ser sincronizados a partir do ambiente local:

* Telefone celular
* Telefone comercial

#### <a name="data-accessible-with-azure-ad-powershell"></a>Dados acessíveis com o PowerShell do Azure AD
Os seguintes campos são acessíveis com o Azure AD PowerShell e a Graph API:

* Email alternativo
* Telefone celular
* Telefone comercial
* Telefone de autenticação
* E-mail de autenticação

#### <a name="data-settable-with-registration-ui-only"></a>Dados configuráveis somente com a interface do usuário de registros
Os seguintes campos só são acessíveis por meio da IU do registro SSPR (https://aka.ms/ssprsetup):

* Perguntas de segurança e respostas

#### <a name="what-happens-when-a-user-registers"></a>O que acontece quando um usuário se registra?
Quando um usuário se registrar, a página de registro **sempre** definirá os seguintes campos:

* Telefone de autenticação
* E-mail de autenticação
* Perguntas de segurança e respostas

Se você forneceu um valor para **Celular** ou **Email alternativo**, os usuários poderão usar isso imediatamente para redefinir suas senhas, mesmo que não tenham registrado o serviço.  Além disso, os usuários verão esses valores ao se registrar pela primeira vez e poderão modificá-los caso queiram.  No entanto, depois deles terem registrado com êxito, esses valores serão mantidos nos campos **Telefone de Autenticação** e **Email de Autenticação**, respectivamente.

Isso pode ser uma maneira útil de desbloquear grandes números de usuários para usar SSPR e ainda permitir que os usuários validem essas informações durante o processo de registro.

#### <a name="setting-password-reset-data-with-powershell"></a>Configurando dados de redefinição de senha com o PowerShell
Você pode definir valores para os seguintes campos com o Azure AD PowerShell.

* Email alternativo
* Telefone celular
* Telefone comercial

Para começar, você precisa primeiro [baixar e instalar o módulo do Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Depois que você tiver instalado, pode seguir as etapas abaixo para configurar cada campo.

##### <a name="alternate-email"></a>Email alternativo
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

##### <a name="mobile-phone"></a>Telefone celular
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

##### <a name="office-phone"></a>Telefone comercial
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

#### <a name="reading-password-reset-data-with-powershell"></a>Lendo dados de redefinição de senha com o PowerShell
Você pode ler os valores dos campos a seguir com o Azure AD PowerShell.

* Email alternativo
* Telefone celular
* Telefone comercial
* Telefone de autenticação
* E-mail de autenticação

Para começar, você precisa primeiro [baixar e instalar o módulo do Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Depois que você tiver instalado, pode seguir as etapas abaixo para configurar cada campo.

##### <a name="alternate-email"></a>Email alternativo
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

##### <a name="mobile-phone"></a>Telefone celular
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

##### <a name="office-phone"></a>Telefone comercial
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

##### <a name="authentication-phone"></a>Telefone de autenticação
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

##### <a name="authentication-email"></a>E-mail de autenticação
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## <a name="links-to-password-reset-documentation"></a>Links para a documentação de redefinição de senha
Veja abaixo links para todas as páginas de documentação sobre Redefinição de Senha do AD do Azure:

* **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md).
* [**Como funciona**](active-directory-passwords-how-it-works.md) – saiba mais sobre os seis diferentes componentes do serviço e o que cada um deles faz
* [**Introdução**](active-directory-passwords-getting-started.md) – saiba como permitir que os usuários redefinam e alterem suas senhas na nuvem ou no local
* [**Personalizar **](active-directory-passwords-customize.md)- aprenda a personalizar a aparência e o comportamento do serviço de acordo com as necessidades de sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - aprenda a implantar rapidamente e gerenciar com eficiência as senhas em sua organização
* [**Obter percepções**](active-directory-passwords-get-insights.md) – saiba mais sobre nossos recursos integrados de relatórios
* [**Perguntas frequentes**](active-directory-passwords-faq.md) - obtenha respostas para perguntas frequentes
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) – aprenda a solucionar rapidamente os problemas com o serviço

[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"



<!--HONumber=Dec16_HO5-->


