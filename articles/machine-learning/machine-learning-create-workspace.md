---
title: Criar um espaço de trabalho de Aprendizado de Máquina | Microsoft Docs
description: Como criar um espaço de trabalho para o Estúdio de Aprendizado de Máquina do Microsoft Azure
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2016
ms.author: garye;bradsev;ahgyger

---
# Criar e compartilhar um espaço de trabalho de Aprendizado de Máquina do Azure
Esse menu vincula-se aos tópicos que descrevem como configurar os vários ambientes de ciência de dados usados pelo CAPS (Processo Cortana Analytics).

[!INCLUDE [configuração do ambiente de ciência de dados](../../includes/cap-setup-environments.md)]

Para usar o Estúdio de Aprendizado de Máquina do Azure, você precisa ter um espaço de trabalho de do Aprendizado de Máquina. Esse espaço de trabalho contém as ferramentas necessárias para criar, gerenciar e publicar testes.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Para criar um espaço de trabalho
1. Entre no [portal clássico do Microsoft Azure].

> [!NOTE]
> Para entrar, você precisa ser um administrador de assinatura do Azure. Ser o proprietário de um espaço de trabalho de aprendizado de máquina não concede acesso ao [portal clássico do Microsoft Azure]. Confira [Privilégios do administrador de assinatura do Azure e proprietário do espaço de trabalho](#subscriptionvsworkspace) para obter mais detalhes.
> 
> 

1. No painel de serviços do Microsoft Azure, clique em **APRENDIZADO DE MÁQUINA**.
   
    ![Serviço de Aprendizado de Máquina][1]
2. Clique em **+NOVO** na parte inferior da janela.
3. Clique em **SERVIÇOS DE DADOS**, depois em **APRENDIZADO DE MÁQUINA** e, em seguida, **CRIAÇÃO RÁPIDA**.
   
    ![Criação Rápida do novo espaço de trabalho][3]
4. Insira um **NOME DE ESPAÇO DE TRABALHO** para seu espaço de trabalho.
5. Especificar o **LOCAL** do Azure, depois insira uma **CONTA DE ARMAZENAMENTO** do Azure existente ou selecione **Criar uma nova conta de armazenamento** para criar uma nova.
6. Clique em **CRIAR UM ESPAÇO DE TRABALHO AM**.

Após seu espaço de trabalho do Aprendizado de Máquina ser criado, você o verá listado na página de **aprendizado de máquina**.

## Compartilhar um espaço de trabalho do Aprendizado de Máquina do Azure
Depois de criar um espaço de trabalho de Aprendizado de Máquina, você pode convidar usuários para acessar seu espaço de trabalho e de compartilhar acesso ao seu espaço de trabalho e todos os seus experimentos. Damos suporte duas funções de usuários:

* **Usuário**: um usuário do espaço de trabalho pode criar, abrir, modificar e excluir conjuntos de dados, testes e serviços Web no espaço de trabalho.
* **Proprietário**: um proprietário pode convidar, remover e listar usuários com acesso ao espaço de trabalho, além do que o usuário pode fazer. Ele também tem acesso aos Notebooks.

### Para compartilhar um espaço de trabalho
1. Entre no [Estúdio de Aprendizado de Máquina]
2. No painel do Estúdio de Aprendizado de Máquina, clique em **CONFIGURAÇÕES**
3. Clique em **USUÁRIOS**.
4. Clique em **CONVIDAR MAIS USUÁRIOS**
   
    ![Convidar mais usuários][4]
5. Insira um ou mais endereços de email. O usuário precisa apenas de uma conta da Microsoft válida (por exemplo, name@outlook.com) ou uma conta organizacional (do Azure Active Directory).
6. Clique no botão de seleção.

Cada usuário que você adicionar receberá um email com instruções para fazer logon no espaço de trabalho compartilhado.

Para saber mais sobre como gerenciar seu espaço de trabalho, consulte [Gerenciar um espaço de trabalho do Aprendizado de Máquina do Azure]. Se você encontrar um problema ao criar seu espaço de trabalho, consulte [Guia de solução de problemas: criar e conectar-se a um espaço de trabalho do Aprendizado de Máquina].

## <a name="subscriptionvsworkspace"></a>Privilégios do administrador de assinatura do Azure e proprietário do espaço de trabalho
Veja abaixo uma tabela que esclarece a diferença entre um administrador de assinatura do Azure e o proprietário de um espaço de trabalho.

| Ações | Administrador de assinatura do Azure | Proprietário do espaço de trabalho |
| --- |:---:|:---:|
| Acessar o [Portal Clássico do Microsoft Azure] |Sim |Não |
| Criar um novo espaço de trabalho |Sim |Não |
| Excluir um espaço de trabalho |Sim |Não |
| Adicionar ponto de extremidade a um serviço Web |Sim |Não |
| Excluir um ponto de extremidade de um serviço Web |Sim |Não |
| Alterar a simultaneidade de um serviço Web |Sim |Não |
| Acessar o [Estúdio de Aprendizado de Máquina] |Não * |Sim |

> [!NOTE]
> *Um administrador de assinatura do Azure é adicionado automaticamente ao espaço de trabalho que ele cria como proprietário do espaço de trabalho. No entanto, simplesmente ser um administrador de assinatura do Azure não concede acesso a qualquer espaço de trabalho nessa assinatura.
> 
> 

<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw4.png
[4]: media/machine-learning-create-workspace/cw5.png


<!--Link references-->
[Gerenciar um espaço de trabalho do Aprendizado de Máquina do Azure]: machine-learning-manage-workspace.md
[Guia de solução de problemas: criar e conectar-se a um espaço de trabalho do Aprendizado de Máquina]: machine-learning-troubleshooting-creating-ml-workspace.md
[Estúdio de Aprendizado de Máquina]: https://studio.azureml.net/
[portal clássico do Microsoft Azure]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0914_2016-->