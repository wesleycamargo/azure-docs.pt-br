---
title: Criação visual no Azure Data Factory | Microsoft Docs
description: Saiba como usar a criação visual no Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: shlo
ms.openlocfilehash: a226402f16f98c830c1e1cf34653b8f6513f06b4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="visual-authoring-in-azure-data-factory"></a>Criação visual no Azure Data Factory
A experiência (UX) da interface do usuário do Azure Data Factory permite criar e implantar visualmente recursos para seu data factory sem ter que gravar nenhum código. Você pode arrastar atividades para uma tela de pipeline, realizar execuções de teste, depurar iterativamente e implantar e monitorar as execuções do pipeline. Há duas abordagens para usar a UX para executar a criação visual:

- Criar diretamente com o serviço de Data Factory.
- Criar com a integração do Git do VSTS (Visual Studio Team Services) para colaboração, controle do código-fonte e controle de versão.

## <a name="author-directly-with-the-data-factory-service"></a>Criar diretamente com o serviço de Data Factory
A criação visual com o serviço de Data Factory difere da criação visual de com VSTS de duas maneiras:

- O serviço de Data Factory não inclui um repositório para armazenar as entidades JSON para as alterações.
- O serviço de Data Factory não está otimizado para colaboração ou controle de versão.

![Configurar o serviço do Data Factory ](media/author-visually/configure-data-factory.png)

Quando você usa a **Tela de criação** UX para criar diretamente com o serviço do Data Factory, somente o modo **Publica Tudo** estará disponível. As alterações feitas são publicadas diretamente no serviço do Data Factory.

![Modo Publicar](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Criar com a integração do Git do VSTS
A criação visual com a integração do Git do VSTS oferece suporte ao controle do código-fonte e à colaboração para trabalhar em seus pipelines de data factory. Você pode associar um data factory com um repositório de conta do Git do VSTS para controle do código-fonte, colaboração, controle de versão e assim por diante. Uma única conta do Git do VSTS pode ter vários repositórios, mas um repositório do Git do VSTS pode ser associado somente a um data factory. Se não tiver uma conta ou repositório do VSTS, siga [estas instruções](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) para criar seus recursos.

> [!NOTE]
> Você pode armazenar arquivos de dados e scripts em um repositório GIT do VSTS. No entanto, você precisa carregar os arquivos manualmente para o Armazenamento do Azure. Um pipeline do Data Factory não carrega automaticamente os arquivos de dados ou scripts armazenados em um repositório GIT do VSTS para o Armazenamento do Azure.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Configurar o repositório do Git do VSTS com o Azure Data Factory
Você pode configurar um repositório do GIT do VSTS com um data factory por meio de dois métodos.

<a name="method1"></a>
#### <a name="configuration-method-1-lets-get-started-page"></a>Método de configuração 1: Página Vamos começar
No Azure Data Factory, vá para a página **Vamos começar**. Selecione **Configurar Repositório de Código**:

![Configurar um repositório de código de VSTS](media/author-visually/configure-repo.png)

O painel de configuração **Definições do repositório** é exibido:

![Configurar as definições do repositório de código](media/author-visually/repo-settings.png)

O painel mostra as definições do repositório de código do VSTS a seguir:

| Configuração | DESCRIÇÃO | Valor |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo de repositório de código do VSTS.<br/>**Observação**: atualmente, não há suporte para o GitHub. | Git do Visual Studio Team Services |
| **Azure Active Directory** | Seu nome de locatário do Microsoft Azure AD. | <your tenant name> |
| **Conta do Visual Studio Team Services** | O novo nome da conta do VSTS. Você pode localizar o nome da conta do VSTS em `https://{account name}.visualstudio.com`. Você pode [entrar em sua conta do VSTS](https://www.visualstudio.com/team-services/git/) para acessar seu perfil do Visual Studio e ver seus repositórios e projetos. | \<nome da sua conta> |
| **ProjectName** | O nome do projeto VSTS. Você pode localizar o nome do projeto VSTS em `https://{account name}.visualstudio.com/{project name}`. | \<nome do projeto VSTS> |
| **RepositoryName** | O nome do repositório de código do VSTS. Os projetos do VSTS contêm repositórios Git para gerenciar seu código-fonte, à medida que o projeto se expande. Você pode criar um novo repositório ou usar um existente que já esteja no projeto. | \<nome do repositório de código do VSTS> |
| **Ramificação de colaboração** | Sua ramificação de colaboração do VSTS que será usada para publicação. Por padrão, é `master`. Altere essa opção se você deseja publicar recursos de outra ramificação. | \<seu nome de ramificação de colaboração> |
| **Pasta raiz** | Sua pasta raiz em sua ramificação de colaboração VSTS. | \<seu nome da pasta raiz> |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se deve-se importar recursos do data factory existentes da UX **Tela de criação** em um repositório do Git do VSTS. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: Tela de criação da UX
Na UX **Tela de criação** do Azure Data Factory, localize seu data factory. Selecione o menu suspenso **Data Factory** e selecione **Configurar repositório de código**.

Um painel de configuração é exibido. Para obter detalhes sobre as definições de configuração, consulte as descrições no <a href="#method1">Método de configuração 1</a>.

![Configurar as definições do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>Usar controle de versão
Os sistemas de controle de versão (também conhecidos como _controle do código-fonte_) permitem aos desenvolvedores colaborar em código e acompanhar as alterações feitas no código base. O controle do código-fonte é uma ferramenta essencial para projetos de vários desenvolvedores.

Cada repositório do Git do VSTS associado a um data factory tem uma ramificação de colaboração. (`master` é a ramificação de colaboração padrão). Os usuários criam ramificações do recurso clicando **+ Nova ramificação** e fazem o desenvolvimento nas ramificações do recurso.

![Alterar o código sincronizando ou publicando](media/author-visually/sync-publish.png)

Quando estiver pronto com o desenvolvimento de recurso em sua ramificação de recurso, clique em **Criar solicitação pull**. Isso o levará para GIT VSTS onde você pode gerar solicitações pull, revisões de código e mesclar alterações para a ramificação de colaboração. (`master` é o padrão). Você só tem permissão para publicar no serviço do Data Factory de sua ramificação de colaboração. 

![Criar uma nova solicitação pull](media/author-visually/create-pull-request.png)

#### <a name="publish-code-changes"></a>Publicar alterações de código
Depois de ter mesclado alterações para a ramificação de colaboração (`master` é o padrão), selecione **Publicar** para publicar manualmente as alterações de código na ramificação mestre para o serviço do Data Factory.

![Publicar as alterações no serviço do Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> O branch mestre não é representativo do que é implantado no serviço de Data Factory. O branch mestre *deve* ser publicado manualmente no serviço de Data Factory.

## <a name="use-the-expression-language"></a>Usar linguagem de expressão
Você pode especificar expressões para valores de propriedade usando a linguagem de expressão com suporte do Azure Data Factory. 

Especifique as expressões para valores de propriedade selecionando **Adicionar Conteúdo Dinâmico**:

![Usar linguagem de expressão](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Usar funções e parâmetros

Você pode usar funções ou especificar parâmetros para pipelines e conjunto de dados no **construtor de expressões** do Data Factory:

Para obter informações sobre as expressões compatíveis, consulte [Expressões e funções no Azure Data Factory](control-flow-expression-language-functions.md).

![Adicionar conteúdo dinâmico](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Fornecer comentários
Selecione **Feedback** para comentar sobre os recursos ou notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o monitoramento e o gerenciamento de pipelines, c [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md).
