---
title: SillyTavern酒馆教程(三) - 给角色形象和声音
date: 2024-9-9
cover: https://oydqrbee757owwwj.public.blob.vercel-storage.com/Snipaste_2024-09-07_13-48-05-SQWMYnATqu78zzoqVytrFZof5T21WC.png
categories: 
  - AI
tags: 
  - SillyTavern酒馆
  - live2D
  - GPT-SoVITS
  - webTTS
---
通过之前的两篇教程，相必大家现在已经可以正常和ai角色进行对话了，但是我们怎么能止步于此？本篇来教大家如何给自己的ai角色自己喜欢的形象和声音。
## 设置形象
### 下载live2D模型
首先，我们需要先获取到live2D的模型。可以自行搜索live2D获取。

这里我贴几个免费的模型：
- [【免费Live2D模型】小魔女一键出道+自带直播UI+舰长礼物稿哔哩哔哩bilibili](https://www.bilibili.com/video/BV1u14y1f7ik/?t=4&spm_id_from=333.1350.jump_directly&vd_source=b321576eba39aedaf1743088883e6c6b)
- https://github.com/Eikanya/Live2d-model

将下载下来的所有内容放到**SillyTavern\data\default-user\assets\live2d**目录下
![](images/post/ai/Snipaste_2024-09-08_10-39-14.png)

### 导入酒馆
先确保梯子打开了，然后按照下图进行配置
![](images/post/ai/f21e95e8-fed6-49d6-adfb-67f06a8e2772.png)
然后在这里会出现很多可以供下载的插件，选择live2d进行下载。

刷新页面，在扩展里可以看到live2D的插件，打开以后按图示进行配置
![](images/post/ai/Snipaste_2024-09-08_10-43-15.png)

## 接入声音
webTTS是b站一个听书狂魔基于GPT-SoVITS做的，这是他的主页：https://space.bilibili.com/3031494

### 在酒馆中添加tts插件
首先保证你的酒馆是最新版本，不是最新的请移步酒馆github进行更新。

然后将下面这段代码复制下来，重命名为**gsvi.js**。将它放在**SillyTavern\public\scripts\extensions\tts**文件夹下。

```javascript

import { saveTtsProviderSettings } from './index.js';

export { GSVITtsProvider };

class GSVITtsProvider {
    //########//
    // Config //
    //########//

    settings;
    ready = false;
    separator = '. ';

    characterList = {};
    voices = [];
    /**
     * Perform any text processing before passing to TTS engine.
     * @param {string} text Input text
     * @returns {string} Processed text
     */
    processText(text) {
        text = text.replace('<br>', '\n'); // Replace <br> with newline
        return text;
    }

    languageLabels = {
        'Multilingual': '多语种混合',
        'Chinese': '中文',
        'English': '英文',
        'Japanese': '日文',
        'Chinese-English': '中英混合',
        'Japanese-English': '日英混合',
    };
    defaultSettings = {
        provider_endpoint: 'http://127.0.0.1:8080',

        language: '多语种混合',

        cha_name: '',
        character_emotion: 'default',
        speed: 1,
        top_k: 15,
        top_p: 1,
        temperature: 1,
        batch_size: 10,
        stream: false,

        stream_chunk_size: 100,
    };

    // Added new methods to obtain characters and emotions
    async fetchCharacterList() {
        const response = await fetch(this.settings.provider_endpoint + '/config');
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}: ${await response.text()}`);
        }
        const characterList = await response.json();
        this.characterList = characterList;
        this.voices = Object.keys(characterList.speaker);

    }



    get settingsHtml() {
        let html = `
        <label for="gsvi_api_language">Text Language</label>
        <select id="gsvi_api_language">`;

        for (let language in this.languageLabels) {
            if (this.languageLabels[language] == this.settings?.language) {
                html += `<option value="${this.languageLabels[language]}" selected="selected">${language}</option>`;
                continue;
            }

            html += `<option value="${this.languageLabels[language]}">${language}</option>`;
        }

        html += `
        </select>
        <label>GSVI Settings:</label><br/>
        <label for="gsvi_tts_endpoint">Provider Endpoint:</label>
        <input id="gsvi_tts_endpoint" type="text" class="text_pole" maxlength="250" value="${this.defaultSettings.provider_endpoint}"/>


        <label for="gsvi_speed">Speed: <span id="gsvi_tts_speed_output">${this.defaultSettings.speed}</span></label>
        <input id="gsvi_speed" type="range" value="${this.defaultSettings.speed}" min="0.5" max="2" step="0.01" />

        <label for="gsvi_top_k">Top K: <span id="gsvi_top_k_output">${this.defaultSettings.top_k}</span></label>
        <input id="gsvi_top_k" type="range" value="${this.defaultSettings.top_k}" min="0" max="100" step="1" />

        <label for="gsvi_top_p">Top P: <span id="gsvi_top_p_output">${this.defaultSettings.top_p}</span></label>
        <input id="gsvi_top_p" type="range" value="${this.defaultSettings.top_p}" min="0" max="1" step="0.01" />

        <label for="gsvi_temperature">Temperature: <span id="gsvi_tts_temperature_output">${this.defaultSettings.temperature}</span></label>
        <input id="gsvi_temperature" type="range" value="${this.defaultSettings.temperature}" min="0.01" max="1" step="0.01" />

        <label for="gsvi_batch_size">Batch Size: <span id="gsvi_batch_size_output">${this.defaultSettings.batch_size}</span></label>
        <input id="gsvi_batch_size" type="range" value="${this.defaultSettings.batch_size}" min="1" max="35" step="1" />

        <label for="gsvi_tts_streaming" class="checkbox_label">
            <input id="gsvi_tts_streaming" type="checkbox" ${this.defaultSettings.stream ? 'checked' : ''}/>
            <span>Streaming</span>
        </label>

        <label for="gsvi_stream_chunk_size">Stream Chunk Size: <span id="gsvi_stream_chunk_size_output">${this.defaultSettings.stream_chunk_size}</span></label>
        <input id="gsvi_stream_chunk_size" type="range" value="${this.defaultSettings.stream_chunk_size}" min="100" max="400" step="1" />
        <p>
            For more information, visit the
            <a href="https://github.com/X-T-E-R/GPT-SoVITS-Inference" target="_blank">GSVI project page</a>.
        </p>
        `;

        return html;
    }

    onSettingsChange() {
        // Update provider settings based on input fields
        this.settings.provider_endpoint = $('#gsvi_tts_endpoint').val();
        this.settings.language = $('#gsvi_api_language').val();


        // Update the rest of TTS settings based on input fields
        this.settings.speed = parseFloat($('#gsvi_speed').val());
        this.settings.temperature = parseFloat($('#gsvi_temperature').val());
        this.settings.top_k = parseInt($('#gsvi_top_k').val(), 10);
        this.settings.top_p = parseFloat($('#gsvi_top_p').val());
        this.settings.batch_size = parseInt($('#gsvi_batch_size').val(), 10);
        this.settings.stream = $('#gsvi_tts_streaming').is(':checked');
        this.settings.stream_chunk_size = parseInt($('#gsvi_stream_chunk_size').val(), 10);

        // Update UI to reflect changes

        $('#gsvi_tts_speed_output').text(this.settings.speed);
        $('#gsvi_tts_temperature_output').text(this.settings.temperature);
        $('#gsvi_top_k_output').text(this.settings.top_k);
        $('#gsvi_top_p_output').text(this.settings.top_p);
        $('#gsvi_stream_chunk_size_output').text(this.settings.stream_chunk_size);
        $('#gsvi_batch_size_output').text(this.settings.batch_size);




        // Persist settings changes
        saveTtsProviderSettings();

    }

    async loadSettings(settings) {
        // Populate Provider UI given input settings
        if (Object.keys(settings).length === 0) {
            console.info('Using default TTS Provider settings');
        }

        // Only accept keys defined in defaultSettings
        this.settings = { ...this.defaultSettings, ...settings };

        // Fetch character and emotion list
        // Set initial values from the settings
        $('#gsvi_tts_endpoint').val(this.settings.provider_endpoint);
        $('#gsvi_api_language').val(this.settings.language);

        $('#gsvi_speed').val(this.settings.speed);
        $('#gsvi_temperature').val(this.settings.temperature);
        $('#gsvi_top_k').val(this.settings.top_k);
        $('#gsvi_top_p').val(this.settings.top_p);
        $('#gsvi_batch_size').val(this.settings.batch_size);
        $('#gsvi_tts_streaming').prop('checked', this.settings.stream);
        $('#gsvi_stream_chunk_size').val(this.settings.stream_chunk_size);

        // Update UI to reflect initial settings
        $('#gsvi_tts_speed_output').text(this.settings.speed);
        $('#gsvi_tts_temperature_output').text(this.settings.temperature);
        $('#gsvi_top_k_output').text(this.settings.top_k);
        $('#gsvi_top_p_output').text(this.settings.top_p);
        $('#gsvi_stream_chunk_size_output').text(this.settings.stream_chunk_size);

        // Register event listeners to update settings on user interaction
        // (Similar to before, ensure event listeners for character and emotion selection are included)
        // Register input/change event listeners to update settings on user interaction
        $('#gsvi_tts_endpoint').on('input', () => { this.onSettingsChange(); });
        $('#gsvi_api_language').on('change', () => { this.onSettingsChange(); });

        $('#gsvi_speed').on('input', () => { this.onSettingsChange(); });
        $('#gsvi_temperature').on('input', () => { this.onSettingsChange(); });
        $('#gsvi_top_k').on('input', () => { this.onSettingsChange(); });
        $('#gsvi_top_p').on('input', () => { this.onSettingsChange(); });
        $('#gsvi_batch_size').on('input', () => { this.onSettingsChange(); });
        $('#gsvi_tts_streaming').on('change', () => { this.onSettingsChange(); });
        $('#gsvi_stream_chunk_size').on('input', () => { this.onSettingsChange(); });

        await this.checkReady();
        console.debug('GSVI: Settings loaded');
    }



    // Perform a simple readiness check by trying to fetch voiceIds
    async checkReady() {
        await Promise.allSettled([this.fetchCharacterList()]);
    }

    async onRefreshClick() {
        return;
    }

    //#################//
    //  TTS Interfaces //
    //#################//

    async getVoice(voiceName) {
        if (this.voices.length == 0) {
            this.fetchCharacterList();
        }
        if (!this.voices.includes(voiceName)) {
            throw `TTS Voice name ${voiceName} not found`;
        }
        return { name: voiceName, voice_id: voiceName, preview_url: false, lang: 'zh-CN' };
    }

    async generateTts(text, voiceId) {
        const response = await this.fetchTtsGeneration(text, voiceId);
        return response;
    }

    //###########//
    // API CALLS //
    //###########//
    async fetchTtsVoiceObjects() {
        if (this.voices.length == 0) {
            await this.fetchCharacterList();
        }
        console.log(this.voices);
        const voices = this.voices.map(x => ({ name: x, voice_id: x, preview_url: false, lang: 'zh-CN' }));
        return voices;
    }


    async fetchTtsGeneration(inputText, voiceId) {
        console.info(`Generating new TTS for voice_id ${voiceId}`);


        const params = new URLSearchParams();
        params.append('text', inputText);
        params.append('spk', voiceId);
        params.append('text_language', this.settings.language);
        params.append('batch_size', this.settings.batch_size.toString());
        params.append('speed', this.settings.speed.toString());
        params.append('top_k', this.settings.top_k.toString());
        params.append('top_p', this.settings.top_p.toString());
        params.append('temperature', this.settings.temperature.toString());
        params.append('stream', this.settings.stream.toString());
        params.append('parallel_infer', '0'); // 并行推理: 0关闭 1开启
        params.append('split_bucket', '0'); // : 0关闭 1开启
        params.append('text_split_method', 'cut0');// cut0不切割 cut1凑4句切 cut2凑50字 cut3按中文逗号切 cut4按英文逗号切 cur5按标点符号切


        return `${this.settings.provider_endpoint}/?${params.toString()}`;

    }

    // Interface not used by GSVI TTS
    async fetchTtsFromHistory(history_item_id) {
        return Promise.resolve(history_item_id);
    }

}
```

请注意代码的254、255、256行代码，请按照需求自己更改
![](images/post/ai/Snipaste_2024-09-08_11-20-21.png)

### 下载webTTS
在这里进行下载：[WebTTS文档](https://yxi3w0wmgv2.feishu.cn/wiki/JilfwWyNRiTWlkkheeUck40qnaf)

下载完以后，将**整合包/GPT-SoVITS-TTS.rar**解压。然后双击打开**GPT-SoVITS-TTS/webtts_v2/TTS2.16.0.exe**

这里双击TTS2.16.0.exe以后会弹出两个程序框，在可以交互的程序框里，按照上面文档里作者给出的进行配置。之后点击“信息”然后点击“WebTTS，启动！”，这时浏览器会弹出一个localhost:8080的网页。这样就可以进行下一步了。
![](images/post/ai/Snipaste_2024-09-08_11-25-16.png)

### 在酒馆中配置

按下图进行配置
![](images/post/ai/Snipaste_2024-09-08_11-32-21.png)

然后点击**Available voices**以后，可以看到作者默认导入的这些声音模型，即代表成功了。这时可以让绑定了声音的角色在对话框说一句话试试看，不要直接点击下图这个界面的模型播放按钮，作者说这里点了不出声是正常的。
![](images/post/ai/Snipaste_2024-09-08_11-33-28.png)

### 添加自己的声音模型
首先，先去网络上寻找自己喜欢的声音模型，如果会自己训练模型的当我没说。

这里放一个链接，里面有很多模型，可以去其他地方找找：https://www.yuque.com/baicaigongchang1145haoyuangong/ib3g1e/nwnaga50cazb2v93

下载下来模型以后，查看模型文件里一般有.pht和.ckpt和一些.wav文件。这里需要将.wav文件按照**语气#语言#文本.wav**进行重命名。将模型文件夹放在**GPT-SoVITS-TTS\models**文件夹里，
![](images/post/ai/Snipaste_2024-09-08_11-42-49.png)

此时再去webTTS的localhost:8080或者酒馆的tts插件里都会看到新的模型。
![](images/post/ai/Snipaste_2024-09-08_11-45-01.png)

more info: 
  - [酒馆live2D教程](https://sqivg8d05rm.feishu.cn/wiki/UvRPwWomOiGIxmkuJAJcfpwynkd)
  - [GPT-SoVITS](https://www.yuque.com/baicaigongchang1145haoyuangong/ib3g1e?#%20%E3%80%8AGPT-SoVITS%E6%8C%87%E5%8D%97%E3%80%8B)
  - [TTS Server](https://yxi3w0wmgv2.feishu.cn/wiki/B93pwV3WQiNQcTkaJVgcySeEnuc)