---
title: "Implantando Grandes Implantações"
description: "Saiba como implantar implantações grandes do Azure usando o Kit de Ferramentas do Azure para Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 5e18bace-5df0-4af8-ad86-6151ea8bd823
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: d6b2b28a7e01d5a70cd08f4cd2d8cccff7d918cf
ms.lasthandoff: 01/05/2017


---
# <a name="deploying-large-deployments"></a>Implantando Grandes Implantações
Se sua implantação for muito grande para caber na pasta approot padrão, você pode usar um recurso de armazenamento local como a pasta raiz de implantação para seu JDK e servidor de aplicativos.

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>Para usar um recurso de armazenamento local como pasta raiz de implantação para grandes implantações
1. Crie um recurso de armazenamento local novo. O nome do recurso não importa. Os recursos de armazenamento são definidos no nível de função. É a maneira mais rápida para acessar a caixa de diálogo de configuração de armazenamento local, da qual você pode criar um novo recurso de armazenamento local usando as etapas a seguir: Clique com o botão direito na função da exibição do **Explorador de Projeto** (expanda o nó do projeto do Azure se não vir a função), clique em **Azure**, e, em seguida, clique em **Armazenamento Local**. Dentro da caixa de diálogo **Armazenamento Local**, clique em **Adicionar** para criar um novo recurso de armazenamento local.

2. Defina o tamanho desejado para pelo menos 2048 MB (algo menor pode causar os mesmos problemas de tamanho de arquivo como haveria na approot).

3. A caixa **Limpar o conteúdo quando a instância de função é reciclada** deve estar marcada; isso ajudará a evitar que a lógica de inicialização da implantação seja executada em conflito com os arquivos pré-existentes no recurso quando a instância de função é reciclada.

4. O valor de **Variável de ambiente armazenando o caminho do diretório do recurso após a implantação** deve estar definido como a cadeia de caracteres **DEPLOYROOT**. A caixa de diálogo de recurso de armazenamento local será semelhante à seguinte.

   ![][ic667943]

Como alternativa, se você usar **DEPLOYROOT** como o *nome* de seu recurso local e não alterar o nome da variável de ambiente gerado automaticamente (que, nesse caso, será definido como **DEPLOYROOT_PATH**), isso funcionaria para seu aplicativo.

Informações adicionais sobre como criar um recurso de armazenamento local podem ser encontradas em [Propriedades de armazenamento local][Local storage properties].

## <a name="see-also"></a>Consulte também
[Kit de Ferramentas do Azure para Eclipse][Azure Toolkit for Eclipse]

[Criar um aplicativo Hello World para Azure no Eclipse][Creating a Hello World Application for Azure in Eclipse]

[Instalar o Kit de Ferramentas do Azure para Eclipse][Installing the Azure Toolkit for Eclipse] 

Para saber mais sobre como usar o Azure com o Java, confira o [Centro de Desenvolvedores Java do Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Local storage properties]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

