---
title: "Criar e implantar um aplicativo de API Java no Serviço de Aplicativo do Azure"
description: "Saiba como criar um pacote do aplicativo de API Java e implantá-lo no Serviço de Aplicativo do Azure."
services: app-service\api
documentationcenter: java
author: rmcmurray
manager: erikre
editor: tdykstra
ms.assetid: 8d21ba5f-fc57-4269-bc8f-2fcab936ec22
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: get-started-article
ms.date: 04/25/2017
ms.author: rachelap;robmcm
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e38c540071cb49b0177e79178566d72ecb5f8886
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Criar e implantar um aplicativo de API Java no Serviço de Aplicativo do Azure
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Este tutorial mostra como criar um aplicativo Java e implantá-lo em aplicativos de API do Serviço de Aplicativo do Azure usando o [Git]. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que seja capaz de executar o Java. O código deste tutorial foi criado usando o [Maven]. O [Jax-RS] é usado para criar o serviço RESTful e é gerado com base na especificação de metadados do [Swagger] usando o [Editor do Swagger].

## <a name="prerequisites"></a>Pré-requisitos
1. [Java Developer's Kit 8] \(ou posterior)
2. [Maven] instalado na máquina de desenvolvimento
3. [Git] instalado na máquina de desenvolvimento
4. Uma assinatura paga ou de [avaliação gratuita] para o [Microsoft Azure]
5. Um aplicativo de teste HTTP como [Postman]

## <a name="scaffold-the-api-using-swaggerio"></a>Gerar automaticamente a API usando o Swagger.IO
Usando o editor online swagger.io, você pode inserir o código JSON ou YAML do Swagger que representa a estrutura da API. Depois que a área de superfície de API for criada, você poderá exportar o código para diversas plataformas e estruturas. Na próxima seção, o código gerado automaticamente será modificado para incluir funcionalidade fictícia. 

Esta demonstração começa com um corpo JSON do Swagger que você colará no editor swagger.io, que será então usado para gerar código utilizando RS-JAX para acessar um ponto de extremidade de API REST. Em seguida, você editará o código gerado automaticamente para retornar dados fictícios, simulando uma API REST criada sobre um mecanismo de persistência de dados.  

1. Copie o seguinte código JSON Swagger para a área de transferência:
   
        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }
2. Navegue até o [Editor Online do Swagger]. Quando estiver lá, clique no item de menu **Arquivo -> Colar JSON**.
   
    ![Colar item de menu JSON][paste-json]
3. Cole o JSON do Swagger da API de Lista de Contatos copiado anteriormente. 
   
    ![Colar código JSON no Swagger][pasted-swagger]
4. Exiba as páginas de documentação e o resumo da API renderizadas no editor. 
   
    ![Exibir Documentos Gerados pelo Swagger][view-swagger-generated-docs]
5. Selecione a opção de menu **Gerar Servidor -> JAX-RS** para gerar automaticamente o código do lado do servidor que você editará posteriormente para adicionar a implementação fictícia. 
   
    ![Gerar Item de Menu de Código][generate-code-menu-item]
   
    Após a geração do código, será fornecido um arquivo ZIP que você poderá baixar. Esse arquivo contém o código gerado automaticamente pelo gerador de código do Swagger e todos os scripts de compilação associados. Descompacte a biblioteca inteira em um diretório em sua estação de trabalho de desenvolvimento. 

## <a name="edit-the-code-to-add-api-implementation"></a>Editar o código para adicionar a implementação da API
Nesta seção, você substituirá a implementação do lado servidor do código gerado pelo Swagger pelo seu código personalizado. O novo código retornará uma ArrayList de entidades de Contact para o cliente da chamada. 

1. Abra o arquivo de modelo *Contact.java* localizado na pasta *src/gen/java/io/swagger/model* usando o [Visual Studio Code] ou seu editor de texto favorito. 
   
    ![Abrir Arquivo de Modelo de Contato][open-contact-model-file]
2. Adicione o construtor a seguir à classe **Contact** . 
   
        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }
3. Abra o arquivo de implementação do serviço *ContactsApiServiceImpl.java* localizado na pasta *src/main/java/io/swagger/api/impl* usando o [Visual Studio Code] ou seu editor de texto favorito.
   
    ![Abrir Arquivo de Código de Serviço de Contato][open-contact-service-code-file]
4. Substitua o código no arquivo por esse novo código para adicionar uma implementação fictícia ao código de serviço. 
   
        package io.swagger.api.impl;
   
        import io.swagger.api.*;
        
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
               
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;
   
        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
   
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
   
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
   
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
   
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }
5. Abra um prompt de comando e altere o diretório para a pasta raiz do aplicativo.
6. Execute o comando do Maven a seguir para compilar o código e execute-o usando o servidor de aplicativo Jetty localmente. 
   
        mvn package jetty:run
7. Você deverá ver a janela de comando mostrar que o Jetty iniciou seu código na porta 8080. 
   
    ![Abrir Arquivo de Código de Serviço de Contato][run-jetty-war]
8. Use o [Postman] para fazer uma solicitação ao método de API "obter todos os contatos" em http://localhost:8080/api/contacts.
   
    ![Chamar a API de Contatos][calling-contacts-api]
