# Comandos SysEx

Todos os comandos estão em hexadecimal, no formato aceito pelo FootCtrlPlus (com `F0`, `F7` e o checksum já calculado). O formato e o cálculo do checksum estão no [README](README.md#como-os-comandos-são-enviados).

- [Presets](#presets)
- [Efeitos](#efeitos)

## Presets

A troca de preset exige duas mensagens, **enviadas nesta ordem** no mesmo botão:

1. o comando de **modo de edição**;
2. o comando do **preset** desejado.

O modo de edição permanece ativo enquanto o amplificador estiver ligado e conectado, então basta enviá-lo uma vez por sessão, mas incluí-lo em todo botão de preset é a forma mais simples e segura.

### Modo de edição

```
F0 41 10 01 05 07 12 7F 00 00 01 01 7F F7
```

### Panel

```
F0 41 10 01 05 07 12 7F 00 01 00 00 00 00 F7
```

### A: CH-1

```
F0 41 10 01 05 07 12 7F 00 01 00 00 01 7F F7
```

### A: CH-2

```
F0 41 10 01 05 07 12 7F 00 01 00 00 02 7E F7
```

### B: CH-1

```
F0 41 10 01 05 07 12 7F 00 01 00 00 05 7B F7
```

### B: CH-2

```
F0 41 10 01 05 07 12 7F 00 01 00 00 06 7A F7
```

> O Katana Gen 3 50 W tem só esses 5 presets. Valores fora dessa lista (3, 4, 7 e 8) são rejeitados pelo amplificador e ele volta para o Panel.

## Efeitos

Os comandos de efeito **não** exigem o modo de edição: cada um é uma única mensagem. Eles atuam no **preset selecionado no momento**, e cada preset guarda o seu próprio estado de efeitos.

Para um botão de toggle, use o modo "Single step (switch between two Banks)" do FootCtrlPlus: cadastre o comando de **ligar** no banco A e o de **desligar** no banco B.

> O pedal não lê o estado do amplificador. Se o efeito for alterado pelo painel ou se o preset mudar, o toggle do pedal pode ficar fora de sincronia e o primeiro toque seguinte não terá efeito visível.

### Booster

Ligar:

```
F0 41 10 01 05 07 12 20 00 08 00 01 57 F7
```

Desligar:

```
F0 41 10 01 05 07 12 20 00 08 00 00 58 F7
```

### Mod

Ligar:

```
F0 41 10 01 05 07 12 20 00 08 01 01 56 F7
```

Desligar:

```
F0 41 10 01 05 07 12 20 00 08 01 00 57 F7
```

### FX

Ligar:

```
F0 41 10 01 05 07 12 20 00 08 02 01 55 F7
```

Desligar:

```
F0 41 10 01 05 07 12 20 00 08 02 00 56 F7
```

### Delay 1

Ligar:

```
F0 41 10 01 05 07 12 20 00 08 03 01 54 F7
```

Desligar:

```
F0 41 10 01 05 07 12 20 00 08 03 00 55 F7
```

### Delay 2

Ligar:

```
F0 41 10 01 05 07 12 20 00 08 04 01 53 F7
```

Desligar:

```
F0 41 10 01 05 07 12 20 00 08 04 00 54 F7
```

### Reverb

Ligar:

```
F0 41 10 01 05 07 12 20 00 08 05 01 52 F7
```

Desligar:

```
F0 41 10 01 05 07 12 20 00 08 05 00 53 F7
```

### EQ

Ligar:

```
F0 41 10 01 05 07 12 20 00 4C 01 01 12 F7
```

Desligar:

```
F0 41 10 01 05 07 12 20 00 4C 01 00 13 F7
```

### EQ 2

Ligar:

```
F0 41 10 01 05 07 12 20 00 4E 01 01 10 F7
```

Desligar:

```
F0 41 10 01 05 07 12 20 00 4E 01 00 11 F7
```
