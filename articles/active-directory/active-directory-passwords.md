<properties 
	pageTitle="O que é: Gerenciamento de Senhas do AD do Azure | Microsoft Azure"
	description="Descrição dos recursos de gerenciamento de senhas no AD do Azure, incluindo redefinição de senha, alteração, relatórios de gerenciamento de senhas e write-back para seu Active Directory local." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="asteen"/>

# Gerenciar suas senhas de qualquer lugar
O autoatendimento tem sido das principais metas para os departamentos de TI em todo o mundo, como uma medida de redução de custos e economia de trabalho. De fato, o mercado é inundado com produtos que permitem que você gerencie seus grupos locais, senhas ou perfis de usuário na nuvem ou no local. O AD do Azure se diferencia dessas ofertas, fornecendo alguns dos recursos de autoatendimento mais fáceis de usar e mais poderosos disponíveis atualmente.

O **Gerenciamento de Senhas do AD do Azure** é um conjunto de recursos que permitem que os usuários gerenciem qualquer senha de qualquer dispositivo, a qualquer momento, de qualquer local, enquanto permanecem em conformidade com as políticas de segurança que você define.

## Visão geral
Você pode começar com um piloto do gerenciamento de senhas do AD do Azure em menos de cinco minutos e pode implantá-lo em toda a sua organização em horas. Abaixo estão alguns recursos úteis para você começar a usar o serviço:

* [**Como funciona**](active-directory-passwords-how-it-works.md) - saiba mais sobre os seis diferentes componentes do serviço e o que cada um deles faz
* [**Introdução**](active-directory-passwords-getting-started.md) - saiba como permitir que os usuários redefinam e alterem suas senhas na nuvem ou no local
* [**Personalizar**](active-directory-passwords-customize.md) - aprenda a personalizar a aparência e o comportamento do serviço de acordo com as necessidades de sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - aprenda a implantar rapidamente e gerenciar com eficiência as senhas em sua organização
* [**Obter percepções**](active-directory-passwords-get-insights.md) - saiba mais sobre nossos recursos integrados de relatórios
* [**Perguntas frequentes**](active-directory-passwords-faq.md) - obtenha respostas para perguntas frequentes
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - aprenda a solucionar rapidamente os problemas com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) - aprofunde-se nos detalhes técnicos do funcionamento do serviço


## O que é possível com o Gerenciamento de Senhas do AD do Azure?
Aqui estão algumas das coisas que você pode fazer com os recursos de gerenciamento de senhas do AD do Azure.

- A alteração de **senha por autoatendimento** permite que os usuários finais ou os administradores alterem suas senhas expiradas ou não expiradas sem chamar um administrador ou o suporte técnico em busca de suporte.
- A redefinição de **senha por autoatendimento** permite que os usuários finais ou os administradores redefinam suas senhas automaticamente, sem chamar um administrador ou o suporte técnico em busca de suporte. A redefinição de senha por autoatendimento exige o AD Premium ou Basic do Azure. Para obter mais informações, consulte Edições do Active Directory do Azure.
- A **Redefinição de senha iniciada pelo administrador** permite que um administrador redefina a senha de um usuário final ou de outro administrador de dentro do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
- **Relatórios de atividade de gerenciamento de senha** fornecem aos administradores percepções sobre as atividades de registro e de redefinição de senha que ocorrem em suas organizações. 
- O **Write-back de senha** permite o gerenciamento de senhas locais na nuvem, de modo que todos os cenários acima podem ser executados por, ou em nome de, usuários federados ou sincronizados por senha. O Write-back de senha requer o AD Premium do Azure. Para saber mais, consulte Introdução ao AD Premium do Azure.

## Por que usar o Gerenciamento de Senhas do AD do Azure?
Aqui estão alguns dos motivos pelos quais você deve usar os recursos de gerenciamento de senhas do AD do Azure

- **Reduzir os custos** - a redefinição do suporte assistido por senha normalmente é de 20% nos gastos de TI da organização
- **Aprimorar as experiências do usuário** - os usuários não querem chamar a assistência técnica e passar uma hora no telefone sempre que esquecem suas senhas
- **Reduzir os volumes de assistência técnica** - o gerenciamento de senhas é gerador de maior volume de assistência técnica para a maioria das organizações
- **Habilitar a mobilidade** - os usuários podem redefinir suas senhas onde quer que estejam

## Atualizações de serviço recentes

