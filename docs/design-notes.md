# Design notes (high level)

These notes describe **how** the hardware was engineered, without the design data itself.

## 1. Architecture
- One Wi-Fi/BLE microcontroller module at the centre; sensors on dedicated buses (I²C, SPI, UART) so they don't compete for bandwidth.
- Sensors chosen for **sports-grade motion tracking**: position/speed from satellites, high-rate motion data from an inertial sensor, and a low-noise physiological channel.
- Local logging during a session, with wireless upload only when the user asks for it (saves battery).

## 2. PCB stack-up and layout
- **4 layers**: signal + component layers outside, a **solid ground plane** and a **power layer** inside.
- The ground layer is kept **unbroken under the RF path** to give the antenna feed a clean reference.
- The microcontroller's on-module antenna sits at a **board corner with a no-copper keep-out on every layer**.
- Switching power circuitry sits **far from the RF and analog sections**, with a short, tight loop for the inductor and capacitors (vendor-recommended layout).
- Components on both sides: tall/user-facing parts on top, most passives and power parts on the bottom.

## 3. RF
- 50 Ω coplanar waveguide (track width and gap calculated for the manufacturer's standard stack-up).
- Ground-via fence along the feed, a small series link to choose between an on-board antenna and an external connector, and router keep-outs so no other trace runs near the feed.

## 4. Power
- USB-C input with ESD protection, Li-ion charger, and an independent battery-protection stage.
- Load-sharing so the device runs from USB while the battery charges.
- Buck-boost regulator gives a stable 3.3 V across the whole battery range.
- Soft power latch: one button turns the device on, then firmware decides when to switch off (it also detects long presses).

## 5. Verification process
1. **Datasheet review** of every IC: reference circuits, recommended component values, boot/strapping pins, unused-pin rules. This caught and fixed **four errors** before layout (a wrong capacitor value, two resistor values, a wrong connector type, and a pin that could not wake the MCU from deep sleep).
2. **Electrical rules check** (ERC) plus scripted **netlist checks** on about 50 critical pin connections.
3. **Footprint ↔ symbol pad check** for every part.
4. **Design rules check** (DRC) with schematic parity: final result **0 errors, 0 unconnected, 0 mismatches**.

## 6. Manufacturing and cost
- Complete fabrication and assembly outputs generated from the design (fab-ready Gerbers, drill, pick-and-place, BOM with manufacturer part numbers, assembly drawings, 3D model).
- Quotes benchmarked across Indian and international manufacturers.
- Unit-economics model across 10 → 5,000 units: the next revision targets about **50% lower BOM cost** by re-selecting sensors and storage, while keeping or improving performance.

---
© 2026 Cinctech. All rights reserved. Portfolio viewing only.
