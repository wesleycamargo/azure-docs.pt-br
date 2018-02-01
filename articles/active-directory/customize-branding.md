---
title: "Personalizar a página de entrada do locatário do Azure Active Directory | Microsoft Docs"
description: "Saiba como adicionar uma identidade visual à página de entrada do Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/19/2018
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 03a6b82f769ed9a36c5d3ff9934de75d1536e1ae
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Início rápido: adicionar identidade visual da empresa à sua página de entrada no Azure AD
Para evitar confusão, muitas empresas desejam aplicar uma aparência consistente em todos os sites e serviços que elas gerenciam. O Azure AD (Azure Active Directory) fornece esse recurso permitindo que você personalize a aparência da página de entrada com esquemas de cores personalizados e o logotipo da empresa. A página de entrada é exibida quando você se conecta a aplicativos baseados na Web, como o Office 365, que usam o Azure AD como o provedor de identidade. Você interage com essa página para inserir suas credenciais.

> [!NOTE]
> * A identidade visual da empresa estará disponível somente se você comprar a licença Premium ou Básica do Azure AD ou se tiver uma licença do Office 365. Para saber se um recurso é compatível com seu tipo de licença, verifique a [página de informações de preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * As edições Azure AD Premium e Básico estão disponíveis para clientes na China por meio da instância mundial do Azure Active Directory. Atualmente, as edições Azure AD Premium e Básico não são compatíveis com o serviço do Azure operado pela 21Vianet na China. Para obter mais informações, fale conosco no [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Personalização da página de entrada

<!--You can customize the following elements on the sign-in page: <attach image>-->

As personalizações de identidade visual da empresa serão exibidas na página de entrada do Azure AD quando os usuários acessarem uma URL específica de locatário, como [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com).

Por exemplo, quando os usuários visitam www.office.com, a página de entrada não mostra nenhuma personalização de identidade visual da empresa porque o usuário ainda não inseriu as credenciais. Depois que o usuário insere sua ID de usuário ou seleciona um bloco de usuário, a identidade visual da empresa é exibida.

> [!NOTE]
> * O nome do domínio deve aparecer como "Ativo" na parte **Domínios** do portal do Azure no qual você configurou a identidade visual. Para saber mais, confira [Adicionar um nome de domínio personalizado](add-custom-domain.md).
> * A identidade visual da página de entrada não é transferida para a página de logon para contas pessoais da Microsoft. Se os funcionários ou parceiros de negócios convidados entrarem com uma conta pessoal da Microsoft, sua página de entrada não reflete a identidade visual da sua organização.


### <a name="banner-logo"></a>Logotipo de faixa 

DESCRIÇÃO | Restrições | Recomendações
------- | ------- | ----------
O logotipo da faixa é exibido na páginas de entrada e do Painel de Acesso.<br>Na página de entrada, o logotipo é mostrado depois que o nome de usuário é inserido. | JPG ou PNG transparente<br>Altura máxima: 36 px<br>Largura máxima: 245 px | Use o logotipo da sua organização aqui.<br>Use uma imagem transparente. Não suponha que a tela de fundo será branco.<br>Não adicione preenchimento ao redor de seu logotipo na imagem ou seu logotipo parecerá desproporcionalmente pequeno.

### <a name="username-hint"></a>Dica do nome de usuário   
DESCRIÇÃO | Restrições | Recomendações
------- | ------- | ----------
Essa opção personaliza o texto de dica no campo de nome de usuário. | Texto em Unicode de até 64 caracteres<br>Somente texto sem formatação | Se você espera que usuários convidados fora de sua organização entrem em seu aplicativo, recomendamos não configurar essa opção.
            
### <a name="sign-in-page-text"></a>Texto da página de entrada   
DESCRIÇÃO | Restrições | Recomendações
------- | ------- | ----------
Essa opção é exibida na parte inferior do formulário de entrada e pode ser usada para comunicar informações adicionais, como o número de telefone para o suporte técnico ou uma declaração legal. | Texto em Unicode de até 256 caracteres<br>Apenas texto sem formatação (sem links ou marcas HTML)    

### <a name="sign-in-page-image"></a>Imagem da página de entrada  
DESCRIÇÃO | Restrições | Recomendações
------- | ------- | ----------
Essa opção é exibida na tela de fundo da página de entrada, é ancorada no centro do espaço visível e é dimensionada e cortada para preencher a janela do navegador.    <br>Em telas estreitas, como celulares, a imagem não é mostrada.<br>Uma máscara preta com opacidade de 0,55 é aplicada sobre essa imagem quando a página é carregada. | JPG ou PNG<br>Dimensões da imagem: 1920 x 1080 px<br>Tamanho do arquivo: &lt; 300 KB | <br>Use imagens que não possuam um foco forte no assunto. O formulário de entrada opaco é exibido no centro dessa imagem e pode abranger qualquer parte da imagem, dependendo do tamanho da janela do navegador.<br>Mantenha o tamanho do arquivo pequeno para garantir tempos de carregamento rápidos. 

### <a name="sign-in-page-background-color"></a>Cor da tela de fundo da página de entrada
DESCRIÇÃO | Restrições | Recomendações
------- | ------- | ----------
Essa cor é usada no lugar da imagem da tela de fundo em conexões de baixa largura de banda. | Cor RGB em hexadecimal (exemplo: #FFFFFF | Sugerimos usar a cor primária do logotipo da faixa ou a cor da sua organização.

### <a name="square-logo-image"></a>Imagem de logotipo quadrado
DESCRIÇÃO | Restrições | Recomendações
------- | ------- | ----------
Essa imagem é exibida durante a instalação de novos computadores Enterprise Windows 10. Ela fornece contexto para os funcionários quando eles configuram seu novo computador de trabalho. A imagem é exibida para locatários que usam o [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) para implantar seus dispositivos de trabalho e nas páginas de entrada de senha em outras experiências do Windows 10. | PNG (preferencial) ou JPG transparente<br>Dimensões da imagem: 240 x 240 px<br>Tamanho do arquivo: &lt; 10 KB | Use o logotipo da sua organização aqui.<br> Use uma imagem transparente.<br>Não suponha que a tela de fundo será branco.<br>Não adicione preenchimento ao seu logotipo na imagem ou o logotipo parecerá desproporcionalmente pequeno.

### <a name="show-option-to-remain-signed-in"></a>Mostrar opção para permanecer conectado
DESCRIÇÃO | Restrições | Recomendações
------- | ------- | ----------
A entrada do Azure AD fornece ao usuário a opção de permanecer conectado ao fechar e reabrir o navegador. Essa configuração oculta essa opção.<br>Defina como **Não** para ocultar essa opção dos usuários. | &nbsp; | Ocultar a opção não afeta o tempo de vida da sessão.<br>Alguns recursos do SharePoint Online e do Office 2010 dependem da capacidade dos usuários de poderem permanecer conectados. Se você definir essa opção como **Não**, os usuários poderão receber prompts adicionais e inesperados de entrada.

> [!NOTE]
> Todos os elementos são opcionais. Por exemplo, se você especificar um logotipo de faixa sem nenhuma imagem de tela de fundo, a página de entrada mostrará seu logotipo e a imagem de tela de fundo para o site de destino (por exemplo, Office 365).

## <a name="add-company-branding-to-your-directory"></a>Adicionar identidade visual da empresa ao diretório

1. Entre no [centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global para o locatário.
2. Selecione **Usuários e Grupos** > **Identidade visual da empresa** > **Editar**.
  
  ![Abrindo uma identidade visual personalizada](./media/customize-branding/navigation-to-branding.png)
3. Modifique os elementos que você deseja personalizar. Todos os elementos são opcionais.
  
  ![Editar identidade visual personalizada](./media/customize-branding/edit-branding.png)
5. Quando terminar, selecione **Salvar**.

Pode demorar até uma hora para que apareçam todas as alterações feitas à identidade visual da página de entrada.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Adicionar identidade visual da empresa específica a um idioma ao diretório

1. Entre no [centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global para o diretório.
2. Selecione **Usuários e grupos** > **Identidade visual da empresa** > **Novo idioma**.
  
  ![Adicionar elementos de identidade visual específicos do idioma](./media/customize-branding/add-language.png)
5. Modifique os elementos que você deseja personalizar. Todos os elementos são opcionais.
6. Quando terminar, selecione **Salvar**.

Pode demorar até uma hora para que apareçam todas as alterações feitas à identidade visual da página de entrada.

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você aprendeu a adicionar identidade visual da empresa ao seu diretório do Azure AD. 

Você pode usar o link a seguir para configurar a identidade visual da empresa no Azure AD no portal do Azure.

> [!div class="nextstepaction"]
> [Configurar a identidade visual da empresa](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 