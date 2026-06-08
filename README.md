# Buck-Boost CCM

## Requisitos de Projeto

Os parâmetros especificados para o projeto do conversor CC-CC Buck-Boost operando em modo de condução contínua (CCM) são:

| Parâmetro | Valor |
|------------|--------|
| Tensão de entrada ($V_{in}$) | 30 V |
| Potência nominal da carga ($P_o$) | 15 W |
| Tensão de saída ($V_o$) | 20 V |
| Ondulação máxima da tensão de saída ($\Delta V_o$) | 10 % |
| Frequência de comutação ($f_s$) | 40 kHz |
| Ondulação da corrente no indutor ($\Delta I_L$) | 30 % |

---

## Resultados Obtidos

Após a execução do script `projeto.mlx` utilizando os parâmetros especificados, foram obtidos os seguintes resultados para o dimensionamento do conversor:

| Grandeza | Valor |
|-----------|--------|
| Duty Cycle ($D$) | 40 % |
| Resistência da carga ($R$) | 26,67 Ω |
| Indutância ($L$) | 800 µH |
| Capacitância ($C$) | 3,75 µF |
| Corrente média no indutor ($I_{L,med}$) | 1,25 A |
| Corrente mínima no indutor ($I_{L,min}$) | 1,0625 A |
| Corrente máxima no indutor ($I_{L,max}$) | 1,4375 A |
| Tensão máxima aplicada ao MOSFET | 50 V |
| Corrente máxima de dreno do MOSFET | 1,4375 A |
| Tensão máxima aplicada ao diodo | 50 V |
| Corrente máxima no diodo | 1,4375 A |

---

## Especificações Mínimas dos Componentes

Com base nos resultados obtidos, os componentes devem suportar, no mínimo:

### MOSFET

| Parâmetro | Valor mínimo |
|------------|--------------|
| Tensão dreno-fonte ($V_{DS}$) | 50 V |
| Corrente de dreno ($I_D$) | 1,44 A |

### Diodo

| Parâmetro | Valor mínimo |
|------------|--------------|
| Tensão reversa ($V_R$) | 50 V |
| Corrente direta ($I_F$) | 1,44 A |

### Indutor

| Parâmetro | Valor |
|------------|--------|
| Indutância | 800 µH |
| Corrente de saturação | > 1,44 A |

### Capacitor de Saída

| Parâmetro | Valor |
|------------|--------|
| Capacitância | 3,75 µF |
| Tensão nominal | > 20 V |


## Escolhendo componentes comerciais

### Análise do MOSFET: IRF530

Com base nas informações obtidas no datasheet do MOSFET IRF530 (`irf530.pdf`), verifica-se que o componente apresenta características elétricas superiores às exigidas pelo projeto.

#### Especificações do IRF530

| Especificação                                            | Valor                    |
| -------------------------------------------------------- | ------------------------ |
| Tensão dreno-fonte ($V_{DS}$)                            | 100 V                    |
| Corrente contínua de dreno ($I_D$)                       | 10 A (100 °C)            |
| Corrente de dreno pulsada                                | 56 A                     |
| Carga total de gate ($Q_g$)                              | máx. 26 nC               |
| Carga gate-source ($Q_{gs}$)                             | máx. 5,5 nC              |
| Carga gate-drain ($Q_{gd}$)                              | máx. 11 nC               |
| Tensão de limiar ($V_{GS(th)}$)                          | 2,0 V a 4,0 V            |
| Capacitância de entrada ($C_{iss}$)                      | típ. 670 pF              |
| Capacitância de saída ($C_{oss}$)                        | típ. 250 pF              |
| Tempo de subida ($t_r$)                                  | típ. 34 ns               |
| Tempo de descida ($t_f$)                                 | típ. 24 ns               |
| Transcondutância direta ($g_{fs}$)                       | mín. 5,1 S               |
| Resistência interna de gate ($R_g$)                      | 1,0 Ω a 4,7 Ω            |
| Tempo de recuperação reversa do diodo interno ($t_{rr}$) | típ. 150 ns, máx. 280 ns |

#### Comparação com os requisitos do projeto

##### Especificações mínimas necessárias

| Parâmetro                     | Valor mínimo |
| ----------------------------- | ------------ |
| Tensão dreno-fonte ($V_{DS}$) | 50 V         |
| Corrente de dreno ($I_D$)     | 1,44 A       |

##### Especificações do IRF530

| Parâmetro                          | IRF530 |
| ---------------------------------- | ------ |
| Tensão dreno-fonte ($V_{DS}$)      | 100 V  |
| Corrente de dreno contínua ($I_D$) | 10 A   |
| Corrente de dreno pulsada          | 56 A   |

