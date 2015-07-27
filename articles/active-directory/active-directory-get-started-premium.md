<properties 
	pageTitle="Introdução ao Active Directory Premium do Azure" 
	description="Um tópico que explica como inscrever-se no Azure Active Directory Premium Edition." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Introdução ao Active Directory Premium do Azure

O Active Directory do Azure é oferecido em três edições: Gratuito, Basic e Premium. A edição gratuita é fornecida com uma assinatura do Azure ou do Office 365. As edições Basic e Premium estão disponíveis por meio de um programa [Microsoft Enterprise Agreement](https://www.microsoft.com/pt-br/licensing/licensing-programs/enterprise.aspx) ou de [Licenciamento por volume aberto](https://www.microsoft.com/pt-br/licensing/licensing-programs/open-license.aspx). Os assinantes do Azure e o Office 365 também podem adquirir o Active Directory Premium online. [Entre aqui](https://portal.office.com/Commerce/Catalog.aspx) para comprá-lo.

> [AZURE.NOTE]As edições Premium e Basic do Active Directory do Azure estão disponíveis para clientes na China usando a instância mundial do Active Directory do Azure. As edições Azure Active Directory Premium e Basic não têm suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](http://feedback.azure.com/forums/169401-azure-active-directory).

O Azure Active Directory Premium também está incluído na **Enterprise Mobility Suite**. A Enterprise Mobility Suite é um modo econômico para as organizações usarem o Microsoft Intune, o Azure Rights Management e o Active Directory Premium juntos em um único plano de licenciamento. Para obter mais informações, consulte o site [Enterprise Mobility Suite](https://www.microsoft.com/pt-br/server-cloud/enterprise-mobility/overview.aspx).

Para começar a usar os recursos do Active Directory Premium do Azure hoje, siga as etapas abaixo. As mesmas etapas aplicam-se à edição Basic do Active Directory do Azure.

## Etapa 1: inscrever-se no Active Directory Premium do Azure

Para se inscrever, consulte o site [Licenciamento por Volume](http://www.microsoft.com/pt-br/licensing/how-to-buy/how-to-buy.aspx).

## Etapa 2: ativar seu plano de licença

Se esta for a primeira vez que você comprou um plano de licença pelo programa de licenciamento por volume corporativo da Microsoft, você precisará primeiro ativar seu plano de licença antes que possa começar a atribuir licenças de dentro de seu diretório do Active Directory do Azure. Para fazer isso, você deve clicar em um dos links Inscrever-se ou Entrar encontrados no email de confirmação (veja abaixo) que você receberá após concluir sua primeira compra de plano de licença. Em qualquer compra subsequente para esse diretório, as licenças serão automaticamente ativadas no mesmo diretório.

![][1]

Se você tiver um locatário existente, selecione o link **Entrar** para entrar com sua conta de administrador existente. É importante que você entre com as credenciais de administrador global do diretório onde as licenças devem ser ativadas.

Se você deseja criar um novo locatário do Active Directory do Azure para usar com seu plano de licenciamento, você deve selecionar o link **Inscrever-se** que o levará para a tela a seguir.

![][2]

Quando você terminar o processo de inscrição ou o processo de entrada que começou a partir do email, você verá a tela a seguir confirmando que o plano de licença está sendo ativado para seu locatário.

![][3]

## Etapa 3: ativar o acesso ao Active Directory do Azure

Depois que as licenças estiverem provisionadas para o diretório, você receberá um email de boas-vindas (veja abaixo) que confirmará que você pode começar a gerenciar licenças e recursos do Active Directory Premium do Azure ou Enterprise Mobility Suite. Se você tiver usado o Microsoft Azure anteriormente, você poderá prosseguir para http://manage.windowsazure.com para atribuir as novas licenças (consulte a etapa 4 abaixo para obter mais instruções). Se você for novo no Microsoft Azure, selecionar o link Entrar no email ou então acessar a [Página de ativação do acesso ao Active Directory do Azure](https://account.windowsazure.com/signup?offer=MS-AZR-0110P) irá conduzi-lo por uma série de etapas para ajudá-lo a acessar o diretório por meio do Portal de Gerenciamento do Azure.

![][4]

Depois que você tiver se conectado com êxito, você precisará concluir a tela de autenticação de um segundo fator (abaixo), fornecendo um número de telefone celular e validando-o. Depois disso será possível ativar seu acesso ao Active Directory do Azure selecionando **Inscrever-se**.

![][5]

A ativação pode levar alguns minutos, conforme ilustrado abaixo; depois que o acesso for ativado, a barra marrom desaparecerá e você poderá clicar no link de Portal no canto superior direito ou navegar até o [Portal de Gerenciamento](http://manage.windowsazure.com).

![][6]

Nesse caso, o acesso ao Azure será limitado ao Active Directory do Azure.

![][7]

Você pode já ter tido acesso ao Azure em uma utilização anterior; além disso, você pode atualizar seu acesso do Active Directory do Azure para acesso completo ao Azure, ao ativar assinaturas adicionais do Azure. Nesses casos o Portal de Gerenciamento terá mais recursos, como demonstrado a seguir.

![][8]

Se você tentar ativar seu acesso ao Active Directory do Azure antes de receber o email de boas-vindas acima, você poderá receber a mensagem de erro a seguir. Tente novamente alguns minutos após ter recebido o email.

![][9]

Novos administradores em sua assinatura também podem ativar o acesso ao Portal de Gerenciamento por meio desse link.

## Etapa 4: atribuir licenças a contas de usuário

Antes de começar a usar o plano que você adquiriu, você precisará atribuir manualmente as licenças para contas de usuário em sua organização para que elas possam usar os recursos avançados fornecidos com a edição Premium. Use as etapas a seguir para atribuir licenças aos usuários para que possam usar os recursos do Active Directory Premium do Azure.

Para atribuir licenças aos usuários:

1. Entre no Portal de Gerenciamento como o administrador global do diretório que você quer personalizar.
2. Clique em **Active Directory** e, em seguida, selecione o diretório onde você deseja atribuir licenças.
3. Selecione a guia **Licenças** selecione **Active Directory Premium** ou **Enterprise Mobility Suite** e, em seguida, clique em **Atribuir**.

    ![][10]

4. Na caixa de diálogo, selecione os usuários para os quais você deseja atribuir licenças e, em seguida, clique no ícone de marca de seleção para salvar as alterações.

    ![][11]

## Restrições de licença

Alguns planos de licenças são subconjuntos ou superconjuntos de outros planos de licenças. Na maioria dos casos, não é possível atribuir a um usuário um plano de licença que já tenha sido atribuído a ele. Se for sua intenção atribuir um plano de licença que é um superconjunto, você precisará primeiro remover o plano de licença que é um subconjunto.

## Requisitos de licença

Quando você atribuir uma licença a um usuário, você pode especificar um local de uso primário nas propriedades da conta desse usuário, conforme mostrado abaixo. Se um local de uso não for especificado, o local do inquilino é atribuído automaticamente ao usuário.

![][12]

A disponibilidade dos serviços e recursos de um serviço de nuvem da Microsoft varia segundo o país ou região. Um serviço, como VoIP (Voice over Internet Protocol, voz por protocolo de Internet) pode estar disponível em um país ou região e não estar disponível em outro. Recursos dentro de um serviço podem ser limitados por motivos jurídicos em determinados países ou regiões. Para ver se um serviço ou recurso está disponível com ou sem restrições, procure seu país ou região no site de restrições de licença do serviço.

## O que vem a seguir

- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)
- [Exibir relatórios de acesso e de uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png


 

<!---HONumber=July15_HO3-->