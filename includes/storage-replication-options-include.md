Os dados da sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade, cumprindo o [SLA do Armazenamento do Azure](http://azure.microsoft.com/support/legal/sla/) mesmo diante de falhas transitórias de hardware. Quando você cria uma conta de armazenamento, deve selecionar uma das seguintes opções de replicação:

- **Armazenamento com redundância local (LRS).** O armazenamento com redundância local mantém três cópias dos seus dados. O LRS é replicado três vezes em uma única instalação, em uma única região. O LRS protege os dados contra falhas normais de hardware, mas não contra falha de uma única instalação.  
  
	O LRS é oferecido com desconto. Para durabilidade máxima, recomendamos que você utilize o armazenamento com redundância geográfica, descrito abaixo.


- **Armazenamento com redundância de zona (ZRS).** O armazenamento com redundância de zona mantém três cópias dos seus dados. O ZRS é replicado três vezes por dois ou três instalações, em uma única região ou em duas regiões, proporcionando maior durabilidade que o LRS. O ZRS assegura que seus dados irão durar em uma única região.

	O ZRS proporciona maior durabilidade que o LRS, todavia, para durabilidade máxima recomendamos que você utilize o armazenamento com redundância geográfica, descrito abaixo.

	> [AZURE.NOTE]O ZRS está disponível atualmente apenas para blobs de bloco.
	> 
	> Depois que tiver criado sua conta de armazenamento e selecionado ZRS, você não poderá convertê-la para utilizar nenhum outro tipo de replicação, ou vice-versa.

- **Armazenamento com redundância geográfica (GRS)**. O armazenamento com redundância geográfica é habilitado para sua conta de armazenamento por padrão quando ela é criada. O GRS mantém seis cópias de seus dados. Com o GRS, seus dados são replicados três vezes na região primária e também são replicados três vezes para uma região secundária a centenas de quilômetros de distância da região primária, oferecendo o nível mais alto de durabilidade. Em caso de falha na região primária, o Armazenamento do Azure realizará failover para a região secundária. O GRS assegura que seus dados serão duráveis em duas regiões separadas.


- **Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)**. O armazenamento com redundância geográfica com acesso de leitura replica seus dados para uma localização geográfica secundária e também fornece acesso de leitura aos dados no local secundário. O armazenamento com redundância geográfica com acesso de leitura permite acessar os dados no local primário ou secundário caso um local fique indisponível.

	> [AZURE.IMPORTANT]Você pode alterar como seus dados são replicados depois que sua conta de armazenamento tiver sido criada, a menos que tenha especificado ZRS quando criou a conta. No entanto, observe que você pode incorrer em custo por uma transferência de dados adicional e ocasional se alternar de LRS para GRS ou RA-GRS.
 
Confira [Replicação de armazenamento do Azure](../articles/storage/storage-redundancy.md) para obter mais detalhes sobre as opções de replicação de armazenamento do Azure.

Para informações sobre preços de replicação de conta de armazenamento, confira [Detalhes de preços de armazenamento](http://azure.microsoft.com/pricing/details/storage/).

Para obter detalhes arquitetônicos sobre a durabilidade com o armazenamento do Azure, confira o [Artigo de SOSP do Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

<!---HONumber=August15_HO6-->