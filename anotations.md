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



