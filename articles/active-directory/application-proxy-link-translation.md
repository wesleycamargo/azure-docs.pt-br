---
title: Converter links e URLs do Proxy de Aplicativo do Azure AD | Microsoft Docs
description: "Cobre as noções básicas sobre os conectores do Proxy de Aplicativo do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: d716476deb81f6627cf03401f78ab399ae940e68
ms.contentlocale: pt-br
ms.lasthandoff: 07/04/2017

---

# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Redirecione os links inseridos no código para aplicativos publicados com o Proxy de Aplicativo do Azure AD

O Proxy de Aplicativo do Azure AD disponibiliza seus aplicativos locais para usuários remotos ou que estão em seus próprios dispositivos. No entanto, alguns aplicativos foram desenvolvidos com o público local em mente, o que normalmente significa que os links locais são inseridos no HTML e, portanto, não funcionam corretamente quando o aplicativo é usado remotamente. Isso é mais comum quando vários aplicativos locais apontam uns para os outros e seus usuários esperam usar os links para se mover entre os aplicativos em vez de digitar a URL externa para cada aplicativo.

O recurso de translação de link do Proxy de Aplicativo foi desenvolvido para resolver esse problema. Quando você tem aplicativos que apontam diretamente para portas ou pontos de extremidade internos, pode mapear essas URLs internas para as URLs de Proxy de Aplicativo externas publicadas. Habilite a translação de link e o Proxy de Aplicativo pesquisa o HTML, o CSS e as marcas JavaScript selecionadas quanto a links internos publicados e os converte para que seus usuários obtenham uma experiência ininterrupta.

>[!NOTE]
>O recurso de translação de link destina-se a locatários que, por qualquer motivo, não podem usar domínios personalizados para ter as mesmas URLs internas e externas para seus aplicativos. Antes de habilitar esse recurso, verifique se [domínios personalizados no Proxy de Aplicativo do Azure AD](active-directory-application-proxy-custom-domains.md) podem funcionar para você.

## <a name="how-link-translation-works"></a>Como a translação de link funciona

Após a autenticação, quando o servidor proxy transmite os dados de aplicativo para o usuário, o Proxy de Aplicativo examina o aplicativo em relação a links inseridos no código e os substitui por suas respectivas URLs externas publicadas.

### <a name="which-links-are-affected"></a>Quais links são afetados?

O recurso de translação de link procura apenas por links que estão nas marcas de código no corpo do aplicativo. O Proxy de Aplicativo tem um recurso separado para converter cookies ou URLs em cabeçalhos. 

Há dois tipos comuns de links internos em aplicativos locais:

- **Links internos relativos** que apontam para um recurso compartilhado em uma estrutura de arquivo local como `/claims/claims.html`. Esses links funcionam automaticamente em aplicativos que são publicados por meio do Proxy de Aplicativo e continuarão a funcionar com ou sem translação de link. 
- **Links internos inseridos no código** para outros aplicativos de locais como `http://expenses` ou arquivos publicados como `http://expenses/logo.jpg`. O recurso de translação de link funciona em links internos inseridos no código e os altera para apontar para as URLs externas de que os usuários remotos precisam para avançar.

### <a name="how-do-apps-link-to-each-other"></a>Como aplicativos são vinculados entre si?

A translação de link está habilitada para cada aplicativo, para que você tenha controle sobre a experiência do usuário no nível por aplicativo. Ative a translação de link para um aplicativo quando desejar que os links *desse* aplicativo sejam convertidos, não os links *para* ele. 

Por exemplo, suponha que você tenha três aplicativos publicados por meio do Proxy de Aplicativo que estão vinculados entre si: Benefícios, Despesas e Viagem. Há um quarto aplicativo, Comentários, que não está publicado pelo Proxy de Aplicativo.

Quando você habilita a translação de link para o aplicativo Benefícios, os links para Despesas e Viagem são redirecionados para as URLs externas para esses aplicativos, mas o link para Comentários não é redirecionado porque não há nenhuma URL externa. Os links de Despesas e Viagem para Benefícios não funcionam, pois a translação de link não foi habilitada para esses dois aplicativos.

![Os links de Benefícios para outros aplicativos quando a translação de link está habilitada](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Quais links não são convertidos?

Para melhorar o desempenho e a segurança, alguns links não são convertidos:

- Links que não estão dentro de marcas de código. 
- Links internos abertos de outros programas. Links enviados por email, mensagem instantânea ou incluídos em outros documentos não serão convertidos. Os usuários precisam saber ir para a URL externa.

Se precisar dar suporte a esses dois cenários, você poderá usar as mesmas URLs de internas e externas, o que elimina a necessidade de translação de link.  

## <a name="enable-link-translation"></a>Habilitar a translação de link

Começar a trabalhar com a translação de link é tão fácil quanto clicar em um botão:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Vá para **Azure Active Directory** > **Aplicativos empresariais** > **Todos os aplicativos** > selecione o aplicativo que deseja gerenciar > **Proxy de aplicativo**.
3. Mude **Converter URLs no corpo do aplicativo** para **Sim**.

   ![Selecione Sim para converter URLs no corpo do aplicativo](./media/application-proxy-link-translation/select_yes.png).
4. Selecione **Salvar** para salvar suas alterações.

Agora, quando os usuários acessarem esse aplicativo, o proxy examinará automaticamente para verificar a existência de URLs internas que foram publicadas por meio do Proxy de Aplicativo no locatário.

## <a name="send-feedback"></a>Enviar comentários

Queremos sua ajuda para fazer esse recurso funcionar para todos os seus aplicativos. Pesquisamos mais de 30 marcas em HTML e CSS e estamos considerando para quais casos do JavaScript dar suporte. Se você tiver um exemplo de links gerados que não estão sendo convertidos, envie um trecho de código para [Comentários de Proxy de Aplicativo](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Próximas etapas
- [Usar domínios personalizados com o Proxy de Aplicativo do Azure AD para ter a mesma URL interna e externa](active-directory-application-proxy-custom-domains.md)

