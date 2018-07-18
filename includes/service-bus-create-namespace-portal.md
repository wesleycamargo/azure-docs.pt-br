---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/03/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: a110998505ed49c36e1ec722b1dfbf0969def060
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449551"
---
Para começar a usar as entidades de mensagens do Barramento de Serviço no Azure, primeiro é necessário criar um namespace com um nome exclusivo no Azure. Um namespace fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo.

Para criar um namespace:

1. Entre no [Portal do Azure][Azure portal].
2. No painel de navegação esquerdo do portal, clique em **+ Criar um recurso**, depois em **Integração** e em **Barramento de Serviço**.
3. Na caixa de diálogo **Criar um namespace** , digite um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.
4. Depois de verificar se o nome do namespace está disponível, escolha o tipo de preço (Básico, Standard ou Premium). Se você quiser usar [tópicos e assinaturas](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), certifique-se de escolher Standard ou Premium. Não há suporte para os tópicos/assinaturas no tipo de preço básico.
5. No campo **Assinatura** , escolha uma assinatura do Azure na qual criar o namespace.
6. No campo **Grupo de Recursos** , escolha um grupo de recursos existente no qual o namespace residirá, ou então crie um novo.      
7. Em **Localização**, escolha o país ou região no qual o namespace deve ser hospedado.
   
    ![Criar um namespace][create-namespace]
8. Clique em **Criar**. Agora, o sistema cria o seu namespace e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

### <a name="obtain-the-management-credentials"></a>Obter as credenciais de gerenciamento

A criação de um novo namespace gera automaticamente uma regra de SAS (assinatura de acesso compartilhado) inicial com um par de chaves primárias e secundárias associado que concede, cada um, controle total sobre todos os aspectos do namespace. Consulte [Autenticação e autorização do Barramento de Serviço](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) para obter mais informações sobre como criar regras adicionais com direitos mais restritos para remetentes e destinatários regulares. Para copiar a regra inicial, siga estas etapas: 

1. Clique em **Todos os recursos** e depois clique no nome do namespace recém-criado.
2. Na janela namespace, clique em **Políticas de acesso compartilhado**.
3. Na tela **Políticas de acesso compartilhado**, clique em **RootManageSharedAccessKey**.
   
    ![informações de conexão][connection-info]
4. Na janela **Política: RootManageSharedAccessKey**, clique no botão copiar ao lado da **Chave primária da cadeia de conexão** para copiar a cadeia de conexão na área de transferência para uso posterior. Cole esse valor no Bloco de notas ou em outro local temporário.
   
    ![connection-string][connection-string]

5. Repita a etapa anterior, copiando e colando o valor de **chave primária** para um local temporário para uso posterior.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
