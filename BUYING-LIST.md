# Buying list — MitraStar UART

Updated **2026-10-26 (America/Sao_Paulo)**. Purpose: supply the missing connectors for a future 3.3 V UART bridge without turning this into another expensive tool purchase.

## Buy one set: female–female Dupont jumper wires

- [ ] **One set of female–female Dupont jumpers**, 2.54 mm pitch, 10–20 cm, preferably **individual 1-pin housings at both ends** so the leads can be placed independently. Two leads suffice for initial receive-only capture (GND and target TX to receiver RX); a third is used only later for verified bidirectional UART. Four or more spare leads are useful. Do not buy male–male by mistake.

### Preferred listing — checked at publication

[Eletrogate: Jumpers Fêmea/Fêmea — 20 unidades de 20 cm](https://www.eletrogate.com/jumpers-femea-femea-20-unidades-de-20-cm)

- SKU: `F4-CP-001`.
- Displayed price: **R$4.90**, or **R$4.66 via Pix**.
- Product page displayed **Estoque: Disponível**.
- Freight is extra; confirm final price, connector arrangement and stock at checkout. These are a dated listing snapshot, not a delivered-price quote or an order.

### Alternative — buy this OR the preferred set, not both

[Eletrogate: Jumpers Fêmea/Fêmea — 40 unidades de 20 cm](https://www.eletrogate.com/jumpers-femea-femea-40-unidades-de-20-cm)

- SKU: `F4-CP-002`.
- Displayed price: **R$8.90**, or **R$8.46 via Pix**, in stock when checked.
- Same freight/checkout qualifications apply.

### Correction to the earlier R$3.90 suggestion

The previously suggested [4-way female–female cable](https://www.eletrogate.com/cabo-dupont-femeafemea-4-vias) now displays **unavailable**. Do not carry forward the earlier price/stock claim as current. A fixed multi-position connector is also less convenient when GND and RX are not adjacent or a VCC position must remain disconnected. The individual-lead set above is the intended purchase.

## What this enables — and what it does not

These leads connect compatible male headers on the router and the expected ESP32 development board without soldering. Verify the actual received board and header spacing. They are wires, **not a voltage converter**: they do not make the approximately 5 V CH341A signals safe for the router.

For UART, leave the router VCC pin and the adapter power pins unconnected to one another. Begin with GND + receive-only signal only after receiver and target electrical compatibility are established. No ribbon-cable pin-order assumptions.

The incoming ESP32 requires suitable bridge firmware; it has not arrived or been tested. This purchase does not guarantee an unlocked shell. See [the hardware session](docs/experiments/mitra-hardware-020.md).

## Already owned / already ordered

| Item | Status | Buy again? |
| --- | --- | --- |
| CH341A programmer and supplied adapters | Received; USB works, target logic voltage unresolved | No |
| SOIC8/SOP8 clip and ribbon | Received; physically obstructed on the MitraStar | No |
| Multimeter | Owned and used | No |
| ESP32 development board | Owner says ordered, not arrived | No |
| Dupont female–female leads | None currently owned | **Yes — only purchase requested now** |

## Deferred — not purchases authorized by this list

A soldering iron/station, fine wire/flux, micro-grabbers, alternate clips or a new programmer are **not** part of the immediate buying task. Revisit only when choosing a specific repair or contact strategy. Do not recommend them as prerequisites to the separate Ethernet-first [Askey investigation](https://github.com/zacmero/mero-askey).

## Todoist

Created in **General Tasks**, without an invented deadline or reminder:

- Task: **Buy female–female Dupont jumpers for MitraStar UART**.
- Task ID: `6hr5x5pvx8hghwMp`.
- Description contains both listings, the checked prices, the exact connector requirements and the no-VCC/no-5-V warning.

The task records a purchase to make; no order has been placed.