Observa-se que a tensão máxima exigida pelo conversor é de 50 V, enquanto o IRF530 suporta até 100 V, fornecendo uma margem de segurança de aproximadamente 100%. Da mesma forma, a corrente máxima calculada para a chave é de aproximadamente 1,44 A, valor significativamente inferior à corrente contínua suportada pelo dispositivo, que é de 10 A.

Além disso, os tempos de comutação do IRF530 são adequados para a frequência de operação do conversor. Considerando a frequência de chaveamento especificada de:

$$
f_s = 40,kHz
$$

o período de comutação é:

$$
T_s=\frac{1}{f_s}=\frac{1}{40000}=25\,\mu s
$$

De acordo com o datasheet, o MOSFET apresenta tempo de subida típico de:

$$
t_r = 34\,ns
$$

e tempo de descida típico de:

$$
t_f = 24\,ns
$$

Assim, o tempo total de transição é:

$$
t_{sw}=t_r+t_f=34+24=58\,ns
$$

Comparando o tempo de transição com o período de chaveamento:

$$
\frac{t_{sw}}{T_s}=\frac{58\,ns}{25\,\mu s}=\frac{58}{25000}\approx 0,0023
$$

Portanto,

$$
t_{sw}\approx0,23\%\,T_s
$$

Isso significa que o MOSFET permanece apenas cerca de **0,23% do ciclo** realizando a transição entre os estados ligado e desligado, permanecendo em condução ou bloqueio durante aproximadamente **99,77% do período**. Dessa forma, as perdas associadas à comutação tendem a ser reduzidas, contribuindo para uma operação eficiente do conversor na frequência especificada.

Além da análise temporal, é possível estimar quantitativamente as perdas de potência do MOSFET durante a operação do conversor.

As perdas de comutação podem ser aproximadas por:

$$
P_{sw}=\frac{1}{2}V_{DS}I_D(t_r+t_f)f_s
$$

Substituindo os valores do projeto:

$$
V_{DS}=50\,V
$$

$$
I_D=1,44\,A
$$

$$
t_r=34\,ns
$$

$$
t_f=24\,ns
$$

$$
f_s=40\,kHz
$$

obtém-se:

$$
P_{sw}=\frac{1}{2}\cdot50\cdot1,44\cdot(34+24)\times10^{-9}\cdot40000
$$

$$
P_{sw}\approx0,084\,W\approx84\,mW
$$

As perdas por condução podem ser estimadas utilizando a resistência dreno-fonte em condução do dispositivo. De acordo com o datasheet, o IRF530 apresenta:

$$
R_{DS(on)}
\approx0,16\,\Omega
$$

Assim, considerando a corrente máxima calculada para o projeto:

$$
P_{cond}=I_D^2R_{DS(on)}
$$

$$
P_{cond}=(1,44)^2\cdot0,16\approx0,33\,W
$$

A potência total dissipada pelo MOSFET pode então ser estimada por:

$$
P_{tot}=P_{cond}+P_{sw}
$$

$$
P_{tot}=0,33+0,084\approx0,41\,W
$$

Observa-se que as perdas por condução são predominantes, representando aproximadamente:

$$
\frac{0,33}{0,41}\times100\approx80\%
$$

das perdas totais, enquanto as perdas de comutação correspondem a cerca de:

$$
\frac{0,084}{0,41}\times100\approx20\%
$$

Esse resultado demonstra que, para a frequência de operação de 40 kHz, os reduzidos tempos de subida e descida do IRF530 limitam as perdas de chaveamento a valores relativamente baixos. Assim, a maior parcela da potência dissipada está associada à resistência de condução do dispositivo.

Com base na análise realizada, conclui-se que o MOSFET **IRF530** atende com ampla margem de segurança aos requisitos elétricos do conversor Buck-Boost projetado. O dispositivo suporta tensões e correntes significativamente superiores às exigidas pelo circuito, apresenta velocidade de comutação compatível com a frequência de operação de 40 kHz e perdas totais estimadas em aproximadamente **0,41 W**, sendo cerca de **0,33 W** provenientes da condução e apenas **0,084 W** associadas ao chaveamento. Dessa forma, o componente oferece operação confiável, reduzidas perdas e desempenho adequado para atuar como chave eletrônica do conversor.


### Análise do Diodo: MBR360

Com base nas informações obtidas no datasheet do diodo Schottky MBR360 (`MBR350-D.PDF`), verifica-se que o componente apresenta características elétricas compatíveis com os requisitos do conversor Buck-Boost projetado. O dispositivo foi desenvolvido para aplicações em conversores chaveados de alta frequência, apresentando baixa queda de tensão direta e reduzidas perdas de condução. 

#### Especificações do MBR360

