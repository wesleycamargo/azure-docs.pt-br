---
title: 'Personalizando: SSPR do Azure AD | Microsoft Docs'
description: 
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.editor: gahug
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 41da786ed13308a1fc030e6b02fac3d8fbca9e61
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>Personalizar a funcionalidade de Autoatendimento de Redefinição de Senha do Azure AD

Há diversas opções que podem ser alteradas no Azure AD e muitas delas estão relacionadas ao SSPR (Autoatendimento de Redefinição de Senha). Este guia explica essas possibilidades.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar o link Contate o administrador

Mesmo que o SSPR não esteja habilitado, os usuários ainda verão um link “contate o administrador” no portal de redefinição de senha.  Ao clicar nesse link, um email será enviado aos administradores solicitando assistência na alteração da senha do usuário. Esse email é enviado para os seguintes destinatários na seguinte ordem:

1. Se a função **Administrador de senha** for atribuída, aqueles com essa função serão notificados
2. Se nenhum Administrador de senhas for atribuído, aqueles com a função **Usuário administrador** serão notificados
3. Se nenhuma das funções anteriores foram atribuídas, os **Administradores globais** são notificados

Em todos os casos, um total de até 100 destinatários são notificados.

### <a name="disable-contact-your-administrator-emails"></a>Desabilitar os emails “Contate o administrador”

Caso sua organização não deseje que os administradores sejam notificados de solicitações de redefinição de senha, a configuração a seguir poderá ser habilitada

* Habilite o autoatendimento de redefinição de senha para todos os usuários finais. Essa opção pode ser encontrada em **Redefinição de Senha**, **Propriedades**
    * Se você não desejar que os usuários redefinam suas próprias senhas, poderá limitar o escopo do acesso a um grupo vazio; no entanto, não recomendamos essa opção**.
* Personalizar o link de assistência técnica para fornecer uma URL da Web ou um endereço mailto: que os usuários podem usar para obter assistência. Essa opção pode ser encontrada em **Redefinição de Senha**, **Personalização** e, em seguida, em **URL ou Email de Assistência Técnica Personalizado**

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>Personalizar o logon e a aparência do painel de acesso

Quando os usuários acessam a página de logon, você pode personalizar o logotipo que é exibido junto com a imagem da página de conexão de acordo com a identidade visual de sua empresa.

Esses elementos gráficos são mostrados nas seguintes circunstâncias:

* Depois que um usuário digita seu nome de usuário
* Quando o usuário acessa uma URL personalizada
    * Ao passar o parâmetro “whr” para a página de redefinição de senha, como “https://login.microsoftonline.com/?whr=contoso.com”
    * Ao passar o parâmetro “username” para a página de redefinição de senha, como “https://login.microsoftonline.com/?username=admin@contoso.com”

### <a name="graphics-details"></a>Detalhes de elementos gráficos

As configurações a seguir permitem alterar as características visuais da página de conexão e podem ser encontradas em **Azure Active Directory**, **Identidade visual da empresa**, **Editar identidade visual da empresa**

* A imagem da página de conexão deve ser um arquivo PNG ou JPG com 1420x1200 pixels e, no máximo, 500 KB. Recomendamos que ela tenha cerca de 200 KB para obter melhores resultados.
* A cor da tela de fundo da página de conexão é usada em conexões de alta latência e deve estar no formato hexadecimal RGB
* A imagem da faixa deve ser um arquivo PNG ou JPG com 60x280 pixels e, no máximo, 10 KB
* Logotipo quadrado (tema normal e escuro) em PNG ou JPG com 240x240 (redimensionável) com, no máximo, 10 KB

### <a name="sign-in-text-options"></a>Opções do texto de conexão

As configurações a seguir permitem adicionar um texto à página de conexão relevante para sua organização. Essas configurações podem ser encontradas em **Azure Active Directory**, **Identidade visual da empresa**, **Editar identidade visual da empresa**

* A **dica de nome de usuário** substitui o texto de exemplo someone@example.com por algo mais apropriado para os usuários (é recomendado que ela seja mantida como padrão durante o suporte a usuários internos e externos)
* O **texto da página de conexão** tem, no máximo, 256 caracteres. Esse texto é exibido em qualquer lugar quando os usuários fazem logon online e na experiência de Ingresso do Azure AD no Windows 10. Use este texto para termos de uso, instruções e dicas para os usuários. **Qualquer pessoa pode ver sua página de conexão; portanto, não forneça informações confidenciais aqui.**

### <a name="keep-me-signed-in-disabled"></a>Opção “Manter-me conectado desabilitado”

A opção “Manter-me conectado desabilitado” permite que os usuários permaneçam conectados ao fechar e reabrir a janela do navegador e não afeta o tempo de vida da sessão. Essa configuração é encontrada em **Azure Active Directory**, **Identidade visual da empresa**, **Editar identidade visual da empresa**.

Alguns recursos do SharePoint Online e do Office 2010 dependem da capacidade de os usuários marcarem essa caixa de seleção. Se você ocultar essa opção, os usuários poderão receber prompts de conexão adicionais e inesperados.

### <a name="directory-name"></a>Nome do diretório

É possível alterar o atributo de nome em **Azure Active Directory**, **Propriedades** para mostrar um nome de organização amigável visto no portal e nas comunicações automatizadas. Essa opção fica mais visível na forma de emails automatizados nos formatos a seguir

* Nome amigável em email “Microsoft em nome da demonstração da CONTOSO”
* Linha do assunto em email “Código de verificação de email da conta de demonstração da CONTOSO”

## <a name="next-steps"></a>Próximas etapas

Os links a seguir fornecem mais informações sobre a redefinição de senha com o Azure AD

* [**Início Rápido**](active-directory-passwords-getting-started.md) – comece agora mesmo a usar o gerenciamento de senhas de autoatendimento do Azure AD 
* [**Licenciamento**](active-directory-passwords-licensing.md) – configure o licenciamento do Azure AD
* [**Dados**](active-directory-passwords-data.md) – entenda os dados que são necessários e como eles são usados para o gerenciamento de senhas
* [**Distribuição**](active-directory-passwords-best-practices.md) – planeje e implante o SSPR em seus usuários usando as diretrizes descritas aqui
* [**Política**](active-directory-passwords-policy.md) – entenda e defina políticas de senha do Azure AD
* [**Write-back de Senha**](active-directory-passwords-writeback.md) – como o write-back de senha funciona com o diretório local
* [**Relatórios**](active-directory-passwords-reporting.md) – descubra se, quando e onde os usuários estão acessando a funcionalidade do SSPR
* [**Aprofundamento Técnico**](active-directory-passwords-how-it-works.md) – vá para os bastidores para entender como ele funciona
* [**Perguntas frequentes**](active-directory-passwords-faq.md) – Como? Por quê? O que? Onde? Quem? Quando? – respostas para perguntas que você sempre quis fazer
* [**Resolver problemas**](active-directory-passwords-troubleshoot.md) – saiba como resolver problemas comuns encontrados no SSPR


