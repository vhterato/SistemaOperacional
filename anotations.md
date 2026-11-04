"Para cada um dos endereços virtuais decimais seguintes, calcule o número da página virtual e deslocamento para (i) páginas de 4 KB e (ii) páginas de 8 KB: 20.000, 32.768, 60.000."



ABSTRAÇÕES DE MEMÓRIA:



Antes dos anos 80, as memórias não eram tidas de maneira abstrata e, portanto, os programas tinham acesso à toda memória, acessando diretamente pelo seu endereço.



Um exemplo do slide é o comando "MOV R1, 1000", movendo o conteúdo da memória 1000 ao R1.



No entanto, se mais de um programa é rodado consecutivamente, há o risco de um invadir o outro e subscrever o trabalho doutro. Desse modo, criou-se a Realocação Estática, que transforma o endereço antes de qualquer execução, porém é caro pois exige várias substituições e distinções entre o que é endereço e o que é valores numéricos. Desse modo, como que o endereço 28 de um programa A pode ter uma localização física diferente do endereço 28 de um programa B? Existe a Realocação Dinâmica, transferindo endereços durante a execução do programa e implementado com 2 registradores: base e limite. Essa é a nossa primeira abstração. Imagem ilustrativa no slide 7 da aula 9a.



E caso não haja memória disponível para todos os processos? Existem, então, o método de *swapping* e de Memória Virtual.



SWAPPING:

Atua trazendo processos para memória, executando-os e devolvendo-os ao disco. Chaveia processos inteiros entre a memória principal e o disco.

Termos cruciais são *Swap-out*, quando o processo vai da memória para uma região especial do disco, chamada "área de swap", e o outro termo é o *Swap-in*, quando o processo vai do disco para a memória. No slide 10 da aula 9.



Bitmap: ocupa tamanho fixo na memória, pois só depende do tamanho da memória e da unidade de alocação. (REVER)

Listas Ligadas: mantêm-se uma lista ligada de segmentos de memória livres e alocados, como na imagem do slide 9 na página 13. (REVER)



MEMÓRIA VIRTUAL:

Permite que programas sejam executados mesmo que estejam parcialmente carregados na memória principal. É utilizado duas técnicas principais para isso: Paginação e Segmentação, que diz respeito a rigidez do tamanho dos blocos, se são fixos ou variáveis.



A paginação é fixa em seu tamanho de bloco. Um processo em Memória Virtual faz referência a endereços virtuais e não a endereços reais de RAM. Então, quando uma instrução será executada, o endereço virtual é traduzido para um endereço real. Portanto, deve existir um mapeamento. Outrossim, como adição, a CPU manipula apenas endereços reais da RAM.



Saindo do assunto principal, o mapeamento é conduzido por uma hardware especial presente na CPU, o MMU, o *memory management unit*.



Voltando, um exemplo seria um PC que gera até 64K de endereços virtuais (16 bits), tendo apenas 32KB de memória física. Um programa de 64KB pode ser escrito, mas não carregado inteiramente na memória. Uma cópia de 64KB deve estar no disco para que partes possam ser carregadas à memória.



Idealmente, a MMU busca o endereço através de um índice na tabela de páginas, criado a partir do endereço virtual. Na MMU, vai chegar uma entrada binária que é o endereço virtual, aí o *page number* e o *offset* serão lidos. O *page number* é o index para entrar na tabela de páginas e o *offset* é a posição dentro da página e é copiada sem mudanças para o endereço físico. Ao acessar a página através do index, teremos o *frame number* da memória física, o que também vai à memória física. Logo, o endereço físico carrega o *frame number* e o *offset* já presente no virtual.



Bits da página: "Moldura da página" é o "frame number" do endereço físico. Bit de residência é aquele vistoria se a página está na memória ou não. Bits de proteção, aqueles os quais dizem se uma página é para leitura, escrita e/ou execução. Há o "bit de modificação" que controla o uso da página, caso igual a 1, a moldura deve ser copiada para o disco, se removida da memória, e caso igual a 0, desse modo, a página não foi modificada e é assim abandonada, visto que a cópia em disco é válida. Também há o de referência e, sempre que a página é referenciada, o hardware faz o bit ser 1. Bit de Cache permite desabilitar o mecanismo de cache para a página, o que é necessário para páginas que acessam registradores de dispositivos ao invés de memória.



