<properties
	pageTitle="Exibir relatórios de acesso e uso | Microsoft Azure"
	description="Explica como exibir relatórios de acesso e uso para obter informações sobre a integridade e a segurança do diretório da organização."
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="kenhoff;Justinha;curtand"/>

# Exibir relatórios de acesso e uso

Você pode usar os relatórios de uso e de acesso do Active Directory do Azure para obter visibilidade quanto à integridade e a segurança do diretório da sua organização. Com essas informações, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem estar, de modo que pode fazer planos adequados para mitigar esses riscos.

No Portal de Gerenciamento do Azure, os relatórios são categorizados das seguintes maneiras:

- Relatórios de anomalias – contêm eventos de entrada que nós identificamos como anômalos. Nosso objetivo é que você fique ciente dessas atividades e permitir que você possa tomar uma decisão quanto a um evento ser suspeito ou não.
- Relatórios de aplicativos integrados – fornece um panorama de como os aplicativos em nuvem estão sendo usados na sua organização. O Active Directory do Azure oferece integração com milhares de aplicativos em nuvem.
- Relatórios de erros – indicam erros que podem ocorrer ao provisionar contas para aplicativos externos.
- Relatórios específicos do usuário – exibem dados de atividade de entrada/dispositivo de vídeo de um usuário específico.
- Logs de atividades – contêm um registro de todos os eventos auditados no últimas 24 horas, últimos 7 dias ou últimos 30 dias, bem como alterações no grupo de atividade e atividades de registro e redefinição de senha.

