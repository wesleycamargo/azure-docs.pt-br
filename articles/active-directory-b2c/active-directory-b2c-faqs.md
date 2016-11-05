---
title: 'Azure Active Directory B2C: perguntas frequentes | Microsoft Docs'
description: Perguntas frequentes sobre o Active Directory B2C do Azure.
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2016
ms.author: swkrish

---
# Azure Active Directory B2C: perguntas frequentes
Esta página responde a perguntas frequentes sobre o Azure AD (Azure Active Directory) B2C. Continue verificando as atualizações.

### Posso usar recursos do AD B2C do Azure no locatário existente de AD do Azure, com base em funcionário?
No momento, os recursos do AD B2C do Azure não podem ser ativados no seu locatário existente do AD do Azure. Recomendamos que você crie um locatário separado para usar recursos do AD B2C do Azure a fim de gerenciar seus consumidores.

### Posso usar AD B2C do Azure para fornecer logon social (Facebook e Google+) no Office 365?
O AD B2C do Azure não pode ser usado com o Microsoft Office 365. Em geral, ele não pode ser usado para fornecer autenticação para quaisquer aplicativos SaaS (Office 365, Salesforce, Workday etc.). Ele fornece gerenciamento de identidades e acesso apenas para aplicativos móveis e da Web voltados ao consumidor e não se aplica a cenários de funcionário ou parceiro.

### O que são contas locais no AD B2C do Azure? Como elas são diferentes de contas corporativas ou de estudante no AD do Azure?
Em um locatário do Azure AD, cada usuário no locatário (exceto os usuários com contas existente da Microsoft) entram com um endereço de email do formulário `<xyz>@<tenant domain>` em que `<tenant domain>` é um dos domínios verificados no locatário ou do domínio inicial `<...>.onmicrosoft.com`. Esse tipo de conta é uma conta corporativa ou de estudante.

Em um locatário Azure AD B2C, a maioria dos aplicativos quer que o usuário entre com qualquer endereço de email arbitrário (por exemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com ou jim@live.com). Esse tipo de conta é uma conta local. Hoje, também apoiamos nomes de usuário arbitrários (apenas sequências de caracteres simples) como contas locais (por exemplo, pedro, paulo, clara ou davi). Você pode escolher um destes dois tipos de conta no serviço do AD B2C do Azure.

### A quais provedores de identidade social você oferece suporte? A quais você planeja oferecer suporte no futuro?
No momento, oferecemos suporte a Facebook, Google+, LinkedIn e Amazon. Vamos adicionar suporte para outros provedores de identidade social populares com base na demanda do cliente.

### Posso configurar escopos para saber mais sobre os consumidores de vários provedores de identidade sociais?
Não, mas esse recurso está em nosso roteiro. Os escopos de padrão usados para nosso conjunto com suporte de provedores de identidade social são:

* Facebook: email
* Google +: email
* Conta da Microsoft: perfil de email openid
* Amazon: perfil
* LinkedIn: r\_emailaddress, r\_basicprofile

### Meu aplicativo precisa ser executado no Azure para funcionar com o AD B2C do Azure?
Não, você pode hospedar seu aplicativo em qualquer lugar (na nuvem ou localmente). Tudo o que ele precisa para interagir com o AD B2C do Azure é a capacidade de enviar e de receber solicitações HTTP em pontos de extremidade acessíveis publicamente.

### Tenho vários locatários do AD B2C do Azure. Como posso gerenciá-los no Portal do Azure?
Cada locatário AD B2C do Azure tem sua própria folha de recursos B2C no Portal do Azure. Confira [Azure AD B2C: registrar seu aplicativo](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) para saber como é possível navegar até a folha de recursos do B2C de um locatário específico no portal do Azure. Alternar entre diretórios do AD B2C do Azure no Portal do Azure não manterá a folha de recursos do B2C aberta na maioria dos navegadores.

### Como personalizar os emails de verificação (o conteúdo e o campo "De:") enviados pelo AD B2C do Azure?
Você pode usar o [recurso de identidade visual da empresa](../active-directory/active-directory-add-company-branding.md) para personalizar o conteúdo dos emails de verificação. Especificamente, esses dois elementos do email podem ser personalizados:

* **Logotipo do banner**: mostrado no canto inferior direito.
* **Cor da tela de fundo**: mostrada na parte superior.
  
    ![Captura de tela de um email de verificação personalizado](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

A assinatura de email contém o nome do locatário B2C que você forneceu ao criar esse locatário pela primeira vez. Você pode alterar o nome usando estas instruções:

* Entre no [Portal clássico do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura.
* Navegue até seu locatário B2C.
* Clique na guia **Configurar**.
* Altere o campo **Nome** sob a seção **Propriedades do diretório**.
* Na parte inferior da página, clique em **Salvar**.

Atualmente não há nenhuma maneira de alterar o campo "De:" no email. Se você estiver interessado nesta funcionalidade e em personalizar totalmente o corpo do email de verificação, vote a favor do recurso em [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### Como posso migrar meus de nomes de usuário, senhas e perfis existentes no meu banco de dados para o AD B2C do Azure?
Você pode usar a API do Graph do AD do Azure para escrever sua ferramenta de migração. Veja o [exemplo da API do Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) para obter detalhes. Forneceremos várias opções de migração e ferramentas para uso imediato no futuro.

### Qual é a política de senha usada para contas locais no AD B2C do Azure?
A política de senha do AD B2C do Azure para contas locais se baseia na política do AD do Azure. As políticas de inscrição, entrada ou redefinição de senha do Azure AD B2C usam a força de senha "forte" e não expiram as senhas. Leia a [Política de senha do Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obter detalhes.

### Posso usar o Azure AD Connect para migrar identidades de consumidores armazenadas no Active Directory local para o AD B2C do Azure?
Não, o Azure AD Connect não foi projetado para funcionar com o AD B2C do Azure. Forneceremos várias opções de migração e ferramentas para uso imediato no futuro.

### O AD B2C do Azure funciona com sistemas CRM, como o Microsoft Dynamics?
Não atualmente. A integração desses sistemas está em nosso roteiro.

### O AD B2C do Azure funciona com o SharePoint local 2016 ou anterior?
Não atualmente. O AD B2C do Azure não tem suporte para tokens SAML 1.1 de que os portais e os aplicativos de comércio eletrônico compilados no SharePoint local precisam. Observe que o Azure AD B2C não se destina ao cenário de compartilhamento com parceiros externos do SharePoint; em vez disso, veja [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### Devo usar o B2C ou o B2B do AD do Azure para gerenciar identidades externas?
Leia este artigo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para saber mais sobre como aplicar os recursos adequados a seus cenários de identidade externa.

### Quais recursos de relatórios e de auditoria são oferecidos pelo AD B2C do Azure? São iguais aos do Azure AD Premium?
Não, o AD B2C do Azure não dá suporte ao mesmo conjunto de relatórios que o Azure AD Premium. O AD B2C do Azure lançará APIs básicas de relatórios e auditoria em breve.

### Posso localizar a interface de usuário das páginas atendidas pelo AD B2C do Azure? Quais são os idiomas com suporte?
Atualmente, AD B2C do Azure é otimizado para o inglês apenas. Planejamos implantar recursos de localização assim que possível.

### Posso usar minhas próprias URLs em minhas páginas de inscrição e de entrada atendidas pelo AD B2C do Azure? Por exemplo, posso alterar as URLs de login.microsoftonline.com para login.contoso.com?
Não atualmente. Esse recurso está em nosso roteiro. Observe também que verificar seu domínio na guia **Domínios** de seu locatário no portal clássico do Azure não fará isso.

### Como excluir o meu locatário do Azure AD B2C?
Siga estas etapas para excluir seu locatário do Azure AD B2C:

* Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
* Navegue até as folhas de **Aplicativos**, **Provedores de identidade** e **Todas as políticas** e exclua todas as entradas de cada um deles.
* Agora entre no [portal clássico do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura. (Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para se inscrever no Azure.)
* Navegue até a extensão do Active Directory à esquerda e clique no locatário B2C.
* Clique na guia **Usuários**.
* Selecione um usuário por vez (excluir o usuário que estiver conectado no momento; por exemplo, o Administrador da Assinatura). Clique em **Excluir** na parte inferior da página e em **SIM** quando solicitado.
* Clique na guia **Aplicativos**.
* Selecione **Aplicativos que minha empresa possui** no campo suspenso **Mostrar** e clique na marca de seleção.
* Você verá um aplicativo chamado **b2c-extensions-app** listado abaixo. Clique em **Excluir** na parte inferior da página e em **SIM** quando solicitado.
* Navegue até a extensão do Active Directory novamente e selecione seu locatário do B2C.
* Clique em **Excluir** na parte inferior da página. Siga as instruções na tela para concluir o processo.

### Posso obter o AD B2C do Azure como parte do Enterprise Mobility Suite?
Não, o AD B2C do Azure é um serviço pré-pago do Azure e não faz parte do Enterprise Mobility Suite.

### Como faço para relatar problemas com o AD B2C do Azure?
Veja [Solicitações de suporte a arquivos para o Azure Active Directory B2C](active-directory-b2c-support.md).

## Mais informações
Também convém analisar as [limitações e restrições do serviço](active-directory-b2c-limitations.md) atuais.

<!---HONumber=AcomDC_0810_2016-->