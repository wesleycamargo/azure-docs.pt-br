---
title: Criar uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB | Microsoft Docs
description: Saiba como criar uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB, agora disponível para a versão de visualização.
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: anhoh

---
# <a name="how-to-create-a-documentdb-account-with-protocol-support-for-mongodb-using-the-azure-portal"></a>Como criar uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB usando o portal do Azure
Para criar uma conta do Banco de Dados de Documentos do Azure com suporte de protocolo para MongoDB, você deve:

* Ter uma conta do Azure. Caso ainda não tenha uma, você pode obter uma [conta gratuita do Azure](https://azure.microsoft.com/free/) .

## <a name="create-the-account"></a>Criar a conta
Para criar uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB, execute as etapas a seguir.

1. Em uma nova janela, entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **NOVO**, em **Dados + Armazenamento**, em **Ver tudo** e procure a categoria **Dados + Armazenamento** para "protocolo do Banco de Dados de Documentos". Clique em **Banco de Dados de Documentos - Suporte de Protocolo para o MongoDB**.
   
    ![Captura de tela das folhas de pesquisa Marketplace e Dados + Armazenamento, destacando o Banco de Dados de Documentos - Suporte do Protocolo para o MongoDB, banco de dados Mongo](./media/documentdb-create-mongodb-account/marketplacegallery2.png)
3. Como alternativa, na categoria **Dados + Armazenamento**, em **Armazenamento**, clique em **Mais** e clique em **Carregar mais** uma ou mais vezes para exibir **Banco de Dados de Documentos - Suporte de Protocolo para o MongoDB**. Clique em **Banco de Dados de Documentos - Suporte de Protocolo para o MongoDB**.
   
    ![Captura de tela das folhas Marketplace e Dados + Armazenamento, destacando o Banco de Dados de Documentos - Suporte do Protocolo para o MongoDB, banco de dados Mongo](./media/documentdb-create-mongodb-account/marketplacegallery1.png)
4. Na folha **Banco de Dados de Documentos - Suporte de Protocolo para o MongoDB (preview)**, clique em **Criar** para iniciar o processo de inscrição na versão de visualização.
   
    ![Banco de Dados de Documentos - Suporte do Protocolo para a folha MongoDB no portal do Azure](./media/documentdb-create-mongodb-account/marketplacegallery3.png)
5. Na folha **Conta do Banco de Dados de Documentos**, clique em **Inscrever-se na versão de visualização**. Revise as informações e clique em **OK**.
   
    ![Inscrição para visualizar a folha hoje para o Banco de Dados de Documentos - Suporte do Protocolo para o MongoDB no portal do Azure](./media/documentdb-create-mongodb-account/registerforpreview.png)
6. Depois de aceitar os termos da versão de visualização, você retornará para a folha de criação.  Na folha **Conta do Banco de Dados de Documentos** , especifique a configuração desejada para a conta.
   
   ![Captura de tela da folha Novo Banco de Dados de Documentos com suporte de protocolo para MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)

    - Na caixa **ID** , insira um nome para identificar a conta.  Quando a **ID** for validada, uma marca de seleção verde será exibida na caixa de **ID**. Esse valor de **ID** torna-se o nome de host no URI. A **ID** pode conter somente letras minúsculas, números e o caractere “-”, e deve ter entre 3 e 50 caracteres. Observe que *documents.azure.com* é anexado ao nome do ponto de extremidade escolhido, o resultado disso se tornará o ponto de extremidade da sua conta.

    - Em **Assinatura**, escolha a assinatura do Azure que deseja usar para a conta. Se a sua conta tiver apenas uma assinatura, essa conta será selecionada por padrão.

    - No **Grupo de Recursos**, escolha ou crie um grupo de recursos para a conta.  Por padrão, um grupo de Recursos na assinatura do Azure será escolhido.  No entanto, você pode optar por criar um novo grupo de recursos ao qual deseja adicionar a conta. Para saber mais, veja [Como usar o portal do Azure para gerenciar os recursos do Azure](resource-group-portal.md).

    - Use **Localização** para especificar o local geográfico no qual hospedar a conta.

    - Marque **Fixar no painel** 

1. Depois que as novas opções de conta forem configuradas, clique em **Criar**.  Pode levar alguns minutos para criar a conta.  Para verificar o status, você pode monitorar o progresso no quadro inicial.  
   ![Captura de tela do bloco Criando no quadro inicial - criador de banco de dados online](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-3.png)  
   
   Ou então, você pode monitorar o progresso do hub de notificações.  
   
   ![Crie bancos dados rapidamente - Captura de tela do hub de notificações, mostrando que a conta do Banco de Dados de Documentos está sendo criada](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-4.png)  
   
   ![Captura de tela do hub de notificações, mostrando que a conta do Banco de Dados de Documentos foi criada com êxito e implantada em um grupo de recursos - Criador de banco de dados online](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-5.png)
2. Depois que a conta for criada, ela estará pronta para ser usada com as configurações padrão. 
   
   ![Captura de tela da folha conta padrão](./media/documentdb-create-mongodb-account/defaultaccountblades.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba como se [conectar](documentdb-connect-mongodb-account.md) a uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.

<!--HONumber=Oct16_HO2-->


