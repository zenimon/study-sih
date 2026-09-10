# ImpulseGuard (SIH26052) — Brutal Jury Q&A Preparation
### Team Audrix | Prepared as a hostile DRDO/SIH panel simulation

**Read this first — ground rules used to build this document:**
- Every claim below traces to your two source documents (the SIH slide deck and the SIH Master V2 doc). Where a number is genuinely measured, it's marked **[MEASURED]**. Where it's a design choice or literature-backed estimate, it's marked **[DESIGN/CLAIM]**. Where it's not demonstrated at all, it's marked **[NOT YET PROVEN]** — and the answer teaches you to say so honestly.
- **One structural landmine to fix before your jury round:** your PPT slide shows an "End-to-End Latency Stability" chart with mean **5.399 ms**, while your Master doc explicitly lists full mic-to-speaker end-to-end latency as **not yet measured**, with only the 2.04 ms GRU-inference stage measured. If a juror cross-reads both documents, this is the first thing they will attack. Decide now, as a team, which one is true, and be ready to explain the discrepancy — see Q34 and Soul-Crusher Q1.
- **A second inconsistency:** the "Impulsive SI-SNR improvement" figure is quoted as **+6.76 dB** on the headline slide and **+6.85 dB** in the category-wise breakdown table. Know which one is correct before you're asked.

---

## SECTION 1 — PROBLEM STATEMENT (5 questions)

**Q1. What exactly is the problem you are solving, in one sentence?**

*Why is the jury asking this?* To test whether you can compress the project into a crisp, non-buzzword statement instead of reciting slide bullets.

*Ideal SIH Answer:* "Conventional defence communication systems — passive hearing protection and analog ANC headsets — handle steady background noise reasonably well, but fail on non-stationary and impulsive noise such as gunshots and blasts. ImpulseGuard is a lightweight, causal, edge-deployed AI model that specifically targets that impulsive-noise gap, running entirely on a low-cost ESP32-S3."

*Technical Explanation:* Stationary noise (engine hum, HVAC) has a roughly constant statistical profile, so spectral-subtraction and classical adaptive filters converge and stay converged. Impulsive noise (gunshots, blasts) is a short, high-energy, broadband transient — by the time a classical filter adapts, the event is already over, and it can also saturate mics/amps.

*Evidence from Our Project:* Master doc A.1 explicitly states traditional noise reduction "handles stationary noise reasonably but fails on non-stationary, overlapping, and impulsive conditions." Your own category-wise results back this: wind-combined continuous noise is where you're weak (Drone+wind −0.05 dB SI-SNRi), while impulsive is where you're strong (+6.76/+6.85 dB).

*If Evidence Is Missing:* N/A — this is your best-supported claim.

*What NOT to Say:* "We cancel all types of noise." You explicitly do not — continuous wind-combination noise is a documented weak point.

*If the Jury Attacks Again:* "So your system doesn't actually solve the general ANC problem?"

*Follow-up Answer:* "Correct — we deliberately scoped to the impulsive-noise gap because it's the specific, underserved, and highest-harm category per hearing-injury literature, rather than trying to be a generic ANC system competing head-on with mature continuous-noise products."

---

**Q2. Why is this problem difficult? Why hasn't it been solved already?**

*Why is the jury asking this?* To check you understand the technical difficulty, not just the "gap in the market."

*Ideal SIH Answer:* "Impulsive noise is difficult because it's short-duration, high-amplitude, broadband, and unpredictable in timing — you can't pre-adapt to it. It also risks clipping the mic/ADC before any software even sees clean data. And no streaming neural model in the reviewed literature has been evaluated against true impulsive military noise, so there wasn't an established playbook to follow."

*Technical Explanation:* Classical Wiener/spectral-subtraction methods assume slowly-varying noise statistics (updated over many frames). An impulse violates that assumption within a single frame, so the estimated noise floor is wrong exactly when it matters most.

*Evidence from Our Project:* Master doc C.1: "No streaming neural SE model in the literature has been evaluated against true impulsive noise;" the only impulsive-specific papers found (Medina & Coelho) use non-causal Hilbert-Huang decomposition, not real-time GRU/STFT.

*If Evidence Is Missing:* You have not independently verified this is a complete literature survey — you're relying on the papers you reviewed, not an exhaustive systematic review.

*What NOT to Say:* "Nobody has ever tried this before" — overclaiming novelty invites an easy rebuttal if the juror knows one counter-example.

*If the Jury Attacks Again:* "Are you sure no one has done this? What about classical clipping/limiter circuits used in analog headsets?"

*Follow-up Answer:* "Analog limiters exist and do attenuate peak amplitude, but they're not intelligibility-aware — they clip everything above a threshold, including speech, and don't reconstruct clean speech afterward. Our contribution is a learned, speech-preserving response plus a measured recovery-time characterization, which analog limiters don't provide."

---

**Q3. Why are existing solutions insufficient?**

*Why is the jury asking this?* Testing whether you understand your competitive landscape technically, not just commercially.

*Ideal SIH Answer:* "Passive hearing protection (EARMOR) gives fixed attenuation with zero adaptivity. Analog/DSP ANR headsets (QUIETPRO, PELTOR ComTac) target continuous low-frequency noise and are largely imported. FPGA-based adaptive-filtering research (e.g., Timmermann et al. 2024) works but needs heavier hardware than an ESP32-S3. Deep-learning SE surveyed by DRDO-DEAL is validated on generic wideband noise, not gunshot/explosion transients, and reports no recovery-time metric."

*Technical Explanation:* The comparison axis that matters is: (a) adaptivity, (b) impulsive-noise specificity, (c) hardware cost/footprint, (d) indigenous availability. Each competitor category fails at least one axis.

*Evidence from Our Project:* Master doc A.5, the existing-solutions comparison table.

*If Evidence Is Missing:* You have not run these competitor systems yourself in a head-to-head benchmark — this is a literature-based comparison, not an experimental one.

*What NOT to Say:* "Our system is strictly better than PELTOR ComTac." You have no side-by-side measured comparison.

*If the Jury Attacks Again:* "Have you tested against a real PELTOR unit?"

*Follow-up Answer:* "No — we don't have access to a reference unit for head-to-head testing. Our comparison is a literature-grounded capability gap analysis, not a measured benchmark. That's an honest limitation, and a real field trial against a reference product would be a valuable next validation step."

---

**Q4. Why does this matter specifically for defence?**

*Why is the jury asking this?* SIH judges want mission relevance, not just a general audio-engineering demo.

*Ideal SIH Answer:* "In combat and training environments, hearing protection is often not worn because it blocks situational-awareness sound and speech — soldiers trade hearing safety for mission awareness. Auditory injury (hearing loss + tinnitus) is the #1 and #2 most compensated service-connected disability category in the US VA system. A system that preserves speech intelligibility *while* suppressing damaging impulsive peaks removes that trade-off."

*Technical Explanation:* Impulsive acoustic trauma (gunshot/blast) causes disproportionate cochlear damage per unit energy compared to continuous exposure, because the ear's protective reflexes (stapedius reflex) are too slow to react to a transient.

*Evidence from Our Project:* Master doc A.2: 2.7M+ veterans receiving VA compensation for hearing loss/tinnitus, ~$660M/yr + ~$190M/yr cost; the doc's own framing that impulsive noise is "more damaging per unit energy than continuous noise."

*If Evidence Is Missing:* These are US VA statistics, not Indian Armed Forces data — you have not cited an Indian-specific casualty/disability figure.

*What NOT to Say:* "Indian soldiers suffer the same rate of hearing loss as US veterans" — you have no Indian-specific data to support that.

*If the Jury Attacks Again:* "This is US data. Why should an Indian defence jury care?"

*Follow-up Answer:* "The underlying acoustic-injury physiology is not country-specific — impulsive noise damages hearing the same way regardless of army. We use VA data because it's the most rigorously published dataset available; the SOF Week 2026 controversy over Indian Para SF hearing protection is the India-specific evidence that this gap exists here too."

---

**Q5. What happens if this problem is not solved?**

*Why is the jury asking this?* To test whether you can articulate consequence/stakes without melodrama.

*Ideal SIH Answer:* "Soldiers continue to either forgo hearing protection to preserve communication, accumulating long-term auditory injury, or wear protection and lose situational awareness/intelligibility in the field. At a systems level, India continues relying on imported hearing-protection/comms systems (PELTOR, QUIETPRO) rather than an indigenous alternative, at a time when the TCS programme has been delayed 15+ years."

*Technical Explanation:* N/A — this is a consequence/impact framing, not a technical claim.

*Evidence from Our Project:* Master doc A.4 — TCS programme 15+ year delay; SOF Week 2026 controversy.

*If Evidence Is Missing:* No.

*What NOT to Say:* "Soldiers will die without this." Avoid unsupported dramatic causal claims about casualties.

*If the Jury Attacks Again:* "That's a market argument, not an engineering one. Convince me on the engineering."

*Follow-up Answer:* "Understood — on the engineering side, unsolved impulsive noise means comms remain unusable exactly during the highest-stress, highest-information-value moments of an operation: contact, breach, extraction, when clear voice comms matter most."

---

## SECTION 2 — COMPLETE ARCHITECTURE (5 questions)

**Q6. Take one audio sample entering your system. Tell me exactly what happens to it until enhanced speech comes out.**

*Why is the jury asking this?* The single most common trap question — tests whether you actually understand your own pipeline or just memorized slide labels.

*Ideal SIH Answer:* "The INMP441 I2S mic captures audio at 16 kHz. We buffer it into 320-sample (20 ms) frames with 160-sample (10 ms) hop and take a 512-point STFT, giving 257 frequency bins. Those 257 bins are compressed into 22 Bark-scale sub-bands, from which we compute 44 features — 22 log-band energies plus 22 delta-energies. Those 44 features feed a causal GRU with 64 hidden units, which outputs a 44-dimensional vector representing a 22-band complex ratio mask (22 real + 22 imaginary components). That sub-band mask is interpolated back to 257 bins, applied multiplicatively to the noisy complex spectrum, and an ISTFT reconstructs the enhanced time-domain signal, which goes out through the MAX98357A to the speaker."

*Technical Explanation:* This is a mask-based, streaming, single-channel speech enhancement pipeline — same family as RNNoise/DTLN, but using a complex (not just magnitude) mask so phase is also corrected, per Williamson/Wang/Wang 2016.

*Evidence from Our Project:* Master doc B.1, the exact V1 pipeline.

*If Evidence Is Missing:* The V1 GRU-equivalence numerical check (streaming vs. batch) is verified (max diff 1.49e-7) but this is a Keras-side check, not yet re-verified against the on-device INT8 output on real audio.

*What NOT to Say:* Do not skip the "interpolation from 22 bands back to 257 bins" step — a juror who catches this omission will assume you don't understand your own reconstruction path.

*If the Jury Attacks Again:* "Why compress to 22 bands and then interpolate back up? Isn't that lossy?"

*Follow-up Answer:* "Yes, it's intentionally lossy — that's the perceptual-compression trade-off (see Section 4). We accept some fine-frequency-resolution loss in exchange for a much smaller GRU, because MCU weight count, not FLOPs, is the binding constraint on this hardware."

---

**Q7. Why does each processing block exist? Could you remove any of them?**

*Why is the jury asking this?* Tests whether every architectural piece is justified or just copied from a paper.

*Ideal SIH Answer:* "STFT gives us a time-frequency representation because speech and noise separate more cleanly in frequency than in raw time domain. Bark compression exists purely for compute/parameter efficiency on an MCU. The GRU exists to model temporal noise/speech dynamics causally. The complex mask exists because phase distortion audibly degrades speech, which a magnitude-only mask can't fix. ISTFT exists to return to a playable waveform. None of them are redundant — removing Bark compression alone would blow up our parameter count; removing the complex mask would degrade quality per Williamson et al."

*Technical Explanation:* Each block maps to a specific literature-justified engineering decision (see Section 4/Q&A on Bark, Section 5 on GRU, Section 6 on complex mask).

*Evidence from Our Project:* Master doc B.2, the architecture-choice-to-literature table.

*If Evidence Is Missing:* You have not run an ablation study (removing one block at a time and measuring the SI-SNR delta) to experimentally prove each block's necessity — the justification is literature-based, not ablation-tested on your own system.

*What NOT to Say:* "We just followed the standard pipeline" — sounds like you didn't make deliberate choices.

*If the Jury Attacks Again:* "Have you run an ablation study?"

*Follow-up Answer:* "No, not yet — that's a fair gap. An ablation (e.g., magnitude-mask-only vs. complex-mask, or 22 bands vs. 40 bands) would let us quantify each block's individual contribution rather than relying on literature precedent alone. That's on our validation to-do list."

---

**Q8. What are your system's inputs and outputs, precisely?**

*Why is the jury asking this?* Basic sanity check — surprisingly often teams fumble this.

*Ideal SIH Answer:* "Input: a single-channel 16 kHz PCM audio stream from the INMP441 I2S microphone, containing mixed speech + noise. Output: a single-channel enhanced PCM audio stream played through the MAX98357A to a speaker/headset, with impulsive-noise peaks attenuated and speech preserved."

*Technical Explanation:* Single-microphone, single-channel enhancement — no beamforming or multi-mic array is used, which simplifies hardware but removes spatial-filtering cues that a multi-mic array could exploit.

*Evidence from Our Project:* PPT technical-approach diagram: "Audio Input (INMP441 I2S Mic)" → ... → "Speaker/Headset."

*If Evidence Is Missing:* No multi-mic array was tested — this is a known scope limitation, not a gap in your claims.

*What NOT to Say:* "We do beamforming/spatial noise cancellation" — you don't; you're single-mic.

*If the Jury Attacks Again:* "Single mic means you can't exploit spatial separation. Isn't a mic array strictly better?"

*Follow-up Answer:* "In principle, yes — spatial cues help. We chose single-mic deliberately for cost, power, and MCU-pin-count reasons appropriate to a low-cost tactical headset; a mic-array version is a valid future direction but changes the hardware BOM significantly."

---

**Q9. Where in the pipeline does the V2 impulse detector sit, and why there?**

*Why is the jury asking this?* Tests understanding of your own V1→V2 modular design decision.

*Ideal SIH Answer:* "V2's impulse detector runs on the reconstructed audio *after* the V1 GRU + ISTFT, not on intermediate features. It's a parallel side-channel: detect impulse → drive an attack-release gain controller applied to the already-enhanced output."

*Technical Explanation:* Operating on reconstructed audio means V2 never touches or retrains the frozen V1 GRU — it's an independent, debuggable, tunable post-processing stage, which reduces integration risk.

*Evidence from Our Project:* Master doc C.2: "Design choice: detector operates on reconstructed audio, not intermediate features... because that's the actual signal reaching the speaker, and it means V2 doesn't require retraining or touching the frozen V1 GRU."

*If Evidence Is Missing:* The detector currently runs on a PC in Python — not yet ported to the ESP32 firmware (explicitly listed as a challenge in your feasibility slide).

*What NOT to Say:* "V2 is already running on the device." It is not — per your own risk slide, "Impulse-detection add-on isn't on the chip yet."

*If the Jury Attacks Again:* "So V2 isn't real yet — it's a PC prototype?"

*Follow-up Answer:* "Correct, and we say that openly. V2's architecture and detector logic are designed and evaluated offline; porting to C/C++ on the ESP32 is a defined, scoped implementation task, not a research risk, because the detector is standard DSP (energy/crest-factor/flux), not a new model requiring retraining."

---

**Q10. What's the end-to-end operation when there is NO impulse present — i.e., normal speech-in-noise?**

*Why is the jury asking this?* Tests whether you understand your baseline (V1) behavior separate from the impulse-specific path.

*Ideal SIH Answer:* "For non-impulsive audio, the signal only goes through the V1 path: STFT → Bark features → GRU → complex mask → ISTFT. The V2 impulse detector, when integrated, would simply pass audio through with gain g=1 (normal gain, 'preserve speech') because no impulse is detected — it doesn't intervene."

*Technical Explanation:* This is exactly the "IDLE" state shown in your own V2 state-machine diagram (Impulse Decision → Normal Audio → Normal Gain, g=1, Preserve Speech).

*Evidence from Our Project:* PPT technical-approach diagram, the V2 Impulse Detector decision branch ("Yes/No → Normal Gain g=1 / Attack-Release Gain Controller").

*If Evidence Is Missing:* None — this is diagrammed in your own architecture.

*What NOT to Say:* "V2 always modifies the signal." It doesn't — it's supposed to be transparent in the non-impulsive case.

*If the Jury Attacks Again:* "How do you guarantee the detector doesn't false-trigger on normal loud speech?"

*Follow-up Answer:* "That's an open risk — see Section 12 (false positives). We use an adaptive threshold with hysteresis specifically to reduce false triggers, but we have not yet measured a false-positive rate on loud speech vs. true impulses."

---

## SECTION 3 — SIGNAL PROCESSING / STFT (7 questions)

**Q11. Why exactly did you choose 20 ms frames and 10 ms hop? Why not 10 ms / 5 ms, or 32 ms / 16 ms?**

*Why is the jury asking this?* Classic parameter-justification trap — many teams copy standard values without understanding the trade-off.

*Ideal SIH Answer:* "20 ms frame / 10 ms hop is the standard choice across the real-time speech-enhancement literature we grounded our design in — RNNoise, DTLN, and the CRN family all use this or very similar framing. It balances frequency resolution (need enough samples to resolve speech formants) against latency (a frame must be short enough to keep total algorithmic delay usable for real-time comms) and against MCU compute budget (fewer, larger frames per second = less per-second GRU work)."

*Technical Explanation:* Frame length trades time resolution against frequency resolution (uncertainty principle) — shorter frames give worse frequency resolution but lower latency; 20 ms is long enough to resolve most formant structure while keeping algorithmic latency in the tens-of-ms range, which is the accepted threshold for real-time speech comms before delay becomes perceptible/annoying.

*Evidence from Our Project:* Master doc B.2: "20ms frame / 10ms hop, 512-pt FFT — Standard across RNNoise, DTLN, CRN family; FFT size = frame length avoids zero-padding overhead."

*If Evidence Is Missing:* You have not run your own sweep (e.g., testing 10 ms/5 ms) to measure the SI-SNR-vs-latency trade-off experimentally on your own dataset — the 20/10 choice is precedent-based, not empirically tuned for your specific noise distribution.

*What NOT to Say:* "20/10 is just the default value everyone uses" — true but sounds like you didn't reason about it; always follow with the trade-off explanation above.

*If the Jury Attacks Again:* "Shorter frames would reduce latency further — why didn't you push lower?"

*Follow-up Answer:* "We could, but shorter frames mean fewer FFT bins to resolve speech harmonics and more frames-per-second for the GRU to process, which raises MCU compute load per second. 20/10 was the literature-converged sweet spot; a dedicated sweep on our own hardware is a valid follow-up experiment we haven't run yet."

---

**Q12. Why a 512-point FFT giving 257 bins? Why not 256 or 1024?**

*Why is the jury asking this?* Tests understanding of the FFT-size-to-frame-length relationship.

*Ideal SIH Answer:* "512-point FFT matches our 320-sample (20 ms @ 16kHz) frame length without unnecessary zero-padding — it's the smallest power-of-two ≥ 320 samples, giving 257 unique frequency bins (512/2 + 1) via the real-FFT symmetry."

*Technical Explanation:* For a real-valued signal, an N-point FFT produces N/2+1 unique bins (the rest are complex-conjugate mirrors). Choosing FFT size ≈ frame length avoids wasting compute on padding while still landing on an efficient power-of-two for FFT algorithms.

*Evidence from Our Project:* Master doc B.2 line on FFT size.

*If Evidence Is Missing:* No experimental comparison against 256-pt or 1024-pt was run.

*What NOT to Say:* "1024-point would just be better resolution, so bigger is always better" — bigger FFT costs more compute and doesn't match your 20ms frame without padding artifacts.

*If the Jury Attacks Again:* "You then throw away resolution by compressing to 22 Bark bands — was the fine FFT resolution ever necessary?"

*Follow-up Answer:* "The fine 257-bin resolution matters for accurate ISTFT reconstruction and complex-mask application — the Bark compression is only on the *feature/estimation* side for the GRU; the mask is interpolated back up to full 257-bin resolution before being applied to the original spectrum, so we don't lose reconstruction fidelity, only estimation granularity."

---

**Q13. Walk me through the windowing function you use and why it matters.**

*Why is the jury asking this?* Tests whether you understand the mechanics under the STFT, not just the headline numbers.

*Ideal SIH Answer:* "We apply a standard analysis window (matching the RNNoise/DTLN convention referenced in our literature grounding) before each FFT to reduce spectral leakage from framing a continuous signal into finite blocks, and a matching synthesis window with overlap-add on reconstruction to avoid discontinuities at frame boundaries."

*Technical Explanation:* Without windowing, abruptly truncating a signal to a frame introduces spectral leakage (energy spreading into adjacent frequency bins) because the implicit rectangular window has poor sidelobe suppression. A smooth window (Hann/Hamming-type) tapers the frame edges, and for perfect reconstruction with 50% overlap, analysis and synthesis windows must satisfy the constant-overlap-add (COLA) condition.

*Evidence from Our Project:* PPT block diagram lists "Output Safety: DC Blocking, Gain/Soft Limiter, Overlap-Add" — confirming overlap-add reconstruction is implemented, though the exact window function isn't named in either document.

*If Evidence Is Missing:* **The exact window function (Hann, Hamming, etc.) and its COLA verification are not explicitly stated in either source document — do not claim a specific window type to the jury unless your team can confirm it from the actual code.**

*What NOT to Say:* Do not invent a specific window name if you're not certain — say "we use a standard COLA-compliant window; I'd need to confirm the exact type from our implementation" rather than guessing.

*If the Jury Attacks Again:* "If you don't know your own window function, how do you know your overlap-add is artifact-free?"

*Follow-up Answer:* "The clean-speech control correlation of 0.995 — meaning the model barely alters already-clean input — is indirect evidence that our overlap-add reconstruction isn't introducing significant artifacts on clean signal, though a dedicated windowing/COLA unit test would make this rigorous rather than inferred."

---

**Q14. What is the overlap percentage between frames, and why that value?**

*Why is the jury asking this?* Direct follow-on to frame/hop — checks arithmetic understanding.

*Ideal SIH Answer:* "Frame = 320 samples (20 ms), hop = 160 samples (10 ms), so consecutive frames overlap by 50% — standard for STFT-based processing to keep reconstruction smooth via overlap-add."

*Technical Explanation:* 50% overlap is the classic choice because it satisfies COLA conditions cleanly for common window shapes and gives one new frame's worth of "fresh" spectral estimate every hop, balancing update rate against compute.

