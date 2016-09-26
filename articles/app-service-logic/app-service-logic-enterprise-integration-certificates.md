<properties 
	pageTitle="Usando certificados com o Enterprise Integration Pack | Serviço de Aplicativo do Microsoft Azure" 
	description="Saiba como usar certificados com o Enterprise Integration Pack e Aplicativos lógicos" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/06/2016" 
	ms.author="deonhe"/>

# Saiba mais sobre certificados e o Enterprise Integration Pack

## Visão geral
A integração corporativa usa certificados para proteger as comunicações B2B. Você pode usar dois tipos de certificados em seus aplicativos de integração corporativa:
- Certificados públicos, que devem ser adquiridos de uma autoridade de certificação (CA)
- Certificados privados, você pode emitir por conta própria. Esses certificados são chamados de certificados autoassinados.


## O que são certificados?
Certificados são documentos digitais usados para verificar a identidade dos participantes de comunicações eletrônicas, e também servem para proteger as comunicações eletrônicas.

## Por que usar certificados?
Às vezes, as comunicações B2B precisam ser mantidas confidenciais. A integração corporativa usa certificados para proteger essas comunicações de duas formas:
- Criptografando o conteúdo das mensagens
- Assinando digitalmente as mensagens

## Como carregar certificados?

### Certificado público
Para usar um **certificado público** em seus Aplicativos lógicos com recursos de B2B, primeiro você precisa carregá-lo em sua conta de integração. Por outro lado, para usar um **certificado autoassinado**, primeiro ele deve ser carregado no [Cofre de chaves](../key-vault/key-vault-get-started.md "Saiba mais sobre o Cofre de Chaves").

Depois de carregar um certificado, ele estará disponível para proteção de suas mensagens B2B durante a definição de suas propriedades nos [contratos](./app-service-logic-enterprise-integration-agreements.md).

Estas são as etapas detalhadas para carregar os certificados públicos em sua conta de integração depois de fazer logon no Portal do Azure:
1. Selecione **Procurar**![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Insira **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Selecione a **conta de integração** à qual você adicionará o certificado ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Escolha o bloco **Certificados**![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. Selecione o botão **Adicionar** na folha Certificados que é aberta. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Insira um **Nome** para seu certificado, escolha o tipo de certificado (neste exemplo, eu usei o tipo de certificado público) e selecione o ícone de pasta à direita da caixa de texto **Certificado**. O seletor de arquivos é aberto, permitindo que você procure e selecione o arquivo de certificado que deseja carregar em sua conta de integração. Depois de selecionar o certificado, escolha **OK** no seletor de arquivo. Esta ação valida e carrega o certificado em sua conta de integração. Por fim, volte à **folha Adicionar certificado** e selecione o botão **OK**. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)
7. Em um minuto, você verá uma notificação indicando que o carregamento do certificado foi concluído.
8. Escolha o bloco **Certificados**. Após a atualização da página, você deverá ver o certificado recém-adicionado: ![](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)

### Certificado privado

Você pode carregar certificados privados em sua conta de integração, bem como executar as seguintes etapas:

1. [Carregar sua chave privada no Cofre de Chaves](../key-vault/key-vault-get-started.md "Saiba mais sobre o Cofre de Chaves")

	> [AZURE.TIP] Você deve autorizar o serviço Aplicativos Lógicos para executar operações no Cofre de Chaves. Você pode conceder acesso à entidade de serviço de Aplicativos Lógicos usando este comando do PowerShell: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

2. Criar um certificado privado
3. Carregar certificados privados em sua conta de integração

Depois de ter concluído as etapas acima, você poderá usar o certificado privado para criar acordos.

Estas são as etapas detalhadas para carregar os certificados privados em sua conta de integração depois de fazer logon no portal do Azure:
   
1. Selecione **Procurar**![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Insira **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Selecione a **conta de integração** à qual você adicionará o certificado ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Escolha o bloco **Certificados**![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. Selecione o botão **Adicionar** na folha Certificados que é aberta. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Insira um **Nome** para seu certificado, escolha o tipo de certificado (neste exemplo, eu usei o tipo de certificado público) e selecione o ícone de pasta à direita da caixa de texto **Certificado**. O seletor de arquivos é aberto, permitindo que você procure e selecione o arquivo de certificado que deseja carregar em sua conta de integração. Depois de selecionar o certificado, escolha **OK** no seletor de arquivo. Esta ação valida e carrega o certificado em sua conta de integração. Por fim, volte à **folha Adicionar certificado** e selecione o botão **OK**. ![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)
7. Em um minuto, você verá uma notificação indicando que o carregamento do certificado foi concluído.
8. Escolha o bloco **Certificados**. Você deverá ver o certificado recém-adicionado: ![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)

Depois de carregar um certificado, ele estará disponível para proteção de suas mensagens B2B durante a definição de suas propriedades nos [contratos](./app-service-logic-enterprise-integration-agreements.md).


## Próximas etapas
- [Criar um Aplicativo Lógico que use recursos de B2B](./app-service-logic-enterprise-integration-b2b.md)
- [Criar um contrato de B2B](./app-service-logic-enterprise-integration-agreements.md)
- [Saiba mais sobre o Cofre da Chave](../key-vault/key-vault-get-started.md "Saiba mais sobre o Cofre de Chaves")

<!---HONumber=AcomDC_0914_2016-->