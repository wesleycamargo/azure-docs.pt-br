---
title: Personalizar a página de entrada na visualização do Azure Active Directory | Microsoft Docs
description: Saiba como adicionar uma identidade visual à página de entrada do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Adicionar identidade visual à sua página de entrada na visualização do Azure Active Directory
Para evitar confusão, muitas empresas desejam aplicar uma aparência consistente em todos os sites e serviços que elas gerenciam. A visualização do Azure Active Directory fornece essa funcionalidade, permitindo que você personalize a aparência da página de entrada com esquema de cores personalizados e o logotipo da empresa. [O que está na visualização?](active-directory-preview-explainer.md) A página de entrada é a página que aparece quando você entra no Office 365 ou em outros aplicativos baseados na Web que estejam usando o Azure AD como provedor de identidade. Você interage com essa página para inserir suas credenciais.

Se você quiser mostrar a marca da empresa, cores e outros elementos personalizáveis nessa página, consulte as imagens a seguir para compreender a diferença entre as duas experiências.

A captura de tela a seguir mostra um exemplo de página de entrada do Office 365 em um computador desktop **antes** de uma personalização:

![Página de entrada do Office 365 antes da personalização](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

A captura de tela a seguir mostra um exemplo de página de entrada do Office 365 em um computador desktop **depois** de uma personalização:

![Página de entrada do Office 365 após a personalização](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)

## Personalização da página de entrada
Normalmente, se você precisar de acesso baseado em navegador para seus aplicativos e serviços de nuvem que sua organização assina, use a página de entrada.

Se você tiver as alterações aplicadas à sua página de entrada, poderá demorar até uma hora para que as alterações sejam exibidas.

Uma página de entrada com marca só aparece quando você visita um serviço com uma URL específica do locatário, como https://outlook.com/**contoso**.com ou https://mail.**contoso**.com.

Quando você visita um serviço com URLs que não são específicas do locatário (por exemplo: https://mail.office365.com), uma página de entrada sem marca é exibida. Nesse caso, sua identidade visual aparecerá assim que você inserir sua ID de usuário ou que tiver selecionado um bloco de usuário.

> [!NOTE]
> * O nome do domínio deve aparecer como "Ativo" na parte **Domínios** do portal do Azure no qual você configurou a identidade visual. Para obter mais informações, confira [Adicionar nomes de domínio personalizados](active-directory-domains-add-azure-portal.md).
> * A identidade visual da página de entrada não se transfere para a página de entrada do consumidor da Microsoft. Se você entrar com uma conta da Microsoft, talvez veja uma lista com identidade visual de blocos de usuários renderizados pelo Azure AD, mas a identidade visual da sua organização não se aplicará à página de entrada da conta da Microsoft.
> 
> 

**Para adicionar identidade visual da empresa ao diretório:**

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global para o diretório.
2. Selecione **Mais serviços**, digite **Usuários e grupos** na caixa de texto e, em seguida, selecione **Enter**.
   
   ![Abrindo o gerenciamento de usuários](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. Na folha **Usuários e grupos**, selecione **Identidade visual da empresa**.
4. Na folha **Usuários e grupos – Identidade visual da empresa**, selecione o comando **Editar**.
   
    ![Editar identidade visual personalizada](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. Modifique os elementos que você deseja personalizar. Todos os elementos são opcionais.
6. Clique em **Salvar**.

Pode demorar até uma hora para que novas alterações feita à identidade visual da página de entrada apareçam.

## Próximas etapas
[Adicionar identidade visual da empresa específica a um idioma](active-directory-branding-localize-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->