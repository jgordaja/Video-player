<template>
    <q-card>
        <q-card-section class="text-h6">
            Проигрыватель VideoPlayer
        </q-card-section>
        <q-card-section>
            <div class="row">
                <div class="col-12">
                    <div>
                        <q-toggle
                            v-model="visibleVideoPlayerContent"
                            class="q-mb-md"
                        />
                        <small>VideoPlayer</small>
                    </div>
                </div>
                <div class="col-12">
                    <q-slide-transition class="q-mt-sm">
                        <q-input
                            v-if="visibleVideoPlayerContent"
                            v-model="VideoPlayerContent"
                            type="textarea"
                            filled
                            autogrow
                        />
                    </q-slide-transition>
                </div>
            </div>
        </q-card-section>
        <q-card-section>
            <q-input
                v-model="videoPlayer.title"
                class="q-pa-md"
                label="Введите заголовок для видео"
            />
            <q-input
                v-model="videoPlayer.fileName"
                label="Введите название файла"
                class="q-pa-md"
            />
            <q-input
                v-model="videoPlayer.src"
                label="Ссылка для видео"
                class="q-pa-md"
            />
            <q-input
                v-model="videoPlayer.poster"
                label="poster"
                class="q-pa-md"
            />
            <q-input
                v-model="videoPlayer.width"
                label="Введите ширину (необязательный параметр)"
                class="q-pa-md"
            />
            <div class="flex justify-center">
                <q-btn
                    class="q-mx-auto"
                    padding="xs lg"
                    push
                    color="primary"
                    label="Загрузить"
                    @click="$refs.jsPlayer.reLoad()"
                />
            </div>
            <VideoPlayer
                v-if="videoPlayer.src"
                ref="jsPlayer"
                :poster="videoPlayer.poster"
                :src="videoPlayer.src"
                :title="videoPlayer.title"
                :file-name="videoPlayer.fileName"
                :width="videoPlayer.width"
            />
        </q-card-section>
    </q-card>
</template>

<script>
import {defineComponent} from 'vue';
import VideoPlayer from "components/VideoPlayer";

export default defineComponent({
    name: 'PageIndex',
    components: {
        VideoPlayer,
    },
    data() {
        return {
            videoPlayer: {
                title: 'Здесь будет заголовок видео',
                fileName: 'Очень интересное видео',
                poster: 'https://umi.ru/images/cms/data/blog/17-vidoe-to-computer.jpg', // пример
                src: '',
                width: '',
            },
            visibleVideoPlayerContent: false,
            VideoPlayerContent: `
        <VideoPlayer
          v-if="videoPlayer.src"
          ref="jsPlayer"
          :poster="videoPlayer.poster"
          :src="videoPlayer.src"
          :title="videoPlayer.title"
          :file-name="videoPlayer.fileName"
          :width="videoPlayer.width"
        />

        Пример доступа к функции "перезагрузки" видео с новой ссылкой:

        <q-btn
          @click="$refs.jsPlayer.reLoad()"
        />
    `,
        }
    }
})
</script>
