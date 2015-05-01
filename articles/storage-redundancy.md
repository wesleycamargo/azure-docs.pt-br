
<properties 
  pageTitle="Opções de redundância de armazenamento do Azure | Microsoft Azure" 
  description="Os dados na sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade. Saiba mais sobre opções de replicação para sua conta de armazenamento do Azure." 
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
  ms.date="03/20/2015" 
  ms.author="tamram"/>

# Opções de redundância de armazenamento do Azure



Os dados em sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade, cumprindo o [SLA do Armazenamento do Azure](http://azure.microsoft.com/support/legal/sla/) mesmo diante de falhas transitórias de hardware. Para obter detalhes arquitetônicos sobre a durabilidade com o armazenamento do Azure, consulte o [Artigo de SOSP do Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

Quando você cria sua conta de armazenamento, deve selecionar uma das seguintes opções de replicação:  

- **Armazenamento com redundância local (LRS).** O armazenamento com redundância local mantém três cópias de seus dados. O LRS é replicado três vezes dentro de um único recurso em uma única região. O LRS protege seus dados contra falhas de hardware normais, mas não da falha de um único recurso.
  
O LRS é oferecido com desconto. Para durabilidade máxima, recomendamos que você utilize o armazenamento com redundância geográfica, descrito abaixo.


- **Armazenamento com redundância de zona (ZRS).** O armazenamento com redundância de zona mantém três cópias dos seus dados. O ZRS é replicado três vezes em duas ou três instalações, seja em uma única região ou em duas regiões, fornecendo maior durabilidade do que o LRS. O ZRS garante que os dados sejam duráveis em uma única região.
O ZRS proporciona maior durabilidade que o LRS, todavia, para durabilidade máxima recomendamos que você utilize o armazenamento com redundância geográfica, descrito abaixo.  

	> [AZURE.NOTE] O ZRS está disponível atualmente apenas para blobs de bloco.  
	> Observe que, depois de criar sua conta de armazenamento e selecionado replicação com redundância de zona, você não poderá convertê-la para utilizar nenhum outro tipo de replicação, ou vice-versa.
 


- **Armazenamento com redundância geográfica (GRS)**. O armazenamento com redundância geográfica é habilitado para sua conta de armazenamento por padrão quando ela é criada. O GRS mantém seis cópias de seus dados. Com o GRS, seus dados são replicados três vezes na região primária e também são replicados três vezes para uma região secundária a centenas de quilômetros de distância da região primária, oferecendo o nível mais alto de durabilidade. Em caso de falha na região primária, o Armazenamento do Azure realizará failover para a região secundária. O GRS assegura que seus dados serão duráveis em duas regiões separadas.


- **Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)**. O armazenamento com redundância geográfica com acesso de leitura replica seus dados para uma localização geográfica secundária e também fornece acesso de leitura aos dados no local secundário. O armazenamento com redundância geográfica com acesso de leitura permite acessar os dados no local primário ou secundário caso um local fique indisponível.

	> [AZURE.IMPORTANT] É possível alterar como os dados replicados após sua conta de armazenamento ter sido criada, mas observe que você pode incorrer em um custo único adicional para a transferência de dados se mudar de LRS para GRS ou RA-GRS. Se você escolher o GRS ao criar a conta, não poderá alternar posteriormente para nenhum outro tipo de replicação, ou vice-versa.
 

A tabela a seguir apresenta uma visão geral das diferenças entre LRS, ZRS, GRS e RA-GRS, enquanto as seções posteriores lidam com cada tipo de replicação em mais detalhes.


|Estratégia de Replicação|LRS|ZRS|GRS|RA-GRS 
|--------------------|---|---|---|------
|Os dados são replicados entre várias instalações|Não|Sim|Sim|Sim|
|Os dados podem ser lidos do local secundário, bem como do local primário|Não|Não|Não|Sim
|Número de cópias de dados mantidas em nós separados|3|3|6|6
 

