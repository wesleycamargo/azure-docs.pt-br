<properties 
	pageTitle="Solução de problemas do Analytics: a ferramenta de pesquisa avançada do Application Insights | Microsoft Azure" 
	description="Problemas com a Application Insights Analytics? Comece por aqui." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2016" 
	ms.author="awills"/>


# Solução de problemas do Analytics no Application Insights


Problemas com a [Application Insights Analytics](app-insights-analytics.md)? Comece por aqui. O Analytics é uma ferramenta de pesquisa avançada do Visual Studio Application Insights.



## Limites

* No momento, os resultados da consulta são limitados apenas a uma semana de dados anteriores.
* Navegadores em que testamos: edições mais recentes do Chrome, Edge e Internet Explorer.

## "Erro inesperado"

![Tela de erro inesperado](./media/app-insights-analytics-troubleshooting/010.png)

Ocorreu um erro interno durante o tempo de execução do portal. Exceção sem tratamento.

* Limpe o cache do navegador. 

## 403\... tente recarregar

![403\... tente recarregar](./media/app-insights-analytics-troubleshooting/020.png)

Ocorreu um erro de autenticação (durante a autenticação ou durante a geração de token de acesso). O portal pode não ter como recuperar-se sem alterar as configurações do navegador.

* Verifique se os cookies de terceiros estão habilitados no navegador. 

    Consulte [como desabilitar cookies de terceiros](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), porém observe que precisamos **habilitá-los**.

## 403\... verifique se a zona de segurança

![403\... verifique a zona de segurança](./media/app-insights-analytics-troubleshooting/030.png)

Ocorreu um erro de autenticação (durante a autenticação ou durante a geração de token de acesso). O portal pode não ter como recuperar-se sem alterar as configurações do navegador.

1. Verifique se os cookies de terceiros estão habilitados no navegador. 

    Consulte [como desabilitar cookies de terceiros](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), porém observe que precisamos **habilitá-los**.

2. Você usou um favorito, indicador ou link salvo para abrir o portal do Analytics? Você entrou com credenciais diferentes daquelas usadas ao salvar o link?

 2. Tente usar uma janela do navegador privada/anônima (depois de fechar todas as janelas desse tipo). Você precisará fornecer suas credenciais.

 2. Abra outra janela do navegador (comum) e vá para [Azure](https://portal.azure.com). Saia. Em seguida, abra o link e entre com as credenciais corretas.

2. Os usuários do Edge e do Internet Explorer também podem receber esse erro quando não há suporte para as configurações de zona confiável.

	Verifique se ambos o [portal do Analytics](https://analytics.applicationinsights.io) e o [portal do Azure Active Directory](https://portal.azure.com) estão na mesma zona de segurança:

 * No Internet Explorer, abra **Opções da Internet**, **Segurança**, **Sites confiáveis** e **Sites**:

    ![Caixa de diálogo Opções da Internet, adicionando um site aos Sites Confiáveis](./media/app-insights-analytics-troubleshooting/033.png)

    Na Lista de sites, se qualquer uma das seguintes URLs estiverem incluídas, verifique se as outras também estão:

    https://analytics.applicationinsights.io<br/> https://login.microsoftonline.com<br/> https://login.windows.net


## 404 ... Recurso não encontrado

![404\... recurso não encontrado](./media/app-insights-analytics-troubleshooting/040.png)

O recurso de aplicativo foi excluído do Application Insights e não está mais disponível. Isso pode acontecer se você salvou a URL para a página do Analytics.


## 403 ... Sem autorização

![403 \... não autorizado](./media/app-insights-analytics-troubleshooting/050.png)

Você não tem permissão para abrir este aplicativo no Analytics.

* Você obteve o link com outra pessoa? Peça a ela para verificar se você está incluído como [leitor ou colaborador para esse grupo de recursos](app-insights-resources-roles-access-control.md).
* Você salvou o link usando credenciais diferentes? Abra o [Portal do Azure](https://portal.azure.com), saia e tente acessar novamente esse link fornecendo as credenciais corretas.

## 403 ... Armazenamento HTML5

Nosso portal usa sessionStorage e localStorage do HTML5.

* Chrome: configurações, privacidade, configurações de conteúdo.
* Internet Explorer: Opções da Internet, guia Avançado, Segurança, Habilitar o armazenamento DOM


![403\... tente habilitar o armazenamento HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## 404 ... Assinatura não encontrada


![404 \... Assinatura não encontrada](./media/app-insights-analytics-troubleshooting/070.png)

A URL é inválida.

* Abra o recurso de aplicativo no [Portal do Application Insights](https://portal.azure.com). Use então o botão Analytics.

## 404\... a página não existe

![404 \... A página não existe](./media/app-insights-analytics-troubleshooting/080.png)

A URL é inválida.

* Abra o recurso de aplicativo no [Portal do Application Insights](https://portal.azure.com). Use então o botão Analytics.

## Se tudo o mais falhar    

Abra [um tíquete de suporte](app-insights-get-dev-support.md).
 
[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0622_2016-->