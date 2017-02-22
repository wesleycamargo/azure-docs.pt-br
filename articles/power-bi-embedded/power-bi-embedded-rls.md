---
title: "Segurança de nível de linha com o Power BI Embedded"
description: "Detalhes sobre segurança de nível de linha com o Power BI Embedded"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 7936ade5-2c75-435b-8314-ea7ca815867a
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 2f0f36e7ffeec52bacc35ac5039cd183976dc3aa
ms.openlocfilehash: c0b3e2de393c53dab4c9e9341269f792603eec18


---
# <a name="row-level-security-with-power-bi-embedded"></a>Segurança de nível de linha com o Power BI Embedded
A RLS (segurança em nível de linha) pode ser usada para restringir o acesso do usuário a dados específicos em um relatório ou conjunto de dados, permitindo que vários usuários diferentes usem o mesmo relatório, mas vendo dados diferentes. O Power BI Embedded agora dá suporte a conjuntos de dados configurados com RLS.

![](media/power-bi-embedded-rls/pbi-embedded-rls-flow-1.png)

Para tirar proveito da RLS, é importante compreender os três conceitos principais: usuários, funções e regras. Vamos examinar cada um com mais detalhes:

**Usuários** – são os usuários finais reais que exibem os relatórios. No Power BI Embedded, os usuários são identificados pela propriedade Nome de usuário em um Token de Aplicativo.

**Funções** – os usuários pertencem a funções. Uma função é um contêiner de regras e pode ter um nome como "Gerente de vendas" ou "Representante de vendas". No Power BI Embedded, os usuários são identificados pela propriedade Funções em um Token de Aplicativo.

**Regras** – funções têm regras e essas regras são os filtros reais aplicados aos dados. Isso pode ser tão simples quanto "País = EUA" ou algo muito mais dinâmico.

### <a name="example"></a>Exemplo
Para o restante deste artigo, forneceremos um exemplo de criação de RLS e o usaremos em um aplicativo incorporado. Nosso exemplo usa o arquivo PBIX [exemplo de análise de varejo](http://go.microsoft.com/fwlink/?LinkID=780547) .

![](media/power-bi-embedded-rls/pbi-embedded-rls-scenario-2.png)

Nosso exemplo de análise de varejo mostra as vendas para todas as lojas em uma cadeia de varejo específica. Sem RLS, não importa que gerente regional entra e exibe o relatório; eles veem os mesmos dados. A cúpula sênior determinou que cada gerente regional veja apenas as vendas das lojas que eles gerenciam e, para fazer isso, podemos usar RLS.

A RLS é criada no Power BI Desktop. Quando o conjunto de dados e o relatório são abertos, podemos alternar para modo de exibição de diagrama a fim de ver o esquema:

![](media/power-bi-embedded-rls/pbi-embedded-rls-diagram-view-3.png)

Eis algumas observações em relação a esse esquema:

* Todas as medidas, como **Total de vendas**, são armazenadas na tabela de fatos **Vendas**.
* Há quatro tabelas de dimensões adicionais relacionadas: **Item**, **Hora**, **Loja** e **Região**.
* As setas nas linhas de relação indicam como os filtros podem fluir de uma tabela para outra. Por exemplo, se um filtro é colocado em **Hora[Data]**, no esquema atual ele filtraria somente valores na tabela **Vendas**. Nenhuma outra tabela seria afetada por esse filtro, já que todas as setas nas linhas de relação apontam em direção à tabela de vendas, e não para fora dela.
* A tabela **Região** indica quem é o gerente de cada região:
  
  ![](media/power-bi-embedded-rls/pbi-embedded-rls-district-table-4.png)

Com base nesse esquema, se aplicamos um filtro na coluna**Gerente regional** e esse filtro corresponder ao usuário que exibe o relatório, ele também filtrará as tabelas **Loja** e **Vendas**, a fim de mostrar somente os dados relacionados ao gerente regional em questão.

Veja como fazer isso:

1. Na guia Modelagem, clique em **Gerenciar Funções**.  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-modeling-tab-5.png)
2. Criar uma nova função chamada **Gerente**.  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-manager-role-6.png)
3. Na tabela **Região**, insira a seguinte expressão DAX: **[District Manager] = Username()**  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-manager-role-7.png)
4. Para verificar se as regras estão funcionando, na guia **Modelagem**, clique em **Exibir como Funções** e digite o seguinte:  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-view-as-roles-8.png)
   
   Os relatórios agora mostrarão dados como se você estivesse conectado como **Paulo Araújo**.

