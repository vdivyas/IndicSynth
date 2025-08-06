# IndicSynth
*A Large-Scale Multilingual Synthetic Speech Dataset for Low-Resource Indian Languages*

**🏆 Outstanding Paper Award, ACL 2025**

---

## 🧠 Overview

**IndicSynth** is a novel multilingual synthetic speech dataset designed to advance multilingual **audio deepfake detection (ADD)** and **anti-spoofing** research. It covers **12 low-resource Indian languages** and provides both **mimicry** and **diversity** subsets.

> 4,000+ hours of synthetic audio  
> 12 languages: Bengali, Gujarati, Hindi, Kannada, Malayalam, Marathi, Odia, Punjabi, Sanskrit, Tamil, Telugu, Urdu  
> Useful for ADD, speaker verification (SV), and bias studies  

---

## 📂 Dataset Structure

Each language folder contains:

<pre>
    
IndicSynth/
├── Bengali/
│ ├── audio/ # All .wav files (synthetic clips)
│ └── metadata.csv # Metadata for all synthetic clips
├── Gujarati/
│ ├── audio/
│ └── metadata.csv

</pre>

Each 'metadata.csv' includes:

- Generative Model (xtts_v2 / vits / freevc24)
- Speaker IDs
- Gender
- Transcript (if applicable)
- File path to synthetic audio


📝 **Note on Transcripts in Metadata**

The transcripts included in the metadata.csv files represent the intended text prompts used during synthetic speech generation via TTS models. We provide these transcripts to enable future explorations, but do not guarantee perfect alignment with the generated audio. If you intend to use IndicSynth for speech-to-text or similar tasks, we strongly recommend conducting careful human evaluation with proficient native speakers of the respective languages.

---

## ⚙️ IndicSynth Generation?

Synthetic data was generated using:

| Model      | Type      | Transcript | Fine-Tuned |
|------------|-----------|------------|-------------|
| xtts_v2  | TTS       | Yes        | Yes (for 10 languages) |
| vits     | TTS       | Yes        | No          |
| freevc24 | VC        | No         | No          |

> **Mimicry subset**: For anti-spoofing research

> **Diversity subset**: Contains diverse set of realistic synthetic voices for multilingual audio deepfake detection research

For more details, please see the Table 1 and Section 3 of our paper: https://aclanthology.org/2025.acl-long.1070.pdf

---

## 📦 Access the Dataset

**HuggingFace:** https://huggingface.co/datasets/vdivyasharma/IndicSynth

You can load data in a specific target language using the following code:

```python
import os
import soundfile as sf
from datasets import load_dataset
from tqdm import tqdm
import pandas as pd

language = "Hindi" # Specify the target language here

# Load Dataset
dataset = load_dataset("vdivyasharma/IndicSynth", name=language, split="train") 

# Create target directory structure
output_dir = language
audio_dir = os.path.join(output_dir, "audio")
os.makedirs(audio_dir, exist_ok=True)

# Store metadata rows here
metadata_rows = []

# Loop through dataset and save each clip
for example in tqdm(dataset):
    audio_array = example["audio"]["array"]
    sampling_rate = example["audio"]["sampling_rate"]
    
    # Get filename
    original_name = example.get("file") or example.get("path") or example["audio"]["path"].split("/")[-1]
    
    # Save audio to audio/ subfolder
    audio_path = os.path.join("audio", original_name)  # relative path for metadata
    sf.write(os.path.join(output_dir, audio_path), audio_array, sampling_rate)
    
    # Store metadata row
    row = {k: v for k, v in example.items() if k != "audio"}
    row["file_name"] = audio_path
    metadata_rows.append(row)

# Save metadata to CSV
df = pd.DataFrame(metadata_rows)
df.to_csv(os.path.join(output_dir, "metadata.csv"), index=False)


```
## License
IndicSynth is released under the **CC BY-NC 4.0 License**.  
It is intended for **non-commercial, academic research only**.

## Citation
If you use IndicSynth, please cite the following papers:

<pre>@inproceedings{sharma-etal-2025-indicsynth,
    title = "{I}ndic{S}ynth: A Large-Scale Multilingual Synthetic Speech Dataset for Low-Resource {I}ndian Languages",
    author = "Sharma, Divya V  and
      Ekbote, Vijval  and
      Gupta, Anubha",
    editor = "Che, Wanxiang  and
      Nabende, Joyce  and
      Shutova, Ekaterina  and
      Pilehvar, Mohammad Taher",
    booktitle = "Proceedings of the 63rd Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers)",
    month = jul,
    year = "2025",
    address = "Vienna, Austria",
    publisher = "Association for Computational Linguistics",
    url = "https://aclanthology.org/2025.acl-long.1070/",
    pages = "22037--22060",
    ISBN = "979-8-89176-251-0"
}

</pre>



<pre>@article{IndicSuperb,
author = {Javed, Tahir and Bhogale, Kaushal and Raman, Abhigyan and Kumar, Pratyush and Kunchukuttan, Anoop and Khapra, Mitesh},
year = {2023},
month = {06},
pages = {12942-12950},
title = {IndicSUPERB: A Speech Processing Universal Performance Benchmark for Indian Languages},
volume = {37},
journal = {Proceedings of the AAAI Conference on Artificial Intelligence},
doi = {10.1609/aaai.v37i11.26521}
}

</pre>

---

## 💬 Contact
For questions or feedback, please feel free to reach out at divyas@iiitd.ac.in.

## 🙏 Acknowledgments
> 🌍 ACL Diversity & Inclusion Subsidy for enabling in-person presentation at ACL 2025

> 🤝 HuggingFace for dataset hosting support