> [AZURE.NOTE]
>
- Alguns relatórios avançados de uso de recursos e anomalias estão disponíveis somente quando você habilita o [Azure Active Directory Premium](active-directory-get-started-premium.md). Relatórios avançados ajudam a melhorar a segurança de acesso, responder às ameaças potenciais e obter acesso a análises sobre o uso do aplicativo e acesso do dispositivo.
- As Azure Active Directory Premium e Basic estão disponíveis para clientes na China usando a instância mundial do Active Directory do Azure. As edições Azure Active Directory Premium e Basic não têm suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](http://feedback.azure.com/forums/169401-azure-active-directory).

## Relatórios

|	Relatório |	Descrição |
|	------												|	-----																						|
|	**Relatórios de atividades anômalas**
|	[Entradas de fontes desconhecidas](active-directory-reporting-sign-ins-from-unknown-sources.md) |	Pode indicar uma tentativa de conexão sem rastreamento. |
|	[Entradas após várias falhas](active-directory-reporting-sign-ins-after-multiple-failures.md) |	Pode indicar um ataque de força bruta com êxito. |
|	[Entradas de várias regiões geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md) |	Pode indicar que vários usuários estão se conectando com a mesma conta. |
|	[Entradas de endereços IP com atividade suspeita](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md) |	Pode indicar uma conexão bem-sucedida após uma tentativa de invasão prolongada. |
|	[Entradas de dispositivos possivelmente infectados](active-directory-reporting-sign-ins-from-possibly-infected-devices.md) |	Pode indicar uma tentativa de conexão de dispositivos possivelmente infectados. |
|	[Atividade de conexão anômala](active-directory-reporting-irregular-sign-in-activity.md) |	Pode indicar eventos anormais para padrões de conexão dos usuários. |
|	[Usuários com atividade de entrada anômala](active-directory-reporting-users-with-anomalous-sign-in-activity.md) |	Indica os usuários cujas contas podem ter sido comprometidas. |
|	Usuários com credenciais insuficientes |	Usuários com credenciais insuficientes |
|	**Logs de atividade**
|	Relatório de auditoria |	Eventos auditados em seu diretório |
|	Atividade de redefinição de senha |	Fornece uma exibição detalhada das redefinições de senha que acontecem em sua organização. |
|	Atividade de registro de redefinição de senha |	Fornece uma exibição detalhada dos registros de redefinições de senha que acontecem em sua organização. |
|	Atividade de grupos de autoatendimento |	Fornece um log de atividades para todas as atividades de autoatendimento de grupo em seu diretório |
|	**Aplicativos integrados**
|	Uso do aplicativo |	Fornece um resumo do uso de todos os aplicativos SaaS integrados ao seu diretório. |
|	Atividade de provisionamento de conta |	Fornece um histórico de tentativas para provisionar contas a aplicativos externos. |
|	Status de substituição de senha |	Fornece uma visão geral detalhada do status de substituição automática de senha de aplicativos SaaS. |
|	Erros de provisionamento de conta |	Indica um impacto sobre o acesso dos usuários para aplicativos externos. |
|	**Gerenciamento de direitos**
|	Uso do RMS |	Fornece um resumo de uso do Rights Management |
|	Usuários RMS mais ativos |	Lista os primeiros 1.000 usuários ativos que acessaram arquivos protegidos por RMS |
|	Uso de dispositivo do RMS |	Lista dispositivos usados para acessar arquivos protegidos por RMS |
|	Uso de aplicativos habilitados para RMS |	Fornece uso de aplicativos habilitados para RMS |

## Edições de relatório

|	Relatório |	Grátis |	Básico |	Premium |
|	------												|	----	|	-----	|	--------	|
|	**Relatórios de atividades anômalas**
|	Entradas de fontes desconhecidas |	✓ |	✓ |	✓ |
|	Entradas após várias falhas |	✓ |	✓ |	✓ |
|	Entradas de várias regiões geográficas |	✓ |	✓ |	✓ |
|	Entradas de endereços IP com atividade suspeita | | |	✓ |
|	Entradas de dispositivos possivelmente infectados | | |	✓ |
|	Atividade de conexão anômala | | |	✓ |
|	Usuários com atividade de entrada anômala | | |	✓ |
|	Usuários com credenciais insuficientes | | |	✓ |
|	**Logs de atividade**
|	Relatório de auditoria |	✓ |	✓ |	✓ |
|	Atividade de redefinição de senha | | |	✓ |
|	Atividade de registro de redefinição de senha | | |	✓ |
|	Atividade de grupos de autoatendimento | | |	✓ |
|	**Aplicativos integrados**
|	Uso do aplicativo | | |	✓ |
|	Atividade de provisionamento de conta |	✓ |	✓ |	✓ |
|	Status de substituição de senha | | |	✓ |
|	Erros de provisionamento de conta |	✓ |	✓ |	✓ |
|	**Gerenciamento de direitos**
|	Uso do RMS | | |	Somente RMS |
|	Usuários RMS mais ativos | | |	Somente RMS |
|	Uso de dispositivo do RMS | | |	Somente RMS |
|	Uso de aplicativos habilitados para RMS | | |	Somente RMS |



## Relatórios de atividades anômalas
<p>Os relatórios de atividade de entrada anômala contêm atividade de entrada suspeita para o Portal de Gerenciamento do Azure, o Painel de Acesso do AD do Azure, o Office365, o Sharepoint Online, o Dynamics CRM Online e outros serviços online da Microsoft.</p>
<p>Todos esses relatórios, exceto o relatório "Entradas após várias falhas", também sinalizam entradas <i>federadas</i> suspeitas nos serviços mencionados anteriormente, independentemente do provedor de federação. </p>
<p>Os seguintes relatórios estão disponíveis: </p><ul> <li>[Entradas de fontes desconhecidas](active-directory-reporting-sign-ins-from-unknown-sources.md).</li> <li>[Entradas após várias falhas](active-directory-reporting-sign-ins-after-multiple-failures.md).</li> <li>[Entradas de várias regiões geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md).</li> <li>[Entradas de endereços IP com atividade suspeita](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md).</li> <li>[Atividade de conexão anômala](active-directory-reporting-irregular-sign-in-activity.md).</li> <li>[Entradas de dispositivos possivelmente infectados](active-directory-reporting-sign-ins-from-possibly-infected-devices.md).</li> <li>[Usuários com atividade de entrada anômala](active-directory-reporting-users-with-anomalous-sign-in-activity.md).</li> <li>Usuários com credenciais insuficientes</li></ul>










## Logs de atividade

### Relatório de auditoria

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Mostra um registro de todos os eventos auditados durante as últimas 24 horas, últimos 7 dias ou últimos 30 dias. <br /> Para obter mais informações, consulte [Eventos de relatório de auditoria do Active Directory do Azure](active-directory-reporting-audit-events.md). | Diretório > guia Relatórios |

![Relatório de auditoria](./media/active-directory-view-access-usage-reports/auditReport.PNG)

### Atividade de redefinição de senha

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Mostra todas as tentativas de redefinição de senha que ocorreram na organização. | Diretório > guia Relatórios |

![Atividade de redefinição de senha](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)

### Atividade de registro de redefinição de senha

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Mostra todos os registros de redefinição de senha que ocorreram na organização | Diretório > guia Relatórios |

![Atividade de registro de redefinição de senha](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)

### Atividade de grupos de autoatendimento

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Mostra todas as atividades para os grupos gerenciados por autoatendimento em seu diretório. | Diretório > Usuários > <i>Usuário</i> > guia Dispositivos |

![Atividade de grupos de autoatendimento](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)











## Relatórios de aplicativos integrados

### Uso do aplicativo: resumo

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Use este relatório quando desejar consultar o uso de todos os aplicativos SaaS em seu diretório. Este relatório está baseado no número de vezes que os usuários clicaram no aplicativo no Painel de Acesso. | Diretório > guia Relatórios |

![Resumo de uso do aplicativo](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)


### Uso do aplicativo: detalhado

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Use este relatório para ver o quanto um aplicativo SaaS específico está sendo usado. Este relatório está baseado no número de vezes que os usuários clicaram no aplicativo no Painel de Acesso. | Diretório > guia Relatórios |

### Painel do aplicativo

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Este relatório indica entradas cumulativas no aplicativo pelos usuários em sua organização, durante um intervalo de tempo selecionado. O gráfico na página do painel ajudará você a identificar tendências para toda a utilização desse aplicativo. | Diretório > Aplicativo > guia Painel |

## Relatórios de erros

### Erros de provisionamento de conta

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Use isto para monitorar os erros que ocorrem durante a sincronização de contas de aplicativos SaaS para o Active Directory do Azure. | Diretório > guia Relatórios |

![Erros de provisionamento de conta](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)









## Relatórios específicos de usuário

### Dispositivos

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Use este relatório quando desejar ver o endereço IP e a localização geográfica de dispositivos que um usuário específico utilizou para acessar o Active Directory do Azure. | Diretório > Usuários > <i>Usuário</i> > guia Dispositivos |

### Atividade

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Mostra a atividade de entrada de um usuário. O relatório inclui informações como o aplicativo conectado ao dispositivo utilizado, o endereço IP e o local. Não coletamos o histórico de usuários que realizam a entrada com uma conta da Microsoft. | Diretório > Usuários > <i>Usuário</i> > guia Atividade |

#### Inscreva-se em eventos incluídos no relatório de Atividade de Usuário

Somente determinados tipos de eventos de entrada serão exibidos no relatório de Atividade de Usuário.

| Tipo de evento | Incluso? |
| ----------------------								| ---------		|
| Entradas no [Painel de Acesso](http://myapps.microsoft.com/) | Sim |
| Entradas no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/) | Sim |
| Entradas no [Portal do Microsoft Azure](http://portal.azure.com/) | Sim |
| Entradas no [Portal do Office 365](http://portal.office.com/) | Sim |
| Entradas em um aplicativo nativo, como o Outlook (consulte exceção abaixo) | Sim |
| Entradas em um aplicativo federado/provisionado por meio do Painel de Acesso, como Salesforce | Sim |
| Entradas em um aplicativo baseado em senha por meio do Painel de Acesso, como o Twitter | Sim |
| Realiza a entrada em um aplicativo de negócios personalizado que foi adicionado ao diretório | Não (em breve) |
| Conexões em um aplicativo de proxy de aplicativo do Azure AD que foi adicionado ao diretório | Não (em breve) |

> Observação: Para reduzir a quantidade de ruído no relatório, as entradas no aplicativo nativo [Lync/Skype for Business](http://products.office.com/pt-BR/skype-for-business/online-meetings) e o [Assistente de Conexão do Microsoft Online Services](http://community.office365.com/pt-BR/w/sso/534.aspx) não são mostradas.










## Coisas a considerar se você suspeitar de violação de segurança

Se você suspeitar que uma conta de usuário pode estar comprometida ou qualquer tipo de atividade de usuário suspeita que pode levar a uma violação de segurança de seus dados de diretório na nuvem, você talvez queira considerar uma ou mais das seguintes ações:

- Contate o usuário para verificar a atividade
- Redefinir a senha do usuário
- [Habilitar a Multi-Factor Authentication](http://go.microsoft.com/fwlink/?linkid=335774) para segurança adicional

## Exibir ou baixar uma fatura

1. No Portal de Gerenciamento do Azure, clique em **Active Directory** e no nome do diretório da sua organização, então clique em **Relatórios**.
2. Na página Relatórios, clique no relatório que você deseja exibir e/ou baixar.

    > [AZURE.NOTE]Se esta for a primeira vez que você usa o recurso de relatório do Active Directory do Azure, verá uma mensagem para aceitar. Se você concordar, clique no ícone de marca de seleção para continuar.

3. Clique no menu suspenso ao lado de Intervalo e, em seguida, selecione um dos seguintes intervalos de tempo que deverá ser usado ao gerar este relatório:
    - Últimas 24 horas
    - Últimos 7 dias
    - Últimos 30 dias
4. Clique no ícone de marca de seleção para executar o relatório.
	- Até 1.000 eventos serão mostrados no Portal de Gerenciamento do Azure.
5. Se aplicável, clique em **Baixar** para baixar o relatório em um arquivo compactado no formato de valores separados por vírgulas (CSV) para visualização offline ou para fins de arquivamento.
	- Até 75.000 eventos serão incluídos no arquivo baixado.

## Ignorar um evento

Se você estiver exibindo os relatórios de anomalias, perceba que você pode ignorar vários eventos que aparecem em relatórios relacionados. Para ignorar um evento, basta destacar o evento no relatório e, em seguida, clicar em **Ignorar**. O botão **Ignorar** removerá permanentemente o evento realçado do relatório e só pode ser usado por administradores globais licenciados.

## Notificações automáticas por email

Para saber mais sobre as notificações de relatórios do Azure AD, confira [Notificações de relatórios do Active Directory do Azure](active-directory-reporting-notifications.md).

## O que vem a seguir

- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)

<!---HONumber=Sept15_HO4-->