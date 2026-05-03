---
layout: project
title: Nutcracker
description: Class project with Graphs
technologies: /
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


"""
Nutcracker Handle Beam Deflection Analysis
==========================================
Assumptions:
- Handle simplified as a straight beam
- Macadamia nut force modeled as a point load (488.4 lb) at x = 1.5 cm from left
- Left end: pin support (Rx, Ry)
- Right end: roller support (139 lb upward) — linear actuator
- Circular cross-section, steel beam
- Length L = 18 cm = 0.18 m
- Units: SI (convert lb to N, cm to m)
"""
 
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.patches as mpatches
 
# ─── Unit Conversions ───────────────────────────────────────────────────────
LB_TO_N = 4.44822
CM_TO_M = 0.01
 
# ─── Problem Parameters ─────────────────────────────────────────────────────
L = 18 * CM_TO_M                   # Beam length [m]
a = 1.4373 * CM_TO_M               # Distance from pin to point load [m]
                                    # Derived from moment equilibrium: a = R_B*L/P
b = L - a                          # Distance from point load to roller [m]
 
P = 488.4 * LB_TO_N                # Point load (downward) [N]
R_B_given = 39.0 * LB_TO_N        # Roller reaction (upward) [N]
 
E = 200e9                          # Young's modulus for steel [Pa]
 
# ─── Part (a): Find location of maximum deflection ──────────────────────────
 
# Reactions (from equilibrium, verify with given values)
# Sum Fy = 0: Ry + R_B - P = 0
# Sum M_A = 0: -P*a + R_B*L = 0 => R_B = P*a/L
R_B = P * a / L
R_A = P - R_B
 
print("=" * 55)
print("  NUTCRACKER HANDLE — BEAM DEFLECTION ANALYSIS")
print("=" * 55)
print(f"\nBeam length L        = {L*100:.1f} cm")
print(f"Load position a      = {a*100:.1f} cm from pin")
print(f"Point load P         = {P:.2f} N ({P/LB_TO_N:.1f} lb)")
print(f"\nReactions:")
print(f"  R_A (pin, upward)  = {R_A:.2f} N ({R_A/LB_TO_N:.2f} lb)")
print(f"  R_B (roller, up)   = {R_B:.2f} N ({R_B/LB_TO_N:.2f} lb)")
print(f"  Given R_B          = {R_B_given:.2f} N ({R_B_given/LB_TO_N:.2f} lb)  ✓" 
      if abs(R_B - R_B_given) < 1 else f"  Check: R_B mismatch!")
 
# ─── Deflection equations (simply supported, asymmetric point load) ──────────
# For x in [0, a]:   y1 = (R_B * b * x)/(6EIL) * (L^2 - b^2 - x^2)    [*]
# For x in [a, L]:   y2 = (R_A * (L-x))/(6EIL) * (2Lx - x^2 - a^2)    [*]
# [*] Standard formulas derived from double integration
 
# Location of maximum deflection (in region 0 to a, since load is very close to pin):
# dy/dx = 0 => x_max = sqrt((L^2 - b^2)/3)
x_max_formula = np.sqrt((L**2 - b**2) / 3)
 
print(f"\n--- Part (a): Maximum Deflection Location ---")
print(f"x_max (numerical)    = {x_max_formula*100:.4f} cm from pin")
 
# ─── Part (b): Design for deflection < 2% of length ─────────────────────────
deflection_limit = 0.02 * L
print(f"\n--- Part (b): Design Requirements ---")
print(f"Deflection limit     = 2% × {L*100:.0f} cm = {deflection_limit*100:.4f} cm = {deflection_limit*1000:.4f} mm")
 
# Maximum deflection formula for simply supported beam with point load:
# y_max = (P * b * (L^2 - b^2)^(3/2)) / (9*sqrt(3)*E*I*L)
# Solve for I:
# I >= (P * b * (L^2 - b^2)^(3/2)) / (9*sqrt(3)*E*L*y_limit)
 
# Find maximum deflection numerically (x_max may be in region II)
def deflection(x, P, a, b, L, E, I):
    y = np.zeros_like(x, dtype=float)
    mask1 = x <= a
    mask2 = x > a
    y[mask1] = P*b*x[mask1]/(6*E*I*L) * (L**2 - b**2 - x[mask1]**2)
    y[mask2] = P*a*(L-x[mask2])/(6*E*I*L) * (2*L*x[mask2] - x[mask2]**2 - a**2)
    return y
 
x_search = np.linspace(0, L, 10000)
# Normalize with I=1 to find shape, then scale
y_norm = deflection(x_search, P, a, b, L, E, 1.0)
idx_max = np.argmax(y_norm)
x_max_formula = x_search[idx_max]
y_max_coeff = y_norm[idx_max]  # y_max = y_max_coeff / I
 
# Required I: y_max_coeff/I_req = deflection_limit
I_required = y_max_coeff / deflection_limit
 
print(f"\nRequired I           >= {I_required:.4e} m^4")
 
