<properties
pageTitle="Usar dados do banco de dados do SQL Server local no Aprendizado de Máquina | Azure"
description="Use dados do banco de dados do SQL Server local para executar análises avançadas com o Aprendizado de Máquina do Azure."
services="machine-learning"
documentationCenter=""
authors="garyericson"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="05/25/2016"
ms.author="garye;krishnan"/>

# Executar análises avançadas com o Aprendizado de Máquina do Azure usando os dados de um banco de dados do SQL Server local


Muitas vezes, empresas que trabalham com dados locais gostariam de aproveitar a escala e a agilidade da nuvem para sua cargas de trabalho de aprendizado de máquina. Mas elas não querem interromper seus fluxos de trabalho e processos de negócios atuais, movendo seus dados locais para a nuvem. O Aprendizado de Máquina do Azure agora dá suporte à leitura de dados do banco de dados do SQL Server local e, em seguida, ao treinamento e à pontuação de um modelo com esses dados. Você não precisa mais copiar manualmente e sincronizar os dados entre a nuvem e o servidor local. Em vez disso, o módulo **Importar Dados** no Estúdio do Aprendizado de Máquina do Azure agora pode ler diretamente do banco de dados do SQL Server local para seus trabalhos de treinamento e pontuação.

Este artigo fornece uma visão geral de como ingressar dados do SQL Server local no Aprendizado de Máquina do Azure. Ele pressupõe que você esteja familiarizado com os conceitos de Aprendizado de Máquina do Azure, como espaços de trabalho, módulos, conjuntos de dados, experiências, *etc.*

> [AZURE.NOTE] Esse recurso não está disponível para os espaços de trabalho gratuitos. Para obter mais informações sobre preços e camadas do Aprendizado de Máquina, consulte [Preços do Aprendizado de Máquina do Azure](https://azure.microsoft.com/pricing/details/machine-learning/).

<!-- --> 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Instale o Gateway de Gerenciamento de Dados da Microsoft

Para acessar um banco de dados do SQL Server local no Aprendizado de Máquina do Azure, você precisa baixar e instalar o Gateway de Gerenciamento de Dados da Microsoft. Quando você configura a conexão de gateway no Estúdio de Aprendizado de Máquina, você terá a oportunidade de baixar e instalar o gateway usando o diálogo **Baixar e registrar o gateway de dados** descrito abaixo.

Também é possível instalar o Gateway de Gerenciamento de Dados antecipadamente baixando e executando um pacote de instalação MSI do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Escolha a versão mais recente, selecionando 32 bits ou 64 bits, conforme apropriado para seu computador. O MSI também pode ser usado para atualizar o Gateway de Gerenciamento de Dados existente para a versão mais recente, com todas as configurações preservadas.

O gateway tem os seguintes pré-requisitos:

- As versões de sistema operacional com suporte são Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
- A configuração recomendada para o computador do gateway é no mínimo 2 GHz, 4 núcleos, 8 GB de RAM e 80 GB de disco.
- Se o computador host hibernar, o gateway não poderá responder a solicitações de dados. Portanto, configure um plano de energia adequado no computador antes de instalar o gateway. A instalação do gateway exibirá uma mensagem se o computador estiver configurado para hibernar.
- Como a atividade de cópia ocorre em uma frequência específica, o uso de recursos (CPU, memória) no computador também segue o mesmo padrão com tempos ociosos e de pico. A utilização de recursos também depende muito da quantidade de dados sendo movida. Quando vários trabalhos de cópia estiverem em andamento, você observará o uso do recurso aumentar durante horários de pico. Embora a configuração mínima listada acima seja tecnicamente suficiente, recomenda-se ter uma configuração com mais recursos do que as configurações mínimas, dependendo da sua carga específica par movimentação de dados.

Você deve considerar o seguinte ao configurar e usar um Gateway de Gerenciamento de Dados:

- Você pode instalar apenas uma instância do Gateway de Gerenciamento de Dados em um único computador.
- Você pode usar um único gateway para várias fontes de dados locais.
- Você pode conectar diversos gateways em diferentes computadores à mesma fonte de dados local.
- Você configura um gateway para apenas um espaço de trabalho por vez. Gateways não podem ser compartilhados entre espaços de trabalho no momento.
- Você pode configurar vários gateways para um único espaço de trabalho. Por exemplo, você talvez queira usar um gateway que está conectado às suas fontes de dados de teste durante o desenvolvimento e um gateway de produção quando você estiver pronto para colocar em operação.
- O gateway não precisa estar no mesmo computador que a fonte de dados, mas ficar mais próximo à fonte de dados reduz o tempo para o gateway se conectar à fonte de dados. Recomendamos que você instale o gateway em um computador diferente daquele que hospeda a fonte de dados local para que o gateway e a donte de dados não disputem os recursos.
- Se você já tiver um gateway instalado no computador atendendo cenários do Power BI ou Azure Data Factory, instale um gateway separado para o Aprendizado de Máquina do Azure em outro computador. 

    > [AZURE.NOTE] Você não pode executar o Gateway de Gerenciamento de Dados e o Gateway do Power BI no mesmo computador.

- Você precisará usar o Gateway de Gerenciamento de Dados para o Aprendizado de Máquina do Azure, mesmo se você estiver usando a Rota Expressa do Azure para outros dados. Você deve tratar a fonte de dados como uma fonte de dados local (que está atrás de um firewall) mesmo quando usar a Rota Expressa e o Gateway de Gerenciamento de Dados para estabelecer a conectividade entre o Aprendizado de Máquina e a fonte de dados.

Você pode encontrar informações detalhadas sobre pré-requisitos de instalação, etapas de instalação e dicas de solução de problemas nas seções de início do artigo [Mover dados entre fontes locais e na nuvem com o Gateway de Gerenciamento de Dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

## <span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Ingressar dados do banco de dados do SQL Server local no Aprendizado de Máquina do Azure


Nesse passo a passo, você vai configurar um Gateway de Gerenciamento de Dados em um espaço de trabalho de Aprendizado de Máquina do Azure, configurá-lo e, em seguida, ler os dados de um banco de dados do SQL Server local.

> [AZURE.TIP] Antes de começar, desabilite o bloqueador de pop-up do navegador para `studio.azureml.net`. Se você estiver usando o navegador Google Chrome, baixe e instale um dos vários plug-ins disponíveis na [Extensão do Aplicativo Click Once](https://chrome.google.com/webstore/search/clickonce?_category=extensions) no Google Chrome WebStore.

### Etapa 1: criar um gateway

A primeira etapa é criar e configurar o gateway para acessar o banco de dados SQL local.

1.  Faça logon em [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net/Home/) e selecione o espaço de trabalho no qual você deseja trabalhar.

2.  Clique na folha **CONFIGURAÇÕES** à esquerda e, em seguida, clique na guia **GATEWAYS DE DADOS** na parte superior.

3.  Clique em **NOVO GATEWAY DE DADOS** na parte inferior da tela.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)

4.  No diálogo **Novo gateway de dados**, insira o **Nome do Gateway** e, se preferir, adicione uma **Descrição**. Clique na seta no canto inferior direito para ir para a próxima etapa da configuração.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)

5.  No diálogo Baixar e registrar gateway de dados, copie a CHAVE DE REGISTRO DO GATEWAY para a área de transferência.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)

