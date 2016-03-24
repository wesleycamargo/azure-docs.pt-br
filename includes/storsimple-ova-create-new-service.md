#### Para criar um novo serviço

1.  Usando suas credenciais de conta da Microsoft, faça logon no portal clássico do Azure nesta URL: [https://manage.windowsazure.com/](https://manage.windowsazure.com/)

2.  No portal, clique em **Novo > Serviços de Dados > StorSimple Manager > Criação rápida**.

3.  No formulário exibido, faça o seguinte:

	1.  Fornecer um **Nome** exclusivo para o serviço. Este é um nome amigável que pode ser usado para identificar o serviço. O nome pode ter entre 2 e 50 caracteres que podem ser letras, números e hífens. O nome deve começar e terminar com uma letra ou um número.

	2.  Para um serviço gerenciar um dispositivo virtual StorSimple, na lista suspensa de **Tipo de dispositivos gerenciados**, escolha **Série do dispositivo virtual**.

	3.  Fornecer um **Local** para o serviço. Local refere-se à região geográfica em que você deseja implantar seu dispositivo.

	 -   Se você tiver outras cargas de trabalho no Azure que planeja implantar com seu dispositivo StorSimple, recomendamos o uso daquele datacenter.

   	 -   O StorSimple Manager e o armazenamento do Azure podem estar em dois locais separados. Nesse caso, é necessário criar a conta de armazenamento do Azure e do StorSimple Manager separadamente. Para criar uma conta de armazenamento do Azure, acesse o serviço Armazenamento do Azure no portal e siga as etapas em [Criar uma conta de Armazenamento do Azure](storage-create-storage-account.md#create-a-storage-account). Após criar essa conta, adicione-a ao serviço StorSimple Manager seguindo as etapas em [Configurar uma nova conta de armazenamento para o serviço](#optional-step-configure-a-new-storage-account-for-the-service).
   	 
   	 	
	1.  Escolha uma **Assinatura** na lista suspensa. A assinatura está vinculada à sua conta de cobrança. Este campo não estará presente quando você tem apenas uma assinatura.

	1.  Selecione **Criar uma nova conta de armazenamento do Azure** para criar automaticamente uma conta de armazenamento com o serviço. Essa conta de armazenamento terá um nome especial, como "storsimplebwv8c6dcnf". Se você precisar de seus dados em um local diferente, desmarque essa caixa de seleção.

	1.  Clique em **Criar StorSimple Manager** para criar o serviço.

		![](./media/storsimple-ova-create-new-service/image1m-include.png)

	Você será direcionado para a página inicial **Serviço**. A criação do serviço levará alguns minutos. Depois que o serviço for criado com êxito, você será notificado adequadamente.

	![](./media/storsimple-ova-create-new-service/image2-include.png)

	O status do serviço será alterado para **Ativo**.

<!---HONumber=AcomDC_0309_2016-->