Implementação:



"Uma vez que a tabela reside no espa ̧co do processo,

e o mapeamento  ́e feito pela MMU, como se d ́a a

liga ̧c ̃ao entre eles?"



Uma forma é ter a tabela na forma de um arranjo de registradores, na MMU, com uma entrada para cada página virtual. Tais registradores são indexados pelo número da página e lembre-se que a tabela tem tamanho fixo pré-definido.

Desse modo, quando um processo é inicializado, o SO carrega os registradores com a tabela de páginas desse processo. A maior vantagem dessa maneira é não precisar referenciar à memória durante o mapeamento, vide que a tabela está carregada. Todavia, cada troca de desempenho necessitará de carregar toda tabela. Ademais, se a tabela for grande, é um método caro.



Outra forma é manter a tabela inteiramente na memória. Daí, o *hardware* precisa somente de um único registrador que aponte para o início da tabela. Dessa maneira, uma mudança de contexto alterará meramente esse registrador. Assim sendo, a maior vantagem desse método é acomodar tabelas grandes com facilidade, porém a desvantagem é precisar de referências extras à memória para que possa ler as entradas na tabela.



Vinda dessa maneira, há como utilizar 2 registradores para uma tabela: um de base da tabela e um outro para o tamanho da tabela. O primeiro aponta para o início da tabela de página, indicando o endereço físico de memória onde a tabela está alocada. Aliás, esse primeiro é conhecido como *page table base register*. O segundo é existente apenas em alguns sistemas. Nele, cada acesso de dados ou de instrução exige dois acessos à memória. No entanto, a maioria dos programas faz um grande número de referências à uma pequena faixa de páginas, ou seja, apenas uma fração pequena das entrada da tabela são lidas com grande frequência.



Desse modo, a utilização de caches de hardwares pode ser uma resolução para esse problema através do TLB, que mapeia endereços virtuais para endereços físicos sem ser forçado a acessar a memória principal. Ele consiste de um pequeno número de entradas, cada uma com informações sobre uma página. TLB significa Translation Lookaside Buffer. Ele é chamado também de memória associativa e é administrado por Hardware ou por Software.



Hardware: dessa maneira ocupa espaço real do computador que poderia ser usado para outras finalidades, mas é mais rápido. Se faltar página, o MMU gera uma interrupção de proteção de proteção e aciona o sistema operacional. Daí o processo é suspenso e seu descritor é inserido na fila dos processos esperando uma página virtual. Desse modo, uma moldura livre (na memória) deve ser alocada. Copy on Write: quando um processo modifica a memória, o SO faz a cópia efetiva. Imagem boa no slide 9, página 59 (REVER).



Software: as entradas da TLB no hardware são carregadas pelo SO. Em caso de *page fault* na TLB, a MMU faz uma interrupção ao SO. Aí o SO busca a página na tabela de páginas, destitui uma entrada da TLB, insere aí a nova página e reinicializa a instrução interrompida. As páginas contendo a própria tabela de páginas podem não estar na TLB, quando do processamento de uma TLB miss -> Causará novas TLB misses durante o processo.



Tipos de ausências:

&#x09;Soft miss: quando a página referenciada não está na TLB, mas está na memória física, daí só basta atualizar a TLB.

&#x09;Hard miss: a página em si não está na memória física (e nem na TLB, naturalmente). Daí deve-se fazer um acesso ao disco para trazê-la à memória (e então à TLB). Muito lento.



SLIDE 9b:



Qual página devemos substituir?



POLÍTICA DE SUBSTITUIÇÃO LOCAL: substitui aquela menos usada recentemente em um processo. Aloca a cada processo fração fixa da memória.



Thrashing é quando um processo gasta mais tempo paginando do que executando.



POLÍTICA DE SUBSTITUIÇÃO GLOBAL: as páginas de todos os processos são utilizadas na troca. Não aloca para cada processo uma fração fixa. Nessa política, processos com menor prioridade podem ter um número muito reduzido de páginas, o que causa falta de páginas e thrashing.



ALOCAÇÃO FIXA OU ESTÁTICA: cada processo tem um número máximo de molduras definido quando o processo é criado: em um page fault, considera-se apenas molduras do próprio processo. O limite pode ser igual por processo ou seguir alguma distribuição. A maior vantagem é a simplicidade e a maior desvantagem são essas: se houver muitas molduras numa página, ocorrerá o desperdício de memória, o contrário é o thrashing.



