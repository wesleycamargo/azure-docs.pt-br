<properties
	pageTitle="Como gerenciar certificados de federação no AD do Azure | Microsoft Azure"
	description="Saiba como personalizar a data de vencimento para seus certificados de federação e como renovar certificados que vencerão em breve."
	services="active-directory"
	documentationCenter=""
	authors="liviodlc"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="liviodlc"/>

#Gerenciamento de certificados para logon único federado no Active Directory do Azure

Este artigo aborda dúvidas comuns relacionadas aos certificados que o Active Directory do Azure cria para estabelecer logon único (SSO) federado para seus aplicativos SaaS.

Este artigo só é relevante para aplicativos que são configurados para usar o **Logon Único do AD do Azure**, conforme mostrado no exemplo a seguir:

![Logon único do AD do Azure](./media/active-directory-sso-certs/fed-sso.PNG)

##Como personalizar a data de vencimento para seu Certificado de Federação

Por padrão, os certificados são definidos para expirar depois de dois anos. É possível escolher uma data de vencimento diferente para o certificado seguindo as etapas abaixo. As capturas de tela incluídas usam o ServiceNow para fins de exemplo, mas essas etapas podem ser aplicadas a qualquer aplicativo SaaS federado.

1. No Active Directory do Azure, na página Início Rápido para seu aplicativo, clique em **Configurar logon único**.

	![Abra o assistente de configuração de SSO.](./media/active-directory-sso-certs/config-sso.png)

2. Selecione **Logon Único do AD do Azure** e, em seguida, clique em **Avançar**.

3. Digite a **URL de logon** do seu aplicativo e marque a caixa de seleção **Configurar o certificado usado para logon único federado**. Em seguida, clique em **Próximo**.

	![Logon único do AD do Azure](./media/active-directory-sso-certs/new-app-config-sso.PNG)

4. Na página seguinte, selecione **Gerar um novo certificado** e escolha por quanto tempo você deseja que o certificado seja válido. Em seguida, clique em **Próximo**.

	![Gere um novo certificado](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Em seguida, clique em **Baixar certificado**. Para saber como carregar o certificado para seu aplicativo SaaS específico, clique em **Exibir instruções de configuração**.

	![Baixe e depois carregue o certificado](./media/active-directory-sso-certs/new-app-config-app.PNG)

6. O certificado não será ativado até que você marque a caixa de seleção na parte inferior da caixa de diálogo de confirmação e depois pressione enviar.

##Como renovar um certificado que vencerá em breve

As etapas de renovação mostradas abaixo idealmente não devem resultar em nenhum tempo de inatividade significativo para seus usuários. As capturas de tela usadas nesta seção apresentam o Salesforce como exemplo, mas essas etapas podem ser aplicadas a qualquer aplicativo SaaS federado.

1. No Active Directory do Azure, na página Início Rápido para seu aplicativo, clique em **Configurar Logon Único**.

	![Abra o assistente de configuração de SSO](./media/active-directory-sso-certs/renew-sso-button.PNG)

2. Na primeira página da caixa de diálogo, **Logon Único do AD do Azure** já deve estar selecionado, então clique em **Avançar**.

3. Na segunda página, marque a caixa de seleção **Configurar o certificado usado para logon único federado**. Em seguida, clique em **Próximo**.

	![Logon único do AD do Azure](./media/active-directory-sso-certs/renew-config-sso.PNG)

4. Na página seguinte, selecione **Gerar um novo certificado** e escolha por quanto tempo você deseja que o novo certificado seja válido. Em seguida, clique em **Próximo**.

	![Gere um novo certificado](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Clique em **Baixar certificado**. Para renovar o certificado com êxito, é preciso realizar as etapas a seguir:

	- Carregue o novo certificado para a tela de configuração de logon único do aplicativo SaaS. Para saber como fazer isso para seu aplicativo SaaS específico, clique em **Exibir instruções de configuração**.

	- No AD do Azure, marque a caixa de seleção de confirmação na parte inferior da caixa de diálogo para habilitar o novo certificado e, em seguida, clique em **Avançar** para enviar.

	> [AZURE.IMPORTANT]O logon único para o aplicativo será desabilitado no momento em que uma dessas duas etapas for concluída, mas será habilitado novamente depois que a segunda etapa for concluída. Portanto, para minimizar o tempo de inatividade, prepare-se para realizar as duas etapas em um curto período de tempo entre si.

	![Baixe e depois carregue o certificado](./media/active-directory-sso-certs/renew-config-app.PNG)

##Consulte também

[Acesso a aplicativos e logon único no AD do Azure](active-directory-appssoaccess-whatis.md)

<!---HONumber=July15_HO5-->