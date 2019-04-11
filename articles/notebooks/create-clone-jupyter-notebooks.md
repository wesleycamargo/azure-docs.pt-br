---
title: Criar e clonar os notebooks Jupyter no Azure
description: Os projetos do Azure Notebooks gerencia uma coleção de notebooks e arquivos relacionados, que você pode cirar novo ou clone de outra fonte.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 0ee0c7162e26b875c74796b6d5379b414981e2d5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282314"
---
# <a name="create-and-clone-projects"></a>Criar e clonar projetos

O Azure Notebooks organiza os notebooks do Jupyter e arquivos relacionados nos grupos lógicos chamados *projetos*. Você cria um projeto primeiro como contêiner, em seguida, criar ou clonar um ou mais notebooks dentro de uma pasta junto com os outros arquivos do projeto. (este processo é demonstrado no [tutorial](tutorial-create-run-jupyter-notebook.md).)

Um projeto também mantém metadados e outras configurações que afetam o servidor no qual os notebooks são executados, incluindo etapas de instalação personalizadas e instalação de pacote. Para obter mais informações, consulte [Gerenciar e configurar projetos](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Use o painel Meus Projetos

Seu painel **Meus Projetos** em `https://notebooks.azure.com/<userID>/projects` é onde você exibe, gerencia e cria projetos:

[![Mpainel projetos y em blocos de anotações do Azure](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

O que você pode fazer no painel depende se você estiver conectado com a conta que possui a ID de usuário:

| Comando | Disponível para | DESCRIÇÃO |
| --- | --- | --- |
| **Executar** | Proprietário | Inicia o servidor de projeto e abre a pasta do projeto no Jupyter. (mais comumente, você primeiro navegar em uma pasta de projeto e iniciar um notebook a partir daí.) |
| **Baixar** | Qualquer pessoa | Baixa uma cópia do projeto selecionado como um arquivo ZIP. |
| **Compartilhar** | Qualquer pessoa | Exibe o pop-up de compartilhamento por meio do qual você pode obter uma URL para um projeto selecionado, compartilhar em mídias sociais, envie um email com a URL e obter código HTML ou Markdown para um crachá "notebook de inicialização" (consulte [obter uma notificação de lançamento](#obtain-a-launch-badge)) com a URL. |
| **Excluir** | Proprietário | Salva o projeto selecionado. Essa operação não pode ser desfeita. |
| **Terminal** | Proprietário | Inicia o servidor de projeto e, em seguida, abre uma nova janela com o terminal bash para esse servidor. |
| **+ Novo projeto** | Proprietário | Cria um novo projeto. Consulte [Criar um novo projeto](#create-a-new-project). |
| **Carregar o repositório do GitHub** | Proprietário | Importa o projeto do GitHub. [Importa o projeto do GitHub](#import-a-project-from-github). |
| **Clone** | Qualquer pessoa | Copia um projeto selecionado para sua própria conta. Solicita que você entre, se ainda não estiver. Consulte [Clonar um projeto](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Obtenha uma notificação de inicialização

Quando você usa o comando **Compartilhamento** de comando e seleciona a guia **Inserção**, você pode copiar o código HTML ou Markdown que cria uma notificação “iniciar bloco de notificações”:

![Iniciar a notificação do notebook](https://notebooks.azure.com/launch.png)

Se você não tiver um projeto do Microsoft Azure Notebooks, você pode criar um link que clona do GitHub diretamente usando os modelos a seguir, substituindo o nome de usuário apropriado e os nomes de repositório:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Criar um novo projeto

Quando você usa o comando **+ Novo projeto**, o Microsoft Azure Notebooks exibem uma janela pop-up **Criar Novo Projeto**. Nessa janela pop-up, insira as informações a seguir, em seguida, selecione **Criar**:

| Campo | DESCRIÇÃO |
| --- | --- |
| Nome do projeto | Um nome amigável para seu projeto que usa o Azure Notebooks para fins de exibição. Por exemplo, "Meu Bloco de anotações do projeto". |
| ID do projeto | Um identificador personalizado que se torna parte da URL que você usa para compartilhar um projeto (o formulário é `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Essa ID pode usar apenas letras, números e hifens, é limitada a 30 caracteres e não pode ser um [reservado da ID do projeto](#reserved-project-ids). Se você não tiver certeza sobre o que usar, uma convenção comum é usar uma versão em letras minúsculas do nome do seu projeto, na qual espaços são transformados em hifens, por exemplo “projeto-meu-notebook” (truncado se necessário para encaixar o limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com o link acesse o projeto. Ao criar um projeto privado, desmarque essa opção. |
| Inicializar este projeto com um arquivo LEIAME | Se definido, cria um padrão de arquivo *README.md* no projeto. Um arquivo *README.md* é onde você fornece documentação para seu projeto, se desejado. |

### <a name="reserved-project-ids"></a>IDs de projeto reservado

As seguintes palavras reservadas não podem ser usadas por si só, como IDs de projeto. Essas palavras reservadas no entanto, podem ser usados como parte do projeto mais IDs.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| about | conta | Administração | api | Blog | classroom |
| conteúdo | painel Transações da Web | Explorar | faq | ajuda | html |
| inicial | importar | biblioteca | gerenciamento | novo | notebook |
| Blocos de anotações | pdf | preview | preços | Perfil | pequisa |
| status | suporte | test | | | |

Se você tentar usar uma dessas palavras como uma ID do projeto, o **criar novo projeto** e **configurações do projeto** indicar pop-ups, "id da biblioteca é um identificador reservado".

Como uma ID de projeto também é parte da URL do projeto, o software de bloqueio do ad pode bloquear o uso de determinadas palavras-chave, como "anúncio". Nesses casos, usar uma palavra diferente na ID de projeto.

## <a name="import-a-project-from-github"></a>Importa o projeto do GitHub

Você pode importar facilmente um repositório do GitHub público inteiro como um projeto, incluindo todos os dados e arquivos *README.md*. Use **Carregar Repositório do GitHub** de comando, forneça os detalhes a seguir na janela pop-up e selecione **Importar**:

| Campo | DESCRIÇÃO |
| --- | --- |
| Repositório GitHub | O nome do repositório de origem em github.com. Por exemplo, para clonar os blocos de anotações do Jupyter para os serviços Cognitivos do Azure em [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), insira "Microsoft/cognitive-services-notebooks".  |
| Clonar recursivamente | Repositórios do GitHub podem conter vários repositórios filho. Defina essa opção se você deseja clonar o repositório pai e todos os seus filhos. Como é possível que um repositório tenha muitos filhos, deixe essa opção não criptografada, a menos que você saiba que ele precisa. |
| Nome do projeto | Um nome amigável para seu projeto que usa o Azure Notebooks para fins de exibição. |
| ID do projeto | Um identificador personalizado que se torna parte da URL que você usa para compartilhar um projeto (o formulário é `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Essa ID pode usar apenas letras, números e hifens, é limitada a 30 caracteres e não pode ser um [reservado da ID do projeto](#reserved-project-ids). Se você não tiver certeza sobre o que usar, uma convenção comum é usar uma versão em letras minúsculas do nome do seu projeto, na qual espaços são transformados em hifens, por exemplo “projeto-meu-notebook” (truncado se necessário para encaixar o limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com o link acesse o projeto. Ao criar um projeto privado, desmarque essa opção. |

Importar um repositório do GitHub também importa o seu histórico. Você pode usar comandos do Git padrão do terminal para confirmar as novas alterações, receber alterações do GitHub e assim por diante.

## <a name="clone-a-project"></a>Clonar um projeto

A clonagem cria uma cópia de um projeto existente em sua própria conta, onde você pode, em seguida, executar e modificar qualquer bloco de notas ou outro arquivo no projeto. Você também pode usar a clonagem para fazer cópias de seus próprios projetos nos quais você faz experimentos ou outro trabalho sem afetar o projeto original.

Pra clonar um projeto:

1. No painel **Meus Projetos**, clique no projeto desejado com o botão direito e selecione **Clonar** (atalho de teclado: c).

    ![Comando clonar no menu de contexto do projeto](media/clone-command.png)

1. Na janela pop-up **Clonar projeto**, insira um nome e uma ID para o clone e especifique se o clone é público. Essas configurações são os mesmas para um [novo projeto](#create-a-new-project).

    ![Clonar janela pop-up do projeto](media/clone-project.png)

1. Depois de selecionar o botão **Clone**, o Azure Notebooks navega diretamente para a cópia.

## <a name="next-steps"></a>Próximos passos

- [Explore os notebooks de exemplo](azure-notebooks-samples.md)
- [Como: Configurar e gerenciar projetos](configure-manage-azure-notebooks-projects.md)
- [Como: Instalar pacotes de dentro de um notebook](install-packages-jupyter-notebook.md)
- [Como: Exibir uma apresentação de slides](present-jupyter-notebooks-slideshow.md)
- [Como: Trabalhar com arquivos de dados](work-with-project-data-files.md)
- [Como: Acessar recursos de dados](access-data-resources-jupyter-notebooks.md)
- [Como: Usar os serviços do Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
