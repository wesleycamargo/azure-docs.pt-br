<properties
	pageTitle="Notificações de Relatórios do Active Directory do Azure"
	description="Como usar as notificações de relatórios do Active Directory do Azure para entradas suspeitas."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Notificações de Relatórios do Active Directory do Azure

## Quais relatórios geram notificações por email

Neste momento, apenas o relatório de atividade de entrada irregular dispara as notificações por email.

## O que é uma "Entrada irregular"?

Entradas irregulares são aquelas que foram identificadas por nossos algoritmos de aprendizado de máquina, com base em uma condição de "viagem impossível" combinada a um dispositivo e um local de entrada anômalas. Isso pode indicar que um hacker tentou entrar usando essa conta.

## Quem recebe as notificações por email?

O email é enviado para todos os administradores globais as quais foi atribuída uma licença do Active Directory Premium. Para garantir que ele seja entregue, podemos enviá-lo também ao endereço de Email alternativo dos administradores. Os administradores devem incluir aad-alerts-noreply@mail.windowsazure.com em suas listas de remetentes seguros, para que eles não deixem de ver o email.

## Com que frequência esses emails são enviados?

O email será enviado se 10 novas atividades de entrada irregular ocorrerem nos últimos 30 dias, ou desde que o último email foi enviado, o que for menor.

## Como acessar o relatório mencionado no email?

Quando você clica no link, será redirecionado à página do relatório no Portal de Gerenciamento do Azure. Para acessar o relatório, você precisa ser ambos:

- Um administrador ou co-administrador de sua assinatura do Azure
- Um administrador global no diretório e ter uma licença do Active Directory Premium atribuída a você. Para obter mais informações, consulte Edições do Active Directory do Azure.

## Posso desativar esses emails?

Sim, para desativar as notificações relacionadas a entradas anômalas no Portal de Gerenciamento do Azure, clique em **Configurar**, e, em seguida, selecione **Desabilitado** sob a seção **Notificações**.

## O que vem a seguir
- Curioso sobre que relatórios de segurança, auditoria e atividade estão disponíveis? Verifique [Relatórios de segurança, auditoria e atividade do AD do Azure](active-directory-view-access-usage-reports.md)
- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)

<!---HONumber=September15_HO1-->