*Evidence from Our Project:* Master doc B.1 (320-sample frame / 160-sample hop = 20ms/10ms).

*If Evidence Is Missing:* None — arithmetic follows directly from stated frame/hop values.

*What NOT to Say:* Don't say "we use no overlap" — that would break your ISTFT reconstruction quality claims entirely.

*If the Jury Attacks Again:* "Higher overlap gives smoother reconstruction — why not 75%?"

*Follow-up Answer:* "75% overlap would mean 4x the frames-per-second for the same frame length, quadrupling GRU inference calls per second on an MCU with a tight power/compute budget — 50% is the standard trade-off point between reconstruction smoothness and compute cost."

---

**Q15. How exactly is ISTFT reconstruction performed, and what happens at frame boundaries?**

*Why is the jury asking this?* Tests understanding of the synthesis side, which is often glossed over.

*Ideal SIH Answer:* "After the complex mask is applied to each frame's spectrum, an inverse FFT converts it back to a time-domain frame, and consecutive frames are combined via overlap-add — each output sample is the sum of contributions from the two (or more) overlapping frames covering that time index, weighted by the synthesis window."

*Technical Explanation:* Overlap-add reconstruction requires the analysis+synthesis window pair to sum to a constant (or known envelope) across overlapping regions, otherwise you get amplitude modulation artifacts at the frame rate.

*Evidence from Our Project:* PPT diagram explicitly lists "ISTFT — Enhanced PCM Audio" and a downstream "Output Safety" block with "Overlap-Add."

*If Evidence Is Missing:* Boundary-artifact measurement (e.g., listening tests or objective boundary-discontinuity metrics) is not reported in either document.

*What NOT to Say:* "There are no boundary artifacts" without having specifically measured for them.

*If the Jury Attacks Again:* "How do you know there are no clicking artifacts at frame boundaries?"

*Follow-up Answer:* "We haven't run a dedicated boundary-artifact test. Our clean-speech control correlation (0.995) suggests gross artifacts aren't dominating, but a targeted test — e.g., measuring energy at hop boundaries on a pure tone — would give a rigorous answer instead of an inferred one."

---

**Q16. What's your algorithmic latency contribution from framing alone (before compute)?**

*Why is the jury asking this?* Tests whether you distinguish algorithmic (buffering) latency from compute latency — a very common confusion.

*Ideal SIH Answer:* "Algorithmic latency from framing is at minimum one hop (10 ms) since we need a full new hop of samples before we can process, plus look-ahead if any is used — and our design is strictly causal with no look-ahead, so framing latency alone is ~10–20 ms depending on how buffering is implemented, before any compute time is added."

*Technical Explanation:* Total system latency = algorithmic/buffering latency (waiting for enough samples) + processing/compute latency (STFT+GRU+mask+ISTFT execution time) + I/O latency (DMA/I2S transfer). These are additive and often conflated in marketing claims.

*Evidence from Our Project:* Master doc B.6 explicitly separates "neural inference stage... measured... within budget at 2.04ms" from the not-yet-measured full pipeline including STFT/ISTFT and I/O.

*If Evidence Is Missing:* The exact framing/buffering-only latency number is not separately reported in either document — only the GRU-inference-only number (2.04 ms) is measured.

*What NOT to Say:* Do not equate the 2.04 ms GRU number with total system latency — that is explicitly only the neural inference sub-component.

*If the Jury Attacks Again:* "So your 2.04ms number is meaningless for real-world latency?"

*Follow-up Answer:* "It's meaningful as a budget-feasibility proof — it shows the most compute-heavy stage fits comfortably in the 10ms hop budget with ~8ms headroom for STFT/Bark/mask/ISTFT/I2S. It is not, by itself, the end-to-end latency figure, which is our next measurement milestone."

---

**Q17. Is your STFT/ISTFT causal, and why does that matter?**

*Why is the jury asking this?* Real-time-specific trap — non-causal processing is disqualifying for a real-time comms device.

*Ideal SIH Answer:* "Yes — strictly causal. We never use future frames; each output depends only on the current and past frames, which is a hard requirement for streaming, real-time operation."

*Technical Explanation:* A non-causal (bidirectional) system would need to see future audio before producing output, which is architecturally impossible in a live comms stream without adding real look-ahead delay.

*Evidence from Our Project:* Master doc B.2: "Strictly causal, streaming hidden state — Universal requirement across every real-time SE paper reviewed... bidirectional RNNs are explicitly noted as unsuited to real-time frame processing."

*If Evidence Is Missing:* None — this is a design invariant you can state with confidence.

*What NOT to Say:* Don't confuse "causal" with "zero-latency" — causal still has the hop-length + compute latency discussed in Q16.

*If the Jury Attacks Again:* "Causal-only limits your model's quality compared to bidirectional models — did you sacrifice performance for this?"

*Follow-up Answer:* "Yes, inherently — a bidirectional model would likely score higher on offline SI-SNR benchmarks, but it's unusable for a live comms device. We chose the entire GRU/causal family deliberately because real-time usability is a harder constraint than maximizing offline quality metrics."

---

## SECTION 4 — BARK / ERB FILTERBANK (5 questions)

**Q18. Why use a perceptual filterbank at all instead of raw FFT bins?**

*Why is the jury asking this?* Fundamental design-rationale check.

*Ideal SIH Answer:* "Two reasons: perceptual relevance and compute efficiency. The Bark scale approximates how the human ear resolves frequency — coarser at high frequencies, finer at low — so we're not wasting model capacity resolving frequency detail the ear can't perceive. Practically, it also compresses 257 raw bins down to 22 bands, which is what keeps our GRU small enough for an MCU."

*Technical Explanation:* The Bark scale is a psychoacoustic frequency scale where each Bark band roughly corresponds to a critical band of human hearing; using it as a feature basis focuses model capacity on perceptually meaningful structure.

*Evidence from Our Project:* Master doc B.2: "RNNoise (Valin, 2018) and PercepNet... perceptual band compression is the standard trick to keep MCU-class GRU weight count small; RNNoise's own complexity analysis shows weights, not FLOPs, dominate MCU cost."

*If Evidence Is Missing:* No ablation comparing Bark-band features against raw-bin or Mel-scale features was run on your own dataset.

*What NOT to Say:* "Bark bands make the model 'hear' better than a raw model." This anthropomorphizes and overstates — it's a compute/parameter-efficiency and inductive-bias choice, not a guaranteed quality improvement.

*If the Jury Attacks Again:* "Why Bark and not Mel, which is more common in speech ML generally?"

*Follow-up Answer:* "Bark and Mel are both perceptually-motivated log-ish frequency scales and are very similar in practice; we followed RNNoise's precedent of using Bark specifically since our whole pipeline design pattern (feature compression → small causal RNN) is modeled directly on RNNoise's approach."

---

**Q19. Why specifically 22 bands? Why not 16 or 32?**

*Why is the jury asking this?* Forces justification of a specific hyperparameter, not just the general technique.

*Ideal SIH Answer:* "22 bands is within the standard range used by comparable real-time systems (RNNoise uses a similar-order Bark decomposition) and gave us a good balance between GRU input dimensionality (44 features = 22 bands × 2) and parameter count (23,980 total params) while keeping our INT8 quantization error (mask MAE 0.0202) well inside literature benchmarks."

*Technical Explanation:* More bands = finer frequency resolution retained in the feature space but more GRU input/output dimensionality and more parameters; fewer bands = smaller model but coarser control over spectral shaping.

*Evidence from Our Project:* PPT/Master doc: 22 bands → 44 features → GRU(64) → 23,980 parameters, INT8 mask MAE 0.0202/max 0.096.

*If Evidence Is Missing:* No direct sweep across band-counts (e.g., 16 vs 22 vs 32) with resulting SI-SNR/parameter-count trade-off curve is reported — 22 was chosen by precedent, not an internal sweep.

*What NOT to Say:* "22 is the mathematically optimal number of bands." There's no proof of optimality — it's a reasonable, precedent-following choice.

*If the Jury Attacks Again:* "Prove 22 is better than, say, 30 bands for your specific noise distribution."

*Follow-up Answer:* "We can't prove that without running the sweep — that's a legitimate open experiment. What we can say is 22 bands kept us within a parameter budget that measurably fits the ESP32-S3's compute/memory budget with 2.04ms inference, which was our primary constraint."

---

**Q20. What information is lost when you compress 257 bins into 22 bands?**

*Why is the jury asking this?* Tests honesty about the cost side of the trade-off, not just the benefit.

*Ideal SIH Answer:* "Fine-grained frequency detail within each band is lost — multiple adjacent FFT bins are pooled into one band-level energy value, so the GRU can't distinguish narrow-band structure within a single Bark band. This could matter for, e.g., separating two tonal components that fall in the same band."

*Technical Explanation:* Band-pooling is a form of lossy dimensionality reduction — information theoretically, you cannot fully recover the original 257-bin resolution from 22 pooled values, which is exactly why the *mask* (not the pooled features) is what gets interpolated and applied back at full 257-bin resolution.

*Evidence from Our Project:* Architecture pipeline itself (257→22 bands for features; mask interpolated back to 257 bins for reconstruction) — Master doc B.1.

*If Evidence Is Missing:* No quantified information-loss metric (e.g., reconstruction error from band-pooling alone) is reported.

*What NOT to Say:* "No information is lost, we get it all back via interpolation." Interpolating a coarse mask back to fine resolution does not recover lost fine-grained *estimation* information — it only defines how the mask is applied, not what the model could distinguish.

*If the Jury Attacks Again:* "So a narrow-band interferer within one Bark band could be missed?"

*Follow-up Answer:* "Yes, that's a real limitation of the architecture, particularly at higher frequencies where Bark bands are wider. It's an accepted trade-off for the parameter savings; a hybrid architecture with finer high-frequency resolution is a possible future refinement."

---

**Q21. Why not just use all 257 FFT bins directly as GRU input?**

*Why is the jury asking this?* Forces you to state the compute-cost argument in concrete numbers.

*Ideal SIH Answer:* "Because GRU parameter count scales with input/output dimensionality — going from 44 features to, say, 514 (257×2 real/imag) would massively increase weight count, which is the dominant MCU cost per RNNoise's own complexity analysis. That would break our 42KB INT8 model size and likely blow the 10ms real-time compute budget."

*Technical Explanation:* GRU parameter count scales roughly quadratically with hidden-size × input-size for the gate matrices; increasing input dimensionality from 44 to 514 would require either a much larger hidden state (worse) or accept degraded capacity per feature — either way, MCU feasibility suffers.

*Evidence from Our Project:* Your literature-grounded design-choices slide: "Causal GRU is the unanimous real-time choice... no streaming GRU/CRM pipeline evaluated on true impulsive noise exists," combined with the stated 23,980-parameter/42KB budget.

*If Evidence Is Missing:* You have not actually trained a 257-bin-input version to measure exactly how much bigger/slower it would be — this is a reasoned estimate, not a measured comparison.

*What NOT to Say:* "It would be literally impossible to run 257 bins on ESP32-S3." Not proven — you simply chose not to, for efficiency; avoid absolute claims you haven't tested.

*If the Jury Attacks Again:* "Give me actual numbers — how much bigger would the model be?"

*Follow-up Answer:* "We don't have that number today since we didn't train that variant — it would require a dedicated experiment. Qualitatively, going from 44 to ~514 input dims is roughly a 10x+ increase in first-layer weight count alone, which is why we didn't pursue it given our 42KB deployment target."

---

**Q22. Why not use log-Mel or raw linear-frequency binning instead of Bark?**

*Why is the jury asking this?* Tests whether you know Bark isn't the only option and can defend the specific choice against a close alternative.

*Ideal SIH Answer:* "Bark and Mel scales are both perceptually motivated and similar in shape; we specifically followed RNNoise's precedent since our whole design pattern — perceptual-band compression feeding a small causal RNN for MCU deployment — is modeled directly on RNNoise's approach, which uses Bark bands."

*Technical Explanation:* Mel scale is derived from pitch-perception experiments and commonly used in speech recognition front-ends; Bark scale is derived from critical-band masking experiments and is more common in perceptual audio coding / noise suppression contexts like RNNoise. Both are log-like compressive mappings; differences in practice are usually small.

*Evidence from Our Project:* Master doc B.2, RNNoise citation for Bark-band feature compression.

*If Evidence Is Missing:* No side-by-side comparison of Bark vs. Mel on your own dataset/metrics was run.

*What NOT to Say:* "Bark is objectively superior to Mel for this task." Not demonstrated — it's the precedent we followed, not a proven superiority.

*If the Jury Attacks Again:* "So you just copied RNNoise's choice without testing alternatives?"

*Follow-up Answer:* "We followed RNNoise's *precedent* deliberately, because RNNoise is the most cited, most MCU-proven real-time noise-suppression architecture in this space — that's a reasonable engineering starting point. We haven't run a Bark-vs-Mel ablation ourselves, and that would be a fair thing to add to strengthen the claim further."

---

## SECTION 5 — GRU (7 questions)

**Q23. Why GRU instead of LSTM?**

*Why is the jury asking this?* Core architecture-choice question, almost guaranteed to be asked.

*Ideal SIH Answer:* "GRU has fewer gates than LSTM (2 vs 3, and no separate cell state), which means fewer parameters and less compute per unit of hidden size for comparable modeling capacity — critical on an MCU. Hasannezhad, Ouyang, Zhu, Champagne (APSIPA 2020) directly compared architectures for complex-mask estimation and found GRU wins the accuracy/memory/parameter trade-off for this exact task under non-stationary noise."

*Technical Explanation:* LSTM has input, forget, and output gates plus a separate cell state; GRU merges the cell and hidden state and uses only reset and update gates, roughly cutting recurrent parameter count relative to LSTM for the same hidden size, at a typically small cost in modeling capacity for many sequence tasks.

*Evidence from Our Project:* Master doc B.2, and your PPT's "Why GRU — Literature-Grounded" chart comparing PESQ improvement and time/memory/parameters across BLSTM/GRU/LSTM.

*If Evidence Is Missing:* The PESQ-improvement and parameter-count comparison chart on your slide appears to be reproduced from cited literature (comparing architectures in general), not a head-to-head experiment you ran yourself on your own dataset with your own trained LSTM variant.

*What NOT to Say:* "GRU is always better than LSTM." Not universally true — it's a task- and constraint-dependent trade-off, and you haven't trained your own LSTM baseline to confirm it for your specific dataset.

*If the Jury Attacks Again:* "Did you train your own LSTM version to compare directly?"

*Follow-up Answer:* "No — we relied on the Hasannezhad et al. (APSIPA 2020) comparison, which studied this exact GRU-vs-LSTM trade-off for complex-mask estimation, rather than re-running that comparison ourselves. Training our own LSTM baseline on our dataset would strengthen this claim and is a reasonable next experiment."

---

**Q24. Why not a CNN instead of a recurrent architecture?**

*Why is the jury asking this?* Tests breadth of architectural reasoning.

*Ideal SIH Answer:* "CNNs are excellent at local spectral pattern extraction but don't naturally model long-range temporal dependencies the way a recurrent hidden state does — for streaming, frame-by-frame enhancement where each frame's optimal mask depends on the evolving noise/speech state over time, a recurrent unit gives a natural, constant-memory way to carry that state forward. Causal 1D-CNNs (like in CRN-family models) are used elsewhere in the literature, but our design followed the GRU-centric line (RNNoise/DTLN)."

*Technical Explanation:* A causal CNN needs a fixed receptive field (via dilation/stacking) to capture temporal context, and that context window is finite and fixed at design time; a GRU's hidden state is a continuously updated summary that can, in principle, carry information arbitrarily far back, with O(1) per-frame update cost — attractive for a streaming MCU application.

*Evidence from Our Project:* Master doc B.2 cites CRN (Tan & Wang) as a reviewed literature point but your chosen line follows RNNoise/DTLN/GTCRN's GRU-centric approach.

*If Evidence Is Missing:* You have not trained or benchmarked a CNN variant of your own architecture.

*What NOT to Say:* "CNNs can't do real-time audio." False — CRN and similar causal-CNN architectures are real-time-capable; be precise that this was a design-lineage choice, not a hard technical impossibility for CNNs.

*If the Jury Attacks Again:* "CRN-family models are also in your literature review — why not follow that line instead?"

*Follow-up Answer:* "Both are valid real-time-capable lines. We followed the GRU/RNNoise lineage because it has the most MCU-deployment precedent (RNNoise itself runs on constrained hardware) and because Hasannezhad et al.'s GRU+CRM result gave us a direct architectural template for the complex-mask output we wanted."

---

**Q25. You say GRU is lightweight. Quantify 'lightweight.'**

*Why is the jury asking this?* Forces numeric precision instead of vague adjectives — extremely common jury trap.

*Ideal SIH Answer:* "Our full model — 44-input GRU with 64 hidden units plus a dense output layer — has 23,980 trainable parameters, about 93.67 KB in FP32. After INT8 quantization it's 42,352 bytes (~42KB) as a deployed TFLite Micro model. Measured inference time on real ESP32-S3 silicon is 2.04ms per 10ms hop, i.e., about 20.4% of our real-time compute budget for that stage."

*Technical Explanation:* "Lightweight" here specifically means: small enough in both parameter count (memory footprint) and per-frame compute (latency) to run within an MCU's SRAM/flash constraints and real-time deadline — verified concretely, not asserted.

*Evidence from Our Project:* Master doc B.3 and B.5 — exact parameter count, model sizes, and measured inference time.

*If Evidence Is Missing:* Power draw (mA/mW) during this inference is explicitly **not yet measured** — "lightweight" in energy terms is unverified.

*What NOT to Say:* "It's super lightweight" without giving the numbers above — always lead with the figures.

*If the Jury Attacks Again:* "42KB and 2ms — is that actually impressive, or just adequate?"

*Follow-up Answer:* "It compares favorably to literature reference points — e.g., DeepFilterNet2 reports RTF 0.42 on a Raspberry Pi 4, which is far more powerful hardware than our ESP32-S3, whereas our GRU-stage RTF is roughly 0.204 on the actual MCU. That said, our full-pipeline RTF including STFT/Bark/ISTFT/I2S is not yet measured, so a complete apples-to-apples comparison isn't available yet."

---

**Q26. What is your GRU hidden size, and how did you choose 64 units specifically?**

*Why is the jury asking this?* Direct hyperparameter-justification question.

*Ideal SIH Answer:* "64 hidden units. This size gave us enough modeling capacity to hit our target SI-SNR improvement numbers while keeping total parameter count (23,980) and INT8 quantization error (mask MAE 0.0202, well inside the Rusci et al. 2022 benchmark) within acceptable bounds for MCU deployment."

*Technical Explanation:* Hidden size directly drives GRU parameter count (roughly proportional to hidden_size × (input_size + hidden_size) per gate, times 3 gates) — doubling hidden size roughly quadruples the recurrent weight matrices' parameter count, so this is a high-leverage size knob.

*Evidence from Our Project:* Master doc B.3 (64 hidden units, 23,980 total params) and B.2 (Rusci et al. 2022 quantization benchmark comparison).

*If Evidence Is Missing:* No reported sweep across hidden sizes (e.g., 32 vs 64 vs 128) with a resulting performance/size trade-off curve — 64 appears to be a chosen operating point, not the output of an explicit architecture search.

*What NOT to Say:* "64 was mathematically proven optimal." Not shown — it's a reasonable engineering choice within your constraints, not a proven optimum.

*If the Jury Attacks Again:* "What would happen with 128 hidden units — have you tried?"

*Follow-up Answer:* "Not yet — a hidden-size sweep (32/64/128) with the resulting parameter-count/quantization-error/SI-SNR trade-off would be a good, fast follow-up experiment to formally justify 64 as a Pareto-optimal choice rather than a reasonable default."

---

**Q27. What is the computational cost of your GRU per frame, and how did you measure it?**

*Why is the jury asking this?* Tests rigor of your headline latency claim.

*Ideal SIH Answer:* "We measured actual GRU inference time on the deployed INT8 TFLite Micro model running on real ESP32-S3 silicon: 2.04 ms per 10ms hop. This is a hardware measurement, not a simulated/theoretical FLOP-count estimate."

*Technical Explanation:* Inference time on an MCU depends on clock speed, memory access patterns (SRAM vs PSRAM), and how well the TFLite Micro kernels are optimized for the target chip — it's not purely a function of parameter count, which is why an actual on-device timing measurement is more trustworthy than a theoretical FLOPs estimate.

*Evidence from Our Project:* PPT headline number "2.04 ms — GRU inference / 10ms hop," and Master doc B.5: "Measured GRU inference: 2.04 ms, against a 10ms hop budget."

*If Evidence Is Missing:* The measurement methodology (how many runs, averaged how, warm vs cold start, PSRAM vs SRAM tensor placement effects) is not detailed in either document — be ready to describe your actual measurement setup from memory/notes, not just the headline number.

*What NOT to Say:* "2.04ms is our total system latency." It is explicitly only the GRU inference stage — see Q16/Q34.

*If the Jury Attacks Again:* "Is 2.04ms an average, worst-case, or single measurement?"

*Follow-up Answer:* "That's a fair question we should be precise about in the room — our source documents report it as 'measured,' but don't specify whether it's mean, min, or max across multiple runs. We should confirm the exact statistic from our raw logs before the jury round rather than guess."

---

**Q28. What's the memory footprint of your model — SRAM vs PSRAM vs Flash — and why did you need PSRAM at all?**

*Why is the jury asking this?* Tests genuine embedded-systems understanding, not just ML-side knowledge.

*Ideal SIH Answer:* "The deployed INT8 model is 42,352 bytes in flash. The TFLite Micro tensor arena needed for inference is allocated in PSRAM via ps_malloc, sized at 200KB, because the ESP32-S3's internal SRAM alone couldn't hold the full tensor arena for our model plus the rest of our firmware's memory needs."

*Technical Explanation:* TFLite Micro requires a contiguous "tensor arena" scratch buffer for intermediate activations during inference; on ESP32-S3, internal SRAM is limited and shared with the rest of the application (audio buffers, I2S DMA buffers, network/BLE stacks if used), so overflow into external PSRAM (slower, but much larger) is a common and necessary mitigation — though PSRAM access is slower than internal SRAM, which can affect inference latency.

*Evidence from Our Project:* Master doc B.5: "Tensor arena in PSRAM (ps_malloc), 200KB — necessary because internal RAM couldn't hold it."

*If Evidence Is Missing:* The specific latency penalty of PSRAM vs. SRAM tensor placement (i.e., how much slower 2.04ms would be if in SRAM, or vice versa) is not separately quantified.

