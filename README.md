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

# workflow

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

## Metric
```
Contacts <4.5 Å
  Native 3BIK: 31
  Best Design: 27
Contacts <3.5 Å
  Native 3BIK: 21
  Best Design: 18
Hydrogen bonds
  Native 3BIK: 18
  Best Design: 6
Buried surface area (Å²)
  Native 3BIK: 920
  Best Design: 873
Salt bridges
 Native 3BIK: 3
Best Design: 5
```
The comparison revealed several interesting differences.
```
The designed interface had a similar number of contacts and a similar buried surface area to the native interface:
27 vs. 31 contacts within 4.5 Å
873 vs. 920 Å² buried surface area
```
This suggests that the designed binder was capable of forming an interface of comparable overall size
```
However, the designed interface contained substantially fewer hydrogen bonds:
Native: 18
Designed: 6
At the same time, the designed complex contained more salt bridges:
Native: 3
Designed: 5
```
One possible interpretation is that the designed binder adopts a different binding orientation or interaction network from the native PD-1 interface. The additional salt bridges may provide alternative electrostatic interactions, while the reduced hydrogen-bond network may indicate less optimal geometric complementarity.
These observations are consistent with the moderate interface confidence indicated by the iPAE of 15.3 Å.
Importantly, these structural metrics should be interpreted as computational evidence rather than proof of experimental binding.

# Key Findings
1. RFdiffusion generated compact de novo binder backbones against PD-L1.
2. ProteinMPNN generated candidate sequences for the designed backbones.
3. AlphaFold provided a means to evaluate the structural stability and predicted interfaces of the generated designs.
4. The best design engaged multiple intended PD-L1 hotspot residues, including A56, A113, and A123.
5. The designed interface had a buried surface area and contact count broadly comparable to the native PD-1–PD-L1 interface.
6. The designed interface contained substantially fewer hydrogen bonds than the native complex and a different salt-bridge pattern.
7. The best design achieved an iPAE of 15.3 Å, indicating that the interface remained less confidently predicted than desired.
8. The results demonstrate both the potential and limitations of small-scale de novo binder design with limited computational sampling.

# Limitations and Next Steps
The main limitation of this study was sampling. Only a relatively small number of RFdiffusion backbones and ProteinMPNN sequences could be evaluated because the workflow was run using free Google Colab resources.
```
A larger design campaign would provide a broader exploration of backbone and sequence space.
Potential next steps include:
generating additional independent RFdiffusion batches;
increasing sequence diversity for promising backbones;
clustering and ranking candidate structures;
analyzing interface hydrogen bonds and electrostatic interactions in greater detail;
comparing alternative binder lengths;
evaluating interface shape complementarity;
(and experimentally validating selected candidates.)
```
# Conclusion
This project demonstrates an end-to-end computational workflow for structure-guided de novo protein binder design, from target and hotspot selection through backbone generation, sequence design, structure prediction, and interface analysis.
Rather than treating a single computational score as evidence of success, the project emphasizes structural interpretation of the generated designs, including comparison with the native PD-1–PD-L1 interface and identification of factors that may limit interface quality.
The current results provide a starting point for further computational optimization and demonstrate the practical use of modern generative protein design tools for protein engineering.

