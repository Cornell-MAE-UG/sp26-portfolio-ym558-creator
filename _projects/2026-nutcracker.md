---
layout: project
title: Nutcracker
description: Class project with Graphs
image: /assets/images/Statics HW4.jpeg

---


# Nutcracker Design Analysis

## Find

The goal is to design a simple lever-type nutcracker that can crack open a macadamia nut by hand. We need to determine the necessary dimensions of the nutcracker and develop a design that makes this task feasible.

---

## Given

- Average load required to crack a macadamia: **222 kg**  
  Source: https://doi.org/10.1007/s10071-007-0131-2

---

## Assumptions

- Average diameter of a macadamia nut: **20 mm**
- Maximum grip strength applied to handle: **40 kg**

---

## Calculations

Taking moments about point P:

      ∑Mₚ = 0  

      15j × (−222i) + yj × (40i) = 0    

      0 = -3330k̂ + 40yK̂
      
      y = 83.25



## Discussion

The total length of the nutcracker is approximately **8 cm**, which makes it discreet and portable. However, the short handle length limits the amount of force that can be comfortably applied.

---

## Beam Analysis

### Assumptions

- Handle simplified as a straight beam
- Macadamia nut force modeled as a point load at **x = 1.44 cm** from the pin
- Left end is **pinned**; right end is a **roller** (linear actuator)
- Circular cross-section, solid steel rod
- Beam self-weight neglected

---

### Given

- Beam length: **L = 18 cm**
- Point load: **P = 488.4 lb (2172.5 N)** — downward
- Roller reaction: **R_B = 39 lb (173.5 N)** — upward
- Elastic modulus: **E = 200 GPa**

---

### Reactions

Taking moments about the pin (A):

      ∑M_A = 0

      R_B · L = P · a

            R_B · L     39 × 18
      a  =  ─────── = ─────────── = 1.44 cm  from pin
               P          488.4

      R_A = P − R_B = 488.4 − 39 = 449.4 lb  (upward)

---

### Part (a) — Location of Maximum Deflection

Using double integration for a simply supported beam with asymmetric point load:

      Region I  (0 ≤ x ≤ a):   y = P·b·x/(6EIL) · (L² − b² − x²)

      Region II (a ≤ x ≤ L):   y = P·a·(L−x)/(6EIL) · (2Lx − x² − a²)

Setting dy/dx = 0 numerically:

      x_max = 7.64 cm from pin

---

### Part (b) — Cross-Section Design

Deflection limit: 2% × 18 cm = **3.6 mm**

Setting y_max = deflection limit and solving for I:

      I_required = y_max_coeff / (E · 0.02 · L) = 8.93 × 10⁻¹¹ m⁴

      d_required = (64 · I_req / π)^(1/4) = 6.53 mm

Rounding up to nearest mm:

      d_design       =  7.0 mm
      I_design       =  1.179 × 10⁻¹⁰ m⁴
      Max deflection =  2.73 mm  <  3.6 mm  ✓
      Handle mass    =  54.4 g

---

### Part (C) — Design
image: /assets/images/design.jpeg

---
### Discussion

The maximum deflection occurs at **x = 7.64 cm** from the pin — near the midpoint — even though the load is applied only 1.44 cm from the pin. A **7 mm diameter solid steel rod** satisfies the deflection constraint at only **54.4 g**,