*What NOT to Say:* "Memory wasn't a real constraint for us." It clearly was — you had to move the tensor arena to PSRAM specifically because of an SRAM limitation.

*If the Jury Attacks Again:* "Doesn't PSRAM access latency undermine your low-latency claims?"

*Follow-up Answer:* "It's a real factor, but our 2.04ms measurement is *with* PSRAM already in the loop, since that's the actual deployed configuration — so the headline number already reflects that overhead, not an idealized SRAM-only best case."

---

**Q29. Could this same model run with a smaller hidden size or fewer bands on a cheaper MCU than ESP32-S3?**

*Why is the jury asking this?* Tests scalability/portability understanding — often relevant for a "cost feasibility" line of questioning.

*Ideal SIH Answer:* "Likely yes in principle — the architecture is deliberately small (23,980 params), and the design pattern (Bark compression + small causal GRU) is proven MCU-feasible at even smaller scales in the literature (Rusci et al. 2022). We haven't tested on a cheaper MCU than ESP32-S3 ourselves, though."

*Technical Explanation:* Portability would depend on the target MCU having sufficient flash for the 42KB model, sufficient RAM for the tensor arena (or external PSRAM support), and a TFLite Micro (or equivalent) runtime port — ESP32-S3 was chosen partly for its available PSRAM and existing embedded-ML tooling support.

*Evidence from Our Project:* Master doc B.2/B.5's MCU-feasibility literature grounding (Rusci et al. 2022) and PSRAM dependency noted above.

*If Evidence Is Missing:* No test on any MCU other than ESP32-S3 has been performed — this answer is a reasoned inference from architecture size, not a demonstrated result.

*What NOT to Say:* "It'll run on any cheap MCU." Overclaim — the PSRAM dependency for the 200KB tensor arena specifically constrains which MCUs are viable without redesign.

*If the Jury Attacks Again:* "Your PSRAM dependency means you actually need a relatively capable MCU, not 'any cheap chip' — isn't that a cost problem?"

*Follow-up Answer:* "Fair point — ESP32-S3 with PSRAM is still low-cost relative to FPGA/DSP-based competitor hardware, which is the comparison that matters for our cost claims, but it's not the absolute cheapest MCU tier available. A memory-optimization pass (smaller tensor arena, in-place ops) could potentially remove the PSRAM dependency, but that's unexplored."

---

## SECTION 6 — COMPLEX RATIO MASK (5 questions)

**Q30. What is a complex ratio mask (cIRM), in plain terms?**

*Why is the jury asking this?* Basic-concept check, but often where nervous teams stumble on terminology.

*Ideal SIH Answer:* "It's a mask applied to the complex (magnitude + phase) spectrum of the noisy signal, rather than just its magnitude. For each time-frequency bin, the mask has a real and imaginary component; multiplying the noisy complex spectrum by this complex mask can simultaneously correct both the magnitude and the phase of the estimated clean speech."

*Technical Explanation:* A magnitude-only mask leaves the noisy phase untouched, implicitly assuming noisy phase ≈ clean phase — which is a reasonable approximation at high SNR but breaks down at low SNR. A complex mask, following Williamson, Wang, Wang (2016), can represent an arbitrary complex-valued transformation, letting the model also correct phase errors.

*Evidence from Our Project:* PPT: "Complex Masking — 22 Real + 22 Imaginary → 257-bin Mask;" Master doc B.2 cites Williamson, Wang, Wang (2016), IEEE/ACM TASLP as the theoretical basis.

*If Evidence Is Missing:* You have not run a controlled magnitude-only-mask ablation on your own dataset to quantify how much the complex (vs. magnitude-only) formulation specifically helped your results.

*What NOT to Say:* "Complex mask means the model is more complex/complicated." This conflates "complex" (the math term for real+imaginary numbers) with "complicated" — a jury will notice this conceptual confusion immediately.

*If the Jury Attacks Again:* "How much of your +6.76dB improvement is due to the complex (vs. magnitude-only) formulation specifically?"

*Follow-up Answer:* "We can't isolate that contribution without an ablation — we didn't train a magnitude-only-mask baseline ourselves to compare against. That would be a valuable experiment to quantify the complex-mask's specific contribution rather than relying on the general literature finding (Williamson et al.) that complex masks outperform magnitude-only ones."

---

**Q31. Why complex mask instead of just magnitude-only masking, which is simpler?**

*Why is the jury asking this?* Direct trade-off justification.

*Ideal SIH Answer:* "Magnitude-only masking leaves phase distortion uncorrected, which becomes audible especially in low-SNR and transient conditions — exactly our impulsive-noise use case. Williamson, Wang, Wang (2016) showed complex ratio masking improves over magnitude-only approaches by also correcting phase, which is directly relevant to our impulsive-noise focus where phase distortion around a transient can be significant."

*Technical Explanation:* Phase errors are perceptually less noticeable than magnitude errors at high SNR (this is why magnitude-only masking was historically dominant), but their audibility increases as SNR drops or during rapid signal changes — both of which describe an impulsive-noise event.

*Evidence from Our Project:* Master doc B.2 citation of Williamson et al. 2016 as direct theoretical basis; your architecture's explicit real+imaginary output.

*If Evidence Is Missing:* No SNR-stratified phase-error analysis specific to your impulsive test segments has been reported.

*What NOT to Say:* "Phase never matters for magnitude-only masks, that's why we don't use them." Backwards — you use complex masks precisely because phase *does* matter in your target conditions.

*If the Jury Attacks Again:* "Doesn't the complex mask cost more parameters than magnitude-only?"

*Follow-up Answer:* "Yes — our GRU outputs 44 values (22 real + 22 imaginary) instead of 22 for magnitude-only, roughly doubling the output layer size. Given our total parameter count is still only 23,980, we judged that cost acceptable for the phase-correction benefit, though we haven't isolated exactly how much that doubling cost us in inference time versus a magnitude-only variant."

---

**Q32. What do the real and imaginary mask components actually represent physically?**

*Why is the jury asking this?* Tests whether "complex mask" is understood mathematically, not just as a slide label.

*Ideal SIH Answer:* "For each time-frequency bin, multiplying the noisy complex spectral value by our estimated complex mask value (a real part and imaginary part) performs a combined scaling and phase rotation — the real part contributes to both magnitude scaling and in-phase correction, the imaginary part contributes to quadrature/phase correction. Together they let the model move the noisy spectral point toward the estimated clean spectral point in the complex plane, not just shrink its magnitude toward the origin."

*Technical Explanation:* Representing a complex number in Cartesian form (a + bi) versus polar form (magnitude, phase) are mathematically equivalent; a complex ratio mask expressed as (real, imaginary) components, when multiplied with the noisy spectrum, is equivalent to simultaneously applying a magnitude scale and a phase shift.

*Evidence from Our Project:* Master doc B.2/PPT architecture diagrams (22 real + 22 imaginary mask values).

*If Evidence Is Missing:* No visualization of learned mask phase-correction behavior (e.g., phase-error-before-vs-after plots) is reported in either document.

*What NOT to Say:* "The real part is for volume and imaginary part is for something imaginary/unreal." This misstates the math — both components jointly determine magnitude and phase after multiplication; they aren't cleanly separable into "loudness" vs. "something else."

*If the Jury Attacks Again:* "Can you show me a plot of your model correcting phase specifically?"

*Follow-up Answer:* "Not today — we have waveform-level evidence (the impulse-suppression plots) but not a dedicated phase-error-before/after visualization. That's a straightforward analysis to add using our existing evaluation pipeline and would make this specific claim more concrete."

---

**Q33. How is the enhanced spectrum actually reconstructed from the mask output?**

*Why is the jury asking this?* Tests whether you understand the full inference-to-output chain, not just the model's forward pass.

*Ideal SIH Answer:* "The GRU outputs a 22-band complex mask (44 values). We interpolate that up to the full 257-bin resolution, multiply it element-wise with the original noisy complex spectrum (bin by bin), and the result is the estimated clean complex spectrum, which then goes through ISTFT with overlap-add to produce the enhanced time-domain waveform."

*Technical Explanation:* This is standard mask-based enhancement: estimated_clean_spectrum[f] = mask[f] × noisy_spectrum[f], for each frequency bin f, where mask[f] is complex-valued and interpolated from the coarser 22-band estimate to the full 257-bin grid.

*Evidence from Our Project:* Master doc B.1: "22 complex sub-band mask → interpolation to 257 bins → complex spectral masking → ISTFT."

*If Evidence Is Missing:* The exact interpolation method (linear, nearest-neighbor, or a learned/fixed Bark-to-linear mapping matrix) is not specified in either document — do not invent a specific method under jury pressure.

*What NOT to Say:* Don't claim a specific interpolation algorithm (e.g., "we use cubic spline interpolation") unless you can confirm it from your actual code — guessing under pressure is worse than saying "let me confirm that detail."

*If the Jury Attacks Again:* "What interpolation method exactly, and does it introduce artifacts at band boundaries?"

*Follow-up Answer:* "I'd want to confirm the exact interpolation method from our implementation before giving a definitive answer rather than guess in the room. What I can say is our clean-speech control correlation of 0.995 suggests any boundary artifacts from interpolation aren't dominating overall signal fidelity on clean input."

---

**Q34. What happens if the predicted mask is wrong — what does the failure look like?**

*Why is the jury asking this?* Robustness/failure-mode question — tests whether you've thought past the success case.

*Ideal SIH Answer:* "A wrong mask could either under-suppress noise (residual noise/impulse energy leaks through) or over-suppress, attenuating speech along with noise, potentially causing audible artifacts like musical noise or speech dropouts. Our residual energy ratio metric (0.10, i.e., ~90% reduction) and clean-speech control correlation (0.995, showing minimal distortion on clean input) are our current proxies for how often and how badly this happens, but we don't have a dedicated 'mask failure rate' metric."

*Technical Explanation:* Mask-based enhancement failure modes are well-documented in the SE literature: under-estimation leaves noise audible; over-estimation causes "speech distortion" (an intelligibility cost); and rapidly-varying/incorrect masks across frames can create "musical noise" artifacts from spectral discontinuities.

*Evidence from Our Project:* Master doc B.4 — residual energy ratio 0.10 and clean-speech correlation 0.995 are the closest quantified proxies you have.

*If Evidence Is Missing:* No dedicated failure-mode taxonomy or musical-noise-specific metric (e.g., a listening test or a specific artifact-detection algorithm) has been run.

*What NOT to Say:* "The mask is never wrong." No neural model has zero error — your own metrics (mask MAE 0.0202, max 0.096) prove nonzero prediction error exists.

*If the Jury Attacks Again:* "Give me a worst-case example of mask failure from your own test set."

*Follow-up Answer:* "We haven't specifically surfaced and reported a worst-case failure example — our reported numbers are aggregate statistics (mean SI-SNRi, mean IISRT/RSDD). Pulling the single worst-performing test clip and analyzing why would be a good concrete addition before the jury round."

---

## SECTION 7 — DATASET & TRAINING (8 questions)

**Q35. What datasets did you use, and why these specific ones?**

*Why is the jury asking this?* Baseline data-provenance check.

*Ideal SIH Answer:* "Clean speech from LibriSpeech train-clean-100 (100 hours of clean English speech). Background-noise augmentation from MUSAN. Labeled real-world noise, including gunshot, engine-idling, and siren classes, from UrbanSound8K. Multilingual testing from the Hindi speech corpus SLR103. Drone-specific noise from the AVQ/All-Drone-Noises collection on Zenodo."

*Technical Explanation:* This is a synthetic-mixture construction approach: clean speech + separately-sourced noise are digitally mixed at controlled SNRs to create training pairs with known ground truth — standard practice in the SE literature (e.g., the DNS Challenge methodology) because real noisy-clean paired recordings are hard to obtain at scale.

*Evidence from Our Project:* PPT "Datasets Used" slide and Master doc B.3.

*If Evidence Is Missing:* None of these are gunshot/blast recordings from actual military ranges or operational environments — UrbanSound8K's "gun_shot" class is a general urban-sound dataset, not defence-specific.

*What NOT to Say:* "We trained on real military gunshot data." You did not — UrbanSound8K is a general urban sound dataset, not military-sourced.

*If the Jury Attacks Again:* "UrbanSound8K gunshots are civilian recordings — how do you know they resemble a real firearm/blast in a combat environment?"

*Follow-up Answer:* "We don't know that yet, honestly — that's a real domain-gap risk. Civilian gunshot recordings likely differ from close-range military weapon discharge or blast overpressure in peak SPL, spectral content, and reverberant environment. Validating against real or realistic military-grade impulsive recordings is an explicitly identified next step, not something we've done."

---

**Q36. How was your synthetic training data constructed — SNR ranges, mixing procedure?**

*Why is the jury asking this?* Tests dataset-construction rigor.

*Ideal SIH Answer:* "We built 26,000 production mixtures total — 20,000 train / 3,000 validation / 3,000 test — each 5 seconds long, with SNR ranging from −5 dB to +20 dB, and impulse gain scaling from 0.20 to 2.00 to vary impulsive-event intensity."

*Technical Explanation:* Varying SNR across a wide range during training helps the model generalize across noise intensities rather than overfitting to one operating point; varying impulse gain specifically stress-tests the model's response to both subtle and extreme transient events.

*Evidence from Our Project:* Master doc B.3: exact mixture counts, SNR range, impulse gain range.

*If Evidence Is Missing:* The exact mixing algorithm (e.g., how impulse events are time-aligned within the 5s clips, how many impulses per clip) is not detailed in the source documents.

*What NOT to Say:* Don't invent details about the exact mixing script logic beyond what's confirmed above.

*If the Jury Attacks Again:* "Is −5dB to +20dB realistic for a battlefield gunfire scenario, which can be far louder/closer?"

*Follow-up Answer:* "That's a fair challenge — our SNR range was chosen to give broad training coverage, but we have not validated it against measured close-range military SPL data. Extreme near-field blast SNR could fall outside our trained range, which is a real generalization risk we should state proactively."

---

**Q37. What was your train/validation/test split, and how did you prevent data leakage?**

*Why is the jury asking this?* Classic ML-rigor trap question.

*Ideal SIH Answer:* "20,000 train / 3,000 validation / 3,000 test mixtures. For the drone-noise subset specifically, we used a session-level split — ensuring no audio from the same drone recording session appears in more than one of train/val/test — to avoid leakage."

*Technical Explanation:* Data leakage occurs when information from the test set indirectly influences training — e.g., if two clips from the same recording session (same drone, same background acoustics) end up split across train and test, the model could partially "memorize" session-specific characteristics rather than generalizing, inflating test performance artificially.

*Evidence from Our Project:* Master doc B.3: "Glasgow drone split: session-level split (no drone/session leakage between train/val/test)."

*If Evidence Is Missing:* The document only explicitly describes session-level leakage prevention for the drone subset — it's not stated whether the same rigor (e.g., speaker-level split for LibriSpeech, or source-recording-level split for UrbanSound8K clips) was applied across all dataset components.

*What NOT to Say:* "We guaranteed zero leakage across our entire dataset." Only the drone-session-level leakage prevention is explicitly documented — don't extend that guarantee to components not mentioned.

*If the Jury Attacks Again:* "What about speaker leakage in LibriSpeech, or noise-clip leakage in UrbanSound8K — was that controlled too?"

*Follow-up Answer:* "The documented leakage-prevention detail we have is specifically the drone-session split. We should verify and be ready to confirm whether equivalent speaker-level and noise-source-level splitting was applied elsewhere, since that's exactly the kind of gap a rigorous jury will probe."

---

**Q38. Are your test speakers and test noise sources unseen during training?**

*Why is the jury asking this?* Directly tests generalization claim validity.

*Ideal SIH Answer:* "For the drone-noise component specifically, yes — session-level splitting guarantees unseen drone sessions in test. For the broader dataset, our held-out 3,000-mixture test set is drawn from the same overall pool structure (train/val/test split before mixture generation), which is standard practice, though we don't have a document-confirmed guarantee of unseen speakers/unseen noise-source-IDs for every component beyond the drone subset."

*Technical Explanation:* "Unseen" in ML evaluation must be interpreted precisely — unseen *mixtures* (different noisy combinations) is a much weaker claim than unseen *speakers* or unseen *noise recordings*, since a model could still overfit to a specific speaker's voice or a specific noise recording's exact spectral signature even across many different mixtures of it.

*Evidence from Our Project:* Master doc B.3 — 26,000 mixtures split 20k/3k/3k before evaluation; drone-session-level split explicitly called out.

*If Evidence Is Missing:* Confirmed unseen-speaker and unseen-noise-recording splitting for the non-drone dataset components is not explicitly documented.

*What NOT to Say:* "All our results are on completely unseen speakers and noise." Only explicitly confirmed for the drone subset — don't generalize this guarantee.

*If the Jury Attacks Again:* "So some of your +6.76dB improvement could be partly from memorized noise recordings, not true generalization?"

*Follow-up Answer:* "That's a real possibility we can't fully rule out without confirming split methodology for every dataset component. It's a legitimate rigor gap, and confirming (or fixing) unseen-speaker/unseen-noise splitting across the entire pipeline, not just the drone subset, would strengthen this claim significantly."

---

**Q39. Your dataset is synthetic. Why should I believe your system will work in a real military environment?**

*Why is the jury asking this?* The single most common and most dangerous sim-to-real question for this project.

*Ideal SIH Answer:* "We shouldn't ask you to fully believe that yet — this is an honest limitation, not something we're hiding. Synthetic mixtures let us train and evaluate at scale with known ground truth, which real noisy/clean paired recordings can't provide. But we have explicitly identified 'only tested on generated/simulated noisy audio' as an open risk, and our stated next step is collecting and testing on real recorded noise — real gunshots, real drone/vehicle audio — to validate sim-to-real transfer before any field-deployment claim."

*Technical Explanation:* The sim-to-real gap is a well-known problem across audio and robotics ML: models trained on synthetic mixtures can overfit to artifacts of the mixing process itself (e.g., unrealistic reverberation, unnaturally clean SNR boundaries, or microphone/recording characteristics of the source clips) that don't match real deployed-microphone-in-real-environment conditions.

*Evidence from Our Project:* Your own feasibility slide explicitly lists this as a challenge: "Only tested on generated/simulated noisy audio so far — not yet tested with real gunshots or real field recordings," with a stated mitigation strategy to "collect and test on real recorded noise."

*If Evidence Is Missing:* No real-world field or range recording has yet been used for training or evaluation — this is fully unproven at the "real environment" level.

*What NOT to Say:* "Our synthetic results generalize perfectly to real conditions." This is precisely the overclaim your own document explicitly warns against ("works equally well for all noise types" is listed under 'Avoid' claims).

*If the Jury Attacks Again:* "So everything you've shown me today could fail completely on a real gunshot?"

*Follow-up Answer:* "It's possible some degradation would occur — we can't rule that out without real-world testing, and we won't claim otherwise. What we can say is the architecture and training methodology are grounded in the same literature-proven techniques used by deployed systems like RNNoise, and UrbanSound8K's gunshot class, while civilian-sourced, does provide real recorded impulsive acoustic events, not purely synthesized clicks — it's a meaningful but not sufficient starting point."

---

**Q40. How many training epochs, what optimizer, what loss function did you use?**

*Why is the jury asking this?* Basic ML-training-hygiene check — a common "gotcha" for teams that didn't personally run training.

*Ideal SIH Answer:* "**[Confirm from your own training logs/notebook before the jury round — this level of hyperparameter detail is not specified in either source document, and we should not guess numbers we can't back up.]** What we can confirm structurally is the model architecture (44→GRU64→44) and the loss target (complex ratio mask regression against ground-truth clean-speech-derived masks)."

*Technical Explanation:* Complex-mask estimation is typically trained with a regression loss (e.g., MSE or a perceptually-weighted variant) between the predicted mask (or predicted enhanced spectrum) and the ground-truth ideal mask (or clean spectrum) derived from the known clean/noise components of each synthetic mixture.

*Evidence from Our Project:* Not explicitly detailed in either document.

*If Evidence Is Missing:* Epoch count, optimizer choice (Adam, etc.), learning rate, and exact loss formulation are not stated in your source documents — **this is a gap the team must fill from actual training code/logs before the jury round**, since confidently answering this from memory (accurately) is a strong credibility signal and its absence is conspicuous.

*What NOT to Say:* Do not invent a plausible-sounding epoch count or optimizer name under pressure — if you're not sure, say so and commit to confirming it, rather than guessing and risking being caught out by a technical follow-up (e.g., "why that learning rate?").

*If the Jury Attacks Again:* "You built this model and don't know your own training hyperparameters?"

*Follow-up Answer:* "That's a legitimate criticism if we can't answer it in the room — before the jury round, every team member should be able to state epochs, optimizer, learning rate, batch size, and loss function from memory, since these are basic facts about our own training run, not open research questions."

---

**Q41. How do you know you're not overfitting?**

*Why is the jury asking this?* Core ML-validity question.

*Ideal SIH Answer:* "We use a held-out validation set (3,000 mixtures) separate from both train and final test, and track performance on it during training. Our reported headline numbers (+6.76dB impulsive SI-SNRi, etc.) are measured on the held-out 3,000-mixture test set, not the training set."

*Technical Explanation:* Overfitting shows up as a growing gap between training-set and validation-set performance during training; the standard mitigation is early stopping or regularization once validation performance plateaus or degrades while training performance keeps improving.

*Evidence from Our Project:* Master doc B.3's 20k/3k/3k train/val/test split structure.

*If Evidence Is Missing:* Neither document reports an actual train-vs-validation loss curve, or explicit mention of early stopping / regularization technique used — so you cannot currently show the jury direct evidence of *how* overfitting was monitored/prevented, only that a validation split exists.

*What NOT to Say:* "We definitely aren't overfitting" without being able to show a loss curve — assert what's evidenced (proper train/val/test split exists) and be honest about what's not yet shown (the actual curves).

*If the Jury Attacks Again:* "Show me your training vs. validation loss curve."

*Follow-up Answer:* "We don't have that plotted in our current SIH materials — it exists in our training logs and should be pulled into a slide/appendix before the jury round, since a loss curve is the single most convincing piece of evidence against overfitting a technical juror can ask for."

---

**Q42. What's your total dataset size, and is it enough for a task this specialized?**

*Why is the jury asking this?* Tests awareness of data-sufficiency limits.

*Ideal SIH Answer:* "79.51 hours total (17,345 speech clips / 60.72h, 3,875 noise clips / 18.79h), used to generate 26,000 5-second production mixtures. For a small, task-specific 24K-parameter model — not a large foundation model — this is a reasonable scale, consistent with other lightweight real-time SE systems in the literature."

