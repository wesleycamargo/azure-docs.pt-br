---
title: Alterar feed para recursos HL7 FHIR - Azure DocumentDB | Microsoft Docs
description: "Saiba como configurar notificações de alteração para registros médicos dos pacientes HL7 FHIR usando os Aplicativos Lógicos do Azure, o DocumentDB e o Barramento de Serviço."
keywords: hl7 fhir
services: documentdb
author: hedidin
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 0d25c11f-9197-419a-aa19-4614c6ab2d06
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: b-hoedid
translationtype: Human Translation
ms.sourcegitcommit: c25274ad48edb0c89e3f177277af1a4ae5fb3eec
ms.openlocfilehash: dafd6aa1172661e82bccb35dc29fd59b2c04dd6e


---

# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-documentdb"></a>Notificação de pacientes de alterações de registro de assistência médica HL7 FHIR usando Aplicativos Lógicos e o DocumentDB

Recentemente, o MVP do Azure Howard Edidin foi contatado por uma organização de saúde que queria adicionar uma nova funcionalidade ao seu portal do paciente. Eles precisavam enviar notificações aos pacientes quando o registro de saúde deles fosse atualizado, e precisavam que os pacientes pudessem assinar essas atualizações. 

Este artigo percorre a solução de notificação de feed de alteração criada para esta organização de saúde usando o DocumentDB, os Aplicativos Lógicos e o Barramento de Serviço. 

## <a name="project-requirements"></a>Requisitos do projeto
- Os provedores enviam documentos C-CDA (Consolidated-Clinical Document Arquitecture) HL7 em formato XML. Os documentos C-CDA englobam praticamente qualquer tipo de documento clínico, incluindo documentos clínicos como históricos familiares e registros de vacinação, bem como documentos administrativos, de fluxo de trabalho e financeiros. 
- Os documentos C-CDA são convertidos em [Recursos HL7 FHIR](http://hl7.org/fhir/2017Jan/resourcelist.html) no formato JSON.
- Os documentos de recurso FHIR modificados são enviados por email no formato JSON.

## <a name="solution-workflow"></a>Fluxo de trabalho da solução 

Em um alto nível, o projeto exigiu as seguintes etapas de fluxo de trabalho: 
1. Converta os documentos C-CDA em recursos FHIR.
2. Execute uma sondagem de gatilho recorrente para recursos FHIR modificados. 
2. Chame um aplicativo personalizado, FhirNotificationApi, para conectar-se ao DocumentDB e consulte documentos novos ou modificados.
3. Salve a resposta na fila do Barramento de Serviço.
4. Sondagem de novas mensagens na fila do Barramento de Serviço.
5. Envie notificações por email para pacientes.

## <a name="solution-architecture"></a>Arquitetura da solução
Essa solução requer três Aplicativos Lógicos atender aos requisitos acima e concluir o fluxo de trabalho da solução. Os três aplicativos lógicos são:
1. **Aplicativo de mapeamento de FHIR HL7**: recebe o documento HL7 C-CDA, transforma-a para o recurso de FHIR e salva-o em DocumentDB.
2. **Aplicativo EHR**: consulta o repositório de DocumentDB FHIR e salva a resposta para uma fila do Barramento de Serviço. Esse aplicativo lógico usa um [aplicativo de API](#api-app) para recuperar documentos novos e alterados.
3. **Aplicativo de notificação do processo**: envia uma notificação por email com os documentos de recursos FHIR no corpo.

![Os três Aplicativos Lógicos usados nesta solução saúde HL7 FHIR](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Serviços do Azure usados na solução

#### <a name="documentdb"></a>DocumentDB
O DocumentDB é o repositório para os recursos FHIR, conforme mostrado na figura a seguir.

![A conta do Azure DocumentDB usada neste tutorial de assistência médica FHIR HL7](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-account.png)

#### <a name="logic-apps"></a>Aplicativos Lógicos
Os Aplicativos Lógicos lidam com o processo de fluxo de trabalho. As capturas de tela a seguir mostram os Aplicativos Lógicos criados para esta solução. 


1. **Aplicativo de mapeamento de FHIR HL7**: recebem o documento HL7 C-CDA e transformá-la a um recurso FHIR usando o Enterprise Integration Pack para Aplicativos Lógicos. O Enterprise Integration Pack manipula o mapeamento de C-CDA para recursos FHIR.

    ![O aplicativo lógico usado para receber registros médicos HL7 FHIR](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-logic-apps-json-transform.png)


2. **Aplicativo EHR**: consultar o repositório DocumentDB FHIR e salvar a resposta para uma fila do Barramento de Serviço. O código do aplicativo GetNewOrModifiedFHIRDocuments está abaixo.

    ![O Aplicativo Lógico usado para consultar o Azure DocumentDB](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-logic-apps-api-app.png)

3. **Aplicativo de notificação do processo**: enviar uma notificação por email com os documentos de recursos FHIR no corpo.

    ![O aplicativo lógico que envia email pacientes com o recurso HL7 FHIR no corpo](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Barramento de Serviço
A figura a seguir mostra os pacientes fila. O valor da propriedade Tag é usado para o assunto do email.

![A fila do Barramento de Serviço usada neste tutorial do HL7 FHIR](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>Aplicativo de API
Um aplicativo de API se conecta ao DocumentDB e consultas documentos de FHIR novos ou modificados por tipo de recurso. Este aplicativo tem um controlador, **FhirNotificationApi**, com uma operação **GetNewOrModifiedFhirDocuments**, veja [fonte do aplicativo de API](#api-app-source).

Estamos usando o [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/en-us/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) classe da API do .NET do DocumentDB. Para obter mais informações, consulte o [artigo de feed de alterações do DocumentDB](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-change-feed). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Operação de GetNewOrModifiedFhirDocuments

**Entradas**
- DatabaseId
- CollectionId
- Nome do Tipo de Recurso FHIR HL7
- Booliano: Começar do início
- INT: Número de documentos retornados

**Saídas**
- Sucesso: Código de Status: 200, Resposta: Lista de Documentos (Matriz JSON)
- Falha: Código de Status: 404, Resposta: "Nenhum Documento encontrado para Tipo de Recurso '*nome do recurso '*"

<a id="api-app-source"></a>

**Fonte do aplicativo de API**

```C#

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///        or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();    
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Testar o FhirNotificationApi 

A imagem a seguir demonstra como o swagger foi usado para testar a [FhirNotificationApi](#api-app-source).

![O arquivo de Swagger usado para testar o aplicativo de API](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Painel do portal do Azure

A imagem a seguir mostra todos os serviços do Azure para essa solução em execução no portal do Azure.

![O portal do Azure mostrando todos os serviços usados neste tutorial HL7 FHIR](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-portal.png)


## <a name="summary"></a>Resumo

- Você aprendeu que DocumentDB tem suporte nativo para notificações para novo ou modificado documentos e como é fácil usar. 
- Aproveitando os Aplicativos Lógicos, você pode criar fluxos de trabalho sem escrever nenhum código.
- Uso de filas do Barramento de Serviço do Azure para lidar com a distribuição para os documentos FHIR HL7.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o DocumentDB, veja a [home page do DocumentDB](https://azure.microsoft.com/en-us/services/documentdb/). Para saber mais sobre os Aplicativos Lógicos, veja [Aplicativos Lógicos](https://azure.microsoft.com/en-us/services/logic-apps/).





<!--HONumber=Feb17_HO2-->


