<properties
	pageTitle="Visualização do Active Directory B2C do Azure: perguntas frequentes | Microsoft Azure"
	description="Perguntas frequentes sobre o Active Directory B2C do Azure."
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Visualização Active Directory B2C do Azure: perguntas frequentes

Esta página responde a perguntas frequentes sobre a visualização AD B2C do Azure. Continue verificando as atualizações.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### Posso usar o AD B2C do Azure no locatário existente de AD do Azure, com base em funcionário?

No momento, os recursos do AD B2C do Azure não podem ser ativados no seu locatário existente do AD do Azure. É recomendável que você crie um locatário separado para usar recursos do AD B2C do Azure, ou seja, para gerenciar seus consumidores.

### Posso usar AD B2C do Azure para fornecer logon Social (Facebook e Google +) para o Office 365?

O AD B2C do Azure não pode ser usado com o Office 365. Em geral, ele não pode ser usado para fornecer autenticação para aplicativos SaaS (Salesforce, Workday, etc.). Ele fornece gerenciamento de identidades e acesso para aplicativos móveis e da Web voltados ao cliente e não se destina a cenários de funcionário ou parceiro.

### O que são "Contas locais" no AD B2C do Azure? Como elas são diferentes de "Conta corporativa ou de estudante" no AD do Azure?

Em um locatário do AD do Azure, cada usuário no locatário (exceto usuários com contas existente da Microsoft) entram com um endereço de email do formulário `<xyz>@<tenant domain>` em que `<tenant domain>` é um dos domínios verificados no locatário ou do domínio inicial `<...>.onmicrosoft.com`. Esse tipo de conta é uma "conta corporativa ou de estudante", também conhecida como uma "conta organizacional".

Em um locatário AD B2C do Azure, a maioria dos aplicativos quer que o usuário entre com qualquer endereço de email arbitrário (exemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com ou jim@live.com)). Esse tipo de conta é "conta local". Hoje, também apoiamos nomes de usuário arbitrários (apenas sequências de caracteres simples) como contas locais (exemplo, joe, bob, sarah ou jim). Você pode escolher um destes dois “tipos” de conta no serviço do AD B2C do Azure.

### A quais provedores de identidade social você oferece suporte? A quais você planeja oferecer suporte no futuro?

No momento, oferecemos suporte a Facebook, Google+, LinkedIn e Amazon. Vamos adicionar suporte para a Conta da Microsoft e outros provedores de identidade social populares com base na demanda do cliente.

### Posso configurar “escopos” para obter mais informações sobre os consumidores de vários provedores de identidade sociais?

Não, mas esse recurso está em nosso roteiro. Os escopos de padrão usados para nosso conjunto com suporte de provedores de identidade social são:

- Facebook: email
- Google +: email
- Amazon: perfil
- LinkedIn: r\_emailaddress r\_basicprofile

### Meu aplicativo precisa ser executado no Azure para ele funcionar com o AD B2C do Azure?

Não, você pode hospedar seu aplicativo em qualquer lugar (na nuvem ou localmente). Tudo o que precisa para interagir com o AD B2C do Azure é a capacidade de enviar e receber solicitações HTTP em pontos de extremidade acessíveis publicamente.

### Vários diretórios do AD B2C do Azure. Como posso gerenciá-los no Portal de Visualização do Azure?

Cada locatário AD B2C do Azure tem sua própria folha de recursos B2C no portal de visualização do Azure. Leia [aqui](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sobre como você navegar até uma folha específica de recursos B2C do locatário no portal de visualização do Azure. Alternando entre diretórios do AD B2C do Azure no portal de visualização do Azure não manterá a folha de recursos do B2C aberta na maioria dos navegadores.

### Como personalizar os emails de verificação (o campo de remetente e o conteúdo, ou seja, o campo “De:") enviados pelo AD B2C do Azure?

Leia [este artigo na personalização da interface de usuário](active-directory-b2c-reference-ui-customization.md) para obter mais detalhes.

### Como migrar meus de nomes de usuário, senhas e perfis do meu banco de dados para o AD B2C do Azure?

Você pode usar o Graph API do AD do Azure (consulte o nosso exemplo [aqui](active-directory-b2c-devquickstarts-graph-dotnet.md)) para gravar sua ferramenta de migração. Forneceremos várias opções de migração e ferramentas para uso imediato no futuro.

### É possível usar o Azure AD Connect para migrar identidades armazenadas no Active Directory local para AD B2C do Azure?

Não, o Azure AD Connect não foi projetado para funcionar com o AD B2C do Azure. Forneceremos várias opções de migração e ferramentas para uso imediato no futuro.

### O AD B2C do Azure funciona com sistemas CRM, como o Microsoft Dynamics?

Não atualmente. A integração desses sistemas está em nosso roteiro.

### O AD B2C do Azure funciona com o SharePoint local 2016 ou anterior?

Não atualmente. O B2C do AD do Azure não tem suporte para tokens SAML 1.1 que os aplicativos de portais / comércio eletrônico desenvolvem de acordo com as necessidades do SP locais. Observe que o AD B2C do Azure não é destinado ao cenário de compartilhamento de parceiro externo do Sharepoint; consulte [AD B2C do Azure](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### Quais recursos de relatórios e auditoria o AD B2C do Azure oferece? É o mesmo que o Azure AD Premium?

Não, o AD B2C do Azure não dá suporte ao mesmo conjunto de relatórios que o Azure AD Premium. O AD B2C do Azure lançará APIs básicas de relatórios e auditoria em breve.

### Posso localizar a interface de usuário das páginas atendidas pelo AD B2C do Azure? Quais são os idiomas com suporte?

Atualmente, AD B2C do Azure é otimizado para o inglês apenas. Planejamos implantar recursos de localização assim que possível.

### Posso usar minhas próprias URLs nas minhas páginas de inscrição e entrada atendidas pelo AD B2C do Azure? Por exemplo, alterar as URLs de login.microsoftonline.com para login.contoso.com?

Não atualmente. Esse recurso está em nosso roteiro. Observe também que “verificar” seu domínio na guia **Domínios** do seu locatário no portal do Azure não fará isso.

### Posso obter AD B2C do Azure como parte do EMS (Enterprise Mobility Suite)?

Não, o AD B2C do Azure é um serviço pré-pago do Azure e não faz parte do EMS.

### Como faço para relatar problemas com o AD B2C do Azure?

Confira [este tópico de suporte](active-directory-b2c-support.md) no AD B2C do Azure.

### Quando o AD B2C do Azure estará disponível?

Não podemos fornecer informações sobre a data de disponibilidade geral no momento.

## Mais informações

Também convém analisar as [restrições de visualização, restrições e limitações](active-directory-b2c-limitations.md) atuais.

<!---HONumber=Oct15_HO3-->