A aplicação do filtro como fizemos aqui filtrará todos os registros nas tabelas **Região**, **Loja** e **Vendas**. No entanto, devido à direção do filtro nas relações entre **Vendas** e **Hora**, as tabelas **Vendas** e **Item** e **Item** e **Hora** não serão filtradas.

![](media/power-bi-embedded-rls/pbi-embedded-rls-diagram-view-9.png)

Que podem ser adequadas para esse requisito; no entanto, se não quisermos que os gerentes vejam itens para os quais não têm vendas, poderíamos ativar a filtragem cruzada bidirecional a relação e fluir o filtro de segurança em ambos os sentidos. Isso pode ser feito editando a relação entre **Vendas** e **Item**, assim:

![](media/power-bi-embedded-rls/pbi-embedded-rls-edit-relationship-10.png)

Agora, os filtros também podem fluir da tabela Vendas para a tabela **Item** :

![](media/power-bi-embedded-rls/pbi-embedded-rls-diagram-view-11.png)

**Observação** Se você estiver usando o modo DirectQuery para seus dados, precisará habilitar a filtragem bidirecional selecionando estas duas opções:

1. **Arquivo** -> **Opções e Configurações** -> **Recursos de Visualização** -> **Habilitar filtragem cruzada em ambas as direções para DirectQuery**.
2. **Arquivo** -> **Opções e Configurações** -> **DirectQuery** -> **Permitir medidas irrestritas no modo DirectQuery**.

Para saber mais sobre filtragem cruzada bidirecional, baixe o whitepaper [Filtragem cruzada bidirecional no SQL Server Analysis Services 2016 e no Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx).

Isso conclui todo o trabalho que precisa ser feito no Power BI Desktop, mas há um mais trabalho que precisa ser feito para tornar as regras de RLS definidas funcionarem no Power BI Embedded. Os usuários são autenticados e autorizados pelo seu aplicativo e Tokens de Aplicativo são usados para conceder acesso de usuário a um relatório de Power BI Embedded específico. O Power BI Embedded não tem nenhuma informação específica sobre quem é o usuário. Para que a RLS funcione, você precisará passar algum contexto adicional como parte de seu token do aplicativo:

* **nome de usuário** (opcional) – usado com RLS, é uma cadeia de caracteres que pode ser usada para ajudar a identificar o usuário ao aplicar regras RLS. Confira Usando segurança de nível de linha com o Power BI Embedded
* **funções** – uma cadeia de caracteres que contém as funções a selecionar ao aplicar regras de segurança em nível de linha. Ao transmitir mais de uma função, elas deverão ser transmitidas como uma matriz de cadeia de caracteres.

Crie o token usando o método [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#Microsoft_PowerBI_Security_PowerBIToken_CreateReportEmbedToken_System_String_System_String_System_String_System_DateTime_System_String_System_Collections_Generic_IEnumerable_System_String__). Se a propriedade de nome de usuário estiver presente, você também deverá passar pelo menos um valor como funções.

Por exemplo, você pode alterar EmbedSample. A linha 55 DashboardController pode ser atualizada de

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

para

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

O token do aplicativo completo será algo parecido com isto:

![](media/power-bi-embedded-rls/pbi-embedded-rls-app-token-string-12.png)

Agora, com todas as partes juntas, quando alguém entrar em nosso aplicativo para exibir esse relatório, só poderá ver os dados que tiver permissão para ver, conforme definido pela nossa segurança em nível de linha.

![](media/power-bi-embedded-rls/pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>Confira também
[RLS (segurança no nível da linha) com Power](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)




<!--HONumber=Jan17_HO1-->


