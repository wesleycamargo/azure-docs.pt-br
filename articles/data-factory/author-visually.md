---
title: "Criação visual no Azure Data Factory | Microsoft Docs"
description: "Saiba como usar a criação visual no Azure Data Factory"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 81b97bb6b6abb5431bedd4efec5f807fa577c4e4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
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

Quando você usa a UX **Tela de criação** para criar diretamente com o serviço do Data Factory, somente o modo **Publicar** está disponível. As alterações feitas são publicadas diretamente no serviço do Data Factory.

![Modo Publicar](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Criar com a integração do Git do VSTS
A criação visual com a integração do Git do VSTS oferece suporte ao controle do código-fonte e à colaboração para trabalhar em seus pipelines de data factory. Você pode associar um data factory com um repositório de conta do Git do VSTS para controle do código-fonte, colaboração, controle de versão e assim por diante. Uma única conta do Git do VSTS pode ter vários repositórios, mas um repositório do Git do VSTS pode ser associado somente a um data factory. Se não tiver uma conta ou repositório do VSTS, siga [estas instruções](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) para criar seus recursos.

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
| **Conta do Visual Studio Team Services** | O novo nome da conta do VSTS. Você pode localizar o nome da conta do VSTS em `https://{account name}.visualstudio.com`. Você pode [entrar em sua conta do VSTS](https://www.visualstudio.com/team-services/git/) para acessar seu perfil do Visual Studio e ver seus repositórios e projetos. | \<nome da sua conta> |
| **ProjectName** | O nome do projeto VSTS. Você pode localizar o nome do projeto VSTS em `https://{account name}.visualstudio.com/{project name}`. | \<nome do projeto VSTS> |
| **RepositoryName** | O nome do repositório de código do VSTS. Os projetos do VSTS contêm repositórios Git para gerenciar seu código-fonte, à medida que o projeto se expande. Você pode criar um novo repositório ou usar um existente que já esteja no projeto. | \<nome do repositório de código do VSTS> |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se deve-se importar recursos do data factory existentes da UX **Tela de criação** em um repositório do Git do VSTS. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: Tela de criação da UX
Na UX **Tela de criação** do Azure Data Factory, localize seu data factory. Selecione o menu suspenso **Data Factory** e selecione **Configurar repositório de código**.

Um painel de configuração é exibido. Para obter detalhes sobre as definições de configuração, consulte as descrições no <a href="#method1">Método de configuração 1</a>.

![Configurar as definições do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>Usar controle de versão
Os sistemas de controle de versão (também conhecidos como _controle do código-fonte_) permitem aos desenvolvedores colaborar em código e acompanhar as alterações feitas no código base. O controle do código-fonte é uma ferramenta essencial para projetos de vários desenvolvedores.

Cada repositório do Git do VSTS associado a um data factory tem um branch mestre. Quando você tem acesso a um repositório do Git do VSTS, pode alterar o código escolhendo **Sincronizar** ou **Publicar**:

![Alterar o código sincronizando ou publicando](media/author-visually/sync-publish.png)

#### <a name="sync-code-changes"></a>Sincronizar alterações de código
Depois de selecionar em **Sincronizar**, você poderá receber alterações do branch mestre para o branch local ou enviar por push as alterações de seu branch local para o branch mestre.

![Sincronizar alterações de código](media/author-visually/sync-change.png)

#### <a name="publish-code-changes"></a>Publicar alterações de código
Selecione **Publicar** para publicar manualmente as alterações de código no branch mestre para o serviço de data factory.

> [!IMPORTANT]
> O branch mestre não é representativo do que é implantado no serviço de Data Factory. O branch mestre *deve* ser publicado manualmente no serviço de Data Factory.

## <a name="use-the-expression-language"></a>Usar linguagem de expressão
Você pode especificar expressões para valores de propriedade usando a linguagem de expressão com suporte do Azure Data Factory. Para obter informações sobre as expressões compatíveis, consulte [Expressões e funções no Azure Data Factory](control-flow-expression-language-functions.md).

Especifique expressões para valores de propriedade usando A UX **Tela de criação**:

![Usar linguagem de expressão](media/author-visually/expression-language.png)

## <a name="specify-parameters"></a>Especificar parâmetros
Você pode especificar parâmetros para pipelines e conjuntos de dados na guia **Parâmetros** do Azure Data Factory. É possível usar facilmente os parâmetros em propriedades selecionando **Adicionar conteúdo dinâmico**:

![Adicionar conteúdo dinâmico](media/author-visually/dynamic-content.png)

Você pode usar parâmetros existentes ou especificar novos para os valores de propriedade:

![Especificar parâmetros para os valores de propriedade](media/author-visually/parameters.png)

## <a name="provide-feedback"></a>Fornecer comentários
Selecione **Feedback** para comentar sobre os recursos ou notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o monitoramento e o gerenciamento de pipelines, c [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md).
