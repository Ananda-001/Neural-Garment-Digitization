# Garment Digitization via Constrained Non Rigid Registration

This repository contains a PyTorch3D based pipeline for recovering clean, animatable garment topology from noisy 3D Gaussian Splatting (SuGaR) extractions. 

## The Problem
Extracting meshes from 3D Gaussian Splatting often results in non-manifold geometry, "polygon soup," and severe noise. Standard non-rigid registration techniques (like raw Chamfer matching) often fail when wrapping a canonical template to these noisy scans, resulting in severe volume collapse or topology tearing.

## The Pipeline
This project bridges the gap between raw scan data and downstream simulation engines through a multi stage registration process:

1. **Rigid Docking:** Utilizes Iterative Closest Point (ICP) to automatically correct global scale and orientation mismatches between the generic template and the scan.
2. **Volume-Preserving Optimization:** Implements a custom "Soft-Shell" warp. By heavily penalizing deviations from the template's resting edge lengths (`mesh_edge_loss`), the optimization fits the mesh to the scan using `chamfer_distance` while mathematically forbidding the geometry from imploding into a 2D plane. 
3. **Automated LBS Rigging:** Binds the optimized garment to an SMPL skeleton. Implements a "Gravity Pose" nearest-neighbor binding strategy to prevent severe artifacting in the armpits during high-angle articulation.
4. **Photometric Transfer:** High-frequency RGB data from the original noisy scan is baked onto the new canonical UV space.

## Visual Results

**Photometric Transfer (The final baked asset)**
![Final Texture](images/01_final_texture_transfer.jpg)

**Volume Preservation (Clean Canonical Topology)**
![Clean Geometry](images/02_clean_geometry.jpg)

**Addressing Volume Collapse**
Standard fitting techniques often result in severe geometric collapse. The edge-length constraints implemented in this pipeline resolve this issue, maintaining the physical volume of the garment.
![Volume Collapse Error](images/04_volume_collapse_error.jpg)

## Tech Stack
* Python, PyTorch, PyTorch3D, Trimesh, NumPy, Blender (for texture baking/UV projection).
