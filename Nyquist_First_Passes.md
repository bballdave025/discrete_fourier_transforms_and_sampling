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