*Technical Explanation:* Data sufficiency depends heavily on model capacity: a 24K-parameter model needs far less data to avoid overfitting than a multi-million-parameter model, so "is 79.51 hours enough" should be judged relative to your specific (small) model size, not against large-model data-scaling norms.

*Evidence from Our Project:* Master doc B.3 exact dataset statistics.

*If Evidence Is Missing:* No formal data-scaling experiment (e.g., training on 25%/50%/100% of the data to see if performance is still improving with more data) has been run to show whether 79.51 hours is sufficient or whether more data would still help.

*What NOT to Say:* "More data would definitely help" or "we have exactly the right amount" — neither is demonstrated without a scaling-curve experiment.

*If the Jury Attacks Again:* "How do you know 79.51 hours is enough and not a bottleneck?"

*Follow-up Answer:* "We don't have a data-scaling curve to answer that rigorously — that's a legitimate open question. Given our small parameter count, we don't believe data volume is our primary current bottleneck (weak categories like drone+wind look more like a data-*diversity*/hard-example problem than a data-*volume* problem — see our V3 plan), but we can't prove that without the scaling experiment."

---

## SECTION 8 — EVALUATION & RESULTS (8 questions)

**Q43. What is your baseline for comparison — what exactly are you improving over?**

*Why is the jury asking this?* Every improvement number is meaningless without a defined baseline.

*Ideal SIH Answer:* "Our baseline is the raw noisy mixture — unprocessed audio before ImpulseGuard. SI-SNR improvement (SI-SNRi) is measured as the SI-SNR of our enhanced output minus the SI-SNR of the raw noisy input, on the same held-out test mixtures."

*Technical Explanation:* SI-SNRi (scale-invariant signal-to-noise ratio improvement) is a standard SE metric precisely because it's a relative, before/after comparison, which controls for the difficulty of each individual test clip rather than reporting an absolute SI-SNR that would vary hugely by clip difficulty.

*Evidence from Our Project:* Master doc B.4, results table framed explicitly as "SI-SNR improvement" (delta from noisy baseline).

*If Evidence Is Missing:* You have not benchmarked against a *competing* system (e.g., RNNoise itself, or a classical spectral-subtraction baseline) on the same test set — your improvement number is against raw noisy input, not against another noise-suppression system.

*What NOT to Say:* "We beat RNNoise by 6.76dB." You have not run RNNoise on your test set for a head-to-head comparison — your number is versus unprocessed input, not versus a competing algorithm.

*If the Jury Attacks Again:* "How does your +6.76dB compare to running RNNoise itself on the same clips?"

*Follow-up Answer:* "We haven't run that head-to-head comparison — it would be a very strong addition to our evidence base. Right now our claim is precisely scoped as 'improvement over unprocessed noisy input,' not 'improvement over the best existing algorithm,' and we should keep that distinction explicit with the jury."

---

**Q44. How many test samples were your headline numbers computed over?**

*Why is the jury asking this?* Tests statistical credibility of your reported averages.

*Ideal SIH Answer:* "3,000 held-out test mixtures, drawn from our full 26,000-mixture production dataset (20k train / 3k val / 3k test split)."

*Technical Explanation:* 3,000 samples is a reasonably large test set for computing stable mean statistics like SI-SNRi, though the *category-wise* breakdowns (e.g., drone+wind specifically) are necessarily computed on a smaller sub-slice of that 3,000, which increases the variance/uncertainty on those specific numbers.

*Evidence from Our Project:* Master doc B.3, test split size.

*If Evidence Is Missing:* The exact per-category sample count (e.g., how many of the 3,000 test clips are "drone+wind" specifically) is not reported — so the confidence interval on category-wise numbers like "−0.05 dB" for drone+wind is unknown.

*What NOT to Say:* "Our drone+wind number (−0.05dB) is precise and reliable." Without knowing the per-category sample count, you can't claim precision — it might be based on a small slice of the 3,000 total.

*If the Jury Attacks Again:* "How many of your 3,000 test clips are specifically drone+wind combinations? Could that −0.05dB just be noise in a small sample?"

*Follow-up Answer:* "We don't have that per-category count readily available, and that's a fair concern — a small per-category sample size would mean the category-wise numbers carry more statistical uncertainty than the aggregate 3,000-sample headline numbers. Reporting per-category sample counts and confidence intervals would make this table much more defensible."

---

**Q45. Was your test set truly unseen, or could there be any overlap with training?**

*Why is the jury asking this?* Repeated attack angle (ties to Section 7) — juries deliberately circle back.

*Ideal SIH Answer:* "The 26,000 production mixtures were split into 20k/3k/3k train/val/test before any training occurred, and for the drone-noise component specifically we used a session-level split guaranteeing no session overlap between train and test."

*Technical Explanation:* See Q37/Q38 — this is the same leakage-control question applied specifically to the evaluation numbers you're presenting as headline results.

*Evidence from Our Project:* Master doc B.3.

*If Evidence Is Missing:* Same gap as Q38 — non-drone components' unseen-speaker/unseen-noise-source guarantees are not explicitly documented.

*What NOT to Say:* "Zero possibility of any leakage anywhere in the dataset." Overclaim beyond what's documented.

*If the Jury Attacks Again:* (see Q38 follow-up — likely to be re-asked in a different form)

*Follow-up Answer:* "As with the earlier leakage question — confirmed for the drone subset via session-level split; for other components we should confirm the exact split methodology from our data-generation code before stating a blanket guarantee."

---

**Q46. What is the worst-case result in your test set, not just the average?**

*Why is the jury asking this?* Averages hide failure cases — a classic jury attack to find the "worst 1%."

*Ideal SIH Answer:* "Our reported numbers are means (e.g., +6.76dB impulsive SI-SNRi). We have category-wise breakdowns showing our weakest categories are wind-combined continuous noise: Drone+wind at −0.05dB and Siren+wind at −0.16dB SI-SNRi — meaning the system very slightly *degrades* quality on those specific combinations, on average."

*Technical Explanation:* A negative mean SI-SNRi in a category means the enhanced output is, on average, *worse* than doing nothing for that noise type — an important, non-trivial failure signal that should be reported prominently, not buried, because it defines the system's honest operating envelope.

*Evidence from Our Project:* Master doc D.1, category-wise table: Drone+wind −0.05dB, Siren+wind −0.16dB.

*If Evidence Is Missing:* A true per-clip worst-case (the single worst clip in the entire test set, not just the worst category average) is not reported in either document.

*What NOT to Say:* "Our system never makes things worse." False by your own category-wise data — two categories show negative average improvement.

*If the Jury Attacks Again:* "So your system actively degrades some real defence-relevant noise conditions?"

*Follow-up Answer:* "Yes, for wind-combined continuous noise specifically — we say this proactively rather than waiting to be caught. That's exactly why V3's stated goal is targeted improvement on these categories via hard-example oversampling, not a claim that V1 already handles them."

---

**Q47. Why these specific metrics (SI-SNR, peak attenuation, residual energy ratio) and not PESQ/STOI/DNSMOS?**

*Why is the jury asking this?* Tests metric-choice justification — a subtle but real methodology question.

*Ideal SIH Answer:* "SI-SNR and SI-SNRi are standard, well-established objective SE metrics that don't require a reference model (unlike DNSMOS, which uses a separately-trained predictor). Peak attenuation and residual energy ratio are custom metrics we added specifically because standard metrics like PESQ/STOI are utterance-level averages that don't specifically characterize *impulsive* event suppression — which is our core differentiator."

*Technical Explanation:* PESQ and STOI are designed and validated primarily for continuous, utterance-level speech-quality/intelligibility prediction; they weren't specifically designed to characterize transient/impulsive event handling, which is why your team introduced impulse-specific metrics (peak attenuation, residual energy ratio, IISRT, RSDD) — see Section 9.

*Evidence from Our Project:* Master doc C.5 lists STOI/PESQ as *planned* additions for V2 evaluation, alongside your existing impulse-specific metrics — implying PESQ/STOI are not yet reported for V1.

*If Evidence Is Missing:* **PESQ and STOI scores for V1 are not reported in either source document** — only SI-SNR-family and your custom impulse metrics are given. Do not claim PESQ/STOI numbers you don't have.

*What NOT to Say:* Do not state a specific PESQ or STOI score for V1 — none is documented; if asked directly, say it hasn't been computed yet for V1.

*If the Jury Attacks Again:* "PESQ and STOI are the industry-standard intelligibility metrics — why haven't you reported them for V1?"

*Follow-up Answer:* "That's a legitimate gap — we prioritized SI-SNR and our novel impulse-specific metrics for V1, and STOI/PESQ are explicitly planned as part of the V2 three-way evaluation (raw/V1/V2). Computing them retroactively for V1 on our existing test set would strengthen our evidence base and should be done before presenting to a jury that will expect industry-standard intelligibility numbers."

---

**Q48. What does SI-SNR fail to capture that matters for your use case?**

*Why is the jury asking this?* Tests critical self-awareness about your own primary metric's limitations.

*Ideal SIH Answer:* "SI-SNR is a scale-invariant energy-ratio metric — it doesn't directly measure perceptual intelligibility (a human's ability to understand the words) or naturalness (how distorted/artifact-laden the output sounds). A system could improve SI-SNR while still sounding unnatural or introducing artifacts a listener would find annoying. That's part of why we also track residual energy ratio and clean-speech control correlation, and why STOI/PESQ are planned additions."

*Technical Explanation:* SI-SNR is computed purely from waveform energy relationships (target vs. error component), which correlates with but does not equal perceptual quality — this is a well-documented limitation across the SE metrics literature, which is why multi-metric evaluation (objective + perceptual + intelligibility) is standard practice.

*Evidence from Our Project:* Implicit in Master doc C.5's plan to add STOI/PESQ alongside SI-SNR-family metrics for V2 — the plan itself is evidence the team recognizes SI-SNR alone is insufficient.

*If Evidence Is Missing:* No human listening test / subjective MOS (mean opinion score) evaluation has been conducted for V1.

*What NOT to Say:* "SI-SNR improvement directly proves better intelligibility for a human listener." Not established — SI-SNR is an objective proxy, not a direct human-perception measurement.

*If the Jury Attacks Again:* "Have you done any listening tests with actual humans?"

*Follow-up Answer:* "No — all our current evaluation is objective/metric-based, not subjective/human-listener-based. A small MOS-style listening test, even informal, would be a valuable and relatively cheap addition to substantiate the intelligibility claims beyond objective metrics alone."

---

**Q49. Your clean-speech control correlation is 0.995 — what does that actually prove, and what doesn't it prove?**

*Why is the jury asking this?* Tests whether you understand this specific metric's exact scope, since it's a somewhat unusual metric to highlight.

*Ideal SIH Answer:* "It proves that when we feed the model already-clean speech (no noise), the output stays very close to the input — correlation 0.995 — meaning the model isn't distorting or degrading clean audio when there's nothing to suppress. It does NOT prove anything about noise-suppression performance itself — that's what SI-SNRi, peak attenuation, and residual energy ratio measure separately."

*Technical Explanation:* This is essentially a "do no harm" sanity check specific to mask-based systems — since the mask could theoretically distort the signal even in the absence of noise (e.g., due to model bias/artifacts), this metric isolates and rules that failure mode out on clean input.

*Evidence from Our Project:* Master doc B.4, listed as its own headline metric alongside — but conceptually separate from — the noise-suppression metrics.

*If Evidence Is Missing:* The exact clean-speech test set size/composition used to compute this correlation is not specified.

*What NOT to Say:* "0.995 correlation proves our noise suppression is excellent." It specifically proves the *opposite* scenario (behavior on clean input) — don't conflate the two.

*If the Jury Attacks Again:* "0.995 isn't 1.0 — what's causing that 0.5% deviation on clean speech?"

*Follow-up Answer:* "We don't have a specific attribution for that residual deviation — it could be minor reconstruction artifacts from windowing/overlap-add, quantization noise, or the model applying a very slight non-unity mask even on clean input. That's a worthwhile root-cause investigation we haven't done yet."

---

**Q50. How was the "improvement" for each metric actually calculated — mean, median, or something else, and were error bars or confidence intervals computed?**

*Why is the jury asking this?* Rigorous statistical-reporting trap — many teams report a single point estimate with no uncertainty measure.

*Ideal SIH Answer:* "Our source materials report point estimates (e.g., mean +6.76dB impulsive SI-SNRi) computed across the 3,000-clip held-out test set. **We do not currently have documented confidence intervals or variance/standard-deviation figures for these headline numbers** — that's a reporting gap, not a claim that the numbers are unstable, just that we haven't quantified their uncertainty."

*Technical Explanation:* A mean improvement of +6.76dB with unknown variance could hide a bimodal distribution (e.g., huge gains on some clips, near-zero or negative on others) — reporting standard deviation, median, or a confidence interval alongside the mean is standard rigorous-evaluation practice and materially strengthens (or appropriately qualifies) a headline claim.

*Evidence from Our Project:* Master doc B.4's results table lists single point values for every metric, with no reported variance/CI.

*If Evidence Is Missing:* Confirmed absent — no standard deviation, confidence interval, or median is reported for any headline metric in either source document.

*What NOT to Say:* "Our +6.76dB is a rock-solid, low-variance number." Not shown — you have no reported variance to support that claim either way.

*If the Jury Attacks Again:* "So for all I know, half your test set could have negative SI-SNRi and it's dragged up by a few big wins?"

*Follow-up Answer:* "That's possible and we can't rule it out with the numbers we currently have documented. Computing and reporting standard deviation or a distribution histogram alongside each headline mean is a fast, high-value addition before presenting these results to a technical jury."

---

## SECTION 9 — IISRT / RSDD (4 questions)

**Q51. What exactly is IISRT, and what does 233 ms actually mean?**

*Why is the jury asking this?* You've branded this as a novel contribution — the jury will test if it's rigorously defined or just a marketing number.

*Ideal SIH Answer:* "IISRT stands for [confirm full expansion from your own documentation — likely 'Impulse-Induced Speech Recovery Time' or similar] — it measures how long it takes, after an impulsive noise event, for the enhanced output to recover back to acceptable speech quality/intelligibility. Our measured value is 233.45 ms on our held-out impulsive test segments."

*Technical Explanation:* Post-impulse recovery time matters because even a system that successfully attenuates the impulse peak itself could leave a "smeared" or degraded region immediately after the event (e.g., from the STFT/mask reacting to the sudden energy change) — IISRT quantifies how long that degraded window lasts before speech quality returns to baseline.

*Evidence from Our Project:* Master doc B.4 (233.45 ms measured) and C.1: "no metric in the reviewed SE literature... measures recovery time after an impulse. You've now actually computed the metric."

*If Evidence Is Missing:* **The precise mathematical definition of IISRT (what threshold defines "recovered," measured against what reference) is not spelled out in either source document provided — confirm the exact definition from your own methodology notes before the jury round, since a jury will likely ask you to define it precisely, not just cite the number.**

*What NOT to Say:* Do not state a confident full-form expansion or precise threshold-based definition unless you've confirmed it from your own methodology documentation — guessing the definition of your own headline metric is a serious credibility risk.

*If the Jury Attacks Again:* "Define 'recovered' precisely — recovered to what threshold, measured against what?"

*Follow-up Answer:* "We should have that precise definition — e.g., time until SI-SNR or some quality proxy returns within X dB of pre-impulse baseline — memorized and ready to state exactly, from our own methodology documentation, before facing the jury. This is exactly the kind of follow-up we should not be caught flat-footed on for our own novel metric."

---

**Q52. What is RSDD, and how is it different from IISRT?**

*Why is the jury asking this?* Tests whether your two novel metrics are actually distinct and both well-defined, or redundant.

*Ideal SIH Answer:* "RSDD (measured at 457.08 ms) is a related but distinct recovery-characterization metric alongside IISRT (233.45 ms) — together they're presented as your novel recovery-time contribution, filling the gap that PESQ/STOI/SI-SDR/DNSMOS don't measure post-impulse recovery time at all."

*Technical Explanation:* Having two related but distinct recovery metrics (e.g., one measuring intelligibility-return-time, another perhaps measuring signal/distortion-decay-time) can be legitimate if they capture genuinely different aspects of recovery — but this needs to be clearly, distinctly defined, or a jury will reasonably ask why you need two numbers for what sounds like one concept.

*Evidence from Our Project:* Master doc B.4 lists both as separate measured values (233.45 ms vs. 457.08 ms) without providing the exact differentiating definition in the documents supplied.

*If Evidence Is Missing:* **The precise, distinct mathematical definitions differentiating RSDD from IISRT are not included in the source documents — this must be confirmed from your own methodology write-up before the jury round.** Presenting two similar-sounding, unexplained numbers back-to-back is a real risk if you can't clearly differentiate them on the spot.

*What NOT to Say:* "IISRT and RSDD basically measure the same thing." If that were true, presenting both as separate "novel metrics" would be double-counting a single contribution — know the real distinction before the jury round.

*If the Jury Attacks Again:* "If these measure basically the same thing, why report both as if they're two separate contributions?"

*Follow-up Answer:* "We should be able to state the precise distinction clearly — [confirm from your methodology docs]. If, on review, they turn out to be measuring very similar things from slightly different angles, we should frame that honestly as 'two views of the same recovery phenomenon' rather than 'two independent novel metrics,' to avoid an inflated novelty claim."

---

**Q53. How reproducible are IISRT and RSDD — could another team replicate your exact numbers?**

*Why is the jury asking this?* Novel, self-defined metrics are inherently vulnerable to a reproducibility challenge.

*Ideal SIH Answer:* "In principle yes, given our exact test set and metric definition — but since these are metrics we defined ourselves (not yet peer-reviewed or externally validated), reproducibility depends entirely on us publishing/documenting the precise definition and using the same held-out test segments."

*Technical Explanation:* A self-defined metric's value as a "novel contribution" is only as strong as its precise, public, reproducible definition — an ambiguous or informally-defined metric is much weaker evidence than a peer-reviewed, standardized one (like PESQ or STOI, which have formal standards).

*Evidence from Our Project:* Master doc C.1 frames IISRT/RSDD as filling a literature gap, but no peer-reviewed validation or external replication is claimed or documented.

*If Evidence Is Missing:* No external validation, peer review, or independent replication of IISRT/RSDD exists — they are self-defined and self-computed metrics at this stage.

*What NOT to Say:* "IISRT and RSDD are established, validated metrics." They are not established in the broader field — they are your team's own proposed contribution, not yet externally validated.

*If the Jury Attacks Again:* "Has anyone outside your team validated or peer-reviewed these metrics?"

*Follow-up Answer:* "No — they're a novel proposal from this project, not yet externally validated or peer-reviewed. That's an honest characterization: 'we propose and report a novel metric with initial results,' not 'this is an established, externally-validated benchmark.'"

---

**Q54. Why should the jury care about recovery time specifically, beyond the initial suppression itself?**

*Why is the jury asking this?* Tests whether you can justify *why* this novel metric matters operationally, not just that it's numerically novel.

*Ideal SIH Answer:* "Because a soldier's ability to hear and understand speech immediately after a gunshot or blast — not just during it — is operationally critical; a system that suppresses the impulse but leaves a long 'dead zone' or distorted region afterward could still cause a soldier to miss a critical radio call right after an engagement, which is arguably the worst possible timing."

*Technical Explanation:* This connects your novel metric directly back to the human-factors justification in Section 1 — recovery time is operationally meaningful because rapid post-event communication (e.g., "man down," "contact right") is often the highest-stakes moment.

*Evidence from Our Project:* This is a reasoned argument connecting Master doc A.2 (human cost/operational context) to C.1 (recovery-time metric definition) — the documents don't explicitly state this operational justification in these words, so present it as your team's reasoning, not a quoted document fact.

*If Evidence Is Missing:* No operational data (e.g., real incident reports of missed post-event communication) directly supports this specific causal claim — it's a logical/human-factors argument, not an empirically demonstrated operational finding.

*What NOT to Say:* "We have evidence that recovery time causes missed communications in real combat." Not demonstrated — this is a plausible operational rationale, not a proven causal finding.

*If the Jury Attacks Again:* "That's speculation. Do you have any real operational evidence recovery time matters this much?"

*Follow-up Answer:* "No direct operational evidence — it's a reasoned justification based on why post-impulse moments are typically high-information-value in combat communication. We present it as the *motivation* for measuring recovery time at all, not as a proven causal/operational finding."

---

## SECTION 10 — REAL-TIME PROCESSING (7 questions)

**Q55. Is your system actually real-time, or do you simply call it real-time?**

*Why is the jury asking this?* The single sharpest, most likely question in the entire round — your own Master doc anticipates it almost verbatim.

*Ideal SIH Answer:* "The neural inference stage — the most compute-heavy part of our pipeline — is measured and proven to fit comfortably within our 10ms real-time budget, at 2.04ms. We have not yet completed full end-to-end (mic-to-speaker) real-time validation including STFT, Bark filterbank, normalization, mask reconstruction, ISTFT, and I2S I/O individually measured and summed. So: partially and honestly proven, not fully validated end-to-end yet."

*Technical Explanation:* "Real-time" for a streaming audio system formally requires that the total per-frame processing time (across every pipeline stage, not just the neural network) stays below the frame's time budget (10ms here) on every single frame, indefinitely, under sustained operation — a claim that requires full-pipeline, sustained-operation measurement, not a single sub-component benchmark.

*Evidence from Our Project:* Master doc B.6, verbatim: "Not yet completed: PC-to-ESP32 numerical validation on real (non-zero) feature vectors, embedded STFT/Bark/normalization/ISTFT, full mic-to-speaker streaming, end-to-end latency and real-time benchmark."

*If Evidence Is Missing:* **Full end-to-end, sustained, mic-to-speaker real-time operation is explicitly not yet proven per your own Master document — despite the PPT's "End-to-End Latency Stability" chart showing a measured 5.399ms mean. This direct contradiction between your two documents must be resolved by your team before the jury round.**

*What NOT to Say:* "Yes, our system is fully real-time end-to-end, proven." This directly contradicts your own Master doc's B.6 section and is the exact "bad answer" example your own prep document warns against.

*If the Jury Attacks Again:* "Your slide deck shows a measured 5.399ms end-to-end latency chart. Your other document says end-to-end latency isn't measured yet. Which is true?"

*Follow-up Answer:* "**This is a genuine inconsistency between our two documents that we need to resolve before presenting** — either the 5.399ms chart reflects a partial-pipeline measurement mislabeled as 'end-to-end,' or the Master doc's B.6 status note is outdated and the full pipeline has since been measured. We should identify which is accurate from our actual test logs and present a single, consistent, correct answer rather than let the jury catch two documents disagreeing."

---

**Q56. What is your worst-case (not average) processing latency?**

