<properties
   pageTitle="O que é o Microsoft Power BI Embedded?"
   description="O Power BI Embedded possibilita que você integre relatórios do Power BI a seus aplicativos móveis ou Web para que não precise compilar soluções personalizadas para visualizar dados para os seus usuários"
   services="power-bi-embedded"
   documentationCenter=""
   authors="mgblythe"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="mblythe"/>

# O que é o Microsoft Power BI Embedded?

Com **Power BI Embedded**, você pode integrar relatórios do Power BI diretamente a seus aplicativos móveis ou Web.

![](media\powerbi-embedded-whats-is\what-is.png)

O **Power BI Embedded** é um serviço do Azure que permite que ISVs e desenvolvedores de aplicativo revelem experiências de dados do Power BI em seus aplicativos. Como desenvolvedor, você criou aplicativos, e esses aplicativos têm seus próprios usuários e um conjunto distinto de recursos. Esses aplicativos também podem ter alguns elementos de dados internos, como gráficos e relatórios que agora podem ser alimentados pelo Microsoft Power BI Embedded. Os usuários não precisam de uma conta do Power BI para usar seu aplicativo. Eles podem continuar a entrar no aplicativo como antes e exibir e interagir com a experiência de emissão de relatórios do Power BI sem a necessidade de qualquer licença adicional.

## Licenciamento do Microsoft Power BI Embedded

No modelo de uso do **Microsoft Power BI Embedded**, o licenciamento para o Power BI não é de responsabilidade do usuário final. Em vez disso, **renderizações** são compradas pelo desenvolvedor do aplicativo que está consumindo os elementos visuais e são cobradas da assinatura proprietária de tais recursos.

## Modelo Conceitual do Microsoft Power BI Embedded

![](media\powerbi-embedded-whats-is\model.png)

Como qualquer outro serviço no Azure, recursos para o Power BI Embedded são provisionados por meio de [APIs do ARM](https://msdn.microsoft.com/library/mt712306.aspx). Nesse caso, o recurso que você provisiona é uma **Coleção de Espaços de Trabalho do Power BI**.

## Coleção de Espaços de Trabalho

Uma **Coleção de Espaços de Trabalho** é o contêiner do Azure de nível mais elevado para recursos que contém 0 ou mais **Espaços de Trabalho**. Uma **Coleção** de **Espaços de Trabalho** tem todas as propriedades padrão do Azure, bem como o seguinte:

-	**Chaves de Acesso** – chaves usadas ao chamar com segurança as APIs do Power BI (descritas em uma seção posterior).
-	**Usuários** – os usuários do AAD (Azure Active Directory) que têm direitos de administrador para gerenciar a Coleção de Espaços de Trabalho do Power BI por meio do portal do Azure ou da API do ARM.
-	**Região** – como parte do provisionamento de uma **Coleção de Espaços de Trabalho**, você pode selecionar uma região na qual ela será provisionada. Para obter mais informações, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

## Espaço de trabalho

Um **Espaço de Trabalho** é um contêiner de conteúdo do Power BI, que pode incluir conjuntos de dados, relatórios e painéis. Um **Espaço de Trabalho** fica vazio logo após ser criado pela primeira vez. Durante a Preview, você criará todo o conteúdo usando o Power BI Desktop e o carregará em um dos seus espaços de trabalho usando as [APIs REST do Power BI](http://docs.powerbi.apiary.io/reference).

## Usando Coleções de Espaços de Trabalho e Espaços de Trabalho
**Coleções de Espaços de Trabalho** e **Espaços de Trabalho** são contêineres de conteúdo que são usados e organizados da forma que melhor se encaixar no design do aplicativo que você está criando. Haverá muitas maneiras diferentes em que você poderia organizar o conteúdo deles. Você pode optar por colocar todo o conteúdo dentro de um espaço de trabalho e, depois, usar tokens de aplicativo para subdividir ainda mais o conteúdo entre seus clientes. Você também pode optar por colocar todos os seus clientes em espaços de trabalho separados, para que haja alguma separação entre eles. Ou, você pode optar por organizar os usuários por região em vez de organizá-los por cliente. Esse design flexível permite que você escolha a melhor maneira de organizar o conteúdo.

## Conjuntos de Dados em Cache

Conjuntos de dados armazenados em cache podem ser usados em Preview. No entanto, você não pode atualizar os dados armazenados em cache após eles serem carregados no **Microsoft Power BI Embedded**.

## Autenticação e autorização com tokens de aplicativo

O **Microsoft Power BI Embedded** transfere para o seu aplicativo para executar toda a autorização e autenticação de usuário necessárias. Não há nenhum requisito explícito de que os usuários finais sejam os clientes do Azure Active Directory (Azure AD). Em vez disso, o aplicativo dá ao **Microsoft Power BI Embedded** autorização expressa para renderizar um relatório do Power BI pelo uso de **Tokens de Autenticação de Aplicativo (Tokens de Aplicativo)**. Esses **Tokens de Aplicativo** são criados conforme necessário, quando seu aplicativo deseja renderizar um relatório. Consulte [Tokens de Aplicativo](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

**Tokens de Autenticação de Aplicativo (Tokens de Aplicativo)** são usados para autenticação com o **Microsoft Power BI Embedded**. Há três tipos de **Tokens de Aplicativo**:

1.	Tokens de Provisionamento – Usados ao provisionar um novo **Espaço de Trabalho** em uma **Coleção de Espaços de Trabalho**
2.	Tokens de Desenvolvimento – Usados ao fazer chamadas diretamente para as **APIs REST do Power BI**
3.	Tokens de Inserção - usados ao fazer chamadas para renderizar um relatório no iframe inserido

Esses tokens são usados para as várias fases de suas interações com **Microsoft Power BI Embedded**. Os tokens são projetados para que você possa delegar permissões do seu aplicativo para o Power BI. Para obter mais informações, consulte o [Fluxo de Tokens de Aplicativo](power-bi-embedded-app-token-flow.md).

## Consulte também
- [Cenários comuns do Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
- [Introdução ao Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0907_2016-->