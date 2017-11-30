---
title: 'Personalizar: SSPR do Azure AD | Microsoft Docs'
description: "Opções de personalização para redefinição de senha por autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: e8cf0ce8ed154a7e42b885e605dcdf37827a6447
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2017
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personalizar a funcionalidade de Autoatendimento de Redefinição de Senha do Azure AD

Os profissionais de TI que desejam implantar a redefinição de senha de autoatendimento (SSPR) no Azure Active Directory (AD do Azure) podem personalizar a experiência para atender às necessidades de seus usuários.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar o link "Contate o administrador"

Mesmo que a SSPR não esteja habilitada, os usuários ainda verão um link "Contate o administrador" no portal de redefinição de senha. Se um usuário selecionar este link, ele executará uma das seguintes ações:
   * Enviará um email aos administradores pedindo ajuda para alterar a senha do usuário. 
   * Envia seus usuários a uma URL que você especifica para obter assistência. 

É recomendável definir esse contato para algo como um endereço de email ou site os quais os usuários já estão acostumados a usar para obter suporte.

![Contato][Contact]

Esse contato é enviado para os seguintes destinatários na seguinte ordem:

1. Se a função **Administrador de senhas** for atribuída, os administradores com essa função serão notificados.
2. Se nenhum administrador de senhas for atribuído, os administradores com a função **administrador de usuários** serão notificados.
3. Se nenhuma das funções anteriores foram atribuídas, os **administradores globais** serão notificados.

Em todos os casos, no máximo 100 destinatários serão notificados.

Para obter mais informações sobre as diferentes funções de administrador e sobre como atribuí-las, consulte [Atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

### <a name="disable-contact-your-administrator-emails"></a>Desabilitar os emails "Contate o administrador"

Caso sua organização não queira que os administradores recebam solicitações de redefinição de senha, você poderá habilitar a configuração a seguir:

* Habilite o autoatendimento de redefinição de senha para todos os usuários finais. Essa opção pode ser encontrada em **Redefinição de Senha** > **Propriedades**.
  
  Se você não quiser que os usuários redefinam as próprias senhas, poderá definir o escopo de acesso como um grupo vazio. *Não recomendamos essa opção.*
* Personalizar o link de assistência técnica para fornecer uma URL da Web ou um endereço mailto: que os usuários podem usar para obter assistência. Essa opção pode ser encontrada em **Redefinição de Senha** > **Personalização** > **URL ou email de assistência técnica personalizados**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personalize a página de entrada do AD FS para SSPR

Os administradores do Active Directory Federation Services (AD FS) podem adicionar um link à página de entrada deles usando diretrizes no artigo [Adicionar descrição da página de entrada](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Para adicionar um link à página de entrada do AD FS, use o seguinte comando no servidor do AD FS. Os usuários podem usar essa página para inserir o fluxo de trabalho de SSPR.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personalizar a página de entrada e a aparência do painel de acesso

Você pode personalizar a página de entrada. Você pode adicionar um logotipo que aparece junto com a imagem adequada à identidade visual de sua empresa.

Os gráficos que escolher são mostrados nas seguintes circunstâncias:

* Depois que um usuário inserir seu nome de usuário
* Se o usuário acessar a URL personalizada:
    * Ao passar o parâmetro *whr* para a página de redefinição de senha, como "https://login.microsoftonline.com/?whr=contoso.com"
    * Ao passar o parâmetro *username* para a página de redefinição de senha, como "https://login.microsoftonline.com/?username=admin@contoso.com"

### <a name="graphics-details"></a>Detalhes de elementos gráficos

Use as seguintes configurações para alterar as características visuais da página de entrada. Vá a **Active Directory do Azure** > **Identidade visual da empresa** > **Editar identidade visual da empresa**:

* A imagem da página de entrada deve ser um arquivo .png ou .jpg com 1420 x 1200 pixels e, no máximo, de 500 KB. Para obter melhores resultados, recomendamos que você a mantenha por volta dos 200 KB.
* A cor da tela de fundo da página de entrada é usada em conexões de alta latência e deve estar no formato hexadecimal RGB.
* A imagem da faixa deve ser um arquivo .png ou .jpg com 60 x 280 pixels e, no máximo, 10 KB.
* O logotipo quadrado (tema escuro e normal) deve ser um arquivo .png ou .jpg, 240 x 240 pixels (redimensionável) e não pode exceder 10 KB.

### <a name="sign-in-text-options"></a>Opções do texto de conexão

Use as seguintes configurações para adicionar um texto à página de entrada relevante para sua organização. Vá a **Active Directory do Azure** > **Identidade visual da empresa** > **Editar identidade visual da empresa**:

* **Dica de nome de usuário**: substitui o texto de exemplo de *someone@example.com* por algo mais adequado a seus usuários. Recomendamos que você deixe a dica padrão ao dar suporte a usuários internos e externos.
* **Texto da página de conexão**: pode ser, no máximo, 256 caracteres. Esse texto é exibido em qualquer lugar quando os usuários fazem logon online e na experiência de Ingresso do Azure AD Workplace no Windows 10. Use este texto para os termos de uso, as instruções e as dicas para os usuários. 

   >[!IMPORTANT]
   >Qualquer pessoa pode ver sua página de entrada. Portanto, não forneça informações confidenciais aqui.
   >

### <a name="the-keep-me-signed-in-disabled-setting"></a>A configuração "Manter-me conectado desabilitado"

A opção **Manter-me conectado desabilitado** permite que os usuários permaneçam conectados ao fechar e reabrir a janela do navegador. Essa opção não afeta o tempo de vida da sessão. Vá a **Active Directory do Azure** > **Identidade visual da empresa** > **Editar identidade visual da empresa**.

Alguns recursos do SharePoint Online e do Office 2010 dependem da capacidade de os usuários marcarem essa caixa de seleção. Se você ocultar essa opção, os usuários poderão receber prompts de conexão adicionais e inesperados.

### <a name="directory-name"></a>Nome do diretório

Você pode alterar o atributo de nome de diretório em **Azure Active Directory** > **Propriedades**. Você pode mostrar um nome amigável de organização que aparece no portal e nas comunicações automatizadas. Essa opção fica mais visível nos emails automatizados nos formatos a seguir:

* O nome amigável no email, por exemplo, "Microsoft em nome da demonstração da CONTOSO"
* A linha do assunto no email, por exemplo, "Código de verificação de email da conta de demonstração da CONTOSO"

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](active-directory-passwords-best-practices.md)
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](active-directory-passwords-data.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](active-directory-passwords-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](active-directory-passwords-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "Contate o administrador para obter ajuda para redefinir o exemplo de email de senha"
