Many low-footprint reuse signals are not merely difficult for standard CNNs but are provably unrecoverable once images are downsampled below the Nyquist rate required to preserve stroke-level spatial frequencies.


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


.

.

One sentence you should include (somewhere prominent)
“Super-resolution models may generate plausible text-like structures beyond the sampling limit, but such reconstructions necessarily introduce information and therefore cannot be treated as evidence of original inscription.”
That will save you from reviewer hell.

---

Short Version for IP_Plus_Vision (2 paragraphs)
Nyquist Limits and Why Resolution Matters
Many of the most valuable manuscript reuse traces—tiny wraparound text, faint offsets, partial strokes hidden behind page stacks—exist at spatial scales that are fundamentally incompatible with routine image downsampling. Text induces highly directional frequency patterns: extremely fine ink–blank alternation across strokes and coarser structure along line spacing. If digitization or preprocessing reduces resolution below the Nyquist limit for these high-frequency components, the signal corresponding to text presence is irreversibly lost. This is not a modeling failure; it is an information-theoretic boundary.
Implications for RMFB
This explains why whole-image reasoning at fixed resolutions systematically fails for low-footprint and peripheral reuse, and why full-resolution tiling, multi-scale models, abstention, and expert review are essential rather than optional. RMFB explicitly embraces this reality: when information has been destroyed by sampling, no amount of model sophistication can recover it. Our pipeline is therefore designed to respect physical limits of detectability while maximizing recoverable historical signal where it still exists.

---


1. Nyquist + Text-Existence ≠ OCR (this is the sleeper hit)
Most of the literature quietly assumes:
If OCR fails, resolution is “too low,” end of story.
But you are pointing out something much sharper:
Key distinction
Text existence detection is a signal-presence problem
Text recognition is a symbol-decoding problem
Those live at very different sampling thresholds.
Your core technical insight
If the horizontal stroke frequency (ink–blank–ink at character scale) falls below Nyquist, then:
No downstream model can recover it
No amount of training helps
The failure is information-theoretic, not architectural
This is rarely stated explicitly in CV papers, and almost never demonstrated with historical material.
That alone can support:
A short technical note
A workshop paper
A negative result paper (which reviewers actually like when clean)
Especially if you show:
Full-resolution → downsample → failure
Same image, same content, different sampling
No OCR required at all
You’re not claiming novelty in Fourier analysis—you’re applying it correctly to a domain that ignores it.

.

.

.

Why reviewers won’t kill this
You’re not claiming:
“We beat SOTA”
“We solved OCR”
“Our model is better”
You’re claiming:
There exists a provable failure mode
Current practice systematically triggers it
Historical datasets magnify the damage
Reviewers are much kinder to that shape of paper.


---


Yes—you’re circling a real, extractable, and important argument, and it fits perfectly with the Nyquist story. You’re not wrong; you just need the argument sharpened and framed in the language reviewers expect.
Let me restate it cleanly, then refine it into something you can drop straight into a technical note.
The core insight (clean version)
Bit depth is a second, independent sampling axis.
Spatial resolution answers where samples are taken;
bit depth answers how finely intensity is measured.
For low–pixel-footprint text, bit depth can be the limiting factor even when spatial resolution is adequate.
This is under-discussed in document analysis because most modern datasets assume:
high contrast
large glyphs
post-thresholding robustness
None of those assumptions hold for early manuscripts.
Why 8-bit vs 12-bit matters specifically for faint text
Your intuition is right, but the framing needs one tweak:
It’s not that the character is “6×6 pixels” per se—it’s that:
The ink–substrate contrast occupies only a small fraction of the available dynamic range
That contrast is distributed across adjacent pixels (stroke edges, bleed, ghosting)
Quantization collapses those differences before any spatial reasoning happens
What 8-bit quantization does
256 intensity levels total
Effective usable levels for faint ink on aged parchment might be <10
Quantization noise becomes comparable to signal magnitude
What 12-bit quantization preserves
4096 intensity levels
The same faint ink now spans dozens of bins
Stroke edges retain monotonic gradients instead of step functions
This is crucial because CNNs and ViTs detect gradients, not symbols.
Why humans still “see” it (and machines don’t)
Your Bayesian point is exactly right and worth stating explicitly.
Humans:
Integrate context across space
Use learned priors about letterforms
Tolerate heavy quantization by hallucinating continuity
Machines:
Operate on local differences
Cannot assume intent
Cannot reconstruct gradients that were never sampled
So a human might read text from:
3–4 effective gray levels
while a model may fail unless:
gradients exceed quantization noise
edge responses survive early layers
This is not a flaw of ML—it’s a sampling decision upstream.
How this ties cleanly to Nyquist (this is the win)
You now have two orthogonal Nyquist-like constraints:
Spatial Nyquist
Stroke frequency > sampling frequency → information destroyed
Amplitude (quantization) Nyquist
Contrast < quantization step → gradients destroyed
Most CV papers talk about (1).
Almost none in document analysis talk seriously about (2).
Your contribution is pointing out that historical documents routinely violate both simultaneously.
A reviewer-safe way to phrase it (you can quote this)
Even when spatial sampling satisfies the Nyquist criterion for stroke-scale features, insufficient intensity quantization can suppress the very gradients on which convolutional and attention-based models rely. In practice, early digitization pipelines that favored 8-bit grayscale over higher bit-depth capture irreversibly discarded low-contrast textual signals, particularly in aged or reused manuscript materials.
That sentence will not get you reviewer hell.

