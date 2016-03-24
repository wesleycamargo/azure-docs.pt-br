<properties
	pageTitle="Crie e configure as APIs gerenciadas pela Microsoft e pela TI no PowerApps Enterprise | Microsoft Azure"
	description="Saiba mais sobre as APIs disponíveis no PowerApps e como registrá-las no portal do Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="guayan"/>

# Registre uma API gerenciada pela Microsoft ou por TI
Há APIs **gerenciadas pela Microsoft** e **gerenciadas pela TI**. Quando você habilita o PowerApps Enterprise, as APIs gerenciadas pela Microsoft são disponibilizadas para você automaticamente. A memória, conectividade, confiança e muito mais são gerenciados automaticamente para você. A próxima etapa é inserir as configurações específicas de usuário, como uma conta e a senha do Twitter.

Ao usar as APIs gerenciadas pela TI, você controla e monitora tudo, incluindo a memória, conectividade, confiança e muito mais. As APIs gerenciadas pela TI também abrangem as APIs que podem se conectar a um sistema local, como o SQL Server e o SharePoint Server.

> [AZURE.SELECTOR]
- [APIs gerenciadas](../articles/power-apps/powerapps-register-from-available-apis.md)
- [APIs em seu ASE](../articles/power-apps/powerapps-register-api-hosted-in-app-service.md)
- [APIs do Swagger](../articles/power-apps/powerapps-register-existing-api-from-api-definition.md)

Neste tópico, nos concentramos na primeira opção - **registrar as APIs gerenciadas pela Microsoft e as APIs gerenciadas pela TI**.

#### Pré-requisitos para iniciar

