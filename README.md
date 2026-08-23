# Protein_Design_1
-practice in protein design 
-Protein Binder Design Targeting PD-L1

# Project Overview
This project explores computational de novo protein binder design targeting PD-L1 using a workflow based on Sergey Ovchinnikov's RFdiffusion Colab notebook, which conducts ProteinMPNN for sequence design and AlphaFold structure prediction for validation.
The goal was to gain practical experience with a generative protein design workflow and investigate whether computationally designed binders could engage the experimentally characterized PD-1 binding interface of PD-L1.
The diffusion Colab notebook and underlying workflow were developed by Sergey Ovchinnikov and collaborators(github/sokrypton/ColabDesign/blob/main/rf/examples/diffusion.ipynb) ; this project uses that existing workflow rather than presenting the implementation as my own.

# My work focused on:
selecting and preparing the PD-L1 target;
identifying and selecting interface hotspot residues;
analyzing generated structures;
comparing the best predicted design with the native PD-1–PD-L1 interface;
interpreting interface contacts, hydrogen bonds, salt bridges, buried surface area, and interface PAE.

# Workflow

```
3BIK PD-L1 structure
      ↓
Interface analysis with ChimeraX
      ↓
Hotspot selection
      ↓
| RFdiffusion                 |
|      ↓                      |
| De novo binder backbone     |
|      ↓                      |
| ProteinMPNN                 | Colab notebook by Sergey Ovchinnikov and collaborator
|      ↓                      |
| Binder sequence design      |
|      ↓                      |
| AlphaFold                   |
      ↓
Predicted binder-PD-L1 complex validation
      ↓
Structural/interface analysis
```

# Tools
```
RFdiffusion — backbone generation using Sergey Ovchinnikov's Colab workflow
ProteinMPNN — sequence design using Sergey Ovchinnikov's Colab workflow
AlphaFold — structure/interface prediction using Sergey Ovchinnikov's Colab workflow
ChimeraX — structural visualization and interface analysis
Python — analysis and filtering of generated designs
```

# My Contribution
I used an established RFdiffusion Colab workflow developed by Sergey Ovchinnikov and collaborators and focused on applying the workflow to a PD-L1 binder-design problem. I selected interface hotspots based on the experimental 3BIK structure, configured the computational workflow, evaluated generated designs, and performed structural analysis to understand how the best candidate differed from the native PD-1–PD-L1 interface.

# Design Strategy

## Target
PD-L1 was selected as the target because its interaction with PD-1 provides a structurally characterized protein-protein interface that can be used to guide binder design.
The 3BIK structure was used as the structural reference. Interface residues were examined using ChimeraX, and residues within approximately 4.5 Å of the interacting partner were considered when selecting candidate hotspot residues.

## Hotspot Selection
```
An initial hotspot strategy focused on the F-strand region:
A121, A122, A123, A124, A125
The design was subsequently refined to distribute hotspots across several regions of the PD-L1 interaction surface:
A56, A63, A113, A123, A124
```
This was intended to provide RFdiffusion with multiple spatial constraints across the target interface rather than concentrating all hotspots on a single structural element.


## Binder Design
```
The binder was designed as a 70-residue de novo protein.
The main RFdiffusion parameters used in the study included:
Target: PD-L1 (3BIK)
Binder length: 70 residues
RFdiffusion iterations: 50
Hotspots: A56, A63, A113, A123, A124
```
Because the project was run on free Google Colab resources, the number of generated designs was kept relatively small.

## Sequence Design and Structure Prediction
```
For each generated backbone, ProteinMPNN was used to generate candidate binder sequences.
Representative settings included:
ProteinMPNN sampling temperature: 0.1
Sequences per backbone: 2
Initial guess: enabled
AlphaFold recycles: 3
```
The resulting sequences were subsequently evaluated using AlphaFold to assess whether the designed sequences could adopt the intended structures and interfaces.

## Computational Evaluation
```
Designs were evaluated using several structural metrics:
RMSD — structural similarity between the generated backbone and the predicted structure
pLDDT — predicted structural confidence
Interface PAE (iPAE) — confidence in the predicted protein-protein interface
Interface contacts — residues within defined distance cutoffs
Hydrogen bonds
Buried surface area
Salt bridges

An initial stringent screening criterion was:
(results["rmsd"] < 2.0) &
(results["plddt"] > 0.8) &
(results["i_pae"] < 10.0)
```
However, because only a small number of designs could be generated on free Colab, these thresholds were treated as screening criteria rather than evidence that a design was experimentally validated.

