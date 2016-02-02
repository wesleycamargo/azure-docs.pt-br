<properties
	pageTitle="Adicionar identidade visual da empresa às páginas de entrada e do painel de acesso"
	description="Um tópico que explica como uma organização pode aplicar aparência e funcionalidade consistentes em todos os sites e serviços que gerencia para que os usuários finais não sejam confundidos quando precisarem usar esses sites."
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
	ms.date="01/26/2016" 
	ms.author="MarkVi"/>

# Adicionar identidade visual da empresa às páginas de entrada e do painel de acesso

> [AZURE.NOTE]
>
- A identidade visual da empresa é um recurso que está disponível somente se você atualizou para a edição Premium ou Basic do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
- As edições Premium e Basic do Active Directory do Azure estão disponíveis para clientes na China usando a instância mundial do Active Directory do Azure. As edições Azure Active Directory Premium e Basic não têm suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato no [Fórum do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).

Muitas empresas querem aplicar aparência e funcionalidade consistentes em todos os sites e serviços que gerenciam para que os usuários finais não sejam confundidos quando precisarem usar esses sites. O Active Directory do Azure fornece esse recurso, permitindo que você personalize a aparência das seguintes páginas Web direcionadas ao usuário final, para que ele inclua o esquema de cor e o logotipo da empresa:

- **Página de entrada** - É para esta página que os usuários são redirecionados quando entram no Office 365 ou outros aplicativos modernos na Web que usam o AD do Azure como provedor de identidade. A maioria dos usuários irá interagir com esta página, seja para passar pela Descoberta de Realm Inicial, que permite que o sistema redirecione usuários federados para seu próprio STS local (como o AD FS), ou para inserir suas credenciais.

- **Página Painel de Acesso** - O painel de acesso é um portal na Web que permite que um usuário final com uma conta corporativa ou de estudante em um diretório AD do Azure, exiba e inicie aplicativos baseados em nuvem para os quais recebeu acesso do administrador de AD do Azure. O Painel de Acesso é acessível a todos os usuários da sua organização em myapps.microsoft.com.

## Personalização da página de entrada

A página de entrada é geralmente a página Web usada com mais frequência por usuários finais que precisam de acesso baseado em navegador até os serviços e aplicativos de nuvem que sua organização assina, garantindo que sua aparência seja proeminente. Se você quiser usar a experiência de página de entrada padrão sem a identidade visual, não precisa fazer nada.

### Quanto tempo leva para ver as alterações de identidade visual nas páginas de entrada?

Pode demorar até uma hora para que os usuários vejam qualquer alteração feita à identidade visual da página de entrada.

### Quando os usuários verão uma página de entrada com identidade visual?

O usuário verá uma página de entrada com identidade visual quando visitar um serviço com uma URL específica de locatário, como https://outlook.com/**contoso**.com, ou https://mail.**contoso**.com (se você tiver criado um CNAME).

