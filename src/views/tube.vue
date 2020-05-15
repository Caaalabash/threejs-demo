<template>
    <canvas ref="canvas"></canvas>
</template>

<script>
import {
    WebGLRenderer,
    Scene,
    PerspectiveCamera,
    MeshLambertMaterial,
    CatmullRomCurve3,
    TubeGeometry,
    FaceColors,
    PointLight,
    Vector3,
    BackSide,
    Mesh,
    Color
} from 'three'

export default {
    name: 'tube',
    mounted() {
        const renderer = new WebGLRenderer({ canvas: this.$refs['canvas'] })
        renderer.setSize(window.innerWidth, window.innerHeight)

        const camera = new PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.001, 1000)
        camera.position.z = 400

        const scene = new Scene()

        // const axesHelper = new AxesHelper(9999)
        // scene.add(axesHelper)

        // 在xz平面上创建一堆三维向量
        const points = [
            [68.5, 185.5],
            [1, 262.5],
            [270.9, 281.9],
            [345.5, 212.8],
            [178, 155.7],
            [240.3, 72.3],
            [153.4, 0.6],
            [52.6, 53.3],
            [68.5, 185.5]
        ].map(([x, z]) => new Vector3(x, 0, z))
        // 创建一条平滑的曲线
        const path = new CatmullRomCurve3(points)
        // 创建3d管道，tubularSegments = 切割tube的份数  radiusSegments = 切割tube四周的份数
        const geometry = new TubeGeometry(path, 600, 4, 32, true)
        // 给每一个面设定颜色
        for (let i = 0, j = geometry.faces.length; i < j; i++) {
            geometry.faces[i].color = new Color('hsl('+Math.floor(Math.random()*360)+',50%,50%)')
        }
        // 材质：使用什么颜色/渲染哪一面
        const material = new MeshLambertMaterial({
            vertexColors: FaceColors,
            side: BackSide,
        })
        // 组合
        const tube = new Mesh(geometry, material)
        scene.add(tube)
        // 添加灯光
        const light = new PointLight(0xffffff, 1, 50)
        scene.add(light)

        let percentage = 0
        function render(){
            percentage += 0.001
            // 获取path任意百分比下的坐标数据
            const p1 = path.getPointAt(percentage % 1)
            const p2 = path.getPointAt((percentage + 0.03) % 1)
            // const p3 = path.getPointAt((percentage + 0.07) % 1)
            const p3 = p2
            // 设置相机的坐标
            camera.position.set(p1.x, p1.y, p1.z)
            // 让相机朝着下一个移动方向看去
            camera.lookAt(p2)
            // 让灯光也照着下一个移动方向
            light.position.set(p3.x, p3.y, p3.z)
            renderer.render(scene, camera)
            requestAnimationFrame(render)
        }
        requestAnimationFrame(render)
    },
}
</script>

<style scoped>

</style>