9. Use o [Postman] para fazer uma solicitação ao método de API "obter um contato específico" em http://localhost:8080/api/contacts/2.
   
    ![Chamar a API de Contatos][calling-specific-contact-api]
10. Por fim, compile o arquivo WAR Java (Web ARchive) executando o comando do Maven a seguir no seu console. 
    
         mvn package war:war
11. Depois que o arquivo WAR for criado, ele será colocado na pasta **target** . Navegue até a pasta de **destino** e renomeie o arquivo WAR para **ROOT.war**. (Verifique se o uso de maiúsculas corresponde a esse formato.)
    
          rename swagger-jaxrs-server-1.0.0.war ROOT.war
12. Por fim, execute os comandos a seguir na pasta raiz do aplicativo para criar uma pasta **deploy** a ser usada para implantar o arquivo WAR no Azure. 
    
          mkdir deploy
          mkdir deploy\webapps
          copy target\ROOT.war deploy\webapps
          cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>Publicar a saída no Serviço de Aplicativo do Azure
Nesta seção, você vai aprender a criar um novo Aplicativo de API usando o Portal do Azure, a preparar esse Aplicativo de API para hospedar aplicativos Java e a implantar o arquivo WAR recém-criado no Serviço de Aplicativo do Azure para executar seu novo Aplicativo de API. 

1. Crie um novo aplicativo de API no [Portal do Azure] clicando no item de menu **Novo -> Web + Móvel -> aplicativo de API**, inserindo detalhes de seu aplicativo e clicando em **Criar**.
   
    ![Criar um novo Aplicativo de API][create-api-app]
2. Quando o aplicativo de API for criado, abra a folha **Configurações** do aplicativo e clique no item de menu **Configurações do aplicativo**. Selecione as versões mais recentes do Java entre as opções disponíveis e o Tomcat mais recente no menu **Contêiner da Web** e clique em **Salvar**.
   
    ![Configurar o Java na folha Aplicativo de API][set-up-java]
3. Clique no item de menu das configurações de **Credenciais de implantação** e forneça um nome de usuário e uma senha que você quer usar para publicar arquivos no Aplicativo de API. 
   
    ![Definir credenciais de implantação][deployment-credentials]
4. Clique no item de menu de configurações de **Origem de implantação** . Uma vez lá, clique no botão **Escolher fonte**, selecione a opção **Repositório Git Local** e clique em **OK**. Isso criará um repositório Git em execução no Azure, com uma associação ao seu Aplicativo de API. Sempre que você confirmar código para a ramificação *mestre* do repositório Git, seu código será publicado em sua instância do Aplicativo de API App em execução em tempo real. 
   
    ![Configurar um novo repositório Git local][select-git-repo]
5. Copie a URL do novo repositório Git para a área de transferência. Salve-a porque ela será importante daqui a pouco. 
   
    ![Configurar um novo repositório Git para o aplicativo][copy-git-repo-url]
6. Envie por push do Git o arquivo WAR para o repositório online. Para fazer isso, navegue até a pasta **deploy** criada anteriormente para que você possa confirmar com facilidade o código no repositório em execução no seu Serviço de Aplicativo. Quando você estiver na janela do console e tiver navegado até a pasta onde a pasta webapps está localizada, emita os comandos Git a seguir para iniciar o processo e disparar uma implantação. 
   
         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master
   
    Quando você emitir a solicitação **push** , será solicitada a senha criada anteriormente para a credencial de implantação. Depois que você inserir suas credenciais, o portal exibirá que a atualização foi implantada.
7. Se você usar novamente o Postman para acessar o Aplicativo de API recém-implantado em execução no Serviço de Aplicativo do Azure, verá que o comportamento é consistente e que agora ele está retornando os dados de contato esperados e usando alterações de código simples feitas no código Java do Swagger.io gerado automaticamente. 
   
    ![Usando a API REST de Contatos Java dinamicamente no Azure][postman-calling-azure-contacts]

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você iniciou com um arquivo JSON do Swagger e código Java gerado automaticamente obtido do editor Swagger.io. A partir daí, suas alterações simples e um processo de implantação Git resultaram em um aplicativo de API funcional escrito em Java. O próximo tutorial mostra como [consumir aplicativos de API de clientes JavaScript usando CORS][App Service API CORS]. Tutoriais subsequentes na série mostram como implementar a autenticação e a autorização.

Para expandir esse exemplo, você pode aprender mais sobre o [SDK de Armazenamento para Java] para manter os blobs JSON. Ou então, você pode usar o [SDK do Java do Banco de Dados de Documentos] para salvar os dados de contato no Banco de Dados de Documentos do Azure. 

<a name="see-also"></a>

## <a name="see-also"></a>Consulte também
Para saber mais sobre como usar o Azure com o Java, visite [Azure para desenvolvedores Java](/java/azure).

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Portal do Azure]: https://portal.azure.com/
[SDK do Java do Banco de Dados de Documentos]: ../documentdb/documentdb-java-application.md
[avaliação gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: http://www.git-scm.com/
[Azure Java Developer Center]: /develop/java/
[Java Developer's Kit 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[Jax-RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Editor Online do Swagger]: http://editor2.swagger.io/
[Postman]: https://www.getpostman.com/
[SDK de Armazenamento para Java]:../storage/blobs/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Editor do Swagger]: http://editor.swagger.io/
[Visual Studio Code]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png

