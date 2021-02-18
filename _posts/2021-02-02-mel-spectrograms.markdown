---
layout: post
title:      "Mel-Spectrograms"
date:       2021-02-02 15:37:54 -0500
permalink:  mel-spectrograms
---

Spectrograms are immensely useful tools that we can use to help dissect information from audio files and process into images. In a spectrogram, the horizontal axis represents time, the vertical axis represents frequency, and the color intensity represents the amplitude of a frequency at a certain point in time. In case you can't quite picture that, here is an example of what a spectrogram looks like: 

<img src='https://upload.wikimedia.org/wikipedia/commons/thumb/c/c5/Spectrogram-19thC.png/600px-Spectrogram-19thC.png' alt='Spectrogram Image'>

The cool part about these images is that we can actually use them to potentially help with classification of things like: diagnosing heart diseases through heart beat audio, identifying certain infections/diseases through coughing audio, detecting and differentiating between sounds and classifying them into groups, etc.

### Creating Signal and Sample Rate

So how exactly can we create spectrograms from audio? First we will import the the necessary libraries, and then load in our target audio file.

```
import librosa, librosa.display
import IPython.display as ipd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

signal, sr = librosa.load(librosa.util.example('brahms'))
```

When we use librosa.load() to load an audio file, we need to specify two variables to take in the signal of the audio and the sample rate. The signal is a 1-dimensional numpy array that contains a number of values that is equal to the sample rate multiplied by the duration of the audio file. Each of these values in the signal is the amplitude of the audio file at a certain point in time. The sample rate (sr) by default is 22050, which means that for every second there are 22,050 samples. To view our loaded in audio, we can use librosa.display.waveplot().

```
plt.figure(figsize=(6, 4))
librosa.display.waveplot(signal, sr=sr)
plt.title('Waveplot', fontdict=dict(size=15))
plt.xlabel('Time', fontdict=dict(size=12))
plt.ylabel('Amplitude', fontdict=dict(size=12))
plt.show()
```

<insert pic here>

Using the above code we can see our audio file in its waveform. We can even use another library called IPython.display to hear the audio with only one line of code!

```
ipd.Audio(signal, rate=sr)
```

### Fast Fourier Transformation

A fast-fourier transformation (FFT) takes our audio from the time domain into the frequency domain. What it does is it decomposes complex periodic sounds into a sum of sine waves oscillating at different frequencies. This allows us to know the different elements that make up the sound. Essentially, it is a snapshot of the audio information.

A FFT is a 1-dimensional numpy array that has as many values as the total number of samples we have in the waveform. For each of these values, we have a complex value which gets the magnitude for each of the values. These magnitudes tell us the level of contribution for each frequency in the sound. The larger the magnitude, the greater the contribution for that frequency to the overall sound in the audio.

```
#Fast Fourier Transformation on our audio signal
fast_fourier_transf = np.fft.fft(signal)

# These magnitudes indicate the contribution of each frequency in the sound
magnitude = np.abs(fast_fourier_transf)

# mapping the magnitude to the relative frequency bins using np.linspace()
frequency = np.linspace(0, sr, len(magnitude))

# We only need the first half of the magnitude and frequency to visualize the FFT

left_mag = magnitude[:int(len(magnitude)/2)]
left_freq = frequency[:int(len(frequency)/2)]

plt.plot(left_freq, left_mag)
plt.title('Fast Fourier Transform')
plt.xlabel('Frequency')
plt.ylabel('Magnitude')
plt.show()
```

The only issue with the FFT is the fact that it is static; there is no time associated with this plot. So in order to incorporate time into our audio to see what frequencies impact at what time, we should use the Short-Time Fourier Transformation.

### Short-time Fourier Transformation

Give us info about the amplitude by using both frequency and time. 

The idea is that we don't perform a fast fourier transformation across the entire signal, but instead we will take small segments, or frames, of the audio signal and apply FFTs to each of these frames. Doing so allows us to preserve information about the time and the way the audio signal evolves over time. Going further, the frames will overlap each other as we slide across the audio signal. How far the frame slides is determined by the hop length, which tells the function how many sample rates to the right it should slide when we create the next frame. Let's go ahead and put this into code.

```
# this is the number of samples in a window per fast fourier transform
n_fft = 2048

# The amount of samples we are shifting each fourier transform (to the right)
hop_length = 512

# Short-time Fourier Transformation on our audio data
audio_stft = librosa.core.stft(signal, hop_length=hop_length, n_fft=n_fft)

# gathering the absolute values for all values in our audio_stft variable
spectrogram = np.abs(audio_stft)

# Plotting the short-time Fourier Transformation
plt.figure(figsize=(8, 7))

# Using librosa.display.specshow() function to create our spectrogram
librosa.display.specshow(spectrogram, sr=sr, x_axis='time', y_axis='hz', hop_length=hop_length)
plt.colorbar(label='Amplitude')
plt.title('Spectrogram (amp)')
plt.xlabel('Time')
plt.ylabel('Frequency')
plt.show()
```

As we can see, most of these frequencies contribute very little to the overall amplitude in the sound. A way for us to visualize loudness, which is not linear but logarithmic, is to convert our spectrograms from amplitude to decibels. 

```
# Short-time Fourier Transformation on our audio data
audio_stft = librosa.core.stft(signal, hop_length=hop_length, n_fft=n_fft)

# gathering the absolute values for all values in our audio_stft variable
spectrogram = np.abs(audio_stft)

# Converting the amplitude to decibels
log_spectro = librosa.amplitude_to_db(spectrogram)

# Plotting the short-time Fourier Transformation
plt.figure(figsize=(8, 7))

# Using librosa.display.specshow() function to create our spectrogram
librosa.display.specshow(log_spectro, sr=sr, x_axis='time', y_axis='hz', 
                         hop_length=hop_length, cmap='magma')
plt.colorbar(label='Decibels')
plt.title('Spectrogram (dB)')
plt.xlabel('Time')
plt.ylabel('Frequency')
plt.show()
```

### Creating the Mel-Spectrogram



```
mel_signal = librosa.feature.melspectrogram(y=signal, sr=sr, hop_length=hop_length, 
                                              n_fft=n_fft)
spectrogram = np.abs(mel_signal)
power_to_db = librosa.power_to_db(spectrogram, ref=np.max)
plt.figure(figsize=(8, 7))
librosa.display.specshow(power_to_db, sr=sr, x_axis='time', y_axis='mel', cmap='magma', 
              hop_length=hop_length)
plt.colorbar(label='dB')
plt.show()
```
