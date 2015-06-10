<properties
   pageTitle="Diretrizes de identidade visual para aplicativos"
   description="Um guia abrangente para recursos para desenvolvedores do Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/29/2015"
   ms.author="mbaldwin"/>


# Diretrizes de identidade visual para aplicativos


Este tópico discute as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos com o Azure Active Directory. Essas diretrizes ajudarão a direcionar os clientes quando eles quiserem usar sua conta comercial ou de estudante, gerenciada no Azure AD, para inscrição e logon no seu aplicativo.

## Contas pessoais versus contas comerciais ou de estudante da Microsoft

A Microsoft gerencia dois tipos de contas de usuário:

- **Contas pessoais** (anteriormente conhecidas como Windows Live ID). Essas contas representam a relação entre usuários *individuais* e a Microsoft e são usadas para acessar serviços e dispositivos do cliente da Microsoft. Essas contas são destinadas a uso pessoal.

- **Contas comerciais ou de estudante.** Essas contas são gerenciadas pela Microsoft em nome de organizações que usam o Azure Active Directory. Essas contas são usadas para fazer logon no Office 365 e em outros serviços comerciais da Microsoft.

As contas comerciais ou de estudante da Microsoft  normalmente são atribuídas aos usuários finais (funcionários, estudantes e funcionários federais) por suas organizações (empresa, escola, órgão do governo). Essas contas são dominadas diretamente na nuvem, na plataforma do Azure AD ou sincronizadas para o Azure AD a partir de um diretório local, como o Windows Server Active Directory. A Microsoft tem a *custódia* das contas comerciais ou de estudante, mas as contas são de propriedade e controladas pela organização.

## Fazendo referência às contas do Azure AD em seu aplicativo

A Microsoft não expõe os usuários finais ao Azure ou os nomes de marca do Active Directory, nem você deve fazê-lo.

- Depois que os usuários estiverem conectados, você deve usar o nome e o logotipo da organização o máximo possível. Isso é melhor do que usar termos genéricos como "sua organização".

- Quando os usuários não estiverem conectados, você deve fazer referência às suas contas como "contas comerciais ou de estudante" e usar o logotipo da Microsoft para informar que essas contas são gerenciadas pela Microsoft. Não use termos como "conta comercial", "conta empresarial" ou "conta corporativa", que criam confusão no usuário.

## Imagem da conta de usuário
Em uma versão anterior dessas diretrizes, recomendamos usar uma imagem de "crachá azul". Com base nos comentários de usuários e de desenvolvedores, agora recomendamos o uso do logotipo da Microsoft em vez disso. Isso ajudará os usuários a entender que eles podem reutilizar a conta que usam com o Office 365 ou com outros serviços corporativos da Microsoft para fazer logon no seu aplicativo.

## Inscrever-se e entrar no Azure AD

Seu aplicativo pode apresentar caminhos separados para inscrição e login, e as seções a seguir fornecem orientação visual para os dois cenários.

**Se seu aplicativo oferecer suporte à inscrição do usuário final (por exemplo, gratuito avaliação ou modelo freemium)**: você pode mostrar um botão de **login** que permite aos usuários acessar seu aplicativo com suas contas comerciais ou de estudante da Microsoft. O Azure AD mostrará um prompt de consentimento da primeira vez que acessar seu aplicativo.

**Se seu aplicativo precisar de permissões que somente os administradores podem conferir, ou se seu aplicativo requer licenciamento organizacional**: você deve separar a aquisição do administrador do logon do usuário. O **botão "obter esse aplicativo"** redirecionará os administradores para fazer logon e pedir que eles concedam autorização em nome dos usuários em sua organização. Isso tem o benefício adicional de suprimir as solicitações de consentimento aos usuários finais para seu aplicativo.

## Orientação visual para aquisição de aplicativo

