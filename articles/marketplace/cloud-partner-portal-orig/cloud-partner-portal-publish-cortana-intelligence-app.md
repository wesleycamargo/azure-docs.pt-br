---
title: Cortana Intelligence | Microsoft Docs
description: Publicação de uma oferta do Cortana Intelligence.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
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
ms.openlocfilehash: f12a15a0d739ae6e98be3871fa8bb4104f49565c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805339"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>Publicar uma oferta do Cortana Intelligence usando o Portal do Cloud Partner

Este artigo descreve como publicar uma oferta do Cortana Intelligence usando o Portal do Cloud Partner.

## <a name="prerequisites"></a>Pré-requisitos

O Portal do Cloud Partner dá suporte ao acesso baseado em função ao portal, que permite que os colaboradores colaborarem na publicação de uma oferta. Para obter mais informações, veja [Gerenciar usuários do Cloud Portal](./cloud-partner-portal-manage-users.md).

Antes de publicar uma oferta em nome de uma conta de editor, uma das pessoas com função de \"proprietário\" precisa concordar em cumprir os [Termos de Uso](https://azure.microsoft.com/support/legal/website-terms-of-use/), a [Política de Privacidade da Microsoft](http://www.microsoft.com/privacystatement/default.aspx) e o [Contrato do Programa Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>Para começar a criar uma oferta do Cortana Intelligence

Depois de cumprir todos os pré-requisitos, você está pronto para começar a criar sua oferta do Cortana Intelligence.

