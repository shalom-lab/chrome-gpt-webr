<script setup>
import { VAceEditor } from 'vue3-ace-editor';
import ace from 'ace-builds';

</script>

<template>
    <div class="webr-editor">
        <div class="webr-editor-header">
            Code
            <button ref="webrEditorButton" disabled="true" class="webr-editor-btn" @click="run">
                &#x25B6; <span ref="webrEditorButtonTitle">Loading...</span>
            </button>
        </div>
        <v-ace-editor v-model:value="input" lang="r" theme="chrome" class="webr-ace-editor" ref="webrEditor"
            :options="{ useWorker: true }" />
    </div>
    <div v-if="output" class="webr-output-container">
        <pre ref="webROutput">{{ output }}</pre>
    </div>
    <canvas class="webr-plot-output" v-show="hasPlot" width="1000" height="800" ref="webrPlotOutput">
    </canvas>
</template>

<script>
import { VAceEditor } from 'vue3-ace-editor';

export default {
    mounted() {
        this.webR.init().then(async () => {
            this.codeShelter = await new this.webR.Shelter();
            const btnText = this.$refs.webrEditorButtonTitle;
            const btn = this.$refs.webrEditorButton;
            btnText.innerText = "Run Code";
            btn.disabled = false;
        });
    },
    data() {
        return {
            input: "head(mtcars, n=5)\n",
            output: '',
            hasPlot: false,
        };
    },
    methods: {
        async run() {
            const btn = this.$refs.webrEditorButton;
            const ace = this.$refs.webrEditor;
            const canvas = this.$refs.webrPlotOutput;
            btn.disabled = true;
            try {
                // Run user provided code
                await this.webR.init();
                await this.webR.evalRVoid('webr::canvas(width=500, height=400)');
                const result = await this.codeShelter.captureR(ace.value, {
                    withAutoprint: true,
                    captureStreams: true,
                    captureConditions: false,
                    env: this.webR.objs.globalEnv,
                });
                await this.webR.evalRVoid('dev.off()');

                // Get captured output
                this.output = result.output.filter(
                    (msg) => msg.type == 'stdout' || msg.type == 'stderr'
                ).map((msg) => msg.data).join('\n');

                // Render plot to HTML canvas element
                const msgs = await this.webR.flush();
                this.hasPlot = false;
                msgs.forEach((msg) => {
                    if (msg.type === 'canvas' && msg.data.event === 'canvasImage') {
                        this.hasPlot = true;
                        canvas.getContext('2d').drawImage(msg.data.image, 0, 0);
                    } else if (msg.type === 'canvas' && msg.data.event === 'canvasNewPage') {
                        canvas.getContext('2d').clearRect(0, 0, canvas.width, canvas.height);
                    }
                });
            } finally {
                // Destroy sheltered R objects
                this.codeShelter.purge();
                btn.disabled = false;
            }
        }
    },
    components: {
        VAceEditor,
    },
};

</script>

<style>
.webr-editor {
    margin: 1rem 0;
    background-color: #ebebeb;
}

.webr-output-container {
    border: 1px solid #CCC;
    padding: 0.5em 1em;
}

.webr-plot-output {
    display: block;
    width: 640px;
}

.webr-editor-header {
    padding: 0.25rem 1rem;
    border: 1px solid #CCC;
}

.webr-editor-btn {
    float: right;
    padding: 0.2rem
}

.webr-ace-editor {
    min-height: 80px;
    border: 1px solid #CCC;
    border-top: none;
}
</style>