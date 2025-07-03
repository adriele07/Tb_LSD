
# Máquina de Lavar em VHDL

## Resumo Executivo

Este projeto simula uma máquina de lavar automática digital, utilizando VHDL e arquitetura modular. O objetivo é demonstrar conceitos avançados de sistemas digitais, controle por FSM, integração de sensores e atuadores, e boas práticas de engenharia para sistemas embarcados. O projeto foi pensado para ser didático, robusto e próximo de aplicações industriais reais.

## Descrição Geral

Este projeto implementa uma máquina de lavar automática utilizando VHDL, composta por diversos módulos que simulam as principais funções de um ciclo de lavagem real. O sistema é controlado por uma máquina de estados finitos (FSM) e pode ser sintetizado em FPGA.

## Arquitetura do Sistema

O sistema é dividido em módulos independentes, cada um responsável por uma função específica. A comunicação entre eles é feita por sinais digitais, promovendo reuso e clareza. A arquitetura segue o padrão de sistemas embarcados industriais, com separação clara entre controle, atuação e interface.

**Diagrama de Blocos (Descrição):**

- **FSM (Controle Central):** Orquestra o ciclo de lavagem, ativando/desativando módulos conforme o estado.
- **Sensores:** Fornecem informações de nível de água e presença de roupas.
- **Atuadores:** Motor, válvula de água, drenagem.
- **Temporizador:** Controla o tempo de cada etapa.
- **Display:** Informa o estado atual ao usuário.

O fluxo de dados é predominantemente do usuário/sensores para a FSM, e da FSM para os atuadores e display.

- **MaquinaDeLavar.vhd**: Módulo principal/topo, integra todos os outros módulos e implementa a FSM.
- **AdicaoDeAgua.vhd**: Controla o enchimento de água.
- **DrenaAgua.vhd**: Controla a drenagem da água.
- **Motor.vhd**: Controla o motor de agitação/centrifugação.
- **Temporizador.vhd**: Controla o tempo de cada etapa.
- **NivelRoupas.vhd**: Detecta o nível de roupas/água.
- **Display.vhd**: Mostra o estado atual no display de 7 segmentos.

## Funcionamento dos Módulos

## Justificativa de Projeto

- **FSM (Finite State Machine):** Permite controle sequencial, seguro e previsível do ciclo de lavagem, facilitando depuração e expansão.
- **Modularização:** Cada função (enchimento, drenagem, motor, etc) é isolada, facilitando manutenção e testes.
- **Sensores Digitais:** Simplicidade e confiabilidade para prototipagem e simulação.
- **Display de 7 segmentos:** Interface amigável e de fácil implementação em FPGA.

### MaquinaDeLavar.vhd
- Implementa uma FSM com 8 estados: `inicial`, `enchimento1`, `lavagem`, `drenagem`, `enchimento2`, `enxague`, `drenagem2`, `centrifugacao`.
- Cada estado ativa/desativa módulos conforme necessário.
- Transições de estado ocorrem por botões, sensores ou fim de temporização.

### AdicaoDeAgua.vhd
- Controla o enchimento de água com base no nível de roupas e sensor de água.
- Sinaliza quando o enchimento terminou.

### DrenaAgua.vhd
- Ativa a drenagem se solicitado e o nível de água não está baixo.
- Sinaliza quando a drenagem terminou.

### Motor.vhd
- Liga o motor quando solicitado e controla o tempo de operação via temporizador.
- Sinaliza fim da operação.

### Temporizador.vhd
- Conta ciclos de clock quando habilitado.
- Sinaliza fim do tempo para transição de estado.

### NivelRoupas.vhd
- Interpreta o sensor de distância para determinar se há roupas/nivel suficiente.

### Display.vhd
- Mostra o estado atual no display de 7 segmentos.

## Máquina de Estados (FSM)

## Fluxo de Dados e Controle

O controle do sistema é centralizado na FSM, que recebe sinais dos sensores e botões, decide o próximo estado e ativa os módulos necessários. O temporizador atua como "relógio" das etapas, e os sinais de fim de enchimento/drenagem/tempo são usados para garantir que cada etapa só termina quando realmente concluída.

**Exemplo de fluxo:**
1. Usuário pressiona `prox` → FSM vai para enchimento.
2. Sensor de água indica nível suficiente → FSM ativa lavagem.
3. Temporizador termina → FSM ativa drenagem, e assim por diante.

- FSM com 8 estados principais.
- Transições ocorrem por eventos: botão `prox`, sensores, fim de temporização.
- Cada estado define quais módulos estão ativos e quais sinais são enviados.

