<properties 
	pageTitle="Data Factory - Regras de nomenclatura | Microsoft Azure" 
	description="Descreve as regras de nomenclatura para entidades de Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - Regras de nomenclatura 
A tabela a seguir fornece regras de nomenclatura para artefatos Data Factory.



Nome | Exclusividade do nome | Verificações de validação
:--- | :-------------- | :----------------
Data Factory | Exclusivo em todo o Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas, isto é, MyDF e mydf referem-se ao mesmo data factory. |<ul><li>Cada data factory está associada a exatamente uma assinatura do Azure.</li><li>Nomes de objeto devem começar com uma letra ou um número e podem conter somente letras, números e o caractere traço (-).</li><li>Cada caractere traço (-) deve ser imediatamente precedido e seguido por uma letra ou um número. Não há permissão para traços consecutivos em nomes de contêiner.</li><li>O nome pode ter de três a 63 caracteres.</li></ul>
Serviços/tabelas/pipelines vinculados | Exclusivo em um mesmo data factory. Os nomes não diferenciam maiúsculas de minúsculas. | <ul><li>Número máximo de caracteres em um nome de tabela: 260.</li><li>Os nomes de objeto devem começar com uma letra, um número ou um sublinhado (\_).</li><li>Os seguintes caracteres não são permitidos: ".", "+", "?", "/", "<", ">","*","%","&",":","\"</li></ul>
Grupo de recursos | Exclusivo em todo o Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas. | <ul><li>Número máximo de caracteres: 1000.</li><li>O nome pode conter letras, dígitos e os seguintes caracteres: “-”, “\_”, “,” e ".".</li></ul>

<!---HONumber=AcomDC_0914_2016-->