<properties
	pageTitle="Preview do Azure Active Directory B2C: Integridade do serviço | Microsoft Azure"
	description="Notificações sobre problemas secundários, status e mitigações do Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="swkrish"/>

# Preview do Azure Active Directory B2C: Integridade do serviço

Esta página fornece notificações sobre problemas de serviço secundários, status e mitigações. É possível marcar essa página como favorito para referência futura. Continue monitorando o [Painel de status do Azure](https://azure.microsoft.com/status/) também.

### 22/3/2016: bug do SSO em locatários B2C

Um bug do SSO (Logon Único) foi introduzido às 13h00 (Horário Padrão do Pacífico, PST) em 17/3/2016 e foi atenuado às 10h00 (PST) em 18/3/2016. Durante esse período, menos de 100 clientes foram afetados. Continuamos monitorando a situação de perto. Condições para que o consumidor tenha percebido esse bug:

1. Você configurou uma política de entrada com “contas locais” como o único provedor de identidade configurado nela.
2. Um consumidor entra no Azure AD B2C com êxito na primeira vez.
3. O consumidor tenta entrar novamente, mas em vez de obter o SSO, ele ou ela recebe uma mensagem de erro.

A única solução alternativa para o consumidor (após a Etapa 3) foi fechar e reabrir o navegador e autenticar novamente.

<!---HONumber=AcomDC_0323_2016-->