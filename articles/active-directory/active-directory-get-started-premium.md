<properties
	pageTitle="Introdução ao Active Directory Premium do Azure"
	description="Um tópico que explica como inscrever-se no Azure Active Directory Premium Edition."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo" 
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/18/2016"
	ms.author="markvi"/>

# Introdução ao Active Directory Premium do Azure

O Active Directory do Azure é oferecido em três edições: Gratuito, Basic e Premium. A edição gratuita é fornecida com uma assinatura do Azure ou do Office 365. As edições Basic e Premium estão disponíveis por meio de um programa [Microsoft Enterprise Agreement](https://www.microsoft.com/pt-BR/licensing/licensing-programs/enterprise.aspx) ou de [Licenciamento por volume aberto](https://www.microsoft.com/pt-BR/licensing/licensing-programs/open-license.aspx). Os assinantes do Azure e o Office 365 também podem adquirir o Active Directory Premium online. [Entre aqui](https://portal.office.com/Commerce/Catalog.aspx) para comprá-lo.

> [AZURE.NOTE]
As edições Premium e Basic do Active Directory do Azure estão disponíveis para clientes na China usando a instância mundial do Active Directory do Azure. As edições Azure Active Directory Premium e Basic não têm suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).

O Azure Active Directory Premium também está incluído na **Enterprise Mobility Suite**. O Enterprise Mobility Suite é uma maneira econômica para as organizações usarem os serviços a seguir em conjunto sob um plano de licenciamento.

- Active Directory Premium 
- Azure Rights Management
- Microsoft Intune


Para obter mais informações, consulte o site [Enterprise Mobility Suite](https://www.microsoft.com/pt-BR/server-cloud/enterprise-mobility/overview.aspx).

Para começar a usar os recursos do Azure Active Directory Premium hoje, siga as etapas das próximas seções. As mesmas etapas também se aplicam à edição Basic do Azure Active Directory.

## Etapa 1: inscrever-se no Active Directory Premium do Azure

Para se inscrever, consulte o site [Licenciamento por Volume](http://www.microsoft.com/pt-BR/licensing/how-to-buy/how-to-buy.aspx).

## Etapa 2: ativar seu plano de licença

Quando sua primeira compra de plano de licença por meio do programa de Licenciamento por Volume Enterprise da Microsoft tiver sido concluída, você receberá um email de confirmação. Você precisará desse email para ativar seu primeiro plano de licença. Em qualquer compra subsequente para esse diretório, as licenças são automaticamente ativadas no mesmo diretório.

Para iniciar a ativação, clique em **Entrar** ou em **Inscrever-se**.


![][1]

Se você tiver um locatário existente, clique em **Entrar** para entrar com sua conta de administrador existente. É importante que você entre com as credenciais de administrador global do diretório onde as licenças devem ser ativadas.

Se você quiser criar um novo locatário do Azure Active Directory para usar com seu plano de licenciamento, clique em **Inscrever-se** para abrir o diálogo **Criar Perfil de Conta**.

![][2]

Quando terminar, o diálogo a seguir será mostrado como a confirmação da ativação do plano de licença para seu locatário.

![][3]

## Etapa 3: ativar o acesso ao Active Directory do Azure

Quando as licenças forem provisionadas no diretório, um **email de boas-vindas** será enviado a você. O email confirma que você pode começar a gerenciar suas licenças e seus recursos do Azure Active Directory Premium ou do Enterprise Mobility Suite.

Se você tiver usado o Microsoft Azure anteriormente, poderá prosseguir para [http://manage.windowsazure.com](http://manage.windowsazure.com) para atribuir as novas licenças (veja a [Etapa 4](#step-4-assign-license-to-user-accounts) abaixo para obter mais detalhes).

Se você não tiver experiência no Microsoft Azure, poderá clicar em **Entrar** no email ou acessar a [página de ativação Acesso ao Azure Active Directory](https://account.windowsazure.com/signup?offer=MS-AZR-0110P). Ambos os métodos guiam você por uma série de etapas para ajudá-lo a acessar seu diretório por meio do portal clássico do Azure.

![][4]

Quando você entrar com êxito, precisará concluir a tela de autenticação de um segundo fator, fornecendo um número de telefone celular e validando-o. Quando você tiver concluído a verificação de celular, será possível ativar seu acesso ao Azure Active Directory clicando em **Inscrever-se**.

![][5]

A ativação pode levar alguns minutos. Quando o acesso estiver ativo, a barra marrom desaparecerá e você poderá clicar em **Portal**.

![][6]

Nesse caso, o acesso ao Azure será limitado ao Azure Active Directory.

![][7]

Você pode já ter tido acesso ao Azure em uma utilização anterior; além disso, você pode atualizar seu acesso do Active Directory do Azure para acesso completo ao Azure, ao ativar assinaturas adicionais do Azure. Nesses casos, o portal clássico do Azure tem mais recursos.

![][8]

Se você tentar ativar seu acesso ao Azure Active Directory antes de receber o email de boas-vindas, você poderá receber a mensagem de erro a seguir. Tente novamente alguns minutos após ter recebido o email.

![][9]

Os novos administradores em sua assinatura também podem ativar o acesso ao portal clássico do Azure por meio desse link.

## Etapa 4: atribuir licenças a contas de usuário

Antes de começar a usar o plano que você adquiriu, você precisará atribuir manualmente as licenças para contas de usuário em sua organização para que elas possam usar os recursos avançados fornecidos com a edição Premium. Use as etapas a seguir para atribuir licenças aos usuários para que possam usar os recursos do Active Directory Premium do Azure.

Para atribuir licenças aos usuários:

1. Entre no portal clássico do Azure como o administrador global do diretório que você quer personalizar.
2. Clique em **Active Directory** e, em seguida, selecione o diretório onde você deseja atribuir licenças.
3. Selecione a guia **Licenças** selecione **Active Directory Premium** ou **Enterprise Mobility Suite** e, em seguida, clique em **Atribuir**.

    ![][10]

4. Na caixa de diálogo, selecione os usuários para os quais você deseja atribuir licenças e, em seguida, clique no ícone de marca de seleção para salvar as alterações.

    ![][11]

## Restrições de licença

Alguns planos de licenças são subconjuntos ou superconjuntos de outros planos de licenças. Normalmente, não é possível atribuir a um usuário um plano de licença que já tenha sido atribuído a ele. Se for sua intenção atribuir um plano de licença que é um superconjunto, você precisará primeiro remover o plano de licença que é um subconjunto.

## Requisitos de licença

Quando você atribuir uma licença a um usuário, você pode especificar um local de uso primário nas propriedades da conta desse usuário. Se um local de uso não for especificado, o local do inquilino é atribuído automaticamente ao usuário.

![][12]

A disponibilidade dos serviços e recursos de um serviço de nuvem da Microsoft varia segundo o país ou região. Um serviço, como VoIP (Voice over Internet Protocol, voz por protocolo de Internet) pode estar disponível em um país ou região e não estar disponível em outro. Recursos dentro de um serviço podem ser limitados por motivos jurídicos em determinados países ou regiões. Para ver se um serviço ou recurso está disponível com ou sem restrições, procure seu país ou região no site de restrições de licença de um serviço.

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

<!---HONumber=AcomDC_0420_2016-->