ALOCAÇÃO VARIÁVEL OU DINÂMICA: número de molduras alocadas ao processo varia durante sua execução. Através dela, pode usar-se o algoritmo de page faut frequency, que me a frequência de faltas de página - se alta, aumenta o volume de molduras e, se baixa, reduz esse limite. A maior vantagem nesse esquema é que processos com elevada taxa de paginação podem ter seu limite de molduras ampliado e, aqueles com baixo, reduzidos. A desvantagem é o monitoramento constante no entanto.



ESTRATÉGIAS DE PAGINAÇÃO:



Paginação simples: todas as páginas virtuais do processo são carregadas para a memória principal. Desse modo, todas as páginas são sempre válidas.



Paginação por demanda (Demand Paging): processos começam com nenhuma página na memória. Daí a CPU executa a primeira instrução, acarretando page fault e sendo trazido uma nova página. Esse, atualmente, é o método mais popular. Nesse estratégia, as páginsa são carregas na memória somente quando são necessárias.



ALGORITMOS DE SUBSTITUIÇÃO DE PÁGINAS:



Algoritmo ótimo: retira-se da memória a página que tem o maior número de instruções faltantes. Ele sempre adia ao máximo a próxima falta de página. É o ideal e impraticável.



NRU - Not Recently Used: classifica as páginas com molduras atualmente na memória. Usa os bits de status (associados à cada página) da tabela de páginas -> R (eferenciada) e M (odificada). "?A referência garante a lida ou escrita, enquanto a modificação mostra que ela foi escrita. Armazenadas em cada entrada da tabela de página e seu valor é determinado pelo hardware.?"



Processo iniciou e ambos R e M são 0 para todas suas páginas. Periodicamente, o bit R é limpo, diferenciando das páginas que não foram referenciadas recentemente, o que é possível fazer-se à cada interrupção de relógio. O bit M não é limpo, pois o S.O precisa saber se deve escrever a página no disco.



Quando ocorre uma falta de página: aleatoriamente remove uma página, escolhendo dentre as classes mais baixas -> 00, 01, 10, 11. Dá preferência a quem não foi recentemente usada e não precisa volta ao disco.



FIFO - First-in First-out: o SO mantém uma fila das páginas atualmente na memória. Quando ocorre um page fault, a página mais antiga é removida e a mais nova é inserida no final da fila. É simples, mas ineficiente, pois pode retirar uma página de uso constante.



Segunda Chance: FIFO + bit R, assim evitando que se jogue fora páginas intensamente usadas. Nesse, inspeciona o bit R da página mais antiga. Se for 0, ela é velha (está no início da fila) e não foi usada recentemente -> é trocada.



Algoritmo do relógio: é uma melhoria do algoritmo de segunda chance. Teremos uma lista circular com ponteiro (cabeça) que aponta à página mais antiga, como se fosse um relógio. Ao ocorrer um page fault, inspeciona-se a cabeça da lista e, se R = 0, substitui-se a página da cabeça pela nova página e avança uma posição do ponteiro. Todavia, caso R = 1, aquela página será salva, mas seu R mudará para 0 até que seja referenciada novamente e, daí, o ponteiro avança e continua até encontrar uma página com R = 0 para eliminar e substituir.



LRU - Least Recently Used: a ideia consiste em substituir as páginas que estão em desuso pelo maior tempo (tempo desde a última referência). Aí deve-se manter uma lista com todos as páginas que estão na memória, sendo que as mais recentemente utilizadas irão ao início enquanto as menos estarão no final. Tal lista deve ser atualizada a cada referência de memória.



Implementa-se tanto através de hardware quanto por software. Se por Hardware, a MMU deve suportar a implementação LRU. Contador em hardware (64 bits) é incrementando automaticamente após cada instrução. E , se a política for local, o contador inicia com o início do processo e, caso global, o contador inicia com início do sistema. Ainda em Hardware, após cada referência à memória, o valor atual de C (contador) é armazenado na entrada correspondente na tabela, ou seja, a tabela de páginas armazena o valor desse contador em cada entrada. Daí em diante em um page fault, o S.O examina todas as entradas na tabela até encontrar aquela com o menor C.