*Why is the jury asking this?* Real-time guarantees are about worst-case, not average-case, behavior.

*Ideal SIH Answer:* "For the GRU inference stage specifically, we don't have a separately reported worst-case figure beyond the single 2.04ms measured value — it's presented as a point measurement, not explicitly as a mean with min/max. If the PPT's latency-stability chart (max 5.428ms) is validated as a genuine full-pipeline measurement, that would be our worst-case reference — but per Q55, that chart's scope needs to be confirmed."

*Technical Explanation:* For hard real-time guarantees, the worst-case execution time (WCET) — not the average — determines whether deadlines will ever be missed; a system with excellent average latency but occasional spikes above the frame budget will still produce audible glitches/dropouts.

*Evidence from Our Project:* PPT chart shows "Max 5.428 ms" alongside "Mean 5.399 ms" and "Min 5.379 ms" — but see Q55's flagged inconsistency about whether this chart represents true end-to-end latency.

*If Evidence Is Missing:* No worst-case/max figure is reported for the GRU-inference-only 2.04ms number, and the scope of the max-5.428ms chart figure is in question.

*What NOT to Say:* "Our latency never exceeds 2.04ms." That's presented as a single measured value, not a proven upper bound across many runs/conditions.

*If the Jury Attacks Again:* "How many times did you run this measurement, and under what CPU/thermal conditions?"

*Follow-up Answer:* "We don't have that methodology detail available in our current materials — number of runs, thermal state, concurrent-task load on the MCU during measurement. That's exactly the kind of measurement rigor we should add before making a hard real-time guarantee to a defence jury."

---

**Q57. What happens if a processing deadline is missed during operation?**

*Why is the jury asking this?* Real-time systems must have a defined failure/degradation behavior, not undefined behavior.

*Ideal SIH Answer:* "**This is explicitly not yet defined or tested in our current materials.** We have not specified what happens — audio glitch, dropped frame, buffer underrun, fallback to raw passthrough — if a frame's processing exceeds its 10ms budget. This is a gap we should address, likely by defining a graceful-degradation behavior (e.g., pass through raw audio for that frame) rather than leaving it undefined."

