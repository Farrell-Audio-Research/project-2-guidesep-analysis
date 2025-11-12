*This project is part of our [Machine Listening & Audio ML Portfolio](https://github.com/Farrell-Audio-Research/audio-ml-portfolio).*

## 🎵 GuideSep: Analysis of Timbral-Guided Source Separation

**Authors:** Conner O. Farrell & Mark Farrell (Fall 2025)
**Reproduction & Extension:** Machine Listening & Audio ML Portfolio

---

## 🧠 Overview & Key Findings

This project reproduces the pipeline from the GuideSep paper and extends it with a critical analysis of the model's robustness against timbral mismatch.

While the baseline demo (humming for strings/piano) was successfully replicated, our primary investigation focused on a challenging, real-world mix: a 10-second clip of a high-gain, distorted electric guitar solo from Guns N' Roses' "Sweet Child O' Mine".

Our key finding is that the timbral quality of the user's guide is a critical factor for successful separation. A simple pitch-matched guide (like humming) is insufficient for complex sources with strong transients (e.g., a picked guitar). The model heavily relies on the guide's timbral characteristics to identify the target source.

**Based on paper:** [User-Guided Generative Source Separation](https://www.arxiv.org/abs/2507.01339) by Wen et al. (2025)

---

## 🧪 Experimental Analysis: The Criticality of Timbre

We hypothesized that a guide with a significant timbral mismatch (e.g., a smooth human hum for a sharp, distorted guitar) would lead to poor separation, while a timbre-matched guide would succeed.

We tested this hypothesis using two custom-recorded guides.

### Experiment A: Timbre-Mismatched Guide (Voice)

**Guide:** Human voice humming the guitar solo melody.  
**Result:** Poor. The separation failed to capture the guitar's characteristic "attack" or plectrum transient, resulting in a sound more like a "synthetic violin" with no attack. We also observed noticeable vocal bleed-in, likely because the model, guided by a voice, was biased toward finding "vocal-like" sounds.

### Experiment B: Timbre-Matched Guide (Guitar)

**Guide:** An unplugged electric guitar playing the solo (recorded low-fidelity on a smartphone). This guide matches the target's core timbre (plucked string) and transient properties.  
**Result:** Significantly Improved. The separated audio was clearly identifiable as a distorted electric guitar. Crucially, the note attacks were present (though quieter than the original mix). This demonstrates the model successfully used the guide's transient information to "find" the correct sound in the mix.

---

## 🔬 Visual Analysis: Spectrogram Comparison

The spectrograms below visually confirm this finding. The Y-axis represents frequency, the X-axis represents time, and brightness represents intensity.

> Note: Due to copyright, the original source mix and its separated outputs cannot be shared. The user-generated guides, however, are included in this repository (`GnR_humming_guide.wav`, `GnR_guitar_guide.wav`).

### Guide (Input) vs Separated (Output)

**A: Voice Hum Guide (Input)**  
<img width="1024" height="512" alt="GnR_humming_guide_spectrogram" src="https://github.com/user-attachments/assets/6d8f6368-751c-46bc-999c-da6510f23c10" />

**B: Result from Voice Guide (Output)**
<img width="1024" height="512" alt="GnR_output_hum_guided_spectrogram" src="https://github.com/user-attachments/assets/4aaa350f-5e9c-4651-9c2e-5faaddf614fa" />



**C: Unplugged Guitar Guide (Input)**  
<img width="1024" height="512" alt="GnR_solo_guide_spectrogram" src="https://github.com/user-attachments/assets/05428a6b-92f3-49c5-8579-1595388f5f0d" />

**D: Result from Guitar Guide (Output)**
<img width="1024" height="512" alt="GnR_output_guitar_guided_spectrogram" src="https://github.com/user-attachments/assets/313b7da1-b5ec-4477-b6ce-79ff3fa1d8ea" />



**Analysis:**  
Compare (A) and (C): The Guitar Guide (C) contains sharp, vertical lines (broadband "clicks") at the start of each note. These are the transients from the plectrum, which are completely absent in the smooth Voice Hum Guide (A).

Compare (B) and (D): The Result from Guitar Guide (D) successfully preserves these vertical transient lines (note attacks), while the Result from Voice Guide (B) fails to separate them, resulting in the "synthetic" sound.

---

## ⚙️ Project Goals

- Replicate the baseline results from the GuideSep paper to confirm functionality.
- Stress-test the model with a complex, dense, real-world audio mix (rock guitar solo).
- Investigate and demonstrate the critical impact of timbral mismatch between the user guide and the target source.
- Package the entire workflow in a portable Google Colab notebook for reproducibility.

---

## 🚀 Quickstart (Google Colab)

### 1. Open the Notebook in Colab
Upload **GuideSep_Demo.ipynb** and run **Cells 1–6**.  
This sets up dependencies, downloads the GuideSep model, and preps your environment.

### 2. Manual Step after Restart
After running Cell 6, Colab restarts automatically (this is expected).  
Reconnect the runtime, upload `audio_processing_utils.py`, and manually run **Cells 7+**.

### 3. Upload Your Audio
Provide:
- A mixture file (e.g., `mix.wav`) → upload to `content/GuideSep/mix`
- Your hum or reference melody (e.g., `hum.wav`) → upload to `content/GuideSep/cond`

You can use the provided `sample_mix.wav` and `sample_hum.wav` for the baseline test, or our `GnR_..._guide.wav` files for the advanced test.

### 4. View Output
The separated audio (`output.wav`) will be saved locally in Colab.  
You can play or download it directly.

---

## 🧩 Repository Structure

```
guidesep-humming-separation/
├── GuideSep_Demo.ipynb            # Main Colab notebook
├── audio_processing_utils.py      # Helper functions loaded by the notebook
│
├── real_3_mix.wav                 # Example audio mixture (Strings/Piano)
├── real_3_humming.wav             # Example humming track for sample_mix
│
├── GnR_humming_guide.wav          # Our custom VOICE guide (timbre-mismatched)
├── GnR_guitar_guide.wav           # Our custom GUITAR guide (timbre-matched)
│
├── separated_output.wav           # Example output after separation
├── README.md                      # This file
└── LICENSE
```

> *Note:* Copyrighted source audio (Guns N' Roses mix) and any separated stems derived from it are **not included** in this repository.

---

## 📚 Background

The GuideSep model learns a multimodal latent space combining:

- Audio spectrograms  
- Conditioning embeddings from user input (hum, whistle, etc.)  
- A diffusion-based generative decoder for source reconstruction

This approach enables semantic control in audio separation, aligning with human perceptual cues rather than purely statistical priors.

---

## 🏗️ Future Work

Based on our findings, we propose:

- **Quantify Timbral Similarity:** Develop a metric to measure the "timbral distance" between a guide and a source to programmatically predict separation success before running the full model.
- **Guide Pre-processing:** Experiment with timbre transfer models to pre-process a user's hum into a more effective guide (e.g., "make this hum sound more 'plucked'").
- **Evaluate with SDR/SIR/SAR:** Quantify the separation quality of our experiments using standard mir_eval metrics.

---

## 📄 License & Credits

Original GuideSep © Yutong Wen et al. (2025)  
Reproduction & Analysis by Conner O. Farrell & Mark Farrell  
Licensed under **MIT License** (for reproduction and educational use)

---

## 🔗 References

- Wen, Yutong et al. *User-Guided Generative Source Separation*, arXiv:2507.01339 (2025).  
- [GuideSep Project Page](https://yutongwen.github.io/GuideSep/)  
- [GuideSep GitHub Repository](https://github.com/YutongWen/GuideSep)
