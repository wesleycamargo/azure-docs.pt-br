---
title: "Modelos de Aplicativo Lógico | Microsoft Docs"
description: "Saiba como usar modelos de Aplicativo lógico pré-criados a fim de obter ajuda para começar"
author: kevinlam1
manager: dwrede
editor: 
services: app-service\logic
documentationcenter: 
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: app-service-logic
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4e38ed7cb28b22679081868fc68852c134517990


---
# <a name="logic-app-templates"></a>Modelos de Aplicativo Lógico
## <a name="what-are-logic-app-templates"></a>O que são modelos de aplicativo lógico
Um modelo de aplicativo lógico é um aplicativo lógico predefinido que você pode usar para começar a criar rapidamente seu próprio fluxo de trabalho. 

Esses modelos são uma boa maneira de descobrir vários padrões que podem ser criados usando aplicativos lógicos. Você pode usá-los como estão ou modificá-los para ajustá-los a seu cenário.

## <a name="overview-of-available-templates"></a>Visão geral dos modelos disponíveis
Existem vários modelos disponíveis atualmente publicados na plataforma de aplicativos lógicos. Algumas categorias de exemplo, bem como os tipos de conectores usados por elas, são listadas abaixo.

### <a name="enterprise-cloud-templates"></a>Modelos de nuvem empresarial
Os modelos que integram o Dynamics CRM, o Salesforce, o Box, o Blob do Azure e outros conectores para suas necessidades de nuvem empresarial. Alguns exemplos do que pode ser feito com esses modelos incluem a organização dos clientes potenciais e fazer backup de dados de arquivos corporativos.

### <a name="enterprise-integration-pack-templates"></a>Modelos de pacote de integração empresarial
Configurações de pipelines VETER (validar, extrair, transformar, enriquecer, rotear), recebendo um documento EDI X12 via AS2 e transformando-o em XML, bem como o tratamento de mensagens X12 e AS2.

### <a name="protocol-pattern-templates"></a>Modelos de padrão de protocolo
Esses modelos consistem em aplicativos lógicos que contêm os padrões de protocolo como solicitação-resposta sobre HTTP, bem como integrações por meio de FTP e SFTP. Use-os como estão ou como base para a criação de padrões mais complexos de protocolo.  

### <a name="personal-productivity-templates"></a>Modelos de produtividade pessoal
Os padrões para ajudar a melhorar a produtividade pessoal incluem modelos que definem lembretes diários, transformam os itens de trabalho importantes em listas de tarefas e automatizam tarefas demoradas até uma etapa de aprovação de usuário único.

### <a name="consumer-cloud-templates"></a>Modelos de nuvem do consumidor
Os modelos simples que se integram com os serviços de mídia social, como o Twitter, o Slack e o email, basicamente capaz de reforçar as iniciativas de marketing de mídia social. Eles também incluem modelos como a cópia em nuvem, que podem ajudar a aumentar a produtividade, economizando o tempo gasto em tarefas tradicionalmente repetitivas. 

## <a name="how-to-create-a-logic-app-using-a-template"></a>Como criar um aplicativo lógico usando um modelo
Para começar a usar um modelo de aplicativo lógico, vá para o designer de aplicativos lógicos. Se você estiver inserindo o designer ao abrir um aplicativo lógico existente, o aplicativo lógico será carregado automaticamente no modo de exibição de designer. Porém, se você estiver criando um novo aplicativo lógico, verá a tela abaixo.  
 ![](../../includes/media/app-service-logic-templates/template7.png)  

Nesta tela, você pode optar por começar com um aplicativo lógico em branco ou com um modelo criado previamente. Se você selecionar um dos modelos, serão fornecidas informações adicionais. Neste exemplo, usamos o modelo *Quando um novo arquivo for criado no Dropbox, copie-o para o OneDrive* .  
 ![](../../includes/media/app-service-logic-templates/template2.png)  

Se você optar por usar o modelo, basta selecionar o botão *usar este modelo* . Você precisará entrar em suas contas com base em quais conectores são utilizados pelo modelo. Ou, se você tiver estabelecido uma conexão com esses conectores anteriormente, poderá selecionar continuar conforme mostrado abaixo.  
 ![](../../includes/media/app-service-logic-templates/template3.png)  

Depois de estabelecer a conexão e selecionar *continuar*, o aplicativo lógico é aberto no modo de exibição de designer.  
 ![](../../includes/media/app-service-logic-templates/template4.png)  

No exemplo acima, como é o caso com muitos modelos, alguns dos campos de propriedade obrigatória podem ser preenchido em conectores; no entanto, alguns ainda podem exigir um valor antes de poder implantar corretamente o aplicativo lógico. Se você tentar implantar sem inserir alguns dos campos ausentes, será notificado com uma mensagem de erro.

Se você quiser voltar ao visualizador de modelos, selecione o botão *Modelos* na barra de navegação superior. Alternando para o visualizador do modelo, você perderá qualquer progresso não salvo. Antes de alternar novamente para o visualizador de modelos, você verá uma mensagem de aviso informando isso.  
 ![](../../includes/media/app-service-logic-templates/template5.png)  

## <a name="how-to-deploy-a-logic-app-created-from-a-template"></a>Como implantar um aplicativo lógico criado de um modelo
Depois de carregar o modelo e de fazer as alterações desejadas, selecione o botão salvar no canto superior esquerdo. Isso salva e publica seu aplicativo lógico.  
 ![](../../includes/media/app-service-logic-templates/template6.png)  

Se você quiser mais informações sobre como adicionar mais etapas a um modelo de aplicativo lógico existente ou fazer edições em geral, leia mais em [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md).




<!--HONumber=Nov16_HO3-->


