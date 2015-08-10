<properties 
	pageTitle="Requisitos do Azure AD Connect Health" 
	description="Esta é a página doAzure AD Connect Health que descreve os requisitos e instalação do agente." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015" 
	ms.author="billmath"/>

# Requisitos de Azure Active Directory Connect Health
A documentação a seguir é uma lista de requisitos que devem ser atendidos antes que você pode prosseguir com a Azure AD Connect Health.



## Uma licença do Azure AD Premium

O Azure AD Connect Health é um recurso do Azure AD Premium e exige uma licença do Azure AD Premium. Para obter uma licença, consulte Introdução ao AD Premium do Azure.
 

## Você deve ser um administrador global do seu locatário do AD do Azure.

Por padrão, os administradores globais têm acesso às informações fornecidas pelo Azure AD Connect Health. Se você não for um administrador global do seu locatário do AD do Azure que é agrupado com o Active Directory local, você não poderá criar uma instância do serviço do Azure AD Connect Health. Certifique-se de que você seja um administrador global. Para obter informações adicionais, consulte [Administrando seu diretório Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx).
 

## O agente do Azure AD Connect Health instalado em cada servidor de destino

O Azure AD Connect Health requer que um agente esteja instalado nos servidores de destino para fornecer os dados que serão exibidos no portal do Azure AD Connect Health. Isso significa que, para obter dados sobre sua infraestrutura do AD FS no local, o agente deve estar instalado nos servidores do AD FS. Isso inclui servidores de Proxy do AD FS e servidores de Proxy de aplicativo Web. Para obter informações sobre como instalar o Azure AD Connect Health, consulte as etapas de instalação do agente do Azure AD Connect Health.


## Requisitos do agente Azure AD Connect Health

As seções a seguir descrevem os requisitos específicos do agente do Azure AD Connect Health.
 

### Download do Agente de Integridade do Azure AD Connect

