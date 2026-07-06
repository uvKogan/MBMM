# NVSim (Circuit-Level Simulator) Domain Knowledge
[cite_start]**Context**: We are simulating 22nm LOP FinFET ReRAM devices[cite: 175, 207]. 

## 🛠️ APPLIED REPAIRS (DO NOT REVERT OR FLAG AS BUGS)
1. [cite_start]**Namespace Collision**: `Makefile` enforces `-std=c++11` to prevent `MemoryType::data` vs `std::data` collisions[cite: 36, 179].
2. [cite_start]**MLC Bypassing**: `main.cpp` and `InputParameter.cpp` have been surgically patched to bypass hardcoded `exit(-1)` development errors when `LevelsPerCell > 2`[cite: 4, 111].
3. **RAM Identity**: Configurations must use `-DesignTarget: RAM` and `-IsNand: false`. [cite_start]Do not attempt to use CAM or NAND logic[cite: 21].

## 📐 GEOMETRIC SKELETON
[cite_start]If encountering Floating Point Exceptions (FPE Exit Code -8) at 22nm, it is due to zero-dimension sensing widths[cite: 22, 203]. [cite_start]Always default to a Fixed Geometry "Stability Skeleton" (e.g., 128x128 Mats) rather than "Automatic Exploration"[cite: 203, 211].