
#### Para criar um novo serviço

1. Usando suas credenciais de conta da Microsoft, faça logon no Portal de Gerenciamento na seguinte URL: [http://azure.microsoft.com/](http://azure.microsoft.com/).

2. No Portal de Gerenciamento, clique em **Novo** > **Serviços de Dados** > **StorSimple Manager** > **Criação Rápida**.

3. No formulário exibido, faça o seguinte:
  1. Fornecer um **Nome** exclusivo para o serviço. Este é um nome amigável que pode ser usado para identificar o serviço. O nome pode ter entre 2 e 50 caracteres que podem ser letras, números e hífens. O nome deve começar e terminar com uma letra ou um número.
  2. Fornecer um **Local** para o serviço. Em geral, escolha um local próximo à região geográfica onde quer implantar os seus serviços. Você talvez queira considerar o seguinte: 
	 
		- If you have existing workloads in Azure that you also intend to deploy with your StorSimple device, you should use that datacenter.
		- Your StorSimple Manager service and Azure storage can be in two separate locations. In such a case, you are required to create the StorSimple Manager and Azure storage account separately. To create an Azure storage account, go to the Azure Storage service in the Management Portal and follow the steps in [Create an Azure Storage account](storage-create-storage-account.md#create-a-storage-account). After you create this account, add it to the StorSimple Manager service by following the steps in [Configure a new storage account for the service](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).
		 
  3. Escolha uma **Assinatura** na lista suspensa. A assinatura está vinculada à sua conta de cobrança. Este campo não estará presente se você tiver apenas uma assinatura.
  4. Selecione **Criar uma nova conta de armazenamento** para criar automaticamente uma conta de armazenamento com o serviço. Esta conta de armazenamento terá um nome especial, como "storsimplebwv8c6dcnf". Se você precisar de seus dados em um local diferente, desmarque essa caixa. 
  5. Clique em **Criar StorSimple Manager** para criar o serviço.

   ![criar um serviço](./media/storsimple-create-new-service/HCS_CreateAService-include.png)

  Você será direcionado para a página inicial **Serviço**. A criação do serviço levará alguns minutos. Depois que o serviço foi criado com êxito, você será notificado adequadamente e o status do serviço será alterado para **Ativo**.
 
   ![criação de serviço](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

<!---HONumber=August15_HO8-->