## Sinais de Controle e Intertravamento

- Sinais como `ativarAdicaoDeAgua`, `ativarDrenagemDeAgua`, `ativarTemporizador` garantem que apenas um módulo crítico está ativo por vez.
- Não é possível drenar e encher ao mesmo tempo.
- O motor só liga quando apropriado.

## Temporização

- Temporizador simples, parametrizável, usado para definir o tempo de lavagem, enxágue, centrifugação, etc.

## Testbenches

## Testabilidade e Validação

O sistema foi projetado para ser facilmente testável em simulação (testbenches) e em hardware. Cada módulo pode ser testado isoladamente. O testbench principal simula um ciclo completo, validando transições de estado, resposta a sensores e robustez do controle.

**Validação:**
- Testes de todos os estados e transições.
- Testes de condições de erro (ex: sensor falho, botão pressionado fora de hora).
- Verificação visual pelo display e sinais de saída.

- `tb_MaquinaDeLavar.vhd` simula o funcionamento do sistema, aplicando estímulos nos sinais de entrada e observando as saídas.
- Testa transições de estado, resposta a sensores e botões.

## Interface com o Usuário

- Entradas: sensores (`sensorDeDistancia_1`, `sensorDeAgua`), botões (`prox`, `centrifugar`).
- Saídas: LEDs de status, display de 7 segmentos, sinais de controle para atuadores.

## Considerações de Hardware

- Projeto para FPGA (há arquivos `.qsf` de restrição de pinos).
- Pode ser sintetizado e testado em hardware real.

## Robustez e Segurança

## Reflexão Crítica e Possíveis Melhorias

**Desafios enfrentados:**
- Garantir sincronização entre módulos.
- Definir tempos realistas para simulação.
- Modularizar sem perder desempenho.

**Possíveis melhorias:**
- Adicionar sensores analógicos para níveis intermediários de água.
- Implementar interface serial para configuração de ciclos.
- Adicionar proteção contra falhas de energia (memória não-volátil).
- Permitir múltiplos ciclos de enxágue ou modos customizados.

**Comparação com sistemas reais:**
O modelo abstrai detalhes mecânicos e hidráulicos, mas segue o fluxo lógico de máquinas reais. Em sistemas industriais, haveria mais sensores, redundância e protocolos de segurança, mas a lógica central seria similar.

**Lições aprendidas:**
- Importância da FSM para controle seguro.
- Valor da modularização para manutenção e testes.
- Como simular sistemas embarcados complexos em ambiente digital.

- O sistema só avança de estado se as condições corretas forem atendidas.
- Reset inicial garante partida segura.
- Não há lógica explícita para falha de energia, mas o reset retorna ao estado inicial.

## Perguntas Frequentes e Respostas

- **Como garantir que o motor não liga sem água?**
  - O motor só é ativado nos estados corretos, após o enchimento ser sinalizado pelo sensor.
- **Como evitar transbordamento?**
  - O enchimento só ocorre até o sensor de nível indicar que está cheio.
- **Como garantir que o ciclo só termina após todas as etapas?**
  - A FSM só avança para o próximo estado após o término de cada etapa (temporizador ou sensor).
- **Como modificar para enxágue duplo?**
  - Adicione um novo estado entre `enxague` e `drenagem2`, duplicando a lógica de enxágue.

## Exemplos de Código Importantes

### Máquina de Estados
```vhdl
case estadoAtual is
    when inicial =>
        -- sinais de saída e ativação de módulos
        if( prox = '1' and proxAnterior = '0' ) then
            estadoProximo <= enchimento1;
        else
            estadoProximo <= inicial;
        end if;
    -- ... demais estados ...
end case;
```

### Temporizador
```vhdl
process( clock, reset )
    variable count : natural := 0;
    constant max : natural := 10;
begin
    if reset = '1' then
        count := 0;
        fim <= '0';
    elsif rising_edge( clock ) then
        if ( count < max ) then
            count := count + 1;
            fim <= '0';
        else
            fim <= '1';
        end if;
    end if;
end process;
```

### Testbench
```vhdl
process is
begin
    wait for offset;
    sensorDeDistancia_1 <= '1';
    wait for clockPeriod;
    prox <= '1';
    wait for clockPeriod;
    sensorDeAgua <= '0';
    -- ... simula o ciclo completo ...
end process;
```

---

Se desejar fluxogramas, diagramas ou explicações ainda mais detalhadas, basta pedir!
