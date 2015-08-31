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

Neste momento, somente o relatório de Atividade de Entrada Anômala e os usuários com o relatório de Atividade de Entrada Anômala estão usando o sistema de notificação por email.

## O que faz com que a notificação por email seja enviada?

Por padrão, o Active Directory do Azure é configurado para enviar notificações por email automaticamente para todos os administradores globais. O email é enviado sob as condições a seguir, para cada relatório.

Para o relatório de Atividade de Entrada Anômala:

- Fontes desconhecidas: 10 eventos
- Várias falhas: 10 eventos
- Endereços IP com atividade suspeita: 10 eventos
- Dispositivos infectados: 10 eventos

Para os usuários com relatório de Atividade de Entrada Anômala:

- Fontes desconhecidas: 10 eventos
- Várias falhas: 10 eventos
- Endereços IP com atividade suspeita: 10 eventos
- Dispositivos infectados: 5 eventos
- Relatório de entradas anômalas: 15 eventos

O email é enviado se qualquer uma das condições anteriores for atendida dentro de 30 dias, ou desde o envio do último email se for um período inferior a 30 dias.

As Entradas Anômalas são aquelas identificadas como "anômalas" por nossos algoritmos de Aprendizado de Máquina, com base em locais de entrada inesperados, hora do dia e locais de entrada inesperados ou uma combinação desses elementos. Isso pode indicar que um hacker tentou entrar usando essa conta. Mais informações sobre esse relatório podem ser encontradas na tabela acima.

## Quem recebe as notificações por email?

O email é enviado para todos os administradores globais as quais foi atribuída uma licença do Active Directory Premium. Para garantir que ele seja entregue, podemos enviá-lo também ao endereço de Email alternativo dos administradores. Os administradores devem incluir aad-alerts-noreply@mail.windowsazure.com em suas listas de remetentes seguros, para que eles não deixem de ver o email.

## Com que frequência esses emails são enviados?

Depois que um email é enviado, o outro será enviado somente quando forem encontrados 10 ou mais novos eventos de entrada anômala dentro de 30 dias a partir do envio de email. Como acessar o relatório mencionado no email?

Quando você clica no link, será redirecionado à página do relatório no Portal de Gerenciamento do Azure. Para acessar o relatório, você precisa ser ambos:

- Um administrador ou co-administrador de sua assinatura do Azure
- Um administrador global no diretório e ter uma licença do Active Directory Premium atribuída a você. Para obter mais informações, consulte Edições do Active Directory do Azure.

## Posso desativar esses emails?

Sim, para desativar as notificações relacionadas a entradas anômalas no Portal de Gerenciamento do Azure, clique em **Configurar**, e, em seguida, selecione **Desabilitado** sob a seção **Notificações**.

## O que vem a seguir
- Curioso sobre que relatórios de segurança, auditoria e atividade estão disponíveis? Verifique [Relatórios de segurança, auditoria e atividade do AD do Azure](active-directory-view-access-usage-reports.md)
- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)

<!---HONumber=August15_HO8-->