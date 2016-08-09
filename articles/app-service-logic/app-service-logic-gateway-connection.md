<properties
   pageTitle="Conexão do gateway de dados local com Aplicativos Lógicos | Microsoft Azure"
   description="Saiba como criar uma conexão ao gateway de dados no local de um aplicativo lógico."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# Conectar-se ao gateway de dados local para Aplicativos Lógicos

Conectores de aplicativos lógicos compatíveis permitem que você configure a conexão para acessar os dados no local por meio do gateway de dados local. As etapas a seguir explicam como instalar e configurar o gateway de dados local para trabalhar com um aplicativo lógico.

## Pré-requisitos

* É necessário usar um endereço de email corporativo ou de estudante no Azure para associar o gateway de dados local com sua conta (conta do Azure Active Directory)
    * Se estiver usando uma Conta da Microsoft (por exemplo, @outlook.com, @live.com), você poderá usar sua conta do Azure para criar um endereço de email corporativo ou de estudante [seguindo estas etapas](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [AZURE.WARNING] No momento, há uma limitação em que a instalação do gateway só será concluída quando usando uma conta que tenha sido registrada com o Power BI. Enquanto isso, registre qualquer conta com "Power BI Gratuito" para concluir a instalação.

* É necessário ter o gateway de dados local [instalado em um computador local](app-service-logic-gateway-install.md).
* O gateway não deve ter sido declarado por outro gateway de dados local do Azure ([a declaração ocorre com a criação da etapa 2 abaixo](#2-create-an-azure-on-premises-data-gateway-resource)) - uma instalação só pode ser associada a um recurso de gateway.

## Instalando e configurando a conexão

### 1\. Instale o gateway de dados local

Informações sobre como instalar o gateway de dados local podem ser encontradas [neste artigo](app-service-logic-gateway-install.md). O gateway deve ser instalado em um computador local antes de continuar com o restante das etapas.

### 2\. Crie um recurso de gateway de dados local do Azure

Uma vez instalado, você deve associar sua assinatura do Azure com o gateway de dados local.

1. Faça logon no Azure usando o mesmo endereço de email corporativo ou escolar usado durante a instalação do gateway
1. Clique no botão de recurso **Novo**
1. Procure e selecione o **gateway de dados local**
1. Preencha as informações para associar o gateway à sua conta e selecione o **Nome de Instalação** apropriado

    ![Conexão ao Gateway de Dados Local][1]
1. Clique no botão **Criar** para criar o recurso

### 3\. Crie uma conexão com o aplicativo lógico no designer

Agora que sua assinatura do Azure está associada a uma instância do gateway de dados local, você pode criar uma conexão a ele de dentro de um aplicativo lógico.

1. Abra um aplicativo lógico e escolha um conector que ofereça suporte à conectividade local (na data de publicação deste artigo, o SQL Server)
1. Marque a caixa de seleção **Conectar por meio do gateway de dados local**

    ![Criação de Gateway do Designer de Aplicativo Lógico][2]
1. Selecione o **Gateway** ao qual se conectar e preencha outras informações de conexão necessárias
1. Clique em **Criar** para criar a conexão

A conexão deve ter sido configurada com êxito para uso em seu aplicativo lógico.

## Próximas etapas
- [Exemplos comuns e cenários de aplicativos lógicos](app-service-logic-examples-and-scenarios.md)
- [Recursos de integração corporativa](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG

<!---HONumber=AcomDC_0727_2016-->