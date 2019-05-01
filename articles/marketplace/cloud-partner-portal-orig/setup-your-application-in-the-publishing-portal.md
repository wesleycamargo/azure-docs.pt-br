---
title: Configurar seu aplicativo no Portal de Publicação
description: Instruções de como configurar seu aplicativo no Portal de Publicação de Nuvem.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6da80bd3b5fe487a44400f4f3c21e8fa49da75d4
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943549"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Configurar seu aplicativo no Portal de Publicação

Agora você está pronto para configurar seu aplicativo no portal de publicação.

## <a name="login-and-create-a-new-offer"></a>Fazer logon e criar uma nova oferta

1. Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).
2. Na barra de navegação à esquerda, clique em "+ Nova oferta" e selecione "Dynamics 365 for Customer Engagement".

![Como selecionar uma nova oferta](./media/CRMScreenShot14.png)

1. Uma exibição de Editor de nova oferta aparece agora para você e estamos prontos para começar a criar.

![Tela Nova oferta](./media/CRMScreenShot15.png)

1. Os formulários que precisam ser preenchidos estão visíveis à esquerda na exibição Editor. Cada formulário consiste em um conjunto de campos que devem ser preenchidos. Os campos obrigatórios estão marcados com um asterisco vermelho (\*).

Há quatro formas principais de criar uma oferta do Dynamics 365 for Customer Engagement

* Configurações da oferta
* Informações técnicas
* Detalhes da vitrine
* Contatos

## <a name="fill-out-the-offer-settings-form"></a>Preencher o formulário de Configurações da oferta

O formulário de configurações da oferta é um formulário básico para especificar as configurações da oferta. Os diferentes campos são descritos abaixo.

### <a name="offer-id"></a>ID da oferta

Esse é um identificador exclusivo para a oferta em um perfil de fornecedor. Essa ID estará visível em URLs do produto. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e pode ter um máximo de 50 caracteres. Esse campo é bloqueado quando uma oferta é ativada.

Por exemplo, se um publicador **"contoso"** Publicadores cria uma oferta com a ID de oferta **"exemplo de WebApp"**, ele será exibido no AppSource como "https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>ID do fornecedor

Este menu suspenso permite que você escolha o perfil de fornecedor em que deseja publicar essa oferta. Esse campo é bloqueado quando uma oferta é ativada.

### <a name="name"></a>NOME

