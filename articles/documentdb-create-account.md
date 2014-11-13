<properties title="Como criar uma conta do Banco de Dados de Documentos" pageTitle="Como criar uma conta do Banco de Dados de Documentos | Azure" description="Descubra como criar uma conta do Banco de Dados de Documentos e escolha as configura&ccedil;&otilde;es da conta no Portal de Visualiza&ccedil;&atilde;o do Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

# Como criar uma conta do Banco de Dados de Documentos

Para usar o Banco de Dados de Documentos, você deve criar uma conta do Banco de Dados de Documentos em uma região disponível. Este tópico descreve como criar uma conta do Banco de Dados de Documentos no Portal de Visualização do Azure.

## Sumário

-   [Como: Criar uma conta do Banco de Dados de Documentos][Como: Criar uma conta do Banco de Dados de Documentos]
-   [Próximas etapas][Próximas etapas]

## <span id="HowTo"></span></a>Como: Criar uma conta do Banco de Dados de Documentos

1.  Entre no [Portal de visualização do Gerenciamento do Azure][Portal de visualização do Gerenciamento do Azure]
2.  Clique em Novo -\> Conta do Banco de Dados de Documentos.
    ![][0]

    Você pode, como alternativa, navegar pela Galeria do Azure, selecionar a categoria “Dados, armazenamento, + backup”, selecionar **Banco de Dados de Documentos** e clicar em **Criar**.

    ![][1]

    Na lâmina **Novo Banco de Dados de Documentos (visualização)**, especifique a configuração desejada para a conta do Banco de Dados de Documentos.

    ![][2]

    Em Nome, insira um nome para ser usado no URI para a conta do Banco de Dados de Documentos. Este valor se torna o nome do host dentro do URI que é usado para endereçar a conta do Banco de Dados de Documentos. A entrada de nome pode conter somente letras minúsculas, números e o caractere “-”, e deve ter entre 3 e 50 caracteres.

    *Observe que documents.azure.com será anexado ao nome do ponto de extremidade que você escolher, e seu resultado se tornará o ponto de extremidade da sua conta do Banco de Dados de Documentos.*

    A lente **Camada de preços** fica bloqueada, pois a visualização do Banco de Dados de Documentos suporta apenas uma camada de preço padrão.

    *Para obter mais informações, consulte os [Detalhes dos preços do Banco de Dados de Documentos][Detalhes dos preços do Banco de Dados de Documentos]*

    A lente **Configuração opcional** é usada para especificar a capacidade inicial alocada à conta do Banco de Dados de Documentos. O Banco de Dados de Documentos utiliza as unidades de capacidade para permitir que você dimensione sua conta do Banco de Dados de Documentos, em que cada unidade de capacidade inclui o armazenamento e a taxa de transferência reservados para o banco de dados. Por padrão, uma unidade de capacidade é provisionada. Você pode ajustar o número de unidades de capacidade disponível para a sua conta do Banco de Dados de Documentos a qualquer momento por meio do [Portal de visualização de Gerenciamento do Azure][Portal de visualização do Gerenciamento do Azure].

    *Para ver detalhes sobre a capacidade e a taxa de transferência do Banco de Dados de Documentos, consulte o artigo [Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos][Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos].*

    Em **Grupo de recursos**, selecione ou crie um grupo de recursos para sua conta do Banco de Dados de Documentos. Por padrão, um novo Grupo de recursos será criado. Você pode, no entanto, optar por selecionar um grupo de recursos existente ao qual gostaria de adicionar sua conta do Banco de Dados de Documentos.

    *Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure][Usando os grupos de recursos para gerenciar seus recursos do Azure]*.

    Para **Assinatura**, selecione a assinatura do Azure que deseja usar para a conta do Banco de Dados de Documentos.

    *Se a sua conta tiver apenas uma assinatura, ela será selecionada automaticamente.*

    Utilize a **Localização** para especificar a localização geográfica em que a conta do Banco de Dados de Documentos será hospedada.

3.  Após as opções da nova conta do Banco de Dados de Documentos serem configuradas, clique em **Criar**. Pode levar alguns minutos para que a conta do Banco de Dados de Documentos seja criada. Para verificar o status, você pode monitorar o progresso no quadro inicial.
    ![][3]

    Ou no hub de Notificações.

    ![][4]

    ![][5]

4.  Após a conta do Banco de Dados de Documentos ter sido criada, ela estará pronta para uso com as configurações padrão.

    *Observe que a consistência padrão da conta do Banco de Dados de Documentos será definida como Sessão. Você pode ajustar a configuração da consistência padrão por meio do [Portal de visualização do Gerenciamento do Azure][Portal de visualização do Gerenciamento do Azure].*
    ![][6]

5.  Você também pode acessar suas contas existentes do Banco de Dados de Documentos a partir da lâmina **Navegar**.
    ![][7]

## <span id="NextSteps"></span></a>Próximas etapas

-   Para saber mais sobre o Banco de Dados de Documento, consulte a documentação do Banco de Dados de Documentos do Azure em [azure.com][azure.com]

<!--Image references-->

  [Como: Criar uma conta do Banco de Dados de Documentos]: #Howto
  [Próximas etapas]: #NextSteps
  [Portal de visualização do Gerenciamento do Azure]: https://portal.azure.com/
  [0]: ./media/documentdb-create-account/ca1.png
  [1]: ./media/documentdb-create-account/ca2.png
  [2]: ./media/documentdb-create-account/ca3.png
  [Detalhes dos preços do Banco de Dados de Documentos]: http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409
  [Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos]: ../documentdb-manage/
  [Usando os grupos de recursos para gerenciar seus recursos do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/azure-preview-portal-using-resource-groups/
  [3]: ./media/documentdb-create-account/ca4.png
  [4]: ./media/documentdb-create-account/ca5.png
  [5]: ./media/documentdb-create-account/ca6.png
  [6]: ./media/documentdb-create-account/ca7.png
  [7]: ./media/documentdb-create-account/ca8.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
