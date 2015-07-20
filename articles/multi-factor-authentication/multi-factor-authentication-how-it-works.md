<properties 
	pageTitle="Azure Multi-Factor Authentication - Como funciona" 
	description="O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece segurança adicional, exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de opções de verificação fácil." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

#Como funciona o Azure Multi-Factor Authentication

A segurança da autenticação multifator baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga saber a senha do usuário, isso será inútil se ele também não tiver posse do dispositivo confiável. Se o usuário perder o dispositivo, quem encontrar esse dispositivo não conseguirá usá-lo, a menos que também saiba a senha do usuário.

![Prova](./media/multi-factor-authentication-how-it-works/howitworks.png)



O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece segurança adicional, exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de opções de verificação fácil:

- chamada telefônica 
- mensagem de texto
- notificação de aplicativos móveis, permitindo que os usuários escolham o método que preferirem
- código de verificação de aplicativo móvel
- Tokens OATH de terceiros

Para obter informações adicionais sobre como ele funciona, veja o vídeo a seguir.

[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##Métodos disponíveis para autenticação multifator
Quando um usuário entra, uma verificação adicional é enviada ao usuário. Veja a seguir uma lista de métodos que podem ser usados para essa segunda verificação.

Método de verificação | Descrição 
------------- | ------------- |
Chamada telefônica | É feita uma chamada para o telefone inteligente do usuário solicitando que verifique se está se conectando pressionando o sinal #. Isso concluirá o processo de verificação. Essa opção é configurável e pode ser alterada para um código que você especificar.
Mensagem de texto | Uma mensagem de texto será enviada para o smartphone do usuário com um código de 6 dígitos. Digite esse código para concluir o processo de verificação.
Notificação de aplicativo móvel | Uma solicitação de verificação será enviada ao smartphone do usuário solicitando que conclua a verificação, selecionando Verificar no aplicativo móvel. Isso ocorrerá se você selecionou a notificação do aplicativo como seu método de verificação principal. Se o usuário receber isso quando não estiver se conectando, é possível relatar como fraude.
Código de verificação co Aplicativos Móveis | Um código de verificação será enviado ao aplicativo móvel que está sendo executado no smartphone do usuário. Isso ocorrerá se você selecionou o código de verificação como seu método de verificação principal.


##Versões disponíveis do Azure Multi-Factor Authentication
O Azure Multi-Factor Authentication está disponível em três versões diferentes. A tabela a seguir descreve cada uma deças em mais detalhes.

Versão | Descrição 
------------- | ------------- |
Autenticação Multifator para Office 365 | Esta versão funciona exclusivamente com os aplicativos do Office 365 e é gerenciada a partir do portal do Office 365. Portanto, os administradores agora podem proteger seus recursos do Office 365 com a autenticação multifator. Esta versão é fornecida com uma assinatura do Office 365.
Autenticação Multifator para administradores do Azure | O mesmo subconjunto de recursos da Autenticação Multifator para Office 365 estará disponível sem custo algum para todos os administradores do Azure. Cada conta administrativa de uma assinatura do Azure agora pode obter proteção adicional, habilitando essa funcionalidade essencial de autenticação multifator. Portanto, um administrador que queira acessar o portal do Azure para criar uma VM, um site, gerenciar armazenamento, serviços móveis ou qualquer outro serviço do Azure poderá adicionar a autenticação multifator à sua conta de administrador.
Autenticação Multifator do Azure | O Azure Multi-Factor Authentication oferece o conjunto mais avançado de recursos. Ele fornece opções de configuração adicionais por meio do Portal de Gerenciamento do Azure, relatórios avançados e suporte para uma variedade de aplicativos locais e na nuvem. O Azure Multi-Factor Authentication vem como parte do Azure Active Directory Premium.

##Comparação de recursos dasversões
A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Azure Multi-Factor Authentication.


Recurso | Multi-Factor Authentication para Office 365 (incluído nas SKUs do Office 365)|A Autenticação Multifator para administradores do Azure (incluída com a assinatura do Azure) | O Multi-Factor Authentication (incluído no Azure AD Premium e Enterprise Mobility Suite) 
------------- | :-------------: |:-------------: |:-------------: |
Os administradores podem proteger contas com a MFA| * | * (Disponível apenas para contas de Administrador do Azure)|*
Aplicativos móveis como um fator secundário|* | * | *
Chamada telefônica como um fator secundário|* | * | *
SMS como um fator secundário|* | * | *
Senhas de aplicativos para clientes que não oferecem suporte a MFA|* | * | *
Controle do administrador sobre métodos de autenticação| | | *
Modo PIN| | | *
Alerta de fraude| | | *
Relatórios de MFA| | | *
Desvio único| | | *
Saudações personalizadas para chamadas telefônicas| | | *
Personalização da ID do chamador para chamadas telefônicas| | | *
Confirmação de evento| | | *
IPs Confiáveis| | | *
Suspender MFA para dispositivos lembrados (visualização pública)| | | *
SDK de MFA| | | *
MFA para aplicativos locais usando o servidor MFA| | | *


##Como obter o Azure Multi-Factor Authentication

O Azure Multi-Factor Authentication vem como parte do Azure Active Directory Premium e o Enterprise Mobility Suite. Se você já os tiver, você também tem o Azure Multi-Factor Authentication.

Se você for usuário do Office 365 ou um assinante do Azure e quiser aproveitar os recursos adicionais fornecidos pelo Azure Multi-Factor Authentication, continue lendo.

Se você não tiver nenhum deles,para começar a usar o Azure Multi-Factor Authentication, você primeiro precisa de uma assinatura do Azure ou uma [Assinatura de avaliação do Azure](http://azure.microsoft.com/pricing/free-trial/).

Quando usar o Azure multi-Factor Authentication, há dua opções de cobrança disponíveis:

- **Por usuário**. Geralmente para empresas que desejam habilitar a autenticação multifator para um número fixo de funcionários que precisam regularmente de autenticação.
- **Por autenticação**. Em geral, para empresas que querem habilitar a autenticação multifator para um grupo grande de usuários externos que raramente precisam de autenticação.

Para obter detalhes sobre preços, consulte [Preços do Azure MFA.](http://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Escolha o modelo que funciona melhor para sua organização. Depois, para iniciar, consulte [Introdução](multi-factor-authentication-get-started.md)



 

<!---HONumber=July15_HO2-->