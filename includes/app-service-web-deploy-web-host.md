### Plano do Serviço de Aplicativo

Cria o plano de serviço para hospedar o aplicativo Web. Forneça o nome do plano por meio do parâmetro **hostingPlanName**. O local do plano é o mesmo usado para o aplicativo Web. A camada de preços e o tamanho do trabalhador são especificados nos parâmetros **sku** e **workerSize**

    {
       "apiVersion":"2015-04-01",
       "name":"[parameters('hostingPlanName')]",
       "type":"Microsoft.Web/serverfarms",
       "location":"[parameters('siteLocation')]",
       "properties":{
         "name":"[parameters('hostingPlanName')]",
         "sku":"[parameters('sku')]",
         "workerSize":"[parameters('workerSize')]",
         "numberOfWorkers":1
       }
    }

<!---HONumber=July15_HO3-->