Para começar a usar a Integridade do o Azure AD Connect, você pode baixar a versão mais recente do agente aqui: [Baixar o Agente de Integridade do Azure AD Connect.](http://go.microsoft.com/fwlink/?LinkID=518973) Verifique se você adicionou o serviço do Marketplace antes de instalar os agentes.

 
### Conectividade de saída para os pontos de extremidade de serviço do Azure
Durante a instalação e o tempo de execução, o agente requer conectividade com os pontos de extremidade de serviço do Azure AD Connect Health listados a seguir. Se você bloquear a conectividade de saída, certifique-se de que os recursos a seguir estão adicionados à lista de permissões:

- *.servicebus.windows.net - Porta: 5671 - https://*.adhybridhealth.azure.com/
- https://*.table.core.windows.net/
- https://policykeyservice.dc.ad.msft.net/
- https://login.windows.net
- https://login.microsoftonline.com
- https://secure.aadcdn.microsoftonline-p.com 

## Permita os sites a seguir se a segurança reforçada do IE estiver habilitada
Os sites a seguir precisam ser permitidos se a segurança reforçada do IE estiver habilitada no servidor que terá o agente instalado.

- https://login.microsoftonline.com 
- https://secure.aadcdn.microsoftonline-p.com
- https://login.windows.net
- O servidor de federação para a sua organização confiável pelo Azure Active Directory para o exemplo:https://sts.contoso.com 


### Para o AD FS, a auditoria do AD FS deve ser ativada para usar a Análise de uso

Para que o recurso de Análise de uso colete e analise dados, o agente do Azure AD Connect Health precisa das informações nos Logs de auditoria do AD FS. Esses logs não estão habilitados por padrão. Isso se aplica apenas aos servidores de Federação do AD FS. Você não precisa habilitar a auditoria em servidores de Proxy de aplicativo Web ou servidores Proxy do AD FS. Use os procedimentos a seguir para habilitar a auditoria do AD FS e localizar os logs de auditoria do AD FS.

#### Para habilitar a auditoria do AD FS 2.0

1. Clique em **Iniciar**aponte para**Programas**, aponte para**Ferramentas administrativas**e, em seguida, clique em**Política de Segurança Local**.
1. Navegue até a pasta **Configurações de segurança\\Políticas locais\\Gerenciamento de direitos de usuário** e, em seguida, clique duas vezes em Gerar auditorias de segurança.
1. Na Guia **Configuração de segurança Local**, verifique se a conta de serviço 2.0 do AD FS está listada. Se não estiver presente, clique em **Adicionar usuário ou grupo**e adicione-a à lista e, em seguida, clique em **OK**.
1. Abra um prompt de comando com privilégios elevados e execute o seguinte comando para habilitar a auditoria.`auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable`
1. Feche a Política de segurança Local e, em seguida, abra o snap-in de Gerenciamento. Para abrir o snap-in de Gerenciamento, clique em **Iniciar**aponte para **Programas**aponte para **Ferramentas administrativas ** e, em seguida, clique em Gerenciamento do AD FS 2.0
1. No painel de ações, clique em Editar propriedades do serviço de federação.
1. Na caixa de diálogo **Propriedades do Serviço de Federação**, clique na guia **Eventos**.
1. Selecione as caixas de seleção **Auditorias com êxito**e**Auditorias com falha**.
1. Clique em **OK**.

#### Para habilitar a auditoria do AD FS no Windows Server 2012 R2

1. Abra**Política de Segurança Local**abrindo**Gerenciador do Servidor**na tela Iniciar, ou Gerenciador de servidores na barra de tarefas na área de trabalho, clique em **Ferramentas/Política de Segurança Local**.
1. Navegue até a pasta **Configurações de segurança\\Políticas locais\\Atribuição de direitos do usuário** e clique duas vezes em**Gerar auditorias de segurança**.
1. Na guia **Configuração de Segurança Local**, verifique se a conta de serviço do AD FS está listada. Se não estiver presente, clique em **Adicionar usuário ou grupo**e adicione-a à lista e, em seguida, clique em **OK**.
1. Abra um prompt de comando com privilégios elevados e execute o comando a seguir para habilitar a auditoria:`auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.`
1. Feche **Política de Segurança Local**e, em seguida, abra o snap-in de **Gerenciamento do AD FS** (no Gerenciador de servidores, clique em ferramentas e, em seguida, selecione Gerenciamento do AD FS).
1. No painel de ações, clique em**Editar propriedades do serviço de Federação**.
1. Na caixa de diálogo Propriedades do serviço de federação, clique na guia **Eventos**.
1. Selecione as caixas de seleção **Auditorias com êxito e Falha auditorias**e, em seguida, clique em**OK**.






#### Para localizar os logs de auditoria do AD FS


1. Abra o **Visualizador de Eventos**.</li>
1. Vá para Logs do Windows e selecione **Segurança**.
1. À direita, clique em **Filtrar logs atuais**.
1. Em Origem do Evento, selecione **Auditoria do AD FS**.

![Logs de auditoria do AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]Se você tiver uma política de grupo que esteja desabilitando a auditoria do AD FS, o agente do Azure AD Connect Health não poderá coletar informações. Certifique-se de que você não tem uma política de grupo que pssa desativar a auditoria.


### Instalação do agente em servidores do Windows Server 2008 R2

Para servidores Windows Server 2008 R2 faça o seguinte:

1. Certifique-se de que o servidor esteja em execução no Service Pack 1 ou superior.
1. Desative a ESC do IE para instalação do agente:
1. Instale o Windows PowerShell 4.0 em cada um dos servidores antes de instalar o agente de integridade do AD. Para instalar o Windows PowerShell 4.0:
 - Instale o [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779)usando o link a seguir para baixar o instalador offline.
 - Instale o PowerShell ISE (de recursos do Windows)
 - Instale o[Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Instale o Internet Explorer versão 10 ou superior no servidor. Isso é necessário para que o serviço de integridade faça sua autenticação usando suas credenciais de administrador do Azure.
1. Para obter informações adicionais sobre como instalar o Windows PowerShell 4.0 no Windows Server 2008 R2, consulte o artigo no wiki [aqui](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

## Implantação do agente do Azure AD Connect Health
Esta seção vai orientá-lo a instalar e configurar o agente do Azure AD Connect Health em seus servidores. Lembre-se de que, antes de você ver todos os dados em sua instância do Azure AD Connect Health, é necessário instalar o agente do Azure AD Connect Health nos servidores de destino. Certifique-se de concluir os requisitos acima antes de instalar o agente. Você pode baixar o agente usando o link acima e, em seguida, siga as etapas abaixo.


Clique duas vezes no arquivo .exe que você baixou. Na primeira tela, clique em instalar.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Depois que a instalação for concluída, clique em Configurar agora.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Isso iniciará um prompt de comando, seguido pelo PowerShell que executará Register-AzureADConnectHealthADFSAgent. Você será solicitado a entrar no Azure. Vá em frente e entre.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Depois de entrar, o PowerShell continuará. Depois de concluir feche o PowerShell e a configuração está concluída.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

Nesse ponto, os serviços devem ser iniciados automaticamente e o agente estará agora monitorando e coletando dados. Lembre-se de que você verá avisos na janela do PowerShell caso não atenda a todos os pré-requisitos descritos nas seções anteriores. A seguinte captura de tela é um exemplo.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Para verificar se o agente foi instalado, abra serviços e procure pelo seguinte. Esses serviços devem ser executados se você concluir a configuração. Caso contrário, eles não serão iniciados até que a configuração esteja concluída.

- Serviço de diagnóstico do AD FS do Azure AD Connect Health
- Serviço do Insights do AD FS do Azure AD Connect Health
- Serviço de Monitoramento do AD FS do Azure AD Connect Health
 
![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

<!---HONumber=July15_HO5-->