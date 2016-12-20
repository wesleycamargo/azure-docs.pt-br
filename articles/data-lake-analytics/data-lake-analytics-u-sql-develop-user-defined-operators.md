---
title: "Desenvolver operadores do U-SQL definidos pelo usuário para trabalhos do Azure Data Lake Analytics | Microsoft Docs"
description: "Saiba como desenvolver operadores definidos pelo usuário para serem usados e reutilizados em trabalhos da Análise Data Lake. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 88a00c2b0a5aac85bbcaef5b21b10f44121c7d38


---
# <a name="develop-u-sql-user-defined-operators-for-azure-data-lake-analytics-jobs"></a>Desenvolver operadores do U-SQL definidos pelo usuário para trabalhos da Análise Azure Data Lake
Saiba como desenvolver operadores definidos pelo usuário para serem usados e reutilizados em trabalhos da Análise Data Lake. Você desenvolverá um operador personalizado para converter nomes de países.

## <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2015, Visual Studio 2013 atualização 4 ou Visual Studio 2012 com Visual C++ instalado
* SDK do Microsoft Azure para .NET versão 2.5 ou posterior.  Instale-o usando o Web Platform Installer.
* Uma conta da Análise Data Lake.  Veja [Introdução à Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md).
* Acompanhe o tutorial [Introdução ao U-SQL Studio da Análise Azure Data Lake](data-lake-analytics-u-sql-get-started.md) .
* Conecte-se ao Azure.
* Carregar os dados de origem, consulte [Introdução ao U-SQL Studio do Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md). 

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>Definir e usar o operador definido pelo usuário no U-SQL
**Para criar e enviar um trabalho do U-SQL**

1. No menu **Arquivo**, clique em **Novo** e em **Projeto**.
2. Escolha o tipo **Projeto U-SQL** .

    ![novo projeto de U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Clique em **OK**. O Visual Studio cria uma solução com um arquivo Script.usql.
4. No **Gerenciador de Soluções**, expanda Script.usql e clique duas vezes em **Script.usql.cs**.
5. Cole o seguinte código no arquivo:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Schwiiz", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Abra o Script.usql e cole o seguinte script U-SQL:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Script.usql** e clique em **Criar Script**.
8. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Script.usql** e clique em **Enviar Script**.
9. Se você ainda não tiver se conectado à sua assinatura do Azure, será solicitada a inserção de suas credenciais de conta do Azure.
10. Clique em **Enviar**. Os resultados do envio e o link do trabalho estarão disponíveis na janela Resultados quando o envio for concluído.
11. Clique no botão Atualizar para ver o status do trabalho mais recente e atualizar a tela.

**Para ver a saída do trabalho**

1. No **Gerenciador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda sua conta do Data Lake Analytics, expanda **Contas de Armazenamento**, clique com o botão direito do mouse no Armazenamento Padrão e clique em **Explorer**.
2. Expanda Exemplos, expanda Saídas e clique duas vezes em **Drivers.csv**.

## <a name="see-also"></a>Confira também
* [Introdução à Análise Data Lake usando o PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução à Análise Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Usar as Ferramentas do Data Lake para Visual Studio para desenvolver aplicativos do U-SQL](data-lake-analytics-data-lake-tools-get-started.md)



<!--HONumber=Nov16_HO3-->