| Especificação                                         | Valor            |
| ----------------------------------------------------- | ---------------- |
| Tensão reversa máxima ($V_R$)                         | 60 V             |
| Corrente média direta ($I_O$)                         | 3 A              |
| Corrente de surto não repetitiva ($I_{FSM}$)          | 80 A             |
| Queda de tensão direta ($V_F$) @ 1 A                  | 0,60 V           |
| Queda de tensão direta ($V_F$) @ 3 A                  | 0,74 V           |
| Corrente reversa @ 25 °C                              | 0,60 mA          |
| Corrente reversa @ 100 °C                             | 20 mA            |
| Temperatura de operação da junção                     | -65 °C a 150 °C  |
| Resistência térmica junção-ambiente ($R_{\theta JA}$) | 28 °C/W          |
| Tecnologia                                            | Schottky Barrier |

#### Comparação com os requisitos do projeto

##### Especificações mínimas necessárias

| Parâmetro               | Valor mínimo |
| ----------------------- | ------------ |
| Tensão reversa ($V_R$)  | 50 V         |
| Corrente direta ($I_D$) | 1,44 A       |

##### Especificações do MBR360

| Parâmetro                     | MBR360 |
| ----------------------------- | ------ |
| Tensão reversa máxima ($V_R$) | 60 V   |
| Corrente média direta ($I_O$) | 3 A    |
| Corrente de surto ($I_{FSM}$) | 80 A   |

Observa-se que a tensão reversa máxima exigida pelo conversor é de 50 V, enquanto o MBR360 suporta até 60 V, fornecendo uma margem de segurança de aproximadamente 20%.

Da mesma forma, a corrente máxima calculada para o diodo é de aproximadamente 1,44 A, valor inferior à corrente média contínua suportada pelo dispositivo, que é de 3 A. Assim, o componente opera com uma margem superior a 100% em relação à corrente requerida pelo projeto.

Outro aspecto importante é que o MBR360 é um **diodo Schottky**. Diferentemente dos diodos retificadores convencionais de silício (junção PN), os diodos Schottky são construídos a partir de uma junção metal-semicondutor. Essa característica resulta em uma menor barreira de potencial, proporcionando uma queda de tensão direta reduzida, tipicamente entre 0,3 V e 0,8 V. Além disso, a condução ocorre predominantemente por portadores majoritários, o que praticamente elimina o armazenamento de carga na junção.

Como consequência, os diodos Schottky apresentam tempo de recuperação reversa extremamente pequeno quando comparados aos diodos PN convencionais. Isso reduz significativamente as perdas durante as transições de chaveamento, tornando esse tipo de dispositivo especialmente adequado para aplicações em conversores CC-CC e fontes chaveadas operando em altas frequências.

Além disso, a baixa queda de tensão direta do MBR360 reduz as perdas por condução. Considerando uma corrente próxima da condição nominal do projeto, a potência dissipada no diodo pode ser estimada por:

$$
P_D \approx V_F \cdot I_D
$$

Assumindo uma queda de tensão média de aproximadamente:

$$
V_F \approx 0,65\,V
$$

e corrente de dreno:

$$
I_D \approx 1,44\,A
$$

obtém-se:

$$
P_D \approx 0,65 \cdot 1,44
\approx 0,94\,W
$$

om base na análise realizada, conclui-se que o diodo MBR360 atende aos requisitos elétricos do conversor Buck-Boost projetado. O dispositivo suporta tensões e correntes superiores às exigidas pelo circuito, fornecendo margens adequadas de segurança para a operação. Além disso, sua baixa queda de tensão direta resulta em menores perdas por condução, contribuindo para o aumento da eficiência energética do conversor. Por utilizar tecnologia Schottky, o componente também apresenta recuperação reversa extremamente rápida e reduzidas perdas de comutação, características particularmente importantes em aplicações chaveadas. Dessa forma, o MBR360 é uma escolha adequada para atuar como diodo de roda livre do conversor, oferecendo bom desempenho, elevada eficiência e operação confiável na frequência de chaveamento de 40 kHz.

### Análise do Capacitor de Saída: 3,9 µF >30V
O projeto foi dimensionado para uma ondulação de tensão de saída de $\Delta V_o = 10%$, resultando em uma capacitância calculada de aproximadamente **3,75 µF** para o capacitor de saída. Como esse valor não é encontrado comercialmente, foi selecionado o valor comercial imediatamente superior e mais próximo, **3,9 µF**, garantindo que a ondulação real seja igual ou inferior à especificada no projeto.

Quanto à tensão de operação, embora a tensão de saída do conversor seja de 20 V, recomenda-se a utilização de um capacitor com tensão nominal superior, de forma a proporcionar uma margem de segurança adequada. Assim, pode ser utilizado qualquer capacitor de **3,9 µF** com tensão nominal de **30 V ou superior**, sendo valores como **35 V** ou **50 V** igualmente adequados.
