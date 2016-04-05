<properties
	pageTitle="Adicionar a identidade visual da empresa às páginas de Entrada e do Painel de Acesso"
	description="Saiba como adicionar uma identidade visual à página de entrada do Azure e à página do painel de acesso da empresa"
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
	ms.date="03/23/2016" 
	ms.author="MarkVi"/>

# Adicionar a identidade visual da empresa às páginas de Entrada e do Painel de Acesso

Para evitar confusão, muitas empresas desejam aplicar uma aparência consistente em todos os sites e serviços que elas gerenciam. O Azure Active Directory fornece esse recurso, permitindo que você personalize a aparência das seguintes páginas Web com o esquema de cor e o logotipo da empresa:

- **Página de entrada** - é a página que aparece quando você entra no Office 365 ou em outros aplicativos baseados na Web que estejam usando o AD do Azure como provedor de identidade. Você interage com essa página durante uma Descoberta de Realm Inicial ou para inserir suas credenciais. A Descoberta de Realm Inicial permite que o sistema redirecione usuários federados para seu STS local (como o AD FS).

- **Página Painel de Acesso** - o Painel de Acesso é um portal baseado na Web que permite exibir e iniciar os aplicativos baseados em nuvem aos quais o administrador do AD do Azure concedeu acesso a você. Para acessar o Painel de Acesso, use a seguinte URL: [https://myapps.microsoft.com](https://myapps.microsoft.com).

Este tópico explica como você pode personalizar a página de entrada e a página do painel de acesso.

> [AZURE.NOTE]
>
- A identidade visual da empresa é um recurso que está disponível somente se você atualizou para a edição Premium ou Basic do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
- As edições Premium e Basic do Active Directory do Azure estão disponíveis para clientes na China usando a instância mundial do Active Directory do Azure. As edições Azure Active Directory Premium e Basic não têm suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).



## Personalização da página de entrada

Normalmente, você usará a página de entrada se precisar de acesso baseado em navegador para seus aplicativos e serviços de nuvem que sua organização assina. <br> Se você tiver as alterações aplicadas à sua página de entrada, poderá demorar até uma hora para que as alterações sejam exibidas.

Uma página de entrada com marca só aparece quando você visita um serviço com uma URL específica do locatário, como https://outlook.com/**contoso**.com ou https://mail.**contoso**.com.

