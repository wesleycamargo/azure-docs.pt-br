---
title: "Personalizar a página de entrada na visualização do Azure Active Directory | Microsoft Docs"
description: "Saiba como adicionar uma identidade visual à página de entrada do Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3a054a01cf21bba4bbaf15c404f74086b2f25651


---
# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Adicionar identidade visual à sua página de entrada na visualização do Azure Active Directory
Para evitar confusão, muitas empresas desejam aplicar uma aparência consistente em todos os sites e serviços que elas gerenciam. A visualização do Azure Active Directory fornece esse recurso, permitindo que você personalize a aparência da página de entrada com esquemas de cores e o logotipo da empresa. [O que há na visualização?](active-directory-preview-explainer.md)  A página de entrada é a página que aparece quando você entra no Office 365 ou em outros aplicativos baseados na Web que estejam usando o Azure AD como provedor de identidade. Você interage com essa página para inserir suas credenciais.

Se você quiser mostrar a marca da empresa, cores e outros elementos personalizáveis nessa página, consulte as imagens a seguir para compreender a diferença entre as duas experiências.

A captura de tela a seguir mostra um exemplo de página de entrada do Office 365 em um computador desktop **antes** de uma personalização:

![Página de entrada do Office 365 antes da personalização](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

A captura de tela a seguir mostra um exemplo de página de entrada do Office 365 em um computador desktop **depois** de uma personalização:

![Página de entrada do Office 365 após a personalização](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)

## <a name="customizing-the-sign-in-page"></a>Personalização da página de entrada
Normalmente, se você precisar de acesso baseado em navegador para seus aplicativos e serviços de nuvem que sua organização assina, use a página de entrada.

Se você tiver as alterações aplicadas à sua página de entrada, poderá demorar até uma hora para que as alterações sejam exibidas.

Uma página de entrada com marca só aparece quando você visita um serviço com uma URL específica do locatário, como https://outlook.com/**contoso**.com ou https://mail.**contoso**.com.

Quando você visita um serviço com URLs específicas sem locatário (por exemplo, https://mail.office365.com), uma página de entrada sem marca é exibida. Nesse caso, sua identidade visual aparecerá assim que você inserir sua ID de usuário ou que tiver selecionado um bloco de usuário.

> [!NOTE]
> * O nome do domínio deve aparecer como "Ativo" na parte **Domínios** do portal do Azure no qual você configurou a identidade visual. Para obter mais informações, confira [Adicionar nomes de domínio personalizados](active-directory-domains-add-azure-portal.md).
> * A identidade visual da página de entrada não se transfere para a página de entrada do consumidor da Microsoft. Se você entrar com uma conta da Microsoft, talvez veja uma lista com identidade visual de blocos de usuários renderizados pelo Azure AD, mas a identidade visual da sua organização não se aplicará à página de entrada da conta da Microsoft.
> 
> 

Na sua página de entrada, a caixa de seleção **Mantenha-me conectado** permite que o usuário permaneça conectado ao fechar e reabrir o navegador. 

   ![Mantenha-me conectado](./media/active-directory-branding-custom-signon-azure-portal/01.png)

Ela não afeta o tempo de vida da sessão. Você pode ocultar a caixa de seleção na página de entrada do Azure Active Directory.
A exibição da caixa de seleção depende da configuração de **Manter a minha sessão iniciada desativada**.

   ![Mantenha-me conectado](./media/active-directory-branding-custom-signon-azure-portal/02.png)

Para ocultar a caixa de seleção, defina essa configuração para **Sim**. 

> [!NOTE]
> Alguns recursos do SharePoint Online e do Office 2010 dependem da capacidade dos usuários marcarem essa caixa de seleção. Se você definir essa configuração como oculta, os usuários poderão ver avisos adicionais e inesperados para entrar.
> 
> 

**Para adicionar identidade visual da empresa ao diretório:**

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.
   
   ![Abrir o gerenciamento de usuários](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. Na folha **Usuários e grupos**, selecione **Identidade visual da empresa**.
4. Na folha **Usuários e grupos - Identidade visual da empresa**, selecione o comando **Editar**.
   
    ![Editar identidade visual personalizada](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. Modifique os elementos que você deseja personalizar. Todos os elementos são opcionais.
6. Clique em **Salvar**.

Pode demorar até uma hora para que apareçam todas as alterações feitas à identidade visual da página de entrada.

## <a name="next-steps"></a>Próximas etapas
[Adicionar identidade visual da empresa específica a um idioma](active-directory-branding-localize-azure-portal.md)




<!--HONumber=Nov16_HO3-->