- Inscreva-se no [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Crie um [ambiente de serviço de aplicativo](powerapps-get-started-azure-portal.md).


## Exiba as APIs gerenciadas pela Microsoft disponíveis
As APIs **gerenciadas pela Microsoft** são fornecidas com o PowerApps Enterprise e também são hospedadas pela Microsoft. Em muitos cenários, as APIS gerenciadas pela Microsoft são ideais para seus aplicativos. Por exemplo, se seu aplicativo envia um tweet, faz upload de um arquivo para o OneDrive ou exibe dados de um arquivo do Excel, então essas APIs gerenciadas pela Microsoft são uma boa opção.

Alguns benefícios extras abrangem:

- Você obtém todas as APIs gerenciadas pela Microsoft APIs disponíveis para você registrar sua própria instância. 
- Os recursos, incluindo a rede, memória ou as configurações de segurança, são monitorados automaticamente. Por exemplo, se você precisar de mais memória para exibir dados do Excel em seu aplicativo, mais memória será adicionada automaticamente. 
- Uma relação de confiança entre seu aplicativo e a API, como o Office e o Twitter, é criada automaticamente. 


#### APIs gerenciadas pela Microsoft

API | Descrição | Link de etapas
--- | --- | ---
![][31] | **Dropbox**<br/><br/> você pode obter, atualizar e excluir itens e muito mais. | [**Guia de introdução**](powerapps-create-api-dropbox.md)
![][32] | **DynamicsCRM Online**<br/><br/> você pode obter, atualizar, excluir itens e muito mais. | [**Guia de introdução**](powerapps-create-api-crmonline.md)
![][33] | **Excel**<br/><br/> você pode obter, atualizar, excluir itens e muito mais. | [**Guia de introdução**](powerapps-create-api-excel.md)
![][34] | **Google Drive**<br/><br/> você pode obter, atualizar, excluir itens e muito mais. | [**Guia de introdução**](powerapps-create-api-googledrive.md)
![][35] | **Microsoft Translator**<br/><br/>Traduz texto, detecta idiomas e muito mais. | [**Guia de introdução**](powerapps-create-api-microsofttranslator.md)
![][36] | **Office 365 Outlook**<br/><br/>Gerenciar seu email. | [**Guia de introdução**](powerapps-create-api-office365-outlook.md)
![][37] | **Usuários do Office365**<br/><br/>Acesse perfis de usuário, seus gerentes, seus relatórios diretos e muito mais. | [**Guia de introdução**](powerapps-create-api-office365-users.md)
![][38] | **OneDrive**<br/><br/> você pode obter, atualizar, excluir itens e muito mais. | [**Guia de introdução**](powerapps-create-api-onedrive.md)
![][39] | **Salesforce**<br/><br/> Você pode obter, atualizar, excluir itens e muito mais. | [**Guia de introdução**](powerapps-create-api-salesforce.md)
![][40] | **SharePoint Online**<br/><br/> você pode obter, atualizar, excluir itens e muito mais. | [**Guia de introdução**](powerapps-create-api-sharepointonline.md)
![][43] | **Twitter**<br/><br/> Envie e pesquise tweets, consulte seguidores e muito mais. | [**Guia de introdução**](powerapps-create-api-twitter.md)


## Exiba as APIs disponíveis gerenciadas pela TI
As APIs **gerenciadas pela TI** são controladas e gerenciadas por você. Elas não são executadas no ambiente gerenciado pela Microsoft. Em alguns cenários, o uso dessas APIs em seu próprio ambiente gerenciado pela TI pode atender às necessidades de seus aplicativos. Por exemplo, seu aplicativo usa a API do Twitter e você precisa usar a chave de organização do Twitter (em vez da chave do Twitter da Microsoft). Nessa situação, é melhor configurar a API do Twitter como uma API gerenciada pela TI. Em outro exemplo, seu aplicativo usa a API do SQL Server para se conectar a um banco de dados local. Em um ambiente gerenciado pela TI, você pode configurar uma rede virtual ou usar o Express Route para se conectar local. A escolha é sua.

Alguns benefícios extras abrangem:

- Os recursos, incluindo a rede, memória ou as configurações de segurança, são monitorados por você. Por exemplo, se você precisa de mais memória para exibir os dados do Excel em seu aplicativo, você controla a quantidade de memória para adicionar em seu ambiente. 
- Você configura a relação de confiança e controla a segurança entre seus aplicativos e a API. Por exemplo, você determina se a API do Office365 pode ser gerenciada pela Microsoft (uma relação de confiança automática) ou usa a API do Office365 em seu próprio ambiente (crie sua própria relação de confiança). 
- **Todas** as APIs gerenciadas pela Microsoft também podem ser gerenciadas pela TI. Por exemplo, se quiser criar sua própria instância do Office365 e ter controle total sobre essa instância, você pode. Você pode usar sua API gerenciada pela TI e a API gerenciada pela Microsoft do Office365 no mesmo ambiente. Ela realmente depende das necessidades do seu aplicativo.
- Ao se conectar a sistemas locais ou usando a API de pesquisa do Bing, você controla a segurança, autenticação, licenciamento e muito mais.


#### APIs gerenciadas pela TI
> [AZURE.NOTE] Lembre-se, **todas** as APIs gerenciadas pela Microsoft também podem ser gerenciadas pela TI. As seguintes APIs são gerenciadas apenas pela TI. Elas não podem ser gerenciadas pela Microsoft.

API | Descrição | Link de etapas
--- | --- | ---
![][30] | **Pesquisa do Bing**<br/><br/>Incorpore os resultados da pesquisa, adicione a funcionalidade de pesquisa e muito mais. | [**Guia de introdução**](powerapps-create-api-bingsearch.md)
![][42] | **SQL Server**<br/><br/>Você pode obter, atualizar, excluir itens e muito mais. | [**Guia de introdução**](powerapps-create-api-sqlserver.md)
![][41] | **SharePoint Server**<br/><br/>você pode obter, atualizar, excluir itens e muito mais. | [**Guia de introdução**](powerapps-create-api-sharepointserver.md)


#### Por que registrar suas próprias instâncias

Usar as APIs integradas gerenciadas pela Microsoft é conveniente. Dito isso, o registro de suas próprias instâncias como APIs gerenciadas pela TI traz muitos benefícios. Em um alto nível, é recomendável que você crie APIs gerenciadas pela TI quando desejar:

- Ter a capacidade de gerenciamento completa sobre as APIs, incluindo o acesso do usuário, segurança ao se conectar a outros sistemas, limites de chamadas às APIs, monitoramento e recursos avançados, como políticas, e muito mais.
- Acesse dados local, visto que o ambiente de serviço de aplicativo dá suporte a redes virtuais.
- Configure as APIs para usuários comerciais, que podem não ser capazes de usá-las por si.

A tabela a seguir compara os recursos das APIs gerenciadas pela Microsoft e pela TI:

| Recurso | Gerenciado pela Microsoft | TI gerenciada |
| ---------- | ----------------- | ------------ |
| Limites de chamada à API | Definidos pela Microsoft | Definidos por conta própria (por meio de políticas) |
| Traga sua própria chave ao se conectar ao SaaS | Sem suporte | Com suporte |
| Acesso de usuário da API | Habilitado para todos | Totalmente gerenciável no nível de usuário e do grupo do AAD |
| Monitoramento da API | Sem suporte | Com suporte |
| Políticas de API | Sem suporte | Com suporte |
| Acesso de usuário de conexão | Somente exibição | Totalmente gerenciável no nível de usuário e do grupo do AAD |
| Gerenciamento de conexão | Somente exibição | Totalmente gerenciável |


## Registrar uma API gerenciada pela Microsoft ou pela TI

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta comercial (*seuNomedeUsuário*@*SuaEmpresa*.com). Você entrará automaticamente na assinatura de sua empresa.
2. Selecione **Navegar**, **PowerApps** e então selecione **Gerenciar APIs**:  
![][17]
3. Na seção Gerenciar APIs, selecione **Adicionar**:  
![][18]
4. Em **Adicionar API**, insira as propriedades da API:  

	- Em **Nome**, insira um nome para sua API. Observe que o nome inserido está incluído na URL de tempo de execução da API. Verifique o nome significativo e exclusivo em sua organização.
	- Na seção **Fonte**, selecione **Das APIs disponíveis**:  
	![][19]
5. Selecione **API** e, em seguida, escolha a API que você deseja registrar:  
![][20]
6. Selecione sua API específica e adicione as propriedades configuráveis.
7. Selecione **Adicionar** para concluir estas etapas.

> [AZURE.TIP] Ao registrar uma API, você está registrando a API para seu ambiente de serviço de aplicativo. Uma vez no ambiente de serviço de aplicativo, ela poderá ser usada por outros aplicativos no mesmo ambiente de serviço de aplicativo.


## Resumo e próximas etapas

Neste tópico, você viu como registrar sua própria instância das APIs disponíveis que o PowerApps fornece integradas. Estes são alguns tópicos e recursos relacionados para saber mais sobre o PowerApps:


- [Configurar as propriedades da API](powerapps-configure-apis.md)
- [Dar aos usuários acesso à API](powerapps-manage-api-connection-user-access.md)
- [Começar a criar seus aplicativos no PowerApps](https://powerapps.microsoft.com/tutorials/)


<!--References-->

[17]: ./media/powerapps-register-from-available-apis/registered-apis-part.png
[18]: ./media/powerapps-register-from-available-apis/add-api-button.png
[19]: ./media/powerapps-register-from-available-apis/add-api-blade.png
[20]: ./media/powerapps-register-from-available-apis/add-api-select-from-marketplace-blade.png
[30]: ./media/powerapps-register-from-available-apis/bingsearch.png
[31]: ./media/powerapps-register-from-available-apis/dropbox.png
[32]: ./media/powerapps-register-from-available-apis/dynamicscrmonline.png
[33]: ./media/powerapps-register-from-available-apis/excel.png
[34]: ./media/powerapps-register-from-available-apis/googledrive.png
[35]: ./media/powerapps-register-from-available-apis/microsofttranslator.png
[36]: ./media/powerapps-register-from-available-apis/office365outlook.png
[37]: ./media/powerapps-register-from-available-apis/office365users.png
[38]: ./media/powerapps-register-from-available-apis/onedrive.png
[39]: ./media/powerapps-register-from-available-apis/salesforce.png
[40]: ./media/powerapps-register-from-available-apis/sharepointonline.png
[41]: ./media/powerapps-register-from-available-apis/sharepointserver.png
[42]: ./media/powerapps-register-from-available-apis/sqlserver.png
[43]: ./media/powerapps-register-from-available-apis/twitter.png

<!----HONumber=AcomDC_0309_2016-->