# NVSim (Circuit-Level Simulator) Domain Knowledge
[cite_start]**Context**: We are simulating 22nm LOP FinFET ReRAM devices[cite: 175, 207]. 

## 🛠️ APPLIED REPAIRS (DO NOT REVERT OR FLAG AS BUGS)
1. [cite_start]**Namespace Collision**: `Makefile` enforces `-std=c++11` to prevent `MemoryType::data` vs `std::data` collisions[cite: 36, 179].
2. **MLC / CAM Bypassing (corrected 2026-09-18)**: only `main.cpp` (`applyConstraint`, lines ~473-516, commit `df92f02`) is patched: a CAM design target is redirected to RAM instead of `exit(-1)`, and the MLC NAND `exit(-1)` is commented out. `InputParameter.cpp` was NOT patched (an earlier version of this note said it was; `git log -- InputParameter.cpp` shows only the initial import). The only other local changes are the `-std=c++11` Makefile flag (item 1) and debug prints in `Mat.cpp` (lines 187-190 and 253-257) that can emit millions of lines per run; filter them (`grep -v '>>> \['`). Verified 2026-09-15 against upstream SEAL-UCSB/NVSim @6334d00: numerically identical output on every sample and on our configs, so there is no software drift (`documents/MBMM_Book_Typst/research_notes/leakage_47x_organization_artifact.md` §14).
3. **RAM Identity**: Configurations must use `-DesignTarget: RAM` and `-IsNand: false`. [cite_start]Do not attempt to use CAM or NAND logic[cite: 21].

## 📐 GEOMETRIC SKELETON
[cite_start]If encountering Floating Point Exceptions (FPE Exit Code -8) at 22nm, it is due to zero-dimension sensing widths[cite: 22, 203]. [cite_start]Always default to a Fixed Geometry "Stability Skeleton" (e.g., 128x128 Mats) rather than "Automatic Exploration"[cite: 203, 211].

**Correction (2026-09-18)**: the skeleton keys written into the cfgs (`-MatHeight`, `-MatWidth`, `-NumRowMat`, `-NumColumnMat`, `-Max/MinNumRowMat`, `-Max/MinNumColumnMat`) are NOT parsed by `InputParameter.cpp`, so the "128x128 Mats" skeleton never took effect and the organization was chosen by NVSim's own exploration. The only organization keys NVSim reads are `-ForceBank (Total AxB, Active CxD)`, `-ForceMat (Total AxB, Active CxD)`, `-ForceMuxSenseAmp`, `-ForceMuxOutputLev1` and `-ForceMuxOutputLev2`, which must come after `-UseCactiAssumption` and the file must end with a newline (a missing final newline silently drops the last key). The 2026-09 revision forces 2048x2048 subarrays at mux 64 this way (T2.6).