Para informações sobre preços para replicação de conta de armazenamento, consulte [Detalhes de preços de armazenamento](http://azure.microsoft.com/pricing/details/storage/).

## Armazenamento com redundância local (LRS)

Armazenamento com redundância local replica seus dados dentro da região em que você criou sua conta de armazenamento. Para maximizar a durabilidade, todas as solicitações feitas nos dados de sua conta de armazenamento são replicadas três vezes. Essas três réplicas residem em domínios de falha e domínios de atualização separados. Um domínio de falha (FD) é um grupo de nós que representam uma unidade física de falha e podem ser considerados nós que pertencem ao mesmo rack físico. Um domínio de atualização (UD) é um grupo de nós atualizados em conjunto durante o processo de atualização de serviço (distribuição). As três réplicas estão espalhadas por UDs e FDs para garantir que os dados estejam disponíveis mesmo que a falha de hardware afete um único rack e quando os nós forem atualizados durante a distribuição. Uma solicitação retorna com êxito depois de ter sido gravada para todas as três réplicas.

Embora o armazenamento com redundância geográfica seja recomendado para a maioria dos aplicativos, armazenamento redundante pode ser desejável em certas situações:  

- O LRS é menos dispendioso que o GRS e também oferece um maior rendimento. Se seu aplicativo armazenar dados que possam ser facilmente reconstruídos, você pode optar por LRS.

- Alguns aplicativos são restritos a replicação de dados dentro de uma única região devido a requisitos de controle de dados.

- Se seu aplicativo tiver sua própria estratégia de replicação geográfica, ele não poderá exigir GRS.


## Armazenamento com redundância de zona (ZRS)

O armazenamento com redundância de zona é replicado três vezes por duas ou três instalações, em uma única região ou em duas regiões, proporcionando maior durabilidade que o LRS. Se sua conta de armazenamento tiver ZRS habilitado, seus dados são duráveis mesmo no caso de falha em uma das instalações.


>[AZURE.NOTE]  O ZRS está disponível atualmente apenas para blobs de bloco. Observe que, uma vez que você tenha criado sua conta de armazenamento e selecionado replicação com redundância de zona, você não pode convertê-la para utilizar nenhum outro tipo de replicação, ou vice-versa.


## Armazenamento com redundância geográfica (GRS)


O armazenamento com redundância geográfica replica seus dados para uma região secundária a centenas de quilômetros da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada. 

Para uma conta de armazenamento com GRS habilitado, uma atualização primeiro é confirmada para a região primária, na qual é replicada três vezes. Em seguida, a atualização é replicada para a região secundária, na qual também é replicada três vezes em domínios de falha e domínios de atualização separados.

 
> [AZURE.NOTE] Com GRS, solicitações de gravação de dados são replicadas de forma assíncrona para a região secundária. É importante observar que optar por GRS não afeta a latência de solicitações feitas na região primária. No entanto, como a replicação assíncrona envolve um atraso, no caso de um desastre regional, é possível que as alterações que ainda não foram replicadas para a região secundária sejam pedidas se os dados não puderem ser recuperados da região primária.
 
Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A região secundária é determinada com base na região primária e não pode ser alterada. A tabela a seguir mostra os emparelhamentos de regiões primárias e secundárias:

|Primária            |Secundária        
| ---------------   |----------------
|Centro-norte dos EUA |Centro-sul dos EUA
|Centro-sul dos EUA |Centro-norte dos EUA
|Leste dos EUA |Oeste dos EUA        
|Oeste dos EUA |Leste dos EUA         
|Leste dos EUA 2 |Centro dos EUA      
|Centro dos EUA |Leste dos EUA 2       
|Norte da Europa |Europa Ocidental     
|Europa Ocidental |Norte da Europa    
|Sudeste da Ásia |Leste da Ásia       
Ásia Oriental |Sudeste da Ásia 
|China Oriental |Norte da China     
|Norte da China |China Oriental      
|Leste do Japão |Oeste do Japão      
|Oeste do Japão |Leste do Japão      
|Sul do Brasil |Centro-sul dos EUA
|Leste da Austrália |Sudeste da Austrália
|Sudeste da Austrália | Leste da Austrália  

 
## Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)

O armazenamento com redundância geográfica de acesso de leitura maximiza a disponibilidade da sua conta de armazenamento, fornecendo acesso somente leitura aos dados no local secundário, além de replicação em duas regiões fornecido por GRS. No caso de os dados ficarem indisponíveis na região primária, seu aplicativo poderá ler os dados da região secundária.

Quando você habilita o acesso somente leitura aos dados na região secundária, seus dados ficam disponíveis em um ponto de extremidade secundário, além do ponto de extremidade primário para sua conta de armazenamento. O ponto de extremidade secundário é semelhante ao ponto de extremidade primário, mas anexa o sufixo `-secundário` ao nome da conta. Por exemplo, se o ponto de extremidade primário para o serviço Blob for `myaccount.blob.core.windows.net`, o ponto de extremidade secundário será `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para sua conta de armazenamento são as mesmas para os pontos de extremidade primário e secundário.

## Próximas etapas

- [Metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md) 
- [Armazenamento com redundância geográfica com acesso de leitura e opções de redundância do Armazenamento do Microsoft Azure ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Emulador de Armazenamento do Microsoft Azure 3.1 com RA-GRS ](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/08/microsoft-azure-storage-emulator-3-1-with-ra-grs.aspx)
- [Artigo SOSP de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)


<!--HONumber=52-->