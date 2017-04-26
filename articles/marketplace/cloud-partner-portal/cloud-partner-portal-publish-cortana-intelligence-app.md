---
title: Publicar um aplicativo Cortana Intelligence no AppSource? | Microsoft Docs
description: "Guia passo a passo para a publicação de um aplicativo Cortana Intelligence ou Microsoft R Services no AppSource"
services: marketplace
documentationcenter: 
author: hiavi
manager: judym
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: abathula
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 62aa3148406068f06d1fbdaf556ff2d526c0e17b
ms.lasthandoff: 04/13/2017


---
# <a name="publish-a-cortana-intelligence-app-to-appsource"></a>Publicar um aplicativo Cortana Intelligence no AppSource?
Guia passo a passo para a publicação de um aplicativo Cortana Intelligence ou Microsoft R Services no AppSource

## <a name="1-introduction"></a>1. Introdução

Agradecemos seu interesse na publicação do aplicativo Cortana Intelligence/Microsoft R Solutions no Microsoft AppSource. A Microsoft está entusiasmada em trabalhar com nossos parceiros dedicados (ISVs/SIs) para fornecer o local premier para clientes, revendedores e parceiros de implementação, a fim de descobrir e experimentar soluções de negócios e obter o máximo de seus investimentos. Este guia explica cada uma das etapas para publicar sua solução.

## <a name="2-getting-started"></a>2. Introdução

Siga as instruções

1.  Em [Guia inicial para ser apresentado à Microsoft](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/CESIPartnerJourneyOverview_1232017.pdf) para ser listado como um parceiro de análise avançada.

