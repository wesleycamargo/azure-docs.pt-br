

Algumas cargas de trabalho de banco de dados como o SQL Server ou Oracle exigem alto de memória, armazenamento e largura de banda I/O, mas não número alto de núcleos. Muitas cargas de trabalho do banco de dados não são de uso intensivo de CPU. O Azure oferece determinados tamanhos de VM, onde você pode restringir a contagem de vCPU VM para reduzir o custo de licenciamento de software, mantendo a mesma memória, armazenamento e largura de banda I/O.

A contagem de vCPU pode ser restrita para a metade ou um quarto do tamanho da VM original. Esses novos tamanhos VM têm um sufixo que especifica o número de vCPUs ativos para facilitar a identificação.

Por exemplo, o tamanho da VM atual Standard_GS5 acompanha 32 vCPUs, 448 GB de RAM, 64 discos (até 256 TB) e 80.000 IOPs ou 2 GB/s de largura de banda I/O. Os novos tamanhos de VM Standard_GS5-16 e 8 Standard_GS5 vêm com 8 e 16 vCPUs activas respectivamente, mantendo o restante das especificações de Standard_GS5 para memória, armazenamento e largura de banda I/O.

Os valores de licenciamento cobrados para SQL Server ou Oracle são restritos à nova contagem de vCPU e outros produtos devem ser cobrados com base na contagem de vCPU nova. Isso resulta em um aumento de 50% a 75% na razão entre as especificações VM para vCPUs ativas (Faturável). Esses novos tamanhos VM que só estão disponíveis no Azure, permitindo que as cargas de trabalho enviem por push maior utilização de CPU em uma fração do custo de licenciamento (por núcleo). Neste momento, o custo de computação, que inclui o licenciamento do sistema operacional, permanece o mesmo com o tamanho original. Para obter mais informações, consulte [tamanhos de VM do Azure para cargas de trabalho do banco de dados mais econômicos](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Nome                | vCPU | Especificações           |
|---------------------|------|-----------------|
| Standard_M64 32ms   | 32   | O mesmo que M64ms   |
| Standard_M64-16ms   | 16   | O mesmo que M64ms   |
| Standard_M128-64ms  | 64   | O mesmo que M128ms  |
| Standard_M128-32ms  | 32   | O mesmo que M128ms  |
| Standard_E32-16_v3  | 16   | O mesmo que E32s_v3 |
| Standard_E32-8s_v3  | 8    | O mesmo que E32s_v3 |
| Standard_E64-32s_v3 | 32   | O mesmo que E64s_v3 |
| Standard_E64-16s_v3 | 16   | O mesmo que E64s_v3 |
| Standard_GS4-8      | 8    | O mesmo que GS4     |
| Standard_GS4-4      | 4    | O mesmo que GS4     |
| Standard_GS5-16     | 16   | O mesmo que GS5     |
| Standard_GS5-8      | 8    | O mesmo que GS5     |
| Standard_DS13-4_v2  | 4    | O mesmo que DS13_v2 |
| Standard_DS13-2_v2  | 2    | O mesmo que DS13_v2 |
| Standard_DS14-8_v2  | 8    | O mesmo que DS14_v2 |
| Standard_DS14-4_v2  | 4    | O mesmo que DS14_v2 |