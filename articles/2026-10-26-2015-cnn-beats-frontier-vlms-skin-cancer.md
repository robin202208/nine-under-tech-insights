# 62% Against 42%: A 2015 CNN Reads Skin Better Than Two Frontier Models

> A London dermatologist tested GPT-6 Astra and Claude Fable 5.1 on 200 biopsy-proven lesions. The striking part is not that a decade-old Inception v3 won — it is how differently the models failed.

## What Happened

On 25 October 2026, Dr Magnus Lynch — consultant dermatologist and Mohs surgeon at Guy's Hospital in London — published a controlled comparison of two frontier models against a fine-tuned Inception v3, the convolutional network Google published in 2015. The test set was 200 lesions drawn at random from MILK10k, each photographed twice (clinical close-up and dermoscopic view), every one confirmed by histopathology. Half could not be safely discharged: 30 melanomas, 25 basal cell carcinomas, 25 squamous cell carcinomas including Bowen's, 20 actinic keratoses. Half were safe: 50 naevi, 50 seborrhoeic keratoses — and because all of them had been biopsied anyway, they are the lesions that worried a clinician, not textbook examples.

Each system had to choose one of six diagnoses, optionally give a second choice, and state whether the lesion could be discharged with no biopsy or follow-up. The CNN named the right diagnosis for 125 of 200 (62%); Claude Fable 5.1 reached 85 (42%) and GPT-6 Astra 84. Allowing a second choice: 173 versus 138 and 134. The CNN caught 13 of 30 melanomas against 11 and 8. Both LLMs received the two images and one identical prompt at default settings; the CNN was fine-tuned in roughly twenty minutes on a single H100 over 68,011 ISIC archive images, with every photograph of the test lesions and patients stripped from training and validation.

The failure shapes differ more than the scores. Astra called 75 of 200 lesions basal cell carcinoma when 25 were, including 14 of the 30 melanomas. Fable 5.1 called 53 actinic keratosis when 20 were. The CNN's errors pointed the other way: 8 of its 17 melanoma misses became benign naevi — the dangerous direction. Yet at thresholds matched to the same safety target, each LLM kept 98–99 of 100 unsafe lesions and discharged one or two melanomas, against 18 for the CNN. A large share of the headline gap is calibration and taxonomy, not blindness.

## Why It Matters

Separate three things. First, the interface. A six-way dermatology taxonomy plus a discharge decision is a perceptual task, and frontier vision models consume images through a pipeline built for web photographs: resize, patchify, compress. Dermoscopic structure at the 0.1 mm scale does not survive that trip intact. Second, calibration. The threshold-matched numbers say the representation is not useless — the models retain nearly all dangerous lesions. What they cannot do is commit to a decision boundary a clinician would trust, because they have no operating point: a "95% confidence" basal cell carcinoma is a class prior wearing a probability costume. Third, the benchmark is adversarial to generalists by construction: histology labels, no clinical history, no chance to ask the patient. Add the notes and let the model order its own biopsy and the comparison changes — the author says as much.

But "narrow models win narrow tasks" is too quick a dismissal. The fact worth registering is not that small models are good; it is that the chatbot-as-universal-interface strategy assumed perception would arrive free with scale. On the task the industry has cited for a decade as medical AI's killer app, it did not. A 2015 architecture with twenty minutes of fine-tuning, small enough to run in a phone browser over WebGPU with nothing uploaded, is a shipping artifact. Two frontier models with a per-token price are not.

## Impact

For teams building on frontier models, the rule is: let the LLM build the pipeline, then ship the small model. Data curation, labeling scaffolds, evaluation harnesses, the analysis and first drafts of this very study — that is where general models earn their cost. The classifier itself should be the cheapest thing that works, because per-query inference over a six-class taxonomy is pure waste, and one HN commenter's heuristic holds up: models are good at what is overrepresented and RL-trained in their corpus, and merely fluent elsewhere.

For evaluation, the lesson is methodological: publish matched-threshold numbers, publish per-class confusion, disclose contamination windows. MILK10k has been public since 2025, so the LLMs may have seen these exact images — if anything that flattered them — while the CNN provably had not. The fair objection is out-of-domain brittleness: the same model scored AUC 0.96 on archive-like images, 0.86 here.

For medical AI the asymmetry is regulatory as much as technical. A fine-tuned Inception is a class II device with a frozen input distribution and a stated sensitivity. A general model that answers differently on Tuesday is not, whatever its leaderboard row says.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Lynch Lab](https://lynchlab.net/llm-skin-cancer) | HN Discussion: [427 points, 205 comments](https://news.ycombinator.com/item?id=50233779)*
