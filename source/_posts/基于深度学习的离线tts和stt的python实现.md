---
title: 基于深度学习的离线tts和stt的python实现
date: 2025-04-06 05:05:00
tags: []
categories: 默认
---

安装模型（stt用的vosk-model-cn-0.22，tts用的kokoro-v1.1-zh）

```zsh
# vosk-model-cn-0.22
wget https://alphacephei.com/vosk/models/vosk-model-cn-0.22.zip
unzip vosk-model-cn-0.22.zip
rm vosk-model-cn-0.22.zip

# kokoro-v1.1-zh
mkdir kokoro-v1.1-zh
cd kokoro-v1.1-zh
wget https://github.com/thewh1teagle/kokoro-onnx/releases/download/model-files-v1.1/kokoro-v1.1-zh.onnx
wget https://github.com/thewh1teagle/kokoro-onnx/releases/download/model-files-v1.1/voices-v1.1-zh.bin
wget https://huggingface.co/hexgrad/Kokoro-82M-v1.1-zh/raw/main/config.json
cd ..
```

安装依赖

```plaintext
addict==2.4.0
annotated-types==0.7.0
anyio==4.9.0
attrs==25.3.0
babel==2.17.0
certifi==2025.1.31
cffi==1.17.1
charset-normalizer==3.4.1
cn2an==0.5.23
colorama==0.4.6
coloredlogs==15.0.1
colorlog==6.9.0
csvw==3.5.1
distro==1.9.0
dlinfo==2.0.0
espeakng-loader==0.2.4
exceptiongroup==1.2.2
flatbuffers==25.2.10
h11==0.14.0
httpcore==1.0.7
httpx==0.28.1
humanfriendly==10.0
idna==3.10
isodate==0.7.2
jieba==0.42.1
jiter==0.9.0
joblib==1.4.2
jsonschema==4.23.0
jsonschema-specifications==2024.10.1
kokoro-onnx==0.4.7
language-tags==1.2.0
misaki==0.9.3
mpmath==1.3.0
numpy==2.2.4
onnxruntime==1.21.0
openai==1.70.0
ordered-set==4.1.0
packaging==24.2
phonemizer-fork==3.3.1
proces==0.1.7
protobuf==6.30.2
PyAudio==0.2.14
pycparser==2.22
pydantic==2.11.2
pydantic_core==2.33.1
pyparsing==3.2.3
pypinyin==0.54.0
pypinyin-dict==0.9.0
python-dateutil==2.9.0.post0
rdflib==7.1.4
referencing==0.36.2
regex==2024.11.6
requests==2.32.3
rfc3986==1.5.0
rpds-py==0.24.0
segments==2.3.0
six==1.17.0
sniffio==1.3.1
sounddevice==0.5.1
srt==3.5.3
sympy==1.13.3
tqdm==4.67.1
typing-inspection==0.4.0
typing_extensions==4.13.1
uritemplate==4.1.1
urllib3==2.3.0
vosk==0.3.44
websockets==15.0.1
```

tts.py

```python
import sounddevice as sd
import kokoro_onnx as kokoro
from misaki import zh
import asyncio

kokoro.MAX_PHONEME_LENGTH = 80


class TTS:
    def __init__(self):
        self.g2p = zh.ZHG2P(version="1.1")
        self.voice = "zm_009"
        self.speed = 1.0
        self.model = kokoro.Kokoro(
            "kokoro-v1.1-zh/model.onnx",
            "kokoro-v1.1-zh/voices.bin",
            vocab_config="kokoro-v1.1-zh/config.json",
        )
        self.model.create(
            self.get_phonemes("你好，世界！"),
            voice=self.voice,
            speed=self.speed,
            is_phonemes=True,
        )
        print("TTS initialized")

    def set_speed(self, speed: float = 1.0):
        self.speed = speed

    def get_phonemes(self, text: str) -> str:
        phonemes, _ = self.g2p(text)
        return phonemes

    def speak(self, text: str):
        async def stream_audio():
            stream = self.model.create_stream(
                self.get_phonemes(text),
                voice=self.voice,
                speed=self.speed,
                is_phonemes=True,
            )
            async for audio, audio_rate in stream:
                sd.play(audio, audio_rate)
                sd.wait()

        asyncio.run(stream_audio())
```

stt.py

```python
import json
import vosk
import pyaudio


class STT:
    def __init__(self):
        self.recognizer = vosk.KaldiRecognizer(vosk.Model("vosk-model-cn-0.22"), 16000)
        self.mic = pyaudio.PyAudio()
        self.stream = self.mic.open(
            format=pyaudio.paInt16,
            channels=1,
            rate=16000,
            input=True,
            frames_per_buffer=4096,
        )
        self.stop_listening = False
        print("STT initialized")

    def listen(self, on_result: callable):
        while not self.stop_listening:
            if self.recognizer.AcceptWaveform(
                self.stream.read(4096, exception_on_overflow=False)
            ):
                result = json.loads(self.recognizer.Result())
                text: str = result.get("text", "")
                if text:
                    on_result(text.replace(" ", ""))
        self.stream.stop_stream()
        self.stream.close()
        self.mic.terminate()

    def stop(self):
        self.stop_listening = True
```

用法

```python
import tts
import stt

tts_engine = tts.TTS()
stt_engine = stt.STT()

def call(text: str):
    tts_engine.speak(text)
    # your logic
    #stt_engine.stop()  # stop mic listen

stt_engine.listen(call)  # start mic listen
```
