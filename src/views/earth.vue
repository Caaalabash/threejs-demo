<template>
    <canvas width="800px" height="600px" ref="canvas"></canvas>
</template>

<script>
import {
    WebGLRenderer,
    Scene,
    PerspectiveCamera,
    AxesHelper,
    SphereGeometry,
    MeshBasicMaterial,
    TextureLoader,
    Mesh
} from 'three'

export default {
    name: 'earth',
    mounted() {
        // 定义渲染器、场景、相机、辅助坐标轴、地球
        const renderer = new WebGLRenderer({ canvas: this.$refs['canvas'] })
        const scene = new Scene()
        const camera = new PerspectiveCamera(45, 4 / 3, 1, 1000)
        const axesHelper = new AxesHelper(4)
        const cube = new Mesh(new SphereGeometry(3, 36, 36),
            new MeshBasicMaterial({
                map: new TextureLoader().load(require('@/assets/earth.jpg')),
            })
        )

        camera.lookAt(0, 0, 0)
        camera.position.set(0, 0, 10)
        scene.add(camera)
        scene.add(axesHelper)
        scene.add(cube)

        // Render
        function draw() {
            window.requestAnimationFrame(draw)
            cube.rotation.y = (cube.rotation.y + 0.01) % (Math.PI*2)
            renderer.render(scene, camera)
        }
        window.requestAnimationFrame(draw)
    },
}
</script>

<style scoped>

</style>