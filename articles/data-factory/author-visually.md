---
title: Criar visualmente data factories do Azure | Microsoft Docs
description: Saiba como criar visualmente data factories do Azure
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
ms.openlocfilehash: 3e67665facba78c4ca8e2317f0323b4c5c02a49c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2018
---
# <a name="visually-author-data-factories"></a>Criar visualmente as fábricas de dados
Com a experiência de UX do Azure Data Factory, os usuários podem criar visualmente e implantar recursos em seu data factory sem precisar escrever uma única linha de código. Essa interface sem código permite que você arraste e solte atividades em uma tela de pipeline, realizar execuções de teste, depurar iterativamente e implantar e monitorar a execução do pipeline. Você pode usar a ferramenta de ADF UX de duas maneiras:

1. Trabalhar diretamente com o serviço de Data Factory
2. Configurar a integração do Git do VSTS para colaboração, controle do código-fonte e controle de versão

## <a name="authoring-with-data-factory"></a>Criar com Data Factory
A primeira opção é criar diretamente com o modo Data Factory. Essa abordagem é diferente da criação por meio do repositório de código do VSTS em que não há nenhum repositório armazenando as entidades JSON de suas alterações, nem é otimizado para colaboração ou controle de versão.

![Configurar o Data Factory](media/author-visually/configure-data-factory.png)

No modo Data Factory, há apenas o modo 'Publicar'. As alterações feitas são publicadas diretamente no serviço do Data Factory.

![Publicação do Data Factory](media/author-visually/data-factory-publish.png)

## <a name="authoring-with-vsts-git-integration"></a>Criando com a integração do Git do VSTS
A criação com a integração do Git do VSTS permite o controle do código-fonte e de colaboração durante a criação de seus pipelines de data factory. Os usuários têm a opção de associar um data factory com um repositório de conta do Git do VSTS para controle do código-fonte, colaboração, controle de versão etc. Uma única conta do GIT do VSTS pode ter vários repositórios. No entanto, um repositório Git do VSTS só pode ser associado a uma única data factory. Se você não tiver uma conta do VSTS e já tiver o repositório, crie um [aqui](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student).

### <a name="configure-vsts-git-repo-with-azure-data-factory"></a>Configurar o repositório Git do VSTS com Azure Data Factory
Os usuários podem configurar um repositório GIT do VSTS com um data factory por meio de dois métodos.

#### <a name="method-1-lets-get-started-page"></a>Método 1: Página 'Vamos começar'

Vá para a página 'Vamos começar' e clique em 'Configurar Repositório de Código'

![Configurar Repositório de Código](media/author-visually/configure-repo.png)

A partir daí, um painel lateral será exibido para definir as configurações do repositório.

![Parâmetros de configuração do repositório](media/author-visually/repo-settings.png)
* **Tipo de repositório**: Git do Visual Studio Team Services (no momento, não há suporte para Github).
* **Conta do Visual Studio Team Services**: o nome da conta pode ser localizado em https://{nome da conta}.visualstudio.com. Entre em sua conta do VSTS [aqui](https://www.visualstudio.com/team-services/git/) e acesse seu perfil do Visual Studio para ver seus repositórios e projetos
* **ProjectName:** o nome do projeto pode ser localizado em https://{nome da conta}.visualstudio.com/{nome do projeto}
* **RepositoryName:** o nome do repositório. Os projetos do VSTS contêm repositórios Git para gerenciar seu código-fonte, à medida que o projeto se expande. Crie um novo repositório ou use um existente que já esteja no projeto.
* **Importar recursos existentes do Data Factory para o repositório**: se você marcar esta caixa, poderá importar seus recursos atuais do data factory criados na tela de UX para o repositório GIT do VSTS associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, serviços vinculados e conjuntos de dados são exportados para JSONs separados).    Se você desmarcar essa caixa de seleção, os recursos existentes não são importados para o repositório Git.

#### <a name="method-2-from-authoring-canvas"></a>Método 2: Da tela de criação

Em 'Tela de criação', clique no menu suspenso de 'Data Factory' sob o nome do data factory. Em seguida, clique em 'Configurar Repositório de Código'. Assim como ocorre no **Método 1**, um painel lateral é exibido para definir as configurações do repositório. Consulte as seções anteriores para obter informações sobre as configurações.

![Configurar repositório de código 2](media/author-visually/configure-repo-2.png)

### <a name="version-control"></a>Controle de versão
Os sistemas de controle de versão, também chamado de controle do código-fonte, permitem aos desenvolvedores colaborar em código e acompanhar as alterações feitas no código base. O controle do código-fonte é uma ferramenta essencial para projetos de vários desenvolvedores.

Cada repositório Git do VSTS uma vez associado a um data factory tem um branch mestre. A partir daí, cada usuário que tem acesso ao repositório Git do VSTS tem duas opções ao fazer alterações: sincronizar e publicar.

![Sincronizar publicação](media/author-visually/sync-publish.png)

#### <a name="sync"></a>Sincronizar

Depois de clicar em 'sincronizar', você poderá receber alterações do branch mestre para o branch local ou enviar por push as alterações de seu branch local para o branch mestre.

![Sincronização de alterações](media/author-visually/sync-change.png)

#### <a name="publish"></a>Publicar
 Publique as alterações no branch mestre para o serviço Data Factory.

> [!NOTE]
> O **branch mestre não é representativo do que é implantado no serviço de Data Factory.** O branch mestre *deve* ser publicado manualmente no serviço de Data Factory.




## <a name="expression-language"></a>Linguagem de expressão

Os usuários podem especificar expressões ao definir valores de propriedade usando a linguagem de expressão com suporte pelo Azure Data Factory. Consulte [Expressões e funções no Azure Data Factory](control-flow-expression-language-functions.md) para saber mais sobre quais expressões têm suporte.

Especifique as expressões em valores de propriedade de UX da seguinte forma.

![Linguagem de expressão](media/author-visually/expression-language.png)

## <a name="parameters"></a>parâmetros
Os usuários podem especificar parâmetros para pipelines e conjuntos de dados na guia 'Parâmetros'. Além disso, use parâmetros nas propriedades com facilidade pressionando "Adicionar Conteúdo Dinâmico".

![Conteúdo dinâmico](media/author-visually/dynamic-content.png)

A partir daí, você pode utilizar um parâmetro existente ou especificar um novo no valor da propriedade.

![parâmetros](media/author-visually/parameters.png)

## <a name="feedback"></a>Comentários
Clique no ícone 'Comentários' para fazer comentários para a Microsoft em vários recursos ou problemas que você possa estar enfrentando.

![Comentários](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o monitoramento e o gerenciamento de pipelines, veja o artigo [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md)
