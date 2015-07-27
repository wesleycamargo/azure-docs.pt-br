<properties 
	pageTitle="Guia de Introdução: Gerenciamento de Senhas do Azure AD | Microsoft Azure" 
	description="Permita que os usuários redefinam suas próprias senhas, descubra os pré-requisitos para redefinição de senha e habilite o Write-back de Senha para gerenciar senhas locais no Active Directory." 
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

# Introdução ao Gerenciamento de Senhas
Permitir que os usuários gerenciem suas próprias senhas da nuvem do Active Directory do Azure ou do Active Directory local leva apenas algumas etapas simples. Depois de garantir que você cumpriu alguns pré-requisitos simples, você terá as opções de alteração e redefinição de senha habilitadas para toda a sua organização num piscar de olhos. Este artigo o guiará pelos seguintes conceitos:

* [**Como permitir que os usuários redefinam suas senhas da nuvem do Active Directory do Azure**](#enable-users-to-reset-their-azure-ad-passwords)
 - [Pré-requisitos de redefinição de senha por autoatendimento](#prerequisites)
 - [Etapa 1: configurar a política de redefinição de senha](#step-1-configure-password-reset-policy)
 - [Etapa 2: adicionar dados de contato para o usuário de teste](#step-2-add-contact-data-for-your-test-user)
 - [Etapa 3: redefinir sua senha como um usuário](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Como habilitar usuários para redefinir ou alterar suas senhas do Active Directory local**](#enable-users-to-reset-or-change-their-ad-passwords)
 - [Pré-requisitos de Write-back de Senha](#writeback-prerequisites)
 - [Etapa 1: baixar a versão mais recente do Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [Etapa 2: habilitar Write-back de Senha no Azure AD Connect através da interface de usuário ou do PowerShell e verificar](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [Etapa 3: configurar o seu firewall](#step-3-configure-your-firewall)
 - [Etapa 4: configurar as permissões apropriadas](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [Etapa 5: redefinir a sua senha do AD como um usuário e verificar](#step-5-reset-your-ad-password-as-a-user)

## Permitir que os usuários redefinam suas senhas Azure AD
Esta seção o orienta pelos processos de habilitação da redefinição de senha por autoatendimento para o seu diretório em nuvem do AAD, registro de usuários para a redefinição de senha por autoatendimento e, por fim, de execução de um teste de redefinição de senha por autoatendimento como um usuário.

- [Pré-requisitos de redefinição de senha por autoatendimento](#prerequisites)
- [Etapa 1: configurar a política de redefinição de senha](#step-1-configure-password-reset-policy)
- [Etapa 2: adicionar dados de contato para o usuário de teste](#step-2-add-contact-data-for-your-test-user)
- [Etapa 3: redefinir sua senha como um usuário](#step-3-reset-your-azure-ad-password-as-a-user)


###  Pré-requisitos
Antes de habilitar e usar a redefinição de senha por autoatendimento, você deve concluir os seguintes pré-requisitos:

- Criar um locatário do AAD. Para saber mais, consulte o [Introdução ao Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).
- Obtenha uma assinatura do Azure. Para saber mais, consulte [O que é um locatário do Azure AD?](active-directory-administer.md#what-is-an-azure-ad-tenant).
- Associe o seu locatário do AAD com a sua assinatura do Azure. Para saber mais sobre as assinaturas do Azure, consulte [Como as assinaturas do Azure são associadas ao Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).
- Atualize para o Azure AD Premium ou Basic. Para saber mais, consulte [Edições do Active Directory do Azure](http://azure.microsoft.com/pricing/details/active-directory/).

  >[AZURE.NOTE]Para habilitar a redefinição de senha por autoatendimento, você deve atualizar para o Azure AD Premium ou Azure AD Basic. Para saber mais, consulte Edições do Active Directory do Azure. Estas informações incluem instruções detalhadas sobre como se inscrever para o Azure AD Premium ou Basic, como ativar o seu plano de licença e ativar o acesso ao Azure AD e como atribuir acesso a contas de administrador e usuário.
  
- Crie pelo menos uma conta de administrador e uma conta de usuário no diretório do AAD.
- Atribua uma licença do AAD Premium ou Basic para a conta de administrador e usuário que você criou.

### Etapa 1: configurar a política de redefinição de senha
Para configurar a política de redefinição de senha de usuário, conclua as seguintes etapas:
 
1.	Abra um navegador de sua escolha e acesse o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2.	No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), localize o **extensão do Active Directory** na barra de navegação à esquerda.

    ![][001]

3. Na guia **Diretório**, clique no diretório no qual você deseja configurar a política de redefinição de senha de usuário, por exemplo, Wingtip Toys.

    ![][002]

4.	Clique na guia **Configurar**.

    ![][003]

5.	Na guia **Configurar**, role para baixo até a seção **política de redefinição de senha de usuário**. Aqui é onde você configura todos os aspectos da política de redefinição de senha de usuário para um determinado diretório.

    >[AZURE.NOTE]Esta **política se aplica somente a usuários finais na sua organização, e não aos administradores**. Por motivos de segurança, a Microsoft controla a política de redefinição de senha para administradores. Se você não vir essa seção, verifique se você se inscreveu para o Azure Active Directory Premium ou Basic e **atribuiu uma licença** à conta de administrador que está configurando este recurso.

    ![][004]

6.	Para configurar a política de redefinição de senha do usuário, deslize o botão de alternância **usuários habilitados para redefinição de senha** para a definição **Sim**. Isso revela vários outros controles que permitem que você configure o modo como esse recurso funciona no seu diretório. Fique à vontade para personalizar como achar melhor a redefinição de senha. Se desejar saber mais sobre o que cada controle de política de redefinição de senha faz, consulte [Personalizar: Gerenciamento de Senhas do Azure AD](active-directory-passwords-customize).

    ![][005]

7.	Depois de configurar a sua política de redefinição de senha de usuário conforme desejado para o seu locatário, clique no botão **Salvar** na parte inferior da tela.

  >[AZURE.NOTE]É recomendada uma política de redefinição de senha de usuário de dois desafios, para que você possa ver como a funcionalidade opera no caso mais complexo.

  ![][006]

### Etapa 2: adicionar dados de contato para o usuário de teste
Você tem várias opções para especificar o modo como dados de usuários na sua organização são usados para redefinição de senha.

-	Editar usuários no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) ou no [Portal de Administração do Office 365](https://portal.microsoftonline.com)
-	Usar o AAD Connect para sincronizar propriedades do usuário no Azure AD a partir de um domínio local do Active Directory
-	Usar o Windows PowerShell para editar propriedades de usuário
-	Permitir que os usuários registrem seus próprios dados guiando-os para o portal de registro em [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
-	Exigir que os usuários se registrem para redefinição de senha ao entrarem no Painel de Acesso em [http://myapps.microsoft.com](http://myapps.microsoft.com) definindo a opção de configuração de SSPR **Exigir que os usuários se registrem ao entrarem no painel de acesso** como **Sim**.

Se desejar saber mais sobre que dados são usados pela redefinição de senha, bem como quaisquer requisitos de formatação para esses dados, consulte [Quais dados são usados pela redefinição de senha?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### Para adicionar dados de contato de usuário por meio do Portal de Registro de Usuário
1.	Para usar o portal de registro de redefinição de senha, você deve fornecer aos usuários da sua organização um link para essa página ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) ou habilitar a opção para exigir que os usuários se registrem automaticamente. Depois que clicarem nesse link, eles são solicitados a entrar com uma conta organizacional. Depois de fazer isso, eles veem a seguinte página:

    ![][007]

2.	Aqui, os usuários podem fornecer e verificar seus telefones celulares, endereços de email alternativos ou perguntas de segurança. Este é um exemplo de verificação de telefone celular.

    ![][008]

3.	Depois que um usuário especificar essas informações, a página será atualizada para indicar que as informações são válidas (ofuscadas abaixo). Clicando nos botões **concluir** ou **cancelar**, o usuário será levado para o painel de acesso.

    ![][009]

4.	Quando um usuário confirmar essas duas informações, seu perfil será atualizado com os dados fornecidos. Neste exemplo, o número do **Telefone Comercial** foi especificado manualmente, portanto o usuário também pode usá-lo como um método de contato para redefinir sua senha.

    ![][010]

### Etapa 3: redefinir sua senha do Azure AD como um usuário
Agora que você configurou uma política de redefinição de usuário e especificou detalhes de contato para o usuário, esse usuário pode executar uma redefinição de senha por autoatendimento.

#### Para executar uma redefinição de senha por autoatendimento
1.	Se você visitar um site como [**portal.microsoftonline.com**](http://portal.microsoftonline.com), verá uma tela de logon como a abaixo. Clique no link **Não consegue acessar sua conta?** para testar a interface de usuário da redefinição de senha.

    ![][011]

2.	Depois de clicar em **Não consegue acessar sua conta?**, você é direcionado para uma nova página que solicitará a **ID de usuário** para a qual você deseja redefinir uma senha. Insira a sua **ID de usuário** de teste aqui, passe o captcha e clique em **avançar**.

    ![][012]

3.	Como o usuário especificou um **telefone comercial**, um **telefone celular** e um **email alternativo** neste caso, você verá que ele recebe todas essas opções para passar o primeiro desafio.

    ![][013]

4.	Nesse caso, opte por **ligar** para o **telefone comercial** primeiro. Observe que ao selecionar um método baseado em telefone, os usuários serão solicitados a **verificar seus números de telefone** antes de poderem redefinir suas senhas. Isso serve para impedir que pessoas mal-intencionadas enviem spam para números de telefone dos usuários da sua organização.

    ![][014]

5.	Após o usuário confirmar o número de telefone, clicar em ligar fará com que um controle giratório seja exibido e que o telefone toque. Uma mensagem será reproduzida após ele atender o telefone, indicando que o **usuário deverá pressionar "#"** para verificar sua conta. Pressionar esta tecla verificará automaticamente se o usuário passou no primeiro desafio e avançará a interface de usuário para a segunda etapa de verificação.

    ![][015]

6.	Após passar no primeiro desafio, a interface de usuário é atualizada automaticamente para removê-lo da lista de escolhas do usuário. Neste caso, como você usou seu **Telefone Comercial**primeiro, apenas **Telefone Celular** e **Email Alternativo** permanecem como opções válidas para serem usadas como o desafio da segunda etapa de verificação. Clique na opção **Enviar email para o meu email alternativo**. Depois que tiver feito isso, pressionar Email enviará um email para o email alternativo registrado.

    ![][016]

7.	Aqui está um exemplo de um email que os usuários verão. Observe a marca do locatário:

    ![][017]

8.	Após o email chegar, a página será atualizada e você poderá inserir a verificação encontrada no email na caixa de entrada mostrada abaixo. Após inserir um código correto, o botão Avançar acende e você consegue passar na segunda etapa de verificação.

    ![][018]

9.	Após atender os requisitos da política organizacional, você pode escolher uma nova senha. A senha é validada se atender aos requisitos de senha "forte" do AAD (consulte [Política de senha no Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)), e um validador de força é exibido para indicar ao usuário se a senha digitada atende a essa política.

    ![][019]

10.	Após fornecer senhas correspondentes que atendem à política organizacional, sua senha é redefinida e você pode entrar com a sua nova senha imediatamente.

    ![][020]


## Permitir que os usuários redefinam ou alterem suas senhas do AD

Esta seção o orienta pela configuração da redefinição de senha para gravar senhas novamente em Active Directory local.

- [Pré-requisitos de Write-back de Senha](#writeback-prerequisites)
- [Etapa 1: baixar a versão mais recente do Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
- [Etapa 2: habilitar Write-back de Senha no Azure AD Connect através da interface de usuário ou do PowerShell e verificar](#step-2-enable-password-writeback-in-azure-ad-connect)
- [Etapa 3: configurar o seu firewall](#step-3-configure-your-firewall)
- [Etapa 4: configurar as permissões apropriadas](#step-4-set-up-the-appropriate-active-directory-permissions)
- [Etapa 5: redefinir a sua senha do AD como um usuário e verificar](#step-5-reset-your-ad-password-as-a-user)


### Pré-requisitos de write-back
Antes de poder habilitar e usar o Write-back de Senha, você deve concluir os seguintes pré-requisitos:

- Ter um locatário do Azure AD com o Azure AD Premium ativado. Para saber mais, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
- Configurar e habilitar a redefinição de senha no seu locatário. Para saber mais, consulte [Permitir que os usuários redefinam suas senhas do Azure AD](#enable-users-to-reset-their-azure-ad-passwords)
- Ter pelo menos uma conta de administrador e uma conta de usuário de teste com uma licença do Azure AD Premium que você pode usar para testar esse recurso. Para saber mais, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

  >[AZURE.NOTE]Certifique-se de que a conta de administrador usada para habilitar o Write-back de Senha é uma conta de administrador de nuvem (criado no Azure AD), e não uma conta federada (criado no AD local e sincronizada com o Azure AD).
  
- Ter uma implantação local única ou de várias florestas do AD executando o Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 ou Windows Server 2012 R2 com service packs mais recentes instalados.

  >[AZURE.NOTE]Se você estiver executando uma versão anterior do Windows Server 2008 ou 2008 R2, você pode usar este recurso, mas será necessário [Baixar e instalar a atualização KB 2386717](https://support.microsoft.com/kb/2386717) antes de poder aplicar a política de senha local do AD na nuvem.
  
- Ter a ferramenta Azure AD Connect instalada e preparar o ambiente do AD para sincronização para a nuvem. Para saber mais, consulte [Usar a sua infraestrutura de identidade local na nuvem](active-directory-aadconnect.md).
- Se você estiver usando DirSync, verifique se o firewall da sua organização está configurado para bloquear conexões de saída e desbloquear a **porta TCP 828 ou 818** para poder habilitar e usar o Write-back de Senha. Se você estiver usando o Azure AD Sync ou o Azure AD Connect, esta etapa não é necessária, porque somente **TCP 443** de saída (e, em alguns casos, **TCP 9350 a 9354**) precisa estar aberta.

  >[AZURE.NOTE]É altamente recomendável que qualquer pessoa que use as ferramentas Azure AD Sync ou DirSync atualizem para a versão mais recente do Azure AD Connect para garantir a melhor experiência possível e obter novos recursos conforme são lançados.
  

### Etapa 1: baixar a versão mais recente do Azure AD Connect
O Write-back de Senha está disponível nas versões do Azure AD Connect ou na ferramenta Azure AD Sync com o número de versão **1.0.0419.0911** ou superior. O Write-back de Senha com o desbloqueio de conta automático está disponível nas versões do Azure AD Connect ou na ferramenta Azure AD Sync com o número de versão **1.0.0485.0222** ou superior. Se você estiver executando uma versão mais antiga, atualize para pelo menos essa versão antes de continuar. [Clique aqui para baixar a versão mais recente do Azure AD Connect](active-directory-aadconnect.md#download-azure-ad-connect).

#### Para verificar a versão do Azure AD Sync
1.	Navegue até **%ProgramFiles%\\Azure Active Directory Sync**. 2.	Encontre o executável **ConfigWizard.exe**.
3.	Clique com o botão direito no executável e selecione a opção **Propriedades** no menu de contexto.
4.	Clique na guia **Detalhes**.
5.	Encontre o campo **Versão do arquivo**.

    ![][021]

Se este número de versão for maior que ou igual a **1.0.0419.0911** ou se você estiver instalando o Azure AD Connect, poderá passar para a [Etapa 2: habilitar o Write-back de Senha no Azure AD Connect através da interface de usuário ou do PowerShell e verificar](#step-2-enable-password-writeback-in-azure-ad-connect).

 >[AZURE.NOTE]Se esta for a primeira vez que você instala a ferramenta Azure AD Connect, é recomendável que você siga algumas práticas úteis para preparar o ambiente para a sincronização de diretórios. Antes de instalar a ferramenta de Azure AD Connect, você deve ativar a sincronização de diretórios no [Portal de Administração do Office 365](https://portal.microsoftonline.com) ou no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com). Para saber mais, consulte [Gerenciar o Azure AD Connect](active-directory-aadconnect-whats-next.md).


### Etapa 2: habilitar o Write-back de Senha no Azure AD Connect
Agora que você já baixou a ferramenta Azure AD Connect, você está pronto para habilitar o Write-back de Senha. Você pode fazer isso de duas maneiras. Você pode habilitar o Write-back de Senha na tela de recursos opcionais do assistente de configuração do Azure Connect AD ou habilitá-lo por meio do Windows PowerShell.

#### Para habilitar o Write-back de Senha no assistente de configuração
1.	No seu **Computador do Directory Sync**, abra o assistente de configuração do **Azure AD Connect**.
2.	Avance pelas etapas até chegar à tela de configuração **recursos opcionais**.
3.	Marque a opção **Write-back de Senha**.

    ![][022]

4.	Conclua o assistente, a página final resumirá as alterações e incluirá a alteração de configuração de Write-back de Senha.

> [AZURE.NOTE]Você pode desabilitar o Write-back de Senha a qualquer momento reexecutando o assistente e desmarcando o recurso, ou definindo a configuração **Gravar senhas novamente para no Diretório Local** definida como **Não** na seção**Política de Redefinição de Senha de Usuário** da guia **Configurar** do seu diretório no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com). Para saber mais sobre como personalizar a sua experiência de redefinição de senha, confira [Personalizar: Gerenciamento de Senhas do Azure AD](active-directory-passwords-customize.md)

#### Para habilitar o Write-back de Senha usando o Windows PowerShell
1.	No seu **Computador do Directory Sync**, abra uma nova **janela do Windows PowerShell com privilégios elevados**.
2.	Se o módulo ainda não tiver carregado, digite o comando `Import-Module ADSync` para carregar os cmdlets do Azure AD Connect na sua sessão atual.
3.	Obter a lista de Conectores AAD no seu sistema executando o cmdlet `Get-ADSyncConnector` e armazenando os resultados no `$aadConnectorName`
4.	Para obter o status atual do write-back do conector atual, execute o seguinte cmdlet: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName`
5.	Habilite o Write-back de Senha executando o cmdlet: `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName –Enable $true`

> [AZURE.NOTE]Se for solicitada uma credencial, certifique-se de que a conta de administrador que você especificar como AzureADCredential é uma **conta de administrador na nuvem (criada no Azure AD)**, e não uma conta federada (criado no AD local e sincronizada com o Azure AD.[AZURE.NOTE]Você pode desabilitar o Write-back de Senha por meio do PowerShell repetindo as mesmas instruções acima, mas passando `$false` em vez disso, ou definindo a configuração **Gravar senhas novamente no Diretório Local** definir como **Não** na **seção Política de Redefinição de Senha do Usuário** da guia **Configurar** do seu diretório no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

#### Verificar se a configuração foi bem-sucedida
Se a configuração for bem-sucedida, você verá a mensagem “Write-back de redefinição de senha habilitado” na janela do Windows PowerShell, ou uma mensagem de êxito na interface de usuário de configuração.

Você também pode verificar o serviço foi instalado corretamente abrindo o Visualizador de eventos, navegando até o log de eventos do aplicativo e procurando o evento **31005 - OnboardingEventSuccess** da origem **PasswordResetService**.

  ![][023]

### Etapa 3: configurar o seu firewall
Depois de habilitar o Write-back de Senha na ferramenta Azure AD Connect, você precisará garantir que o serviço possa se conectar à nuvem.

1.	Depois que a instalação estiver concluída, se você estiver bloqueando as conexões de saída desconhecidas no seu ambiente, você também precisará adicionar as seguintes regras ao firewall. Não deixe de reiniciar o computador do AAD Connect após fazer estas alterações:
   - Permitir conexões de saída pela porta TCP 443
   - Permitir conexões de saída para https://ssprsbprodncu-sb.accesscontrol.windows.net/ 
   - Ao usar um proxy ou ter problemas gerais de conectividade, permitir conexões de saída pelas portas TCP 9350-9534

### Etapa 4: configurar as permissões apropriadas do Active Directory
Para cada floresta que contenha os usuários cujas senhas serão redefinidas, se X for a conta que foi especificada para essa floresta no assistente de configuração (durante a configuração inicial), X deverá receber os direitos estendidos **Redefinir Senha**, **Alterar Senha**, **Permissões de Gravação** no `lockoutTime`, e **Permissões de Gravação** na `pwdLastSet` no objeto raiz de cada domínio da floresta. A direita deve ser marcada como herdada por todos os objetos de usuário.

Definir essas permissões permite que a conta de serviço MA de cada floresta gerencie senhas em nome de contas de usuário na floresta. Se você não atribuir essas permissões, em seguida, mesmo que o write-back pareça estar configurado corretamente, os usuários encontrarão erros ao tentar gerenciar suas senhas locais na nuvem. Aqui estão as etapas detalhadas sobre como você pode fazer isso usando o snap-in de gerenciamento **Usuários e Computadores do Active Directory**:

>[AZURE.NOTE]Pode levar até uma hora para que essas permissões sejam replicadas em todos os objetos no diretório.

#### Para configurar as permissões corretas para que o write-back ocorra

1.	Abra o snap-in **Usuários e Computadores do Active Directory** com uma conta que tenha as permissões de administração de domínio apropriadas.
2.	Na opções **Menu Exibir**, verifique se **Recursos Avançados** está habilitado.
3.	No painel esquerdo, clique com o botão direito no objeto que representa a raiz do domínio.
4.	Clique na guia **Segurança**.
5.	Em seguida, clique em **Avançado**.

    ![][024]

6.	Na guia **Permissões**, clique em **Adicionar**.

    ![][025]

7.	Selecione a conta para a qual você deseja conceder permissões (é a mesma conta que foi especificada ao configurar a sincronização para essa floresta).
8.	No menu suspenso na parte superior, selecione **Objetos descendentes de Usuário**.
9.	Na caixa de diálogo **Entrada de Permissão** exibida, marque as caixas **Redefinir Senha**, **Alterar Senha**, **Permissões de Gravação** no `lockoutTime`, e **Permissões de Gravação** na `pwdLastSet`.

    ![][026] ![][027] ![][028]

10.	Em seguida, clique em **Aplicar/Ok** por meio de todas as caixas de diálogo abertas.

### Etapa 5: redefinir sua senha do AD como um usuário
Agora que o Write-back de senha foi habilitado, você pode testar se ele funciona redefinindo a senha de um usuário cuja conta foi sincronizada para o seu locatário de nuvem.
 
#### Para verificar se o Write-back de Senha está funcionando corretamente
1.	Navegue até [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) ou vá para qualquer tela de logon de ID organizacional e clique no link **Não consegue acessar sua conta?**.

    ![][029]

2.	Agora você deve ver uma nova página que solicita a ID de usuário para a qual você deseja redefinir uma senha. Insira a sua ID de usuário de teste e prossiga com o fluxo de redefinição de senha.
3.	Depois de redefinir a sua senha, você verá uma tela semelhante a esta. Isso significa que você redefiniu a sua senha com êxito em seus diretórios locais e/ou na nuvem.

    ![][030]

4.	Para verificar se a operação foi bem-sucedida ou diagnosticar erros, vá para o **computador do Directory Sync**, abra **Visualizador de Eventos**, navegue até o **log de eventos do aplicativo** e procure pelo evento **31002 - PasswordResetSuccess** da fonte **PasswordResetService** do seu usuário de teste.

    ![][031]


<br/> <br/> <br/>

**Recursos adicionais**


* [O que é o Gerenciamento de Senhas](active-directory-passwords.md)
* [Como funciona o Gerenciamento de Senhas](active-directory-passwords-how-it-works.md)
* [Personalizar o Gerenciamento de Senhas](active-directory-passwords-customize.md)
* [Práticas recomendadas de gerenciamento de senhas](active-directory-passwords-best-practices.md)
* [Como obter percepções operacionais com relatórios de gerenciamento de senhas](active-directory-passwords-get-insights.md)
* [Perguntas frequentes sobre Gerenciamento de Senhas](active-directory-passwords-faq.md)
* [Solucionar problemas do Gerenciamento de Senhas](active-directory-passwords-troubleshoot.md)
* [Saiba mais](active-directory-passwords-learn-more.md)
* [Gerenciamento de Senhas no MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"

 

<!---HONumber=July15_HO3-->