**Atualizações úteis para a Página de Registro** - Outubro de 2015

- Agora, quando um usuário tiver dados já registrados, ele poderá simplesmente clicar em "boa aparência" para atualizar os dados sem a necessidade de enviar novamente o email ou fazer uma chamada telefônica.

**Confiabilidade Melhorada de Write-back de Senha** - Setembro de 2015

- A partir da versão de setembro do Azure AD Connect, agora o agente de write-back de senha repetirá com mais agressividade as conexões e os recursos de failover adicionais mais robustos.

**API para Recuperar os Dados de Relatório da Redefinição de Senhas ** - Agosto de 2015

- Agora, os dados dos relatórios da redefinição de senhas podem ser recuperados diretamente a partir dos [Relatórios do AD do Azure e da API de Eventos](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

**Suporte para a Redefinição de Senhas do AD do Azure Durante o Ingresso no Domínio em Nuvem** - Agosto de 2015

- Agora, qualquer usuário de nuvem pode redefinir sua senha diretamente na tela de conexão do Windows 10 durante a experiência de integração de associação do domínio em nuvem. Observe que isso ainda não é exibido na tela de conexão do Windows 10.

**Impor o Registro de Redefinição de Senhas ao Entrar no Azure e nos Aplicativos Federados** - Julho de 2015

- Além de aplicar o registro ao entrar em myapps.microsoft.com, agora temos suporte para impor o registro durante as conexões no Portal de Gerenciamento do Azure e em quaisquer aplicativos de logon único federados

**Suporte à Localização da Pergunta de Segurança** - Maio de 2015

- Agora, você tem a opção de selecionar perguntas de segurança predefinidas que estão localizadas na linguagem O365 completa definida ao configurar as Perguntas de Segurança para a redefinição de senha.

**Suporte para o Desbloqueio da Conta durante a Redefinição da Senha** - Junho de 2015

- Se você estiver usando o write-back de senha e redefinir sua senha quando a conta for bloqueada, poderemos desbloquear automaticamente sua conta do Active Directory!

**Registro SSPR de marca** - abril de 2015

- A página de registro de redefinição de senha agora contém a marca com o logotipo da empresa!

**Perguntas de segurança** - março de 2015

- Lançamos perguntas de segurança para GA!

**Desbloqueio de conta** - março de 2015

- Agora os usuários podem desbloquear suas contas quando ocorre uma redefinição de senha

## Em breve

A seguir estão alguns dos recursos interessantes nos quais estamos trabalhando agora!

**Suporte para Desbloquear as Contas do Active Directory sem redefinir uma Senha** - Em breve!

- Muitas pessoas solicitaram a capacidade de desbloquear as contas AD separadamente da redefinição da senha. Estamos muito contentes em anunciar que estamos dando os toques finais nesse recurso agora e em breve será lançado para qualquer pessoa que usar o write-back de senha!

**Suporte para Lembrar os Usuários para Atualizarem seu Dados Registrados Durante a Conexão** - Trabalho em andamento

- Atualmente, oferecemos suporte para lembrar os usuários para atualizarem seus dados registrados ao acessarem myapps.microsoft.com, mas estamos trabalhando na capacidade de fazer isso para todas as conexões.

**Impor o Registro de Redefinição de Senha na Conexão dos Aplicativos do Office 365** - Trabalho em andamento

- Cada vez mais aplicativos do Office estão se juntando à experiência de conexão do AD do Azure mais recente e melhor. Quando isso acontecer, eles serão suportados automaticamente para o registro imposto de SSPR!

<br/> <br/> <br/>

**Recursos adicionais**


* [Como funciona o gerenciamento de senhas](active-directory-passwords-how-it-works.md)
* [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md)
* [Personalizar o gerenciamento de senhas](active-directory-passwords-customize.md)
* [Práticas recomendadas de gerenciamento de senhas](active-directory-passwords-best-practices.md)
* [Como obter percepções operacionais com relatórios de gerenciamento de senhas](active-directory-passwords-get-insights.md)
* [Perguntas frequentes sobre Gerenciamento de Senhas](active-directory-passwords-faq.md)
* [Solucionar problemas do Gerenciamento de Senhas](active-directory-passwords-troubleshoot.md)
* [Saiba mais](active-directory-passwords-learn-more.md)
* [Gerenciamento de senhas no MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)

<!---HONumber=Oct15_HO4-->