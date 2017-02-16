---
title: "Introdução ao Active Directory Premium do Azure"
description: "Um tópico que explica como inscrever-se no Azure Active Directory Premium Edition por meio do site de Licenciamento por Volume."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 73cbbce9ae3d727632b8cac2a29c2b1b92332ae7


---
# <a name="getting-started-with-azure-active-directory-premium"></a>Introdução ao Active Directory Premium do Azure
Para se inscrever no Active Directory Premium, você tem várias opções: 

**Azure ou Office 365** ‒ como assinante do Azure ou do Office 365, você pode comprar o Active Directory Premium online. Para obter etapas detalhadas, confira [Como comprar o Azure Active Directory Premium ‒ clientes existentes](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) ou [Como comprar o Azure Active Directory Premium ‒ novos clientes](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

**Enterprise Mobility Suite** ‒ o Enterprise Mobility Suite + Security (conhecido anteriormente como Enterprise Mobility Suite) é uma forma econômica para que as organizações usem os seguintes serviços em conjunto em um plano de licenciamento: Active Directory Premium, Azure Rights Management e Microsoft Intune. Para obter mais informações, veja o site [Enterprise Mobility + Security](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx). Para obter uma avaliação gratuita de 30 dias, clique [aqui](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0).

**Microsoft Volume Licensing** ‒ o Azure Active Directory Premium está disponível por meio de um [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 ou mais licenças) ou um programa [Open Volume License](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5 – 250 licenças).

Este tópico mostra como começar a usar um Azure Active Directory Premium adquirido por meio do Programa de Licenciamento por Volume. Se ainda não estiver familiarizado com as diferentes edições do Azure Active Directory, confira [Edições do Azure Active Directory](active-directory-editions.md).  

> [!NOTE]
> As edições Premium e Basic do Active Directory do Azure estão disponíveis para clientes na China usando a instância mundial do Active Directory do Azure. As edições Azure Active Directory Premium e Basic não têm suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).
> 
> 

## <a name="step-1-sign-up-for-active-directory-premium"></a>Etapa 1: inscrever-se no Active Directory Premium do Azure
Para se inscrever, veja [Como comprar por meio do Licenciamento por Volume](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx).

## <a name="step-2-activate-your-license-plan"></a>Etapa 2: ativar seu plano de licença
Esta é sua primeira compra de plano de licença por meio do programa de Licenciamento por Volume Enterprise da Microsoft?
Nesse caso, você receberá um email de confirmação quando a compra for concluída.
Você precisará desse email para ativar seu primeiro plano de licença.

Em qualquer compra subsequente para esse diretório, as licenças são automaticamente ativadas no mesmo diretório.

**Para ativar o plano de licença, execute uma das seguintes etapas:**

1. Para iniciar a ativação, clique em **Entrar** ou em **Inscrever-se**.
   
    ![Entrar][1]

    - Se você tiver um locatário existente, clique em **Entrar** para entrar com sua conta de administrador existente. Você precisa entrar com as credenciais de administrador global do diretório em que as licenças devem ser ativadas.

    - Se quiser criar um novo locatário do Azure Active Directory para usar com seu plano de licenciamento, clique em **Inscrever-se** para abrir a caixa de diálogo **Criar Perfil de Conta**.

        ![Criar Perfil de Conta][2]

Quando terminar, o diálogo a seguir será mostrado como a confirmação da ativação do plano de licença para seu locatário.

![Confirmação][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>Etapa 3: ativar o acesso ao Active Directory do Azure
Se já tiver usado o Microsoft Azure anteriormente, prossiga para a [Etapa 4](#step-4-assign-license-to-user-accounts). 

Quando as licenças forem provisionadas no diretório, um **Email de boas-vindas** será enviado a você. O email confirma que você pode começar a gerenciar suas licenças e seus recursos do Azure Active Directory Premium ou do Enterprise Mobility Suite. 

Caso tente ativar o acesso ao Azure Active Directory antes de receber o email de boas-vindas, a mensagem de erro abaixo poderá ser exibida. 

![O acesso não está disponível][9]

Tente novamente alguns minutos após ter recebido o email.

Os novos administradores em sua assinatura também podem ativar o acesso ao portal clássico do Azure por meio desse link.

**Para ativar o acesso ao Azure Active Directory, execute as seguintes etapas:**

1. No **Email de boas-vindas**, clique em **Entrar**. 
   
    ![Email de boas-vindas][4]
2. Após entrar com êxito, você precisa concluir a autenticação de um segundo fator, na forma de uma verificação de celular:
   
    ![Verificação de celular][5]

A ativação pode levar alguns minutos. Quando o acesso estiver ativo, a barra marrom desaparecerá, e você poderá clicar em **Portal**.

![Aguarde enquanto fazemos a configuração][6]

Nesse caso, o acesso ao Azure será limitado ao Azure Active Directory.

![Recursos do Azure][7]

Você pode já ter tido acesso ao Azure em uma utilização anterior; além disso, você pode atualizar seu acesso do Azure Active Directory para acesso completo ao Azure, ao ativar assinaturas adicionais do Azure. Nesses casos, o portal clássico do Azure tem mais recursos.

![Recursos do Azure][8]

## <a name="step-4-assign-license-to-user-accounts"></a>Etapa 4: atribuir licenças a contas de usuário
Antes de começar a usar o plano que você adquiriu, você precisará atribuir manualmente as licenças para contas de usuário em sua organização para que elas possam usar os recursos avançados fornecidos com a edição Premium. Use as etapas a seguir para atribuir licenças aos usuários para que possam usar os recursos do Active Directory Premium do Azure.

**Para atribuir licenças a usuários, execute as seguintes etapas:**

1. Entre no portal clássico do Azure como o administrador global do diretório que você quer personalizar.
2. Clique em **Active Directory**e, em seguida, selecione o diretório onde você deseja atribuir licenças.
3. Selecione a guia **Licenças** selecione **Active Directory Premium** ou **Enterprise Mobility Suite** e, em seguida, clique em **Atribuir**.
   
    ![Planos de licença][10]
4. Na caixa de diálogo, selecione os usuários para os quais você deseja atribuir licenças e, em seguida, clique no ícone de marca de seleção para salvar as alterações.
   
    ![Atribuir licenças][11]

### <a name="license-restrictions"></a>Restrições de licença
Alguns planos de licenças são subconjuntos ou superconjuntos de outros planos de licenças. Normalmente, não é possível atribuir a um usuário um plano de licença que já tenha sido atribuído a ele. Se for sua intenção atribuir um plano de licença que é um superconjunto, você precisará primeiro remover o plano de licença que é um subconjunto.

### <a name="license-requirements"></a>Requisitos de licença
Quando você atribuir uma licença a um usuário, você pode especificar um local de uso primário nas propriedades da conta desse usuário. Se um local de uso não for especificado, o local do inquilino é atribuído automaticamente ao usuário.

![Local do usuário][12]

A disponibilidade dos serviços e recursos de um serviço de nuvem da Microsoft varia segundo o país ou região. Um serviço, como VoIP (Voice over Internet Protocol, voz por protocolo de Internet) pode estar disponível em um país ou região e não estar disponível em outro. Recursos dentro de um serviço podem ser limitados por motivos jurídicos em determinados países ou regiões. Para ver se um serviço ou recurso está disponível com ou sem restrições, procure seu país ou região no site de restrições de licença de um serviço.

## <a name="whats-next"></a>O que vem a seguir
* [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)
* [Exibir relatórios de acesso e de uso](active-directory-view-access-usage-reports.md)

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



<!--HONumber=Dec16_HO2-->


