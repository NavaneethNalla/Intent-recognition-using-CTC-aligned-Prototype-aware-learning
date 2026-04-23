
Project Overview
In real-world human-computer interaction, understanding a user's intent is difficult because signals are often "noisy." A person might say something positive while having a frustrated facial expression, or background environment noise might obscure their speech. 

Our implementation is specifically optimized for Hindi-language contexts by replacing standard BERT with MuRIL.

Technical Architecture and Workflow

1. Bridging the Modality Gap (Hindi-Specific)
The model extracts high-level numerical features using specialized pre-trained backbones:
- Text: We use MuRIL (Multilingual Representations for Indian Languages). Unlike standard BERT, MuRIL is specifically trained to capture the nuances, syntax common in Indian languages.
- Audio and Video: High-dimensional features are extracted via an acoustic encoder and a visual encoder.
- Synchronization: These signals are synchronized using a CTC (Connectionist Temporal Classification) module, ensuring facial expressions and vocal tones are perfectly aligned with the corresponding Hindi tokens.

2. Temporal Feature Reinforcement
Following alignment, the data is re-encoded using a Bi-Peephole LSTM for audio and specialized Transformer/MuRIL encoders for video and text. This stage "pushes" the temporal alignment features directly into the embeddings, ensuring every numerical vector is aware of its context in time.

3. Coarse-to-Fine Dynamic Attention Fusion (DAF)
To prevent the model from being distracted by local noise (like a blurry video frame or a misheard word), we implement a Coarse-to-Fine strategy:
- Global Summary for Noise Correction: The model extracts a "Coarse" global summary for each modality. This acts as a reliable reference point to stabilize the model.
- Dynamic Correction: If a specific fine-grained token is noisy or ambiguous, the DAF module uses the Global Summary to "correct" the representation, ensuring the overall "vibe" of the speech or video guides the interpretation of individual details.

4. Prototype-Aware Contrastive Alignment
Instead of just learning boundaries between classes, the model learns a "Prototype"—an ideal mathematical center—for every intent (e.g., Gratefulness, Complaint). 
- Semantic Grounding: By aligning fused features with these prototypes, the model develops a stable understanding of what each intent looks and sounds like.
- Rare-Class Robustness: This is powerful for intents with limited training data, as the prototype provides a fixed target for the model to aim for, preventing it from getting lost in high-dimensional space.


The dataset used in this project is **entirely self-created** and designed for intent recognition in real-world human-computer interaction scenarios. It consists of **300 annotated samples**, each representing a short segment of a video.

Each sample is organized using a combination of structured metadata and corresponding media files:

### 1. Metadata (Excel File)
The dataset includes an Excel sheet where each row corresponds to a single sample with the following fields:
* **ID:** Unique identifier for each sample
* **Start Time:** Start timestamp of the segment in the video
* **End Time:** End timestamp of the segment
* **Text:** Transcription of the spoken content (in Hindi and Hinglish)
* **Label:** Annotated intent of the sample

---

### 2. Video Data
A folder containing video clips for each sample:
* Each file is named using its corresponding ID from the Excel sheet

---

### 3. Audio Data
A folder containing extracted audio files:
* Each audio file corresponds to a video sample
* File names match the ID in the metadata

---

### Data Alignment
All components of the dataset are linked through the **unique ID**, ensuring seamless mapping between:
* Metadata (Excel)
* Video files
* Audio files
