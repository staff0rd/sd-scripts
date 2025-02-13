This repository contains training, generation and utility scripts for Stable Diffusion.

[__Change History__](#change-history) is moved to the bottom of the page.
更新履歴は[ページ末尾](#change-history)に移しました。

[日本語版README](./README-ja.md)

For easier use (GUI and PowerShell scripts etc...), please visit [the repository maintained by bmaltais](https://github.com/bmaltais/kohya_ss). Thanks to @bmaltais!

This repository contains the scripts for:

* DreamBooth training, including U-Net and Text Encoder
* Fine-tuning (native training), including U-Net and Text Encoder
* LoRA training
* Texutl Inversion training
* Image generation
* Model conversion (supports 1.x and 2.x, Stable Diffision ckpt/safetensors and Diffusers)

__Stable Diffusion web UI now seems to support LoRA trained by ``sd-scripts``.__ Thank you for great work!!! 

## About requirements.txt

These files do not contain requirements for PyTorch. Because the versions of them depend on your environment. Please install PyTorch at first (see installation guide below.) 

The scripts are tested with PyTorch 1.12.1 and 1.13.0, Diffusers 0.10.2.

## Links to how-to-use documents

Most of the documents are written in Japanese.

[English translation by darkstorm2150 is here](https://github.com/darkstorm2150/sd-scripts#links-to-usage-documentation). Thanks to darkstorm2150!

* [Training guide - common](./docs/train_README-ja.md) : data preparation, options etc... 
  * [Chinese version](./docs/train_README-zh.md)
* [Dataset config](./docs/config_README-ja.md) 
* [DreamBooth training guide](./docs/train_db_README-ja.md)
* [Step by Step fine-tuning guide](./docs/fine_tune_README_ja.md):
* [training LoRA](./docs/train_network_README-ja.md)
* [training Textual Inversion](./docs/train_ti_README-ja.md)
* [Image generation](./docs/gen_img_README-ja.md)
* note.com [Model conversion](https://note.com/kohya_ss/n/n374f316fe4ad)

## Windows Required Dependencies

Python 3.10.6 and Git:

- Python 3.10.6: https://www.python.org/ftp/python/3.10.6/python-3.10.6-amd64.exe
- git: https://git-scm.com/download/win

Give unrestricted script access to powershell so venv can work:

- Open an administrator powershell window
- Type `Set-ExecutionPolicy Unrestricted` and answer A
- Close admin powershell window

## Windows Installation

Open a regular Powershell terminal and type the following inside:

```powershell
git clone https://github.com/kohya-ss/sd-scripts.git
cd sd-scripts

python -m venv venv
.\venv\Scripts\activate

pip install torch==1.12.1+cu116 torchvision==0.13.1+cu116 --extra-index-url https://download.pytorch.org/whl/cu116
pip install --upgrade -r requirements.txt
pip install -U -I --no-deps https://github.com/C43H66N12O12S2/stable-diffusion-webui/releases/download/f/xformers-0.0.14.dev0-cp310-cp310-win_amd64.whl

cp .\bitsandbytes_windows\*.dll .\venv\Lib\site-packages\bitsandbytes\
cp .\bitsandbytes_windows\cextension.py .\venv\Lib\site-packages\bitsandbytes\cextension.py
cp .\bitsandbytes_windows\main.py .\venv\Lib\site-packages\bitsandbytes\cuda_setup\main.py

accelerate config
```

update: ``python -m venv venv`` is seemed to be safer than ``python -m venv --system-site-packages venv`` (some user have packages in global python).

Answers to accelerate config:

```txt
- This machine
- No distributed training
- NO
- NO
- NO
- all
- fp16
```

note: Some user reports ``ValueError: fp16 mixed precision requires a GPU`` is occurred in training. In this case, answer `0` for the 6th question: 
``What GPU(s) (by id) should be used for training on this machine as a comma-separated list? [all]:`` 

(Single GPU with id `0` will be used.)

### about PyTorch and xformers

Other versions of PyTorch and xformers seem to have problems with training.
If there is no other reason, please install the specified version.

### Optional: Use Lion8bit

For Lion8bit, you need to upgrade `bitsandbytes` to 0.38.0 or later. Uninstall `bitsandbytes`, and for Windows, install the Windows version whl file from [here](https://github.com/jllllll/bitsandbytes-windows-webui) or other sources, like:

```powershell
pip install https://github.com/jllllll/bitsandbytes-windows-webui/raw/main/bitsandbytes-0.38.1-py3-none-any.whl
```

For upgrading, upgrade this repo with `pip install .`, and upgrade necessary packages manually.

## Upgrade

When a new release comes out you can upgrade your repo with the following command:

```powershell
cd sd-scripts
git pull
.\venv\Scripts\activate
pip install --use-pep517 --upgrade -r requirements.txt
```

Once the commands have completed successfully you should be ready to use the new version.

## Credits

The implementation for LoRA is based on [cloneofsimo's repo](https://github.com/cloneofsimo/lora). Thank you for great work!

The LoRA expansion to Conv2d 3x3 was initially released by cloneofsimo and its effectiveness was demonstrated at [LoCon](https://github.com/KohakuBlueleaf/LoCon) by KohakuBlueleaf. Thank you so much KohakuBlueleaf!

## License

The majority of scripts is licensed under ASL 2.0 (including codes from Diffusers, cloneofsimo's and LoCon), however portions of the project are available under separate license terms:

[Memory Efficient Attention Pytorch](https://github.com/lucidrains/memory-efficient-attention-pytorch): MIT

[bitsandbytes](https://github.com/TimDettmers/bitsandbytes): MIT

[BLIP](https://github.com/salesforce/BLIP): BSD-3-Clause

## Change History

### 31 May 2023, 2023/05/31

- Show warning when image caption file does not exist during training. [PR #533](https://github.com/kohya-ss/sd-scripts/pull/533) Thanks to TingTingin!
  - Warning is also displayed when using class+identifier dataset. Please ignore if it is intended.
- `train_network.py` now supports merging network weights before training. [PR #542](https://github.com/kohya-ss/sd-scripts/pull/542) Thanks to u-haru!
  - `--base_weights` option specifies LoRA or other model files (multiple files are allowed) to merge.
  - `--base_weights_multiplier` option specifies multiplier of the weights to merge (multiple values are allowed). If omitted or less than `base_weights`, 1.0 is used.
  - This is useful for incremental learning. See PR for details.
- Show warning and continue training when uploading to HuggingFace fails.

- 学習時に画像のキャプションファイルが存在しない場合、警告が表示されるようになりました。 [PR #533](https://github.com/kohya-ss/sd-scripts/pull/533) TingTingin氏に感謝します。
  - class+identifier方式のデータセットを利用している場合も警告が表示されます。意図している通りの場合は無視してください。
- `train_network.py` に学習前にモデルにnetworkの重みをマージする機能が追加されました。 [PR #542](https://github.com/kohya-ss/sd-scripts/pull/542) u-haru氏に感謝します。
  - `--base_weights` オプションでLoRA等のモデルファイル（複数可）を指定すると、それらの重みをマージします。
  - `--base_weights_multiplier` オプションでマージする重みの倍率（複数可）を指定できます。省略時または`base_weights`よりも数が少ない場合は1.0になります。
  - 差分追加学習などにご利用ください。詳細はPRをご覧ください。
- HuggingFaceへのアップロードに失敗した場合、警告を表示しそのまま学習を続行するよう変更しました。

### 25 May 2023, 2023/05/25

- [D-Adaptation v3.0](https://github.com/facebookresearch/dadaptation) is now supported. [PR #530](https://github.com/kohya-ss/sd-scripts/pull/530) Thanks to sdbds!
  - `--optimizer_type` now accepts `DAdaptAdamPreprint`, `DAdaptAdanIP`, and `DAdaptLion`.
  - `DAdaptAdam` is now new. The old `DAdaptAdam` is available with `DAdaptAdamPreprint`.
  - Simply specifying `DAdaptation` will use `DAdaptAdamPreprint` (same behavior as before).
  - You need to install D-Adaptation v3.0. After activating venv, please do `pip install -U dadaptation`.
  - See PR and D-Adaptation documentation for details.
- [D-Adaptation v3.0](https://github.com/facebookresearch/dadaptation)がサポートされました。 [PR #530](https://github.com/kohya-ss/sd-scripts/pull/530)  sdbds氏に感謝します。
  - `--optimizer_type`に`DAdaptAdamPreprint`、`DAdaptAdanIP`、`DAdaptLion` が追加されました。
  - `DAdaptAdam`が新しくなりました。今までの`DAdaptAdam`は`DAdaptAdamPreprint`で使用できます。
  - 単に `DAdaptation` を指定すると`DAdaptAdamPreprint`が使用されます（今までと同じ動き）。
  - D-Adaptation v3.0のインストールが必要です。venvを有効にした後 `pip install -U dadaptation` としてください。
  - 詳細はPRおよびD-Adaptationのドキュメントを参照してください。

### 22 May 2023, 2023/05/22

- Fixed several bugs.
  - The state is saved even when the `--save_state` option is not specified in `fine_tune.py` and `train_db.py`. [PR #521](https://github.com/kohya-ss/sd-scripts/pull/521) Thanks to akshaal!
  - Cannot load LoRA without `alpha`. [PR #527](https://github.com/kohya-ss/sd-scripts/pull/527) Thanks to Manjiz!
  - Minor changes to console output during sample generation. [PR #515](https://github.com/kohya-ss/sd-scripts/pull/515) Thanks to yanhuifair!
- The generation script now uses xformers for VAE as well.
- いくつかのバグ修正を行いました。
  -  `fine_tune.py`と`train_db.py`で`--save_state`オプション未指定時にもstateが保存される。 [PR #521](https://github.com/kohya-ss/sd-scripts/pull/521) akshaal氏に感謝します。
  - `alpha`を持たないLoRAを読み込めない。[PR #527](https://github.com/kohya-ss/sd-scripts/pull/527) Manjiz氏に感謝します。
  - サンプル生成時のコンソール出力の軽微な変更。[PR #515](https://github.com/kohya-ss/sd-scripts/pull/515) yanhuifair氏に感謝します。
- 生成スクリプトでVAEについてもxformersを使うようにしました。

### 16 May 2023, 2023/05/16

- Fixed an issue where an error would occur if the encoding of the prompt file was different from the default. [PR #510](https://github.com/kohya-ss/sd-scripts/pull/510) Thanks to sdbds!
  - Please save the prompt file in UTF-8.
- プロンプトファイルのエンコーディングがデフォルトと異なる場合にエラーが発生する問題を修正しました。 [PR #510](https://github.com/kohya-ss/sd-scripts/pull/510) sdbds氏に感謝します。
  - プロンプトファイルはUTF-8で保存してください。

### 15 May 2023, 2023/05/15

- Added [English translation of documents](https://github.com/darkstorm2150/sd-scripts#links-to-usage-documentation) by darkstorm2150. Thank you very much!
- The prompt for sample generation during training can now be specified in `.toml` or `.json`. [PR #504](https://github.com/kohya-ss/sd-scripts/pull/504) Thanks to Linaqruf!
  - For details on prompt description, please see the PR.

- darkstorm2150氏に[ドキュメント類を英訳](https://github.com/darkstorm2150/sd-scripts#links-to-usage-documentation)していただきました。ありがとうございます！
- 学習中のサンプル生成のプロンプトを`.toml`または`.json`で指定可能になりました。 [PR #504](https://github.com/kohya-ss/sd-scripts/pull/504) Linaqruf氏に感謝します。
  - プロンプト記述の詳細は当該PRをご覧ください。

### 11 May 2023, 2023/05/11

- Added an option `--dim_from_weights` to `train_network.py` to automatically determine the dim(rank) from the weight file. [PR #491](https://github.com/kohya-ss/sd-scripts/pull/491) Thanks to AI-Casanova!
  - It is useful in combination with `resize_lora.py`. Please see the PR for details.
- Fixed a bug where the noise resolution was incorrect with Multires noise. [PR #489](https://github.com/kohya-ss/sd-scripts/pull/489) Thanks to sdbds!
  - Please see the PR for details.
- The image generation scripts can now use img2img and highres fix at the same time.
- Fixed a bug where the hint image of ControlNet was incorrectly BGR instead of RGB in the image generation scripts.
- Added a feature to the image generation scripts to use the memory-efficient VAE.
  - If you specify a number with the `--vae_slices` option, the memory-efficient VAE will be used. The maximum output size will be larger, but it will be slower. Please specify a value of about `16` or `32`.
  - The implementation of the VAE is in `library/slicing_vae.py`.

- `train_network.py`にdim(rank)を重みファイルから自動決定するオプション`--dim_from_weights`が追加されました。 [PR #491](https://github.com/kohya-ss/sd-scripts/pull/491) AI-Casanova氏に感謝します。
  - `resize_lora.py`と組み合わせると有用です。詳細はPRもご参照ください。
- Multires noiseでノイズ解像度が正しくない不具合が修正されました。 [PR #489](https://github.com/kohya-ss/sd-scripts/pull/489)  sdbds氏に感謝します。
  - 詳細は当該PRをご参照ください。
- 生成スクリプトでimg2imgとhighres fixを同時に使用できるようにしました。
- 生成スクリプトでControlNetのhint画像が誤ってBGRだったのをRGBに修正しました。
- 生成スクリプトで省メモリ化VAEを使えるよう機能追加しました。
  - `--vae_slices`オプションに数値を指定すると、省メモリ化VAEを用います。出力可能な最大サイズが大きくなりますが、遅くなります。`16`または`32`程度の値を指定してください。
  - VAEの実装は`library/slicing_vae.py`にあります。

### 7 May 2023, 2023/05/07

- The documentation has been moved to the `docs` folder. If you have links, please change them.
- Removed `gradio` from `requirements.txt`.
- DAdaptAdaGrad, DAdaptAdan, and DAdaptSGD are now supported by DAdaptation. [PR#455](https://github.com/kohya-ss/sd-scripts/pull/455) Thanks to sdbds!
  - DAdaptation needs to be installed. Also, depending on the optimizer, DAdaptation may need to be updated. Please update with `pip install --upgrade dadaptation`.
- Added support for pre-calculation of LoRA weights in image generation scripts. Specify `--network_pre_calc`.
  - The prompt option `--am` is available. Also, it is disabled when Regional LoRA is used.
- Added Adaptive noise scale to each training script. Specify a number with `--adaptive_noise_scale` to enable it.
  - __Experimental option. It may be removed or changed in the future.__
  - This is an original implementation that automatically adjusts the value of the noise offset according to the absolute value of the mean of each channel of the latents. It is expected that appropriate noise offsets will be set for bright and dark images, respectively.
  - Specify it together with `--noise_offset`.
  - The actual value of the noise offset is calculated as `noise_offset + abs(mean(latents, dim=(2,3))) * adaptive_noise_scale`. Since the latent is close to a normal distribution, it may be a good idea to specify a value of about 1/10 to the same as the noise offset.
  - Negative values can also be specified, in which case the noise offset will be clipped to 0 or more.
- Other minor fixes.

- ドキュメントを`docs`フォルダに移動しました。リンク等を張られている場合は変更をお願いいたします。
- `requirements.txt`から`gradio`を削除しました。
- DAdaptationで新しくDAdaptAdaGrad、DAdaptAdan、DAdaptSGDがサポートされました。[PR#455](https://github.com/kohya-ss/sd-scripts/pull/455) sdbds氏に感謝します。
  - dadaptationのインストールが必要です。またオプティマイザによってはdadaptationの更新が必要です。`pip install --upgrade dadaptation`で更新してください。
- 画像生成スクリプトでLoRAの重みの事前計算をサポートしました。`--network_pre_calc`を指定してください。
  - プロンプトオプションの`--am`が利用できます。またRegional LoRA使用時には無効になります。
- 各学習スクリプトにAdaptive noise scaleを追加しました。`--adaptive_noise_scale`で数値を指定すると有効になります。
  - __実験的オプションです。将来的に削除、仕様変更される可能性があります。__
  - Noise offsetの値を、latentsの各チャネルの平均値の絶対値に応じて自動調整するオプションです。独自の実装で、明るい画像、暗い画像に対してそれぞれ適切なnoise offsetが設定されることが期待されます。
  - `--noise_offset` と同時に指定してください。
  - 実際のNoise offsetの値は `noise_offset + abs(mean(latents, dim=(2,3))) * adaptive_noise_scale` で計算されます。 latentは正規分布に近いためnoise_offsetの1/10～同程度の値を指定するとよいかもしれません。
  - 負の値も指定でき、その場合はnoise offsetは0以上にclipされます。
- その他の細かい修正を行いました。

Please read [Releases](https://github.com/kohya-ss/sd-scripts/releases) for recent updates.
最近の更新情報は [Release](https://github.com/kohya-ss/sd-scripts/releases) をご覧ください。

### Naming of LoRA

The LoRA supported by `train_network.py` has been named to avoid confusion. The documentation has been updated. The following are the names of LoRA types in this repository.

1. __LoRA-LierLa__ : (LoRA for __Li__ n __e__ a __r__  __La__ yers)

    LoRA for Linear layers and Conv2d layers with 1x1 kernel

2. __LoRA-C3Lier__ : (LoRA for __C__ olutional layers with __3__ x3 Kernel and  __Li__ n __e__ a __r__ layers)

    In addition to 1., LoRA for Conv2d layers with 3x3 kernel 
    
LoRA-LierLa is the default LoRA type for `train_network.py` (without `conv_dim` network arg). LoRA-LierLa can be used with [our extension](https://github.com/kohya-ss/sd-webui-additional-networks) for AUTOMATIC1111's Web UI, or with the built-in LoRA feature of the Web UI.

To use LoRA-C3Lier with Web UI, please use our extension.

### LoRAの名称について

`train_network.py` がサポートするLoRAについて、混乱を避けるため名前を付けました。ドキュメントは更新済みです。以下は当リポジトリ内の独自の名称です。

1. __LoRA-LierLa__ : (LoRA for __Li__ n __e__ a __r__  __La__ yers、リエラと読みます)

    Linear 層およびカーネルサイズ 1x1 の Conv2d 層に適用されるLoRA

2. __LoRA-C3Lier__ : (LoRA for __C__ olutional layers with __3__ x3 Kernel and  __Li__ n __e__ a __r__ layers、セリアと読みます)

    1.に加え、カーネルサイズ 3x3 の Conv2d 層に適用されるLoRA

LoRA-LierLa は[Web UI向け拡張](https://github.com/kohya-ss/sd-webui-additional-networks)、またはAUTOMATIC1111氏のWeb UIのLoRA機能で使用することができます。

LoRA-C3Lierを使いWeb UIで生成するには拡張を使用してください。

## Sample image generation during training
  A prompt file might look like this, for example

```
# prompt 1
masterpiece, best quality, (1girl), in white shirts, upper body, looking at viewer, simple background --n low quality, worst quality, bad anatomy,bad composition, poor, low effort --w 768 --h 768 --d 1 --l 7.5 --s 28

# prompt 2
masterpiece, best quality, 1boy, in business suit, standing at street, looking back --n (low quality, worst quality), bad anatomy,bad composition, poor, low effort --w 576 --h 832 --d 2 --l 5.5 --s 40
```

  Lines beginning with `#` are comments. You can specify options for the generated image with options like `--n` after the prompt. The following can be used.

  * `--n` Negative prompt up to the next option.
  * `--w` Specifies the width of the generated image.
  * `--h` Specifies the height of the generated image.
  * `--d` Specifies the seed of the generated image.
  * `--l` Specifies the CFG scale of the generated image.
  * `--s` Specifies the number of steps in the generation.

  The prompt weighting such as `( )` and `[ ]` are working.

## サンプル画像生成
プロンプトファイルは例えば以下のようになります。

```
# prompt 1
masterpiece, best quality, (1girl), in white shirts, upper body, looking at viewer, simple background --n low quality, worst quality, bad anatomy,bad composition, poor, low effort --w 768 --h 768 --d 1 --l 7.5 --s 28

# prompt 2
masterpiece, best quality, 1boy, in business suit, standing at street, looking back --n (low quality, worst quality), bad anatomy,bad composition, poor, low effort --w 576 --h 832 --d 2 --l 5.5 --s 40
```

  `#` で始まる行はコメントになります。`--n` のように「ハイフン二個＋英小文字」の形でオプションを指定できます。以下が使用可能できます。

  * `--n` Negative prompt up to the next option.
  * `--w` Specifies the width of the generated image.
  * `--h` Specifies the height of the generated image.
  * `--d` Specifies the seed of the generated image.
  * `--l` Specifies the CFG scale of the generated image.
  * `--s` Specifies the number of steps in the generation.

  `( )` や `[ ]` などの重みづけも動作します。

