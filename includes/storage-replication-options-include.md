Os dados em sua conta de armazenamento são replicados para garantir a durabilidade que também está altamente disponível, atendendo ao [Contrato de Nível de Serviço do Armazenamento do Azure](http://azure.microsoft.com/support/legal/sla/), mesmo no caso de falhas transitórias de hardware. O Armazenamento do Azure é implantado em 15 regiões ao redor do mundo e também inclui suporte para replicar dados entre diferentes regiões. Você tem diversas opções para replicar os dados em sua conta de armazenamento:

- O *LRS \(armazenamento com redundância local\)* mantém três cópias de seus dados. O LRS é replicado três vezes em uma única instalação, em uma única região. O LRS protege os dados contra falhas normais de hardware, mas não contra falha de uma única instalação.

	O LRS é oferecido com desconto. Para durabilidade máxima, recomendamos que você utilize o armazenamento com redundância geográfica, descrito abaixo.

- O *ZRS \(armazenamento com redundância de zona\)* mantém três cópias de seus dados. O ZRS é replicado três vezes por dois ou três instalações, em uma única região ou em duas regiões, proporcionando maior durabilidade que o LRS. O ZRS assegura que seus dados irão durar em uma única região.
 
	O ZRS proporciona maior durabilidade que o LRS, todavia, para durabilidade máxima recomendamos que você utilize o armazenamento com redundância geográfica, descrito abaixo.

	> [AZURE.NOTE]O ZRS está disponível atualmente apenas para blobs de bloco. Observe que, uma vez que você tenha criado sua conta de armazenamento e selecionado replicação com redundância de zona, você não pode convertê-la para utilizar nenhum outro tipo de replicação, ou vice-versa.

- O *GRS \(armazenamento com redundância geográfica\)* está habilitado para sua conta de armazenamento por padrão, quando ela é criada por você. O GRS mantém seis cópias de seus dados. Com o GRS, seus dados são replicados três vezes na região primária e também são replicados três vezes para uma região secundária a centenas de quilômetros de distância da região primária, oferecendo o nível mais alto de durabilidade. Em caso de falha na região primária, o Armazenamento do Azure realizará failover para a região secundária. O GRS assegura que seus dados serão duráveis em duas regiões separadas.

	> [AZURE.NOTE]O GRS é recomendado no lugar do ZRS ou LRS, para máxima durabilidade.

- *O RA-GRS \(armazenamento com acesso de leitura geograficamente redundante\)* fornece todos os benefícios do armazenamento com redundância geográfica e também permite acesso de leitura dos dados na região secundária caso a região primária fique indisponível. O armazenamento com acesso de leitura geograficamente redundante é recomendado para disponibilidade máxima além de durabilidade.

Para obter mais detalhes sobre as opções de replicação, consulte [Opções de Redundância de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/) e o [Blog da Equipe de Armazenamento do Azure](storage-redundancy.md).
	
As diferenças de preço entre as diversas opções de replicação encontram-se na página [Detalhes do Preço de Armazenamento](http://azure.microsoft.com/pricing/details/storage/)\).

<!--HONumber=52-->