Se o usuário visitar um serviço com URLs específicas de não locatário (como https://mail.office365.com) ele verá uma página de entrada sem identidade visual. A página de entrada será atualizada para mostrar a identidade visual quando os usuários tiverem inserido a ID de usuário ou selecionado uma organização lado a lado de usuário.

> [AZURE.NOTE]
>
- O nome do domínio deve aparecer como "Ativo" na seção **Active Directory** > **Diretório** > **Domínios** do Portal de Gerenciamento do Azure onde você configurou a identidade visual.
- A identidade visual da página de entrada não se transfere para a página de entrada do consumidor da Microsoft. Isso significa que os usuários que entram com uma conta pessoal da Microsoft (anteriormente Windows Live ID) podem ver uma lista de marcas de organização lado a lado do usuário renderizadas pelo AD do Azure, mas a identidade visual de sua organização não se aplicará à página de entrada da conta da Microsoft.

### O que meus usuários finais verão depois que eu personalizar a página de entrada?

Se você quiser mostrar a marca da empresa, cores e outros elementos personalizáveis nessa página, consulte as imagens a seguir para compreender a diferença entre as duas experiências.

Quando um usuário tenta entrar em um computador desktop, aqui está um exemplo do que o usuário veria na página de entrada do Office 365 *antes* da personalização:

![][1]

E aqui está o que o mesmo usuário veria *após* a personalização:

![][2]

Quando um usuário tenta entrar a partir de um dispositivo móvel, aqui está um exemplo do que o usuário veria na página de entrada do Office 365 *antes* da personalização:

![][3]

E aqui está o que o mesmo usuário veria *após* a personalização:

![][4]

### Quais elementos posso personalizar na página?

Você pode personalizar os seguintes elementos na página de entrada:

![][5]

 Elemento de página | Local na página
	------------- | -------------
Logotipo de faixa | Mostrado na parte superior direita da página. Substitua o logotipo que normalmente seria exibido pelo site de destino ao qual os usuários estão se conectando (por exemplo Office 365 ou Azure).
Ilustração grande/cor da tela de fundo | Mostrado à esquerda da página. Substitua a imagem que normalmente seria exibida pelo site de destino ao qual os usuários estão se conectando. A cor da tela de fundo pode ser mostrada no lugar da ilustração grande em conexões de baixa largura de banda ou em telas muito estreitas.
Texto da página de entrada | Mostrado acima do rodapé da página quando você precisa transmitir informações úteis aos usuários antes de eles entrar com sua conta corporativa ou de estudante. Por exemplo, você pode querer incluir o número de telefone da assistência técnica ou uma instrução legal.

> [AZURE.NOTE]
Todos os elementos são opcionais. Por exemplo, se você especificar um logotipo de faixa, mas nenhuma ilustração grande, a página de entrada mostrará o logotipo e ilustração do site de destino (ou seja, a imagem da estrada da Califórnia do Office 365).

Você também pode localizar todos os elementos desta página. Depois de configurar um elemento “padrão” de personalização, você pode configurar versões adicionais para localidades diferentes. Você também pode misturar e combinar vários elementos. Por exemplo, você pode:

- Criar uma ilustração grande "padrão" que funciona para todas as culturas e depois criar versões específicas para o inglês e o francês. Usuários com navegadores definidos para um desses dois idiomas verão a imagem específica, enquanto todos os outros verão a imagem padrão.
- Configure logotipos diferentes para sua organização (por exemplo, versões em japonês ou hebraico).

### Qual será a aparência da ilustração depois que o navegador for redimensionado?

Durante um redimensionamento de janela de navegador, a ilustração grande, como a mostrada anteriormente, quase sempre será cortada para acomodar as diferentes proporções de tela. Com isso em mente, você deve tentar manter os principais elementos visuais na ilustração, para que eles sejam sempre exibidos no canto superior esquerdo (superior direito para idiomas da direita para esquerda). Isso é importante porque o redimensionamento geralmente ocorre do canto inferior direito até o superior/esquerdo ou da parte inferior até a superior.

A figura a seguir mostra como a ilustração será cortada quando o navegador for redimensionado à esquerda:

![][6]

E aqui está como ele aparecerá depois que o navegador for redimensionado em direção à parte superior:

![][7]

## Personalização da página Painel de Acesso

A página Painel de Acesso é essencialmente uma página do portal para todos os usuários finais que precisam de acesso rápido, por meio de faixas de aplicativos clicáveis, para os diversos aplicativos na nuvem aos quais você tem acesso. Se você quiser usar a experiência da página Painel de Acesso sem a identidade visual, não precisa fazer nada.

### O que os usuários finais veem depois que personalizo a página Painel de Acesso?

![][8]

## Configurar seu diretório com a identidade visual da empresa

Um conjunto de elementos personalizáveis padrão pode ser configurado por diretório no Portal de Gerenciamento. Depois que os padrões são salvos, um administrador também tem a opção de adicionar versões localizadas de cada elemento a diferentes idiomas/locais. Todos os elementos personalizáveis são opcionais.

Por exemplo, se você configurar um logotipo de faixa padrão, mas nenhuma ilustração grande, a página de entrada exibirá o logotipo no canto superior direito, mas a ilustração padrão do site será exibida. Se você configurar um logotipo de faixa padrão e o texto da página de entrada em inglês e configurar o texto da página de entrada específico de idioma como alemão, usuários com preferência do idioma alemão verão seu logotipo de faixa padrão, mas o texto em alemão. Embora tecnicamente você possa configurar um conjunto diferente para cada idioma suportado pelo AD do Azure, recomendamos que mantenha o número de variações baixo, por motivos de desempenho e manutenção.

Para adicionar identidade visual da empresa ao diretório:

1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) como o administrador do diretório que você quer personalizar.
2. Selecione o diretório que você quer personalizar.
3. Selecione a guia **Configurar** e, em seguida, selecione **Personalizar Identidade Visual**.
4. Modifique os elementos que você quer personalizar. Observe que todos os campos são opcionais.
5. Clique em **Salvar**.

