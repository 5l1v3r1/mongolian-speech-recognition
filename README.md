An online demo trained with a Mongolian proprietary dataset (WER 14%): [https://chimege.mn/](https://chimege.mn/).

In this repo, following papers are implemented:
* [QuartzNet: Deep Automatic Speech Recognition with 1D Time-Channel Separable Convolutions](https://arxiv.org/abs/1910.10261)
* [An End-to-End Trainable Neural Network for Image-based Sequence Recognition and Its Application to Scene Text Recognition](https://arxiv.org/abs/1507.05717)
  * speech recognition as optical character recognition
  * for online demo with WER 14%, see [https://chimege.mn/](https://chimege.mn/).

This repo is partially based on:
* decoder from [SeanNaren/deepspeech.pytorch](https://github.com/SeanNaren/deepspeech.pytorch)
* Jasper/QuartzNet blocks from [NVIDIA/NeMo](https://github.com/NVIDIA/NeMo)

## Training
1. Install PyTorch>=1.2 with conda
2. Install remaining dependencies: `pip install -r requirements.txt`
3. Download the Mongolian Bible dataset: `cd datasets && python dl_mbspeech.py`
4. Pre compute the mel spectrograms: `python preprop_dataset.py --dataset mbspeech`
5. Train: `python train.py --dataset mbspeech`
   * logs for the TensorBoard are saved in the folder `logdir`

## Results
During the training, the ground truth and recognized texts are logged into the TensorBoard.
Because the dataset contains only a single person, the predicted texts from the validation set
should be already recognizable after few epochs:

**EXPECTED:**
```
аливаа цус хувцсан дээр үсрэхэд цус үсэрсэн хэсгийг та нар ариун газарт угаагтун
```
**PREDICTED:**
```
аливаа цус хувцсан дээр үсэрхэд цус усарсан хэсхийг та нар ариун газарт угаагтун
```

The dataset contains only first 3 books of the Mongolian Bible. You can validate your trained model
from other Bible books (download them from https://www.bible.com/versions/1590-2013-ariun-bibli-2013 as mp3 file).

To transcribe an audio file using a pretrained model, use following commands:
```
# download a pretrained model
wget https://www.dropbox.com/s/9wan945h110wmyc/epoch-0182-fb4c392.pth
# switch to the commit where the model was trained
git checkout fb4c392
# evaluate an audio file
python eval.py --checkpoint=epoch-0182-fb4c392.pth test.mp3
```

For fun, you can also generate an audio with a Mongolian TTS and try to recognize it.
The following code generates an audio with the
[TTS of the Mongolian National University](http://172.104.34.197/nlp-web-demo/)
and does speech recognition on that generated audio:
```
# generate audio for 'Миний төрсөн нутаг Монголын сайхан орон'
wget -O test.wav "http://172.104.34.197/nlp-web-demo/tts?voice=1&text=Миний төрсөн нутаг Монголын сайхан орон."
# speech recognition on that TTS generated audio
python eval.py --checkpoint=epoch-0182-fb4c392.pth test.wav
# will output: 'биний төрсн нуутөр мөнголын сайхон орн'
```

It is also possible to use a KenLM binary model. First download it from
[tugstugi/mongolian-nlp](https://github.com/tugstugi/mongolian-nlp#mongolian-language-model).
After that, execute:
```
python eval.py --checkpoint=path/to/checkpoint --lm=mn_5gram.binary --alpha 0.3 test.wav
```

## Contribute
If you are Mongolian and want to help us, please record your voice on [Common Voice](https://voice.mozilla.org/mn/speak).
