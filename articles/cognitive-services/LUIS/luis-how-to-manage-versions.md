---
title: Gerenciar versões em aplicativos LUIS no Azure | Microsoft Docs
description: Saiba como gerenciar versões em aplicativos LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: diberry
ms.openlocfilehash: 4941cf533f1b860ead07a416d5af6f62a1978305
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226587"
---
# <a name="manage-versions"></a>Gerenciar versões

Sempre que trabalhar no modelo, crie uma [versão](luis-concept-version.md) do aplicativo. 

## <a name="set-active-version"></a>Definir versão ativa
Para trabalhar com versões, abra o aplicativo selecionando o nome dele na página **Meus Aplicativos** e, em seguida, selecione **Configurações** na barra superior.

![Página de versões](./media/luis-how-to-manage-versions/settings.png)

A página **Configurações** permite que você defina configurações para todo o aplicativo, incluindo versões e colaboradores. 

## <a name="clone-a-version"></a>Clonar uma versão
1. Na página **Configurações**, depois das seções Configurações de Aplicativo e Colaboradores, localize a linha com a versão que você quer clonar. Selecione o botão de reticências (***...***) na extrema direita. 

    ![Propriedades da linha de versão](./media/luis-how-to-manage-versions/version-section.png)

2. Selecione **Clonar** da lista.

    ![Escolher propriedades da linha de versão](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. Na caixa de diálogo **Clonar versão**, digite um nome para a nova versão, como "0.2".

   ![Caixa de diálogo Clonar Versão](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > A ID da versão pode consistir apenas em caracteres, dígitos ou '.' e não pode ter mais de 10 caracteres.
 
 Uma nova versão com o nome especificado é criada e definida como a versão ativa.
 
  ![A versão é criada e adicionada à lista](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > Conforme mostrado na imagem anterior, uma versão publicada é associada a uma marca colorida, indicando o tipo de slot onde foi publicado: Produção (verde), Processo de preparo (vermelho) e ambos (preto). As datas de treinamento e publicação são exibidas para cada versão publicada.

## <a name="set-active-version"></a>Definir versão ativa
1. Na página **Configurações**, na lista **Versões**, selecione as reticências (***...***) na extrema direita.

2. Na lista pop-up, selecione **Definir como ativo**.

    ![Definir versão ativa](./media/luis-how-to-manage-versions/set-active-version.png)

    A versão ativa é realçada por uma cor azul clara, conforme mostrado na captura de tela a seguir:

    ![A versão ativa](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Importa versão
É possível importar uma versão de um arquivo JSON. Após importar uma versão, a nova versão se tornará a versão ativa.

**Para importar uma versão:**

1. Na página **Configurações**, selecione o botão **Importar nova versão**.

    ![Botão Importar](./media/luis-how-to-manage-versions/import-version.png)

2. Selecione **procurar** e escolha o arquivo JSON.

    ![Caixa de diálogo Importar versão](./media/luis-how-to-manage-versions/import-version-dialog.png)

É necessário somente definir uma ID de versão se a versão no arquivo JSON já existir no aplicativo.

## <a name="export-version"></a>Exportar versão
É possível exportar uma versão para um arquivo JSON.

**Para exportar uma versão:**

1. Na página **Configurações**, na lista **Versões**, selecione as reticências (***...***) na extrema direita.

2. Selecione **Exportar** na lista pop-up de ações e selecione onde quer salvar o arquivo.

## <a name="delete-a-version"></a>Excluir uma versão
É possível excluir versões, porém é necessário manter pelo menos uma versão do aplicativo. É possível excluir todas as versões, exceto a versão ativa. 

1. Na página **Configurações**, na lista **Versões**, selecione as reticências (***...***) na extrema direita.

2. Selecione **Excluir** na lista pop-up de ações e selecione onde quer salvar o arquivo.

    ![Confirmação de exclusão de versão](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Renomear uma versão
É possível renomear versões, contanto que o nome da versão não esteja em uso.  

1. Na página **Configurações**, na lista **Versões**, selecione as reticências (***...***) na extrema direita.

2. Selecione **Renomear** na lista pop-up de ações.

3. Insira o novo nome de versão e selecione **Concluído**.

    ![Confirmação de renomeação de versão](./media/luis-how-to-manage-versions/rename-popup.png) 
