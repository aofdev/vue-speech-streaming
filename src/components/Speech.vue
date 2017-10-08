<template>
  <v-layout row wrap>
    <v-flex xs4>
    </v-flex>
    <v-flex xs4 class="text-xs-center">
    <v-select v-show="btn" v-bind:items="items" v-model="selected" label="Select a language"></v-select>
      <v-btn v-show="btn"  @click.native="startRecording" block round color="primary" dark>
        <v-icon left>mic</v-icon> Recording</v-btn>
      <v-btn v-show="btnStop" @click.native="stopRecording" block round color="error" dark>
        <v-icon left>stop</v-icon> Stop</v-btn>
      </br>
    </v-flex>
    <v-flex xs4>
    </v-flex>
    <v-flex xs12>
      <transition name="slide">
        <div v-show="result">
  
          <v-layout row wrap>
            <v-flex xs3>
            </v-flex>
            <v-flex xs6>
              <v-card class="blue-grey darken-2 white--text">
                   <v-card-title primary-title>
                <div class="headline"></div>
                <div>{{textResult}}</div>
              </v-card-title>

               <h6></h6> 
              </v-card>
            </v-flex>
            <v-flex xs3>
            </v-flex>
          </v-layout>
           </div>
      </transition>
      <transition name="slide">
        <div v-show="resultError" class="text-xs-center">
  
          <v-layout row wrap>
            <v-flex xs3>
            </v-flex>
            <v-flex xs6>
              <v-card class="red darken-3 white--text">
                <v-card-title primary-title>
                  <div class="headline">Reached transcription time limit</div>
                </v-card-title>
                <v-card-actions>
                  <v-btn @click.native="redirectError" flat dark>Try Again</v-btn>
                </v-card-actions>
              </v-card>
            </v-flex>
            <v-flex xs3>
            </v-flex>
          </v-layout>
        </div>
      </transition>
  
    </v-flex>
  </v-layout>
</template>

