# Dispositivos de Entrada e Saída
- **Arquitetura do sistema**: CPU conectada com a memória principal por um barramento de memória, alguns dispositivos estão conectados pela entrada/saída desse barramento, como o PCI, placa de vídeo e outros dispositivos de alta performance. Além disso, há uma camada mais abaixo que possui o chamado barramento de periféricos, porque é onde SCSI, STA ou USB (Universal Serial Bus) são conectados, esses tipos de conexão são para dispositivos lentos, como HDD, alguns SSD's, mouses e teclados.
- **Por que usar esse tipo de arquitetura?**
	Custo e física. Quanto mais rápido for o meio de transporte, menor ele deve ser, dessa forma, barramentos mais velozes não tem entradas o suficiente para plugar todos esses dispositivos. Além de que eles são mais caros. Portanto, engenheiros adotaram esse tipo de abordagem de deixar os dispositivos que precisam de uma alta performance e maior velocidade mais próximo da CPU, enquanto os outros mais distante.
	Em uma arquitetura mais atualizada que a anterior, especificamente o chip Z270 da Intel, a estrutura é assim:
  - A CPU conecta com um chip de entrada e saída por meio de uma interface;
  - Os restantes dos dispositivos são conectados a esse chip por meio de diferentes interconexões;
  - A direita do chip, existe uma interface eSATA que viabiliza a conexão SATA externa.
  - A esquerda do chip, são conectados dispositivos de alta performance, como placa gráfica, via PCIe (Peripheral Component Interconnect Express);
  - Abaixo do chip, há conexões USB disponíveis para dispositivos de entrada e saída mais lentos, como mouse, teclado, etc.

- Existem estruturas comuns aos I/O devices, eles possuem uma interface que é apresentada ao sistema, por meio dessa interface (como se fosse sua própria API), o sistema consegue saber quais funcionalidades o dispositivo possui e como controlar. Além disso, cada dispositivo possui sua estrutura interna implementada de forma especifica ao seu objetivo, sendo essas complexas ou simples.

# Introdução a sistema de arquivos
- É uma abstração do sistema operacional que permite persistir dados nomeados.
- Nomeados pois permite que seja acessado pelo usuário por meio desse identificador, mesmo que o programa que o criou já tenha finalizado, além de permitir compartilhamento por múltiplas aplicações.
- Persistência, pois armazena o dado até que seja explicitamente deletado.
- Os computadores devem ser capazes de armazenar dados de forma confiável.
- Dois principais atores: 
### Arquivo:
Conjunto **nomeado** de dados em um sistema de arquivos.
    - **Ex:** "/home/manel/documentos/notas_so.txt"
  - A informação de um arquivo possui duas partes, os **metadados** e os **dados**. O primeiro é a informação sobre o arquivo que é entendido e manuseado pelo SO, como **tamanho, tempo de modificação (times_stamp), dono e dados de segurança**, como quem pode ler, escrever ou executar. Já o segundo pode ser qualquer informação que o usuário adicionou ou a aplicação. Do ponto de vista do sistema de arquivos, é apenas um array de bytes não digitados, com eles as aplicações podem usar qualquer informação que queiram em qualquer formato que desejam.
### Diretório:
Fornecem nomes para arquivos. Um diretório de arquivo é uma lista de nomes legíveis por humano e um mapeamento de cada nome para um arquivo ou diretório subjacente.
		> Um diretório é uma pasta que contém documentos (arquivos) e outras pastas (diretórios).
- A String que identifica um arquivo ou diretório é chamada de caminho **(path)**. A barra **"/"** separa componentes do caminho, cada componente representa a entrada em um diretório.	
### Volume:
É uma coleção de recursos de armazenamento físico que formam um dispositivo lógico de armazenamento
- Há uma API com várias funções que podem ser chamadas para exercer determinadas funções.
  - **Ex:** `read()`, `write()`, `seek()`, `close()`, etc.
### Camadas da pilha do sistema de arquivos:
- A abstração do sistema de arquivos é dividida em camadas pelo SO: Application, Library, File System, Block Cache, Block Device Interface, Devive Driver, Memory-Mapped I/O, DMA, Interrupts e Physical Device. Essas camadas possuem dois conjuntos de tarefas:
- **API e performance (Library, File System, Block Cache)**: O níveis mais altos da stack, bibliotecas a nível de usuário, sistema de arquivos a nível de Kernel e cache de bloco do Kernel fornecem uma API conveniente para acessar arquivos nomeados e também funcionam para minimizar acessos lentos de armazenamento por meio de cache, buffering de gravação e pré-busca.
- **Acesso a dispositivo(Block Device Interface, Device Driver, Memory-Mapped I/O, DMA, Interrupts)**: Níveis mais baixos na stack, provém ao SO meios de acesso aos dispositivos de entrada e saída. Os drives para esses dispositivos abstraem os detalhes de hardware específicos a cada dispositivo I/O, fornecendo um código de máquina específico a cada um e substituindo esse código por trás de um mais simples, geral por meio de uma interface para que o restante do SO possa usar.
	Os drivers de dispositivo são executados como código normal a nível de kernel, usando os principais processadores e memória dos sistemas, mas devem interagir com os dispositivos de E/S.
	Os processadores e a memória de um sistema se comunicam com seus dispositivos de E/S usando a entrada e saída mapeada em memória, DMA e Interrupções.
