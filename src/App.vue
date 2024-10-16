<script lang='ts' setup>
import {
    CanvaskitMap,
    MarkerLayer,
    TaskQueue,
    TileLayer,
} from "@canvaskit-map/core";
import initCanvaskit from "canvaskit-wasm";
import { onMounted, ref } from "vue";


const mapRef = ref<HTMLDivElement>()


onMounted(async () => {
    if (mapRef.value) {
        const canvaskit = await initCanvaskit({
            locateFile: (file) => 'https://unpkg.com/canvaskit-wasm@latest/bin/' + file
        });

        const map = new CanvaskitMap(canvaskit, {
            element: mapRef.value,
            size: [17408, 17408],
            origin: [3568 + 5888, 6286 + 2048],
            maxZoom: 1,
            onClick(event) {
                console.log(event.coordinate);
            },
        });
        map.addLayer(
            new TileLayer({
                minZoom: 10,
                maxZoom: 13,
                offset: [-5888, -2048],
                getTileUrl(x, y, z) {
                    return `https://assets.yuanshen.site/tiles_twt40/${z}/${x}_${y}.png`;
                },
            })
        );

        // 添加一个marker点
        // 创建 CanvasImageSource 
        let canvas = document.createElement('canvas')
        canvas.width = 10
        canvas.height = 10
        // 创建红色背景
        let ctx = canvas.getContext('2d')
        if (ctx) {
            ctx.fillStyle = 'red'
            ctx.fillRect(0, 0, 10, 10)
            ctx.fillStyle = 'white'
            ctx.font = '20px serif'
        }
        let marker = new MarkerLayer({
            image: canvas,
            items: [{ x: 0, y: 0 }],
        })

        map.addLayer(marker)
    }
})

</script>
<template>
    <div class="App">
        <div class="header">
            <h1>标题</h1>
        </div>
        <div class="map" ref="mapRef"></div>
    </div>
</template>
<script lang='ts'>

export default {
    name: 'App',
}
</script>
<style lang='less'>
* {
    margin: 0;
    padding: 0;
}

.header {
    height: 50px;
    padding: 0 1rem;
    background-color: #212121;
    color: aliceblue;
}

.map {
    width: 100%;
    height: calc(100vh - 50px);
}
</style>