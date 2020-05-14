<template>
    <canvas width="800px" height="600px" ref="canvas"></canvas>
</template>

<script>
    import {
        WebGLRenderer,
        Scene,
        PerspectiveCamera,
        AxesHelper,
        AmbientLight
    } from 'three'
    import { OBJLoader, MTLLoader } from 'three-obj-mtl-loader'

    export default {
        name: 'earth',
        mounted() {
            // 定义渲染器、场景、相机、辅助坐标轴、地球
            const renderer = new WebGLRenderer({ canvas: this.$refs['canvas'] })
            const scene = new Scene()
            const camera = new PerspectiveCamera(75, 4 / 3, 1, 1000)
            const axesHelper = new AxesHelper(4)
            // 来点光
            const dirLight = new AmbientLight('white')
            dirLight.position.set(100, 100, 50)
            scene.add(dirLight)

            let desk = null
            new MTLLoader().load('desk.mtl', materials => {
                materials.preload()
                new OBJLoader().setMaterials(materials).load('desk.obj', obj => {
                    desk = obj
                    desk.position.set(0, 0, 0)
                    desk.rotation.x = Math.PI
                    scene.add(desk)
                })
            })
            camera.lookAt(0, 0, 0)
            camera.position.set(0, 5, 40)
            scene.add(camera)
            scene.add(axesHelper)

            // Render
            function draw() {
                window.requestAnimationFrame(draw)
                if (desk) {
                    desk.rotation.y = (desk.rotation.y + 0.01) % (Math.PI*2)
                }
                renderer.render(scene, camera)
            }
            window.requestAnimationFrame(draw)
        },
    }
</script>

<style scoped>

</style>