<properties 
	pageTitle="Atualizar os Serviços de Mídia após implantar chaves de acesso de armazenamento" 
	description="Este artigo fornece diretrizes sobre como atualizar os Serviços de Mídia após implantar chaves de acesso de armazenamento." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>

#Como: Atualizar os Serviços de Mídia após implantar chaves de acesso de armazenamento

Ao criar uma nova conta dos Serviços de Mídia do Azure, você também é solicitado a selecionar uma conta de Armazenamento do Azure, que é usada para armazenar o conteúdo de mídia. Observe que você pode adicionar mais de uma conta de armazenamento à sua conta dos Serviços de Mídia.

Quando uma nova conta de armazenamento é criada, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticar o acesso à sua conta de armazenamento. Para manter as conexões de armazenamento mais seguro, é recomendável regenerar periodicamente e fazer uma rotação de sua chave de acesso de armazenamento. Duas chaves de acesso (primária e secundária) são fornecidas para habilitá-lo a manter conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra chave de acesso. Esse procedimento também é chamado de "implantação de chaves de acesso".

Os Serviços de Mídia têm dependência em relação a uma das chaves de armazenamento (primária ou secundária). Especificamente, os localizadores que são usados para transmitir ou baixar os ativos dependem da chave de acesso. Durante a implantação de chaves de acesso de armazenamento, você também precisa atualizar seus localizadores para que não haja interrupção do serviço de streaming.

>[AZURE.NOTE]Após regenerar uma chave de armazenamento, sincronize a atualização com os Serviços de Mídia.

Este tópico descreve as etapas que necessárias para implantar chaves de armazenamento e atualizar os Serviços de Mídia para usar a chave de armazenamento apropriada. Observe que, se tiver várias contas de armazenamento, você deverá executar esse procedimento com cada conta de armazenamento.

>[AZURE.NOTE]Antes de executar as etapas descritas neste tópico em uma conta de produção, teste-as em uma conta de pré-produção.


## Etapa 1: Regenerar a chave de acesso de armazenamento secundária

Comece com a regeneração da chave de armazenamento secundária. Por padrão, a chave secundária não é usada pelos Serviços de Mídia. Para obter informações sobre chaves de acesso do armazenamento, consulte [Como exibir, copiar e regenerar chaves de acesso de armazenamento](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Etapa 2: Atualizar os Serviços de Mídia para usar a nova chave de armazenamento secundária

Atualize os Serviços de Mídia para usar a chave de acesso de armazenamento secundária. Você pode usar um dos dois métodos a seguir para sincronizar a chave de armazenamento regenerada com os Serviços de Mídia.

- Usar o Portal do Azure: selecione sua conta dos Serviços de Mídia e clique no ícone "GERENCIAR CHAVES" na parte inferior da janela do portal. Dependendo de qual chave de armazenamento você desejar sincronizar com os Serviços de Mídia, selecione o botão para sincronizar a chave primária ou sincronizar a chave secundária. Nesse caso, use a chave secundária.

- Use a API REST de gerenciamento dos Serviços de Mídia.

	O exemplo de código a seguir mostra como criar a solicitação https://endpoint/<subscriptionId>/services/mediaservices/Accounts/<accountName>/StorageAccounts/<storageAccountName>/Key para sincronizar a chave de armazenamento especificada com os Serviços de Mídia. Nesse caso, o valor da chave de armazenamento secundária é usado. Para obter mais informações, consulte [Como: Use a API REST de gerenciamento dos Serviços de Mídia](http://msdn.microsoft.com/en-us/library/azure/dn167656.aspx).
 
		public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
		{
		    var clientCert = GetCertificate(CertThumbprint);
		
		    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
		        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
		    request.Method = "PUT";
		    request.ContentType = "application/json; charset=utf-8";
		    request.Headers.Add("x-ms-version", "2011-10-01");
		    request.Headers.Add("Accept-Encoding: gzip, deflate");
		    request.ClientCertificates.Add(clientCert);
		
		
		    using (var streamWriter = new StreamWriter(request.GetRequestStream()))
		    {
		        streamWriter.Write(""");
		        streamWriter.Write(storageAccountKey);
		        streamWriter.Write(""");
		        streamWriter.Flush();
		    }
		
		    using (var response = (HttpWebResponse)request.GetResponse())
		    {
		        string jsonResponse;
		        Stream receiveStream = response.GetResponseStream();
		        Encoding encode = Encoding.GetEncoding("utf-8");
		        if (receiveStream != null)
		        {
		            var readStream = new StreamReader(receiveStream, encode);
		            jsonResponse = readStream.ReadToEnd();
		        }
		    }
		}

Em seguida, atualize os localizadores existentes (que têm dependência em relação à chave de armazenamento antiga).

>[AZURE.NOTE]Aguarde 30 minutos antes de executar quaisquer operações com os Serviços de Mídia (por exemplo, criar novos localizadores) para impedir que haja impacto em relação a trabalhos pendentes.

##Etapa 3: Atualizar localizadores 

Após 30 minutos, é possível atualizar os localizadores existentes para que eles tenham dependência em relação à nova chave de armazenamento secundária.

Para atualizar a data de validade em um localizador, use as APIs [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Observe que, quando você atualiza a data de validade de um localizador SAS, a URL é alterada.

##Estapa 5: Regenerar a chave de acesso de armazenamento primária

Regenere a chave de acesso de armazenamento primária. Para obter informações sobre chaves de acesso do armazenamento, consulte [Como exibir, copiar e regenerar chaves de acesso de armazenamento](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##Etapa 6: Atualizar os Serviços de Mídia para usar a nova chave de armazenamento primária
	
Use o mesmo procedimento, conforme descrito na [etapa 2](media-services-roll-storage-access-keys.md#step2), mas, desta vez, sincronize a nova chave de acesso de armazenamento primária com a conta dos Serviços de Mídia.

>[AZURE.NOTE]Aguarde 30 minutos antes de executar quaisquer operações com os Serviços de Mídia (por exemplo, criar novos localizadores) para impedir que haja impacto em relação a trabalhos pendentes.

##Etapa 7: Atualizar localizadores  

Após 30 minutos, é possível atualizar os localizadores existentes para que eles tenham dependência em relação à nova chave de armazenamento primária.

Para atualizar a data de validade em um localizador, use as APIs [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Observe que, quando você atualiza a data de validade de um localizador SAS, a URL é alterada.

 
 

<!---HONumber=July15_HO4-->