2.  No AppSource, preenchendo o formulário [Listar um aplicativo](https://appsource.microsoft.com/partners/list-an-app).
    - Para a pergunta *"Escolha os produtos para os quais seu aplicativo foi criado*", marque a caixa de seleção **Outros** e liste "Cortana Intelligence" no controle de edição.

## <a name="3-solution-evaluation-criteria"></a>3. Critérios de avaliação da solução

Aqui está a lista de critérios que o aplicativo precisa atender

1.  O aplicativo precisa abordar problemas específicos de caso de uso de negócios dentro de determinada área funcional repetidamente. Com configurações/personalizações mínimas, deve ser capaz de fornecer proposta de valor predefinido em um curto período de tempo.

2.  A solução deve usar pelo menos um dos seguintes componentes de análise

    1. HDInsight
    2. Aprendizado de Máquina
    3. Data Lake Analytics
    4. Stream Analytics
    5. Serviços Cognitivos
    6. Ambas as estruturas
    7. Microsoft R Server autônomo ou serviços do R no SQL 2016/HDInsight Premium

3.  A solução deve gerar pelo menos \$1 K/mês/cliente usando programas DPOR (Parceiro Digital de Registro) ou CSP (Provedor de Soluções na Nuvem).

4.  A solução deve usar o SSO federado do AAD (Logon Único federado do Azure Active Directory) habilitado para autenticação de usuário e controles de acesso ao recurso. Se seu aplicativo não ainda estiver habilitado, consulte

    1. [Guia de Introdução](https://identity.microsoft.com/)
    2. [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)

5.  Use o Power BI na solução e compartilhe arquivo da área de trabalho do Power BI (PBIX): opcional, mas altamente recomendado, já que conforme comprovado, gera um número maior de leads

    1. Verifique se todos os dados foram importados para o arquivo PBIX
    2. Sem dados referenciados externamente – criamos o relatório incorporado para você.

## <a name="4-decide-on-the-type-of-offer-you-would-like-to-publish-on-appsource"></a>4. Decidir o tipo de oferta que você quer publicar no AppSource

O AppSource permite que os parceiros ofereçam dois tipos de experiências de avaliação aos visualizadores
1.  As avaliações são lideradas por clientes, que podem experimentar o aplicativo por si mesmos
    1.  Avaliação gratuita
        1.  O usuário do AppSource navega até uma URL que você (parceiro) fornece, que o conduz através do SSO federado do AAD e fornece uma experiência de avaliação de tempo demarcado.
        2.  A expectativa aqui é que seu aplicativo é um aplicativo SaaS com suporte de multilocação para isolar configuração/dados de um usuário do outro ou a experiência de avaliação oferece apenas uma experiência de subconjunto que só precisa de operações somente leitura.

        Exemplo: Consulte [AFS POP Retail Execution](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview), [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview) (esse aplicativo fornece uma experiência estruturada com caminhos claros para o conjunto selecionado de personas de usuário) etc.
    2.  Test drive
        1.  Sua solução (parceiro) ou um subconjunto dela pode ser empacotada usando modelos do Azure Resource Manager que o AppSource pode instanciar e gerenciar na assinatura do Azure do parceiro, com demarcação de tempo e manutenção ativa/passiva de pool de instâncias etc.
        2.  Poderemos fornecer modelos e código de exemplo para ajudá-lo, se você escolher essa rota.
        
        Exemplo: Veja [Otimização de estoque](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) do Neal Analytics.

2.    O parceiro lidera as avaliações, o que exige que os clientes preencham um formulário de informações de contato para que o parceiro possa acompanhar e fornecer uma demonstração/avaliação etc.

Assista ao vídeo [Passo a passo da experiência de avaliação do AppSource](http://aka.ms/trialexperienceforwebapps) para uma visão geral de alto nível.

Os dados mostram claramente que quando o cliente lidera as avaliações, há um alto potencial de geração de leads, em contraste com quando o parceiro lidera as avaliações – quem gosta de preencher formulários deve pensar sobre isso 😊

Decida qual tipo de oferta você deseja adicionar ao AppSource.

## <a name="5-getting-approved-to-use-azure-marketplace-publishing-portal"></a>5. Obtendo aprovação para usar o portal de publicação do Azure Marketplace

A partir de agora, as IDs de email do AAD/MSA precisam ser aprovadas para acessar o Portal de publicação do Azure Marketplace a fim de iniciar a publicação. Compartilhe gentilmente os seguintes detalhes em [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs)

-   Nome de exibição do fornecedor: &lt;nome da empresa como aparece no AppSource como fornecedor do aplicativo&gt;

-   Nome exclusivo do fornecedor: &lt;nome exclusivo usando apenas caracteres minúsculos e hífen '-'&gt;

-   Site: &lt;site da empresa&gt;

-   Email do proprietário: &lt;ID de email do AAD/MSA&gt;

-   Email do colaborador2: &lt;ID de email do AAD/MSA&gt;

 Se necessário, adicione mais colaboradores.

## <a name="6-get-info-about-your-companys-lead-management"></a>6. Obter informações sobre o gerenciamento de leads da empresa

À medida que o visitante expressa interesse nas soluções, esses leads são passados diretamente para os sistemas de gerenciamento de leads do parceiro. O AppSource dá suporte a vários tipos de sistemas de gerenciamento de leads (Dynamics CRM, Salesforce, Marketo etc.).

Consulte [Gerenciamento de leads de marketing do AppSource](./cloud-partner-portal-get-customer-leads.md) para obter detalhes do processo geral e como você pode encontrar as informações certas para configurar os leads do AppSource.    

## <a name="7-publish-your-app-on-the-publishing-portal"></a>7. Publicar seu aplicativo no portal de publicação

Se você precisar de informações sobre qualquer um dos campos que ignoramos, envie email para <appsourcecissupport@microsoft.com>

Siga estas etapas

1.  Usando o navegador Google Chrome, abra [Portal do Cloud Partner](https://cloudpartner.azure.com)

2.  Entre com sua ID de email do AAD/MSA da lista branca

3.  Selecione nova oferta – &gt; Cortana Intelligence

    ![publishaaapp1][1]
4.  Preencha os detalhes de configurações da oferta

    ![publishaaapp2][2]

    1. A ID da oferta é usada exclusivamente para identificar seu aplicativo no AppSource como parte do URL exclusivo do AppSource. Exemplo: "appsource-saas-app". Ela ficará visível aos clientes em locais como modelos do Azure Resource Manager e relatórios de cobrança. Observe que as informações de ID da oferta e do fornecedor não podem ser alteradas depois de salvas.

    2. Para Nome da Oferta, use o nome da solução. Não adicione o nome da empresa uma vez que ele é adicionado automaticamente abaixo do nome da solução

5.  Preencha a seção de informações técnicas – a maior parte da seção é autoexplicativa e tem dicas de ferramenta com mais informações.

    1.  Os vídeos de demonstração devem ser gravações minimamente editadas sobre a funcionalidade real da solução, realçando a autenticação dos usuários usando o AAD, os principais aspectos da funcionalidade de usuários e a integração com a plataforma do Cortana Intelligence.  Os vídeos de demonstração **não** ficam disponíveis publicamente aos clientes, mas devem representar como a solução *seria* mostrada a um cliente potencial.  Assim, eles devem ser repetíveis e com script.

        Qualquer ferramenta de gravação de tela que exporta um formato de vídeo padrão (por exemplo, MPEG) pode ser usada para preparar o vídeo de demonstração. Aqui estão as instruções se você tiver o Skype for Business

        1.  [Inicie uma reunião](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
        2. [Compartilhe sua área de trabalho](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
        3. [Inicie a gravação](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
        4. Depois de parar a gravação, [use o gerenciador de gravações para publicar a gravação](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

        Carregue o vídeo gravado em um serviço que permita que você gere uma URL compartilhada.  Por exemplo, você pode usar um [link convidado no OneDrive/Sharepoint](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232). 

    2.  Consulte [Modelo de fluxo de trabalho da solução de análise avançada](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx) para obter diretrizes ao carregar o diagrama de arquitetura da solução

6.  Preencha a seção Detalhes da Vitrine Eletrônica

    1.  Observação: os campos "Descrição da Oferta" e "Termos de uso" podem usar conteúdo html. Use qualquer editor html online para ajustar o conteúdo html até obter a aparência correta. A descrição da oferta deve, de forma clara e sucinta, fornecer um bom apelo comercial do caso de negócios que resolve, o ROI que um cliente obteria e quaisquer manuais e acessórios de apoio a esse apelo.

    2.  Tipo de aplicativo: com base na seção anterior, intitulada "Decidir o tipo de oferta que você que publicar no AppSource", selecione a opção
        1.  "Gratuito" para oferecer aos clientes experiência gratuita totalmente sem demarcação de tempo.
        2.  "Avaliação" se quiser dar aos clientes experiência de avaliação/test drive, com demarcação de tempo
        3.  "Solicite uma avaliação" se quiser dar aos clientes uma experiência de avaliação liderada pelo parceiro.

    3.  Oculte a chave para testar sua oferta de preparo: ela é usada para criar uma URL de visualização para o seu aplicativo depois que ele é preparado e não é uma senha, por isso, ela pode ser simples. Exemplo: “AppSourceHideKey”

    4.  Imagens carregadas precisam ser imagens legíveis de alta qualidade para que a publicação da oferta seja aprovada.

    5.  Estudo de caso: fornecer um estudo de caso/história com os seguintes detalhes

        1.  Implementação operacionalizada do cliente
        2. Instrução de problema claramente definida para clientes reais ou anônimos
        3. Etapas para resolver o problema
        4. Faça referência aos componentes de análise avançada CIS/Microsoft que ajudaram a resolver o problema.
        5.  Benefício líquido ou ROI da solução instituída
        6. *O que não queremos*: folheto de marketing ou vídeo com apenas uma explicação sobre a plataforma geral sem os requisitos acima

    6.  Altamente recomendável uma vez que gera mais leads: um vídeo com os mesmos requisitos do estudo de caso a ser exibido no AppSource. Ao inserir a URL do vídeo, os vídeos podem ser vídeos do Youtube ou Vimeo. Verifique se que os vídeos estão em um destes formatos
        1. https://vimeo.com/########## ou
        2. https://www.youtube.com/watch?v=##########

7.  Preencha a seção Contatos – ela será usada para enviar a notificação de uso, alertas de mercado etc.

8.  Publique sua oferta. Você receberá notificações por email à medida que a solução passa pelo processo de certificação. Você pode ver detalhes de status na guia "Status" da interface do usuário de publicação da oferta.

    1.  Suas soluções serão preparadas primeiro.
    2.  Você pode verificar o conteúdo de teste clicando no link AppSource que receberá por email.
    3.  Faça as alterações necessárias e envie novamente. Quando estiver satisfeito com o conteúdo final, envie o aplicativo por push para produção.
    4.  Neste ponto, validamos os metadados do aplicativo e finalmente o aprovamos para ser listado no AppSource ou para ser acompanhado por um motivo da rejeição caso rejeitemos o aplicativo.

[1]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp1.png
[2]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp2.png    