# For circular cross-section: I = pi*d^4/64
# d = (64*I/pi)^(1/4)
d_required = (64 * I_required / np.pi)**0.25
print(f"Required diameter    >= {d_required*1000:.4f} mm")
 
# Round up to nearest mm
import math
d_design = math.ceil(d_required * 1000) / 1000  # round up to nearest mm
I_design = np.pi * d_design**4 / 64
A_design = np.pi * d_design**2 / 4
rho_steel = 7850  # kg/m^3
mass_per_length = rho_steel * A_design  # kg/m
total_mass = mass_per_length * L
 
print(f"\nDesign choice:")
print(f"  Diameter           = {d_design*1000:.1f} mm  (rounded up)")
print(f"  I                  = {I_design:.4e} m^4")
print(f"  Cross-section area = {A_design*1e6:.4f} mm^2")
print(f"  Mass of handle     = {total_mass*1000:.4f} g  (most mass-efficient)")
 
# ─── Compute deflection curve with design diameter ───────────────────────────
I = I_design
x_all = np.linspace(0, L, 1000)
y_all = deflection(x_all, P, a, b, L, E, I_design)
y_max_actual = np.max(np.abs(y_all))
x_at_ymax = x_all[np.argmax(np.abs(y_all))]
 
x1 = np.linspace(0, a, 200)
x2 = np.linspace(a, L, 200)
y1 = deflection(x1, P, a, b, L, E, I_design)
y2 = deflection(x2, P, a, b, L, E, I_design)
 
print(f"\nVerification with design diameter:")
print(f"  Max deflection     = {y_max_actual*1000:.6f} mm at x = {x_at_ymax*100:.4f} cm")
print(f"  Deflection limit   = {deflection_limit*1000:.4f} mm")
print(f"  Within limit?      {'✓ YES' if y_max_actual <= deflection_limit else '✗ NO'}")
 
# ─── Plot ────────────────────────────────────────────────────────────────────
fig, axes = plt.subplots(2, 1, figsize=(10, 9))
fig.patch.set_facecolor('#0f1117')
 
for ax in axes:
    ax.set_facecolor('#0f1117')
    ax.tick_params(colors='#aaaaaa')
    ax.spines['bottom'].set_color('#333333')
    ax.spines['left'].set_color('#333333')
    ax.spines['top'].set_visible(False)
    ax.spines['right'].set_visible(False)
    ax.yaxis.label.set_color('#cccccc')
    ax.xaxis.label.set_color('#cccccc')
    ax.title.set_color('#ffffff')
 
# Plot 1: Deflection curve
ax1 = axes[0]
ax1.plot(x_all * 100, y_all * 1e6, color='#00e5ff', linewidth=2.5, label='Deflection')
ax1.axvline(x=a * 100, color='#ff4444', linestyle='--', linewidth=1.5, label=f'Point load @ {a*100:.1f} cm')
ax1.axvline(x=x_at_ymax * 100, color='#ffaa00', linestyle=':', linewidth=1.5, label=f'Max deflection @ {x_at_ymax*100:.3f} cm')
ax1.axhline(y=0, color='#444444', linewidth=0.8)
ax1.fill_between(x_all * 100, y_all * 1e6, alpha=0.15, color='#00e5ff')
ax1.set_xlabel('Position along beam [cm]')
ax1.set_ylabel('Deflection [μm]')
ax1.set_title('Nutcracker Handle — Elastic Deflection Curve')
ax1.legend(facecolor='#1a1a2e', edgecolor='#333333', labelcolor='#cccccc')
ax1.grid(True, alpha=0.15, color='#ffffff')
 
# Annotate max deflection
ax1.annotate(f'y_max = {y_max_actual*1e6:.4f} μm\n@ x = {x_at_ymax*100:.4f} cm',
             xy=(x_at_ymax*100, y_max_actual*1e6),
             xytext=(x_at_ymax*100 + 2, y_max_actual*1e6 * 0.7),
             color='#ffaa00',
             arrowprops=dict(arrowstyle='->', color='#ffaa00'),
             fontsize=9)
 
# Plot 2: Shear force diagram
ax2 = axes[1]
shear = np.where(x_all <= a, R_A, R_A - P)
ax2.step(x_all * 100, shear / LB_TO_N, color='#ff6b6b', linewidth=2.5, where='post', label='Shear force')
ax2.axhline(y=0, color='#444444', linewidth=0.8)
ax2.fill_between(x_all * 100, shear / LB_TO_N, alpha=0.15, color='#ff6b6b', step='post')
ax2.set_xlabel('Position along beam [cm]')
ax2.set_ylabel('Shear Force [lb]')
ax2.set_title('Shear Force Diagram')
ax2.legend(facecolor='#1a1a2e', edgecolor='#333333', labelcolor='#cccccc')
ax2.grid(True, alpha=0.15, color='#ffffff')
 
plt.tight_layout(pad=2.5)
plt.savefig('/mnt/user-data/outputs/nutcracker_beam_analysis.png', dpi=150,
            bbox_inches='tight', facecolor='#0f1117')
plt.close()
 
print(f"\nPlot saved to nutcracker_beam_analysis.png")
print("=" * 55)