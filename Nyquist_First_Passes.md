FORMATTING IS NOT HERE, YET!

Technical Note (≈1–2 pages)
Title
Directional Nyquist Constraints on the Detectability of Textual Traces in Digitized Manuscript Imagery
Abstract
Computer vision approaches to manuscript analysis often treat text detectability as a modeling or training-data problem. In this note, we argue that for many historically significant reuse cases—particularly low-footprint, peripheral, or wraparound text—detectability is instead governed by fundamental sampling constraints. Text induces highly anisotropic spatial frequency signatures: fine-grained, high-frequency structure orthogonal to stroke direction and coarser periodicity along line spacing. When digitization or downstream preprocessing reduces resolution below the Nyquist limit for these high-frequency components, the signal corresponding to text presence is irretrievably lost. This loss precedes and dominates any model-level considerations. We outline the implications for manuscript reuse detection, motivate full-resolution and multi-scale analysis, and justify abstention and human review as principled responses to information-theoretic limits rather than model uncertainty.
1. Introduction
In digitized manuscript collections, especially those derived from microfilm or legacy scanning workflows, textual traces of reuse frequently appear outside the focal document surface: behind stacks of pages, wrapped around spines, or embedded in bindings. These traces often occupy only a few pixels in one spatial dimension while extending meaningfully in another. Conventional computer vision pipelines—typically operating on downsampled, fixed-resolution inputs—implicitly assume that such traces remain detectable if sufficiently distinctive. This assumption is false in general.
The failure mode is not one of insufficient training data or inadequate model architecture, but of sampling theory. Once the relevant signal falls below the Nyquist frequency of the sampled image, no downstream method can recover it.
2. Directional Frequency Structure of Text
Text is not an isotropic visual phenomenon. In most scripts relevant to medieval and early modern manuscripts, text exhibits:
Horizontal direction (left–right):
Rapid alternation between ink and background at the scale of stroke width and inter-stroke gaps. This produces strong high-frequency components in the horizontal spatial frequency spectrum.
Vertical direction (top–bottom):
Slower periodic structure associated with baselines, ascenders/descenders, and inter-line spacing, yielding lower-frequency components.
The detectability of text presence depends critically on the preservation of the high-frequency horizontal components. These components are often far finer (in pixel units) than those required for identifying page layout or parchment texture.
3. Nyquist Limits and Irreversible Information Loss
Let a digitized image be downsampled—either at acquisition or during preprocessing—such that the sampling frequency is less than twice the dominant horizontal stroke frequency. Under standard anti-aliasing, these components are removed; under naive downsampling, they are aliased into unrelated lower frequencies. In either case, the original ink–blank–ink signal no longer exists in the sampled image.
Crucially, this is not a matter of signal degradation but of signal annihilation. Below the Nyquist limit, text presence is not merely difficult to infer—it is mathematically undecidable from the available data. This explains empirical observations in which humans can read text at full resolution but both humans and models fail entirely after modest downsampling.
4. Implications for Reuse Detection
This analysis has several immediate consequences:
Whole-image downsampling is insufficient for detecting low-footprint reuse.
Multi-scale or tiled full-resolution analysis is not an optimization but a necessity.
Model abstention is principled when sampling limits are exceeded; it reflects information-theoretic absence, not classifier uncertainty.
Human review remains essential in edge cases, as humans can access full-resolution signals unavailable to models operating on reduced inputs.
These conclusions apply equally to CNN-based, transformer-based, and hybrid architectures.
5. Relation to Prior Work and Future Directions
While OCR and scene-text literature discuss minimum readable resolutions, they rarely frame failure in terms of directional Nyquist constraints or distinguish detectability from recognizability. Explicitly incorporating sampling theory into manuscript analysis clarifies why certain reuse classes are systematically under-detected and provides a rigorous foundation for dataset design and evaluation.
Future work may explore frequency-aware acquisition standards, super-resolution approaches near the Nyquist boundary, and probabilistic models for text presence under partial frequency loss. These directions, however, do not mitigate the fundamental limit described here.
6. Conclusion
Text detection failures in manuscript imagery are often unavoidable consequences of sampling decisions rather than shortcomings of modern vision models. Recognizing and formalizing this distinction strengthens both methodological rigor and collaboration between computational and manuscript studies communities.


---

Short Version for IP_Plus_Vision (2 paragraphs)
Nyquist Limits and Why Resolution Matters
Many of the most valuable manuscript reuse traces—tiny wraparound text, faint offsets, partial strokes hidden behind page stacks—exist at spatial scales that are fundamentally incompatible with routine image downsampling. Text induces highly directional frequency patterns: extremely fine ink–blank alternation across strokes and coarser structure along line spacing. If digitization or preprocessing reduces resolution below the Nyquist limit for these high-frequency components, the signal corresponding to text presence is irreversibly lost. This is not a modeling failure; it is an information-theoretic boundary.
Implications for RMFB
This explains why whole-image reasoning at fixed resolutions systematically fails for low-footprint and peripheral reuse, and why full-resolution tiling, multi-scale models, abstention, and expert review are essential rather than optional. RMFB explicitly embraces this reality: when information has been destroyed by sampling, no amount of model sophistication can recover it. Our pipeline is therefore designed to respect physical limits of detectability while maximizing recoverable historical signal where it still exists.

---