<script>
 import axios from 'axios';
  var audioContext = new(window.AudioContext || window.webkitAudioContext)();
  var socket = io.connect('http://localhost:3555');
  var ssStream = ss.createStream();
  var scriptNode;
  
  export default {
    data() {
      return {
        btn: true,
        btnStop: false,
        result: false,
        resultError: false,
        textResult: "",
        selected: 'en-US',
        items: [{
            text: 'Afrikaans (Suid-Afrika)',
            value: 'af-ZA'
          },
          {
            text: 'አማርኛ (ኢትዮጵያ)',
            value: 'am-ET'
          },
          {
            text: 'Հայ (Հայաստան)',
            value: 'hy-AM'
          },
          {
            text: 'Azərbaycan (Azərbaycan)(Azerbaijan)',
            value: 'az-AZ'
          },
          {
            text: 'Bahasa Indonesia (Indonesia)',
            value: 'id-ID'
          },
          {
            text: 'Bahasa Melayu (Malaysia)',
            value: 'ms-MY'
          },
          {
            text: 'বাংলা (বাংলাদেশ)',
            value: 'bn-BD'
          },
          {
            text: 'বাংলা (ভারত)',
            value: 'bn-IN'
          },
          {
            text: 'Català (Espanya)',
            value: 'ca-ES'
          },
          {
            text: 'Čeština (Česká republika)',
            value: 'cs-CZ'
          },
          {
            text: 'Dansk (Danmark)',
            value: 'da-DK'
          },
          {
            text: 'Deutsch (Deutschland)',
            value: 'de-DE'
          },
          {
            text: 'English (Australia)',
            value: 'en-AU'
          },
          {
            text: 'English (Canada)',
            value: 'en-CA'
          },
          {
            text: 'English (Ghana)',
            value: 'en-GH'
          },
          {
            text: 'English (Great Britain)',
            value: 'en-GB'
          },
          {
            text: 'English (India)',
            value: 'en-IN'
          },
          {
            text: 'English (Ireland)',
            value: 'en-IE'
          },
          {
            text: 'English (Kenya)',
            value: 'en-KE'
          },
          {
            text: 'English (New Zealand)',
            value: 'en-NZ'
          },
          {
            text: 'English (Nigeria)',
            value: 'en-NG'
          },
          {
            text: 'English (Philippines)',
            value: 'en-PH'
          },
          {
            text: 'English (South Africa)',
            value: 'en-ZA'
          },
          {
            text: 'English (Tanzania)',
            value: 'en-TZ'
          },
          {
            text: 'English (United States)',
            value: 'en-US'
          },
          {
            text: 'Español (Argentina)',
            value: 'es-AR'
          },
          {
            text: 'Español (Bolivia)',
            value: 'es-BO'
          },
          {
            text: 'Español (Chile)',
            value: 'es-CL'
          },
          {
            text: 'Español (Colombia)',
            value: 'es-CO'
          },
          {
            text: 'Español (Costa Rica)',
            value: 'es-CR'
          },
          {
            text: 'Español (Ecuador)',
            value: 'es-EC'
          },
          {
            text: 'Español (El Salvador)',
            value: 'es-SV'
          },
          {
            text: 'Español (España)',
            value: 'es-ES'
          },
          {
            text: 'Español (Estados Unidos)',
            value: 'es-US'
          },
          {
            text: 'Español (Guatemala)',
            value: 'es-GT'
          },
          {
            text: 'Español (Honduras)',
            value: 'es-HN'
          },
          {
            text: 'Español (México)',
            value: 'es-MX'
          },
          {
            text: 'Español (Panamá)',
            value: 'es-PA'
          },
          {
            text: 'Español (Paraguay)',
            value: 'es-PY'
          },
          {
            text: 'Español (Perú)',
            value: 'es-PE'
          },
          {
            text: 'Español (Puerto Rico)',
            value: 'es-PR'
          },
          {
            text: 'Español (República Dominicana)',
            value: 'es-DO'
          },
          {
            text: 'Español (Uruguay)',
            value: 'es-UY'
          },
          {
            text: 'Español (Venezuela)',
            value: 'es-VE'
          },
          {
            text: 'Euskara (Espainia)',
            value: 'eu-ES'
          },
          {
            text: 'Filipino (Pilipinas)',
            value: 'fil-PH'
          },
          {
            text: 'Français (Canada)',
            value: 'fr-CA'
          },
          {
            text: 'Français (France)',
            value: 'fr-FR'
          },
          {
            text: 'Galego (España)',
            value: 'gl-ES'
          },
          {
            text: 'ქართული (საქართველო)',
            value: 'ka-GE'
          },
          {
            text: 'ગુજરાતી (ભારત)',
            value: 'gu-IN'
          },
          {
            text: 'Hrvatski (Hrvatska)',
            value: 'hr-HR'
          },
          {
            text: 'IsiZulu (Ningizimu Afrika)',
            value: 'zu-Z'
          },
          {
            text: 'Íslenska (Ísland)',
            value: 'is-IS'
          },
          {
            text: 'Italiano (Italia)',
            value: 'it-IT'
          },
          {
            text: 'Jawa (Indonesia)',
            value: 'jv-ID'
          },
          {
            text: 'ಕನ್ನಡ (ಭಾರತ)',
            value: 'kn-IN'
          },
          {
            text: 'ភាសាខ្មែរ (កម្ពុជា)',
            value: 'km-KH'
          },
          {
            text: 'ລາວ (ລາວ)',
            value: 'lo-LA'
          },
          {
            text: 'Latviešu (latviešu)',
            value: 'lv-LV'
          },
          {
            text: 'Lietuvių (Lietuva)',
            value: 'lt-LT'
          },
          {
            text: 'Magyar (Magyarország)',
            value: 'hu-HU'
          },
          {
            text: 'മലയാളം (ഇന്ത്യ)',
            value: 'ml-IN'
          },
          {
            text: 'मराठी (भारत)',
            value: 'mr-IN'
          },
          {
            text: 'Nederlands (Nederland)',
            value: 'nl-NL'
          },
          {
            text: 'नेपाली (नेपाल)',
            value: 'ne-NP'
          },
          {
            text: 'Norsk bokmål (Norge)',
            value: 'nb-NO'
          },
          {
            text: 'Polski (Polska)',
            value: 'pl-PL'
          },
          {
            text: 'Português (Brasil)',
            value: 'pt-BR'
          },
          {
            text: 'Português (Portugal)',
            value: 'pt-P'
          },
          {
            text: 'Română (România)',
            value: 'ro-RO'
          },
          {
            text: 'සිංහල (ශ්රී ලංකාව)',
            value: 'si-LK'
          },
          {
            text: 'Slovenčina (Slovensko)',
            value: 'sk-SK'
          },
          {
            text: 'Slovenščina (Slovenija)',
            value: 'sl-SI'
          },
          {
            text: 'Urang (Indonesia)',
            value: 'su-ID'
          },
          {
            text: 'Swahili (Tanzania)',
            value: 'sw-TZ'
          },
          {
            text: 'Swahili (Kenya)',
            value: 'sw-KE'
          },
          {
            text: 'Suomi (Suomi)',
            value: 'fi-FI'
          },
          {
            text: 'Svenska (Sverige)',
            value: 'sv-SE'
          },
          {
            text: 'தமிழ் (இந்தியா)',
            value: 'ta-IN'
          },
          {
            text: 'தமிழ் (சிங்கப்பூர்)',
            value: 'ta-SG'
          },
          {
            text: 'தமிழ் (இலங்கை)',
            value: 'ta-LK'
          },
          {
            text: 'தமிழ் (மலேசியா)',
            value: 'ta-MY'
          },
          {
            text: 'తెలుగు (భారతదేశం)',
            value: 'te-IN'
          },
          {
            text: 'Tiếng Việt (Việt Nam)',
            value: 'vi-VN'
          },
          {
            text: 'Türkçe (Türkiye)',
            value: 'tr-TR'
          },
          {
            text: 'اردو (پاکستان)',
            value: 'ur-PK'
          },
          {
            text: 'اردو (بھارت)',
            value: 'ur-IN'
          },
          {
            text: 'Ελληνικά (Ελλάδα)',
            value: 'el-GR'
          },
          {
            text: 'Български (България)',
            value: 'bg-BG'
          },
          {
            text: 'Русский (Россия)',
            value: 'ru-RU'
          },
          {
            text: 'Српски (Србија)',
            value: 'sr-RS'
          },
          {
            text: 'Українська (Україна)',
            value: 'uk-UA'
          },
          {
            text: 'עברית (ישראל)',
            value: 'he-IL'
          },
          {
            text: 'العربية (إسرائيل)',
            value: 'ar-IL'
          },
          {
            text: 'العربية (الأردن)',
            value: 'ar-JO'
          },
          {
            text: 'العربية (الإمارات)',
            value: 'ar-AE'
          },
          {
            text: 'العربية (البحرين)',
            value: 'ar-BH'
          },
          {
            text: 'العربية (الجزائر)',
            value: 'ar-DZ'
          },
          {
            text: 'العربية (السعودية)',
            value: 'ar-SA'
          },
          {
            text: 'العربية (العراق)',
            value: 'ar-IQ'
          },
          {
            text: 'العربية (الكويت)',
            value: 'ar-KW'
          },
          {
            text: 'العربية (المغرب)',
            value: 'ar-MA'
          },
          {
            text: 'العربية (تونس)',
            value: 'ar-TN'
          },
          {
            text: 'العربية (عُمان)',
            value: 'ar-OM'
          },
          {
            text: 'العربية (فلسطين)',
            value: 'ar-PS'
          },
          {
            text: 'العربية (قطر)',
            value: 'ar-QA'
          },
          {
            text: 'العربية (لبنان)',
            value: 'ar-LB'
          },
          {
            text: 'العربية (مصر)',
            value: 'ar-EG'
          },
          {
            text: 'فارسی (ایران)',
            value: 'fa-IR'
          },
          {
            text: 'हिन्दी (भारत)',
            value: 'hi-IN'
          },
          {
            text: 'ไทย (ประเทศไทย)',
            value: 'th-TH'
          },
          {
            text: '한국어 (대한민국)',
            value: 'ko-KR'
          },
          {
            text: '國語 (台灣)',
            value: 'cmn-Hant-TW'
          },
          {
            text: '廣東話 (香港)',
            value: 'yue-Hant-HK'
          },
          {
            text: '日本語（日本)',
            value: 'ja-JP'
          },
          {
            text: '普通話 (香港)',
            value: 'cmn-Hans-HK'
          },
          {
            text: '普通话 (中国大陆)',
            value: 'cmn-Hans-CN'
          }
        ]
      }
    },
    methods: {
      successCallback(stream) {
        const vm = this;
        console.log('successCallback:....IN');
        var input = audioContext.createMediaStreamSource(stream);
        var bufferSize = 2048;
        scriptNode = audioContext.createScriptProcessor(bufferSize, 1, 1);
        scriptNode.onaudioprocess = scriptNodeProcess;
        input.connect(scriptNode);
  
        // console.log('ScriptNode BufferSize:', scriptNode.bufferSize);
        function scriptNodeProcess(audioProcessingEvent) {
        var inputBuffer = audioProcessingEvent.inputBuffer;
        var outputBuffer = audioProcessingEvent.outputBuffer;
        var inputData = inputBuffer.getChannelData(0);
        var outputData = outputBuffer.getChannelData(0);
  
  
        // Loop through the 4096 samples
        for (var sample = 0; sample < inputBuffer.length; sample++) {
          outputData[sample] = inputData[sample];
        }
        ssStream.write(new ss.Buffer(vm.downsampleBuffer(inputData, 44100, 16000)));
      }
      },
      downsampleBuffer(buffer, sampleRate, outSampleRate) {
        if (outSampleRate == sampleRate) {
          return buffer;
        }
        if (outSampleRate > sampleRate) {
          throw "downsampling rate show be smaller than original sample rate";
        }
        var sampleRateRatio = sampleRate / outSampleRate;
        var newLength = Math.round(buffer.length / sampleRateRatio);
        var result = new Int16Array(newLength);
        var offsetResult = 0;
        var offsetBuffer = 0;
        while (offsetResult < result.length) {
          var nextOffsetBuffer = Math.round((offsetResult + 1) * sampleRateRatio);
          var accum = 0,
            count = 0;
          for (var i = offsetBuffer; i < nextOffsetBuffer && i < buffer.length; i++) {
            accum += buffer[i];
            count++;
          }
  
          result[offsetResult] = Math.min(1, accum / count) * 0x7FFF;
          offsetResult++;
          offsetBuffer = nextOffsetBuffer;
        }
        return result.buffer;
      },
      startRecording() {
        const languageSelected = this.selected;
        socket.emit('LANGUAGE_SPEECH', languageSelected);
        this.result = true;
        this.btn = false;
        this.btnStop = true;
        scriptNode.connect(audioContext.destination);
        ss(socket).emit('START_SPEECH', ssStream);
      },
      stopRecording() {
        this.btnStop = false;
        this.btn = true;
        scriptNode.disconnect(audioContext.destination);
        // ssStream.end();
        socket.emit('STOP_SPEECH', {});
      },
      errorCallback(error) {
        console.log('errorCallback:', error);
      },
      redirectError(){
         window.location.href = "http://localhost:8080/"
      }
    },
    created() {
      const that = this;
     
      socket.on('SPEECH_RESULTS', function(text) {
        if('Reached_transcription_time_limit' == text){
            that.resultError = true;
        }else{
              that.textResult += text;
        }
      })
        if (navigator.mediaDevices.getUserMedia) {
          console.log('getUserMedia supported...');
          navigator.webkitGetUserMedia({ audio: true }, function(stream) {
            that.successCallback(stream)
          }, function(error) {
            that.errorCallback(error)
          });
        } else {
          console.log('getUserMedia not supported on your browser!');
        }
      }
    }
</script>


<style>
  .slide-enter {
    opacity: 0;
  }
  
  .slide-enter-active {
    animation: slide-in 1s ease-out forwards;
    transition: opacity .5s;
  }
  
  .slide-move {
    transition: transform 1s;
  }
  
  @keyframes slide-in {
    from {
      transform: translateY(20px);
    }
    to {
      transform: translateY(0);
    }
  }
</style>
