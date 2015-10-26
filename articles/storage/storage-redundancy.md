
<properties 
  pageTitle="Replicação do Armazenamento do Azure | Microsoft Azure" 
  description="Os dados na sua conta de Armazenamento do Microsoft Azure são replicados para garantir durabilidade e alta disponibilidade. Opções de replicação incluem LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona), GRS (armazenamento com redundância geográfica) RA-GRS (armazenamento com redundância geográfica com acesso de leitura)." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="10/07/2015" 
  ms.author="tamram"/>

# Replicação de Armazenamento do Azure

Os dados da sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade, cumprindo o [SLA do Armazenamento do Azure](http://azure.microsoft.com/support/legal/sla/) mesmo diante de falhas transitórias de hardware.

Quando você cria uma conta de armazenamento, deve selecionar uma das seguintes opções de replicação:

- [Armazenamento com redundância local (LRS)](#locally-redundant-storage)
- [Armazenamento com redundância de zona (ZRS)](#zone-redundant-storage)
- [Armazenamento com redundância geográfica (GRS)](#geo-redundant-storage)
- [Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)](#read-access-geo-redundant-storage)

A tabela a seguir apresenta uma visão geral das diferenças entre LRS, ZRS, GRS e RA-GRS, enquanto as seções posteriores lidam com cada tipo de replicação em mais detalhes.


|Estratégia de replicação|LRS|ZRS|GRS|RA-GRS
|--------------------|---|---|---|------
|Os dados são replicados entre várias instalações.|Não|Sim|Sim|Sim|
|Os dados podem ser lidos do local secundário, bem como do local primário.|Não|Não|Não|Sim
|Número de cópias de dados mantidas em nós separados.|3|3|6|6


## Armazenamento com redundância local

O LRS (armazenamento com redundância local) replica seus dados dentro da região em que você criou sua conta de armazenamento. Para maximizar a durabilidade, todas as solicitações feitas nos dados de sua conta de armazenamento são replicadas três vezes. Essas três réplicas residem em domínios de falha e domínios de atualização separados. Um domínio de falha (FD) é um grupo de nós que representam uma unidade física de falha e podem ser considerados nós que pertencem ao mesmo rack físico. Um domínio de atualização (UD) é um grupo de nós atualizados em conjunto durante o processo de atualização de um serviço (distribuição). As três réplicas estão espalhadas por UDs e FDs para garantir que os dados estejam disponíveis mesmo que a falha de hardware afete um único rack e quando os nós forem atualizados durante a distribuição. Uma solicitação retorna com êxito depois de ter sido gravada para todas as três réplicas.

Embora o armazenamento com redundância geográfica (GRS) seja recomendado para a maioria dos aplicativos, armazenamento com redundância local pode ser desejável em certas situações:

- O LRS é menos dispendioso que o GRS e também oferece um maior rendimento. Se seu aplicativo armazenar dados que possam ser facilmente reconstruídos, você pode optar por LRS.

- Alguns aplicativos são restritos a replicação de dados dentro de uma única região devido a requisitos de controle de dados.

- Se seu aplicativo tiver sua própria estratégia de replicação geográfica, ele não poderá exigir GRS.


## Armazenamento com redundância de zona

O ZRS (armazenamento com redundância de zona) é replicado três vezes por duas ou três instalações, em uma única região ou em duas regiões, proporcionando maior durabilidade que o LRS. Se sua conta de armazenamento tiver ZRS habilitado, seus dados são duráveis mesmo no caso de falha em uma das instalações.


>[AZURE.NOTE]O ZRS está atualmente disponível apenas para blobs de blocos e tem suporte apenas nas versões de 14/02/2014 e posteriores. Observe que, uma vez que você tenha criado sua conta de armazenamento e selecionado replicação com redundância de zona, você não pode convertê-la para utilizar nenhum outro tipo de replicação ou vice-versa.


## Armazenamento com redundância geográfica

O GRS (armazenamento com redundância geográfica) replica seus dados para uma região secundária a centenas de quilômetros da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

Para uma conta de armazenamento com GRS habilitado, uma atualização primeiro é confirmada para a região primária, na qual é replicada três vezes. Em seguida, a atualização é replicada para a região secundária, na qual também é replicada três vezes em domínios de falha e domínios de atualização separados.


> [AZURE.NOTE]Com GRS, solicitações de gravação de dados são replicadas de forma assíncrona para a região secundária. É importante observar que optar por GRS não afeta a latência de solicitações feitas na região primária. No entanto, como a replicação assíncrona envolve um atraso, no caso de um desastre regional, é possível que as alterações que ainda não foram replicadas para a região secundária sejam pedidas se os dados não puderem ser recuperados da região primária.

Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A região secundária é determinada com base na região primária e não pode ser alterada. A tabela a seguir mostra os emparelhamentos de regiões primárias e secundárias.

|Primário |Secundário
| ---------------   |----------------
|Centro-Norte dos EUA |Centro-Sul dos Estados Unidos
|Centro-Sul dos Estados Unidos |Centro-Norte dos EUA
|Leste dos EUA |Oeste dos EUA
|Oeste dos EUA |Leste dos EUA
|Leste dos EUA 2 |Centro dos EUA
|Centro dos EUA |Leste dos EUA 2
|Norte da Europa |Europa Ocidental
|Europa Ocidental |Norte da Europa
|Sudeste da Ásia |Ásia Oriental
|Ásia Oriental |Sudeste da Ásia
|China Oriental |Norte da China
|Norte da China |China Oriental
|Leste do Japão |Oeste do Japão
|Oeste do Japão |Leste do Japão
|Sul do Brasil |Centro-Sul dos Estados Unidos
|Leste da Austrália |Sudeste da Austrália
|Sudeste da Austrália|Leste da Austrália  


## Armazenamento com redundância geográfica com acesso de leitura

O RA-GRS (armazenamento com redundância geográfica de acesso de leitura) maximiza a disponibilidade da sua conta de armazenamento, fornecendo acesso somente leitura aos dados no local secundário, além de replicação em duas regiões fornecido por GRS. No caso de os dados ficarem indisponíveis na região primária, seu aplicativo poderá ler os dados da região secundária.

Quando você habilita o acesso somente leitura aos dados na região secundária, seus dados ficam disponíveis em um ponto de extremidade secundário, além do ponto de extremidade primário para sua conta de armazenamento. O ponto de extremidade secundário é semelhante ao ponto de extremidade primário, mas anexa o sufixo `–secondary` ao nome da conta. Por exemplo, se o ponto de extremidade primário para o serviço Blob for `myaccount.blob.core.windows.net`, seu ponto de extremidade secundário será `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para sua conta de armazenamento são as mesmas para os pontos de extremidade primário e secundário.

## Próximas etapas

- [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md)
- [Armazenamento com redundância geográfica com acesso de leitura e opções de redundância do Armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Emulador de Armazenamento do Microsoft Azure 3.1 com RA-GRS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/08/microsoft-azure-storage-emulator-3-1-with-ra-grs.aspx)
- [Artigo SOSP de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

<!---HONumber=Oct15_HO3-->