6.  <span id="note-1" class="anchor"></span>Se você ainda não tiver baixado e instalado o Gateway de Gerenciamento de Dados da Microsoft, clique em **Baixar gateway de gerenciamento de dados**. Isso levará você ao Centro de Download da Microsoft, em que você pode selecionar a versão de gateway necessária, baixá-la e instalá-la. Você pode encontrar informações detalhadas sobre pré-requisitos de instalação, etapas de instalação e dicas de solução de problemas nas seções de início do artigo [Mover dados entre fontes locais e na nuvem com o Gateway de Gerenciamento de Dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

7.  Depois que o gateway está instalado, o Gerenciador de Configurações do Gateway de Gerenciamento de Dados será aberto e o diálogo **Registrar gateway** exibido. Cole a **Chave de Registro de Gateway** que você copiou para a área de transferência e clique em **Registrar**.

8.  Se você já tiver um gateway instalado, execute o Gerenciador de Configurações do Gateway de Gerenciamento de Dados, clique em **Alterar chave**, cole a **Chave de Registro de Gateway** que você copiou para a área de transferência e clique em **OK**.

9.  Quando a instalação estiver concluída, o diálogo **Registrar gateway** para o Gerenciador de Configurações do Gateway de Gerenciamento de Dados da Microsoft será exibido. Cole a CHAVE DE REGISTRO DE GATEWAY que você copiou para a área de transferência acima e clique em **Registrar**.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)

10.  A configuração do gateway é concluída quando os seguintes valores são definidos na guia **Início** do Gerenciador de Configurações do Gateway de Gerenciamento de Dados Microsoft:

    -   O **Nome do gateway** e o **nome de instância** são definidos como o nome do gateway.

    -   **Registro** é definido como **Registrado**.

    -   O **Status** é definido como **Iniciado**.

    -   A barra de status inferior exibe **Conectado ao Serviço de Nuvem do Gateway de Gerenciamento de Dados** junto com uma marca de seleção verde.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

    O Estúdio de Aprendizado de Máquina do Azure também é atualizado quando o registro é bem-sucedido.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-registered.png)

11.  No diálogo **Baixar e registrar o gateway de dados**, clique na marca de seleção para concluir a instalação. A página **Configurações** exibe o status do gateway como "Online". No painel à direita, você encontrará o status e outras informações úteis.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-status.png)