Pode demorar até uma hora para que os usuários vejam qualquer alteração feita à identidade visual da página de entrada.

Para adicionar identidade visual da empresa específica de idioma:

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), na guia **Configurar** selecione **Personalizar Identidade Visual**.
2. Selecione **Adicionar identidade visual a um idioma específico**, selecione o idioma para o qual você deseja personalizar o logotipo e, em seguida, clique em **Avançar**.
3. Edite somente os elementos para os quais você deseja configurar substituições específicas de idioma. Observe que todos os campos são opcionais. Se um campo for deixado em branco, o valor padrão personalizado será exibido no lugar (ou o padrão da Microsoft se um padrão personalizado não estiver configurado).
4. Clique em **Salvar**.

Para remover a identidade visual da empresa do diretório

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), na guia **Configurar** selecione **Personalizar Identidade Visual**.
2. Na página Personalizar Identidade Visual, selecione **Editar Configurações de Identidade Visual Existentes** e vá para a próxima página.
3. Dependendo de quais elementos você deseja remover, siga um ou mais destes procedimentos:
	1. Para logotipo de faixa, clique na caixa de seleção para **Remover logotipo carregado**.
    2. Para logotipo de organização lado a lado, clique na caixa de seleção para **Remover logotipo carregado**.
    3. Para o rótulo de nome de usuário da página de entrada, apague todo texto.
    4. Para texto da página de entrada, apague todo texto.
    5. Para ilustração da página de entrada, clique na caixa de seleção **Remover ilustração**.
    6. Para cor da tela de fundo da página de entrada, apague todo o texto.
4. Clique em **Salvar** para remover os elementos.
5. Se necessário, clique em **Personalizar Identidade Visual** novamente e repita essas etapas para todos as identidades visuais específicas de idioma que precisam ser removidas. Todas as configurações de identidade visual são removidas quando você clica em **Personalizar Identidade Visual** e vê o formulário **Personalizar Identidade Visual Padrão** sem as configurações existentes.

## Testes e exemplos

É recomendável que você experimente um locatário de teste antes de fazer alterações em seu ambiente de produção. A maneira mais simples de verificar se sua identidade visual foi aplicada é abrir uma sessão de navegador InPrivate ou Incognito e visitar https://outlook.com/contoso.com, substituindo contoso.com pelo domínio personalizado. Observe que isso também funciona com domínios que se parecem com contoso.onmicrosoft.com.

Para ajudá-lo a criar conjuntos eficientes de personalização, personalizamos as seguintes duas páginas de entrada fictícias:

- [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

Para testar as configurações específicas de idioma, você precisará modificar as preferências de idioma padrão no navegador Web para um idioma definido em sua personalização. No Internet Explorer, isso é configurado no menu **Opções da Internet**.

## Elementos personalizáveis

Alguns elementos personalizáveis no AD do Azure têm vários casos de uso. Logotipos da empresa podem ser configurados uma vez por diretório e são usados nas páginas de entrada e painel de acesso onde, como alguns elementos personalizáveis, são específicos somente à página de entrada. A tabela a seguir fornece detalhes para os diferentes elementos personalizáveis.

Nome | Descrição | Restrições | Recomendações
	------------- | ------------- | ------------- | -------------
Logotipo de faixa | O logotipo de faixa é exibido na página de entrada e no painel de acesso. | <p>JPG ou PNG</p><p>60x280 pixels</p><p>10 KB</p> | <p>Use o logotipo completo de sua organização (inclusive pictograma e logotipo)</p><p>Mantenha-o abaixo de 30 pixels de altura para evitar introduzir barras de rolagem em dispositivos móveis</p><p>Mantenha-o em 4 KB</p><p>Use um PNG transparente (não presuma que a página de entrada sempre terá uma tela de fundo branca)</p>
Logotipo de organização lado a lado | (atualmente não usado na página de entrada) No futuro, esse texto pode ser usado para substituir o pictograma genérico "conta corporativa ou de estudante" em diferentes locais da experiência. | <p>JPG ou PNG</p><p>120x120 pixels</p><p>10 KB</p> | <p>Mantenha simples (sem texto pequeno), já que essa imagem pode ser redimensionada para 50%
</p> |
Rótulo de nome de usuário da página de entrada | (atualmente não usado na página de entrada). No futuro, esse texto pode ser usado para substituir a sequência de caracteres genérico "conta corporativa ou de estudante" em diferentes locais da experiência. Você pode defini-lo para algo como "Conta Contoso" ou "ID Contoso". | <p>Texto em Unicode, até 50 caracteres</p><p>Apenas texto sem formatação (sem links ou tags HTML)</p> | <p>Mantenha-o curto e simples</p><p>Pergunte aos usuários como eles geralmente se referem à conta corporativa ou de estudante que você fornece a eles.</p>
Texto da página de entrada | Esse texto clichê aparece abaixo do formulário da página de entrada e pode ser usado para comunicar instruções adicionais, ou onde obter ajuda e suporte. | <p>Texto em Unicode, até 256 caracteres</p><p>Apenas texto sem formatação (sem links ou tags HTML)</p> | Mantenha abaixo de 250 caracteres (aproximadamente 3 linhas de texto)
Ilustração da página de entrada | A ilustração é uma imagem grande exibida na página de entrada à esquerda do formulário da página de entrada. | <p>JPG ou PNG</p><p>1420x1200</p><p>500 KB</p> | <p>1420 x 1200 pixels</p><p>Importante: mantenha o menor possível, idealmente abaixo de 200 KB. Se esta imagem for muito grande, ela irá impactar o desempenho da página de entrada quando a imagem não estiver armazenada em cache</p><p>Essa imagem quase sempre será cortada, para acomodar diferentes proporções de tela . Mantenha os elementos visuais primários no canto superior esquerdo (canto superior direito para idiomas RTL), pois o redimensionamento ocorrerá do canto inferior/direito para o superior/esquerdo, à medida que a janela do navegador é reduzida.</p>
Cor da tela de fundo da página de entrada | A cor da tela de fundo da página de entrada é usada na área à esquerda do formulário da página de entrada. Isso é visível mesmo quando não existe ilustração na página de entrada. | Deve ser uma cor RGB em formato hexadecimal (exemplo: #FFFFFF) | <p>A cor da tela de fundo pode ser mostrada no lugar da ilustração grande em conexões de baixa largura de banda</p><p>Sugerimos que você escolha a cor primária do logotipo da faixa</p>


## O que vem a seguir

- [Introdução ao Active Directory Premium do Azure](active-directory-get-started-premium.md)
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

<!---HONumber=AcomDC_0128_2016-->