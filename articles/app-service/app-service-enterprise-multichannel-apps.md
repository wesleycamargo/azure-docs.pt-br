<properties 
	pageTitle="Aplicativos empresariais multicanal" 
	description="Visão geral de como um aplicativo multicanal abrange recursos locais e serviços de software na nuvem." 
	services="app-service" 
	documentationCenter="na" 
	authors="stefsch" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="stefsch"/>

# Criar aplicativos multicanal para a empresa

## Visão geral

Aplicativos empresariais multicanal apresentam dados aos clientes usando várias tecnologias. Consumidores empresariais em plataformas Web, móveis e de API recuperam e processam informações fornecidas por diversas fontes. Essas fontes abrangem sistemas internos executados localmente bem como serviços baseados em nuvem.

Aplicativos empresariais também têm a necessidade de acesso seguro, com funcionários e parceiros se conectando aos dados usando identidades seguras sob o controle direto da empresa.

Aplicativos empresariais em execução no AAS (Serviço de Aplicativo do Azure) podem fornecer todos esses recursos.

O exemplo de aplicativo de viagem de funcionário mostrado abaixo mostra um aplicativo multicanal empresarial protegido pelo AAD (Active Directory do Azure) que se integra com recursos locais, bem como serviços de SaaS (Software-como Serviço) como o Office 365 e Salesforce.

## <a name="acceptablefiles"></a>Controlando o acesso ao Active Directory do Azure

Os usuários do aplicativo de viagem deverão primeiro autenticar em relação a um Active Directory corporativo. Algumas etapas foram executadas para configurar o aplicativo para usar o AAD (Active Directory do Azure):

* Um Active Directory do Azure foi criado para a empresa.
* O Active Directory local da empresa foi federado com o Active Directory do Azure.
* Finalmente, o aplicativo foi registrado com o AAD usando o recurso de integração fácil de AAD do Serviço de Aplicativo do Azure. 

O resultado final é que o aplicativo solicita os usuários a fazer logon no AAD (e, por extensão, o AD corporativo).
	
![Logon no AAD][AADLogin]

Para obter mais informações sobre como configurar a integração do AAS com o AAD, consulte [Integração do AAS com o AAD][AASIntegrationwithAAD].

## <a name="acceptablefiles"></a>Acessando recursos locais

Quando um usuário está conectado no AAD, é exibida uma lista de viagens corporativas planejadas. Como o aplicativo Web está em execução no Microsoft Azure, ele precisa de uma maneira de acessar o SQL Server local, que contém essas informações.

![Dados do SQL Server local][DatafromOnpremisesSqlServer]

O aplicativo é configurado com o recurso de integração ponto a site VNET. Isso habilita o aplicativo a usar a lógica de acesso a dados padrão (no caso, o Entity Framework) para acessar um SQL Server remoto em execução dentro da rede da empresa de forma transparente.

Para obter mais informações sobre a integração ponto a site VNET, consulte [Integração VNET][VNETIntegration].

## <a name="acceptablefiles"></a>Integração com o Office 365

Cada vez que um funcionário cria um registro de viagem, o aplicativo Web cria uma solicitação de viagem em uma lista do SharePoint Online. Há um link no aplicativo que pode ser usado pelos funcionários para acessar facilmente a lista do Sharepoint:

![Link de lista do SharePoint][SharepointListLink]

Clique no link e navegue automaticamente até a lista do SharePoint. Como os funcionários estão conectados em seu AAD corporativo, são transparentemente autenticados no Office 365 usando o token de segurança emitido pelo AAD.

![Link do SharePoint][SharepointList]

O aplicativo Web também cria um documento de viagem em uma biblioteca de documentos do Sharepoint Online.

![Biblioteca de documentos do SharePoint][SharepointDocumentLibrary]

Os ativos criados no SharePoint Online permitem que o aplicativo Web aproveite os recursos do Office 365. Por exemplo um fluxo de trabalho de aprovação/decisão pode ser disparado cada vez que um item é criado na lista do Sharepoint.

Para obter mais informações sobre a integração do Office 365, consulte [Integração do Office 365][Office365Integration]

## <a name="acceptablefiles"></a>Integração com serviços SaaS

As empresas hoje usam uma variedade de serviços SaaS e precisam interagir com dados de SaaS de outros aplicativos. O aplicativo de viagem mostra um exemplo desse cenário. Ele atualiza as informações de conta do cliente no Salesforces sempre que um funcionário planeja viagem para uma conta de cliente.

![Integração do Salesforce][SalesforceIntegration]

A conta de Salesforce da empresa é configurada com o AAD para permitir autenticação transparente no Salesforce usando as credenciais do AAD. Como resultado, quando os funcionários são registrados no aplicativo de viagem usando o AAD, o aplicativo pode ler e gravar dados armazenados no Salesforce.

Para obter mais informações sobre a integração do SaaS, consulte [Integração do SaaS][SaaSIntegration]

## <a name="NextSteps"></a>Próximas etapas

Para obter mais informações, consulte [Serviços de Aplicativo do Azure][AzureApplicationServices]
 
[AASIntegrationwithAAD]: http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/
[VNETIntegration]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
[Office365Integration]: http://azure.microsoft.com/documentation/articles/app-service-logic-connector-office365/
[SaaSIntegration]: http://azure.microsoft.com/documentation/articles/app-service-logic-connector-salesforce/
[AzureApplicationServices]: ../app-service-cloud-app-platform.md

[AADLogin]: ./media/app-service-enterprise-multichannel-apps/01aAADLogin.png
[DatafromOnpremisesSqlServer]: ./media/app-service-enterprise-multichannel-apps/02aDatafromOnpremisesSqlServer.png
[SharepointListLink]: ./media/app-service-enterprise-multichannel-apps/03aSharepointListLink.png
[SharepointList]: ./media/app-service-enterprise-multichannel-apps/04aSharepointList.png
[SharepointDocumentLibrary]: ./media/app-service-enterprise-multichannel-apps/05aSharepointDocumentLibrary.png
[SalesforceIntegration]: ./media/app-service-enterprise-multichannel-apps/06aSalesforceIntegration.png

<!---HONumber=August15_HO6-->