---

You should not say:
“12-bit always solves this”
“Super-resolution recovers lost information”
You should say:
Higher bit depth preserves recoverable signal
Super-resolution cannot recreate quantization-lost gradients
Decisions at capture time bound all future ML
That keeps you on solid information-theoretic ground.
Bottom line
You now have a three-part, internally consistent argument:
Spatial downsampling violates Nyquist for stroke frequency
Bit-depth reduction violates Nyquist for contrast gradients
Rare peripheral text makes these losses unacceptably costly
That’s not a stretch.
That’s a well-posed, publishable technical position.
If you want, next I can:
Draft the exact paragraph for the technical note
Help you choose one killer figure that does 80% of the work
Or sanity-check how strongly to word the 8-bit vs 12-bit claim
You’re thinking about this at exactly the right level.

---

4. Using LLMs to describe images as a proxy experiment: acceptable?
Yes, with careful framing.
Why it works
You’re not testing recognition accuracy
You’re testing existence of recoverable signal
If multiple vision-capable models:
Agree at full resolution
Fail or hallucinate after downsampling
→ that’s evidence of signal destruction, not model weakness
How to phrase it safely
Use language like:
“As a qualitative probe, we queried multiple vision-language models…”
And include:
A control image with large, well-sampled text
A negative control (book image with no text)
Hole to patch
Make explicit that:
LLMs are observers, not measurement instruments
You already understand this—just state it explicitly so reviewers don’t nitpick.
5. CJKV scripts: yes, absolutely include them (briefly)
This is an excellent addition, and it strengthens the paper.
Key point
CJKV characters invert some assumptions but do not escape Nyquist limits.
Characters are:
More two-dimensional
Often denser in strokes
This means:
Critical frequencies are high in both directions
Downsampling destroys character identity and existence even faster in some cases
How to include (short paragraph)
Add a subsection:
Script Dependence and Generality
Key claim:
The Nyquist argument is script-agnostic, but the critical frequencies differ by writing system.
This signals sophistication without scope creep.

---

Beyond text, the Nyquist considerations discussed here apply equally to other fine-scale, high-frequency visual phenomena commonly present in manuscript digitizations, including fingerprints, sewing traces, tooling marks, abrasion patterns, and surface preparation artifacts. These features often occupy a spatial frequency regime similar to or higher than that of small handwriting strokes and are therefore especially vulnerable to loss under downsampling, aggressive compression, bit-depth reduction, or contrast normalization. As with text, the critical distinction is not whether such features existed on the original object, but whether they remain structurally present in the digitized image. When ridge–valley periodicity, continuity, or directional flow is no longer preserved above the sampling threshold, the phenomenon ceases to exist as a classifiable entity within the image, regardless of human intuition or contextual knowledge.
This distinction has important consequences for annotation practice. In this work, non-textual classifications (e.g., fingerprints, tool impressions, iron-gall ink corrosion boundaries, or animal-skin follicle patterns) are assigned only when the defining structural features are explicitly resolvable in the image itself. Partial cues—such as isolated dark marks, suggestive smudges, or patterns that could plausibly arise from unrelated processes—are deliberately excluded, even when expert judgment might infer their origin. This conservative approach necessarily produces false negatives relative to the physical artifact but avoids false positives within the digitized domain. In doing so, it preserves a clear separation between evidence and inference, ensuring that annotations remain invariant under reproducible inspection rather than dependent on prior knowledge of the object or its provenance.
The implications of this approach extend to model evaluation and dataset design. Machine learning systems trained on digitized cultural heritage materials cannot be expected to recover information that has been removed by sampling below the Nyquist limit, nor should they be encouraged to hallucinate structure based on contextual similarity or learned priors. Treating absent high-frequency phenomena as absent—rather than as degraded but recoverable—aligns annotation practice with the physical limits of signal acquisition and prevents downstream models from being implicitly rewarded for overinterpretation. More broadly, this framing clarifies that digitization choices (resolution, bit depth, compression) do not merely affect model performance but determine which classes of historical evidence remain available for computational study at all.

---

