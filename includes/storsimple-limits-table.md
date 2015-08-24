
| Identificador de limite | Limite | Comentários |
|----------------- | ------|--------- |
| Número máximo de credenciais de conta de armazenamento | 64 | |
| Número máximo de contêineres de volume | 64 | |
| Número máximo de volumes | 255 | |
| Número máximo de modelos de largura de banda | 25 | |
| Número máximo de agendas por modelo de largura de banda | 168 | Uma agenda para cada hora, cada dia da semana (24x7). |
| Tamanho máximo de um volume | 64 TB | |
| Número máximo de conexões iSCSI | 512 | |
| Número máximo de conexões iSCSI dos iniciadores | 512 | |
| Número máximo de registros de controle de acesso por dispositivo | 64 | |
| Número máximo de volumes por política de backup | 24 | |
| Número máximo de backups retidos por política de backup | 64 | |
| Número máximo de agendas por política de backup | 10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser retidos por volume | 256 | Isso inclui instantâneos locais e instantâneos de nuvem. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo | 10\.000 | |
| Número máximo de volumes que podem ser processados paralelamente para backup, restauração e clonagem | 16 |<ul><li>Se houver mais de 16 volumes, eles serão processados sequencialmente à medida que os slots de processamento se tornarem disponíveis.</li><li>Novos backups de um volume clonado ou restaurado não poderão ocorrer até que a operação seja finalizada.</li></ul>|
| Tempo de recuperação de clonagem e restauração | Menos de 2 minutos | <ul><li>O volume é disponibilizado em até 2 minutos depois da operação de restauração ou clonagem, independentemente do tamanho do volume.</li><li>Inicialmente, o desempenho do volume pode ser mais lento do que o normal, pois a maioria dos dados e metadados ainda reside na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano a uma taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>A operação de restauração ou clonagem será concluída quando todos os metadados estiverem no dispositivo.</li><li>As operações de backup não podem ser executadas até que a operação de restauração ou clonagem seja totalmente concluída.|
| Disponibilidade de restauração fina | Último failover | |
| Taxa de transferência máxima de leitura/gravação do cliente (quando servida da camada SSD)* | 920/720 MB/s com uma única interface de rede de 10 GbE | Até 2 vezes com MPIO e duas interfaces de rede. |
| Taxa de transferência máxima de leitura/gravação do cliente (quando servida da camada HDD)* | 120/250 MB/s |
| Taxa de transferência máxima de leitura/gravação do cliente (quando servida da camada de nuvem)* | 11/41 MB/s | A taxa de transferência de leitura depende dos clientes que geram e mantêm profundidade suficiente de fila de E/S. |

&#42; A taxa de transferência máxima por tipo de E/S foi medida com cenários 100% de gravação e 100% de leitura. A taxa de transferência real pode ser menor e depende da combinação de E/S e das condições da rede.

<!---HONumber=August15_HO7-->