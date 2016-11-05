---
title: Assistente de Cópia do Data Factory | Microsoft Docs
description: Saiba mais sobre como usar o Assistente de Cópia do Data Factory para copiar dados de fontes de dados com suporte para coletores.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2016
ms.author: spelluru

---
# Assistente de Cópia do Data Factory
O Assistente de Cópia do Azure Data Factory permite que você crie um pipeline para copiar dados de fontes compatíveis para destinos sem gravar as definições de JSON para serviços vinculados, conjuntos de dados e pipelines. Para iniciar o Assistente de Cópia, clique no bloco **Copiar dados** na home page da sua fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## Recursos
### Um assistente intuitivo e simples para copiar dados
Este assistente permite mover dados facilmente de uma origem para um destino em minutos com estas etapas simples:

1. Selecione a origem.
2. Selecione o destino.
3. Defina as configurações.

![Selecione uma fonte de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

### Exploração de dados avançados e mapeamentos de esquema
Você pode procurar tabelas/pastas, visualizar dados, mapear um esquema, validar expressões e executar transformações de dados simples no assistente.

**Procurar tabelas/pastas** ![Procurar tabelas e pastas](./media/data-factory-copy-wizard/browse-tables-folders.png)

### Experiência escalonável para tipos de dados e objetos diversificados
A experiência foi projetada tendo em mente Big Data desde o início. É simples e eficiente de criar pipelines de Data Factory que movem centenas de pastas, arquivos ou tabelas.

**Visualizar dados** ![Configurações de formato de arquivo](./media/data-factory-copy-wizard/file-format-settings.png)

**Mapear um esquema** ![Mapeamento de esquema](./media/data-factory-copy-wizard/schema-mapping.png)

**Executar transformações simples** ![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### Experiência escalonável para tipos de dados e objetos diversificados
Usando o Assistente para cópia, você pode mover de modo simples e com eficiência centenas de pastas, arquivos ou tabelas.

![Selecione as tabelas das quais copiar dados](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

### Opções de agendamento mais avançadas
Você pode executar a operação de cópia uma vez ou segundo um agendamento (por hora, por dia e assim por diante). Ambas essas opções podem ser usadas para a variedade de conectores em cópias de área de trabalho local, da nuvem e locais.

Uma operação de cópia única permite, uma única vez, a movimentação de dados de uma origem para um destino. Ela se aplica aos dados de qualquer tamanho e em qualquer formato com suporte. A cópia programada permite copiar dados com uma recorrência prescrita. Você pode aproveitar as configurações avançadas (como repetição, tempo limite, alertas etc.) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## Variáveis no caminho da pasta de blob do Azure
Você pode usar variáveis no caminho da pasta para copiar dados de uma pasta que é determinada em tempo de execução com base nas [variáveis do sistema WindowStart](data-factory-functions-variables.md#data-factory-system-variables). As variáveis com suporte são: **{ano}**, **{mês}**, **{dia}**, **{hora}**, **{minuto}** e **{personalizado}**. Exemplo: pastadeentrada/{ano}/{mês}/{dia}.

Suponha que você tenha pastas de entrada no seguinte formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Clique no botão **Procurar** para **Arquivo ou pasta**, navegue até uma dessas pastas (por exemplo, 2016 -> 03 -> 01 -> 02) e clique em **Escolher**. Você verá **2016/03/01/02** na caixa de texto. Agora, substitua **2016** por **{ano}**, **03** por **{mês}**, **01** por **{dia}**, **02** por **{hora}** e pressione Tab. Você deverá ver listas suspensas para escolher o formato dessas quatro variáveis:

![Usando variáveis de sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)

Conforme mostrado na captura de tela abaixo, você também pode usar uma variável **personalizada**, além de usar qualquer [cadeia de caracteres com formato compatível](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com essa estrutura, use primeiro o botão **Procurar**. Em seguida, substitua um valor com **{personalizado}** e pressione Tab para ver a caixa de texto em que você poderá digitar a cadeia de caracteres de formato.

![Usando variáveis personalizadas](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## Próximas etapas
Para ver um passo a passo rápido sobre como usar o Assistente de Cópia do Data Factory para criar um pipeline com uma Atividade de Cópia, confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).

<!---HONumber=AcomDC_0824_2016-->