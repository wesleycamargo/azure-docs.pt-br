<properties 
	pageTitle="Perguntas frequentes sobre Gerenciamento de API do Azure | Microsoft Azure" 
	description="Conheça as respostas a perguntas comuns, padrões e práticas recomendadas do Gerenciamento de API do Azure" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/12/2016" 
	ms.author="sdanie"/>

# Perguntas frequentes sobre Gerenciamento de API do Azure

Conheça as respostas a perguntas comuns, padrões e práticas recomendadas do Gerenciamento de API do Azure

## Perguntas frequentes

-	[Como fazer uma pergunta à equipe de Gerenciamento de API?](#how-can-i-ask-a-question-to-the-api-management-team)
-	[Se um recurso está em Visualização, o que isso significa?](#what-does-it-mean-if-a-feature-is-in-preview)
-	[Quais são as opções com suporte para proteger a conexão entre o gateway de Gerenciamento de API e meus serviços de back-end?](#what-are-the-supported-options-to-secure-the-connection-between-the-api-management-gateway-and-my-backend-services)
-	[Como copiar uma instância de Gerenciamento de API para uma nova instância?](#how-can-i-copy-an-api-management-instance-to-a-new-instance)
-	[Posso gerenciar minha instância de Gerenciamento de API por meio de programação?](#can-i-manage-my-api-management-instance-programmatically)
-	[Como adicionar um usuário ao grupo Administradores?](#how-can-i-add-a-user-to-the-administrators-group)
-	[Por que a política que desejo adicionar não está habilitada no editor de política?](#why-is-the-policy-that-i-want-to-add-not-enabled-in-the-policy-editor)

### Como fazer uma pergunta à equipe de Gerenciamento de API?

-	Você pode postar suas perguntas em nosso [Fórum do MSDN de Gerenciamento de API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-	Você também pode nos enviar um email no endereço: `apimgmt@microsoft.com`.

### Se um recurso está em Visualização, o que isso significa?

Um recurso em visualização está funcionalmente completo, mas está em visualização porque estamos buscando ativamente comentários sobre esse recurso. É possível que venhamos a fazer uma grande mudança em resposta aos comentários dos clientes. Portanto, recomendamos que não dependa do recurso para uso em ambientes de produção. Se você tiver comentários sobre os recursos em visualização, informe-nos usando um dos mecanismos descritos em [Como fazer uma pergunta à equipe de Gerenciamento de API?](#how-can-i-ask-a-question-to-the-api-management-team).

### Quais são as opções com suporte para proteger a conexão entre o gateway de Gerenciamento de API e meus serviços de back-end?

Há várias opções diferentes com suporte.

1. Use a autenticação básica HTTP. Para obter mais informações, confira [Definir configurações de API](api-management-howto-create-apis.md#configure-api-settings).
2. Use a autenticação mútua de SSL, conforme descrito em [Saiba como garantir serviços de back-end usando autenticação de certificado do cliente no Gerenciamento de API do Azure.](api-management-howto-mutual-certificates.md).
3. Use a lista branca de IPs em seu serviço de back-end. Se você tiver uma instância de Gerenciamento de API de camada Standard ou Premium, o endereço IP do gateway permanecerá constante e você poderá configurar sua lista branca para permitir esse endereço IP. Você pode recuperar o endereço IP de sua instância de Gerenciamento de API no **Painel** no Portal Clássico do Azure.
4. Você pode conectar sua instância de Gerenciamento de API a uma Rede Virtual do Azure (clássico). Para saber mais, confira [Como configurar conexões VPN no Gerenciamento de API do Azure](api-management-howto-setup-vpn.md).

### Como copiar uma instância de Gerenciamento de API para uma nova instância?

Há várias opções diferentes que você pode usar para copiar uma instância de serviço de Gerenciamento de API para uma nova instância.

-	Use o recurso de backup e restauração do Gerenciamento de API. Para saber mais, confira [Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure](api-management-howto-disaster-recovery-backup-restore.md).
-	Crie seu próprio recurso de backup e restauração usando a [API REST do Gerenciamento de API](https://msdn.microsoft.com/library/azure/dn776326.aspx) para salvar e restaurar as entidades desejadas de sua instância de serviço.
-	Baixe a configuração de serviço usando o Git e carregue-a novamente em uma nova instância. Para saber mais, confira [Saiba como salvar e definir a configuração de seu serviço de Gerenciamento de API usando o Git](api-management-configuration-repository-git.md).

### Posso gerenciar minha instância de Gerenciamento de API por meio de programação?

Sim, você pode gerenciá-la usando a [API REST do Gerenciamento de API](https://msdn.microsoft.com/library/azure/dn776326.aspx) e cmdlets do PowerShell de [Implantação de serviço](https://msdn.microsoft.com/library/mt619282.aspx) e [Gerenciamento de serviço](https://msdn.microsoft.com/library/mt613507.aspx).

### Como adicionar um usuário ao grupo Administradores?

No momento, os administradores são limitados a usuários que fazem logon no Portal Clássico do Azure como administradores ou coadministradores na assinatura do Azure que contém a instância de Gerenciamento de API. Os usuários criados no portal do editor não podem ser designados como administradores nem adicionados ao grupo Administradores.


### Por que a política que desejo adicionar não está habilitada no editor de política?

Se a política que você quer adicionar não estiver habilitada, verifique se você está no escopo correto para essa política. Cada declaração de política é projetada para uso em determinados escopos e seções de política. Para examinar as seções da política e os escopos de uma política, confira a seção **Uso** da política na [Referência à Política](https://msdn.microsoft.com/library/azure/dn894080.aspx).

<!---HONumber=AcomDC_0413_2016-->