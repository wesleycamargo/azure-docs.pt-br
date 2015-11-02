<properties 
	pageTitle="Solicitação de aumento dos limites da conta do Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba como solicitar um ajuste aos limites do Banco de Dados de Documentos, como o número de coleções permitidas, procedimentos armazenados e cláusulas de consulta." 
	services="documentdb" 
	authors="AndrewHoh" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="anhoh"/>

# Limites da conta do Banco de Dados de Documentos aumentados por solicitação

O [Banco de Dados de Documentos do Microsoft Azure](http://azure.microsoft.com/services/documentdb/) tem um conjunto de limites padrão e imposições de cota. Diversas cotas podem ser ajustadas entrando em contato com o suporte do Azure. Este artigo mostra como solicitar um aumento de limite de conta.

Após ler este artigo, você poderá responder as perguntas a seguir:

-	Quais cotas de conta do Banco de Dados de Documentos podem ser ajustadas entrando em contato com o suporte do Azure?
-	Como posso solicitar um ajuste de cota de conta do Banco de Dados de Documentos?

##<a id="AdjustableQuotas"></a> Cotas de conta do Banco de Dados de Documentos ajustáveis

A tabela a seguir descreve as cotas do Banco de Dados de Documentos que podem ser ajustadas entrando em contato com o suporte do Azure:

|Entidade |Cota (oferta padrão)|
|-------|--------|
|Contas do banco de dados |5
|Número de procedimentos armazenados, gatilhos e UDFs por coleção |25 cada
|Coleções máximas por conta do banco de dados |100
|Armazenamento máximo de documentos por banco de dados (100 coleções) |1 TB
|Número máximo de UDFs por consulta |1
|Número máximo de JOINs por consulta |2
|Número máximo de cláusulas AND por consulta |5
|Número máximo de cláusulas OR por consulta |5
|Número máximo de valores por expressão IN |100
|Número máximo de coleções criadas por minuto |5
|Número máximo de operações de escala por minuto |5

##<a id="RequestQuotaIncrease"></a> Solicitar um ajuste de cota
As etapas a seguir mostram como solicitar um ajuste de cota.

1. No [Portal de visualização do Azure](https://portal.azure.com), clique em **Navegar** e, em seguida, clique em **Ajuda + suporte**.

	![Captura de tela da inicialização da ajuda e suporte](media/documentdb-increase-limits/helpsupport.png)

2. Na folha **Ajuda + suporte**, clique em **Obter suporte**.

	![Captura de tela da criação de um chamado de suporte](media/documentdb-increase-limits/getsupport.png)

3. Na folha **Nova solicitação de suporte**, clique em **Tipo de solicitação** e, em seguida, na folha **Tipo de solicitação**, clique em **Cotas**.

	![Captura de tela do tipo de solicitação de chamado de suporte](media/documentdb-increase-limits/supportrequest1.png)

4. Na folha **Assinatura**, escolha a assinatura que hospeda sua conta do Banco de Dados de Documentos.

	![Captura de tela do seletor de assinatura de chamado de suporte](media/documentdb-increase-limits/supportrequest2.png)

5. Na folha **Recursos**, escolha **Contas do Banco de Dados de Documentos**.

	![Captura de tela do seletor de recurso de chamado de suporte](media/documentdb-increase-limits/supportrequest3.png)

6. Na folha **Plano de suporte**, escolha **Suporte gratuito de cotas**.

	![Captura de tela do seletor de plano de suporte do chamado de suporte](media/documentdb-increase-limits/supportrequest4.png)

7. Na folha **Problema**, escolha a categoria do problema **Solicitações de aumento de cota ou núcleo no Banco de Dados de Documentos**.

	![Captura de tela do seletor de categoria de problema do chamado de suporte](media/documentdb-increase-limits/supportrequest5.png)

8. Na folha **Descrição**, insira uma descrição da solicitação. Certifique-se de incluir os ajustes de cota específicos que você está solicitando, bem como as contas para as quais os ajustes devem ser feitos.

	![Captura de tela da caixa de texto de descrição do chamado de suporte](media/documentdb-increase-limits/supportrequest6.png)

9. Clique em **Criar**.

	![Captura de tela do botão de criação do chamado de suporte](media/documentdb-increase-limits/supportrequest7.png)

Depois que o chamado de suporte tiver sido criado, você deve receber o número de solicitação de suporte por email. Você também pode exibir a solicitação de suporte clicando em **Solicitações de suporte** na folha **Ajuda + suporte**.

![Captura de tela da folha das solicitações de suporte](media/documentdb-increase-limits/supportrequest8.png)
  

##<a name="NextSteps"></a> Próximas etapas
- Para saber mais sobre o Banco de Dados de Documentos, clique [aqui](http://azure.com/docdb).
 

<!---HONumber=Oct15_HO4-->