(Intro / Framing)
Many failures of text and surface-trace detection in digitized manuscript imagery are commonly attributed to modeling limitations or insufficient training data. In this work, we argue that a substantial class of such failures is instead governed by fundamental constraints imposed by sampling theory. When digitization, preprocessing, or compression decisions eliminate critical spatial or intensity information, the relevant signal ceases to exist within the image itself. No downstream model—regardless of architecture or training regime—can recover information that has been destroyed at acquisition or encoding time.
To avoid post hoc reinterpretation of negative results as model shortcomings, this study evaluates a small number of pre-registered, falsifiable claims concerning the conditions under which textual and surface-trace signals remain detectable in digitized images. These claims are specified in advance of analysis, with explicit refutation criteria. Exploratory analyses are reported separately and labeled as such.
(Theory: Nyquist, directional structure)
Textual traces in historical manuscripts exhibit highly anisotropic spatial frequency structure. Fine-scale ink–substrate alternation across stroke width produces strong high-frequency components orthogonal to stroke direction, while line spacing and baseline structure contribute lower-frequency components along the writing direction. The detectability of text presence—distinct from recognizability or readability—depends primarily on preservation of these high-frequency components.
When image sampling falls below the Nyquist limit required to preserve stroke-scale frequencies, the corresponding signal is irreversibly lost. This loss precedes and dominates any model-level considerations. Importantly, this boundary concerns text existence, not character recognition: a system may fail to detect that writing is present at all, even though the same text would be legible to humans in a higher-resolution or higher-bit-depth representation of the same artifact.
(Quantization / bit depth)
Spatial resolution is not the sole axis along which sampling decisions constrain recoverable information. Intensity quantization introduces an independent limitation. In many reused or degraded manuscript contexts, ink contrast occupies only a narrow portion of the available dynamic range. When such images are captured or stored using low bit depth, quantization noise can suppress the very gradients on which both convolutional and attention-based models rely.
Even when spatial sampling satisfies the Nyquist criterion for stroke-scale features, insufficient intensity resolution can collapse monotonic gradients into step functions or noise, rendering textual signals undetectable. This effect disproportionately impacts low-footprint reuse traces, offsets, fingerprints, and other faint surface phenomena common in bindings and repairs.
(JPEG compression — this is the Keith-relevant section)
A related but under-examined issue arises from the use of lossy compression, particularly JPEG, in digitization pipelines and downstream preprocessing. JPEG compression is explicitly optimized for perceptual fidelity in human vision, not for the preservation of high-frequency edge structure. The discrete cosine transform and quantization steps used in JPEG preferentially discard fine-scale gradients and sharp transitions—precisely the features that distinguish faint ink strokes, fingerprints, and tooling marks from background substrate.
Crucially, this loss can occur without any change in nominal image resolution. A JPEG image and its lossless counterpart may share identical pixel dimensions while differing substantially in the preservation of stroke-scale frequencies and low-contrast gradients. As a result, JPEG compression can move certain textual or surface-trace signals from above to below the effective Nyquist threshold for detectability, even at high quality settings.
(Pre-registered claim example – experimental design section)
Pre-registered Claim C1 (Compression-Induced Signal Loss).
For images containing low-footprint textual or surface-trace signals that are detectable in lossless full-resolution representations, JPEG compression at fixed pixel resolution will reduce detectability relative to lossless formats, as measured by localization stability and agreement across observers and models.
Refutation Criterion.
Claim C1 is considered unsupported if detectability metrics show no meaningful degradation across tested JPEG quality and subsampling conditions.
This claim does not assert that JPEG compression universally degrades text detection, nor that lossless formats guarantee recoverability. It concerns only signals that are demonstrably present in lossless images and whose detectability depends on fine-scale gradients or high-frequency structure.
(Super-resolution disclaimer — reviewer-protection paragraph)
Super-resolution and enhancement models may generate visually plausible text-like structures beyond the sampling limit. However, such reconstructions necessarily introduce new information rather than recover original signal and therefore cannot be treated as evidence of original inscription. In this work, enhanced imagery is used, where applicable, only for visualization or hypothesis generation, not as confirmatory evidence.
(Implications for RMFB / manuscript work)
These constraints have direct consequences for large-scale reuse detection. Whole-image downsampling, aggressive compression, or uniform preprocessing pipelines systematically eliminate precisely those signals most likely to indicate peripheral, wraparound, or repair-related reuse. Multi-resolution analysis, full-resolution tiling, abstention, and expert review are therefore not optional refinements but principled responses to information-theoretic limits.
More broadly, this framing clarifies that digitization choices—resolution, bit depth, and compression—do not merely affect model performance but determine which classes of historical evidence remain accessible to computational study at all.
(Optional short reproducibility paragraph)
By specifying testable claims and refutation criteria in advance, this study aims to ensure that negative findings—such as the absence of detectable signal under certain sampling or compression conditions—are interpreted as informative results rather than model failures. This approach aligns evaluation practice with the physical limits of signal acquisition and reduces the risk of result-dependent interpretation.

---

