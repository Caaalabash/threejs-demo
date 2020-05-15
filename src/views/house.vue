<template>
    <canvas id="canvas"></canvas>
</template>

<script>
import * as THREE from 'three'

export default {
    name: 'house',
    mounted() {
        const textureLoader = new THREE.TextureLoader()
        textureLoader.load(require('@/assets/house.jpg'), texture => {
            init(texture)
            animate()
        })

        let renderer, scene, camera
        function init(texture) {
            renderer = new THREE.WebGLRenderer({ antialias: true, canvas: document.getElementById('canvas') })
            renderer.setPixelRatio(window.devicePixelRatio)
            renderer.setSize(window.innerWidth, window.innerHeight)

            camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 1, 1000)
            scene = new THREE.Scene()
            // 如果想要将一张equirectangular格式的的全景图转换到cubemap格式，则使用此方法，全景照片的长款比例固定为2：1
            scene.background = new THREE.WebGLCubeRenderTarget(1024).fromEquirectangularTexture(renderer, texture)

            document.addEventListener( 'mousedown', onDocumentMouseDown, false )
            document.addEventListener( 'wheel', onDocumentMouseWheel, false )
            window.addEventListener( 'resize', onWindowResized, false )
        }
        function animate() {
            requestAnimationFrame(animate)
            render()
        }

        let onPointerDownPointerX, onPointerDownPointerY, onPointerDownLon, onPointerDownLat
        let phi = 0, theta = 0, lon = 0, lat = 0

        function render() {
            lat = Math.max( - 85, Math.min( 85, lat ) )
            phi = THREE.MathUtils.degToRad( 90 - lat )
            theta = THREE.MathUtils.degToRad( lon )

            camera.position.x = 100 * Math.sin( phi ) * Math.cos( theta )
            camera.position.y = 100 * Math.cos( phi )
            camera.position.z = 100 * Math.sin( phi ) * Math.sin( theta )
            camera.lookAt( scene.position )

            renderer.render( scene, camera )
        }
        function onWindowResized() {
            renderer.setSize( window.innerWidth, window.innerHeight )

            camera.aspect = window.innerWidth / window.innerHeight
            camera.updateProjectionMatrix()
        }

        function onDocumentMouseDown( event ) {
            event.preventDefault()

            onPointerDownPointerX = event.clientX
            onPointerDownPointerY = event.clientY

            onPointerDownLon = lon
            onPointerDownLat = lat

            document.addEventListener( 'mousemove', onDocumentMouseMove, false )
            document.addEventListener( 'mouseup', onDocumentMouseUp, false )
        }

        function onDocumentMouseMove( event ) {
            lon = ( event.clientX - onPointerDownPointerX ) * 0.1 + onPointerDownLon
            lat = ( event.clientY - onPointerDownPointerY ) * 0.1 + onPointerDownLat
        }

        function onDocumentMouseUp() {
            document.removeEventListener( 'mousemove', onDocumentMouseMove, false )
            document.removeEventListener( 'mouseup', onDocumentMouseUp, false )
        }

        function onDocumentMouseWheel( event ) {
            let fov = camera.fov + event.deltaY * 0.05
            camera.fov = THREE.MathUtils.clamp( fov, 10, 75 )
            camera.updateProjectionMatrix()
        }
    }
}
</script>

<style scoped>

</style>