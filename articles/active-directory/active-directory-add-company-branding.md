---
title: "Adicionar identidade visual da empresa às páginas de entrada e Painel de Acesso no Azure Active Directory"
description: "Saiba como adicionar uma identidade visual à página de entrada do Azure e à página do painel de acesso da empresa"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/07/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 6d4fbfe97288fcb76628b45649b8b678152198a9
ms.lasthandoff: 04/07/2017


---
# <a name="add-company-branding-to-sign-in-and-access-panel-pages"></a>Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso
Muitas organizações desejam aplicar uma aparência consistente em todos os sites e serviços que gerenciam. O Azure Active Directory fornece esse recurso, permitindo que os profissionais de TI personalizem a aparência das páginas da Web a seguir com imagens e logotipos da empresa:

* **Página de entrada** - essa é a página que aparece quando os funcionários e parceiros de negócios convidados entram no Office 365 ou outros aplicativos que usam o Azure AD.
* **Página Painel de Acesso** - o Painel de Acesso é um portal baseado na Web que permite exibir e iniciar os aplicativos baseados em nuvem para os quais o administrador do Azure AD concedeu acesso a você. O painel de acesso pode ser encontrado em: [https://myapps.microsoft.com](https://myapps.microsoft.com).

Este tópico explica como você pode personalizar a página de entrada e a página do painel de acesso.

> [!NOTE]
> * A identidade visual da empresa estará disponível somente se você atualizar para a edição Premium ou Basic do Azure Active Directory ou tiver uma licença do Office 365. Para obter mais informações, confira Edições do Azure Active Directory.
> 
> * As edições Premium e Basic do Active Directory do Azure estão disponíveis para clientes na China usando a instância mundial do Active Directory do Azure. As edições Azure Active Directory Premium e Basic não têm suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no Fórum do Azure Active Directory.


## <a name="customizing-the-sign-in-page"></a>Personalização da página de entrada
Os usuários geralmente interagem com a página de entrada do Azure AD durante a tentativa de acessar aplicativos em nuvem e os serviços que sua organização assina.

Se você aplicou alterações de identidade visual para sua página de entrada, pode demorar até uma hora para que as alterações sejam exibidas para os usuários finais.

Elementos de identidade visual da empresa serão exibidos na página de entrada do Azure AD quando os usuários acessarem uma URL específica de locatário, como https://outlook.com/contoso.com.

Quando os usuários acessam um serviço em uma URL genérica como www.office.com, a página de entrada ainda não contém informações de identidade visual da empresa porque o sistema não sabe quem é o usuário. No entanto, a identidade visual da empresa aparece depois que os usuários inserem sua ID de usuário ou selecionam uma imagem do usuário.

> [!NOTE]
> * O nome do seu domínio deve aparecer como "Ativo" na seção **Active Directory** > **Diretório** > **Domínios** do Portal Clássico do Azure no qual você configurou a identidade visual.
> * A identidade visual da página de entrada não é transferida para a página de logon para contas pessoais da Microsoft. Se os funcionários ou parceiros de negócios convidados entrarem com uma conta pessoal da Microsoft, sua página de entrada não refletirá a identidade visual da organização.
>

As capturas de tela a seguir explicam como as páginas de entrada são personalizadas.

### <a name="scenario-1-contoso-employee-goes-to-a-generic-app-url-for-example-wwwofficecom"></a>Cenário 1: um funcionário da Contoso acessa uma URL de aplicativo genérica (por exemplo, www.office.com)

Neste exemplo, um usuário da Contoso entra em um aplicativo móvel ou um aplicativo Web usando uma URL genérica. A ilustração à esquerda sempre representará o aplicativo, e o painel de interação à direita será atualizado para mostrar os elementos de marca Contoso, quando apropriado.

![Página de entrada do Office 365 antes e após a personalização][1]

### <a name="scenario-2-contoso-employee-goes-to-contoso-app-thats-restricted-to-internal-users"></a>Cenário 2: um funcionário da Contoso acessa um aplicativo da Contoso que é restrito a usuários internos

Neste exemplo, um usuário da Contoso está entrando em um aplicativo interno usando uma URL específica da empresa. A ilustração à esquerda representa a marca da empresa (Contoso). O painel de interação à direita está bloqueado para a Contoso e ajuda os funcionários a entrar.

![página de entrada do aplicativo restrito][2]

### <a name="scenario-3-contoso-employee-goes-to-a-contoso-app-thats-open-to-external-users"></a>Cenário 3: um funcionário de Contoso acessa um aplicativo da Contoso que está aberto a usuários externos

Neste exemplo, os usuários estão se conectando a um aplicativo de LoB da Contoso, mas o usuário pode ou não ser um funcionário da Contoso. A ilustração à esquerda representa o proprietário do recurso (Contoso), assim como o caso \#2 acima. Porém, desta vez, o painel de interação à direita não está bloqueado para a Contoso, para indicar que os usuários externos podem entrar.

![entrar para abrir o acesso][3]

### <a name="scenario-4-fabrikam-business-guest-goes-to-contoso-app-thats-open-to-external-users"></a>Cenário 4: um convidado de negócios da Fabrikam acessa o aplicativo da Contoso que está aberto a usuários externos

Neste exemplo, um usuário da Contoso está entrando em um aplicativo interno usando uma URL específica da empresa. A ilustração à esquerda representa a marca da empresa (Contoso). O painel de interação à direita está bloqueado para a Contoso e ajuda os funcionários a entrar.

![entrar como um usuário externo][4]


## <a name="what-elements-on-the-page-can-i-customize"></a>Quais elementos posso personalizar na página?

Você pode personalizar os seguintes elementos na página de entrada:

![][5]

| Elemento de página | Local na página |
|:--- | --- |
| Logotipo de faixa | Mostrado na parte superior direita da página. Substitua o logotipo do aplicativo depois que a organização do usuário for determinada (geralmente, depois que o nome de usuário for inserido). |
| Ilustração de tela de fundo | Mostrado como uma imagem de sangramento completo no lado esquerdo da página de entrada. Substitui a ilustração do aplicativo, para cenários de logon com locatário (quando o usuário acessa um aplicativo publicado por sua própria organização ou uma organização na qual é um convidado comercial).<br>Em conexões de baixa largura de banda, a ilustração de tela de fundo é substituída por uma cor de tela de fundo. Em telas estreitas, como celulares, a ilustração não é mostrada.<br>A ilustração de tela de fundo será cortada quando os usuários redimensionarem o navegador. Ao projetar sua ilustração, mantenha os principais elementos visuais no canto superior esquerdo para que eles não sejam cortados. | 
| Caixa de seleção "Mantenha-me conectado" | Mostrado sob a caixa **Senha**. |
| Texto da página de entrada | Texto clichê a ser mostrado acima do rodapé da página. Pode ser usado para transmitir informações úteis para os usuários, como o número de telefone do suporte técnico ou uma instrução legal. |

> [!NOTE]
> Todos os elementos são opcionais. Por exemplo, se você especificar um logotipo de faixa, mas nenhuma ilustração de fundo, a página de entrada mostrará o logotipo e ilustração do site de destino (por exemplo, a imagem da autoestrada da Califórnia do Office 365).
>

Na página de entrada, a caixa de seleção **Mantenha-me conectado** permite que o usuário permaneça conectado ao fechar e reabrir o navegador e não afeta a duração da sessão.

A exibição da caixa de seleção depende da configuração de **Ocultar KMSI**.

![Ocultar configuração KMSI][6]

Para ocultar a caixa de seleção, defina essa configuração como **Oculta**.

> [!NOTE]
> Alguns recursos do SharePoint Online e do Office 2010 dependem da capacidade dos usuários marcarem essa caixa de seleção. Se definir essa configuração como oculta, os usuários poderão ver prompts de entrada adicionais e inesperados.
>
>

Você também pode localizar todos os elementos desta página. Depois de configurar um elemento “padrão” de personalização, você pode configurar mais versões para localidades diferentes. Você também pode misturar e combinar vários elementos. Por exemplo, você pode:

* Criar uma ilustração "padrão" que funciona para todas as culturas e depois criar versões específicas para o inglês e o francês. Quando você define seus navegadores com um desses dois idiomas, é exibida a imagem específica, enquanto a ilustração padrão é exibida para todos os outros idiomas.
* Configure logotipos diferentes para sua organização (por exemplo, versões em japonês ou hebraico).

## <a name="access-panel-page-customization"></a>Personalização da página Painel de Acesso
A página Painel de Acesso é essencialmente uma página do portal para acesso rápido a aplicativos de nuvem aos quais seu administrador concedeu acesso a você. Nessa página, seus aplicativos são exibidos como blocos de aplicativo clicáveis.

A captura de tela a seguir mostra um exemplo de uma página do painel de acesso após a personalização.

![][8]

## <a name="configure-your-directory-with-company-branding"></a>Configurar seu diretório com a identidade visual da empresa
Você pode configurar um conjunto padrão de elementos personalizáveis por diretório no portal clássico do Azure. Depois que os padrões tiverem sido salvos, um administrador poderá adicionar versões localizadas de cada elemento para diferentes idiomas/localidades. Todos os elementos personalizáveis são opcionais.

Por exemplo, se você configurar um logotipo de faixa padrão, mas nenhuma ilustração grande, a página de entrada exibirá o logotipo no canto superior direito. No entanto, a ilustração padrão do site é exibida.

Imagine a seguinte configuração:

* Um logotipo de faixa padrão e um texto de página de entrada em inglês
* Um Texto de Página de entrada específico do idioma alemão

Se sua preferência de idioma for o alemão, você obterá o logotipo de faixa padrão com o texto em alemão.

Embora tecnicamente você possa configurar um conjunto diferente para cada idioma suportado pelo AD do Azure, recomendamos que mantenha o número de variações baixo, por motivos de desempenho e manutenção.
 
**Para adicionar a identidade visual da empresa ao seu diretório, execute as seguintes etapas:**

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador do diretório que você deseja personalizar.
2. Selecione o diretório que você deseja personalizar.
3. Na barra de ferramentas na parte superior, clique em **Configurar**.
4. Clique em **Personalizar Identidade Visual**.
5. Modifique os elementos que você deseja personalizar. Todos os campos são opcionais.
6. Clique em **Salvar**.

Pode demorar até uma hora para que a nova alteração feita à identidade visual da página de entrada apareça.

**Para adicionar a identidade visual da empresa específica de idioma, execute as seguintes etapas:**

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador do diretório que você deseja personalizar.
2. Selecione o diretório que você deseja personalizar.
fs3. Na barra de ferramentas na parte superior, clique em **Configurar**.
4. Clique em **Personalizar Identidade Visual**.
5. Clique em **Adicionar identidade visual para um idioma específico**.
6. Selecione o idioma para o qual você deseja personalizar o logotipo e clique em **Avançar**.
7. Edite somente os elementos para os quais você deseja configurar substituições específicas de idioma. Todos os campos são opcionais. Se um campo for deixado em branco, o valor padrão personalizado será exibido em seu lugar (ou o padrão da Microsoft se um padrão personalizado não estiver configurado).
8. Clique em **Salvar**.

**Para remover a identidade visual da empresa do seu diretório, execute as seguintes etapas:**

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador do diretório que você deseja personalizar.
2. Selecione o diretório que você deseja personalizar.
3. Na barra de ferramentas na parte superior, clique em **Configurar**.
4. Clique em **Personalizar Identidade Visual**.
5. Na página Personalizar Identidade Visual, selecione **Editar Configurações de Identidade Visual Existentes** e vá para a próxima página.
6. Dependendo de quais elementos você deseja remover, siga um ou mais destes procedimentos:

    a. Em **Logotipo de Faixa**, selecione **Remover logotipo carregado**.

    b. Em **Logotipo de Bloco**, selecione **Remover logotipo carregado**.

    c. Remova o texto de todas as caixas de texto.

    d. Clique em **Avançar**.

    e. Remova o texto de todas as caixas de texto.
7. Clique em **Salvar** para remover os elementos.
8. Se necessário, clique em **Personalizar Identidade Visual** novamente e repita essas etapas para todos as identidades visuais específicas de idioma que precisam ser removidas.
    Todas as configurações de identidade visual são removidas quando você clica em **Personalizar Identidade Visual** e vê o formulário **Personalizar Identidade Visual Padrão** sem as configurações existentes.


## <a name="customizable-elements"></a>Elementos personalizáveis
Logotipos de empresa são usados para as páginas de logon e do Painel de Acesso, enquanto outros elementos são usados apenas na página de entrada. A tabela a seguir fornece detalhes para os diferentes elementos personalizáveis.

| Nome | Descrição | Restrições | Recomendações |
| --- | --- | --- | --- |
| Logotipo de faixa |O Logotipo de faixa é exibido na página de entrada e no Painel de Acesso. |<p>JPG ou PNG</p><p>60 x 280 pixels</p><p>10 KB</p> |<p>Use o logotipo completo da sua organização (incluindo pictograma e logotipo)</p><p>Mantenha-o com menos de 30 pixels de altura para evitar a introdução de barras de rolagem em dispositivos móveis</p><p>Mantenha-o com menos de 4 KB</p><p>Use um PNG transparente (não presuma que a página de entrada sempre terá uma tela de fundo branca)</p> |
| Logotipo em bloco | Não usado no momento |<p>JPG ou PNG</p><p>120 x 120 pixels</p><p>10 KB</p> |<p>Mantenha simples (sem texto pequeno), já que essa imagem pode ser redimensionada para 50% |
| </p> | | | |
| Rótulo de nome de usuário de entrada | Não usado no momento |<p>Texto em Unicode, até 50 caracteres</p><p>Apenas texto sem formatação (sem links ou marcas HTML)</p> |<p>Mantenha-o curto e simples</p><p>Pergunte aos usuários como eles geralmente se referem à conta corporativa ou de estudante que você fornece a eles.</p> |
| Texto clichê da página de entrada |Esse texto clichê aparece abaixo do formulário da página de entrada e pode ser usado para comunicar instruções adicionais ou onde obter ajuda e suporte. |<p>Texto em Unicode, até 256 caracteres</p><p>Apenas texto sem formatação (sem links ou marcas HTML)</p> |Mantenha abaixo de 250 caracteres (aproximadamente 3 linhas de texto) |
| Ilustração de tela de fundo na página de entrada | Imagem grande que é exibida à esquerda da página de entrada (à direita para idiomas RtL) quando os usuários acessam URLs específicas do locatário. |<p>JPG ou PNG</p><p>1.420 x 1.200</p><p>500 KB</p> |<p>1.420 x 1.200 pixels</p><p>Importante: mantenha o menor possível, idealmente abaixo de 200 KB. Se essa imagem for muito grande, ela afetará o desempenho da página de entrada quando a imagem não estiver armazenada em cache</p><p>Essa imagem quase sempre será cortada para acomodar proporções de tela diferentes. Mantenha os elementos visuais primários no canto superior esquerdo.</p> |
| Cor da tela de fundo da página de entrada | Em uma conexão de baixa largura de banda, essa cor sólida é usada em vez da ilustração de tela de fundo. | Deve ser uma cor RGB em formato hexadecimal (exemplo: \#FFFFFF) | É recomendável escolher a cor primária do logotipo do banner. |

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/signin-page_before-customization.png
[2]: ./media/active-directory-add-company-branding/signin-page-restricted-app.png
[3]: ./media/active-directory-add-company-branding/signin-page-open-access.png
[4]: ./media/active-directory-add-company-branding/signin-page-external-guest.png
[5]: ./media/active-directory-add-company-branding/which-elements-can-i-customize.png
[6]: ./media/active-directory-add-company-branding/hide-kmsi.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png

