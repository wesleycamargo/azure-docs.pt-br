---
title: 'Tutorial: Usar o Serviço de Migração de Banco de Dados do Azure para migrar offline do MongoDB para a API do Mongo do Azure Cosmos DB | Microsoft Docs'
description: Saiba como migrar offline do MongoDB local para a API do Mongo do Azure Cosmos DB usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 46e8ca97411e9dce77c0c82c4e0f5dd164bce01b
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320720"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-mongo-api-offline-using-dms"></a>Tutorial: Migrar offline do MongoDB para a API do Mongo do Azure Cosmos DB usando o DMS
É possível usar o Serviço de Migração de Banco de Dados do Azure para realizar uma migração única offline de bancos de dados de uma instância local ou em nuvem do MongoDB para a API do Mongo do Azure Cosmos DB.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.

Neste tutorial, o conjunto de dados **Wingtips** no MongoDB hospedado em uma Máquina Virtual do Azure será migrado para a API do MongoDB do Cosmos DB por meio do Serviço de Migração de Banco de Dados do Azure. Se não houver uma origem do MongoDB configurada, confira o artigo [Instalar e configurar o MongoDB em uma VM do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará:
- [Criar uma conta de API do MongoDB do Azure Cosmos DB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Criar um VNET para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Verificar se as regras do Grupo de Segurança de Rede da VNET (Rede Virtual) do Azure não bloqueiam as portas de comunicação: 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego do NSG da Rede Virtual do Azure, consulte o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o servidor MongoDB de origem, que, por padrão, é a porta TCP 27017.
- Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration
1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.
 
    ![Exibir provedores de recursos](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.
 
    ![Registrar provedor de recursos](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Escolher uma instância
1.  No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.
 
    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione o local no qual você deseja criar a instância do Serviço de Migração de Banco de Dados do Azure. 

5. Escolha uma rede virtual existente (VNET) ou criar uma nova.

    A VNET fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao SQL Server de origem e à instância do Banco de Dados SQL do Azure de destino.

    Para mais informações sobre como criar uma VNET no portal do Azure, consulte o artigo [Criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    Se você precisar de ajuda na escolha do nível certo do Serviço de Migração de Banco de Dados do Azure, veja as recomendações na postagem no blog [aqui](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.
 
      ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na tela dos **Serviços de Migração de Banco de Dados do Azure**, procure o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou e, em seguida, selecione a instância.

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **Novo projeto de migração**, especifique um nome para o projeto. Na caixa de texto **Tipo de servidor de origem**, selecione **MongoDB**; na caixa de texto **Tipo de servidor de destino**, selecione **CosmosDB (API do MongoDB)**. Em **Escolher tipo de atividade**, selecione **Migração de dados offline**. 

    ![Criar o projeto do Serviço de Migração de Banco de Dados](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especifique as configurações de origem
1. Na tela **Detalhes da origem**, especifique os detalhes da conexão do servidor de origem do MongoDB.
    
   Também é possível usar o modo de cadeia de conexão e fornecer uma localização para um contêiner de arquivos de armazenamento de blog na qual os dados de coleta que você pretende migrar foram despejados.

   > [!NOTE]
   > O Serviço de Migração de Banco de Dados do Azure também pode migrar documentos BSON ou JSON para as coleções da API do Mongo do Azure Cosmos DB.
    
   Você também pode usar o endereço IP para situações em que a resolução de nome do DNS não é possível.

   ![Especifique as configurações de origem](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito
1. Na tela de **Detalhes de destino da migração**, especifique os detalhes de conexão da conta do Azure Cosmos DB de destino, que é a conta do MongoDB do Azure Cosmos DB provisionada anteriormente para a qual os dados do MongoDB serão migrados.

    ![Detalhes do destino favorito](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Clique em **Salvar**.

## <a name="map-to-target-databases"></a>Mapear para bancos de dados de destino
1. No **são mapeados para os bancos de dados de destino** tela, a origem e o banco de dados de destino para migração do mapa.

    Se o banco de dados de destino contém o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure seleciona o banco de dados de destino por padrão.

    Se a cadeia de caracteres **Criar** for exibida ao lado do nome do banco de dados, será uma indicação de que o Serviço de Migração de Banco de Dados do Azure não localizou o banco de dados de destino e o serviço criará o banco de dados para você.

    Neste momento da migração, é possível [provisionar a taxa de transferência](https://docs.microsoft.com/azure/cosmos-db/set-throughput). No Cosmos DB, você pode provisionar a taxa de transferência no nível do banco de dados ou individualmente para cada coleção. A taxa de transferência é medida em [RUs](https://docs.microsoft.com/azure/cosmos-db/request-units) (Unidades de Solicitação). Saiba mais sobre o [preço do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapear para bancos de dados de destino](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Clique em **Salvar**.
3. Na tela **Configuração de coleção**, expanda e analise a lista de coleções a ser migrada.

    O Serviço de Migração de Banco de Dados do Azure seleciona automaticamente todas as coleções na instância do MongoDB de origem que não estão presentes na conta do Azure Cosmos DB de destino. Se quiser migrar novamente as coleções que já contêm dados, você precisará selecionar as coleções nesta folha de forma explícita.

    É possível especificar a quantidade de RUs a serem usados nas coleções. O Serviço de Migração de Banco de Dados do Azure sugere padrões inteligentes com base no tamanho da coleção.

    Também é possível especificar uma chave fragmentada para aproveitar o [particionamento no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) e obter a escalabilidade ideal. Não se esqueça de analisar as [melhores práticas para selecionar uma chave fragmentada/de partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Selecionar tabelas de coleções](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Clique em **Salvar**.

5. Na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

    ![Resumo da migração](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração
- Selecione **Executar migração**.

    A janela de atividade de migração aparece e o **Status** da atividade está **Não Iniciado**.

    ![Status da atividade](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorar a migração
- Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Concluído**.

   > [!NOTE]
   > É possível selecionar a Atividade para saber detalhes sobre as métricas de migração no nível do banco de dados e da coleção.

    ![Status de atividade concluído](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Verificar os dados no Cosmos DB

- Após a conclusão da migração, é possível verificar a conta da API do MongoDB do Cosmos DB para confirmar se todas as coleções foram migradas com êxito.

    ![Status de atividade concluído](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>Recursos adicionais

 * [Informações sobre o serviço do Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Próximas etapas
- Revisar as diretrizes de migração para saber sobre mais cenários no [Guia de Migração de Banco de Dados](https://datamigration.microsoft.com/) da Microsoft.