# Real Time Clock (RTC)

## Resumo Geral

O RTC (Real Time Clock) é um periférico desenvolvido para o projeto **riscv-multicycle**, permitindo ao processador RISC-V acessar e controlar um relógio de tempo real através de registradores mapeados em memória (Memory Mapped I/O).

O periférico é responsável por manter a contagem de:

- Segundos
- Minutos
- Horas
- Dia
- Mês
- Ano

A comunicação entre software e hardware é realizada por meio do barramento de dados do processador, utilizando drivers desenvolvidos em C.

---

# Características
- Contagem automática do tempo.
- Conversão interna para BCD.
- Interface Memory Mapped I/O.
- Leitura e escrita dos registradores pelo processador.
- Compatível com o softcore RISC-V do projeto riscv-multicycle.
- Biblioteca em C para acesso ao periférico.

---

# Arquitetura do Hardware

O RTC foi integrado como um periférico do barramento do processador.

```
              RISC-V
                 │
                 │
          Data Bus
                 │
         I/O Data Bus Mux
                 │
               RTC
                 │
        Time Counters
```

O periférico responde aos acessos de leitura e escrita realizados pelo processador através do barramento de dados.

---

# Memory Map

| Offset | Register | Description |
|---------|----------|-------------|
| 0x00 | SEC | Seconds |
| 0x04 | MIN | Minutes |
| 0x08 | HOUR | Hours |
| 0x0C | DAY | Day |
| 0x10 | MONTH | Month |
| 0x14 | YEAR | Year |
| 0x18 | CTRL | Control Register |

Base Address:

```
RTC_BASE_ADDRESS
```

definido em:

```
hardware.h
```

---

# Registradores

## Seconds Register

Contém os segundos atuais.

```
RTC_SEC
```

---

## Minutes Register

Contém os minutos atuais.

```
RTC_MIN
```

---

## Hours Register

Contém as horas atuais.

```
RTC_HOUR
```

---

## Day Register

Contém o dia do mês.

```
RTC_DAY
```

---

## Month Register

Contém o mês.

```
RTC_MONTH
```

---

## Year Register

Contém o ano.

```
RTC_YEAR
```

---

## Control Register

Responsável por controlar o funcionamento do RTC.

Exemplo:

| Bit | Description |
|-----|-------------|
| 0 | Enable RTC |

---

# Software Driver

O driver é composto pelos arquivos:

```
rtc.h
rtc.c
```

Principais funções disponíveis:

```c
rtc_enable();

rtc_disable();

rtc_write_hour();

rtc_write_min();

rtc_write_sec();

rtc_read_hour();

rtc_read_min();

rtc_read_sec();

rtc_read_day();

rtc_read_month();

rtc_read_year();
```

---

# Exemplo

Inicialização do RTC:

```c
rtc_write_hour(15);
rtc_write_min(30);
rtc_write_sec(0);

rtc_enable();
```

Leitura:

```c
uint32_t hour;
uint32_t min;
uint32_t sec;

hour = rtc_read_hour();
min  = rtc_read_min();
sec  = rtc_read_sec();
```

---

# Integração do Hardware

Os seguintes arquivos do projeto foram modificados para integrar o RTC ao sistema.

## hardware.h

Foi adicionada a definição do endereço base do periférico.

```
RTC_BASE_ADDRESS
```

---

## rtc.vhd

Implementação do periférico RTC.

Responsável por:

- Contagem do tempo.
- Interface com o barramento.
- Registradores internos.
- Conversão para BCD.

---

## iodatabusmux.vhd

Foi adicionada a seleção do RTC no multiplexador de dados.

Quando o endereço pertence ao RTC, o barramento retorna os dados provenientes do periférico.

---

## de0_lite.vhd

Foi realizada a instanciação do periférico RTC.

O módulo foi conectado ao barramento do processador utilizando os sinais:

- daddress
- ddata_w
- ddata_r
- d_we
- d_rd
- dcsel
- dmask

---

# Operação

O fluxo de acesso ocorre da seguinte forma:

```mermaid
graph LR

APP["Aplicação C"] --> API["rtc.c"]
API --> HW["hardware.h"]
HW --> BUS["RISC-V Data Bus"]
BUS --> MUX["I/O Data Bus Mux"]
MUX --> RTC["RTC Peripheral"]
RTC --> REG["Registradores<br/>SEC MIN HOUR DAY MONTH YEAR"]

REG --> RTC
RTC --> MUX
MUX --> BUS
BUS --> API
API --> APP
```

---

# Placa de Desenvolvimento

- Intel MAX 10 (DE10-Lite)

---

# Processador

- RISC-V Softcore
- riscv-multicycle

---

# Linguagem

Hardware

- VHDL

Software

- C

---

# Autores

André Outi
Pedro Thomáz

Instituto Federal de Santa Catarina (IFSC)

Electronic Engineering