*Technical Explanation:* Real-time embedded audio systems typically need an explicit deadline-miss policy (e.g., skip processing and pass raw audio, or reuse the previous frame's mask) to avoid catastrophic failure (silence, crash, or severe artifact) if a rare compute spike occurs.

*Evidence from Our Project:* Neither document specifies a deadline-miss handling policy — this is a genuine, currently-unaddressed design gap.

*If Evidence Is Missing:* Confirmed — no deadline-miss / overrun handling policy is documented anywhere in either source.

*What NOT to Say:* "We have a robust fallback for missed deadlines." Not documented — do not claim a specific mechanism you haven't actually implemented and can't describe precisely.

*If the Jury Attacks Again:* "So a missed deadline could crash your system or cause dead air on a critical radio call?"

*Follow-up Answer:* "That's a fair worst-case characterization of an undefined behavior, and it's exactly why this needs to be addressed before any deployment claim. A sensible design would default to passing raw (unenhanced) audio through rather than silence on any deadline miss — which is a design principle we should explicitly commit to and implement, not just assert."

---

**Q58. Have you measured CPU load, RAM usage, and flash usage comprehensively — not just the model's own footprint?**

*Why is the jury asking this?* Tests whether your resource-usage claims cover the whole system or just the model.

*Ideal SIH Answer:* "We have solid model-specific numbers — 42,352 bytes flash for the INT8 model, 200KB PSRAM tensor arena, 2.04ms GRU inference time. We do not have comprehensive full-system numbers — total firmware flash usage, total RAM usage across all buffers (I2S DMA, STFT/Bark working buffers, application logic), or sustained CPU utilization percentage under continuous streaming operation."

*Technical Explanation:* The model's own memory/compute footprint is only one part of a full embedded system's resource budget — audio I/O buffers, RTOS overhead, and application logic all consume additional flash/RAM/CPU that must be accounted for to determine true system feasibility, especially on a resource-constrained MCU.

*Evidence from Our Project:* Master doc B.5 gives model-specific numbers only; no full-firmware resource audit is reported in either document.

*If Evidence Is Missing:* Confirmed — full-system flash/RAM/CPU utilization is not reported anywhere in the source materials.

*What NOT to Say:* "Our system easily fits within ESP32-S3's resources." You've shown the model fits — you have not shown the full firmware fits with margin.

*If the Jury Attacks Again:* "What if the full firmware doesn't fit alongside your 200KB tensor arena?"

*Follow-up Answer:* "That's a real, currently-unverified risk — a full-system memory map (all buffers, all libraries, application code, plus the 200KB tensor arena) hasn't been documented. It's a necessary next step before claiming full deployment feasibility, not just model-level feasibility."

---

**Q59. Has power consumption been measured? What's your estimated battery life?**

*Why is the jury asking this?* Deployment-feasibility question directly relevant to a wearable/field device.

*Ideal SIH Answer:* "No — power usage has not been measured yet. This is explicitly listed as an open risk in our own feasibility analysis, with a defined mitigation: run dedicated power-draw tests on the deployed device (mA/mW during inference) to get real battery-life numbers before making any field-deployment claim."

*Technical Explanation:* Battery life for a wearable device depends on average current draw across all operating states (continuous audio capture, continuous inference, radio/BLE if present, display if any) integrated against battery capacity — none of which has been measured here.

*Evidence from Our Project:* PPT feasibility slide, verbatim: "Power usage isn't measured yet — we don't know battery life numbers yet," with the stated strategy "Run dedicated power-draw tests on the deployed device (mA/mW during inference) to get real battery-life numbers before any field-deployment claims."

*If Evidence Is Missing:* Fully confirmed absent — zero power/battery-life data exists for this project currently.

*What NOT to Say:* "It'll easily last a full mission on battery." No basis for this claim whatsoever — do not estimate a specific battery life number without any measured current draw data.

*If the Jury Attacks Again:* "So you can't tell me if this even runs for an hour on a battery?"

*Follow-up Answer:* "Correct, not yet — and we won't guess. Power measurement is a defined, straightforward next step (multimeter/power-profiler current draw during sustained inference), and until it's done, any battery-life number would be pure speculation, which we're not willing to present as fact."

---

**Q60. Does your system operate continuously and reliably over long durations, or has it only been tested on short clips?**

*Why is the jury asking this?* Tests sustained/long-duration robustness, distinct from short-benchmark performance.

*Ideal SIH Answer:* "Our evaluation mixtures are 5 seconds each — we have not tested sustained, continuous, long-duration operation (e.g., a 30-minute continuous stream) on the actual hardware, which could surface issues like memory fragmentation, buffer drift, or thermal throttling that short 5-second clips wouldn't reveal."

*Technical Explanation:* Short-clip testing can miss failure modes that only emerge over sustained operation — memory leaks, floating-point drift in streaming hidden states, DMA buffer desynchronization, or thermal-driven clock throttling on the MCU — all of which are common real embedded-systems issues.

*Evidence from Our Project:* Master doc B.3 confirms all production mixtures are 5-second clips; no long-duration/sustained-operation test is reported anywhere.

*If Evidence Is Missing:* No long-duration continuous-operation test has been performed — confirmed gap.

*What NOT to Say:* "It runs fine continuously, we just haven't formally tested it." Don't assert an untested claim even informally/anecdotally unless you've actually run it and can describe the conditions.

*If the Jury Attacks Again:* "What if there's a memory leak that only shows up after 10 minutes of continuous operation?"

*Follow-up Answer:* "We can't rule that out — we haven't run a long-duration soak test. That's a straightforward, valuable test to add: leave the device running continuously for an extended period and monitor for degradation, crashes, or drift, which 5-second clip testing simply cannot reveal."

---

**Q61. Does your GRU's streaming hidden state stay numerically stable over a long session, or could it drift?**

*Why is the jury asking this?* Deeper technical follow-up specific to recurrent-state models in streaming/embedded contexts.

*Ideal SIH Answer:* "We verified streaming-vs-batch GRU equivalence on the Keras side with a very small numerical difference (max diff 1.49e-7), confirming our streaming-state conversion is mathematically correct at that stage. We have not separately tested for long-session numerical drift specifically on the quantized INT8 on-device version over extended continuous operation."

*Technical Explanation:* INT8 quantization introduces rounding error at each step; over a very long streaming session, small per-step quantization errors in the hidden state *could* in principle accumulate or drift, though GRUs' gating mechanisms (update gate can reset/refresh state) generally provide some natural resistance to unbounded drift — this is a plausible-but-unverified concern, not a proven one.

*Evidence from Our Project:* Master doc B.5: "streaming-state conversion (GRU equivalence verified, max diff 1.49e-7)" — this verification is on the FP32 Keras-side conversion, not the deployed INT8 device over long sessions.

*If Evidence Is Missing:* No long-session INT8 hidden-state drift test has been performed or reported.

*What NOT to Say:* "Our hidden state is mathematically guaranteed never to drift." The 1.49e-7 verification covers a specific conversion check, not a long-duration INT8 drift guarantee.

*If the Jury Attacks Again:* "INT8 quantization error could compound over a long session — how do you know it doesn't?"

*Follow-up Answer:* "We don't know that definitively yet — it's a reasonable technical concern given INT8 rounding at every step. A long-duration on-device test specifically monitoring output quality/stability over time (not just the short-clip mask MAE we currently report) would directly answer this and is a good addition to our validation plan."

---

## SECTION 11 — ESP32-S3 / HARDWARE (7 questions)

**Q62. Show me exactly where audio enters the hardware and where enhanced audio leaves.**

*Why is the jury asking this?* Tests hands-on hardware fluency, not just software/ML knowledge.

*Ideal SIH Answer:* "Audio enters via the INMP441, a digital I2S MEMS microphone, streaming PCM samples over the I2S bus into the ESP32-S3. After processing, enhanced PCM audio is sent back out over I2S to the MAX98357A, a digital I2S Class-D audio amplifier, which drives the speaker or headset directly."

*Technical Explanation:* I2S (Inter-IC Sound) is a digital audio bus protocol using separate clock, word-select, and data lines, avoiding the analog noise/interference issues of analog mic/speaker interfaces — both the INMP441 and MAX98357A are digital I2S peripherals, meaning the entire signal chain from capture to playback is digital except for the final acoustic transduction (speaker) and the mic's own MEMS diaphragm.

*Evidence from Our Project:* PPT block diagram explicitly labels "Audio Input (INMP441 I2S Mic)" and "MAX98357A Amplifier → Speaker/Headset."

*If Evidence Is Missing:* None — this is straightforward, documented hardware wiring.

*What NOT to Say:* Don't confuse I2S with I2C — they are different protocols (I2S for streaming audio data, I2C for control/register configuration) and mixing them up in front of a hardware-literate juror is an easy credibility loss.

*If the Jury Attacks Again:* "How is DMA used in this signal path?"

*Follow-up Answer:* "I2S peripherals on ESP32-S3 typically use DMA (direct memory access) to transfer audio samples between the peripheral and memory buffers without CPU intervention for every sample, freeing the CPU to run the GRU/DSP pipeline while I/O happens in the background — the exact DMA buffer sizes/configuration used in our firmware should be confirmed from our actual code before stating specific numbers."

---

**Q63. Why ESP32-S3 specifically, and not a different MCU (e.g., STM32, a different ESP32 variant)?**

*Why is the jury asking this?* Hardware-selection justification.

*Ideal SIH Answer:* "ESP32-S3 gives us available PSRAM (needed for our 200KB tensor arena), existing TFLite Micro/Espressif tooling support for embedded ML deployment, sufficient compute headroom (our 2.04ms GRU inference against a 10ms budget), and it's low-cost relative to FPGA/DSP-based competitor hardware — which directly supports our cost-feasibility and 'closes the DRDO-DEAL gap' claims."

*Technical Explanation:* Key selection criteria for an edge-ML MCU: available RAM (internal + external PSRAM), flash size, clock speed, availability of an ML inference runtime (TFLite Micro, in this case, with Espressif's own optimized kernels), I2S peripheral support for digital audio, and cost/power for a wearable form factor.

*Evidence from Our Project:* PPT technologies slide lists ESP32-S3 alongside TFLite Micro; Master doc B.5's PSRAM/tensor-arena discussion.

*If Evidence Is Missing:* No documented head-to-head comparison against alternative MCUs (e.g., an STM32 with a comparable ML runtime) was performed — ESP32-S3 appears to be the chosen platform from the start, not the winner of a formal comparison.

*What NOT to Say:* "We benchmarked against 5 other MCUs and ESP32-S3 won." Not documented — don't invent a comparison process you didn't run.

*If the Jury Attacks Again:* "Did you formally evaluate alternative MCUs, or just pick ESP32-S3 because it's popular?"

*Follow-up Answer:* "We didn't run a formal multi-MCU bake-off — ESP32-S3 was selected for its PSRAM availability, mature TFLite Micro tooling, and I2S support, which directly addressed our known requirements. A formal comparison against 1-2 alternatives (e.g., an STM32 with CMSIS-NN) would strengthen this choice with evidence rather than reasonable-but-unvalidated selection criteria."

---

**Q64. What happens if the microphone clips or saturates on a very loud gunshot?**

*Why is the jury asking this?* Directly tests the "front door" failure mode of the entire system — if the mic clips, no downstream processing can recover the lost information.

*Ideal SIH Answer:* "This is an acknowledged, currently-unresolved risk. Our own feasibility analysis states the current INMP441 mic is a 'basic prototype part... not yet loud/rugged enough for real field conditions,' with a stated mitigation strategy of upgrading to a mic rated for high sound-pressure levels so it doesn't distort on loud gunshots."

*Technical Explanation:* ADC/mic clipping is an information-destroying, non-recoverable failure mode — once a signal is clipped (hard-limited at the sensor/ADC level), no downstream software processing, however sophisticated, can reconstruct the lost peak information; this must be solved at the hardware/analog-front-end level (appropriate mic SPL rating, possibly a hardware limiter or AGC before the ADC), not in software.

*Evidence from Our Project:* PPT feasibility slide: "Speaker and mic are basic prototype parts — good enough to test with, but not yet loud/rugged enough for real field conditions," with mitigation "Upgrade to a higher-power speaker and a mic rated for high sound-pressure levels (so it doesn't distort on loud gunshots)."

*If Evidence Is Missing:* No SPL (sound pressure level) rating for the INMP441 is cited, and no gunshot-level (potentially 140+ dB SPL at the source) clipping test has been performed.

*What NOT to Say:* "Our software handles mic clipping gracefully." Software cannot recover information destroyed by hardware-level clipping — this must be solved at the mic/analog-front-end selection level, and you haven't solved it yet.

*If the Jury Attacks Again:* "A real gunshot at close range could be well above 140dB SPL. Is your current mic even rated for that?"

*Follow-up Answer:* "We don't have the INMP441's SPL rating memorized with confidence, and regardless, this is an explicitly open hardware-upgrade item on our own risk list, not something we claim is solved. A high-SPL-rated mic (and possibly a hardware limiter ahead of the ADC) is a defined next step before any real gunshot-proximity claim."

---

**Q65. What DMA/buffering strategy do you use for the I2S audio streams, and how does it interact with your 10ms frame processing budget?**

*Why is the jury asking this?* Deep hardware-integration question — tests whether the audio-I/O side is understood as rigorously as the ML side.

*Ideal SIH Answer:* "**[Confirm exact buffer sizes and DMA configuration from your actual firmware code before the jury round — this level of low-level embedded detail is not specified in either source document, and guessing would be risky.]** Conceptually, I2S DMA buffers need to be sized and double/multi-buffered so that the CPU can process one buffer's worth of audio (feeding our 10ms hop) while DMA fills the next buffer in the background, avoiding audio dropouts."

*Technical Explanation:* A classic embedded-audio pattern is double-buffering (or a small ring buffer): while the DSP/ML pipeline processes buffer A, DMA fills buffer B; when both are ready, they swap — this decouples the (potentially jittery) compute time from the (must-be-steady) audio sample rate, but requires careful sizing so buffer processing time never exceeds buffer fill time.

*Evidence from Our Project:* Not detailed in either document — this is a firmware implementation detail your team should be able to describe from your actual code.

*If Evidence Is Missing:* Specific DMA buffer sizes, number of buffers, and interrupt/task structure are not documented in your source materials.

*What NOT to Say:* Do not invent specific buffer-size numbers under jury pressure — this is exactly the kind of detail a hardware-literate juror can immediately probe further ("why that buffer size specifically?").

*If the Jury Attacks Again:* "If your compute occasionally takes longer than your buffer fill time, what happens to the audio?"

*Follow-up Answer:* "That circles back to our deadline-miss handling gap (see Section 10, Q57) — we don't currently have a documented, defined behavior for this scenario, and it's a genuine open item to resolve with an explicit buffer-overrun/underrun policy."

---

**Q66. How much hardware latency (not algorithmic/compute latency) does your I2S mic-in-to-speaker-out chain add?**

*Why is the jury asking this?* Distinguishes hardware transport latency from the algorithmic/compute latency discussed in Section 10 — often conflated.

*Ideal SIH Answer:* "This has not been separately measured. Our reported latency figures focus on compute (2.04ms GRU inference); the additional hardware-level latency from I2S DMA transfer, buffer depth, and the MAX98357A's own internal processing/DAC delay has not been isolated and measured."

*Technical Explanation:* Even with instant (zero-time) compute, an I2S-based digital audio chain has inherent latency from buffer depth (how many samples must accumulate before a DMA transfer completes) and the DAC/amplifier's own group delay — this is a real, physically-imposed latency floor independent of your algorithm's compute time.

*Evidence from Our Project:* Master doc B.6 lists "full mic-to-speaker streaming, end-to-end latency" as not yet completed — implicitly including this hardware-transport component.

*If Evidence Is Missing:* Confirmed — no isolated hardware-transport latency measurement exists.

*What NOT to Say:* "Hardware latency is negligible." Not measured — don't assume it's small without data; I2S buffer depths can sometimes add several milliseconds depending on configuration.

*If the Jury Attacks Again:* "So your true end-to-end latency could be meaningfully higher than 2.04ms plus your other software stages?"

*Follow-up Answer:* "Yes, that's possible — hardware transport latency is additive to compute latency, and we haven't isolated and measured it yet. It's part of the same full-pipeline end-to-end latency measurement we've identified as our next validation milestone."

---

**Q67. Is your current hardware setup rugged enough for actual field/defence deployment?**

*Why is the jury asking this?* Direct deployment-readiness reality check.

*Ideal SIH Answer:* "No, not yet, and we say this openly. Our current INMP441/MAX98357A/ESP32-S3 setup is explicitly a prototype-grade build — good for proving the algorithm and measuring feasibility, but not rated for field ruggedness (shock, dust, moisture, high-SPL gunfire exposure). Our stated mitigation is upgrading to higher-power/higher-SPL-rated components with better power/wiring design for field ruggedness."

*Technical Explanation:* Field-grade defence hardware typically requires MIL-STD environmental ratings (shock, vibration, ingress protection, temperature range) that consumer-grade prototyping components (like a bare INMP441 breakout board) are not designed or tested to meet.

*Evidence from Our Project:* PPT feasibility slide, verbatim on both speaker/mic ruggedness gaps and the strategy to address them.

*If Evidence Is Missing:* No MIL-STD or IP-rating testing has been performed on any current hardware component.

*What NOT to Say:* "Our current prototype is field-ready." Directly contradicts your own documented risk list.

*If the Jury Attacks Again:* "So this is a lab demo, not a deployable device?"

*Follow-up Answer:* "At this stage, yes — it's a feasibility prototype proving the algorithm and embedded-deployment path work, not a field-hardened product. That's a normal and expected stage for an SIH-stage project; the path to field-grade hardware is a defined (if not yet executed) next phase, not a research uncertainty."

---

## SECTION 12 — V2 IMPULSE DETECTOR (6 questions)

**Q68. What features does your V2 impulse detector actually use, and why these specifically?**

*Why is the jury asking this?* Tests grounding of the detector design in real signal-processing principles.

*Ideal SIH Answer:* "Short-term energy, peak amplitude, crest factor, spectral flux, and high-frequency energy ratio — combined into a detector score compared against an adaptive threshold with hysteresis. These are standard transient-detection primitives with precedent in classical DSP literature, specifically the same underlying principles used in OM-LSA-plus-transient-detector systems."

*Technical Explanation:* Crest factor (peak-to-RMS ratio) spikes sharply during a transient because peak amplitude jumps while short-term RMS hasn't caught up yet; spectral flux (frame-to-frame spectral change) spikes because an impulse's broadband energy differs sharply from the preceding frame's spectrum; high-frequency energy ratio helps distinguish sharp transients (broadband, HF-rich) from low-frequency continuous noise.

*Evidence from Our Project:* Master doc C.3 and PPT's V2 block diagram list exactly these features, with the OM-LSA-plus-transient-detector (Multimedia Tools and Applications, 2020) cited as the classical-DSP lineage.

*If Evidence Is Missing:* No ablation showing which individual feature contributes most to detection accuracy has been reported.

*What NOT to Say:* "We use AI/deep learning for impulse detection too." You don't — the detector is explicitly classical/handcrafted DSP features with a threshold, not a learned model; conflating this with your GRU-based enhancement stage would be a factual error.

*If the Jury Attacks Again:* "Why not use a small learned classifier instead of handcrafted features for detection?"

*Follow-up Answer:* "A learned classifier is a valid alternative and could potentially outperform handcrafted features, but it would add training/data requirements and retraining risk to what we deliberately designed as a simple, tunable, non-learned side-channel — see Q69 on why that design choice was made."

---

**Q69. Why is the detector rule-based/classical rather than another neural network?**

*Why is the jury asking this?* Tests understanding of your own stated design philosophy for V2.

*Ideal SIH Answer:* "Deliberate design choice: a classical, deterministic, tunable detector means V2 doesn't require retraining or touching the frozen, already-validated V1 GRU. The attack-release response is governed by an explicit release-time constant — not buried in opaque learned weights — so it's sweepable and demoable live, and lower-risk to integrate."

*Technical Explanation:* This is a modularity/risk-management argument: coupling a second learned model to the first would create joint-training complexity, potential instability, and harder debugging (is a bad result from V1's GRU or the new detector model?) — a classical, interpretable detector avoids all of that.

*Evidence from Our Project:* PPT innovation slide: "Deterministic, tunable recovery: attack-release response is governed by an explicit release-time constant, not buried in opaque learned weights — sweepable and demoable live," and "Modular V2 design... doesn't retrain or risk the frozen V1 GRU."

*If Evidence Is Missing:* No comparison against a learned-detector alternative has been built or tested — this is a design rationale, not an experimentally-proven superiority claim.

*What NOT to Say:* "A neural detector would definitely perform worse." Not tested — the choice is about risk/interpretability/integration simplicity, not a proven performance advantage over a learned alternative.

*If the Jury Attacks Again:* "Have you tested whether a learned detector would actually perform better?"

*Follow-up Answer:* "No — we chose the classical approach primarily for integration risk and interpretability reasons, not because we proved it's more accurate. A learned-detector comparison is a legitimate future experiment, but wasn't necessary to validate our core modularity design principle."

---

**Q70. What happens if a loud human voice or shout looks like an impulse to your detector — how do you avoid false positives?**

*Why is the jury asking this?* The single most obvious attack vector against any energy/crest-factor-based transient detector.

*Ideal SIH Answer:* "This is a real, currently-untested risk. Our detector uses an adaptive threshold (mean + k×standard-deviation of the detector score) with hysteresis specifically to reduce rapid false-toggling, but we have not reported a measured false-positive rate specifically for loud speech (shouting, sudden vocal onsets) versus true impulsive events."

*Technical Explanation:* A loud shout does share some acoustic characteristics with an impulse — a sudden amplitude onset — though it typically differs in spectral shape (voiced speech has harmonic structure and lower high-frequency energy ratio than a broadband gunshot) and duration (a shout's onset, while sudden, is usually followed by sustained voiced energy, unlike a true impulse's rapid decay) — features your detector does include (HF energy ratio, spectral flux) that should help discriminate, but this discrimination has not been explicitly measured.

*Evidence from Our Project:* Master doc C.3's feature list (which includes HF energy ratio, useful for this discrimination) — but no false-positive-rate-on-speech evaluation is reported anywhere.

*If Evidence Is Missing:* Confirmed absent — no measured false-positive rate on loud speech/shouting exists in either document.

*What NOT to Say:* "Our detector never confuses speech with impulses." Not tested — this is precisely the kind of unproven robustness claim that invites the hardest follow-up question below.

*If the Jury Attacks Again:* "So a soldier shouting a warning could get their voice clipped/attenuated by your own attack-release gain controller?"

*Follow-up Answer:* "That's a real possible failure mode we haven't ruled out with data. It's a high-priority test to run before making any robustness claim: feed the detector a labeled set of shouted/loud speech clips alongside true impulses and measure the false-positive rate directly, rather than relying on the theoretical discrimination our chosen features *should* provide."

---

**Q71. Describe your detector's state machine — IDLE, ONSET, RECOVERY. What triggers each transition?**

*Why is the jury asking this?* Tests understanding of the actual control-flow logic, not just the feature list.

*Ideal SIH Answer:* "Broadly: IDLE is the default state where normal gain (g=1) preserves speech unmodified. An impulse crossing the adaptive attack threshold triggers a transition to an active/attenuation state, applying the attack-release gain controller. As the detector score falls back below a (typically lower, hysteresis-separated) release threshold, the system transitions toward a recovery state, smoothly returning gain to 1 via the exponential release time constant."

*Technical Explanation:* Hysteresis (separate, non-identical attack and release thresholds) prevents rapid toggling right at a single threshold boundary — without it, a detector score hovering near one threshold would cause the gain controller to flicker rapidly between states, causing audible chattering artifacts.

*Evidence from Our Project:* PPT block diagram explicitly shows "IDLE / ONSET / RECOVERY" states and "Hysteresis" as a detector feature; Master doc C.4 describes the exponential attack-release gain formula.

*If Evidence Is Missing:* The exact numeric threshold values and state-transition logic (precise conditions, not just conceptual flow) are not specified in either document.

*What NOT to Say:* Do not state specific numeric threshold values (e.g., a specific k in μ+kσ) unless confirmed from your own implementation — this is exactly the kind of specific-number question a technical juror will drill into.

*If the Jury Attacks Again:* "What is your exact k value in the adaptive threshold formula, and how was it chosen?"

*Follow-up Answer:* "**[Confirm the exact k value and its tuning method — grid search, manual tuning, or literature default — from your own implementation before the jury round.]** We should be able to state this precisely rather than describe the formula only conceptually."

---

**Q72. How would you validate that V2 actually improves on V1, once it's integrated?**

*Why is the jury asking this?* Tests whether you have a rigorous evaluation plan for your next milestone, not just an architecture diagram.

*Ideal SIH Answer:* "Same 3,000 held-out test mixtures used for V1, evaluated three-way: raw noisy / V1-only / V2 (V1+detector+attack-release). We'd track SI-SNR, SI-SNR improvement, STOI, PESQ, plus our impulse-specific metrics — peak attenuation, residual energy ratio, IISRT, RSDD, plus new metrics like speech-hole duration and gain recovery time — and real-time performance (per-frame processing time, end-to-end latency, CPU/memory, under/overruns)."

*Technical Explanation:* A three-way comparison (raw/V1/V2) on the identical test set isolates V2's specific incremental contribution beyond what V1 alone already achieves, which is methodologically stronger than only comparing V2 against raw noisy input.

*Evidence from Our Project:* Master doc C.5, the full V2 evaluation plan.

*If Evidence Is Missing:* This evaluation has not yet been run — V2 evaluation is a documented plan, not yet executed results.

*What NOT to Say:* "V2 already shows improvement over V1." No V2 results are reported in either document — V2 is architecturally designed and offline-prototyped, but the evaluation plan itself has not yet produced reported numbers.

*If the Jury Attacks Again:* "Do you have any preliminary V2 numbers at all, even informal ones?"

*Follow-up Answer:* "Not in our current documentation — V2's evaluation plan is defined and ready to execute, but no results are reported yet in the materials we're presenting from. We should be careful not to imply V2 improvement is already demonstrated."

---

**Q73. Why is smooth exponential attack-release gain better than an instant on/off gate?**

*Why is the jury asking this?* Tests understanding of a specific, deliberate design choice with clear audio-engineering rationale.

*Ideal SIH Answer:* "An instant on/off gate creates abrupt gain discontinuities, which are audibly perceived as clicks/pops — a well-known artifact in audio dynamics processing. Our exponential attack-release (g_t = α·g_{t-1} + (1−α)·g_target, with separate α for attack vs. release) smoothly transitions gain, avoiding that discontinuity, the same principle used in audio compressors/limiters generally."

*Technical Explanation:* This is a first-order IIR (exponential moving average) smoothing filter applied to the gain signal itself; separate attack and release time constants allow fast reaction to an impulse onset (short attack time) while allowing a longer, more natural recovery back to unity gain (longer release time) — mirroring standard dynamics-processor design.

*Evidence from Our Project:* Master doc C.4, the exact gain-smoothing formula and rationale.

*If Evidence Is Missing:* The specific numeric α values for attack vs. release are not given in either document.

*What NOT to Say:* Do not state specific α values unless confirmed from your implementation.

*If the Jury Attacks Again:* "What are your actual attack and release time constants?"

*Follow-up Answer:* "**[Confirm exact numeric attack/release time constants from implementation before the jury round.]** Conceptually, attack should be fast (to suppress the impulse peak quickly) and release should be slower/tunable (to avoid abrupt recovery discontinuities) — and this is exactly the parameter our IISRT/RSDD sweep plot is designed to characterize."

---

## SECTION 13 — SAFETY / FALLBACK (5 questions)

**Q74. What happens when the AI model fails or encounters unseen noise it wasn't trained for?**

*Why is the jury asking this?* Core robustness/failure-mode question for any AI-based safety-adjacent system.

*Ideal SIH Answer:* "**This is currently an open gap, not a solved problem.** Neither document describes an explicit out-of-distribution detection or fallback mechanism if the GRU encounters noise conditions far outside its training distribution — the model will simply produce whatever mask it produces, which could be poor quality, but there's no documented safety net that detects 'this input is unfamiliar, fall back to raw passthrough.'"

*Technical Explanation:* Neural networks generally degrade gracefully-but-unpredictably on out-of-distribution inputs rather than failing with an explicit, detectable error signal — which is exactly why systems intended for safety-relevant contexts typically need an explicit OOD-detection or confidence-estimation mechanism, or a rule-based fallback trigger, layered on top of the neural model.

*Evidence from Our Project:* Your own category-wise weak points (drone+wind −0.05dB, siren+wind −0.16dB) are evidence of exactly this degradation happening on known-hard combinations, without any documented detect-and-fallback mechanism.

*If Evidence Is Missing:* Confirmed — no OOD detection or explicit model-failure fallback mechanism is documented anywhere in either source.

*What NOT to Say:* "Our system detects when it's failing and switches to a safe fallback." Not implemented or documented — do not claim a safety mechanism that doesn't exist yet.

*If the Jury Attacks Again:* "So a soldier has no way of knowing if the AI is currently making things worse?"

*Follow-up Answer:* "Correct, not currently — that's a legitimate safety gap for a defence-context deployment. A future addition — e.g., monitoring output-vs-input energy/correlation in real time and triggering raw-passthrough if the model appears to be degrading the signal — would meaningfully close this gap, but it's not built today."

---

**Q75. What happens when a processing deadline is missed — does the system produce silence, garbage, or pass through raw audio?**

*Why is the jury asking this?* Direct repeat/reinforcement of Q57 from a safety-framing angle — juries deliberately re-ask critical gaps from different angles.

*Ideal SIH Answer:* (See Q57 — same honest answer: this behavior is currently undefined/undocumented, and the sensible design principle would be defaulting to raw passthrough rather than silence.)

*Technical Explanation:* See Q57.

*Evidence from Our Project:* See Q57.

*If Evidence Is Missing:* See Q57.

*What NOT to Say:* See Q57 — do not claim a specific implemented fallback behavior.

*If the Jury Attacks Again:* "You mentioned this gap earlier too — why hasn't it been fixed?"

*Follow-up Answer:* "It's a known, scoped gap on our roadmap, not an oversight we're unaware of — implementing an explicit overrun-handling policy (raw passthrough on missed deadline) is a straightforward firmware addition, and we should prioritize it precisely because it's a safety-relevant gap, not just a performance nicety."

---

**Q76. What happens if the microphone or speaker hardware fails outright during operation?**

*Why is the jury asking this?* Tests hardware-failure-mode thinking, not just software/model failure.

*Ideal SIH Answer:* "**Not explicitly addressed in our current documentation.** There's no described hardware-failure detection (e.g., detecting a disconnected/malfunctioning I2S peripheral) or failover behavior. In a production system, this would typically require I2S bus health monitoring and an alert/failsafe state, which isn't part of our current prototype scope."

*Technical Explanation:* Hardware failure detection for I2S peripherals typically involves monitoring for expected clock/data activity and flagging an error state if the peripheral stops responding as expected — standard embedded-systems robustness practice not yet implemented here.

*Evidence from Our Project:* Not addressed in either document.

*If Evidence Is Missing:* Confirmed — no hardware-failure detection/handling is documented.

*What NOT to Say:* "Our system has hardware failure detection built in." Not true — don't claim this.

*If the Jury Attacks Again:* "So if the mic disconnects mid-mission, the soldier gets silence with no warning?"

*Follow-up Answer:* "Under our current prototype, most likely yes — there's no implemented detection/alerting for that failure mode. That's appropriate scope for a prototype-stage SIH project, but a genuine gap to flag honestly rather than claim is handled."

---

**Q77. What happens if the speaker output saturates or distorts, especially right after impulse suppression is applied?**

*Why is the jury asking this?* Tests output-side safety, complementing the input-side (mic clipping) question in Q64.

*Ideal SIH Answer:* "Our pipeline includes an 'Output Safety' stage — DC blocking, gain/soft limiter, and overlap-add reconstruction — before the signal reaches the MAX98357A amplifier, which is intended to guard against exactly this kind of output-side distortion/saturation."

*Technical Explanation:* A soft limiter caps output amplitude smoothly (avoiding hard-clipping distortion) if the reconstructed signal's amplitude exceeds a safe range; DC blocking removes any DC offset that could otherwise waste headroom or stress the amplifier/speaker.

*Evidence from Our Project:* PPT block diagram explicitly lists "Output Safety: DC Blocking, Gain/Soft Limiter, Overlap-Add" as a pipeline stage before the amplifier.

*If Evidence Is Missing:* No measured test of this safety stage's actual effectiveness (e.g., feeding a deliberately extreme reconstructed signal and confirming the limiter engages correctly) is reported.

*What NOT to Say:* "Output saturation is impossible with our design." A soft limiter reduces risk but its actual effectiveness under extreme conditions hasn't been specifically stress-tested and reported.

*If the Jury Attacks Again:* "Have you specifically tested this limiter under worst-case conditions?"

*Follow-up Answer:* "Not with a documented, dedicated stress test — the limiter is architecturally present in our pipeline, but we haven't reported a specific test confirming its behavior under an extreme worst-case reconstructed signal. That's a good, cheap validation test to add."

---

**Q78. Why should a soldier trust your enhanced audio instead of just the raw signal?**

*Why is the jury asking this?* The deepest trust/adoption question — tests whether you understand this isn't just a technical claim but a human-factors/trust problem.

*Ideal SIH Answer:* "Honestly, at the current prototype stage, we can't yet claim they definitively should in every condition — our own data shows V1 slightly *degrades* quality on some continuous-noise combinations (drone+wind, siren+wind). Where we can make a strong case is the impulsive-noise category specifically: +6.76dB measured SI-SNR improvement, 12.13dB peak attenuation, and a 0.995 clean-speech correlation showing the system doesn't distort speech when there's nothing to suppress. Trust should be built incrementally, category by category, backed by measured evidence — not claimed universally."

*Technical Explanation:* This connects your category-wise honesty (Section 8/D.1) directly to an operational trust argument — a system that's honest about its own operating envelope (where it helps vs. where it doesn't yet) is more deployable and more trustworthy than one that claims universal benefit.

*Evidence from Our Project:* Master doc D.1's category-wise table, directly supporting a scoped, honest trust claim rather than a universal one.

*If Evidence Is Missing:* No actual soldier/end-user trust study or human-factors evaluation has been conducted — this is a technical/data-driven argument, not a validated human-trust finding.

*What NOT to Say:* "Soldiers should always trust our enhanced audio over raw signal." Directly contradicted by your own weak-category data — never claim universal superiority.

*If the Jury Attacks Again:* "So in some conditions, a soldier is actually better off ignoring your system?"

*Follow-up Answer:* "Based on our current V1 data, yes — for drone+wind and siren+wind specifically, our measured average SI-SNRi is slightly negative. We say that proactively because pretending otherwise would be dishonest and, more importantly, operationally dangerous if a soldier trusted the system exactly where it currently underperforms."

---

## SECTION 14 — INNOVATION (5 questions)

**Q79. What exactly is new here? Be specific — not general phrases.**

*Why is the jury asking this?* Forces precision on your innovation claim, the single most commonly over-inflated section of any SIH pitch.

*Ideal SIH Answer:* "Three specific, falsifiable claims: (1) we evaluated a streaming, causal, GRU-based speech-enhancement model specifically against impulsive noise — which, per our literature review, no other reviewed streaming neural SE system has done; (2) we defined and computed two novel post-impulse recovery-time metrics, IISRT (233ms) and RSDD (457ms), which no metric in the reviewed literature (PESQ/STOI/SI-SDR/DNSMOS) measures; (3) our V2 architecture keeps the impulse-detector and attack-release logic as a parallel, non-retraining side-channel to the frozen V1 GRU, with a deterministic, tunable release-time constant rather than an opaque learned parameter."

*Technical Explanation:* Each of these is a scoped, checkable claim rather than a blanket "we invented AI noise cancellation" assertion — which is exactly what makes them defensible under scrutiny.

*Evidence from Our Project:* PPT innovation slide and Master doc C.1.

*If Evidence Is Missing:* These novelty claims are based on your team's own literature review, not an exhaustive systematic survey of all existing work — it's possible an unreviewed paper already does something similar.

*What NOT to Say:* "We invented AI-based noise cancellation" or "nothing like this has ever existed." Both are sweeping overclaims your own literature review doesn't support — GRU-based speech enhancement (RNNoise, DTLN) and complex-ratio masking (Williamson et al.) both pre-date your project; your contribution is the specific *combination and impulsive-noise-specific evaluation*, not the underlying techniques.

*If the Jury Attacks Again:* "GRU + complex mask isn't new — Hasannezhad et al. already did that in 2020. What's actually new?"

*Follow-up Answer:* "Correct, the GRU+CRM architecture itself follows Hasannezhad et al.'s 2020 precedent directly — we don't claim that combination is new. Our novelty claim is specifically the *impulsive-noise-focused evaluation* of that architecture family, the new recovery-time metrics, and the deployed-and-measured ESP32-S3 implementation, not the base neural architecture."

---

**Q80. What has already been done in prior work, and what is genuinely your contribution versus engineering integration?**

*Why is the jury asking this?* Tests whether you can honestly separate "things we built by combining existing ideas" from "things we contributed that didn't exist before."

*Ideal SIH Answer:* "Already done elsewhere: causal GRU speech enhancement (RNNoise, DTLN), complex ratio masking (Williamson et al.), Bark-band feature compression (RNNoise, PercepNet), MCU INT8 quantization for RNNs (Rusci et al.), TFLite Micro deployment (David et al.). Our engineering integration: combining all of these into one working, measured, ESP32-S3-deployed pipeline. Our genuine research-level contribution: the impulsive-noise-specific evaluation methodology and the two novel recovery-time metrics (IISRT/RSDD)."

*Technical Explanation:* This three-way split (prior art / integration / genuine contribution) is exactly the honesty structure a rigorous technical jury is testing for — most successful engineering projects are largely integration of existing techniques, with a smaller, sharply-scoped genuine contribution, and pretending otherwise is both dishonest and easily disproven.

*Evidence from Our Project:* Master doc Part H's full citation list, explicitly organized by "what they support in your actual build" — this structure is already present in your own documentation.

*If Evidence Is Missing:* None — this honest breakdown is well-supported by your own citation-grounded documentation.

*What NOT to Say:* Claiming the entire pipeline (GRU, complex mask, Bark bands, quantization) is your original contribution — it is not; be precise that these are adopted, literature-grounded techniques you integrated and applied to a new evaluation context.

*If the Jury Attacks Again:* "So most of your 'innovation' is really just assembling existing published techniques?"

*Follow-up Answer:* "Largely yes, and we say that with no discomfort — that's how most applied engineering works. Our specific, defensible research contribution is narrower and sharper: the impulsive-noise-focused evaluation and the recovery-time metrics, which is a smaller but real and citable addition to the literature, layered on top of solid engineering integration of proven techniques."

---

**Q81. Can you prove your 'first-of-kind evaluation' claim — that no one else has evaluated a streaming neural SE model against true impulsive noise?**

*Why is the jury asking this?* Directly tests the strongest, most falsifiable novelty claim you've made — a jury member who happens to know of a counter-example will destroy this claim instantly if it's stated as an absolute.

*Ideal SIH Answer:* "We can't prove a universal negative — we can only say that within our literature review (the specific papers cited in our references), we did not find a streaming, causal neural SE model evaluated specifically against true impulsive noise with a recovery-time metric. That's the scope of our claim: 'not found in our reviewed literature,' not 'proven to not exist anywhere.'"

*Technical Explanation:* This is a fundamental epistemic point: absence of evidence in a literature review is not proof of absence in the broader field — a claim of the form "first of its kind" is always scoped to the reviewer's search, not to all human knowledge.

*Evidence from Our Project:* Master doc C.1's explicit phrasing: "No streaming neural SE model in the literature has been evaluated against true impulsive noise" — note this is phrased relative to "the literature" reviewed, and is further supported by the DRDO-DEAL 2026 survey's own independently-stated gap.

*If Evidence Is Missing:* You have not conducted (or cannot claim to have conducted) an exhaustive systematic literature review covering all publication venues and languages.

*What NOT to Say:* "We are definitely the first team in the world to do this." Unfalsifiable overclaim — always scope novelty claims to "within our reviewed literature," strengthened by the independent DRDO-DEAL confirmation.

*If the Jury Attacks Again:* "What if I know of a paper that already did exactly this?"

*Follow-up Answer:* "Then we'd want to see it and would genuinely update our claim — that's the correct scientific response, not defensiveness. Our claim was always scoped to 'not found in our literature review,' independently corroborated by DRDO-DEAL's own 2026 survey stating this exact gap remains underexplored — but we hold that claim provisionally, not as an absolute."

---

**Q82. Why should SIH select this project over an existing, more mature system?**

*Why is the jury asking this?* Direct competitive-justification/selection-criteria question.

*Ideal SIH Answer:* "Because it targets a specifically underserved gap — impulsive-noise handling, with a novel measurement methodology for it — at a fraction of the hardware cost of FPGA/DSP-based alternatives, with a working, measured (though not yet field-hardened) implementation on real ESP32-S3 hardware, aligned with the Atmanirbhar Bharat/Make in India policy push and a live, independently-confirmed (DRDO-DEAL 2026) capability gap."

*Technical Explanation:* N/A — competitive/selection framing.

*Evidence from Our Project:* Synthesis of Master doc Parts A (market/policy), B (V1 results), and C.1 (novelty), all previously discussed.

*If Evidence Is Missing:* No direct, measured comparison against a mature existing system (e.g., an actual PELTOR ComTac unit) has been performed — see Q3.

*What NOT to Say:* "We're already better than every existing system." Not demonstrated — your evidence supports "a promising, low-cost, early-stage alternative with a specific novel focus," not proven superiority.

*If the Jury Attacks Again:* "This all sounds like potential, not proof. Why fund potential over a working, mature competitor?"

*Follow-up Answer:* "Because SIH and schemes like iDEX/ADITI exist precisely to fund early-stage, high-potential indigenous work before it's fully mature — that's the stage we're honestly presenting: promising measured feasibility results with a clear, scoped roadmap (V2/V3) to close the remaining gaps, not a finished, field-proven product."

---

## SECTION 15 — DEFENCE DEPLOYMENT (5 questions)

**Q83. Where would this actually be deployed — what's the realistic use case and environment?**

*Why is the jury asking this?* Tests concreteness of deployment vision beyond "defence communication" as a vague label.

*Ideal SIH Answer:* "Envisioned for tactical headsets/hearing-protection-communication devices used by soldiers and industrial personnel in high-noise environments — combat/training ranges (gunfire, blasts), vehicle/aircraft cabins (engine, rotor noise), and general field operations. This mirrors the exact use case UK MoD's HPCSA programme and Germany/Rheinmetall's SmG headset programme are procuring for."

*Technical Explanation:* N/A — deployment-context framing.

*Evidence from Our Project:* Master doc A.4 (UK MoD HPCSA, Rheinmetall SmG programmes) as external validation of this exact use-case category.

*If Evidence Is Missing:* No actual field trial or deployment in any real defence/industrial setting has occurred — this remains a target use case, not a validated deployment.

*What NOT to Say:* "This is ready to deploy in these environments today." Contradicted by your own hardware-ruggedness and power-measurement gaps (Sections 11/10).

*If the Jury Attacks Again:* "Have you tested in an actual vehicle or firing range?"

*Follow-up Answer:* "No — all testing to date is on synthetic mixtures and lab/desk hardware measurements. Real-environment field testing (vehicle cabin, firing range) is an explicitly identified next step in our own risk/mitigation list, not something we've done yet."

---

**Q84. What happens to your system's performance around vehicle engines and rotorcraft noise specifically?**

*Why is the jury asking this?* Tests a specific, named deployment-relevant noise category from your own weak-point data.

*Ideal SIH Answer:* "Engine+wind combination shows a modest positive improvement in our category-wise data (+0.20dB SI-SNRi) — notably weaker than our impulsive-noise results (+6.76/+6.85dB) but not negative like drone+wind or siren+wind. Pure rotorcraft/rotor noise specifically is not broken out as its own category in our reported results."

*Technical Explanation:* N/A — direct data reporting.

*Evidence from Our Project:* Master doc D.1 category-wise table: Engine+wind +0.20dB.

*If Evidence Is Missing:* No dedicated rotorcraft/helicopter-cabin noise category is reported — your literature review cites a comparable classical-filtering study (Timmermann et al. 2024, FPGA, helicopter cabin) as a reference point, but you have not evaluated your own system on that specific noise type.

*What NOT to Say:* "We've proven this works well in helicopter cabins." Not tested on this specific noise type — don't extrapolate from the general "engine" category to a specific, more extreme rotorcraft cabin environment without data.

*If the Jury Attacks Again:* "Helicopter cabin noise is a well-known especially hostile environment — why no dedicated test for it?"

*Follow-up Answer:* "Fair — we haven't sourced or tested a dedicated rotorcraft-cabin noise dataset specifically; our 'engine' category is more general. Adding a rotorcraft-specific test category, especially since it's a literature-cited hostile environment (Timmermann et al. 2024), would meaningfully strengthen our defence-relevance evidence."

---

**Q85. What about radio-frequency interference or electromagnetic environments common in defence settings — does your hardware handle that?**

*Why is the jury asking this?* Tests awareness of a defence-specific hardware concern beyond pure acoustics.

*Ideal SIH Answer:* "**Not addressed or tested in our current documentation.** Our work to date has focused on the acoustic/ML signal-processing side; RF/EMI hardware robustness (shielding, susceptibility to radio interference from tactical comms equipment operating nearby) has not been evaluated."

*Technical Explanation:* Defence electronics near radio transmitters typically require EMI/EMC (electromagnetic interference/compatibility) design consideration and testing — an entirely separate engineering discipline from the acoustic signal-processing work presented here.

*Evidence from Our Project:* Not addressed in either document.

*If Evidence Is Missing:* Confirmed — zero EMI/RF-interference testing or design consideration is documented.

*What NOT to Say:* "Our system is EMI-hardened." No basis for this claim.

*If the Jury Attacks Again:* "This is a defence communication device — how can you not have considered RF interference at all?"

*Follow-up Answer:* "That's a fair and important gap at the current prototype stage — our focus so far has been proving the core acoustic AI pipeline and embedded feasibility. EMI/EMC hardening is a necessary engineering phase before any real defence deployment, and we should state it explicitly as unaddressed scope rather than imply it's been considered."

---

**Q86. What happens with dust, rain, heat, or other harsh environmental conditions?**

*Why is the jury asking this?* Standard defence-hardware environmental-robustness question.

*Ideal SIH Answer:* "Not tested. Our current hardware is prototype-grade (bare INMP441/MAX98357A/ESP32-S3 components), explicitly not yet rated or tested for environmental ruggedness — this is part of the same hardware-upgrade gap identified in our feasibility analysis (Section 11)."

*Technical Explanation:* Environmental robustness for field electronics typically requires IP-rated enclosures (dust/water ingress protection) and components rated for extended temperature ranges — none of which your current prototype build has been evaluated against.

*Evidence from Our Project:* Consistent with PPT feasibility slide's hardware-ruggedness gap discussion.

*If Evidence Is Missing:* Confirmed — no environmental testing (IP rating, temperature range, humidity) has been performed.

*What NOT to Say:* "Our system is rated for field conditions." Not tested or rated.

*If the Jury Attacks Again:* "So this can't leave a lab or clean demo room right now?"

*Follow-up Answer:* "Correctly characterized — at this stage it's a lab/desk-validated feasibility prototype. Environmental hardening (enclosure design, component re-selection for temperature/ingress rating) is a defined, standard next phase of hardware engineering, not yet undertaken."

---

**Q87. If this system fails during an actual mission, what's the consequence, and how is that risk managed?**

*Why is the jury asking this?* Highest-stakes deployment question — tests whether you've thought about mission-criticality risk management, not just algorithm performance.

*Ideal SIH Answer:* "Honestly, our current design does not have a fully worked-out failure-management story — no OOD detection, no defined deadline-miss behavior, no hardware-failure detection (Sections 10/13). Until those are built, the responsible operational stance would be: deploy this as a supplementary hearing-protection/communication aid alongside, not as a sole replacement for, existing standard-issue equipment, until field-validated failure handling is in place."

*Technical Explanation:* N/A — risk-management/deployment-policy framing, directly synthesizing the open gaps already identified across Sections 10 and 13.

*Evidence from Our Project:* Synthesis of Master doc B.6 and PPT feasibility risks.

*If Evidence Is Missing:* No mission-scenario failure analysis or formal risk assessment (e.g., an FMEA — failure modes and effects analysis) has been conducted for this system.

*What NOT to Say:* "Our system is fail-safe and mission-ready." Directly contradicted by every open gap identified throughout this document.

*If the Jury Attacks Again:* "So you're telling a defence jury this isn't safe to rely on for a real mission yet?"

*Follow-up Answer:* "At this SIH prototype stage, correct — and that's the honest, professionally responsible answer, not a weakness to hide. A defined path to mission-readiness — failure-mode handling, environmental hardening, real-world validation — is exactly what further development funding (iDEX/ADITI) would be used for; presenting this as already mission-ready would be both false and, frankly, dangerous given the stakes involved."

---

# SOUL CRUSHER — QUESTIONS DESIGNED TO BREAK THE TEAM

**SC1. Your slide deck says end-to-end latency is measured at 5.399ms mean. Your other document says end-to-end latency has NOT been measured. Which one is a lie?**

*Why asked:* Direct cross-document contradiction — the hardest, most embarrassing possible catch.

*Ideal Answer:* "Neither document is dishonest intentionally, but this is a real inconsistency we need to resolve, and we take responsibility for not catching it before this round. We will confirm from our actual test logs which is accurate and present one consistent number to you, rather than defend both."

*Technical Explanation:* Most plausible resolution: the 5.399ms chart may represent a *partial*-pipeline measurement (e.g., GRU + a subset of stages) mislabeled as "end-to-end" on the slide, while the Master doc's more detailed B.6 status section is the more carefully-scoped, accurate status. But this must be verified, not assumed.

*Evidence:* Both documents as supplied.

*If Evidence Is Missing:* The true resolution requires checking actual test/measurement logs, not available in the documents themselves.

*Weak answer:* Picking whichever number sounds better and asserting it confidently without acknowledging the contradiction — a technical juror who has read both documents will immediately catch this as evasion.

*Follow-up attack:* "This makes me doubt every other number in your presentation. Why should I trust your 6.76dB figure either?"

*Follow-up Answer:* "That's a reasonable reaction, and it's exactly why we need to resolve this before presenting — every other number (SI-SNRi, IISRT, RSDD, parameter counts) traces to a specific measured/documented source in our Master doc and we're confident in them individually, but we understand one unresolved contradiction reasonably makes you want the whole set re-verified. We'd rather over-verify than have you doubt correct numbers because of one inconsistent one."

---

**SC2. Your headline SI-SNR improvement is quoted as both +6.76dB and +6.85dB for the impulsive category in your two documents. Which is correct?**

*Why asked:* Second cross-document numerical inconsistency, testing whether you'll notice and handle it honestly.

*Ideal Answer:* "We need to verify which figure is correct from our underlying results — this is likely a rounding/reporting discrepancy between two write-ups of the same underlying result, but we should confirm and use one consistent number rather than let two different values appear."

*Technical Explanation:* Small discrepancies like this often arise from computing the same metric slightly differently (e.g., mean over slightly different test subsets, or a typo in one document) — neither is necessarily "wrong" in a deceptive sense, but presenting both without reconciling them undermines credibility.

*Evidence:* PPT headline number (+6.76dB) vs. Master doc D.1 category table (+6.85dB).

*If Evidence Is Missing:* Root cause of the discrepancy is not determinable from the documents alone.

*Weak answer:* "It doesn't matter, they're close enough." A jury testing rigor will not accept "close enough" for a headline number.

*Follow-up attack:* "A 0.09dB discrepancy in your single most-quoted number — how many other small errors exist that we haven't caught?"

*Follow-up Answer:* "We can't know that without a full audit, and we should do one before presenting — check every headline number in our slides against its source computation in our Master doc/results files, and fix every discrepancy before the jury round, not just this one."

---

**SC3. What exactly have you proven, and what are you merely proposing?**

*Ideal Answer:* "Proven/measured: the V1 architecture trained and evaluated on 3,000 held-out synthetic test mixtures (+6.76dB impulsive SI-SNRi, 12.13dB peak attenuation, 0.995 clean-speech correlation, IISRT 233ms, RSDD 457ms); quantized and deployed to real ESP32-S3 hardware with measured 2.04ms GRU inference within a 10ms budget. Proposed/planned, not proven: V2 (impulse detector + attack-release) on-chip integration and evaluation; V3 continuous-noise improvement; full end-to-end real-time validation; power/battery measurement; field/environmental hardening; real (non-synthetic) noise validation."

*Follow-up attack:* "That's a short proven list and a long proposed list."

*Follow-up Answer:* "Yes — that's an honest reflection of an early-stage SIH prototype. The proven list demonstrates core technical feasibility (the hardest, most uncertain part); the proposed list is scoped, concrete engineering work, not open research risk."

---

**SC4. Give me one reason to reject your project.**

*Ideal Answer:* "The strongest reason to be cautious: nearly all of our quantitative evidence comes from synthetic, simulated training/test data — we have not yet validated against real gunshots, real field recordings, or real deployment conditions, and our own weak-category data (drone+wind, siren+wind) shows the system can actively degrade performance on some real, defence-relevant noise combinations today."

*Follow-up attack:* "So why should we fund this over a project with real-world validated results?"

*Follow-up Answer:* "Because SIH-stage funding is precisely meant to bridge from promising, rigorously-measured feasibility (which we have) to real-world validation (which we've clearly scoped as the next step) — we're asking for the resources to close exactly the gap you've just identified, not claiming it's already closed."

---

**SC5. What is the weakest claim in your presentation?**

*Ideal Answer:* "Our 'first-of-kind evaluation' and novel-metric claims (IISRT/RSDD) are the weakest in the sense that they rest on our own literature review being comprehensive, and on metric definitions that haven't been externally peer-reviewed or validated — they're the most defensible technically but the most vulnerable to an external 'actually, X already did this' counter-example."

*Follow-up attack:* "So your headline 'innovation' slide is your shakiest ground?"

*Follow-up Answer:* "In terms of novelty-claim vulnerability, yes — though our measured V1 results and embedded deployment numbers underneath that novelty framing are solid regardless of how the novelty claim itself holds up under scrutiny."

---

**SC6. Which result would you remove if I asked you to remove one?**

*Ideal Answer:* "The PPT's 'End-to-End Latency Stability' chart (5.399ms mean) — given the direct contradiction with our Master doc's explicit statement that full end-to-end latency isn't yet measured, that chart is currently our least defensible piece of evidence and should be removed or re-labeled precisely until we confirm what it actually measures."

*Follow-up attack:* "If you'd remove it, why did you include it in the first place?"

*Follow-up Answer:* "That's a fair challenge to ourselves — it likely happened because two team members or two work sessions produced slightly inconsistent framings of a partial measurement, and it wasn't caught in review. That's a process failure to fix, and we're fixing it now by flagging and correcting it before presenting to you."

---

**SC7. Why should I believe your synthetic dataset produces trustworthy results?**

*Ideal Answer:* "You shouldn't fully believe it generalizes to real conditions yet — we don't claim that. Synthetic mixtures let us do rigorous, scaled, ground-truth-labeled evaluation (26,000 mixtures, proper train/val/test splitting, session-level leakage control for drone data), which is methodologically sound *for what it measures* — relative model behavior under controlled, known conditions — but it is not a substitute for real-world validation, which remains undone."

*Follow-up attack:* "Then your entire results section is unproven for real use."

*Follow-up Answer:* "It's proven for the conditions we tested — synthetic mixtures constructed from real speech and real (if civilian-sourced) noise recordings — and unproven for real deployed-hardware-in-real-environment conditions. That's a precise, defensible scope, not a blanket 'unproven' dismissal."

---

**SC8. What happens when your AI is completely wrong?**

*Ideal Answer:* "See Section 13 (Q74) — there is currently no explicit detect-and-fallback mechanism for a badly-wrong model output; the system would simply output whatever the GRU produces, which our own data shows can be a slight net degradation in specific noise categories (drone+wind, siren+wind)."

*Follow-up attack:* "That sounds dangerous for a defence device."

*Follow-up Answer:* "It would be, for a fielded product — which is exactly why we're not claiming field-readiness. An output-quality monitor with automatic raw-passthrough fallback is a concrete, buildable addition identified as necessary before any real deployment claim."

---

**SC9. Why shouldn't we simply use a traditional DSP system instead of AI here?**

*Ideal Answer:* "For stationary noise, a traditional DSP system might perform comparably at much lower complexity — we're not claiming AI is necessary everywhere. Our specific argument is that impulsive, non-stationary noise is where classical adaptive-filter assumptions (slowly-varying noise statistics) break down, and a learned model can capture more complex, data-driven speech/noise structure than a hand-tuned classical filter for that specific hard case."

*Follow-up attack:* "Have you actually benchmarked against a classical DSP baseline (e.g., spectral subtraction) on the same test set?"

*Follow-up Answer:* "No, we haven't run that direct comparison — that's a legitimate, currently-missing piece of evidence. A classical-baseline comparison on our exact 3,000-clip test set would make the 'AI is necessary here, not just nice-to-have' argument evidence-based rather than assumed."

---

**SC10. What is genuinely novel here versus just re-implementing existing papers?**

*(See Q79/Q80/Q81 — same core answer: architecture is literature-adopted; genuine novelty is the impulsive-noise-specific evaluation and the IISRT/RSDD metrics, scoped honestly to your own literature review, not claimed as absolute/universal firsts.)*

---

**SC11. If I remove your AI model entirely, what remains?**

*Ideal Answer:* "The signal-acquisition hardware chain (INMP441 → ESP32-S3 → MAX98357A), the STFT/ISTFT framework, and — if V2 is integrated — the classical DSP-based impulse detector and attack-release gain controller, which don't depend on the GRU at all. Without the GRU, you'd have something closer to a classical impulse-limiter system with no learned noise suppression."

*Follow-up attack:* "So your classical detector alone might handle a lot of the impulsive-noise use case without needing the AI model at all?"

*Follow-up Answer:* "That's a genuinely interesting question we haven't directly tested — an attack-release limiter alone (no GRU) would likely reduce impulse peak amplitude but wouldn't provide the learned, speech-preserving spectral reconstruction the GRU+complex-mask stage provides, and V1's SI-SNR/clean-speech-correlation results are specifically attributable to the learned model. But a 'detector+limiter only, no GRU' baseline comparison is something we haven't run and would clarify exactly how much the GRU specifically contributes."

---

**SC12. If I remove your DSP/signal-processing pipeline (STFT/Bark/ISTFT), what remains?**

*Ideal Answer:* "Nothing usable — the GRU operates on Bark-band features derived from the STFT, and its complex-mask output must be interpolated and applied via ISTFT to produce playable audio. The DSP pipeline isn't optional scaffolding around the AI model — it's the feature-extraction and reconstruction machinery the AI model fundamentally depends on."

*Follow-up attack:* "So really the 'AI' is a small piece embedded in a larger classical DSP system?"

*Follow-up Answer:* "That's an accurate and fair characterization — the GRU is 23,980 parameters operating within a much larger classical signal-processing framework (STFT, Bark filterbank, ISTFT, and in V2, a classical detector too). We wouldn't push back on that framing; it's precisely how most practical real-time neural audio systems are actually built."

---

**SC13. You claim real-time. Prove it.**

*(See Q55 — the sharpest, most central question in this entire document. Same honest answer: GRU-inference-stage proven at 2.04ms/10ms budget; full end-to-end pipeline not yet proven; the 5.399ms chart's scope must be resolved and reconciled with the Master doc's B.6 status before presenting.)*

---

**SC14. You claim defence applicability. Where is your defence validation?**

*Ideal Answer:* "We have no direct defence-organization validation, trial, or endorsement. What we have is: independent confirmation of the underlying capability gap from a DRDO-DEAL-co-authored 2026 Defence Science Journal paper (Narain, Kant, Singh), and market/policy evidence (UK MoD HPCSA, Germany/Rheinmetall SmG procurement) that this exact problem category is being actively funded by defence organizations internationally. Neither of those is a validation of *our specific system* by any defence organization — that distinction matters and we should be precise about it."

*Follow-up attack:* "So you're citing a paper about the problem existing, not validation that your solution works?"

*Follow-up Answer:* "Correct — that's an important, precise distinction. The DRDO-DEAL citation validates that the *gap* we're targeting is real and underexplored, per an independent, defence-affiliated source. It does not, and we won't claim it does, validate that our specific ImpulseGuard system is itself proven or endorsed by any defence body."

---

**SC15. What happens outside your training distribution?**

*(See Q39/Q74 — same honest answer: not tested, no explicit OOD detection or fallback mechanism exists yet; behavior is unpredictable/unverified for genuinely out-of-distribution inputs like real military-grade blast overpressure.)*

---

**SC16. What is your worst-case latency?**

*(See Q56 — no separately reported worst-case/max figure exists for the GRU-inference-only measurement beyond the single 2.04ms point value; the PPT chart's max-5.428ms figure's scope must be resolved per SC1 before it can be cited with confidence as a worst-case reference.)*

---

**SC17. What is the worst failure mode of your entire system?**

*Ideal Answer:* "Arguably the combination of (a) no deadline-miss handling and (b) no hardware-failure/OOD detection — meaning a soldier could, in the worst case, experience dead air or degraded audio with zero warning at exactly the moment (post-impulse, high-stress) when clear communication matters most, with no fallback to raw passthrough currently implemented."

*Follow-up attack:* "That sounds like the whole safety case is currently undefined."

*Follow-up Answer:* "At the current prototype stage, yes, substantially undefined — and we're not going to pretend otherwise. It's the clearest, most important item on our own next-steps list, precisely because it's the highest-consequence gap, not just a nice-to-have."

---

**SC18. If your system fails during a live mission, what happens — walk me through it.**

*Ideal Answer:* "With current firmware behavior undocumented for this scenario, the honest answer is: we don't know precisely, because the failure-handling behavior (deadline miss, hardware fault, OOD input) isn't yet defined or tested. The responsible interim answer is that this system should not be relied upon as a sole communication channel in an actual mission until that failure-handling story is built and validated — it's a prototype demonstrating algorithmic and embedded feasibility, not a mission-ready safety-critical device."

*Follow-up attack:* "Then why present this to a defence hackathon at all?"

*Follow-up Answer:* "Because SIH evaluates feasibility, innovation, and roadmap potential at an early stage, not finished, field-certified products — and we believe the measured algorithmic feasibility (impulsive-noise-specific improvement, real hardware deployment within budget) is strong enough evidence to justify continued development funding toward closing exactly the safety/robustness gaps we've been transparent about throughout this Q&A."

---

**SC19. Why should I trust anything in this presentation given the inconsistencies we've found today?**

*Ideal Answer:* "Because we're not defending the inconsistencies — we're acknowledging them directly, explaining our best understanding of their likely cause, and committing to resolve them with our actual underlying data before making any final claims to you. Every other number in this document traces to a specific, documented measurement or dataset statistic, most of which we've been able to explain in technical depth throughout this session — the inconsistencies are a documentation/consistency-check failure on our part, not evidence of fabricated or unreliable underlying results."

*Follow-up attack:* "That's exactly what a team with fabricated results would also say."

*Follow-up Answer:* "That's fair, and we can't fully resolve that suspicion with words alone in this room — what we can offer is full access to our raw training logs, evaluation scripts, and measurement data for independent verification, which a team presenting fabricated numbers would be far less willing or able to provide convincingly."

---

**SC20. In one sentence, what is the single biggest risk that this entire project doesn't actually work as claimed?**

*Ideal Answer:* "That our results are entirely from synthetic, simulated data and a full sim-to-real transfer to genuine military-grade impulsive noise — and full end-to-end, sustained, real-hardware real-time operation — has not yet been demonstrated."

*Follow-up attack:* "That's basically everything that matters. What DOES work, then?"

*Follow-up Answer:* "What's genuinely demonstrated: the model trains and generalizes on a large, properly-split synthetic dataset; it measurably and specifically improves impulsive-noise segments over raw input; it quantizes and runs on real ESP32-S3 hardware within its neural-inference compute budget; and it doesn't distort clean speech. That's real, measured technical feasibility — the sim-to-real and full-system-integration gap is the next, well-defined phase of work, not evidence the core idea is broken."

---

# TOP 20 QUESTIONS TO MEMORIZE

For each: a 10-second, 30-second, and 60-second answer.

**1. Is your system actually real-time end-to-end?**
- *10-sec:* "GRU inference is measured at 2.04ms within a 10ms budget; full end-to-end mic-to-speaker latency is not yet fully validated — that's our next milestone."
- *30-sec:* Add: the pipeline stages not yet individually measured (STFT, Bark, normalization, mask reconstruction, ISTFT, I2S I/O), and the ~8ms of remaining headroom in the budget.
- *60-sec:* Add: acknowledge and resolve the PPT-vs-Master-doc latency-chart discrepancy (SC1) proactively, state the plan to confirm from raw logs.

**2. Why should I believe your synthetic dataset?**
- *10-sec:* "It gives us rigorous, scaled, ground-truth evaluation, but sim-to-real transfer to genuine military noise is not yet validated — that's an explicit next step, not a hidden gap."
- *30-sec:* Add: dataset composition (LibriSpeech, MUSAN, UrbanSound8K, drone noise), session-level leakage control for drone data.
- *60-sec:* Add: the full mitigation plan (collect/test on real recorded gunshots/drone/vehicle audio) and why small-model data-sufficiency reasoning applies.

**3. Why GRU over LSTM/CNN/Transformer?**
- *10-sec:* "GRU wins the accuracy/memory/parameter trade-off for complex-mask estimation under non-stationary noise, per Hasannezhad et al. 2020, and fits our MCU budget."
- *30-sec:* Add: fewer gates than LSTM = fewer params; causal requirement rules out non-causal Transformers/BLSTMs for real-time streaming.
- *60-sec:* Add: acknowledge no in-house LSTM/CNN baseline was trained for direct comparison — this is literature-grounded, not self-benchmarked.

**4. What exactly is new here?**
- *10-sec:* "Impulsive-noise-specific evaluation of a streaming causal GRU model, two novel recovery-time metrics (IISRT/RSDD), and a modular non-retraining V2 detector design."
- *30-sec:* Add: GRU+CRM architecture itself is adopted from Hasannezhad et al. 2020 — the novelty is evaluation focus and metrics, not the base architecture.
- *60-sec:* Add: DRDO-DEAL 2026's independent confirmation of this exact gap; scope the claim to "not found in our reviewed literature," not an absolute first.

**5. What is IISRT and RSDD, precisely?**
- *10-sec:* "Two novel post-impulse recovery-time metrics we defined — measured at 233ms and 457ms — filling a gap since PESQ/STOI/SI-SDR/DNSMOS don't measure recovery time."
- *30-sec:* Add: why recovery time matters operationally (post-event communication is high-stakes).
- *60-sec:* **Confirm and state the exact mathematical definitions from your own methodology notes** — this is the single most important thing to nail down before the jury round.

**6. Why 22 Bark bands specifically?**
- *10-sec:* "Standard range following RNNoise's precedent — balances GRU parameter count against retained frequency detail."
- *30-sec:* Add: 22 bands → 44 features → 23,980 total params, INT8 mask MAE 0.0202, within the Rusci et al. 2022 benchmark.
- *60-sec:* Add: acknowledge no direct band-count sweep was run — 22 is precedent-based, not self-optimized.

**7. What's your headline SI-SNR improvement?**
- *10-sec:* "+6.76dB on impulsive segments, +1.17dB on non-impulsive, measured on 3,000 held-out test mixtures."
- *30-sec:* Add: 12.13dB peak attenuation, 0.995 clean-speech control correlation.
- *60-sec:* Add: proactively flag and resolve the +6.76 vs +6.85dB documentation inconsistency (SC2).

**8. What are your weakest noise categories?**
- *10-sec:* "Drone+wind (−0.05dB) and siren+wind (−0.16dB) — both continuous-noise combinations, our system slightly underperforms there today."
- *30-sec:* Add: engine+wind is only marginally positive (+0.20dB); impulsive and wind+impulsive are our strongest (+6.85dB, +7.53dB).
- *60-sec:* Add: V3's explicit, scoped plan (hard-example oversampling first, richer features second) to address this.

**9. What is your model size and inference time?**
- *10-sec:* "23,980 parameters, 42KB INT8 model, 2.04ms measured GRU inference on real ESP32-S3 silicon against a 10ms budget."
- *30-sec:* Add: 200KB PSRAM tensor arena required; quantization mask MAE 0.0202/max 0.096.
- *60-sec:* Add: comparison to DeepFilterNet2's RTF 0.42 on Raspberry Pi 4 vs. your ~0.204 RTF for the GRU stage alone on much weaker hardware.

**10. Why complex mask instead of magnitude-only?**
- *10-sec:* "Complex masks also correct phase distortion, which magnitude-only masks leave uncorrected — important for our low-SNR, transient-heavy impulsive use case."
- *30-sec:* Add: Williamson, Wang, Wang (2016) theoretical basis; 22 real + 22 imaginary mask outputs.
- *60-sec:* Add: acknowledge no magnitude-only-mask ablation was run to isolate exactly how much this specific choice contributed.

**11. Have you measured power consumption / battery life?**
- *10-sec:* "No — explicitly not yet measured. It's an open, acknowledged gap with a defined next-step mitigation plan."
- *30-sec:* Add: the plan (dedicated mA/mW power-draw tests during inference on the deployed device).
- *60-sec:* Add: why this matters for wearable-device feasibility claims and why you won't guess a number.

**12. Is your hardware field-rugged?**
- *10-sec:* "No — current INMP441/MAX98357A components are basic prototype parts, not rated for field conditions."
- *30-sec:* Add: the upgrade plan (higher-SPL-rated mic, higher-power speaker, better power/wiring design).
- *60-sec:* Add: connect to mic-clipping risk on loud gunshots (Q64) and environmental (dust/rain/heat) untested status (Q86).

**13. What happens if the AI model fails or encounters unfamiliar noise?**
- *10-sec:* "Currently undefined — no OOD detection or automatic fallback to raw passthrough exists yet."
- *30-sec:* Add: connects to your own weak-category data (drone+wind, siren+wind showing measured degradation).
- *60-sec:* Add: the proposed mitigation (output-quality monitoring triggering raw passthrough) and why it's not yet built.

**14. Is V2 (impulse detector) actually working, or just an idea?**
- *10-sec:* "Designed and evaluated offline in Python on a PC — not yet ported to or running on the ESP32 firmware."
- *30-sec:* Add: why it's architecturally low-risk to port (classical DSP, doesn't touch/retrain the frozen V1 GRU).
- *60-sec:* Add: the full V2 evaluation plan (three-way comparison, expanded metrics) that hasn't produced results yet.

**15. What's your dataset size and split methodology?**
- *10-sec:* "79.51 hours total, 26,000 production mixtures, split 20k train / 3k val / 3k test."
- *30-sec:* Add: session-level leakage prevention for the drone-noise subset specifically.
- *60-sec:* Add: acknowledge the same rigor isn't explicitly confirmed/documented for non-drone dataset components.

**16. Why should SIH/a defence jury select this over a mature existing product?**
- *10-sec:* "It targets a specific, independently-confirmed (DRDO-DEAL 2026) underserved gap — impulsive noise — at far lower hardware cost than FPGA/DSP competitors."
- *30-sec:* Add: policy alignment (Atmanirbhar Bharat, iDEX/ADITI), current SOF Week 2026 equipment-gap context.
- *60-sec:* Add: honest framing — this is early-stage, funding-appropriate potential, not a finished mature competitor to PELTOR/QUIETPRO today.

**17. What's your worst-case / most dangerous unproven claim?**
- *10-sec:* "That the system works reliably on real (non-synthetic) military-grade impulsive noise and in full end-to-end real-time operation — neither is yet proven."
- *30-sec:* Add: both are explicitly scoped as next validation milestones in your own materials, not hidden gaps.
- *60-sec:* Add: your team's discipline around the Safe-vs-Avoid claims list (Part F) as evidence of proactive honesty.

**18. Why 20ms frame / 10ms hop specifically?**
- *10-sec:* "Standard across RNNoise/DTLN/CRN — balances frequency resolution, algorithmic latency, and MCU compute budget."
- *30-sec:* Add: FFT size (512-pt) chosen to match frame length and avoid zero-padding.
- *60-sec:* Add: acknowledge no in-house sweep (e.g., 10ms/5ms) was run to empirically validate this is optimal for your specific dataset.

**19. What happens with a loud human shout — could your V2 detector false-trigger?**
- *10-sec:* "A real, currently-untested risk — no measured false-positive rate on loud speech vs. true impulses exists yet."
- *30-sec:* Add: the detector's HF-energy-ratio and spectral-flux features should theoretically help discriminate, but this hasn't been validated.
- *60-sec:* Add: the concrete test needed (labeled shout/loud-speech clips vs. true impulses, measuring false-positive rate) before making a robustness claim.

**20. Give me one reason to reject this project.**
- *10-sec:* "Nearly all quantitative evidence is from synthetic data, with no real-world/field validation yet — that's the single biggest unproven leap."
- *30-sec:* Add: your own weak-category data shows active degradation on some real, relevant noise types today (drone+wind, siren+wind).
- *60-sec:* Add: reframe — this is exactly the gap SIH-stage funding exists to help close, not a disqualifying flaw at this project stage.

---

# TOP 10 QUESTIONS MOST LIKELY TO DESTROY YOU

1. **"Your PPT and Master doc disagree on end-to-end latency (5.399ms measured vs. not-yet-measured) — which is true?"**
 *Why dangerous:* Direct, provable cross-document contradiction on your single most important real-time claim.
 *Missing evidence:* Raw test logs needed to determine which figure (or neither) is accurate.
 *How to answer:* Acknowledge immediately, don't defend both; commit to verifying from source data before the jury round.
 *Strengthening experiment:* Run and log an actual full mic-to-speaker end-to-end latency measurement on hardware before presenting, resolving the ambiguity with real data.

2. **"Your two documents also disagree on the headline impulsive SI-SNR number (+6.76dB vs +6.85dB) — which is correct?"**
 *Why dangerous:* A second numerical inconsistency erodes trust in every other number.
 *Missing evidence:* Source computation/results file for the definitive value.
 *How to answer:* Same as above — verify, don't guess or dismiss as "close enough."
 *Strengthening experiment:* Full audit of every headline number in your slides against its source computation.

3. **"Prove your system is real-time, not just call it real-time."**
 *Why dangerous:* Your own Master doc explicitly anticipates this as the hardest question and states the honest limitation.
 *Missing evidence:* Full-pipeline (STFT+Bark+GRU+mask+ISTFT+I2S I/O) sustained latency measurement.
 *How to answer:* State exactly what IS measured (2.04ms GRU stage) vs. NOT (full pipeline), per B.6.
 *Strengthening experiment:* Complete the full mic-to-speaker latency benchmark — your own document already identifies this as the immediate next milestone.

4. **"Why should I believe your synthetic dataset generalizes to real military noise?"**
 *Why dangerous:* Sim-to-real gap is a fundamental, well-known ML weakness, and it's explicitly unaddressed here.
 *Missing evidence:* Any real (non-synthetic) noisy-speech test.
 *How to answer:* Full honesty per Q39 — acknowledge, cite your own stated mitigation plan.
 *Strengthening experiment:* Collect even a small set of real recorded gunshot/blast/drone audio and re-run evaluation on it.

5. **"Your detector could false-trigger on a soldier shouting — have you tested that?"**
 *Why dangerous:* An obvious, easily-imagined real failure scenario with zero supporting data.
 *Missing evidence:* False-positive-rate measurement on loud speech vs. true impulses.
 *How to answer:* Acknowledge directly per Q70 — describe why your features should theoretically help but admit it's untested.
 *Strengthening experiment:* Build a labeled loud-speech/shout test set and measure the detector's false-positive rate directly.

6. **"What happens to a soldier's audio if your system misses a processing deadline or the AI is simply wrong — walk me through the failure."**
 *Why dangerous:* No defined fallback behavior exists for either failure mode — this is a genuine open safety gap.
 *Missing evidence:* Any documented deadline-miss or OOD-detection/fallback policy.
 *How to answer:* Full honesty per Q57/Q74 — state this is undefined and identify the sensible fix (raw passthrough).
 *Strengthening experiment:* Implement and test an explicit raw-passthrough fallback for both deadline-miss and low-confidence-output scenarios.

7. **"Has power/battery life been measured at all?"**
 *Why dangerous:* Basic deployment-feasibility question with a flat "no" answer and zero data.
 *Missing evidence:* Any current-draw (mA/mW) measurement.
 *How to answer:* Direct, honest per Q59 — state the gap and your defined mitigation plan.
 *Strengthening experiment:* A single afternoon with a multimeter/power profiler would close this gap significantly before the jury round.

8. **"Is your mic even rated to survive a close-range gunshot without clipping?"**
 *Why dangerous:* Front-door failure mode — if the mic clips, nothing downstream can help; unresolved.
 *Missing evidence:* INMP441 SPL rating vs. real gunshot SPL levels; no clipping test performed.
 *How to answer:* Acknowledge per Q64 — this is an identified, unresolved hardware-upgrade item.
 *Strengthening experiment:* Look up the INMP441's actual maximum SPL rating and compare explicitly against published close-range gunshot SPL figures — even without new hardware, this desk-research would substantially strengthen your answer.

9. **"How do you know you're not overfitting — show me a training vs. validation curve."**
 *Why dangerous:* Fundamental ML-rigor question with no visual evidence currently prepared.
 *Missing evidence:* An actual plotted loss curve.
 *How to answer:* Per Q41 — state the proper split exists, admit the curve isn't currently presented.
 *Strengthening experiment:* Pull your actual training logs and add a simple train-vs-val loss plot to your appendix before the jury round — this is low-effort, high-credibility-return.

10. **"What is genuinely novel here versus just combining existing published techniques?"**
 *Why dangerous:* Tests whether your "innovation" section can survive a rigorous breakdown, and whether you'll overclaim under pressure.
 *Missing evidence:* An exhaustive systematic literature search proving true novelty (impossible to fully provide).
 *How to answer:* Per Q79/Q80 — give the precise three-way breakdown (prior art / integration / genuine contribution).
 *Strengthening experiment:* None fully closes this gap — the strongest mitigation is disciplined, precise language ("within our reviewed literature") every single time novelty is claimed, never an absolute "first ever."

---

# FINAL TEAM CHEAT SHEET

### 20 numbers every team member must know cold
1. 23,980 — trainable parameters
2. ~93.67 KB — FP32 model size
3. 42,352 bytes (~42KB) — INT8 deployed model size
4. 2.04 ms — measured GRU inference time per 10ms hop
5. 10 ms — real-time hop/frame budget
6. 16 kHz — sampling rate
7. 320 samples / 20 ms — frame length
8. 160 samples / 10 ms — hop length
9. 512-point FFT → 257 frequency bins
10. 22 — Bark sub-bands
11. 44 — GRU input features (22 log-energy + 22 delta-energy)
12. 64 — GRU hidden units
13. +6.76 dB (headline) / +6.85 dB (category table) — impulsive SI-SNR improvement (**resolve before presenting**)
14. +1.17 dB — non-impulsive SI-SNR improvement
15. 12.13 dB — peak impulse attenuation
16. 0.995 — clean-speech control correlation
17. 233.45 ms — IISRT
18. 457.08 ms — RSDD
19. 79.51 hours / 26,000 mixtures (20k/3k/3k split) — dataset scale
20. 200 KB — PSRAM tensor arena size

### 20 concepts every team member must understand
1. STFT and why frame/hop/FFT-size choices trade off resolution vs. latency vs. compute
2. What a complex ratio mask is and why phase correction matters
3. Why Bark-band compression exists (perceptual + parameter-efficiency)
4. Why GRU over LSTM/CNN/Transformer for this task
5. What "causal/streaming" means and why it's mandatory for real-time
6. INT8 quantization and why mask MAE is the relevant error metric
7. The difference between algorithmic, compute, and hardware I/O latency
8. Why PSRAM was needed for the tensor arena
9. SI-SNR / SI-SNRi definition and what it does/doesn't capture
10. The difference between "improvement vs. raw noisy input" and "improvement vs. a competing system"
11. What IISRT and RSDD are trying to measure and why no prior metric does this
12. V2's modular, non-retraining, parallel-side-channel design philosophy
13. The detector's feature set (energy, crest factor, spectral flux, HF ratio) and why each helps
14. Attack-release exponential gain smoothing vs. instant on/off gating
15. Hysteresis and why it prevents state-flicker
16. Data leakage and why session-level splitting matters
17. The sim-to-real gap and why it's your biggest unresolved risk
18. The three-way prior-art / integration / genuine-contribution breakdown of your novelty claim
19. Why single-mic (no beamforming) was chosen
20. The full honest list of what's proven vs. not-yet-proven (Master doc B.6)

### 20 claims you must NEVER overclaim
1. "Our system is fully real-time end-to-end, proven." (Not proven.)
2. "We removed 90% of gunshots." (It's a residual-energy-metric reduction, not gunshot removal.)
3. "Zero latency." (False by definition of any real processing pipeline.)
4. "Works equally well for all noise types." (Contradicted by your own weak-category data.)
5. "Completely eliminates background noise." (Never claim complete elimination.)
6. "Full mic-to-speaker deployment is complete." (It is not — see B.6.)
7. "V2/V3 improvements are already achieved." (They are designed/planned, not yet measured.)
8. "Ready for field deployment." (Explicitly not — hardware, power, safety gaps remain.)
9. "We are the first team in the world to ever do this." (Scope to "within our reviewed literature.")
10. "Our hardware is field-rugged." (It is prototype-grade.)
11. "Power/battery life is good." (Not measured at all.)
12. "Our results prove real-world military performance." (Results are on synthetic data only.)
13. "Soldiers should always trust our enhanced output over raw audio." (Contradicted by negative categories.)
14. "The detector never false-triggers on loud speech." (Untested.)
15. "There are no boundary/reconstruction artifacts." (Not specifically tested.)
16. "We beat [competitor product] head-to-head." (No direct competitive benchmark run.)
17. "GRU is universally better than LSTM/CNN." (Task- and constraint-dependent, not universal.)
18. "Our system is EMI-hardened / RF-interference-tested." (Not addressed at all.)
19. "We definitely aren't overfitting." (No loss curve currently shown as evidence.)
20. "This is mission-ready / safety-proven." (Explicitly not — no failure-mode handling built yet.)

### 10 biggest weaknesses
1. Entirely synthetic training/evaluation data — no real-world validation
2. Full end-to-end real-time latency not yet measured (and contradicted by an inconsistent slide)
3. No power/battery-life measurement at all
4. V2 impulse detector not yet ported to the ESP32 — PC-only prototype
5. No deadline-miss / hardware-failure / OOD-input fallback behavior defined
6. Prototype-grade, non-rugged hardware (mic, speaker) not rated for field conditions
7. Negative SI-SNRi on drone+wind and siren+wind categories (measurable degradation)
8. No detector false-positive-rate testing against loud human speech
9. Two internal numeric inconsistencies between your own source documents
10. No head-to-head benchmark against any existing competing product or classical DSP baseline

### 10 strongest technical points
1. Measured (not simulated) 2.04ms GRU inference on real ESP32-S3 silicon, well within a 10ms budget
2. Every architecture choice traceable to a specific literature citation
3. Complex ratio masking for phase-aware reconstruction, not just magnitude masking
4. Rigorous 79.51-hour, 26,000-mixture dataset with a proper 20k/3k/3k split
5. Session-level leakage prevention for the drone-noise subset
6. Two novel, purpose-built recovery-time metrics (IISRT, RSDD) filling a genuine literature gap
7. Small, MCU-appropriate model (23,980 params, 42KB INT8) with quantization error well inside published benchmarks
8. High clean-speech control correlation (0.995), showing minimal distortion when nothing needs suppressing
9. Honest, proactive category-wise weak-point reporting (drone+wind, siren+wind) rather than hiding it
10. Modular V2 design that doesn't require retraining or risking the already-validated V1 GRU

### 10 strongest innovation points
1. First (within your reviewed literature) evaluation of a streaming causal neural SE model against true impulsive noise
2. IISRT/RSDD as genuinely novel, purpose-built recovery-time metrics
3. Independent confirmation of the targeted gap by a DRDO-DEAL-co-authored 2026 Defence Science Journal paper
4. Real, measured (not simulated) ESP32-S3 hardware deployment — a low-cost MCU rather than FPGA/DSP-class hardware
5. Modular, parallel-side-channel V2 architecture — deterministic, tunable, non-retraining
6. Explicit, sweepable attack-release recovery-time tuning via a release-time constant
7. Multilingual testing consideration (Hindi speech corpus) beyond English-only speech
8. Drone-specific noise testing, directly relevant to a modern battlefield noise profile
9. Category-wise (not just aggregate) result reporting, enabling precise, honest scoping of claims
10. A clearly staged V1→V2→V3 roadmap with each stage's risk and evaluation plan explicitly defined

### 10 experiments you should run before SIH, if time allows
1. Resolve and correct the latency and SI-SNRi numeric inconsistencies between your two documents using source data
2. Complete a full mic-to-speaker end-to-end real-time latency measurement (sustained, not single-shot)
3. Run a false-positive-rate test of the V2 detector against loud human speech/shouting
4. Measure power draw (mA/mW) during sustained inference for a rough battery-life estimate
5. Plot and present an actual training-vs-validation loss curve
6. Test against even a small real (non-synthetic) impulsive-noise recording set
7. Run a magnitude-only-mask ablation to quantify the complex mask's specific contribution
8. Run a classical DSP baseline (e.g., spectral subtraction) on your same test set for direct comparison
9. Look up and cite the INMP441's actual SPL rating against real gunshot SPL figures
10. Confirm and be ready to state exact IISRT/RSDD mathematical definitions, and exact training hyperparameters (epochs, optimizer, learning rate, loss function)

### 10 sentences you should NEVER say to the jury
1. "Yes, our system is fully real-time, proven end-to-end."
2. "We've basically solved this problem."
3. "Nobody has ever done anything like this before."
4. "It works perfectly on all noise types."
5. "This is ready for actual field deployment."
6. "Trust us, the numbers are right." (without offering to verify a flagged discrepancy)
7. "Power isn't really a concern for this kind of device."
8. "Our hardware is fully rugged and field-tested."
9. "The AI will always know when it's wrong and correct itself."
10. "That's just a minor detail, it doesn't really matter." (in response to any numeric inconsistency)

### 10 professional phrases for handling questions you don't know
1. "That's a fair question — I don't have that exact figure memorized, and I'd rather confirm it from our data than guess."
2. "We haven't run that specific experiment yet — it's a good addition to our validation plan."
3. "That's outside what we've measured so far; here's what we do know that's adjacent to it..."
4. "I want to be precise rather than approximate on that — let me state what's confirmed and flag what isn't."
5. "That's an honest gap in our current work, not something we're trying to avoid."
6. "We'd need to check our source logs/code to answer that with full confidence."
7. "Our documentation has an inconsistency there that we should resolve — here's our best current understanding."
8. "That's scoped as future work (V2/V3), not something we're claiming today."
9. "We don't have a measured number for that — here's the reasoning we used instead."
10. "That's a great stress-test of our design — here's the honest limitation, and here's our planned mitigation."

---

*Prepared as an adversarial jury simulation grounded strictly in the two uploaded ImpulseGuard SIH documents (PPT idea submission and Master V2 doc). Where numbers, methods, or definitions were not present in either source, this document flags them explicitly for the team to confirm from actual training logs, code, and methodology notes before the real jury round — do not improvise these details under pressure.*
