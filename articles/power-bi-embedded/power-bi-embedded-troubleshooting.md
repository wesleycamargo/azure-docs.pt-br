<properties
   pageTitle="Solução de problemas da Preview do Microsoft Power BI Embedded"
   description="Solução de problemas da Preview do Microsoft Power BI Embedded"
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
   ms.date="04/25/2016"
   ms.author="derrickv"/>

# Solução de problemas da Preview do Microsoft Power BI Embedded
Este artigo fornece respostas para saber como solucionar problemas do **Power BI Inserido**.

<a name="connection-string"/>
## Configurando cadeias de conexão do SQL Server
Para definir uma cadeia de conexão do SQL Server, você precisará seguir um formato específico. Abaixo está um exemplo da cadeia de conexão para o SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Para saber mais sobre cadeias de conexão do SQL Server, consulte os seguintes artigos:

-	[Cadeias de Conexão do SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
-	[SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## Configurando credenciais
Em um caso em que você tenha as credenciais para um ambiente de preparo ou de desenvolvimento, como nome de usuário e senha, talvez seja necessário atualizar as credenciais que correspondem a uma solução de produção.

## Consulte também
- [Introdução a exemplos](power-bi-embedded-get-started-sample.md)
- [O que é o Power BI Embedded?](power-bi-embedded-what-is-power-bi-embedded.md)

<!---HONumber=AcomDC_0504_2016-->