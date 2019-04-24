---
title: Etapas de publicação de aplicativo | Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 0fc82229e158ed35b97203b11d08841c683c45ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398004"
---
<a name="app-publishing-steps"></a>Etapas de publicação de aplicativo
====================

Para iniciar o processo de publicação, você clicará em "Publicar" na guia Editor.

![Botão Publicar Aplicativo CPP](./media/d365-financials/image014.jpg)


Na guia Status, você verá as etapas de Publicação indicando onde sua oferta está no processo de publicação. Em qualquer ponto no processo de publicação, você também pode entrar e clicar na guia Todas as Ofertas para exibir o status mais recente de qualquer uma das suas ofertas. Clique diretamente no status da oferta e veja os detalhes de onde sua oferta está no processo de publicação.

Vamos percorrer cada uma das etapas de publicação e discutir o que acontece em cada etapa e quanto tempo cada etapa levará.

![Diagrama do processo de publicação](./media/d365-financials/image017.png)


**Validar os pré-requisitos**

Quando você clicar em "Publicar", uma verificação automática ocorrerá para garantir que todos os campos obrigatórios estejam preenchidos em sua oferta. Se algum campo não estiver preenchido, um aviso será exibido ao lado do campo e você precisará preenchê-lo com precisão. Depois, clique em "Publicar" novamente.

Após a conclusão correta dessa etapa, um pop-up será exibido solicitando um endereço de email, que será usado para enviar notificações de publicação. Depois de enviar seu endereço de email, esta etapa será concluída.


**Validação automatizada de aplicativo**

Nesta etapa, nosso serviço de certificação automatizado verifica se o conteúdo das extensões de aplicativo fornecidas com uma oferta se alinham com os metadados da oferta. Sempre verifique se o nome, a versão, o editor e a ID do aplicativo correspondem aos fornecidos no manifesto de extensão chamado `app.json`.


**Validação de test drive**

Se você tiver optado por configurar o test drive, esse estágio será aquele em que as configurações do test drive são validadas.


**Validação do gerenciamento e do registro de clientes potenciais**

Durante esse estágio, se você tiver configurado o recurso de geração de cliente potencial, nós validaremos se a integração de CRM está funcionando enviando um cliente potencial de teste ao seu CRM. Você verá um registro com dados falsos preenchidos no CRM ou na Tabela do Azure após a conclusão dessa etapa. Toda a documentação para Geração de Cliente Potencial está localizada aqui.


**Empacotamento do AppSource**

Os artefatos de detalhes da vitrine estão sendo verificados e o pacote de versão prévia do AppSource está sendo gerado.


**Saída do editor**

Durante esse estágio, o botão **Go Live** ficará ativo. Também haverá um link para visualizar sua oferta (com seu hidekey). Quando estiver satisfeito com a aparência de sua visualização, clique no botão Go Live.
Tenha em mente que esta solicitação não torna seu aplicativo ativo no App Source, mas dispara nosso processo interno de validação.


**Validação de marketing e técnica do aplicativo**

Esta etapa é aquela em que podemos realizar as validações de marketing e técnicas em paralelo. Confira os documentos de diretrizes [Lista de verificação para enviar seu aplicativo](https://aka.ms/CheckBeforeYouSubmit) e [white paper Developing Apps for Dynamics 365 for Finance and Operations](https://go.microsoft.com/fwlink/?linkid=841518) (Desenvolvendo aplicativos para o Dynamics 365 for Finance and Operations) para saber quais são os requisitos obrigatórios e as recomendações. Durante o processo de validação, vamos:
-  trabalhar com você para resolver problemas e questões pendentes.  
- fornecer uma data de publicação do aplicativo e notificá-lo quando seu aplicativo é publicado. 
- fornecer a você comentários iniciais sobre a validação de marketing e técnica em até cinco a sete dias úteis.

Normalmente, estas etapas podem levar mais de uma semana, e não é necessário que você permaneça conectado continuamente no Portal do Cloud Partner.


**Publicar o aplicativo no serviço**

Sua oferta passará por um processamento final. Seu aplicativo passou na validação de marketing e técnica, mas agora precisa passar por um processamento final para ficar pronto para o App Source.


**Ativa**

Sua oferta está online agora no AppSource, e os clientes poderão exibir e implantar o aplicativo em suas assinaturas do Microsoft Dynamics 365 Business Central. Você receberá um email nosso, informando que seu aplicativo ficou público no App Source. A qualquer momento, clique na guia Todas as ofertas e veja o status de todas as suas ofertas listadas na coluna à direita. Você pode clicar no status para ver em detalhes o status do fluxo de publicação de sua oferta.


<a name="error-handling"></a>Tratamento de erros
--------------

Durante o processo de publicação, você pode encontrar um erro. Se isso acontecer, você receberá um email de notificação informando que ocorreu um erro e com instruções sobre as próximas etapas. Você também pode ver os erros a qualquer momento durante esse processo clicando na guia Status. Você verá em que ponto do processo o erro ocorreu junto com uma mensagem de erro descrevendo o que precisa ser resolvido.

Se você encontrar erros durante o processo de publicação, será necessário corrigi-los e, depois, clicar em **Publicar** para reiniciar o processo. Você precisa começar no início das etapas de publicação em **Validar pré-requisitos** ao republicar após a correção de algum erro.

Se você estiver enfrentando problemas para resolver um erro, abra uma solicitação de suporte para obter ajuda de nossos engenheiros de suporte.


<a name="canceling-the-publishing-request"></a>Cancelar a solicitação de publicação
--------------------------------

Você pode iniciar o processo de publicação e ter que cancelar a solicitação. Você só pode cancelar uma solicitação de publicação depois que a solicitação de publicação atingir a etapa de Aprovação do Publicador. Para cancelar, clique em **Cancelar Publicação**. O status da publicação voltará à Etapa 1 e, para publicar novamente, você deverá clicar em Publicar e executar as etapas no status.

![Botão Cancelar publicação](./media/d365-financials/image013.png)
