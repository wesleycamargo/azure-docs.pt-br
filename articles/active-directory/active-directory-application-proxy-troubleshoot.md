<properties
	pageTitle="Solucionar problemas de Proxy de Aplicativo"
	description="Aborda como solucionar erros no Proxy de Aplicativo do Azure do AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="rkarlin"/>



# Solucionar problemas de Proxy de Aplicativo


> [AZURE.IMPORTANT]O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

Se ocorrerem erros ao acessar um aplicativo publicado ou em aplicativos de publicação, verifique as seguintes opções para ver se o Proxy de Aplicativo do AD do Microsoft Azure está funcionando corretamente:

- Abra o console de serviços do Windows e verifique se o serviço de "Conector de Proxy de Aplicativo do Microsoft AAD" está habilitado e em execução. Também convém examinar a página de propriedades do serviço de Proxy de Aplicativo, conforme mostrado na imagem a seguir:

![Captura de tela de propriedades de Conector de Proxy de Aplicativo do Microsoft AAD][1]

- Abra o Visualizador de Eventos e procure eventos relacionados ao conector do Proxy de Aplicativo localizado em **Logs de Aplicativos e Serviços** > **Microsoft** > **AadApplicationProxy** > **Conector** > **Admin**.
- Se necessário, logs mais detalhados estão disponíveis ativando a análise e depuração de logs e ativando o log de sessão do conector do Proxy de Aplicativo, conforme mostrado na imagem a seguir:

![Captura de tela do log da sessão do Conector do Proxy de Aplicativo][2]


## Erros gerais

Erro | Descrição | Resolução
--- | --- | ---
Não foi possível acessar este aplicativo corporativo. Você não está autorizado a acessar este aplicativo. Falha na autorização. Certifique-se de atribuir o acesso a este aplicativo ao usuário. | Você pode não ter atribuído o usuário para esse aplicativo. | Vá para a guia Aplicativo e, em Usuários e Grupos, atribua esse usuário ou grupo de usuários a esse aplicativo.
Não foi possível acessar este aplicativo corporativo. Você não está autorizado a acessar este aplicativo. Falha na autorização. Certifique-se de que o usuário tenha licença para Azure Active Directory Premium ou Basic. | O usuário pode obter esse erro ao tentar acessar o aplicativo publicado se o usuário que tentou acessar o aplicativo não tiver uma licença Premium/Basic explicitamente atribuída pelo administrador do assinante. | Vá para a guia **Licenças** do Active Directory do assinante guia e certifique-se de que esse usuário ou grupo esteja atribuído a uma licença Premium ou Basic.


