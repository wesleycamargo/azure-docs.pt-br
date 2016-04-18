<properties
   pageTitle="Sobre o fluxo de tokens de aplicativo no Power BI Embedded"
   description="Sobre o fluxo de tokens de aplicativo no Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Sobre o fluxo de tokens de aplicativo no Power BI Embedded

O serviço **Power BI Embedded** usa **Tokens de Aplicativo** para autenticação e autorização, em vez de usar autenticação explícita do usuário final. No modelo de **Token de Aplicativo**, seu aplicativo gerencia a autenticação e autorização para os usuários finais. Quando necessário, seu aplicativo cria e envia os **Tokens de Aplicativo** que dizem ao nosso serviço para renderizar o relatório solicitado. Esse design não requer que o aplicativo use o **Azure Active Directory** para autorização e autenticação de usuário, embora você possa fazer isso.

**Como funciona o fluxo de chaves dos tokens de aplicativo**

1. Copie as chaves de API para o seu aplicativo. Você pode obter as chaves no **Portal do Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. O token faz a asserção de uma declaração e tem uma data de validade.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. O token é assinado com uma chave de acesso de API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. Solicitações do usuário para exibir um relatório.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.	O token é validado com uma chave de acesso de API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.	O Power BI Embedded envia um relatório para o usuário.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Após o **Power BI Embedded** enviar um relatório para o usuário, o usuário pode exibir o relatório em seu aplicativo personalizado. Por exemplo, se você importou o [exemplo de PBIX Analisando Dados de Vendas](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), o aplicativo Web de exemplo teria essa aparência:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## Consulte também
- [Introdução ao exemplo do Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)
- [O que é o Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Cenários comuns do Microsoft Power BI Embedded Preview](power-bi-embedded-scenarios.md)
- [Introdução ao Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->