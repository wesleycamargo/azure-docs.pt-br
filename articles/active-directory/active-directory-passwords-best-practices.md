<properties 
	pageTitle="Práticas recomendadas: Gerenciamento de Senhas do AD do Azure | Microsoft Azure" 
	description="Práticas recomendadas de implantação e uso, documentação de usuário final de exemplo e guias de treinamento para Gerenciamento de Senhas no Active Directory do Azure." 
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
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# Implantando o gerenciamento de senhas e treinando os usuários para usá-lo
Após habilitar a redefinição de senha, a próxima etapa que você precisa tomar é fazer com que os usuários usem o serviço em sua organização. Para fazer isso, você precisará verificar se os usuários estão configurados para usar o serviço adequadamente e também se os usuários têm o treinamento necessário para ter êxito no gerenciamento de suas próprias senhas. Este artigo o guiará pelos seguintes conceitos:

* [**Como configurar os usuários para o gerenciamento de senhas**](#how-to-get-users-configured-for-password-reset)
  * [O que torna uma conta configurada para redefinição de senha](#what-makes-an-account-configured)
  * [Maneiras de popular os dados de autenticação por conta própria](#ways-to-populate-authentication-data)
* [**As melhores maneiras de distribuir a redefinição de senhas para sua organização**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [Distribuição baseada em email + exemplo de comunicações por email](#email-based-rollout)
  * [Como usar o registro imposto para forçar os usuários a se registrarem ao entrar](#using-enforced-registration)
  * [Como carregar dados de autenticação para contas de usuário](#uploading-data-yourself)
* [**Material de treinamento de suporte e usuário de exemplo (em breve!)**](#sample-training-materials)

## Como configurar os usuários para redefinição de senha
Esta seção descreve vários métodos pelos quais você pode garantir que todos os usuários em sua organização possam usar a redefinição de senhas de autoatendimento de forma eficaz caso esqueçam suas senhas.

### O que torna uma conta configurada
Para que um usuário possa usar a redefinição de senha, **todas** as seguintes condições devem ser atendidas:

1.	A redefinição de senha deve ser habilitada no diretório. Saiba como habilitar a redefinição de senha lendo [Habilitar os usuários para redefinir suas senhas do AD do Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) ou [Habilitar os usuários para redefinir ou alterar suas senhas do AD](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
2.	O usuário deve ser licenciado.
 - Para usuários de nuvem, o usuário deve ter **alguma licença paga do Office 365** ou uma **licença do AAD básica** ou uma **licença do AAD Premium** atribuída.
 - Para usuários locais (federados ou com sincronização de hash), o usuário **deve ter uma licença de AAD Premium atribuída**.
3.	O usuário deve ter o **conjunto mínimo de dados de autenticação definidos** de acordo com a política de redefinição de senha atual.
 - Os dados de autenticação serão considerados definidos se o campo correspondente no diretório contiver dados válidos.
 - Um conjunto mínimo de dados de autenticação estará definido em **pelo menos uma** das opções de autenticação habilitadas se uma política de uma porta estiver configurada ou se **pelo menos duas** das opções de autenticação habilitadas se uma política de duas portas estiver configurada.
4.	Se o usuário estiver usando uma conta local, o [Write-back de Senha](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) deverá ser habilitado e ativado

### Maneiras de popular os dados de autenticação
Você tem várias opções para especificar o modo como dados de usuários na sua organização são usados para redefinição de senha.

- Editar usuários no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) ou no [Portal de Administração do Office 365](https://portal.microsoftonline.com)
- Usar o AADSync para sincronizar propriedades do usuário no Azure AD a partir de um domínio local do Active Directory
- Usar o Windows PowerShell para editar propriedades de usuário
- Permitir que os usuários registrem seus próprios dados guiando-os para o portal de registro em [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Exigir que os usuários se registrem para redefinição de senha ao entrarem no Painel de Acesso em [http://myapps.microsoft.com](http://myapps.microsoft.com) definindo a opção de configuração de SSPR **Exigir que os usuários se registrem** como **Sim**.

Os usuários não precisam se registrar para a redefinição de senha para que o sistema funcione. Por exemplo, se tiver números de telefone existentes em seu diretório local, você poderá sincronizá-los no AD do Azure e nós os usaremos para a redefinição de senha automaticamente.

## Qual é a melhor maneira de distribuir a redefinição de senha para os usuários?
A seguir estão as etapas de distribuição geral para a redefinição de senha:

1.	Habilite a redefinição de senha em seu diretório indo para a guia **Configurar** de [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) e selecionando **Sim** para a opção **Usuários Habilitados para Redefinição de Senha**.
2.	Atribua as licenças apropriadas para cada usuário a quem você deseja oferecer a redefinição de senha indo para a guia **Licenças** do [Portal de Gerenciamento](https://manage.windowsazure.com).
3.	Restrinja opcionalmente a redefinição de senha para um grupo de usuários para distribuir o recurso lentamente ao longo do tempo, definindo a opção **Restringir o Acesso para Redefinição de Senha** como **Sim** e selecionando um grupo de segurança para habilitar a redefinição de senha (observe que todos esses usuários devem ter licenças atribuídas a eles).
4.	Instrua os usuários a usar a redefinição de senha enviando a eles um email instruindo-os a se registrar, habilitando o registro imposto no painel de acesso ou carregando os dados de autenticação apropriados para os usuários por conta própria por meio de DirSync, do PowerShell ou do [Portal de Gerenciamento](https://manage.windowsazure.com). Mais detalhes são fornecidos abaixo.
5.	Ao longo do tempo, examine o registro dos usuários navegando até a guia Relatórios e exibindo o relatório [**Atividade de Registro de Redefinição de Senha**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity).
6.	Depois que um bom número de usuários se registrarem, assista a eles ao usarem a redefinição de senha navegando até a guia Relatórios e exibindo o relatório [**Atividade de Redefinição de Senha**](active-directory-passwords-get-insights.md#view-password-reset-activity).

Há várias maneiras de informar os usuários que podem se registrar e usar a redefinição de senha em sua organização. Elas são detalhadas abaixo.

### Distribuição baseada em email
Talvez a abordagem mais simples para informar os usuários de que devem se registrar ou usar a redefinição de senha é enviar um email instruindo-os a fazer isso. Abaixo está um modelo que você pode usar para fazer isso. Fique à vontade para substituir as cores/logotipos pelos seus próprios e personalizá-lo para atender às suas necessidades.

  ![][001]

Você pode baixar o modelo de email [aqui](http://1drv.ms/1xWFtQM).

### Usando o registro imposto
Se quiser que seus usuários se inscrevam na redefinição de senha por conta própria, você também poderá forçá-los a se registrar quando entrarem no painel de acesso em [http://myapps.microsoft.com](http://myapps.microsoft.com). Você pode habilitar essa opção na guia **Configurar** em seu diretório, habilitando a opção **Exigir que os Usuários se Registrem ao Entrar no Painel de Acesso**.

Opcionalmente, você também pode definir se eles serão solicitados ou não a se registrar novamente após um período de tempo configurável, modificando a opção **Número de dias antes que os usuários precisem confirmar seus dados de contato** como um valor diferente de zero. Consulte [Personalização do comportamento do gerenciamento de senha do usuário](active-directory-passwords-customize.md#password-management-behavior) para obter mais informações.

  ![][002]

Depois que você habilitar essa opção, quando os usuários entrarem no painel de acesso, ele verão um pop-up informando que seu administrador solicitou que verifiquem suas informações de contato. Eles poderão usá-lo para redefinir a senha se perderem o acesso à sua conta.

  ![][003]

Clicar em **Verificar agora** os leva para o **portal de registro de redefinição de senha** em [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) e exige o registro. O registro por esse método pode ser ignorado clicando no botão **cancelar** ou fechando a janela, mas os usuários serão lembrados sempre que entrarem sem o devido registro.

  ![][004]

### Carregando dados por conta própria
Se você desejar carregar dados de autenticação por conta própria, os usuários não precisarão se registrar para a redefinição de senha para redefinir suas senhas. Desde que os usuários tenham os dados de autenticação definidos em sua conta que atendam à política de redefinição de senha definida por você, os usuários poderão redefinir suas senhas.

Para saber quais propriedades podem ser definidas por meio do AAD Connect ou do Windows PowerShell, consulte [Quais dados são usados pela redefinição de senha](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Você pode carregar os dados de autenticação por meio do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) seguindo as etapas abaixo:

1.	Navegue até sei diretório na **Extensão do Active Directory** no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2.	Clique na guia **Usuários**.
3.	Selecione na lista o usuário em que você está interessado.
4.	Na primeira guia, você encontrará **Email Alternativo**, que pode ser usado como uma propriedade para habilitar a redefinição de senha. 

    ![][005]

5.	Clique na guia **Informações de Trabalho**.
6.	Nessa página, você encontrará **Telefone Comercial**, **Celular**, **Telefone de Autenticação** e **Email de Autenticação**. Essas propriedades também podem ser definidas para permitir que um usuário redefina sua senha. 

    ![][006]

Consulte [Quais dados são usados pela redefinição de senha](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) para ver como cada uma dessas propriedades pode ser usada.

## Materiais de treinamento de exemplo
Estamos trabalhando no material de treinamento de exemplo que você pode usar para que sua organização de TI e seus usuários aprendam rapidamente sobre implantar e usar a redefinição de senha. Fique atento!


<br/> <br/> <br/>

**Recursos adicionais**


* [O que é o Gerenciamento de Senhas](active-directory-passwords.md)
* [Como funciona o gerenciamento de senhas](active-directory-passwords-how-it-works.md)
* [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md)
* [Personalizar o gerenciamento de senhas](active-directory-passwords-customize.md)
* [Como obter percepções operacionais com relatórios de gerenciamento de senhas](active-directory-passwords-get-insights.md)
* [Perguntas frequentes sobre Gerenciamento de Senhas](active-directory-passwords-faq.md)
* [Solucionar problemas do Gerenciamento de Senhas](active-directory-passwords-troubleshoot.md)
* [Saiba mais](active-directory-passwords-learn-more.md)
* [Gerenciamento de senhas no MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
 

<!---HONumber=August15_HO6-->