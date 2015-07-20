<properties 
	pageTitle="Perguntas frequentes sobre o Azure Multi-Factor Authentication" 
	description="O Azure Multi-Factor Authentication é um método de verificar quem você é e que requer o uso de mais do que apenas um nome de usuário e uma senha. Ela fornece uma camada adicional de segurança para logons de usuário e transações." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadwha" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Perguntas frequentes sobre o Azure Multi-Factor Authentication

Aqui você encontrará perguntas que são feitas frequentemente sobre o Azure Multi-Factor Authentication. Elas podem incluir dicas ou informações para solução de problemas. Use o menu de navegação à direita para localizar rapidamente sua pergunta.

## Como posso obter ajuda com o Azure Multi-Factor Authentication?
[Pesquise a Base de Dados de Conhecimento (KB) Microsoft](http://search.microsoft.com/supportresults.aspx?form=mssupport&q=phonefactor)

- Pesquise a Base de Dados de Conhecimento (KB) Microsoft em busca de soluções técnicas para problemas comuns de conserto sobre Suporte para o Servidor Microsoft Azure Multi-Factor Authentication (Phone Factor).

[Fóruns do Active Directory do Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

- Você pode pesquisar e procurar perguntas e respostas técnicas na comunidade ou fazer sua própria pergunta clicando [aqui](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Redefinição de senha](mailto:phonefactorsupport@microsoft.com)

- Em todas as consultas sobre redefinição de senha ou em busca de ajuda para redefinir a senha, envie um email para [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com).

[Atendimento ao cliente para o Servidor Microsoft Azure Multi-Factor Authentication (Phone Factor)](https://support.microsoft.com/oas/default.aspx?prid=14947)

- Use este link para contatar um profissional do suporte da Microsoft. Faremos a você algumas perguntas que nos ajudarão a determinar quais são suas opções de suporte. As opções de suporte podem incluir email, envio online ou suporte por telefone. 

[Consultas gerais sobre o Servidor Microsoft Azure Multi-Factor Authentication (Phone Factor)](http://azure.microsoft.com/services/multi-factor-authentication)

- Para saber mais sobre o Servidor Microsoft Azure Multi-Factor Authentication (Phone Factor) ou se você tem perguntas sobre como comprar o produto e as diferentes opções de suporte disponíveis, visite ou envie um email para [pfsales@microsoft.com](mailto:pfsales@microsoft.com). 


## O que devo fazer quando vejo o erro “A solicitação de autenticação não é para uma conta ativada” quando autentico usando as notificações do aplicativo móvel?

- Acesse [https://account.activedirectory.windowsazure.com/profile/](https://account.activedirectory.windowsazure.com/profile/) e entre com sua conta organizacional.
- Se necessário, clique em Outras opções de verificação e selecione outra opção para concluir a verificação da conta.
- Clique em Verificação de Segurança Adicional.
- Remova a conta existente do seu aplicativo móvel.
- Clique em Configurar e siga as instruções para reconfigurar o aplicativo móvel.


## O que devo fazer se eu não receber uma resposta no meu telefone ou se esquecer do meu telefone?

Se, anteriormente, você configurou um telefone de backup, tente novamente selecionando esse telefone quando solicitado na página de entrada. Se você não tiver outro método configurado, contate seu administrador e peça a ele para atualizar o número atribuído ao seu telefone principal – celular ou comercial.

## O que devo fazer quando vejo o erro 0x800434D4L ao tentar me conectar usando um aplicativo que não usa navegador?
Atualmente, a verificação de segurança adicional pode ser usada somente com aplicativos/serviços que você pode acessar pelo navegador. Os aplicativos que não usam navegador (também conhecidos como aplicativos cliente avançados) que são instalados no computador local, como o Windows PowerShell, não funcionarão com contas que exigem a verificação de segurança adicional. Nesse caso, o aplicativo pode gerar o erro 0x800434D4L.

Uma solução alternativa para isso é ter uma conta de usuário para as operações administrativas e outra para as operações não administrativas. Posteriormente, você pode vincular caixas de correio entre a conta administrativa e a conta não administrativa para que seja possível entrar no Outlook usando a conta não administrativa. Para obter mais detalhes sobre isso, consulte [Fornecer a um administrador a capacidade de abrir e exibir o conteúdo da caixa de correio de um usuário] (http://help.outlook.com/141/gg709759(d=loband).aspx?sl=1).




## Removi um usuário de uma função administrativa, mas esqueci de desabilitar a autenticação multifator. Agora, ela não está aparecendo na lista. Como removo esse recurso?

- De acordo com o portal você está usando, no painel esquerdo, clique em Usuários ou Usuários e Grupos.
- Marque a caixa de seleção ao lado do usuário que deseja editar e clique em Editar ou no ícone Editar.
- Clique em Configurações, em Atribuir função, selecione Sim e adicione o usuário de volta à função administrativa anterior.
- Acesse a página da autenticação multifator. Agora, a conta deve ser mostrada na lista exibida na página. 
- Siga as etapas acima para desabilitar a autenticação multifator de uma conta. 
- Nesse momento, você pode remover a conta da função administrativa.


## O que fazer se um usuário contatar você, o administrador, com uma conta que foi bloqueada
Você pode redefinir o usuário, forçando-o a passar pelo processo de registro novamente. Para fazer isso, consulte [Gerenciando as configurações de dispositivo e usuário com o Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md)

## O que fazer se um usuário tiver perdido ou teve um dispositivo roubado que está usando senhas de aplicativo
Você pode excluir todas as senhas de aplicativo dos usuários para impedir qualquer acesso não autorizado. Assim que tiver um dispositivo de substituição, o usuário poderá recriá-las. Para fazer isso, consulte [Gerenciando as configurações de dispositivo e usuário com o Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md)

## E se o usuário não conseguir entrar nos aplicativos que não usam navegador?

- Os usuários habilitados para autenticação multifator exigirão a senha de aplicativo para entrar em alguns aplicativos que não usam navegador.
- Os usuários precisarão limpar as informações de entrada (excluir as informações para conexão), reiniciar o aplicativo e entrar com os respectivos nome de usuário e senha de aplicativo. 

Para obter informações sobre como criar senhas de aplicativo, consulte [Ajuda com senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md)


>[AZURE.NOTE]Autenticação moderna para os clientes do Office 2013
>
> Os clientes do Office 2013 (incluindo o Outlook) agora oferecem suporte a novos protocolos de autenticação e podem ser habilitados para oferecer suporte ao Multi-Factor Authentication. Isso significa que uma vez habilitado, as senhas de aplicativo não são necessárias para usar os clientes do Office 2013. Para obter mais informações, consulte [Anúncio do preview público da autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

## O que devo fazer se eu não receber uma mensagem de texto ou responder a uma mensagem de texto bidirecional, mas o tempo limite da verificação se esgotar?
O serviço Azure Multi-Factor Authentication envia mensagens de texto usando agregadores de SMS. Muitos fatores podem afetar a confiabilidade da entrega e do recebimento da mensagem de texto, incluindo o agregador usado, o país de destino, a operadora do celular e a intensidade do sinal. Portanto, a entrega de mensagens de texto e o recebimento de respostas SMS ao executar o SMS bidirecional não são garantidos. É recomendável usar SMS unidirecional em vez de SMS bidirecional sempre que possível, pois além de ser mais confiável, evita que os usuários incorram em cobranças globais de SMS geradas por responder a uma mensagem de texto que foi enviada de outro país.

As verificações de mensagem de texto também são mais confiáveis em alguns países, como os Estados Unidos e o Canadá. Os usuários que enfrentam dificuldades para receber mensagens de texto de modo confiável ao usar o Azure Multi-Factor Authentication são estimulados a selecionar os métodos de ligação ou aplicativo móvel. O aplicativo móvel é ótimo porque as notificações de aplicativo móvel podem ser recebidas por conexões celulares e Wi-Fi, e o código de acesso do aplicativo móvel é exibido mesmo quando o dispositivo não tem nenhum sinal. O aplicativo Azure Authenticator está disponível para [Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).


## Minha organização será cobrada por ligações ou mensagens de texto usadas para autenticar meus usuários?
Todos os custos são distribuídos no custo por usuário ou por autenticação do serviço. As organizações não são cobradas pelas ligações individuais feitas ou mensagens de texto enviadas ao usuários finais ao usar o Azure Multi-Factor Authentication. Os proprietários do telefone podem incorrer em custos relacionados ao roaming ou outros custos das respectivas operadoras para receber as ligações ou as mensagens de texto.

## Como a organização é cobrada pelo Multi-Factor Authentication?
O modelo de cobrança/uso ‘por usuário’ ou ‘por autenticação’ é escolhido durante a criação de um Provedor do Multi-Factor Auth no Portal de Gerenciamento do Azure. É um recurso baseado em consumo que é cobrado de acordo com a assinatura do Azure escolhida pela organização, assim como máquinas virtuais, sites, etc., são cobrados com base na assinatura.

## O modelo de cobrança ‘por usuário’ se baseia no número de usuários habilitados para o Multi-Factor Authentication ou no número de usuários que executam as verificações?
A cobrança é baseada no número de usuários habilitados para o Multi-Factor Authentication.

<!---HONumber=July15_HO2-->