<properties
	pageTitle="Saiba mais: Gerenciamento de senha do Azure AD | Microsoft Azure"
	description="Tópicos avançados sobre o gerenciamento de senha do Azure AD, incluindo como funciona o write-back de senha, segurança de write-back de senha, como funciona o portal de redefinição de senha e quais dados são usados na redefinição de senha."
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="kbrint"
	editor="billmath"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/18/2015" 
	ms.author="asteen"/>

# Saiba mais sobre Gerenciamento de Senha
Se você já tiver implantado o Gerenciamento de Senha ou estiver somente procurando saber mais sobre a parte técnica antes de implantá-lo, esta seção lhe dará uma boa visão geral dos conceitos técnicos por trás do serviço. Abordaremos o seguinte:

* [**Visão geral de write-back de senha**](#password-writeback-overview)
  - [Como funciona o write-back de senha](#how-password-writeback-works)
  - [Cenários com suporte para write-back de senha](#scenarios-supported-for-password-writeback)
  - [Modelo de segurança de write-back de senha](#password-writeback-security-model)
* [**Como funciona o portal de redefinição de senha?**](#how-does-the-password-reset-portal-work)
  - [Quais dados são usados na redefinição de senha?](#what-data-is-used-by-password-reset)

## Visão geral de write-back de senha
O write-back de senha é um componente do [Azure Active Directory Connect](active-directory-aadconnect) que pode ser habilitado e usado pelos assinantes atuais do Active Directory Premium do Azure. Para saber mais, confira [Edições do Active Directory do Azure](active-directory-editions.md).

O write-back de senha permite que você configure o locatário de nuvem para gravar senhas de volta no seu Active Directory local. Ele evita que você precise configurar e gerenciar uma solução de redefinição de senha de autoatendimento complicado no local e fornece uma maneira conveniente baseada em nuvem para que os usuários redefinam suas senhas locais onde quer que estejam. Leia sobre alguns dos principais recursos de write-back de senha:

- **Feedback sem atraso.** O write-back de senha é uma operação síncrona. Os usuários serão notificados imediatamente se a senha não atender à política ou não puder ser redefinida ou alterada por qualquer motivo.
- **Com suporte à redefinição de senha para os usuários do AD FS ou outras tecnologias de federação.** Com o write-back de senha, desde que as contas de usuário federado sejam sincronizadas com seu locatário do Azure AD, eles poderão gerenciar suas senhas do AD locais da nuvem.
- **Com suporte à redefinição de senhas de usuários usando a sincronização de hash de senha.** Quando o serviço de redefinição de senha detecta que uma conta de usuário sincronizada está habilitada para sincronização de hash de senha, redefinimos o local dessa conta e a senha da nuvem ao mesmo tempo.
- **Com suporte à alteração de senhas do painel de acesso e do Office 365.** Quando usuários federados ou sincronizados com senha alteram suas senhas expiradas ou não expiradas, gravamos as senhas novamente em seu ambiente do AD local.
- **Com suporte ao write-back de senhas quando um administrador as redefine do ** [**Portal de Gerenciamento do Azure**](https://manage.windowsazure.com). Sempre que um administrador redefine uma senha de usuário no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), se esse usuário é federado ou sincronizado com senha, vamos definir a senha que o administrador selecionar no AD local também. Isso não é suportado atualmente no Portal de Administração do Office.
- **impõe as suas políticas de senha do AD locais.** Quando um usuário redefine a sua senha, verificamos se ele atende às suas políticas do AD locais antes de confirmá-lo nesse diretório. Isso inclui histórico, complexidade, tempo, filtros de senha e qualquer outra restrição de senha que você tenha definido no AD local.
- **Não requer regras de firewall de entrada.** O write-back de senha usa uma retransmissão do barramento de serviço do Azure como um canal de comunicação subjacente, o que significa que você não precisa abrir todas as portas de entrada no firewall para que esse recurso funcione.
- **Sem suporte para contas de usuário que existam em grupos protegidos no seu Active Directory local.** Para saber mais sobre grupos protegidos, confira [Contas e grupos protegidos do Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### Como funciona o write-back de senha
O write-back de senha tem três componentes principais:

- O serviço de nuvem de redefinição de senha (também integrado às páginas de alteração de senha do Azure AD)
- A retransmissão do barramento de serviço do Azure específica de locatário
- O ponto de extremidade de redefinição de senha local

Elas se encaixam conforme descrito no diagrama abaixo:

  ![][001]

Quando um usuário federado ou com sincronização de hash de senha redefine ou altera a sua senha na nuvem, ocorre o seguinte:

1.	Verificamos que tipo de senha o usuário tem. Se virmos que a senha é gerenciada no local, garantimos a execução do serviço de write-back. Se estiver em execução, permitimos que o usuário continue; se não estiver, informamos ao usuário que a senha não pode ser redefinida aqui.
2.	Em seguida, o usuário passa pelas entradas de autenticação pertinentes e alcança a tela de redefinição de senha.
3.	O usuário seleciona uma nova senha e a confirma.
4.	Ao clicar em enviar, criptografamos a senha em texto sem formatação com uma chave simétrica que foi criada durante o processo de instalação de write-back.
5.	Após criptografar a senha, nós a incluímos em uma carga que seja enviada por um canal HTTPS para a retransmissão do barramento de serviço específica do seu locatário (que também configuramos para você durante o processo de instalação de write-back). Esta retransmissão é protegida por uma senha gerada aleatoriamente que somente a sua instalação local sabe.
6.	Depois que a mensagem chega ao barramento de serviço, o ponto de extremidade de redefinição de senha é automaticamente reativado e vê que há uma solicitação de redefinição pendente.
7.	O serviço procurará o usuário em questão usando o atributo de âncora de nuvem. Para que essa pesquisa seja bem-sucedida, o objeto de usuário deve existir no espaço do conector do AD, deve ser vinculado ao objeto de MV correspondente e deve ser vinculado ao objeto de conector AAD correspondente. Finalmente, para a sincronização localizar essa conta de usuário, o link do objeto de conector do AD para MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` no link. Isso é necessário porque quando a chamada vem da nuvem, o mecanismo de sincronização usa o atributo cloudAnchor para pesquisar o objeto de espaço do conector AAD, segue o link de volta para o objeto de MV e segue o link de volta para o objeto do AD. Como pode haver vários objetos do AD (várias florestas) para o mesmo usuário, o mecanismo de sincronização depende do `Microsoft.InfromADUserAccountEnabled.xxx` link para escolher o correto.
8.	Depois que a conta de usuário é encontrada, tentamos redefinir a senha diretamente na floresta do AD pertinente.
9.	Se a operação de definição de senha for bem-sucedida, dizemos ao usuário que sua senha foi modificada e que ele pode seguir o seu caminho tranquilamente.
10.	Se a operação de definição de senha falhar, retornamos o erro para o usuário e o deixamos tentar novamente. A operação pode falhar porque o serviço estava inoperante, porque a senha selecionada não atende às políticas da organização, porque não foi possível encontrar o usuário no AD local ou por vários outros motivos. Temos uma mensagem específica para muitos desses casos e informamos ao usuário o que podem fazer para resolver o problema.

### Cenários com suporte para write-back de senha
A tabela a seguir descreve quais os cenários com suporte para quais versões de nossos recursos de sincronização. Em geral, é altamente recomendável que você instale a versão mais recente do [Azure AD Connect](active-directory-aadconnect.md#download-azure-ad-connect) se quiser usar o write-back de senha.

  ![][002]

### Modelo de segurança de write-back de senha
O write-back de senha é um serviço altamente seguro e sólido. Para garantir que as suas informações estarão protegidas, habilitamos um modelo de segurança com quatro camadas que é descrito abaixo.

- **Retransmissão de barramento de serviço específica do locatário**: quando você configurar o serviço, configuramos uma retransmissão do barramento de serviço específica do locatário que é protegida por uma senha forte gerada aleatoriamente a qual a Microsoft nunca tem acesso.
- **Chave de criptografia de senha criptograficamente forte e bloqueada**: depois que a retransmissão do barramento de serviço é criada, podemos criar uma chave simétrica forte que usamos para criptografar a senha quando ela é transmitida. Essa chave reside somente no repositório secreto do locatário na nuvem, que é bem bloqueado e auditado, assim como qualquer senha no diretório.
- **TLS padrão da indústria**: quando uma redefinição de senha ou a operação de alteração ocorre na nuvem, podemos levar a senha de texto sem formatação e criptografá-la com sua chave pública. Vamos então colocá-la em uma mensagem HTTPS que é enviada por um canal criptografado usando certificados SSL da Microsoft para a sua retransmissão do barramento de serviço. Depois que essa mensagem é recebida no Barramento de Serviço, o agente local acorda, autentica para o barramento de serviço usando a senha forte que foi gerada, recebe a mensagem criptografada, descriptografa usando a chave particular que nós geramos e tenta definir a senha por meio da API do AD DS SetPassword. Essa etapa é o que nos permite impor a política de senha local do AD (complexidade, idade, histórico, filtros, etc.) na nuvem.
- **Políticas de expiração de mensagem**: por fim, se por algum motivo a mensagem ficar no barramento de serviço porque o serviço local está inativo, o tempo limite terá sido atingido e ela será removida após alguns minutos para aumentar ainda mais a segurança.

## Como funciona o portal de redefinição de senha?
Quando um usuário navega até o portal de redefinição de senha, um fluxo de trabalho é iniciado para determinar se essa conta de usuário é válida, a que organização o usuário pertence, onde a senha do usuário é gerenciada e se o usuário tem licença para usar o recurso ou não. Ler as etapas abaixo para saber mais sobre a lógica por trás da página de redefinição de senha.

1.	O usuário clica no link Não consegue acessar a sua conta ou vai diretamente para [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2.	O usuário insere uma id de usuário e passa por um captcha.
3.	O AD do Azure verifica se o usuário é capaz de usar esse recurso fazendo o seguinte:
    - Verifica se o usuário tem esse recurso habilitado e uma licença do Azure AD atribuída.
        - Se o usuário não tiver esse recurso habilitado ou uma licença atribuída, ele é aconselhado a entrar em contato com seu administrador para redefinir a senha.
    - Verifica se o usuário tem os dados de desafio corretos definidos na sua conta de acordo com a política do administrador.
        - Se a política exige apenas um desafio, fica garantido que o usuário tem os dados apropriados definidos em pelo menos um dos desafios habilitados pela política do administrador.
          - Se o usuário não estiver configurado, ele é aconselhado a entrar em contato com seu administrador para redefinir a senha.
        - Se a política exige dois desafios, fica garantido que o usuário tem os dados apropriados definidos em pelo menos dois dos desafios habilitados pela política do administrador.
          - Se o usuário não estiver configurado, ele é aconselhado a entrar em contato com seu administrador para redefinir a senha.
    - Verifica se a senha do usuário é gerenciada no local (federada ou com sincronização de hash de senha).
       - Se o write-back estiver implantado e a senha do usuário for gerenciada no local, o usuário tem permissão para prosseguir, autenticar e redefinir a senha.
       - Se o write-back não estiver implantado e a senha do usuário for gerenciada no local, o usuário é aconselhado a entrar em contato com seu administrador para redefinir a senha.
4.	Se ficar estabelecido que o usuário é capaz de redefinir a senha com êxito, ele será guiado através do processo de redefinição.

Saiba mais sobre como implantar o write-back de senha em [Introdução: gerenciamento de senhas do Azure AD](active-directory-passwords-getting-started.md).

### Quais dados são usados na redefinição de senha?
A tabela a seguir descreve onde e como esses dados são usados durante a redefinição de senha e foi projetada para ajudá-lo a decidir quais opções de autenticação são adequadas para a sua organização. Esta tabela também mostra os requisitos de formatação quando você fornecer dados em nome dos usuários de caminhos de entrada que não validarem esses dados.

> [AZURE.NOTE]O Telefone comercial não aparece no portal de registro porque os usuários atualmente não podem editá-lo no diretório.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nome do método de contato</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento de dados do Active Directory do Azure</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Usados/Definíveis onde?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Requisitos de formato</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Telefone comercial</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>Por exemplo, Set-MsolUser -UserPrincipalName NMendes@contoso.com -PhoneNumber "+ 1 1234567890x1234"</p>
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
              <p>Por exemplo, Set-MsolUser -UserPrincipalName NMendes@contoso.com -MobilePhone "+ 1 1234567890x1234"</p>
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
              <p>Observação: o campo de email alternativo é especificado como uma matriz de cadeias de caracteres no diretório. Usamos a primeira entrada nessa matriz.</p>
              <p>Por exemplo, Set-MsolUser -UserPrincipalName NMendes@contoso.com -AlternateEmailAddresses "email@live.com"</p>
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
                <a href="mailto:user@domain.com">usuário@domínio.com</a> ou 甲斐@黒川.日本</p>
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

<br/> <br/> <br/>

**Recursos adicionais**


* [O que é o Gerenciamento de Senhas](active-directory-passwords.md)
* [Como funciona o gerenciamento de senhas](active-directory-passwords-how-it-works.md)
* [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md)
* [Personalizar o gerenciamento de senhas](active-directory-passwords-customize.md)
* [Práticas recomendadas de gerenciamento de senhas](active-directory-passwords-best-practices.md)
* [Como obter percepções operacionais com relatórios de gerenciamento de senhas](active-directory-passwords-get-insights.md)
* [Perguntas frequentes sobre Gerenciamento de Senhas](active-directory-passwords-faq.md)
* [Solucionar problemas do Gerenciamento de Senhas](active-directory-passwords-troubleshoot.md)
* [Gerenciamento de senhas no MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"

<!---HONumber=Sept15_HO4-->