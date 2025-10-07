# CPU de 8 Bits

## Introdução
Neste projeto, desenvolvi uma CPU simplificada de 8 bits utilizando o simulador Digital.  
O objetivo foi implementar uma ULA (Unidade Lógica e Aritmética) completa, integrada a uma Unidade de Controle e a um Datapath, simulando o funcionamento básico de um processador real.

Além das operações aritméticas e lógicas, também foi implementado o ciclo de busca (fetch) e execução (execute), coordenado por um sequenciador de clock e visualizado em displays de 7 segmentos que mostram o resultado final.

---

## Funcionalidades da ULA

A ULA foi projetada para suportar as seguintes operações principais:

- Soma (8 bits) – utilizando um conjunto de Full Adders conectados em cascata.  
- Subtração (8 bits) – realizada por complemento de dois (inversão de B + 1).  
- Multiplicação (operandos de 4 bits) – com portas AND e somadores parciais.  
- Divisão (operandos de 4 bits) – implementando quociente e resto simples.  
- Incremento (8 bits) – adiciona 1 ao valor atual.  
- Decremento (8 bits) – subtrai 1 do valor atual.  
- Shift Left (8 bits) – desloca todos os bits uma posição para a esquerda (multiplicação por 2).  

Além das operações da ULA, a CPU também reconhece instruções de controle:

- LOAD – carrega um valor no acumulador.  
- JUMP – salto incondicional para outro endereço de memória.  
- JUMP Z – salto condicional (executado apenas se a flag Z = 1, ou seja, resultado igual a zero).  
- OUT – envia o conteúdo do acumulador para os displays de saída.  

A escolha da operação é feita através de um opcode de 3 bits, controlado pela Unidade de Controle, que ativa o sinal correspondente dentro da ULA.

---

## Componentes Principais

### Unidade de Controle (`ControlUnit.dig`)
Responsável por interpretar o opcode e gerar os sinais de controle que coordenam todo o ciclo de execução.  
Ela define quando cada registrador deve ler ou escrever dados, quando a ULA deve operar e quando o resultado deve ser exibido.

A Unidade de Controle também possui um sequenciador (`sequenciator.dig`) que divide o clock em seis tempos (T1 a T6), representando as etapas do ciclo de instrução.

---

### Datapath (`DataPath.dig`)
O Datapath é o caminho dos dados dentro da CPU.  
Ele conecta os principais componentes — registradores A e B, registrador de instrução (IR), contador de programa (PC), memória e ULA — através de um barramento de dados de 8 bits (WBus).

O barramento é controlado por um multiplexador, que seleciona qual fonte de dados (A, B, ULA, PC ou memória) vai colocar seu valor na linha principal.

---

### Registradores (`ResettableRegister.dig`, `RegA`, `RegB`, `IR`)
Cada registrador possui:
- Entrada de dados (D_in);
- Sinal de Load (Ld) para armazenar o valor;
- Sinal de Reset (RST) para zerar;
- Saída (Q_out) conectada ao barramento ou à ULA.

Esses registradores armazenam os operandos, resultados intermediários e a instrução atual.

---

### Contador de Programa (`PC.dig` / `newPC.dig`)
O Program Counter (PC) é responsável por apontar qual endereço da memória será lido em seguida.  
A cada ciclo, ele é incrementado automaticamente, exceto quando ocorre um salto (JUMP ou JUMP Z), caso em que ele recebe um novo valor de endereço.

---

### Memória (`memory.dig`)
A memória ROM contém o programa que a CPU executa.  
Cada posição guarda uma instrução composta por opcode + operando (endereço ou dado).  
Durante o ciclo de fetch, o PC envia o endereço atual para a memória, e o valor retornado é armazenado no registrador de instrução (IR).

---

### Ciclo de Fetch e Execute
O funcionamento da CPU é dividido em duas grandes fases:

1. **Fetch (Busca):**
   - O PC envia o endereço atual para a memória;
   - A memória devolve a instrução (opcode + operando);
   - O opcode é armazenado no IR e enviado para a Unidade de Controle.

2. **Execute (Execução):**
   - A Unidade de Controle ativa os sinais correspondentes à instrução;
   - A ULA realiza a operação necessária (soma, subtração, etc.);
   - O resultado é salvo no registrador A ou enviado para o display.

Todo esse processo ocorre de forma sincronizada pelo clock e sequenciada por T1 a T6.

---

### Saída e Displays
O resultado final das operações é armazenado em um registrador de saída conectado a dois displays de 7 segmentos.  
Eles exibem o valor em formato decimal, permitindo visualizar os resultados positivos ou negativos da ULA em tempo real.

---

## Estrutura de Arquivos

| Arquivo | Função |
|----------|--------|
| `5bit_Adder.dig`, `8bit_Adder.dig`, `FullAdder.dig`, `HalfAdder.dig` | Circuitos de soma em diferentes larguras. |
| `8bit_Subtract.dig` | Subtração com complemento de dois. |
| `8bits_Leftshift.dig` | Deslocamento lógico à esquerda. |
| `8bit_incrementor.dig`, `8bit_decrementor.dig` | Incremento e decremento. |
| `Multiplier.dig` | Multiplicação de 4 bits. |
| `Divisor.dig`, `Divide_step.dig` | Divisão de 4 bits com resto. |
| `ControlUnit.dig`, `Sequenciator.dig` | Unidade de controle e sequência de tempos T1–T6. |
| `DataPath.dig` | Conexão entre registradores, ULA e barramento. |
| `ResettableRegister.dig` | Modelo de registrador com reset. |
| `PC.dig`, `NewPC.dig` | Contador de programa. |
| `Memory.dig` | Memória ROM com programa de instruções. |
| `Main_CPU.dig`, `NewCPU.dig` | Montagem completa da CPU de 8 bits. |

---

## Demonstração em Vídeo
Gravei um vídeo apresentando o funcionamento completo da CPU, explicando:
- Como o ciclo de fetch busca as instruções da memória;
- Como a Unidade de Controle ativa os sinais;
- E como o resultado é mostrado nos displays.

[Link do vídeo demonstrativo]

---

## Conclusão
O desenvolvimento dessa CPU permitiu compreender e aplicar conceitos fundamentais de arquitetura de computadores, como:
- Construção de somadores, subtratores e multiplicadores em nível lógico;
- Organização de barramentos e controle de fluxo de dados;
- Implementação de ciclos de fetch e execute;
- Coordenação de todos os componentes por clock e unidade de controle.

Com isso, foi possível construir uma CPU de 8 bits funcional, capaz de executar operações aritméticas básicas e instruções de controle, consolidando o aprendizado sobre o funcionamento interno de processadores.
