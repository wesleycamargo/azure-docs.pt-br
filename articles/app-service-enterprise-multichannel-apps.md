<properties 
	pageTitle="Aplicativos empresariais multicanal" 
	description="Visão geral de como um aplicativo multicanal amplia recursos locais e serviços de software baseados na nuvem." 
	services="app-service" 
	documentationCenter="na" 
	authors="stefsch" 
	writer="tdykstra" 
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

Aplicativos empresariais multicanal apresentam dados para clientes que usam várias tecnologias.  Consumidores empresariais em plataformas Web, móveis e de API recuperam e processam informações fornecidas por diversas fontes.  Essas fontes abrangem sistemas internos executados localmente bem como
serviços baseados em nuvem.  

Aplicativos empresariais também têm a necessidade de acesso seguro, com funcionários e parceiros se conectando aos dados usando identidades seguras sob o controle direto da empresa.

Aplicativos empresariais em execução no AAS (Serviço de Aplicativo do Azure) podem fornecer todos esses recursos.  

O exemplo de aplicativo de viagem para funcionários mostrado abaixo mostra um aplicativo empresarial multicanal protegido pelo AAD (Active Directory do Azure) que se integra a recursos locais e a SaaS (software como serviço), como o Office 365 e 
O Salesforce.

## <a name="acceptablefiles"></a>Controlando o acesso com o Active Directory do Azure

Os usuários do aplicativo de viagem deverão primeiro ser autenticados em um Active Directory corporativo.  Algumas etapas foram executadas para configurar o aplicativo para usar o AAD (Active Directory do Azure):

* Um Active Directory do Azure foi criado para a empresa.
* O Active Directory local da empresa foi federado com o Active Directory do Azure.
* Por fim, o aplicativo foi registrado com o AAD usando o recurso de integração simples do AAD do Serviço de Aplicativo do Azure. 

O resultado final é que o aplicativo solicita que os usuários façam logon no AAD (e, por extensão, no AD corporativo).
	
![AAD Login][AADLogin]

Para obter mais informações sobre como configurar a integração com o AAS do AAD, consulte [Integração do AAS com o AAD][AASIntegrationwithAAD]. 

## <a name="acceptablefiles"></a>Acessando recursos locais

Quando um usuário é conectado com o AAD, é exibida uma lista de viagens corporativas planejadas.  Como o aplicativo Web está em execução no Microsoft Azure, ele precisa de uma maneira de acessar o SQL Server local, que contém essas informações.

![Data from On-premises Sql Server][DatafromOnpremisesSqlServer]

O aplicativo é configurado com o recurso de integração de VNET ponto a site.  Isso habilita o aplicativo a usar a lógica de acesso a dados padrão (no caso, o Entity Framework) para acessar de forma transparente um SQL Server remoto em execução dentro da rede da empresa.

Para obter mais informações sobre a integração de VNET ponto a site, consulte [Integração VNET][VNETIntegration].

## <a name="acceptablefiles"></a>Integração com o Office 365

Cada vez que um funcionário cria um registro de viagem, o aplicativo Web cria uma solicitação de viagem em uma lista do SharePoint Online.  Há um link no aplicativo que os funcionários podem usar para acessar facilmente a lista do Sharepoint:

![SharePoint List Link][SharepointListLink]

Clicar no link levará automaticamente para a lista do SharePoint.  Como os funcionários estão conectados usando o AAD corporativo, eles são autenticados no Office 365 de maneira transparente usando o token de segurança emitido pelo AAD.

![SharePoint List][SharepointList]

O aplicativo Web também cria um documento de viagem em uma biblioteca de documentos do Sharepoint Online.

![SharePoint Document Library][SharepointDocumentLibrary]

Os ativos criados no SharePoint Online permitem que o aplicativo Web utilize os recursos do Office 365.  Por exemplo, um fluxo de trabalho de aprovação/decisão poderia ser acionado sempre que um item fosse criado na lista do Sharepoint.

Para obter mais informações sobre a integração com o Office 365, consulte [Integração com o Office 365][Office365Integration]

## <a name="acceptablefiles"></a>Integração com serviços SaaS

As empresas hoje usam uma variedade de serviços SaaS e precisam interagir com dados de SaaS de outros aplicativos.  O aplicativo de viagem
mostra um exemplo desse cenário.  Ele atualiza as informações da conta do cliente no Salesforce sempre que um funcionário planeja uma viagem com uma conta de cliente.

![Salesforce Integration][SalesforceIntegration]

A conta do Salesforce da empresa é configurada com o AAD para permitir a autenticação transparente no Salesforce usando as credenciais do AAD.  Como resultado, quando os funcionários são conectados ao aplicativo de viagem usando o AAD, o aplicativo pode ler e gravar dados armazenados no Salesforce.

Para obter mais informações sobre a integração com SaaS, consulte [Integração com SaaS][SaaSIntegration]

## <a name="NextSteps"></a>Próximas etapas

Para obter mais informações, consulte [Serviços de Aplicativo do Azure][AzureApplicationServices]
 
[AASIntegrationwithAAD]:http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/
[VNETIntegration]:http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/ 
[Office365Integration]: app-service-cloud-app-platform.md
[SaaSIntegration]: app-service-cloud-app-platform.md
[AzureApplicationServices]: app-service-cloud-app-platform.md

[AADLogin]: ./media/app-service-enterprise-multichannel-apps/01aAADLogin.png
[DatafromOnpremisesSqlServer]: ./media/app-service-enterprise-multichannel-apps/02aDatafromOnpremisesSqlServer.png
[SharepointListLink]: ./media/app-service-enterprise-multichannel-apps/03aSharepointListLink.png
[SharepointList]: ./media/app-service-enterprise-multichannel-apps/04aSharepointList.png
[SharepointDocumentLibrary]: ./media/app-service-enterprise-multichannel-apps/05aSharepointDocumentLibrary.png
[SalesforceIntegration]: ./media/app-service-enterprise-multichannel-apps/06aSalesforceIntegration.png

<!--HONumber=49-->