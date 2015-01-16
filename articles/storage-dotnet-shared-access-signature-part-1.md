<properties urlDisplayName="" pageTitle="Assinaturas de acesso compartilhado: Noções básicas sobre o modelo SAS | Microsoft Azure" metaKeywords="Azure blob, Azure table, Azure queue, shared access signatures" description="Saiba como delegar acesso a recursos de blob, fila e tabela com assinaturas de acesso compartilhado" metaCanonical="" services="storage" documentationCenter="" title="Part 1: Understanding the SAS Model" solutions="" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />



# Assinaturas de acesso compartilhado, Parte 1: Noções básicas sobre o modelo SAS

O uso de SAS (assinatura de acesso compartilhado) é uma maneira eficiente de conceder acesso limitado a blobs, tabelas e filas a outros clientes na sua conta de armazenamento, sem precisar expor sua chave de conta. Na Parte 1 deste tutorial sobre assinaturas de acesso compartilhado, apresentaremos uma visão geral do modelo SAS e examinaremos as práticas recomendadas à SAS. [Parte 2](../storage-dotnet-shared-access-signature-part-2/) do tutorial o orientará pelo processo de criação de assinaturas de acesso compartilhado com o serviço Blob.

## O que é uma assinatura de acesso compartilhado? ##

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Isso significa que você pode conceder permissões limitadas a um cliente aos seus blobs, suas filas ou suas tabelas por um período especificado e com um conjunto determinado de permissões, sem precisar compartilhar suas chaves de acesso de conta. A SAS é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente só precisa passar a SAS ao construtor apropriado ou ao método apropriado.

## Quando você deve usar uma assinatura de acesso compartilhado? ##

Você pode usar uma SAS quando desejar fornecer acesso aos recursos de sua conta de armazenamento a um cliente não confiável com a chave de conta. As chaves de conta de armazenamento incluem uma chave primária e uma chave secundária, que concedem acesso administrativo à sua conta e a todos os recursos que ela contém. A exposição de qualquer uma de suas chaves de conta torna sua conta vulnerável a um possível uso mal-intencionado ou negligente. As assinaturas de acesso compartilhado são uma alternativa segura que permite que outros clientes leiam, gravem e excluam dados da sua conta de armazenamento de acordo com as permissões que você tiver concedido a eles e sem que eles precisem da chave de conta.

Um cenário comum em que uma SAS é útil é um serviço onde os usuários leem e gravam seus próprios dados na sua conta de armazenamento. Em um cenário em que uma conta de armazenamento armazena dados do usuário, há dois padrões de design comuns:


1\. Os clientes carregam e baixam dados por meio de um serviço de proxy front-end, que executa a autenticação. Esse serviço de proxy front-end tem a vantagem de permitir a validação de regras de negócio, mas, para grandes quantidades de dados ou transações de alto volume, a criação de um serviço que possa ser dimensionado de acordo com a demanda pode ser difícil ou dispendiosa.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\.	Um serviço leve autentica o cliente, conforme necessário, e gera uma SAS. Depois que o cliente recebe a SAS, ele pode acessar os recursos da conta de armazenamento diretamente com as permissões definidas pela SAS e para o intervalo permitido pela SAS. A SAS reduz a necessidade de roteamento de todos os dados por meio do serviço de proxy front-end.

![sas-storage-provider-service][sas-storage-provider-service]

Vários serviços reais podem usar uma mistura híbrida dessas duas abordagens, dependendo do cenário envolvido, com alguns dados processados e validados por meio do proxy front-end, enquanto outros dados são salvos e/ou lidos diretamente com a SAS.

## Como funciona uma assinatura de acesso compartilhado ##

Uma assinatura de acesso compartilhado é um URI que aponta para um recurso de armazenamento e inclui um conjunto especial de parâmetros de consulta que indicam como o recurso pode ser acessado pelo cliente. Um desses parâmetros, a assinatura, é construído por meio dos parâmetros SAS e assinado com a chave de conta. Essa assinatura é usada pelo Armazenamento do Azure para autenticar a SAS.

