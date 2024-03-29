# Automatic Speech Recognition (ASR)

## Feature Extraction

Most used feature extraction techniques:

1. Mel-frequency cepstral coefficients (MFCCs)
2. Discrete Wavelet Transform (DWT)

Language Model is an important part of the ASR since it is responsible for generating grammatically correct text.

### Mel Frequency

MFCC feature extraction is composed of the following steps:

1. Window the signal.
2. Apply Discrete Fourier Transform.
3. Logarithm of the magnitude.
4. Convert to a Mel scale.
5. Apply inverse discrete cosine transform (DCT).

## Deep Neural Networks for ASR

CNNs and RNNs have previously been used for ASR while currently Transformer based architectures are delivering great results.

### Recurrent Neural Networks

In Speech Recognition, the information of the future context is equally as important as the past context so BiDirectional RNNs have been more commonly used.

**CTC is an objective function that computes the alignment between the input speech signal and the output sequence of the words.**

## Mel Spectrograms

### Code

Source : [TorchAudio Tutorial](https://pytorch.org/audio/0.11.0/tutorials/audio_feature_extractions_tutorial.html#spectrogram)

#### Converting to Spectrogram

**The GriffinLim is used to convert a spectrogram back into a waveform.**


```Python

import torchaudio.transforms as T

waveform, sample_rate = get_speech_sample()

## Get the spectrogram for the waveform.
sample_spectrogram = T.Spectrogram(
    n_fft = n_fft,
    win_length = win_length,
    hop_length = hop_length
    )(waveform)

griffin_lim = T.GriffinLim(
    n_fft = n_fft,
    win_length = win_length,
    hop_length = hop_length
)

waveform = griffin_lin(sample_spectrogram)

```

#### Converting to Mel Spectrogram

```Python

waveform, sampling_rate = get_speech_sample()

mel_spectrogram = T.MelSpectrogram(

    sample_rate = sampling_rate,
    n_fft = n_fft,
    win_length = win_length,
    hop_length = hop_length,
    center = True,
    pad_mode = "reflect",
    power = 2.0,
    norm = "slaney",
    ondeside = True,
    n_mels = n_mels,
    mel_scale = "htk"
)

melspec = mel_spectrogram(waveform)
```

### Data Cleaning

- Convert text to lowercase. This can be later corrected by a grammar focused Language Model, the most important thing is to identify vocals and pair them with characters or words.

- Remove Unicode and Accent characters, unicode characters may sometimes be introduced to the text corpus, these are not used in everyday language and can be easily described using characters and words. Same goes for the accent characters, we are more concerned with identifying the text in the audio rather than identifying the accent.

- CommonVoice dataset is very dirty, it has a lot of accent, unicode and chinese characters.

### Transducer

[Transducer](https://lorenlugosch.github.io/posts/2020/11/transducer/)


### Training

- Teacher forcing is essential for training the network, higher values lead to better loss descent during the training but will result in worser predictions during inference due to the model expecting a different distribution if the model is getting the correct inputs too often.

- CTC expects that the **first target sentence does not** have blanks. If the first target, does have blanks, it will output negative CTC loss.

- No need to worry about padding during CTC training because the loss won't calculate after the target length, in case of padded targets.

- Cannot use LSTM Layer (should use LSTM Cell) as indicated by [CMU LAS Homework](https://deeplearning.cs.cmu.edu/F20/document/homework/Homework_4_2.pdf). Will have to dig in deeper to know why and does it apply to GRU too?