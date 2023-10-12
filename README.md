# 🐶 BARK AI: but with the ability to use voice cloning on custom audio samples

If you want to clone a voice just follow the `clone_voice.ipynb` notebook. If you want to generate audio from text, follow the `generate.ipynb` notebook.

To create a voice clone sample, you need an audio sample of around 5-6 seconds

You will get the best results by making generations with your cloned voice until you find one that is really close to the source. Then use that as the new history prompt (comes from the model so should theoretically be more consistent)

- [BARK text to speech @ SERP AI](https://serp.ai/tools/bark-text-to-speech-ai-voice-clone-app/)

# Shoutouts
- Huge shoutout to [gitmylo](https://github.com/gitmylo/bark-voice-cloning-HuBERT-quantizer/) for the solution to the semantic token generation for better voice clones and finetunes

-------------------------------------------------------------------
# Original README.md
## 🤖 Usage

```python
from bark import SAMPLE_RATE, generate_audio, preload_models
from IPython.display import Audio

# download and load all models
preload_models()

# generate audio from text
text_prompt = """
     Hello, my name is Serpy. And, uh — and I like pizza. [laughs] 
     But I also have other interests such as playing tic tac toe.
"""
audio_array = generate_audio(text_prompt)

# play text in notebook
Audio(audio_array, rate=SAMPLE_RATE)
```

[pizza.webm](https://user-images.githubusercontent.com/5068315/230490503-417e688d-5115-4eee-9550-b46a2b465ee3.webm)


To save `audio_array` as a WAV file:

```python
from scipy.io.wavfile import write as write_wav

write_wav("/path/to/audio.wav", SAMPLE_RATE, audio_array)
```

### 🌎 Foreign Language

Bark supports various languages out-of-the-box and automatically determines language from input text. When prompted with code-switched text, Bark will attempt to employ the native accent for the respective languages. English quality is best for the time being, and we expect other languages to further improve with scaling. 

```python
text_prompt = """
    Buenos días Miguel. Tu colega piensa que tu alemán es extremadamente malo. 
    But I suppose your english isn't terrible.
"""
audio_array = generate_audio(text_prompt)
```

[miguel.webm](https://user-images.githubusercontent.com/5068315/230684752-10baadfe-1e7c-46a2-8323-43282aef2c8c.webm)

### 🎶 Music

Bark can generate all types of audio, and, in principle, doesn't see a difference between speech and music. Sometimes Bark chooses to generate text as music, but you can help it out by adding music notes around your lyrics.

```python
text_prompt = """
    ♪ In the jungle, the mighty jungle, the lion barks tonight ♪
"""
audio_array = generate_audio(text_prompt)
```

[lion.webm](https://user-images.githubusercontent.com/5068315/230684766-97f5ea23-ad99-473c-924b-66b6fab24289.webm)

### 🎤 Voice Presets and Voice/Audio Cloning

Bark has the capability to fully clone voices - including tone, pitch, emotion and prosody. The model also attempts to preserve music, ambient noise, etc. from input audio. However, to mitigate misuse of this technology, we limit the audio history prompts to a limited set of Suno-provided, fully synthetic options to choose from for each language. Specify following the pattern: `{lang_code}_speaker_{0-9}`.

```python
text_prompt = """
    I have a silky smooth voice, and today I will tell you about 
    the exercise regimen of the common sloth.
"""
audio_array = generate_audio(text_prompt, history_prompt="en_speaker_1")
```


[sloth.webm](https://user-images.githubusercontent.com/5068315/230684883-a344c619-a560-4ff5-8b99-b4463a34487b.webm)

*Note: since Bark recognizes languages automatically from input text, it is possible to use for example a german history prompt with english text. This usually leads to english audio with a german accent.*

### 👥 Speaker Prompts

You can provide certain speaker prompts such as NARRATOR, MAN, WOMAN, etc. Please note that these are not always respected, especially if a conflicting audio history prompt is given.

```python
text_prompt = """
    WOMAN: I would like an oatmilk latte please.
    MAN: Wow, that's expensive!
"""
audio_array = generate_audio(text_prompt)
```

[latte.webm](https://user-images.githubusercontent.com/5068315/230684864-12d101a1-a726-471d-9d56-d18b108efcb8.webm)


## 💻 Installation

```
pip install git+https://github.com/suno-ai/bark.git
```

or

```
git clone https://github.com/suno-ai/bark
cd bark && pip install . 
```

## 🛠️ Hardware and Inference Speed

Bark has been tested and works on both CPU and GPU (`pytorch 2.0+`, CUDA 11.7 and CUDA 12.0).
Running Bark requires running >100M parameter transformer models.
On modern GPUs and PyTorch nightly, Bark can generate audio in roughly realtime. On older GPUs, default colab, or CPU, inference time might be 10-100x slower. 

## ⚙️ Details

Similar to [Vall-E](https://arxiv.org/abs/2301.02111) and some other amazing work in the field, Bark uses GPT-style 
models to generate audio from scratch. Different from Vall-E, the initial text prompt is embedded into high-level semantic tokens without the use of phonemes. It can therefore generalize to arbitrary instructions beyond speech that occur in the training data, such as music lyrics, sound effects or other non-speech sounds. A subsequent second model is used to convert the generated semantic tokens into audio codec tokens to generate the full waveform. To enable the community to use Bark via public code we used the fantastic 
[EnCodec codec](https://github.com/facebookresearch/encodec) from Facebook to act as an audio representation.

Below is a list of some known non-speech sounds

- `[laughter]`
- `[laughs]`
- `[sighs]`
- `[music]`
- `[gasps]`
- `[clears throat]`
- `—` or `...` for hesitations
- `♪` for song lyrics
- capitalization for emphasis of a word
- `MAN/WOMAN:` for bias towards speaker

**Supported Languages**

| Language | Status |
| --- | --- |
| English (en) | ✅ |
| German (de) | ✅ |
| Spanish (es) | ✅ |
| French (fr) | ✅ |
| Hindi (hi) | ✅ |
| Italian (it) | ✅ |
| Japanese (ja) | ✅ |
| Korean (ko) | ✅ |
| Polish (pl) | ✅ |
| Portuguese (pt) | ✅ |
| Russian (ru) | ✅ |
| Turkish (tr) | ✅ |
| Chinese, simplified (zh) | ✅ |
| Arabic  | Coming soon! |
| Bengali | Coming soon! |
| Telugu | Coming soon! |