Quando você visita um serviço com URLs que não são específicas do locatário (por exemplo: https://mail.office365.com), uma página de entrada sem marca é exibida. Nesse caso, sua identidade visual aparecerá assim que você inserir sua ID do usuário ou que tiver selecionado um bloco de usuário.

> [AZURE.NOTE]
>
- O nome do seu domínio deve aparecer como "Ativo" na seção **Active Directory** > **Diretório** > **Domínios** do portal clássico do Azure onde você configurou a identidade visual.
- A identidade visual da página de entrada não se transfere para a página de entrada do consumidor da Microsoft. Se você entrar com uma conta da Microsoft pessoal (antes conhecida como Windows Live ID), talvez veja uma lista com identidade visual de blocos de usuários renderizados pelo AD do Azure, mas a identidade visual da sua organização não se aplicará à página de entrada da conta da Microsoft.


Se você quiser mostrar a marca da empresa, cores e outros elementos personalizáveis nessa página, consulte as imagens a seguir para compreender a diferença entre as duas experiências.

A captura de tela a seguir mostra um exemplo de página de entrada do Office 365 em um computador desktop *antes* de uma personalização:

![Página de entrada do Office 365 antes da personalização][1]

Após uma personalização, a página teria esta aparência:

![Página de entrada do Office 365 após a personalização][2]

A captura de tela a seguir mostra um exemplo de página de entrada do Office 365 em um computador desktop antes de uma personalização:

![Página de entrada do Office 365 antes da personalização][3]

Após uma personalização, a página teria esta aparência:

![Página de entrada do Office 365 após a personalização][4]


Quando você redimensiona uma janela de navegador, a ilustração grande, como a mostrada anteriormente, quase sempre será cortada para acomodar as diferentes proporções de tela. Com isso em mente, você deve tentar manter os principais elementos visuais na ilustração, para que eles sempre apareçam no canto superior esquerdo (superior direito para idiomas da direita para esquerda). Isso é importante porque o redimensionamento geralmente ocorre do canto inferior direito até o superior/esquerdo ou da parte inferior até a superior.

A figura a seguir mostra como a ilustração será cortada quando o navegador for redimensionado à esquerda:

![][6]

Veja como ela será mostrada depois que o navegador for redimensionado em direção à parte superior:

![][7]

## Quais elementos posso personalizar na página?

Você pode personalizar os seguintes elementos na página de entrada:

![][5]

 Elemento de página | Local na página
	------------- | -------------
Logotipo de faixa | Mostrado na parte superior direita da página. Substitua o logotipo que normalmente seria exibido pelo site de destino no qual você está entrando (por exemplo, Office 365 ou Azure).
Ilustração grande/cor da tela de fundo | Mostrado à esquerda da página. Substitua a imagem que normalmente seria exibida pelo site de destino no qual você está entrando. A cor da tela de fundo pode ser mostrada no lugar da ilustração grande em conexões de baixa largura de banda ou em telas muito estreitas.
Texto da página de entrada | Mostrado acima do rodapé da página quando você precisa transmitir informações úteis antes de uma entrada com uma conta corporativa ou de estudante. Por exemplo, você pode querer incluir o número de telefone da assistência técnica ou uma instrução legal.

> [AZURE.NOTE]
Todos os elementos são opcionais. Por exemplo, se você especificar um logotipo de faixa, mas nenhuma ilustração grande, a página de entrada mostrará o logotipo e ilustração do site de destino (ou seja, a imagem da autoestrada da Califórnia do Office 365).

Você também pode localizar todos os elementos desta página. Depois de configurar um elemento “padrão” de personalização, você pode configurar versões adicionais para localidades diferentes. Você também pode misturar e combinar vários elementos. Por exemplo, você pode:

- Criar uma ilustração grande "padrão" que funciona para todas as culturas e depois criar versões específicas para o inglês e o francês. Quando você define seus navegadores com um desses dois idiomas, é exibida a imagem específica, enquanto a ilustração padrão é exibida para todos os outros idiomas.
- Configure logotipos diferentes para sua organização (por exemplo, versões em japonês ou hebraico).



## Personalização da página Painel de Acesso

A página Painel de Acesso é essencialmente uma página do portal para acesso rápido a aplicativos de nuvem aos quais seu administrador concedeu acesso a você. Nessa página, seus aplicativos são exibidos como blocos de aplicativo clicáveis.


A captura de tela a seguir mostra um exemplo de uma página do painel de acesso após a personalização.

![][8]

## Configurar seu diretório com a identidade visual da empresa

Você pode configurar um conjunto padrão de elementos personalizáveis por diretório no portal clássico do Azure. Depois que os padrões tiverem sido salvos, um administrador também terá a opção de adicionar versões localizadas de cada elemento para diferentes idiomas/localidades. Todos os elementos personalizáveis são opcionais.

Por exemplo, se você configurar um logotipo de faixa padrão, mas nenhuma ilustração grande, a página de entrada exibirá o logotipo no canto superior direito, mas a ilustração padrão do site será exibida.

Imagine a seguinte configuração:

- Um logotipo de faixa padrão e um texto de página de entrada em inglês 
- Um texto de página de entrada específico do idioma para alemão 

Se sua preferência de idioma for o alemão, você obterá o logotipo de faixa padrão com o texto em alemão.

Embora tecnicamente você possa configurar um conjunto diferente para cada idioma suportado pelo AD do Azure, recomendamos que mantenha o número de variações baixo, por motivos de desempenho e manutenção.

**Para adicionar a identidade visual da empresa ao seu diretório, execute as seguintes etapas:**

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador do diretório que você deseja personalizar.
2. Selecione o diretório que você deseja personalizar.
3. Na barra de ferramentas na parte superior, clique em **Configurar**.
4. Clique em **Personalizar Identidade Visual**.
4. Modifique os elementos que você deseja personalizar. Observe que todos os campos são opcionais.
5. Clique em **Salvar**.

Pode demorar até uma hora para que a nova alteração feita à identidade visual da página de entrada apareça.

**Para adicionar a identidade visual da empresa específica de idioma, execute as seguintes etapas:**

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador do diretório que você deseja personalizar.
2. Selecione o diretório que você deseja personalizar.
3. Na barra de ferramentas na parte superior, clique em **Configurar**.
4. Clique em **Personalizar Identidade Visual**.
2. Clique em **Adicionar identidade visual para um idioma específico**.
3. Selecione o idioma para o qual você deseja personalizar o logotipo e clique em **Avançar**.
3. Edite somente os elementos para os quais você deseja configurar substituições específicas de idioma. Observe que todos os campos são opcionais. Se um campo for deixado em branco, o valor padrão personalizado será exibido no lugar (ou o padrão da Microsoft se um padrão personalizado não estiver configurado).
4. Clique em **Salvar**.

**Para remover a identidade visual da empresa do seu diretório, execute as seguintes etapas:**

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador do diretório que você deseja personalizar.
2. Selecione o diretório que você deseja personalizar.
3. Na barra de ferramentas na parte superior, clique em **Configurar**.
4. Clique em **Personalizar Identidade Visual**.
5. Na página Personalizar Identidade Visual, selecione **Editar Configurações de Identidade Visual Existentes** e vá para a próxima página.
3. Dependendo de quais elementos você deseja remover, siga um ou mais destes procedimentos:

	a. Em **Logotipo de Faixa**, selecione **Remover logotipo carregado**.

    b. Em **Logotipo de Bloco**, selecione **Remover logotipo carregado**.

    c. Remova o texto de todas as caixas de texto.

    d. Clique em **Próximo**.

    e. Remova o texto de todas as caixas de texto.

4. Clique em **Salvar** para remover os elementos.
5. Se necessário, clique em **Personalizar Identidade Visual** novamente e repita essas etapas para todos as identidades visuais específicas de idioma que precisam ser removidas. Todas as configurações de identidade visual são removidas quando você clica em **Personalizar Identidade Visual** e vê o formulário **Personalizar Identidade Visual Padrão** sem as configurações existentes.

## Testes e exemplos

É recomendável que você faça experiências com um locatário de teste antes de fazer alterações em seu ambiente de produção.<br> **Para verificar se sua identidade visual foi aplicada:**

1. Abra uma sessão de navegador InPrivate ou Incognito. 
2. Visite https://outlook.com/contoso.com, substituindo contoso.com pelo domínio personalizado. 

Isso também funciona com domínios que se parecem com contoso.onmicrosoft.com.

Para ajudar você a criar conjuntos efetivos de personalização, personalizamos estas duas páginas de entrada fictícias:

- [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

Para testar as configurações específicas de idioma, você precisará modificar as preferências de idioma padrão no navegador Web para um idioma definido em sua personalização. No Internet Explorer, configure isso no menu **Opções da Internet**.

## Elementos personalizáveis

Alguns elementos personalizáveis no AD do Azure têm vários casos de uso. Você pode configurar os logotipos da empresa uma vez por diretório e eles serão usados nas páginas de entrada e Painel de Acesso. Alguns elementos personalizáveis são específicos da página de entrada. A tabela a seguir fornece detalhes para os diferentes elementos personalizáveis.

Nome | Descrição | Restrições | Recomendações
	------------- | ------------- | ------------- | -------------
Logotipo de faixa | O Logotipo de Faixa é exibido na página de entrada e no Painel de Acesso. | <p>JPG ou PNG</p><p>60x280 pixels</p><p>10 KB</p> | <p>Use o logotipo completo de sua organização (incluindo o pictograma e o logotipo)</p><p>Mantenha-o com menos de 30 pixels de altura para evitar a introdução de barras de rolagem em dispositivos móveis</p><p>Mantenha-o com menos de 4 KB</p><p>Use um PNG transparente (não presuma que a página de entrada sempre terá uma tela de fundo branca)</p>
Logotipo de organização lado a lado | (no momento, não é usado na página de entrada) No futuro, esse texto pode ser usado para substituir o pictograma genérico "conta corporativa ou de estudante" em diferentes locais da experiência. | <p>JPG ou PNG</p><p>120x120 pixels</p><p>10 KB</p> | <p>Mantenha simples (sem texto pequeno), já que essa imagem pode ser redimensionada para 50%
</p> |
Rótulo de nome de usuário da página de entrada | (no momento, não é usado na página de entrada). No futuro, esse texto pode ser usado para substituir a sequência de caracteres genérico "conta corporativa ou de estudante" em diferentes locais da experiência. Você pode defini-lo para algo como "Conta Contoso" ou "ID Contoso". | <p>Texto em Unicode, até 50 caracteres</p><p>Apenas texto sem formatação (sem links ou tags HTML)</p> | <p>Mantenha-o curto e simples</p><p>Pergunte aos usuários como eles geralmente se referem à conta corporativa ou de estudante que você fornece a eles.</p>
Texto da página de entrada | Esse texto clichê aparece abaixo do formulário da página de entrada e pode ser usado para comunicar instruções adicionais ou onde obter ajuda e suporte. | <p>Texto em Unicode, até 256 caracteres</p><p>Apenas texto sem formatação (sem links ou tags HTML)</p> | Mantenha abaixo de 250 caracteres (aproximadamente 3 linhas de texto)
Ilustração da página de entrada | A ilustração é uma imagem grande exibida na página de entrada à esquerda do formulário da página de entrada. | <p>JPG ou PNG</p><p>1420x1200</p><p>500 KB</p> | <p>1420 x 1200 pixels</p><p>Importante: mantenha o menor possível, idealmente abaixo de 200 KB. Se essa imagem for muito grande, ela afetará o desempenho da página de entrada quando a imagem não estiver armazenada em cache</p><p>Essa imagem quase sempre será cortada, para acomodar diferentes proporções de tela. Mantenha os elementos visuais primários no canto superior esquerdo (canto superior direito para idiomas RTL), pois o redimensionamento ocorrerá do canto inferior/direito para o superior/esquerdo, à medida que a janela do navegador é reduzida.</p>
Cor da tela de fundo da página de entrada | A cor da tela de fundo da página de entrada é usada na área à esquerda do formulário da página de entrada. Isso é visível mesmo quando não existe ilustração na página de entrada. | Deve ser uma cor RGB em formato hexadecimal (exemplo: #FFFFFF) | <p>A cor da tela de fundo pode ser mostrada no lugar da ilustração grande em conexões de baixa largura de banda</p><p>Sugerimos que você escolha a cor primária do Logotipo de Faixa</p>


## Próximas etapas

- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png

<!---HONumber=AcomDC_0330_2016-->