## Solução de problemas do conector
Se o registro falhar durante a instalação do Assistente de conector, você pode exibir o motivo da falha procurando no log de eventos **Logs do Windows** > **Aplicativo** ou executando o seguinte comando do Windows PowerShell.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Erro | Descrição | Resolução |
| --- | --- | --- |
| Falha no registro de conector: verifique se você habilitou o Proxy de Aplicativo no Portal de Gerenciamento do Azure e se inseriu o nome de usuário e a senha do Active Directory corretamente. Erro: "ocorreram um ou mais erros”. | Você fechou a janela de registro sem efetuar logon no AD do Azure. | Execute o Assistente de conector novamente e registrar o Conector. |
| Falha no registro de conector: verifique se você habilitou o Proxy de Aplicativo no Portal de Gerenciamento do Azure e se inseriu o nome de usuário e a senha do Active Directory corretamente. Erro: “AADSTS50001: o recurso `https://proxy.cloudwebappproxy.net/registerapp` está desabilitado”. | O Proxy de Aplicativo está desabilitado. | Habilite o Proxy de Aplicativo no portal do AD do Azure antes de tentar registrar o Conector. Para obter mais informações sobre como habilitar o Proxy de Aplicativo, consulte [Habilitar serviços de Proxy de Aplicativo](active-directory-application-proxy-enable.md). |
| Falha no registro de conector: verifique se você habilitou o Proxy de Aplicativo no Portal de Gerenciamento do Azure e se inseriu o nome de usuário e a senha do Active Directory corretamente. Erro: "ocorreram um ou mais erros”. | Se a janela de registro abre e fecha imediatamente sem permitir que você faça logon, você provavelmente obterá este erro. Esse erro ocorre quando há algum tipo de erro de rede em seu sistema. | Certifique-se de que é possível conectar-se de um navegador para um site público e que as portas estejam abertas conforme especificado nos [Pré-requisitos do Proxy de Aplicativo](active-directory-application-proxy-enable.md). |
| Falha no registro do conector: verifique se o computador está conectado à Internet. Erro: “não havia nenhum ponto de extremidade escutando em `https://connector.msappproxy.net:9090/register/RegisterConnector` para aceitar a mensagem. Isso geralmente é causado por um endereço incorreto ou ação SOAP. Consulte InnerException, se existir, para obter mais detalhes. " | Se você efetuar logon usando seu nome de usuário e a senha do AD do Azure, mas ainda assim receber esse erro, pode ser que todas as portas acima de 8081 estejam bloqueadas. | Certifique-se de que as portas necessárias estejam abertas. Para obter mais informações, consulte [Pré-requisitos do Proxy de Aplicativo](active-directory-application-proxy-enable.md). |
| Apagar erro é apresentado na janela de registro. Não é possível continuar, somente é possível fechar a janela | Você inseriu o nome de usuário ou a senha incorretos. | Tente novamente. |
| Falha no registro de conector: verifique se você habilitou o Proxy de Aplicativo no Portal de Gerenciamento do Azure e se inseriu o nome de usuário e a senha do Active Directory corretamente. Erro: “AADSTS50059: nenhuma informação de identificação de locatário foi encontrada na solicitação nem está implícita em quaisquer credenciais fornecidas; a pesquisa por URI de principio de serviço falhou. | Você está tentando fazer logon usando uma Conta da Microsoft e não de um domínio que faz parte da ID da organização do diretório que você está tentando acessar. | Certifique-se de que o administrador faça parte do mesmo nome de domínio que o domínio do locatário, por exemplo, se o domínio do AD do Azure for contoso.com, o do administrador deve ser admin@contoso.com. |
| Falha ao recuperar a política de execução atual para executar scripts do PowerShell | Se a instalação do Conector falhar, verifique se a política de execução do PowerShell não está desabilitada. | Abra o Editor de Política de Grupo. Vá para Configuração do Computador > Modelos Administrativos > Componentes do Windows > Windows PowerShell e clique duas vezes em Ativar a Execução do Script. Isso pode ser definido como Não Configurado ou Habilitado. Se definido como Habilitado, certifique-se de que em a Política de Execução esteja definida como **Permitir scripts locais e scripts remotos assinados** ou **Permitir todos os scripts** em Opções. |
| Falha ao baixar a configuração do Conector | O certificado de cliente do Conector, que é usado para autenticação, expirou. Isso também pode ocorrer se você tiver o Conector instalado atrás de um proxy. Nesse caso, o Conector não poderá acessar a Internet e não será capaz de fornecer aplicativos a usuários remotos. | Renovar a confiança manualmente usando o cmdlet `Register-AppProxyConnector` do Windows PowerShell. Se seu Conector estiver atrás de um proxy, será necessário conceder acesso à Internet para as contas "serviços de rede" e "sistema local" do Conector. Isso pode ser feito concedendo acesso ao Proxy ou configurando-os para ignorar o proxy. |
| Falha no registro do conector: verifique se você é um Administrador Global do seu Active Directory para registrar o conector. Erro: “a solicitação de registro foi negada”. | O alias com o qual você está tentando fazer logon não é um administrador neste domínio. O Conector sempre é instalado para o diretório que possui o domínio do usuário. | Certifique-se de que o administrador com o qual você está tentando fazer logon tenha as permissões globais para o locatário do AD do Azure.|


## Erros de Kerberos