NFU - Not Frequently Used: implementações em hardware da LRU são raríssimas e há que se simular em software. Daí se segue assim: para cada página existe um contador iniciado com zero, a cada interrupção do clock, o SO varre todas as páginas da memória, para cada página, adiciona o bit R ao contador correspondente e, daí, conta por quantos tiques do clock cada página foi referenciada. Daí, aquela com o mentor contador será substituída.



Aging é crucial para lidar com pages que, apesar de possuírem contadores altos, não serão mais usadas, o que poderia resultar em desperdício de espaço. Trata-se de locomover o contador um bit à direita e daí o bit R é adicionado àquele mais à esquerda. Desse modo, páginas que não tenham sido referenciadas por mais tempo, terão mais zeros em seus bits mais significativos.



Working Set (WS): é um conjunto de páginas que um processo está usando atualmente. Logo, w(k, t) é o conjunto que consiste de todas as páginas usadas pelas k referências mais recentes à memória em um dado instante t. 



Existe uma propriedade que é apresentada pela maioria dos processos: a Localidade de Referência, tratando-se de que, durante qualquer fase de execução, o processo só vai referenciar uma fração relativamente pequena de suas páginas.



A meta é reduzir a falta de páginas e um processo é somente executado quando todas as páginas necessárias no tempo t estão carregadas na memória.



O WS varia lentamente com o tempo e podemos estimar o número de páginas necessárias quando o programa é trazido do disco com seu ws de quando foi interrompido.  Daí, pré-paginação consiste em carregar essas páginas antes de rodar novamente o processo.



Ao implementar, o SO mantem o registro de páginas que estão no ws e, daí, quando ocorre um page fault, encontra uma página fora do working set e a remove da memória. Utiliza-se o bit R e o tempo de relógio da última vez que a página foi referenciada.



No WS, se o R = 1 no momento do page fault, significa que ele estava em uso e não é candidata à remoção. Fosse R = 0 e passaria a ser uma candidata e, se sua idade fosse maior que o intervalo t do working set, seria removida por não estar em ws. Daí a nova página entra no local. Todavia, se R = 0 é uma condição necessária, porém não suficiente. Se a idade da página for menor ou igual a t, aí a página é poupada. E caso não haja sequer uma que seja menor do que t, a mais velha é, assim, substituída. E, caso todas sejam iguais a R = 1, escolhe-se aleatoriamente alguma para remoção, de preferência alguma sem modificações.



No WSclock, um anel começa vazio e é preenchido por páginas à medida que o processo as usa. Disso, um ponteiro vai avaliando as páginas nas mesmas diretrizes já escritas no WS, tendo algumas exceções: se R = 0 e M = 0, some, mas, se M = 1, agenda-se uma escrita ao disco, evitando troca de processo. Então, avança o ponteiro. Se, após o ponteiro dar volta completa, houver ao menos uma escrita no disco agendada, o ponteiro continua se movendo em busca de uma página limpa e, em algum momento, a escrita agendada será executada, marcando a página como limpa. Caso nenhuma página foi substituída após a volta do ponteiro e caso haja um page fault, substituiu qualquer página limpa ou, se nenhuma limpa exisitir, escreva a atual no disco.



WS e WSClock são locais, enquanto os outros algorítimos podem ser locais ou globais.



IMPLEMENTAÇÃO DE PAGINAÇÃO:



Problema: endr de disco usados para armazenas páginas que não estão na memória não fazem parte da tabela de páginas. Onde então colocar as páginas no disco, quando retiradas da memória? Aliás, como saber onde estão? Como gerenciar isso?



Uma simples solução é uma partição de swap, o que é efetuado no Unix e no Linux, não possuindo um sistema de arquivos normais Aí, ao iniciar o SO, a partição swap está vazia e ela é representada na memória como uma única entrada a conter sua localização e tamanho. À medida em que processos são iniciados, o SO reserva um pedaço da área de swap do tamanho do processo. É gerenciável como uma lsita de espaços disponíveis. A cada processo está associado o endereço de sua área de swap no disco.



Antes de um processo iniciar, a área de swap iniciar-se-á primeiro. Existe a possibilidade A: assim que criado, ele é copiado para sua área de swap dentro do disco, sendo carregado para memória quando necessário.