Uma assinatura de acesso compartilhado tem as seguintes restrições que a definem, cada uma das quais é representada como um parâmetro no URI:

- **O recurso de armazenamento.**Os recursos de armazenamento para os quais você pode delegar acesso incluem contêineres, blobs, filas, tabelas e intervalos de entidades de tabela.
- **Hora de início.** É a hora em que a SAS torna-se válida. A hora de início para uma assinatura de acesso compartilhado é opcional; se estiver omitida, a SAS entrará em vigor imediatamente. 
- **Hora de expiração.** É a hora após a qual a SAS não é mais válida.As práticas recomendadas sugerem que você especifique uma hora de expiração para uma SAS ou associe-a a uma política de acesso armazenada (veja mais abaixo).
- **Permissões.** As permissões especificadas nas SAS indicam quais operações o cliente pode executar com o recurso de armazenamento usando a SAS. 

Este é um exemplo de um URI SAS que fornece permissões de leitura e gravação para um blob. A tabela divide cada parte do URI para explicar como ele também contribui para a SAS:

https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

<table border="1" cellpadding="0" cellspacing="0">
    <tbody>
        <tr>
            <td valign="top" width="213">
                <p>
                    URI do blob
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    O endereço do blob. Observe que o uso de HTTPS é altamente recomendável.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Versão dos serviços de armazenamento
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sv=2012-02-12
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Para os serviços de armazenamento de versão 12-02-2012 e posterior, este parâmetro indica a versão a ser usada.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Hora de início
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    st=2013-04-29T22%3A18%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Especificada em um formato ISO 8061. Se você quiser que a SAS seja imediatamente válida, omita a hora de início.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Hora de expiração
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    se=2013-04-30T02%3A23%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Especificada em um formato ISO 8061.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Recurso
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sr=b
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    O recurso é um blob.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Permissões
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sp=rw
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    As permissões concedidas pelas SAS incluem Ler (r) e Gravar (w).
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Assinatura
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Usada para autenticar o acesso ao blob. A assinatura é um HMAC computado em uma cadeia-para-assinar e uma chave que usa o algoritmo SHA256 e                     depois codificado usando a codificação Base64.
                </p>
            </td>
        </tr>
    </tbody>
</table>


## Controlando as assinaturas de acesso compartilhado com uma política de acesso armazenada ##

Uma assinatura de acesso compartilhado pode assumir uma destas duas formas:

- **SAS ad hoc:** Quando você cria uma SAS ad hoc, a hora de início, a hora de expiração e as permissões para a SAS são todas especificadas no URI SAS (ou implícitas, quando a hora de início é omitida).Esse tipo de SAS pode ser criado em um contêiner, um blob, uma tabela ou uma fila.
- **SAS com política de acesso armazenada:** Uma política de acesso armazenada é definida em um contêiner de recurso - um contêiner de blob, uma tabela ou uma fila - e pode ser usada para gerenciar as restrições de uma ou mais assinaturas de acesso compartilhado.Quando você associa uma SAS a uma política de acesso armazenada, a SAS herda as restrições - a hora de início, a hora de expiração e as permissões - definidas para a política de acesso armazenada.

A diferença entre as duas formas é importante para um cenário fundamental: revogação. Uma SAS é uma URL, portanto qualquer pessoa que obtiver a SAS poderá usá-la, independentemente de quem a tiver solicitado em primeiro lugar. Se uma SAS for publicada publicamente, ela poderá ser usada por qualquer pessoa no mundo. Uma SAS distribuída será válida até que ocorra um destes quatro fatores:

1.	A hora de expiração especificada na SAS é atingida.
2.	A hora de expiração especificada na política de acesso armazenada referenciada pelas SAS é atingida (se uma política de acesso armazenada for referenciada e especificar uma hora de expiração). Isso pode ocorrer porque transcorreu o intervalo ou porque você modificou a política de acesso armazenada para ter uma hora de expiração no passado, que é uma maneira de revogar a SAS.
3.	A política de acesso armazenada referenciada pelas SAS é excluída, que é uma outra maneira de revogar a SAS. Observe que, se você recriar a política de acesso armazenada exatamente com o mesmo nome, todos os tokens SAS existentes serão novamente válidos de acordo com as permissões associadas a essa política de acesso armazenada (supondo que a hora de expiração não tenha passado na SAS). Se você estiver pretendendo revogar a SAS, certifique-se de usar um nome diferente se recriar a política de acesso com uma hora de expiração no futuro.
4.	A chave de conta usada para criar as SAS é regenerada.  Observe que isso causará uma falha de autenticação de todos os componentes do aplicativo que usam essa chave de conta até que eles sejam atualizados para usar a outra chave de conta válida ou a chave de conta recém-regenerada.
 
## Práticas recomendadas para uso de assinaturas de acesso compartilhado ##

Ao usar assinaturas de acesso compartilhado nos seus aplicativos, você precisará estar ciente de dois riscos possíveis:

- Se ocorrer perda de uma SAS, ela poderá ser usada por qualquer pessoa que a obtiver, o que poderá comprometer a sua conta de armazenamento.
- Se uma SAS fornecida para um aplicativo cliente expirar e o aplicativo não conseguir recuperar uma nova SAS do seu serviço, a funcionalidade do aplicativo poderá ser prejudicada.  

As recomendações a seguir para uso de assinaturas de acesso compartilhado ajudarão a equilibrar esses riscos:

1. **Sempre use HTTPS** para criar ou distribuir uma SAS.Se uma SAS for passada por HTTP e interceptada, um invasor que estiver realizando um ataque intermediário poderá lê-la e, em seguida, usá-la exatamente como o usuário previsto, o que poderá comprometer dados confidenciais ou gerar dados corrompidos pelo usuário mal-intencionado.
2. **Faça referência às políticas de acesso armazenadas sempre que possível.**  As políticas de acesso armazenadas permitem que você revogue permissões sem precisar regenerar as chaves de conta de armazenamento.Defina a expiração nessas políticas para um tempo muito longo (ou infinito) e certifique-se de que ela seja atualizada regularmente para uma data mais distante no futuro.
3. **Use horas de expiração de curto prazo em uma SAS ad hoc.** Dessa forma, mesmo que uma SAS seja comprometida inadvertidamente, ela só será viável por um curto período. Esta prática será especialmente importante se você não puder fazer referência a uma política de acesso armazenada.Essa prática também ajudará a limitar a quantidade de dados que podem ser gravados em um blob, limitando o tempo disponível para carregá-los.
4. **Faça com que os clientes renovem a SAS automaticamente, se necessário.**  Os clientes devem renovar a SAS bem antes da expiração esperada, para que haja tempo para novas tentativas se o serviço que fornece a SAS estiver indisponível.  Se o objetivo de uso da SAS for destinado a um pequeno número de operações imediatas e de curta duração, que devem ser concluídas dentro do tempo de expiração fornecido, isso talvez não seja necessário, pois não se espera que a SAS seja renovada.  No entanto, se você tiver um cliente que está sempre fazendo solicitações via SAS, surge a possibilidade de expiração.A principal consideração consiste em equilibrar a necessidade de a SAS ser de curta duração (conforme mencionado acima) com a necessidade de garantir que o cliente esteja solicitando renovação com antecedência suficiente para evitar uma interrupção devido à expiração da SAS antes da renovação bem-sucedida.
5. **Tenha cuidado com a hora de início da SAS.**   Se você definir a hora de início de uma SAS para **agora**, poderão ser observadas falhas intermitentemente para os primeiros minutos devido à defasagem horária (diferenças na hora atual de acordo com máquinas diferentes).  Em geral, defina a hora de início para, no mínimo, 15 minutos antes, ou não a defina, o que a tornará imediatamente válida em todos os casos.Em geral, o mesmo também aplica-se à hora de expiração - lembre-se de que você pode observar até 15 minutos de defasagem horária em uma das duas direções em qualquer solicitação.Observação para os clientes que usam uma versão de REST anterior à 2012-02-12, a duração máxima de uma SAS que não faz referência a uma política de acesso armazenada é 1 hora, e qualquer política que especifique um período maior do que esse falhará.
6.	**Seja específico com o recurso a ser acessado.**  Uma prática recomendada de segurança comum consiste em fornecer os privilégios mínimos necessários a um usuário.  Se um usuário precisar apenas de acesso de leitura a uma única entidade, conceda-lhe acesso de leitura a essa única entidade, e não acesso de leitura/gravação/exclusão a todas as entidades.Isso também ajuda a minimizar a ameaça da SAS que estiver sendo comprometida, pois a SAS terá menos poder nas mãos de um invasor.
7.	**É importante que você entenda que a sua conta será cobrada por qualquer uso, incluindo o realizado com a SAS.**  Se você fornecer acesso de gravação a um blob, um usuário poderá optar por carregar um blob de 200 GB.  Se você também tiver concedido a ele acesso de leitura, é possível que ele decida baixá-lo 10 vezes, incorrendo em 2 TB de custos de egresso para você.  Mais uma vez, forneça permissões limitadas, para ajudar a reduzir a possibilidade de usuários mal-intencionados.Use SAS de curta duração para reduzir essa ameaça (mas, tenha cuidado com a defasagem horária na hora de término).
8.	**Valide os dados gravados com a SAS.** Quando um aplicativo cliente gravar dados na sua conta de armazenamento, tenha em mente de que poderá haver problemas com esses dados. Se o seu aplicativo necessitar de que esses dados sejam validados ou autorizados antes que estejam prontos para uso, você deverá realizar essa validação depois que os dados forem gravados e antes que eles sejam usados pelo seu aplicativo.Essa prática também protegerá contra dados corrompidos ou mal-intencionados que estiverem sendo gravados na sua conta por um usuário que adquiriu a SAS de forma adequada ou por um usuário que estiver explorando uma SAS vazada.
9. **Não use sempre SAS.**  Às vezes, os riscos associados a uma determinada operação em relação à sua conta de armazenamento superam os benefícios da SAS. Para essas operações, crie um serviço de camada intermediária que grave na sua conta de armazenamento após a validação, a autenticação e a auditoria da regra de negócio. Além disso, algumas vezes é mais simples de gerenciar o acesso de outras maneiras.Por exemplo, se quiser tornar todos os blobs de um contêiner publicamente legíveis, você poderá tornar o contêiner Público, em vez de fornecer uma SAS para o acesso de cada cliente.
10.	**Use a Análise de Armazenamento para monitorar seu aplicativo.** Você pode usar log e métricas para observar qualquer pico nas falhas de autenticação devido a uma interrupção no seu serviço de provedor de SAS ou à remoção acidental de uma política de acesso armazenada.Veja o [Blog da Equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) para mais informações.

## Conclusão ##

As assinaturas de acesso compartilhado são úteis para fornecer permissões limitadas para a sua conta de armazenamento aos clientes que não devem ter a chave de conta.  Desse modo, elas são uma parte vital do modelo de segurança para qualquer aplicativo que utilize o Armazenamento do Azure.  Se você seguir as práticas recomendadas listadas aqui, poderá usar a SAS para oferecer mais flexibilidade de acesso aos recursos da sua conta de armazenamento, sem comprometer a segurança do seu aplicativo.

## Próximas Etapas ##

[Assinaturas de acesso compartilhado, Parte 2: Criar e usar um SAS com o Serviço Blob](../storage-dotnet-shared-access-signature-part-2/)

[Gerenciar o acesso aos recursos de Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/ee393343.aspx)

[Delegando acesso com uma assinatura de acesso compartilhado (API REST)](http://msdn.microsoft.com/pt-br/library/windowsazure/ee395415.aspx)

[Introdução à Tabela e à Fila SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png



<!--HONumber=35.1-->
