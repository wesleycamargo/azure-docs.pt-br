---
title: "Criar e registrar conectores SOAP – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Configurar conectores SOAP para uso em Aplicativos Lógicos do Azure"
author: divyaswarnkar
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 0323b0f7ee03dce209d5a71c6711988a34ba7633
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Criar e registrar conectores SOAP nos Aplicativos Lógicos do Azure

Para integrar os serviços SOAP em seus fluxos de trabalho do aplicativo lógico, você pode criar e registrar um conector personalizado do SOAP (protocolo SOAP) por meio da WSDL (linguagem WSDL) que descreve seu serviço SOAP. Os conectores SOAP funcionam como conectores pré-compilados. Portanto, você pode usá-los da mesma maneira que faz com outros conectores em seus aplicativos lógicos.


## <a name="prerequisites"></a>Pré-requisitos

Para registrar seu conector SOAP, você precisará destes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá iniciar com uma [conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, inscreva-se para uma [assinatura de Pagamento conforme o uso](https://azure.microsoft.com/pricing/purchase-options/).

* Qualquer item aqui:
  * Uma URL para um WSDL que define seu serviço SOAP e as APIs
  * Um arquivo WSDL que define seu serviço SOAP e as APIs

  Para este tutorial, você pode usar nosso exemplo de [serviço SOAP de pedidos](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl).

* Opcional: Uma imagem para usar como um ícone para o conector personalizado


## <a name="1-create-your-connector"></a>1. Criar seu conector

1. No portal do Azure, no menu principal do Azure, escolha **Novo**. Na caixa de pesquisa, digite "conector de aplicativos lógicos" como seu filtro e pressione Enter.

   ![Novo, pesquisar por "conector de aplicativos lógicos"](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. Na lista de resultados, escolha **Conector de Aplicativos Lógicos** > **Criar**.

   ![Criar conector de Aplicativos Lógicos](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. Forneça detalhes para registrar seu conector conforme descrito na tabela. Quando tiver concluído, escolha **Fixar no painel** > **Criar**.

   ![Detalhes do conector personalizado de Aplicativo Lógico](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | Propriedade | Valor sugerido | Descrição | 
   | -------- | --------------- | ----------- | 
   | **Nome** | *soap-connector-name* | Forneça um nome para seu conector. | 
   | **Assinatura** | *Azure-subscription-name* | Selecione sua assinatura do Azure. | 
   | **Grupo de recursos** | *Azure-resource-group-name* | Crie ou selecione um grupo do Azure para organizar seus recursos do Azure. | 
   | **Localidade** | *deployment-region* | Selecione uma região de implantação para o conector. | 
   |||| 

   Depois que o Azure implanta seu conector, o menu do conector dos aplicativos lógicos é aberto automaticamente. 
   Caso contrário, escolha seu conector SOAP no painel do Azure.

## <a name="2-define-your-connector"></a>2. Definir seu conector

Agora, especifique o arquivo WSDL ou a URL para criar seu conector, a autenticação que o conector usa e as ações e gatilhos que seu conector SOAP fornece


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. Especifique o arquivo WSDL ou a URL para o conector

1. No menu do conector, se ainda não estiver selecionado, escolha **Conector de Aplicativos Lógicos**. Na barra de ferramentas, escolha **Editar**.

   ![Editar conector personalizado](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. Escolha **Geral** para que você possa fornecer os detalhes nessas tabelas para criar, proteger e definir as ações e gatilhos para o conector SOAP.

   1. Nos **Conectores personalizados**, selecione **SOAP** para o seu **Ponto de extremidade da API**, de modo que você possa fornecer o arquivo WSDL que descreve sua API.

      ![Forneça o arquivo WSDL para sua API](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | Opção | Formatar |Descrição | 
      | ------ | ------ | ----------- | 
      | **Carregar o WSDL do arquivo** | *Arquivo WSDL* | Navegue até o local do seu arquivo WSDL e selecione o arquivo. | 
      | **Carregar o WSDL da URL** | http://*path-to-wsdl-file* | Forneça a URL para o arquivo WSDL do serviço. | 
      | **SOAP para REST** |   | Transforme as APIs no serviço SOAP em APIs REST. | 
      |||| 

   2. Para **Informações gerais**, carregue um ícone para o conector. 
   Normalmente, os campos **Descrição**, **Host** e **URL Base** são preenchidos automaticamente do seu arquivo WSDL. 
   Mas, se não forem, adicione essas informações conforme descrito na tabela e escolha **Continuar**. 

      ![Detalhes do conector](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | Opção ou configuração | Formatar | Descrição | 
      | ----------------- | ------ | ----------- | 
      | **Carregar ícone** | *png-or-jpg-file-under-1-MB* | Um ícone que representa seu conector <p>Cor: de preferência um logotipo branco em uma tela de fundo colorida. <p>Dimensões: Um logotipo de ~ 160 pixels dentro de um quadrado 230 pixels | 
      | **Cor da tela de fundo do ícone** | *icon-brand-color-hexadecimal-code* | <p>A cor atrás de seu ícone que corresponde à cor da tela de fundo em seu arquivo de ícone. <p>Formato: hexadecimal. Por exemplo, #007ee5 representa a cor azul. | 
      | **Descrição** | *connector-description* | Forneça uma descrição curta para seu conector. | 
      | **Host** | *connector-host* | Forneça o domínio do host para seu serviço SOAP. | 
      | **URL base** | *connector-base-URL* | Forneça a URL base para seu serviço SOAP. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Descrever a autenticação que o conector usa

1. Agora, escolha **Segurança** para que você possa examinar ou descrever a autenticação que o conector usa. A autenticação assegura que as identidades de seus usuários fluam adequadamente entre seu serviço e todos os clientes.

   Por padrão, o **Tipo de autenticação** do seu conector está definido como **Nenhuma autenticação**.
   
   ![Tipo de autenticação.](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   Para alterar o tipo de autenticação, escolha **Editar**. Você pode selecionar a **autenticação Básica**. Para usar rótulos de parâmetro diferentes dos valores padrão, atualize-os em **Rótulo de parâmetro**.

   ![Autenticação básica](./media/logic-apps-soap-connector-create-register/security.png)

   
2. Para salvar seu conector depois de inserir as informações de segurança, na parte superior da página, escolha **Atualizar conector**, em seguida, escolha **Continuar**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Analisar, atualizar ou definir ações e gatilhos para o conector

1. Agora, escolha **Definição** para que você possa examinar, editar ou definir novas ações e gatilhos que os usuários podem adicionar a seus fluxos de trabalho.

   As ações e os gatilhos são baseados nas operações definidas em seu arquivo WSDL, que preenche automaticamente a página **Definição** e inclui os valores de solicitação e resposta. Portanto, se as operações necessárias já aparecerem aqui, vá para a próxima etapa no processo de registro sem fazer alterações nesta página.

   ![Definição de conector](./media/logic-apps-soap-connector-create-register/definition.png)

2. Como alternativa, se você quiser editar as ações e os gatilhos existentes ou adicionar novos, [continue com estas etapas](logic-apps-custom-connector-register.md#add-action-or-trigger).


## <a name="3-finish-creating-your-connector"></a>3. Concluir a criação do seu conector

Quando estiver pronto, escolha **Atualizar Conector** para que você possa implantar seu conector. 

Parabéns! Agora, quando você cria um aplicativo lógico, é possível localizar o conector no Designer de Aplicativos Lógico e adicionar esse conector ao seu aplicativo lógico.

![No Designer de Aplicativos Lógicos, localize o conector](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Compartilhar seu conector com outros usuários de Aplicativos Lógicos

Conectores personalizados registrados, mas não certificados funcionam como conectores gerenciados pela Microsoft, mas são visíveis e estão disponíveis *somente* para o autor do conector e usuários que têm o mesmo locatário do Azure Active Directory e a assinatura do Azure para os aplicativos lógicos na região em que esses aplicativos são implantados. Embora o compartilhamento seja opcional, pode haver situações em que você deseja compartilhar seus conectores com outros usuários. 

> [!IMPORTANT]
> Se você compartilhar um conector, outras pessoas poderão começar a depender esse conector. 
> ***Excluir seu conector exclui todas as conexões com ele.***
 
Para compartilhar seu conector com usuários externos fora desses limites, por exemplo, com os usuários de Aplicativos Lógicos, [envie seu conector para certificação da Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>Perguntas frequentes

**P:** O conector SOAP está geralmente disponível (GA)? </br>
**R:** O conector SOAP está na **versão prévia** e ainda não é um serviço GA.

**P:** Existem quaisquer restrições e problemas conhecidos para o conector SOAP? </br>
**R:** Sim, consulte os [problemas conhecidos e restrições do conector SOAP](../api-management/api-management-api-import-restrictions.md#wsdl).

**P:** Há algum limite para conectores personalizados? </br>
**R:** Sim, consulte os [limites de conectores personalizados aqui](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Obtenha suporte

* Para obter suporte com o desenvolvimento e a integração ou para solicitar recursos que ainda não estão disponíveis no assistente de registro, entre em contato com [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). A Microsoft monitora essa conta quanto a dúvidas e problemas de desenvolvedor e os direciona para a equipe apropriada.

* Para fazer ou responder perguntas ou saber o que os outros usuários dos Aplicativos Lógicos do Azure estão fazendo, visite o [fórum de Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar os Aplicativos Lógicos, vote ou envie ideias no [site de comentários do usuário dos Aplicativos Lógicos](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Próximas etapas

* [Opcional: certificar seu conector](../logic-apps/custom-connector-submit-certification.md)
* [Perguntas frequentes do conector personalizado](../logic-apps/custom-connector-faq.md)