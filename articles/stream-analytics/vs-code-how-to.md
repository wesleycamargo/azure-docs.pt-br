---
title: Explorar o Azure Stream Analytics com o código do Visual Studio (versão prévia)
description: Este artigo mostra como exportar um trabalho do Azure Stream Analytics para um projeto local, listar trabalhos e entidades de trabalho do modo de exibição e configurar um pipeline de CI/CD para o trabalho do Stream Analytics.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079204"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explorar o Azure Stream Analytics com o código do Visual Studio (versão prévia)

O Azure Stream Analytics para a extensão do Visual Studio Code fornece aos desenvolvedores uma experiência leve para gerenciar seus trabalhos do Stream Analytics. Com a extensão do Azure Stream Analytics, você pode:

- [Criar](quick-create-vs-code.md), iniciar e parar trabalhos
- Exportar os trabalhos existentes em um projeto local
- Executar consultas localmente
- Configurar um pipeline de CI/CD

## <a name="export-a-job-to-a-local-project"></a>Exportar um trabalho para um projeto local

Para exportar um trabalho para um projeto local, localize o trabalho que você deseja exportar na **Stream Analytics Explorer** no Visual Studio code. Em seguida, selecione uma pasta para o seu projeto. O projeto é exportado para a pasta selecionada, e você pode continuar a gerenciar o trabalho do Visual Studio Code. Para obter mais informações sobre como usar o Visual Studio Code para gerenciar trabalhos do Stream Analytics, consulte o Visual Studio Code [quickstart](quick-create-vs-code.md).

![Exportar trabalho ASA no Visual Studio Code](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>Executar consultas localmente

Você pode usar a extensão do Azure Stream Analytics para Visual Studio Code para testar seus trabalhos do Stream Analytics localmente com os dados de exemplo.

1. Depois de criar o trabalho do Stream Analytics, use **Ctrl + Shift + P** para abrir a paleta de comando. Em seguida, digite e selecione **ASA: Adicionar entrada**.

    ![Adicionar entrada do ASA no Visual Studio code](./media/vs-code-how-to/add-input.png)

2. Selecione **entrada Local**.

    ![Adicionar entrada de local de ASA no Visual Studio code](./media/vs-code-how-to/add-local-input.png)

3. Selecione **+ nova entrada Local**.

    ![Adicionar um local de ASA nova entrada no Visual Studio code](./media/vs-code-how-to/add-new-local-input.png)

4. Insira o mesmo alias de entrada que você usou em sua consulta.

    ![Adicionar um novo alias de entrada de ASA local](./media/vs-code-how-to/new-local-input-alias.png)

5. No **LocalInput_DefaultLocalStream.json** de arquivos, insira o caminho do arquivo onde se encontra o arquivo de dados local.

    ![Insira o caminho do arquivo local no Visual Studio](./media/vs-code-how-to/local-file-path.png)

6. Retornar para o seu editor de consulta e selecione **executados localmente**.

    ![Selecione Executar localmente no editor de consultas](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>Configurar um pipeline de CI/CD

Você pode habilitar a integração contínua e implantação para trabalhos do Azure Stream Analytics usando o **cicd asa ferramentas** pacote NPM. O pacote NPM fornece as ferramentas para a implantação automática de projetos do Visual Studio Code do Stream Analytics. Ele pode ser usado no Windows, macOS e Linux sem instalar o Visual Studio Code.

Quando você tiver [baixou o pacote](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar), use o seguinte comando para gerar os modelos do Azure Resource Manager. Se o **outputPath** não for especificado, os modelos serão colocados na **Deploy** pasta sob o projeto **bin** pasta.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>Próximas etapas

* [Criar um trabalho de nuvem do Azure Stream Analytics no Visual Studio Code (visualização)](quick-create-vs-code.md)