"Para cada um dos endereços virtuais decimais seguintes, calcule o número da página virtual e deslocamento para (i) páginas de 4 KB e (ii) páginas de 8 KB: 20.000, 32.768, 60.000."



ABSTRAÇÕES DE MEMÓRIA:



Antes dos anos 80, as memórias não eram tidas de maneira abstrata e, portanto, os programas tinham acesso à toda memória, acessando diretamente pelo seu endereço.



Um exemplo do slide é o comando "MOV R1, 1000", movendo o conteúdo da memória 1000 ao R1.



No entanto, se mais de um programa é rodado consecutivamente, há o risco de um invadir o outro e subscrever o trabalho doutro. Desse modo, criou-se a Realocação Estática, que transforma o endereço antes de qualquer execução, porém é caro pois exige várias substituições e distinções entre o que é endereço e o que é valores numéricos. Desse modo, como que o endereço 28 de um programa A pode ter uma localização física diferente do endereço 28 de um programa B? Existe a Realocação Dinâmica, transferindo endereços durante a execução do programa e implementado com 2 registradores: base e limite. Essa é a nossa primeira abstração. Imagem ilustrativa no slide 7 da aula 9a.



E caso não haja memória disponível para todos os processos? Existem, então, o método de *swapping* e de Memória Virtual. 



SWAPPING:

Atua trazendo processos para memória, executando-os e devolvendo-os ao disco. Chaveia processos inteiros entre a memória principal e o disco.

Termos cruciais são *Swap-out*, quando o processo vai da memória para uma região especial do disco, chamada "área de swap", e o outro termo é o *Swap-in*, quando o processo vai do disco para a memória. No slide 10 da aula 9.



MEMÓRIA VIRTUAL:

Permite que programas sejam executados mesmo que estejam parcialmente carregados na memória principal. É utilizado duas técnicas principais para isso: Paginação e Segmentação, que diz respeito a rigidez do tamanho dos blocos, se são fixos ou variáveis.



A paginação é fixa em seu tamanho de bloco. Um processo em Memória Virtual faz referência a endereços virtuais e não a endereços reais de RAM. Então, quando uma instrução será executada, o endereço virtual é traduzido para um endereço real. Portanto, deve existir um mapeamento. Outrossim, como adição, a CPU manipula apenas endereços reais da RAM.



Saindo do assunto principal, o mapeamento é conduzido por uma hardware especial presente na CPU, o MMU, o *memory management unit*.



Voltando, um exemplo seria um PC que gera até 64K de endereços virtuais (16 bits), tendo apenas 32KB de memória física. Um programa de 64KB pode ser escrito, mas não carregado inteiramente na memória. Uma cópia de 64KB deve estar no disco para que partes possam ser carregadas à memória.



Idealmente, a MMU busca o endereço através de um índice na tabela de páginas, criado a partir do endereço virtual. Na MMU, vai chegar uma entrada binária que é o endereço virtual, aí o *page number* e o *offset* serão lidos. O *page number* é o index para entrar na tabela de páginas e o *offset* é a posição dentro da página e é copiada sem mudanças para o endereço físico. Ao acessar a página através do index, teremos o *frame number* da memória física, o que também vai à memória física. Logo, o endereço físico carrega o *frame number* e o *offset* já presente no virtual.



Página

