Nome de exibição da oferta. Esse é o nome que será exibido no [AppSource](https://appsource.microsoft.com/). Ele pode ter um máximo de 50 caracteres.

Clique em "Salvar" para salvar o progresso. A próxima etapa seria adicionar Informações técnicas à oferta.

## <a name="fill-out-the-technical-info-form"></a>Preencher o formulário de Informações técnicas


É no formulário de informações técnicas que você preencherá informações específicas para a solução Dynamics 365 for Customer Engagement. Passar o mouse sobre ele apresentará mais informações a você. Veja o exemplo abaixo.

![Tela Informações técnicas](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informações do aplicativo

A maioria dos editores deixará esses campos com os valores padrão, Usuário, Não, Não e uma URL de Configuração de Aplicativo em branco, de acordo com a captura de tela acima.

### <a name="crm-package"></a>Pacote CRM

![Informações de pacote CRM](./media/CRMScreenShot17.png)

Aqui está uma explicação para esses campos:

* Nome do arquivo do seu pacote: o nome do arquivo que você criou na etapa acima ao criar o arquivo zip que é o seu pacote CRM do AppSource. No exemplo acima, é "Microsoft\_SamplePackage.zip".
* URL do local do pacote: a URL para a conta de Armazenamento do Azure que contém o nome do arquivo de pacote especificado acima. Essa é a URL criada na etapa 9 da seção acima.
* Há mais de um pacote crm em seu arquivo de pacote: Selecione Sim **APENAS** se você estiver dando suporte a várias versões de CRM com pacotes diferentes. Para a maioria dos parceiros, isso será "Não". Se selecionar Sim, você precisará criar pacotes do AppSource para cada versão de sua solução. _Observação: isso NÃO está perguntando se você tem vários arquivos **zip**. Se tiver vários arquivos zip de solução mas apenas uma versão, você ainda deverá selecionar "Não". A ferramenta de empacotamento os reunirá automaticamente._

### <a name="crm-package-availability"></a>Disponibilidade do Pacote CRM

Nesta seção, selecione para quais regiões do CRM seu pacote será disponibilizado. Para obter informações sobre quais CRM regiões servem que países/regiões, consulte o link: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Observação: implantar para "Soberana e nuvem US Gov" da Alemanha REQUER PERMISSÃO ESPECIAL e VALIDAÇÃO DURANTE A CERTIFICAÇÃO

## <a name="storefront-details"></a>Detalhes da vitrine

### <a name="offer-summary"></a>Resumo da oferta

Este é um resumo da proposta de valor da sua oferta. Ele será exibido na página de pesquisa da sua oferta. Ele deve ter um máximo de 100 caracteres.

### <a name="offer-description"></a>Descrição da oferta

Esta é a descrição que será exibida na página de detalhes de seu aplicativo. Máximo permitido é de 1300 caracteres

### <a name="industries"></a>Indústrias

Selecione o setor ao qual seu aplicativo está mais alinhado. Se o aplicativo for relacionado a vários setores, você poderá deixar isso em branco.

### <a name="categories"></a>Categories

Selecione as categorias relevantes para seu aplicativo. Selecione um máximo de 3.

### <a name="app-type"></a>Tipo de aplicativo

Selecione o tipo de avaliação permitirá que seu aplicativo no AppSource. 'Gratuito' significa que seu aplicativo é gratuito. 'Avaliação' significa que os clientes podem testar seu aplicativo por um curto período no AppSource. 'Solicitar avaliação' não é uma opção compatível para aplicativos Dynamics 365 for Customer Engagement. Não selecione essa opção.

### <a name="help-link-for-your-app"></a>Link de ajuda para o aplicativo

Insira a URL para uma página que tem informações de ajuda relacionadas para seu aplicativo.

### <a name="supported-countriesregions"></a>Regiões e países com suporte

Este campo determina os países / regiões em que sua oferta estará disponível para avaliação.

### <a name="supported-languages"></a>Idiomas com suporte

Selecione os idiomas aos quais o aplicativo dá suporte. Se o aplicativo der suporte a idiomas adicionais que não estejam nesta lista, continue para publicar sua oferta e nos informe pelo email [appsource@microsoft.com](mailto:appsource@microsoft.com).

### <a name="app-version"></a>Versão do aplicativo

Insira o número de versão para seu aplicativo

### <a name="app-release-date"></a>Data de lançamento do aplicativo

Inserir a data de lançamento do aplicativo

### <a name="products-your-app-works-with-max-3"></a>Produtos com que o aplicativo funciona (máx. de 3)

Produtos específicos de lista com o quais o aplicativo funciona. Você pode listar no máximo três produtos. Para listar um produto, clique no sinal de mais (ao lado do novo) e será criado um novo campo de texto em aberto para que você insira o nome de um produto com o qual o aplicativo funciona.

### <a name="search-keywords-max-3"></a>Pesquisar palavras-chave (máx. de 3)

O AppSource permite que o cliente pesquise com base em palavras-chave. Você pode inserir o conjunto de palavras-chave pelas quais o aplicativo será exibido para os clientes.

Por exemplo, se o aplicativo é, "Meu serviço de email" Emails, Correio Eletrônico e Serviço de Email podem ser algumas palavras-chave. Escolha palavras que os usuários provavelmente usarão para pesquisar pelo aplicativo na caixa de pesquisa do AppSource.

### <a name="hide-key"></a>Chave de ocultação

Essa é uma chave que será combinada com a URL da versão prévia da oferta para ocultá-la da exibição pública. Ela não é uma senha. Você pode inserir qualquer cadeia de caracteres aqui.

### <a name="offer-logo-png-format-48x48"></a>Logotipo da oferta (formato .png, 48 x 48)

Isso será exibido na página de pesquisa do aplicativo. **Somente o formato .png é permitido.** Carregar uma imagem png com resolução de 48PX\*48PX

### <a name="offer-logo-png-format-216x216"></a>Logotipo da oferta (formato .png, 216 x 216)

Isso será exibido na página de detalhes do aplicativo. **Somente o formato .png é permitido.** Carregar uma imagem png com resolução de 216PX\*216PX

### <a name="videos"></a>vídeos

Você pode carregar no máximo quatro vídeos. Para cada vídeo que você deseja fazer upload, você precisa preencher o nome do vídeo, a URL (somente YouTube ou Vimeo) e a miniatura para associar ao vídeo. A miniatura deve estar no formato png e deve ter tamanho de 1.280 \* 720 px. Para adicionar novos vídeos, clique no sinal de mais. Miniaturas de vídeo aparecerão na página de detalhes do aplicativo.

### <a name="documents"></a>Documentos

Você pode fazer upload de no máximo três documentos no formato PDF. Para cada documento que você deseja fazer upload, você precisa preencher o nome do documento e fazer upload do documento. O documento precisa estar no formato PDF.

Para adicionar novos documentos, clique no sinal de adição

### <a name="screenshots"></a>Capturas de tela

Essas são as capturas de tela que aparecerão na página de detalhes do AppSource para o aplicativo.

### <a name="privacy-policy"></a>Política de Privacidade

Insira a URL para a política de privacidade do aplicativo

### <a name="terms-of-use"></a>Termos de uso

Insira os termos de uso do aplicativo. Os clientes da AppSource são obrigados a aceitar estes termos antes de poderem experimentar a sua aplicação

### <a name="support-url"></a>URL do suporte

Insira a URL de suporte do aplicativo.

### <a name="lead-destination"></a>Destino do cliente potencial

Selecione um sistema CRM onde você lidera será armazenado. Selecione "Tabela do Azure" aqui se tiver um dos seguintes sistemas CRM: Salesforce, Marketo, Microsoft Dynamics CRM. O sistema CRM que você seleciona aqui é onde escreveremos os detalhes dos usuários finais que experimentam seu aplicativo no AppSource (leads). Dependendo do sistema CRM selecionado, clique no URL correspondente abaixo para obter informações sobre como concluir o próximo conjunto de campos

* [Tabela do Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Detalhes da vitrine

Os detalhes de contato são usados apenas para comunicação interna entre o parceiro e Microsoft. Observação: nesses campos, é importante usar um endereço de email monitorado. Usaremos esse email para comunicarmos a você seu progresso na publicação no AppSource. Somente a URL de suporte estará disponível para os clientes.