1.  Entre no [Portal do Cloud Partner](http://cloudpartner.azure.com/).
2.  Na barra de navegação da esquerda, selecione **+ Nova oferta**.
3. Selecione **Cortana Intelligence**.
4. Selecione a guia **Editor**, na exibição **Nova oferta**, para configurar as opções a seguir:
    -   Configurações da oferta
    -   Informações técnicas
    -   Detalhes da vitrine
    -   Contatos

    Cada uma das opções anteriores exibe um formulário que você preenche.  Os campos obrigatórios para cada formulário estão marcados com um asterisco vermelho (\*).

## <a name="to-configure-offer-settings"></a>Como definir as configurações da oferta

As configurações da oferta fornecem informações básicas sobre a oferta, como uma ID da oferta, a ID do editor e o nome da oferta.

### <a name="offer-id"></a>ID da oferta

Esse é um identificador exclusivo para a oferta em um perfil de fornecedor.
Essa ID está visível nas URLs dos produtos. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e pode ter um máximo de 50 caracteres. 
>[!Note]
>Esse campo é bloqueado quando uma oferta é ativada.

**Exemplo:**

Se o editor **contoso** criar uma oferta com a ID da oferta *sample-Cortana Intelligence*, ela será exibida no AppSource como "https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-Cortana Intelligence*?tab=Overview".

### <a name="publisher-id"></a>ID do fornecedor

Uma lista suspensa de perfis de editor. Utilize para escolher o perfil do editor em que deseja publicar essa oferta.

>[!Note]
>Esse campo é bloqueado quando uma oferta é ativada.

### <a name="name"></a>NOME

O nome de exibição da oferta. Esse nome é exibido no [AppSource](https://appsource.microsoft.com). Ele pode ter um máximo de 50 caracteres.

Quando terminar de fornecer as informações necessárias nas configurações da oferta, selecione **Salvar** para passar para a parte de informações técnicas da oferta. opção.

## <a name="to-configure-technical-info"></a>Como configurar as informações técnicas

Utilize esta exibição para fornecer as informações técnicas que serão exibidas na página da oferta. Os campos a seguir se aplicam a esta exibição.

### <a name="partner-mpn-id"></a>ID MPN do parceiro

Se você for um parceiro da Microsoft registrado, acesse o [site de parceiros](https://partners.microsoft.com/) e entre para obter sua ID de parceiro da organização. Insira essa ID para a **ID MPN do parceiro**.

### <a name="analytics-components-used"></a>Componentes de análise usados

Selecione todos os componentes que sua oferta usa. Selecione pelo menos 1 componente. Selecione **Microsoft R** apenas se estiver usando alguma das licenças MRS dos serviços SQL 2016 R, HDInsight Premium ou MRS em execução em VMs.

### <a name="additional-components-used"></a>Componentes adicionais usados

Selecione todos os outros componentes usados pela sua solução.

### <a name="customer-name-using-solution"></a>Nome do cliente que usa a solução

Forneça o nome do cliente que está usando essa solução na produção. 

>[!Note]
>Essas informações não são publicadas no AppSource. São usadas somente para avaliação da solução.

### <a name="azure-consumption-requirement-met"></a>O requisito de consumo do Azure foi cumprido?

Indique se a solução gera ou não pelo menos 1.000 USD por mês por cliente dos componentes do Azure Suite ou se a solução utiliza Microsoft R.

>[!Note]
>Essas informações não são publicadas no AppSource. São usadas somente para avaliação da solução.

### <a name="demo-video-url"></a>URL do vídeo de demonstração

Forneça uma URL de um vídeo de demonstração para a solução/aplicativo que sua equipe de pré-vendas possa mostrar aos clientes. 

>[!Note]
>Essas informações não são publicadas no AppSource. São usadas somente para avaliação da solução.

#### <a name="demo-video-guidelines"></a>Diretrizes do vídeo de demonstração

- A duração do vídeo deve ser inferior a 15 minutos.
- O vídeo deve ser editado ao mínimo, com uma gravação da funcionalidade da solução. 
- O vídeo destaca os principais aspectos da funcionalidade voltada ao usuário e concentra-se na integração da análise avançada. 
- Os vídeos de demonstração **não** ficam disponíveis publicamente aos clientes, mas devem representar como a solução *seria* mostrada a um cliente potencial. Assim, eles devem ser repetíveis e com script.
- Utilize qualquer ferramenta de gravação de tela que exporte um formato de vídeo padrão (por exemplo, MPEG) para criar o vídeo. 

As instruções a seguir mostram como criar um vídeo usando o Skype for Business. 

1. [Inicie uma reunião](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [Compartilhe sua área de trabalho](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [Inicie a gravação](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. Depois de parar a gravação, [use o gerenciador de gravações para publicar a gravação](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

Carregue o vídeo gravado em um serviço que permita que você gere uma URL compartilhada. Por exemplo, um [link convidado no OneDrive ou Sharepoint](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232).

### <a name="supported-regions"></a>Regiões com suporte

Selecione todas as regiões para as quais sua solução dá suporte. Selecione pelo menos 1 região.

### <a name="power-bi-desktop-file-pbix"></a>Arquivo do Power BI Desktop (.pbix)

Se aplicável, carregue um arquivo .pbix. Certifique-se de que os dados sejam importados para o arquivo, não referenciados externamente. Vamos criar o relatório incorporado para você.

### <a name="solution-architecture"></a>Arquitetura da solução

Carregue um documento que contenha detalhes sobre a arquitetura da solução. Para fazer upload de uma diretriz de diagrama da arquitetura da solução, veja [Modelo de fluxo de trabalho da solução de análise avançada](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx).

>[!Note]
>Essas informações não são publicadas no AppSource. São usadas somente para avaliação da solução.

### <a name="select-segments"></a>Selecionar segmentos

Selecione todos os segmentos do setor relacionados. Se seu aplicativo for para qualquer segmento não listado, digite o nome do segmento no campo **Outros**. O limite é de três palavras para o segmento.

### <a name="select-business-processes"></a>Selecionar processos de negócios

Selecione os processos de negócios que descrevem melhor sua solução. Se seu aplicativo for para qualquer processo não listado, digite o nome do processo no campo **Outros**. O limite é de três palavras para o processo.

### <a name="trial-info"></a>Informações da avaliação gratuita

-   **URL da avaliação gratuita de SaaS:** insira a URL para a experiência de avaliação gratuita do seu aplicativo.
-   **URL da avaliação gratuita de Test Drive:** insira a URL para a experiência de Test Drive do seu aplicativo.

Para obter mais informações sobre as avaliações gratuitas, consulte **Tipo de aplicativo** na próxima seção deste artigo.

Quando terminar de fornecer as informações necessárias nas informações técnicas, selecione **Salvar** para passar para a parte de detalhes da vitrine da oferta. 

## <a name="to-configure-storefront-details"></a>Como configurar os detalhes da vitrine

### <a name="offer-summary"></a>Resumo da oferta

Este é um resumo da proposta de valor da sua oferta. Ele será exibido na página de pesquisa da sua oferta. O comprimento máximo do resumo é de 100 caracteres.

### <a name="offer-description"></a>Descrição da oferta

Esta é a descrição que será exibida na página de detalhes do seu aplicativo.
O tamanho máximo da descrição é de 1.300 caracteres.

Observe que esse campo utiliza conteúdo html com marcações como \<p\>, \<h1\>, \<h2\>, \<li\> etc., que permitem que você torne o conteúdo muito mais apresentável. A equipe do portal de publicação está trabalhando para adicionar um recurso que permite uma visualização dos detalhes da vitrine a fim de tornar o conteúdo iterativamente mais apresentável – enquanto isso, você pode usar qualquer ferramenta html em tempo real online, como [http://htmledit.squarefree.com](http://htmledit.squarefree.com/), para ver como ficaria a descrição.

O formato sugerido da descrição é dividir o texto em subseções com base em proposições de valor, cada uma destacada com um subtítulo. Geralmente, os visitantes verificam rapidamente o campo "Resumo da oferta" e os subtítulos para obter o conteúdo de endereço do aplicativo e o porquê eles devem considerar o aplicativo apenas diante de um rápido olhar. Portanto, é importante chamar a atenção do usuário e apresentar-lhe uma razão para ler e obter as especificidades.

#### <a name="partner-examples"></a>Exemplos de parceiro

- [Otimização de Inventário do Neal Analytics](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Personalização de Varejo Plexure](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [Serviços ao Cidadão AvePoint](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

### <a name="industries"></a>Indústrias

Selecione o setor ao qual seu aplicativo está mais alinhado. Se o seu aplicativo estiver relacionado a vários setores, deixe esse campo em branco.

### <a name="categories"></a>Categorias

Selecione as categorias relevantes para seu aplicativo. Selecione um máximo de duas categorias.

### <a name="app-type"></a>Tipo de aplicativo

Selecione o tipo de avaliação gratuita à qual seu aplicativo dará suporte no AppSource. Escolha uma das avaliações gratuitas a seguir:
- **Gratuito** significa que seu aplicativo é gratuito.
- **Avaliação gratuita** significa que os clientes podem testá-lo por um período especificado.
- **Solicitação de avaliação gratuita** significa que os clientes podem solicitar uma avaliação gratuita do aplicativo.

Os parceiros podem fornecer dois tipos de experiências de avaliação gratuita no AppSource.

- A opção **Avaliação gratuita**, também chamada de **CLT (Avaliações Gratuitas Conduzidas pelo Cliente)** pode ser de um dos tipos a seguir: 
    - Avaliação gratuita de SaaS
        - O cliente pode navegar até uma URL fornecida por você ou seu parceiro. O cliente passa por SSO federada do Azure Active Directory para ter uma experiência de avaliação gratuita com tempo limitado.
        - É um aplicativo SaaS com vários locatários que isola a configuração/dados de um usuário de outros usuários. Ou essa experiência fornece apenas um subconjunto que usa operações somente leitura.

        **Exemplos:**

        - [Execução de varejo POP AFS](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)
        - [Serviços ao Cidadão AvePoint](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview). (Esse aplicativo proporciona uma experiência estruturada. com caminhos claros, para um conjunto selecionado de personas de usuário.)

     - Test drive
        - Sua solução (ou do seu parceiro) ou um subconjunto dela pode ser empacotado usando modelos do Azure Resource Manager que o AppSource pode instanciar. O AppSource é capaz de gerenciar o aplicativo em uma assinatura de parceiro com conversão boxing de tempo e manter um pool quente/frio de instâncias etc.
        - Podemos fornecer modelos e código de exemplo para ajudá-lo, caso você escolha essa opção.

        **Exemplos:**

        - [Otimização de Inventário do Neal Analytics](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)

- A opção **Solicitação de avaliação gratuita** (**Avaliações Gratuitas Conduzidas pelo Parceiro / PLT**) exige que os clientes preencham um formulário de informações de contato para acompanhamento pelo parceiro. O parceiro faz o acompanhamento e fornece uma demonstração ou avaliação gratuita do aplicativo. Para obter mais informações, consulte [Passo a passo da experiência de avaliação do AppSource](http://aka.ms/trialexperienceforwebapps) para uma visão geral de alto nível.

>[!Note]
>Os dados mostram que **Avaliações Gratuitas Conduzidas pelo Cliente** têm um potencial de geração de cliente potencial maior do que **Avaliações Gratuitas Conduzidas pelo Parceiro**.


### <a name="help-link-for-your-app"></a>Link de ajuda para seu aplicativo

Forneça uma URL para uma página que tenha informações de ajuda do seu aplicativo.

### <a name="supported-countriesregions"></a>Países/regiões com suporte

Este campo determina os países/regiões em que sua oferta estará disponível para avaliação gratuita.

![Países/regiões com suporte](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>Idiomas com suporte

Selecione os idiomas aos quais o aplicativo dá suporte. Se seu aplicativo der suporte a idiomas que não estão nesta lista, publique sua oferta e envie um email para: <appsource@microsoft.com> para nos informar sobre suporte para outros idiomas.

### <a name="app-version"></a>Versão do aplicativo

Insira o número de versão para seu aplicativo.

### <a name="products-your-app-works-with"></a>Produtos com os quais seu aplicativo funciona

Lista produtos específicos com os quais seu aplicativo funciona. É possível listar um máximo de 3 produtos. Para listar um produto, selecione o **sinal de mais** (ao lado de novo) e um campo de texto aberto será criado para você. Insira o nome de um produto com o qual seu aplicativo funciona.

### <a name="hide-key"></a>Chave de ocultar

Essa é uma chave combinada com a URL de visualização da oferta para ocultar a oferta da exibição pública. Não é uma senha. Você pode inserir qualquer cadeia de caracteres alfanumérica.

### <a name="offer-logo-small"></a>Logotipo da oferta (pequena)

Esse logotipo será exibido na página de pesquisa do aplicativo. O formato de imagem png é o único formato que é permitido. O tamanho da imagem é 48 pixels por 48 pixels.

### <a name="offer-logo-large"></a>Logotipo da oferta (grande)

Esse logotipo será exibido na página de detalhes do aplicativo. O formato de imagem png é o único formato que é permitido. O tamanho da imagem é 48 pixels por 48 pixels.

### <a name="video"></a>Vídeo

Você pode carregar no máximo quatro vídeos. Para cada vídeo que quiser carregar:
- Forneça o nome do vídeo
- Forneça uma URL (apenas YouTube ou Vimeo)
- Forneça uma miniatura para associar ao vídeo. A miniatura deve usar o formato de imagem png e seu tamanho deve ser 1280 pixels X 720 pixels. 

Para adicionar novos vídeos, selecione **+ Novo**, mostrado na próxima captura de tela.

![Adicionar novo vídeo](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>Documento

Você pode carregar no máximo três documentos. Cada documento deve usar o formato de arquivo PDF. Para adicionar um novo documento:

- Selecione **+ Novo**
- Insira um nome de documento
- Selecione **Carregar** para carregar um documento.

![Adicionar novo documento](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>Política de Privacidade

Insira a URL para a política de privacidade do aplicativo

### <a name="terms-of-use"></a>Termos de uso

Insira os termos de uso do aplicativo. Os clientes do AppSource precisam aceitar esses termos antes de poderem testar o aplicativo.

>[!Note]
>Este campo aceita conteúdo que usa marcações HTML, como o conteúdo html <p\>, <h1\> e <li\>. É possível usar essas marcações para formatar o conteúdo. 

### <a name="lead-destination"></a>Destino do cliente potencial

Selecione um sistema CRM no qual seus clientes potenciais serão armazenados. 

Selecione **Tabela do Azure** se estiver usando um destes sistemas de CRM: Salesforce, Marketo ou Microsoft Dynamics CRM. 

Para obter mais informações sobre o sistema CRM que deseja usar, selecione um dos links a seguir para sistemas com suporte.

-   [Tabela do Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
