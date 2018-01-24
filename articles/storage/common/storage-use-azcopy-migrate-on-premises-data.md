---
title: Migrar dados locais para o Armazenamento do Azure usando o AzCopy | Microsoft Docs
description: "Use o AzCopy para migrar dados para ou de conteúdo de blob, tabela e arquivo. Migre dados com facilidade do armazenamento local para o Armazenamento do Azure."
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: v-ruogun
ms.openlocfilehash: 3dbfb935ac0b134e127a5dccb7bc76716c688e8a
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Migrar dados locais para o armazenamento em nuvem usando o AzCopy

O AzCopy é uma ferramenta de linha de comando para copiar dados para ou do Armazenamento de Blobs do Azure, Arquivos do Azure e Armazenamento de Tabelas do Azure, usando comandos simples. Os comandos são projetados para desempenho ideal. Você pode copiar dados entre um sistema de arquivos e uma conta de armazenamento, ou entre contas de armazenamento.  

Há duas versões do AzCopy que podem ser baixadas:

* O [AzCopy no Linux](storage-use-azcopy.md) é criado com o .NET Core Framework. Ele se destina a plataformas Linux, oferecendo opções de linha de comando de estilo POSIX. 
* O [AzCopy no Windows](../storage-use-azcopy.md) é criado com o .NET Framework. Ele oferece opções de linha de comando estilo Windows. 
 
Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento. 
> * Use o AzCopy para carregar todos os seus dados.
> * Modifique os dados para fins de teste.
> * Crie uma tarefa agendada ou trabalho de Cron para identificar novos arquivos a serem carregados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, baixe a versão mais recente do AzCopy em [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) ou [Windows](http://aka.ms/downloadazcopy). 

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>Se você deseja ser capaz de baixar blobs de uma região secundária para o armazenamento local e vice-versa, defina **Replicação** para **Armazenamento com redundância geográfica para acesso de leitura**. Selecionar essa opção cria uma conta de [armazenamento com redundância geográfica](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage). 
>
>

## <a name="create-a-container"></a>Criar um contêiner

Os blobs são sempre carregados em um contêiner. Você pode usar contêineres para organizar grupos de blobs da mesma forma que organiza os arquivos em pastas no seu computador. 

Execute estas etapas para criar um contêiner:

1. Selecione o botão **Contas de armazenamento** na página principal e, em seguida, selecione a conta de armazenamento que você criou.
2. Selecione **Blobs** em **Serviços** e, em seguida, selecione **Contêiner**. 

   ![Criar um contêiner](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Nomes de contêiner devem começar com uma letra ou número. Eles podem conter apenas letras, números e o caractere de hífen (-). Para obter mais regras sobre como nomear blobs e contêineres, confira [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>Carregar todos os arquivos contidos em uma pasta no Armazenamento de Blobs

Você pode usar AzCopy para carregar todos os arquivos em uma pasta para Armazenamento de Blobs em [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) ou [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Para carregar todos os blobs em uma pasta, digite o seguinte comando do AzCopy:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S
---

Substitua `<key>` e `key` pela sua chave de conta. No Portal do Azure, você pode recuperar a chave de conta selecionando **Chaves de acesso** em **Configurações** em sua conta de armazenamento. Selecione uma chave e, em seguida, cole-a no comando AzCopy. Se o contêiner de destino especificado não existir, o AzCopy o criará e carregará o arquivo nele. Atualize o caminho de origem para o diretório de dados e substituir **myaccount** na URL de destino com o nome da sua conta de armazenamento.

Para carregar o conteúdo do diretório especificado recursivamente do Armazenamento de Blobs, especifique a opção `--recursive` (Linux) ou `/S` (Windows). Quando você executa o AzCopy com uma dessas opções, todas as subpastas e os respectivos arquivos são carregados também.

## <a name="upload-modified-files-to-blob-storage"></a>Carregar os arquivos modificados no Armazenamento de Blobs
Você pode usar AzCopy para [carregar arquivos](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) com base na hora da sua última modificação. Para testar isso, modifique ou crie novos arquivos em seu diretório de origem para fins de teste. Para carregar apenas arquivos novos ou atualizados, adicione o parâmetro `--exclude-older` (Linux) ou `/XO` (Windows) ao comando AzCopy.

Se você quiser copiar apenas os recursos de origem que não existem no destino, especifique ambos os parâmetros, `--exclude-older` e `--exclude-newer` (Linux), ou `/XO` e `/XN` (Windows), no comando AzCopy. O AzCopy carrega apenas os dados atualizados, com base em seu carimbo de data/hora.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>Criar uma tarefa agendada ou trabalho de Cron 
Você pode criar uma tarefa agendada ou trabalho de Cron que execute um script de comando AzCopy. O script identifica e carrega novos dados locais para o armazenamento em nuvem em um intervalo de tempo específico. 

Copie o comando AzCopy para um editor de texto. Atualize os valores de parâmetro do comando AzCopy para os valores apropriados. Salve o arquivo como `script.sh` (Linux) ou `script.bat` (Windows) para AzCopy. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

O AzCopy é executado com a opção detalhada `--verbose` (Linux) ou `/V` (Windows). A saída é redirecionada para um arquivo de log. 

Neste tutorial, [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) é usado para criar uma tarefa agendada no Windows. O comando [Crontab](http://crontab.org/) é usado para criar um trabalho Cron no Linux. 
 **Schtasks** permite que um administrador crie, exclua, consulte, altere, execute e finalize as tarefas agendadas em um computador local ou remoto. **Cron** permite que os usuários do Linux e Unix executem comandos ou scripts em uma data e hora especificadas usando [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Para criar um trabalho Cron no Linux, digite o seguinte comando em um terminal: 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

Especificar a expressão Cron `*/5 * * * * ` no comando indica que o script de shell `script.sh` deve ser executado a cada cinco minutos. Você pode agendar o script para ser executado em um momento específico diariamente, mensalmente ou anualmente. Para saber mais sobre como definir a data e hora para a execução do trabalho, consulte [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression). 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Para criar uma tarefa agendada no Windows, digite o seguinte comando no prompt de comando ou no PowerShell:

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

O comando usa:
- O parâmetro `/SC` para especificar um agendamento a ser executado a cada um minuto.
- O parâmetro `/MO` para especificar um intervalo de cinco minutos.
- O parâmetro `/TN` para especificar o nome da tarefa.
- O parâmetro `/TR` para especificar o caminho para o arquivo `script.bat`. 

Para saber mais sobre como criar uma tarefa agendada no Windows, consulte [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---
 
Para validar que o trabalho cron/tarefa agendada seja executada corretamente, crie novos arquivos no seu diretório `myfolder`. Aguarde cinco minutos para confirmar que os novos arquivos foram carregados para sua conta de armazenamento. Vá para o diretório de log para exibir os logs de saída da tarefa agendada ou do trabalho Cron. 

Para saber mais sobre as maneiras de mover dados locais para o Armazenamento do Azure e vice-versa, consulte [Mover dados para e do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o Armazenamento do Azure e o AzCopy, consulte os seguintes recursos:

* [Introdução ao Armazenamento do Azure](../storage-introduction.md)
* [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) 
* [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md) 
* [Criar uma conta de armazenamento](../storage-create-storage-account.md)
* [Gerenciar blobs com o Gerenciador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

