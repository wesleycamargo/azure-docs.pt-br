---
title: Copie os arquivos novos e alterados por LastModifiedDate com o Azure Data Factory | Microsoft Docs
description: Saiba como usar um modelo de solução para copiar os arquivos novos e alterados por LastModifiedDate com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2019
ms.locfileid: "58111932"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copie os arquivos novos e alterados por LastModifiedDate com o Azure Data Factory

Este artigo descreve um modelo de solução que você pode usar para copiar os arquivos novos e alterados somente por LastModifiedDate de um armazenamento baseado em arquivo para um armazenamento de destino. 

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Esse modelo seleciona primeiro os arquivos novos e alterados somente por seus atributos **LastModifiedDate**e, em seguida, copia os arquivos selecionados do repositório de fonte de dados para o armazenamento de dados de destino.

O modelo contém uma atividade:
- **Cópia** para copiar os arquivos novos e alterados somente por LastModifiedDate de um armazenamento de arquivos para um armazenamento de destino.

O modelo define quatro parâmetros:
-  *FolderPath_Source* é o caminho da pasta onde você pode ler os arquivos do armazenamento de origem. Você precisa substituir o valor padrão com seu próprio caminho de pasta.
-  *FolderPath_Destination* é o caminho da pasta onde você deseja copiar arquivos para o armazenamento de destino. Você precisa substituir o valor padrão com seu próprio caminho de pasta.
-  *LastModified_From* é usado para selecionar os arquivos cujo atributo LastModifiedDate é após ou igual a esse valor de data e hora.  Para selecionar os novos arquivos, que não foi copiado última vez, esse valor de data e hora pode ser a hora quando o pipeline foi disparado última vez. Você pode substituir o valor padrão ' 2019-02-01T00:00:00Z' para seu LastModifiedDate esperado no fuso horário UTC. 
-  *LastModified_To* é usado para selecionar os arquivos cujo atributo LastModifiedDate é antes desse valor de data e hora. Para selecionar os novos arquivos, que não foi copiado última vez, esse valor de data e hora pode ser a hora atual.  Você pode substituir o valor padrão ' 2019-02-01T00:00:00Z' para seu LastModifiedDate esperado no fuso horário UTC. 

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para o modelo **copiar novos arquivos apenas pela LastModifiedDate**. Criar uma **New** conexão para o armazenamento do armazenamento de origem. O repositório de armazenamento de origem é onde você deseja copiar os arquivos de.

    ![Criar uma nova conexão para a origem](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Primeiro, selecione o armazenamento **tipo**. Depois que o armazenamento de entrada **nome da conta** e o **chave de conta**. Por fim, selecione **concluir**.

    ![Uma cadeia de caracteres de conexão de entrada](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Criar uma **New** conexão para seu armazenamento de destino. O armazenamento de destino é onde você deseja copiar os arquivos. Você também precisará inserir as informações de conexão do repositório de destino de dados semelhante de como você na etapa 2.

    ![Criar uma nova conexão para o destino](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Selecione **Use este modelo**.

    ![Usar este modelo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Você verá o pipeline disponível no painel, conforme mostrado no exemplo a seguir:

    ![Mostrar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Selecione **Debug**, escreva o valor para o **parâmetros** e selecione **concluir**.  Na imagem abaixo, podemos definir os parâmetros da seguinte maneira.
   - **FolderPath_Source** = **/source/**
   - **FolderPath_Destination** = **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     O exemplo é que indica os arquivos que foram modificados pela última vez em que o período de tempo entre *2019-02-01T00:00:00Z* e *2019-03-01T00:00:00Z* serão copiados de uma pasta */source/*  para uma pasta */destination/*.  Você pode substituí-las com seus próprios parâmetros.
    
     ![Executar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Revise o resultado. Você verá apenas os arquivos modificado pela última vez configurado dentro do período de tempo foi copiado para o armazenamento de destino.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Agora você pode adicionar um gatilho do windows em cascata para automatizar esse pipeline, para que o pipeline sempre possa copiar os arquivos novos e alterados somente por LastModifiedDate periodicamente.  Selecione **adicionar disparador**e selecione **novo/editar**.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. Na janela **Adicionar Gatilhos**, selecione **+ Novo**.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Selecione **janela em cascata** para o tipo de gatilho, defina **cada 15 minutos** como a recorrência (você pode alterar para qualquer intervalo de tempo) e, em seguida, selecione **próxima**.

    ![Criar gatilho](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Gravar o valor para o **parâmetros de execução de gatilho** como a seguir e selecione **concluir**.
    - **FolderPath_Source** = **/source/**.  Você pode substituir com sua pasta no armazenamento de dados de origem.
    - **FolderPath_Destination** = **/destination/**.  Você pode substituir com sua pasta no repositório de dados de destino.
    - **LastModified_From** =  **@trigger().outputs.windowStartTime**.  É uma variável de sistema do gatilho determinar a hora quando o pipeline foi disparado última vez.
    - **LastModified_To** = **@trigger().outputs.windowEndTime**.  É uma variável de sistema do gatilho determinar a hora quando o pipeline é disparado neste momento.
    
    ![Parâmetros de entrada](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Selecione **Publicar Tudo**.
    
    ![Publicar Tudo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Crie novos arquivos em sua pasta de origem do armazenamento de dados de origem.  Você agora está aguardando o pipeline seja disparado automaticamente e apenas os novos arquivos serão copiados para o armazenamento de destino.

14. Selecione **monitoramento** guia no painel de navegação à esquerda e aguarde cerca de 15 minutos, se a recorrência do gatilho tiver sido definida para cada 15 minutos. 

    ![Selecione o monitoramento](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Revise o resultado. Você verá o pipeline será disparado automaticamente a cada 15 minutos, e somente os arquivos novos ou alterados de armazenamento de origem serão copiados para o armazenamento de destino em cada execução de pipeline.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)