| Erro | Descrição | Resolução |
| --- | --- | --- |
| Falha ao recuperar a política de execução atual para executar scripts do PowerShell | Se a instalação do Conector falhar, verifique se a política de execução do PowerShell não está desabilitada. | Abra o Editor de Política de Grupo. Vá para Configuração do Computador > Modelos Administrativos > Componentes do Windows > Windows PowerShell e clique duas vezes em Ativar a Execução do Script. Isso pode ser definido como Não Configurado ou Habilitado. Se definido como Habilitado, certifique-se de que em a Política de Execução esteja definida como **Permitir scripts locais e scripts remotos assinados** ou **Permitir todos os scripts** em Opções. |
| 12008 - O Azure AD excedeu o número máximo de tentativas de autenticação Kerberos permitidas para o servidor back-end. | Esse evento pode indicar uma configuração incorreta entre o AD do Azure e o servidor de aplicativos de back-end ou um problema na configuração de data e hora nos dois computadores. | O servidor back-end recusou o tíquete Kerberos criado pelo AD do Azure. Verifique se a configuração do AD do Azure e do servidor de aplicativos de back-end estão definidas corretamente. Verifique se a configuração de data e hora no AD do Azure e no servidor de aplicativos back-end estão sincronizadas. |
| 13016 - O AD do Azure não pode recuperar um tíquete Kerberos em nome do usuário porque não há nenhum UPN no token de borda ou no cookie de acesso. | Há um problema com a configuração de STS. | Corrija a configuração de declaração UPN no STS. |
| 13019 - O AD do Azure não pode recuperar um tíquete Kerberos em nome do usuário devido ao seguinte erro geral da API. | Esse evento pode indicar uma configuração incorreta entre o AD do Azure e o servidor de controlador de domínio ou um problema na configuração de data e hora nos dois computadores. | O controlador de domínio recusou o tíquete Kerberos criado pelo AD do Azure. Verifique se a configuração do AD do Azure e do servidor de aplicativos de back-end estão definidas corretamente, especialmente a configuração de SPN. Verifique se que o AD do Azure é o domínio ingressado no mesmo domínio que o controlador de domínio para garantir que este estabeleça a relação de confiança com o AD do Azure. Verifique se a configuração de data e hora no AD do Azure e no controlador de domínio estão sincronizadas. |
| 13020 - O AD do Azure não pode recuperar um tíquete Kerberos em nome do usuário porque o SPN do servidor de back-end não está definido. | Esse evento pode indicar uma configuração incorreta entre o AD do Azure e o servidor de controlador de domínio ou um problema na configuração de data e hora nos dois computadores. | O controlador de domínio recusou o tíquete Kerberos criado pelo AD do Azure. Verifique se a configuração do AD do Azure e do servidor de aplicativos de back-end estão definidas corretamente, especialmente a configuração de SPN. Verifique se que o AD do Azure é o domínio ingressado no mesmo domínio que o controlador de domínio para garantir que este estabeleça a relação de confiança com o AD do Azure. Verifique se a configuração de data e hora no AD do Azure e no controlador de domínio estão sincronizadas. |
| 13022 - O AD do Azure não pode autenticar o usuário porque o servidor de back-end responde às tentativas de autenticação Kerberos com um erro HTTP 401. | Esse evento pode indicar uma configuração incorreta entre o AD do Azure e o servidor de aplicativos de back-end ou um problema na configuração de data e hora nos dois computadores. | O servidor back-end recusou o tíquete Kerberos criado pelo AD do Azure. Verifique se a configuração do AD do Azure e do servidor de aplicativos de back-end estão definidas corretamente. Verifique se a configuração de data e hora no AD do Azure e no servidor de aplicativos back-end estão sincronizadas. |
| O site não pode exibir a página. | O usuário pode obter esse erro ao tentar acessar o aplicativo publicado se o aplicativo for um aplicativo IWA. O SPN definido para este aplicativo pode estar incorreto. | Para aplicativos IWA: certifique-se de que o SPN configurado para este aplicativo esteja correto. |
| O site não pode exibir a página. | O usuário pode obter esse erro ao tentar acessar o aplicativo publicado se o aplicativo for um aplicativo do OWA. Isso pode ser causado por um dos seguintes motivos: | Etapas para uma atenuação adequada: |
| | O SPN definido para este aplicativo está incorreto. | Certifique-se de que o SPN configurado para este aplicativo esteja correto. |
| | O usuário que tentou acessar o aplicativo está usando uma conta da Microsoft em vez da conta corporativa apropriada para entrar, ou o usuário é um usuário convidado. | Verifique se o usuário faz logon usando sua conta corporativa correspondente ao domínio do aplicativo publicado. Convidados e usuários de Conta da Microsoft não podem acessar aplicativos IWA. |
| | O usuário que tentou acessar o aplicativo não está definido corretamente para esse aplicativo no lado do local. | Certifique-se de que esse usuário tenha as permissões apropriadas, conforme definido para este aplicativo de back-end no computador local. |
| Não foi possível acessar este aplicativo corporativo. Você não está autorizado a acessar este aplicativo. Falha na autorização. Certifique-se de atribuir o acesso a este aplicativo ao usuário. | O usuário pode obter esse erro ao tentar acessar o aplicativo publicado se o usuário que tentou acessar o aplicativo estiver usando uma Conta da Microsoft em vez da conta corporativa apropriada para entrar, ou se o usuário for um usuário convidado. | Convidados e usuários de Conta da Microsoft não podem acessar aplicativos IWA. Verifique se o usuário faz logon usando sua conta corporativa correspondente ao domínio do aplicativo publicado. |
| Não foi possível acessar esse aplicativo corporativo no momento. Tente novamente mais tarde... O conector atingiu o tempo limite. | O usuário pode obter esse erro ao tentar acessar o aplicativo publicado se o usuário que tentou acessar o aplicativo não estiver definido corretamente para esse aplicativo localmente. | Certifique-se de que esse usuário tenha as permissões apropriadas, conforme definido para este aplicativo de back-end no computador local. |


## Consulte também

[Usando o proxy de aplicativo para publicar aplicativos para acesso remoto seguro](active-directory-application-proxy-configure.md)

[Habilitar os serviços do Proxy de Aplicativo](active-directory-application-proxy-enable.md)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/ConnectorProperties.jpg
[2]: ./media/active-directory-application-proxy-troubleshoot/SessionLog.jpg

<!---HONumber=August15_HO9-->