# Best Design
```
The best computationally ranked design achieved an interface PAE of approximately:
iPAE = 15.3 Å
```
Although this did not meet the initial stringent iPAE < 10 Å criterion, structural inspection showed that the binder interacted with several of the intended PD-L1 hotspot regions.
```
Using the original 3BIK residue numbering, the best design was found to contact hotspot residues including:
A56
A113
A123
within approximately 4.5 Å.
This was important because it indicated that the design was engaging the intended PD-L1 interface rather than simply binding an unrelated surface.
```
# Native vs. Designed Interface Analysis
The best design was compared with the native PD-1–PD-L1 interface in 3BIK.
```
| Metric                   | Native 3BIK | Best Design |
| ------------------------ | ----------: | ----------: |
| Contacts <4.5 Å          |          31 |          27 |
| Contacts <3.5 Å          |          21 |          18 |
| Hydrogen bonds           |          18 |           6 |
| Buried surface area (Å²) |         920 |         873 |
| Salt bridges             |           3 |           5 |
```
## Structural Interpretation

Structural comparison revealed an important difference between the native and designed interfaces.
The native PD-1 structure in 3BIK presents a predominantly β-sheet architecture at the PD-L1 binding interface, whereas the designed binder generated in this study adopts a predominantly α-helical architecture.

This secondary-structure difference changes how the binder approaches and interacts with the PD-L1 surface. Although the designed binder engages several of the selected hotspot residues, its interaction network differs from the native PD-1–PD-L1 complex.

The interface analysis supports this observation:

The best design was compared with the native PD-1–PD-L1 interface in 3BIK.

The designed binder therefore achieves a similar overall interface size, as indicated by contact counts and buried surface area, but forms a substantially different interaction network. In particular, the lower number of hydrogen bonds and increased number of salt bridges suggest that the designed binder does not reproduce the native interaction geometry.

The difference in secondary-structure architecture is a potential explanation for this behavior. A predominantly α-helical binder may interact with the PD-L1 surface differently from the β-sheet architecture used by native PD-1.

The best design achieved an interface PAE (iPAE) of 15.3 Å. Rather than interpreting this value alone, the structural comparison suggests that the moderate interface confidence may be related to differences in interface geometry and secondary-structure architecture.

## Lessons Learned
One of the main lessons from this design iteration was that targeting the correct hotspot residues does not necessarily reproduce the native binding mode.
The best design contacted several intended PD-L1 hotspot residues, including A56, A113, and A123, but its α-helical architecture produced an interaction geometry different from the native β-sheet interface.
This highlighted the importance of evaluating not only computational confidence metrics but also:
```
secondary-structure architecture;
interface geometry;
residue-level contacts;
hydrogen-bond networks;
salt bridges; and
buried surface area.
```
# Limitations and Next Steps
The main limitation of this study was sampling. Only a relatively small number of RFdiffusion backbones and ProteinMPNN sequences could be evaluated because the workflow was run using free Google Colab resources.

A future design iteration will investigate secondary-structure conditioning in the RFdiffusion configuration, with the goal of generating β-sheet-containing binder architectures that may better complement the native PD-L1 binding surface.

The next round will compare designs with different secondary-structure characteristics and evaluate whether a more β-sheet-like architecture improves:
```
interface geometry;
hydrogen-bonding interactions;
hotspot engagement; and
interface PAE.
```
Because the current study used a relatively small number of designs due to computational limitations, additional independent RFdiffusion sampling will also be considered.

# Skills Demonstrated
This project demonstrates practical experience with:
```
RFdiffusion
ProteinMPNN
AlphaFold
ChimeraX
protein-protein interface analysis
structural hotspot identification
de novo protein binder design
computational structural evaluation
interpretation of pLDDT, RMSD, and interface PAE
hydrogen-bond and salt-bridge analysis
buried surface area analysis
iterative computational design and troubleshooting
```
# Conclusion
This project demonstrates an end-to-end computational workflow for structure-guided de novo protein binder design, from target and hotspot selection through backbone generation, sequence design, structure prediction, and interface analysis.
Rather than treating a single computational score as evidence of success, the project emphasizes structural interpretation of the generated designs, including comparison with the native PD-1–PD-L1 interface and identification of factors that may limit interface quality.
The current results provide a starting point for further computational optimization and demonstrate the practical use of modern generative protein design tools for protein engineering.

