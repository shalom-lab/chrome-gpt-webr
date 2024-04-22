<template>
    <n-input v-model:value="code" type="textarea" placeholder="基本的 Textarea" />
    <n-button type="primary" @click="onRun" :disabled="isLoading">
        Run
    </n-button>
    <div v-if="output" class="webr-output-container">
        <pre ref="webROutput">{{ output }}</pre>
    </div>
    <canvas class="webr-plot-output" v-show="hasPlot" width="1000" height="800" ref="webrPlotOutput">
    </canvas>
</template>

<script>

export default {
    data() {
        return {
            isLoading: true
        }
    },
    methods: {
        async onRun() {
            await this.webR.init()
            console.log(this.code)
            console.log(this.webR)
            let result = await this.webR.evalR('rnorm(10,5,1)');
            let output = await result.toArray();
            console.log('Result of running `rnorm` from webR: ', output);
        }
    },
    async mounted() {
        await this.webR.init();
        this.isLoading = false
        this.codeShelter = await new this.webR.Shelter();
        console.log(this.codeShelter)
    },

};
</script>

<style scoped></style>