### Drivers: 
Os drivers de dispositivo traduzem os detalhes específicos de hardware dos dispositivos de E/S por meio de uma interface que o SO pode usar.
### Memory-mapped I/O:
Mapeia os registros de controle de cada dispositivo para um intervalo de endereços físicos no barramento de memória. 
### Direct Memory Access (DMA):
Usada para transmitir os volumes de dados da memória do dispositivo E/S para a memória do sistema de forma direta, evitando requisitar a CPU que leia ou escreva cada dado que deseja-se transmitir.
### Interrupts: 
"Notificação" enviada ao SO para informar quando um dispositivo de entrada e saída concluiu o tratamento de uma solicitação ou quando uma nova entrada externa chega.

### Os sistemas de armazenamento de computadores devem ter essas características:
- **Confiança**: Os dados do usuário devem ser armazenados com segurança mesmo que a máquina desligue ou o sistema operacional quebra ou trava. Mais que isso, devem manter seguros mesmo que o próprio computador esteja danificado.
- **Alta capacidade de armazenamento e baixo custo**: Usuários comuns e empresas armazenam um alto volume de dados, então seria interessante comprar dispositivos capazes de armazenar essa densidade de conteúdos por um baixo custo.
- **Alta performance**: Para programas usarem dados, eles precisam conseguir acessá-la e realizar isso de forma eficiente e rápido. Por exemplo, a inicialização de um programa deve ser quase que instantânea, principalmente se a aplicação for de extrema importância.
- **Dados nomeados**: O sistema de arquivos deve ter um sistema de identificação de cada arquivo. Isso facilita a procura, pois, por exemplo, quero buscar um programa em python que criei, basta eu nomeá-lo adequadamente que eu consigo fazer uma rápida nos meus arquivos por seu nome e achá-lo.
- **Compartilhamento controlado**: Usuários podem compartilhar dados, porém podem escolher se será apenas pra leitura ou escrita, ou apenas um, ou sem acesso nenhum aos dados. Além de controlar pra quem ou para quais outros usuários ele deseja realizar tais modos de compartilhamentos.
- **Armazenamento não volátil**: O conteúdo deve manter seu estado mesmo que o sistema quebre ou faltar energia (derrepente desligar por que o pc tava sem bateria), outro nome pra isso é armazenamento persistente ou armazenamento estável.
  - Observação: Memória RAM é volátil, ou seja, caso o sistema falhe ou acabe a energia, os dados seriam perdidos.
  - Tradeoff: Memória não volátil como HDD e SSD não permitem acesso aleatório a palavras individuais de armazentamento, em vez disso o acesso deve ser feito em porções de 512, 2048 ou mais bytes por vez. Isso deixa o acesso mais lento.
- **Sistema de arquivos**: permitem que as aplicações acessem armazenamento não volátil. Os files system são uma abstração comum aos sistema operacionais, eles utilizam várias técnicas para lidarem com limitações físicas das unidades de armazenamento e fornecer uma melhor manipulação dos dados por parte do usuário.

## Exercício

1) Suponha que um processo abra com sucesso um arquivo existente que tenha um único hard link para ele, mas enquanto o processo está lendo esse arquivo, outro processo desvincula esse arquivo. O que deve acontecer com as leituras subsequentes do primeiro processo? Elas devem ter sucesso? Elas devem falhar? Por quê?  
    - Se um processo abre um arquivo que possui apenas um hard link e, enquanto o lê, outro processo remove esse link com `unlink()`, as leituras continuarão funcionando normalmente. Isso porque, em sistemas como Unix/Linux, `unlink()` remove apenas o nome do arquivo do diretório, mas o conteúdo do arquivo — controlado pelo inode — permanece acessível enquanto houver descritores de arquivo abertos. Assim, o processo que abriu o arquivo antes da remoção pode continuar lendo seu conteúdo sem falhas.

2) Considere um editor de texto que salva um arquivo sempre que você clica no botão de salvar. Suponha que, ao pressionar esse botão, o editor simplesmente (1) anima o evento de pressionar o botão (por exemplo, colorindo o botão de cinza), (2) usa a chamada de sistema `write()` para gravar seu texto no arquivo e então (3) anima o evento de soltar o botão (por exemplo, colorindo o botão de branco). O que de ruim pode acontecer se um usuário editar um arquivo, salvá-lo e então desligar o computador diretamente pelo botão de energia (em vez de desligar o sistema corretamente)?  
    - Se o editor de texto usa apenas a chamada `write()` para salvar o arquivo, mas não força a gravação no disco, há um risco real de perda de dados. Isso acontece porque o sistema operacional pode manter os dados apenas em cache por um tempo, mesmo após a conclusão da write(). Assim, se o usuário desligar a máquina abruptamente logo após clicar em salvar, o conteúdo pode nunca ter sido escrito fisicamente no disco, resultando em perda ou corrupção do arquivo. Para garantir a integridade dos dados, o editor deveria forçar a sincronização com o disco antes de indicar que o salvamento foi concluído.
	 
