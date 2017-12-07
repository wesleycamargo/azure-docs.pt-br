---
title: "Codificação colaborativa com o Git - Azure Machine Learning | Microsoft Docs"
description: "Como fazer o desenvolvimento de código de colaboração para projetos de ciência de dados usando o Git com o planejamento do Agile."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: e6808c0d9d4ca14101eeebe8b3e286d64e2328f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="collaborative-coding-with-git"></a>Codificação colaborativa com o Git

Este artigo descreve como fazer o desenvolvimento de código colaborativo para projetos de ciência de dados usando o Git como o framework do desenvolvimento de código compartilhado. Ele aborda como vincular essas atividades de codificação ao trabalho planejado no [Desenvolvimento do Agile](agile-development.md) e como fazer revisões de código.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Vincular um item de trabalho a uma Git branch 

O VSTS fornece uma maneira conveniente para se conectar a um item de trabalho (uma história ou uma tarefa) com uma Git branch. Isso permite que você vincule sua história ou tarefa diretamente ao código associado a ele. 

Para se conectar a um item de trabalho para uma nova branch, clique duas vezes em um item de trabalho e, na janela pop-up, clique em **Criar uma nova branch** em **+ Adicionar link**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Forneça as informações para essa nova branch, como nome da branch, repositório do Git de base e branch. O repositório do Git escolhido deve ser o repositório no mesmo projeto de equipe ao qual o item de trabalho pertence. A branch base pode ser a branch mestre ou alguma outra branch existente.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Uma prática recomendada é criar uma Git branch para cada item de trabalho da história. Em seguida, para cada item de trabalho da tarefa, você deve criar uma branch com base no na branch da história. Organizar as branches dessa maneira hierárquica, que corresponde às relações de tarefa da história, é útil quando você tem várias pessoas trabalhando em histórias diferentes do mesmo projeto ou quando há várias pessoas trabalhando em tarefas diferentes da mesma história. Conflitos podem ser minimizados quando cada membro da equipe trabalha em uma branch diferente e quando cada membro trabalha em códigos diferentes ou em outros artefatos ao compartilhar uma branch. 

A figura a seguir ilustra a estratégia recomendada de branch para TDSP. Talvez não sejam necessárias muitas branches como mostrado aqui, especialmente quando você tiver apenas uma ou duas pessoas trabalhando no mesmo projeto ou apenas uma pessoa trabalhando em todas as tarefas de uma história. No entanto, separar a branch de desenvolvimento da branch mestre é sempre uma boa prática. Isso pode ajudar a impedir que a branch de versão seja interrompida por atividades de desenvolvimento. Uma descrição mais completa do modelo de Git branch pode ser encontrada em [Um modelo de GIT branch bem-sucedido](http://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Para alternar para a branch na qual você deseja trabalhar, execute o seguinte comando em um comando shell (Windows ou Linux). 

    git checkout <branch name>

Alterar o *< nome da branch\>* para **mestre** leva você de volta para a branch **mestre**. Depois que você alternar para a branch de trabalho, você poderá iniciar o trabalho nesse item de trabalho, desenvolvendo os artefatos de código ou documentação necessários para concluir o item. 

Você também pode vincular um item de trabalho para uma branch existente. Na página **Detalhes** de um item de trabalho, em vez de clicar em **Criar uma nova branch**, clique em **+ Adicionar link**. Em seguida, selecione a branch a qual você deseja vincular o item de trabalho. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Você também pode criar uma nova branch nos comandos de Busca do Git. Se <nome base da branch\> estiver ausente, o <novo nome de branch\> será baseado na branch _mestre_. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Trabalhar em uma branch e confirmar as alterações 

Agora suponha que você faça algumas alterações na branch *ingestão de\_dados* para o item de trabalho, como a adição de um arquivo de R na branch no seu computador local. Você poderá confirmar o arquivo de R adicionado ao branch para este item de trabalho, desde que você esteja nessa branch no seu shell Git, usando os seguintes comandos do Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Criar uma solicitação de pull no VSTS 

Quando você estiver pronto após algumas confirmações e envios por push, para mesclar a branch atual em sua branch base, você poderá enviar uma **solicitação de pull** no servidor do VSTS. 

Vá para a página principal do seu projeto de equipe e clique em **CÓDIGO**. Selecione a branch a ser mesclada e o nome do repositório Git ao qual você deseja mesclar a branch. Em seguida, clique em **Solicitações de pull**, clique em **Nova solicitação de pull** para criar uma análise de solicitação de pull antes do trabalho na branch ser mesclado na branch de base.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Preencha algumas descrições sobre a solicitação de pull, adicione revisores e envie.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Examinar e mesclar 

Quando a solicitação de pull é criada, os revisores recebem uma notificação por email para examinar as solicitações de pull. Os revisores precisam verificar se as alterações estão funcionando ou não e testar as alterações com o solicitante, se possível. Com base na avaliação, os revisores podem aprovar ou rejeitar a solicitação de pull. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Após a análise, a branch de trabalho é mesclada na branch base clicando no botão **Concluir**. Você pode optar por excluir a branch de trabalho depois que ela for mesclada. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Confirme no canto superior esquerdo que a solicitação está marcada como **CONCLUÍDO**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Quando voltar para o repositório em **CÓDIGO**, você será informado de que alternou para a branch mestre.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Você também pode usar os seguintes comandos do Git para mesclar sua branch de trabalho em sua branch base e excluir a branch de trabalho após a mesclagem:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Próximas etapas

[Executar tarefas de ciência de dados](execute-data-science-tasks.md) mostra como usar os utilitários para executar várias tarefas de ciência de dados comuns como a exploração interativa de dados, a análise de dados, os relatórios e a criação de modelo.

Também são fornecidas instruções passo a passo que demonstram todas as etapas do processo para **cenários específicos**. Eles serão listados e vinculados a descrições em miniatura no artigo [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

