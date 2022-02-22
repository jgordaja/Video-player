<template>
    <div>
        <div
            class="q-mx-md q-my-auto q-pa-md"
            :style="{width: widthContent +'px'}"
        >
            <div class="q-py-sm">
                {{ title }}
            </div>
            <video
                ref="videoPlayer"
                class="video-js vjs-big-play-centered video-player"
            />
            <div class="flex justify-center q-pa-md">
                {{ fileName }}
            </div>
        </div>
    </div>
</template>

<script>
import videojs from 'video.js';
import 'video.js/dist/video-js.css';
// плагин для вопроизведения видео с youtube
import 'videojs-youtube';
// !!! videojs c 7-й версии поддерживает dash и hls по ум. т.е. следующие библиотеки подключать не нужно:
// import 'dashjs/dist/dash.all.min.js';
// import 'videojs-contrib-dash/dist/videojs-dash';

let ruLanguage = require('video.js/dist/lang/ru.json');
// добавить отсутствующие значения перевода
const addition = {'Picture-in-Picture': 'Мини-прогирыватель'};

ruLanguage = Object.assign(ruLanguage, addition);

export default {
    name: 'VideoPlayer',
    props: {
        src: {String, default: '', require: false},
        title: {String, default: '', require: false},
        fileName: {String, default: '', require: false},
        poster: {String, default: '', require: false},
        width: {String, default: '', require: false},
    },
    data() {
        return {
            player: {},
            overrideNative: false,
            options: {
                // videojs options
                autoplay: false,
                controls: true,
                muted: false,
                languages: {
                    ru: ruLanguage,
                },
                language: 'ru',
                preload: 'auto',
                responsive: true,
                playbackRates: [0.7, 1.0, 1.5, 2.0],
                fluid: true,
                poster: this.poster,
                // src - получаем в пропсах и устанавливаем на этапе инициализации или перезагрузки
                // type - определяем в зависимости от переданной ссылки на видео, устанавливаем так же
                // sources: [
                //     {
                //         type: '',
                //         src: '',
                //     },
                // ],
                // настройки для iOS/Safari, нужно потестить/. upd потестила, работает
                html5: {
                    hls: {
                        overrideNative: this.overrideNative,
                    },
                    nativeVideoTracks: !this.overrideNative,
                    nativeAudioTracks: !this.overrideNative,
                    nativeTextTracks: !this.overrideNative,
                },
            },
        };
    },
    computed: {
        // размеры плеера устанавливаем ограничением ширины контента,
        // в самом videojs - установлено свойство responsive=true,
        widthContent() {
            // размер не меньше 500px, иначе - на весь контент
            return (!this.width || +this.width < 500) ? '' : this.width;
        },
        calculateType() {
            // выбрать тип видео в зависимоти от переданной ссылки src
            // "application/x-mpegURL" for HLS protocols (m3u8)
            // "application/dash+xml" for DASH protocols (mpd)
            // "video/mp4" - mp4
            // "video/youtube" - youtube
            // "video/webm" - webm
            // "video/ogg" - ogv
            let typeVideo = '';
            const link = this.src;
            // если ссылка на видео содержи слова "youtube.com" или "youtu.be", тогда это видео с youtube
            const pattern = RegExp(/(youtube.com|youtu.be)/);
            if (link.match(pattern)) {
                typeVideo = 'video/youtube';
            } else {
                // определяем тип видео по расширению
                const extension = link.split('.').pop();
                switch (extension) {
                    case 'mpd':
                        typeVideo = 'application/dash+xml';
                        break;
                    case 'm3u8':
                        typeVideo = 'application/x-mpegURL';
                        break;
                    case 'mp4':
                        typeVideo = 'video/mp4';
                        break;
                    case 'webm':
                        typeVideo = 'video/webm';
                        break;
                    case 'ogv':
                        typeVideo = 'video/ogg';
                        break;
                }
            }
            return typeVideo;
        },
    },
    mounted() {
        // инициализируем плеер
        this.player = videojs(this.$refs.videoPlayer, this.options);
        this.player.src({
            src: this.src,
            type: this.calculateType,
        });
    },
    beforeUnmount() {
        if (this.player) {
            this.player.dispose();
        }
    },
    methods: {
        // перезагрузка видео с новой ссыкой и типом файла
        reLoad() {
            this.player.ready(() => {
                this.player.src({
                    src: this.src,
                    type: this.calculateType,
                });
                this.player.options.poster = this.poster;
            });
        },
    },
};
</script>