12. Em Gerenciador de Configurações do Gateway do Gerenciamento de Dados da Microsoft, alterne para a guia **Certificado**. O certificado especificado nessa guia é usado para criptografar/descriptografar credenciais para o armazenamento de dados local que você especifica no portal. Este é o certificado padrão gerado. A Microsoft recomenda alterar para seu próprio certificado com backup no sistema de gerenciamento de certificado. Clique em **Alterar** para usar seu próprio certificado.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-certificate.png)

13. (opcional) Se desejar habilitar o log detalhado para solução de problemas com o gateway, no Gerenciador de Configurações do Gateway do Gerenciamento de Dados da Microsoft alterne para a guia **Diagnóstico** e selecione a opção **Habilitar log detalhado para solução de problemas**. As informações de log podem ser encontradas no Visualizador de Eventos do Windows em **Logs de Aplicativos e Serviços** -> nó **Gateway de Gerenciamento de Dados**. Você também pode usar a guia **Diagnóstico** para testar a conexão para uma fonte de dados local usando o gateway.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-verbose-logging.png)

Isso conclui o processo de configuração do gateway no Aprendizado de Máquina do Azure. Agora você está pronto para usar seus dados locais.

Você pode criar e configurar vários gateways no Studio de cada espaço de trabalho. Por exemplo, você pode ter um gateway que você deseja conectar às suas fontes de dados de teste durante o desenvolvimento e um gateway diferente para suas fontes de dados de produção. O Aprendizado de Máquina do Azure oferece a flexibilidade para configurar vários gateways, dependendo do seu ambiente corporativo. Atualmente, não é possível compartilhar um gateway entre espaços de trabalho e apenas um gateway pode ser instalado em um único computador. Para obter mais considerações ao instalar o gateway, consulte [Considerações para usar o Gateway de Gerenciamento de Dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway) neste artigo, [Mover dados entre fontes locais e na nuvem com o Gateway de Gerenciamento de Dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

### Etapa 2: usar o gateway para ler dados de uma fonte de dados local

Depois de configurar o gateway, você pode adicionar um módulo **Importar Dados** a um experimento que insere os dados do banco de dados do SQL Server local.

1.  No Estúdio de Aprendizado de Máquina, selecione a guia **EXPERIMENTOS**, clique em **+NOVO** no canto inferior esquerdo e selecione **Experimento em Branco** (ou selecione uma das várias experiências de exemplo disponíveis).

2.  Localize e arraste o módulo **Importar Dados** para a tela do experimento.

3.  Clique em **Salvar como**, abaixo das telas. Insira "Tutorial do SQL Server Local do Aprendizado de Máquina do Azure" como nome do experimento, selecione o espaço de trabalho e clique na marca de seleção **OK**.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\experiment-save-as.png)

4.  Clique no módulo **Importar Dados** para selecioná-lo e, no painel **Propriedades** à direita da tela, selecione "Banco de Dados SQL Local" na lista suspensa **Fonte de Dados**.

5.  Selecione o **Gateway de dados** instalado e registrado. Você pode configurar outro gateway selecionando "(adicionar novo Gateway de Dados...)".

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-select-on-premises-data-source.png)

6.  Insira o **nome do servidor de banco de dados** SQL e o **Nome do banco de dados**, juntamente com a **consulta de banco de dados** SQL que você deseja executar.

7.  Clique em **Inserir valores** em **Nome de usuário e senha** e insira suas credenciais de banco de dados. Você pode usar a Autenticação Integrada do Windows ou Autenticação do SQL Server dependendo de como o SQL Server local está configurado.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\database-credentials.png)
    
    A mensagem "valores necessários" será alterado para "valores definidos" com uma marca de seleção verde. Basta inserir as credenciais de uma vez, a menos que as informações de banco de dados ou a senha seja alterada. O Aprendizado de Máquina do Azure usa o certificado fornecido quando você instalou o gateway para criptografar as credenciais na nuvem. O Azure nunca armazenará credenciais locais sem criptografia.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-properties-entered.png)

8.  Clique em **EXECUTAR** para executar o experimento.

Quando o experimento concluir a execução, você poderá visualizar os dados importados do banco de dados clicando na porta de saída do módulo **Importar Dados** e selecionando **Visualizar**.

Depois de concluir o desenvolvimento de seu experimento, você poderá implantar e colocar o modelo em operação. Usando o Serviço de Execução de Lote, os dados do banco de dados SQL Server local configurados no módulo **Importar Dados** serão lidos e usados para pontuação. Embora você possa usar o Serviço de Resposta de Solicitação para pontuar dados locais, a Microsoft recomenda usar o [Suplemento do Excel](machine-learning-excel-add-in-for-web-services.md). Atualmente, gravar em um banco de dados do SQL Server local por meio de **Exportar Dados** não tem suporte em seus experimentos ou em serviços Web publicados.

<!---HONumber=AcomDC_0601_2016-->