O link "obtenha o aplicativo" deve redirecionar o usuário para a página de concessão de acesso (autorização) Azure AD, para permitir que o administrador da organização autorize seu aplicativo para ter acesso aos dados de sua organização que são hospedados pela Microsoft. Detalhes sobre como solicitar acesso são discutidos no tópico [Adicionar, atualizar e remover um aplicativo](https://msdn.microsoft.com/library/azure/dn132599.aspx).

Depois que os administradores derem consentimento ao seu aplicativo, eles podem optar por adicioná-lo à experiência do inicializador de aplicativos do Office 365 de seus usuários (acessível a partir do waffle e em [https://portal.office.com/myapps](https://portal.office.com/myapps)). Se você deseja anunciar esse recurso, você pode usar termos como "Adicione esse aplicativo para sua organização" e mostrar um botão como este:

![Cenários e tipos de aplicativos](./media/active-directory-branding-guidelines/add-to-my-org.png)
  
No entanto, recomendamos que você escreva um texto explicativo em vez de depender de botões. Por exemplo:
> *Se você já usa o Office 365 ou outro serviço corporativo da Microsoft, você pode simplesmente conceder <your_app_name> acesso aos dados da sua organização. Isso permitirá que os usuários obtenham acesso <your_app_name> com suas contas de trabalho existentes.*


## Orientação visual para logon
Seu aplicativo deve exibir um botão de logon que redireciona os usuários ao ponto de extremidade de login, que corresponde ao protocolo que você usa para integrar com o Azure AD. A seção a seguir fornece detalhes sobre como deve ser esse botão.

### Imagem e "conta comercial ou de estudante" 
É a associação do logotipo da Microsoft e dos termos genéricos "comercial ou de estudante" que representa exclusivamente o Azure AD entre outros provedores de identidade aos quais seu aplicativo pode oferecer suporte. Se você não tiver espaço suficiente para "contas comerciais ou de estudante", não tem problema reduzir o termo para "conta comercial".
 
![Cenários e tipos de aplicativos](./media/active-directory-branding-guidelines/work-or-school-account.png)

![Cenários e tipos de aplicativos](./media/active-directory-branding-guidelines/work-account.png)

Você também pode fornecer uma explicação adicional para ajudar os usuários finais a reconhecer se eles podem usar esse botão:

![Cenários e tipos de aplicativos](./media/active-directory-branding-guidelines/work-account-with-explaination.png)
 
## Regras de identidade visual
**USE** "conta comercial ou de estudante" em combinação com o logotipo da Microsoft para representar logons no Azure AD. Se o espaço for um fator determinante, não tem problema usar o termo "conta comercial", mas **NÃO** use outros termos como "conta empresarial", "conta de negócios" ou "conta corporativa".

**NÃO** use "ID do Office 365" ou "ID do Azure". Office 365 também é o nome de uma oferta ao consumidor da Microsoft que não usa o Azure AD para autenticação.

**NÃO** altere o logotipo da Microsoft.

**NÃO** exponha os usuários finais às marcas do Azure ou Active Directory. No entanto, é possível usar esses termos com desenvolvedores, profissionais de TI e administradores.

## Regras de navegação

**Forneça** uma maneira de os usuários fazerem logout e trocarem para outra conta de usuário. Embora a maioria das pessoas tenha uma única conta pessoal da Microsoft/Facebook/Google/Twitter, as pessoas geralmente são associadas a mais de uma organização. O suporte a vários usuários conectados estará disponível em breve.

## Suporte ao Azure AD e a contas da Microsoft em seu aplicativo

Se seu aplicativo oferecer suporte ao Azure AD e a contas da Microsoft, você precisa incluir dois botões de login separados em seu aplicativo. Estamos trabalhando ativamente em uma atualização que permitirá que você integre uma vez e ofereça suporte a contas pessoais e comerciais da Microsoft. Quando isso estiver disponível, você poderá mostrar um único botão de "login na Microsoft" em seu aplicativo.

<!---HONumber=58-->