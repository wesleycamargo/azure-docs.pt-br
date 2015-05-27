## Examinar metadados do aplicativo de API

Os metadados que habilitam um projeto de API Web a ser implantado como um aplicativo de API estão contidos em um arquivo *apiapp.json* e uma pasta *Metadados*.

![](./media/app-service-api-review-metadata/metadatainse.png)

O conteúdo padrão do arquivo *apiapp.json* tem a aparência do seguinte exemplo:

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsList",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

Para este tutorial, você pode aceitar os padrões. A seção [Metadados de aplicativo de API](#api-app-metadata) posteriormente no tutorial explica como personalizar esses metadados.
<!--HONumber=54-->