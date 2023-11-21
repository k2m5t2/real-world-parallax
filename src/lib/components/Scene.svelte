<script lang="ts">
  import { T } from '@threlte/core'
  import { ContactShadows, Float, Grid, OrbitControls, interactivity } from '@threlte/extras'
  import { spring } from 'svelte/motion'
	import App from './App.svelte';

  interactivity()

  let cursorPosition = { x: 0, y: 0 }
  let camPos = {x: 0, y: 0, z: 10}
  const scale = spring(1)
  let intentional = false
  function position(curPos, camPos, intentional) {
    if (intentional) {
      return [camPos.x, camPos.y, camPos.z]
        // x: camPos.x,
        // y: camPos.y,
        // z: camPos.z
    }
    else {
      return [camPos.x+curPos.x, camPos.y+curPos.y, camPos.z]
        // x: camPos.x+curPos.x,
        // y: camPos.y+curPos.y,
        // z: camPos.z
    }
  }
</script>

$: <pre>{JSON.stringify(camPos, null, 2)}</pre>

<T.PerspectiveCamera
  makeDefault
  position={position(cursorPosition, camPos, intentional)}
  on:position={({ detail: { position } }) => {
    camPos.x = position.x
    camPos.y = position.y
    camPos.z = position.z
  }}
  fov={35}
>
  <OrbitControls
    enableZoom={false}
    enableDamping
    autoRotateSpeed={0.5}
    target.y={1.5}
  />
</T.PerspectiveCamera>

<T.DirectionalLight
  intensity={0.8}
  position.x={5}
  position.y={10}
/>
<T.AmbientLight intensity={0.2} />

<Grid
  position.y={-0.001}
  cellColor="#ffffff"
  sectionColor="#ffffff"
  sectionThickness={0}
  fadeDistance={25}
  cellSize={2}
/>

<ContactShadows
  scale={10}
  blur={2}
  far={2.5}
  opacity={0.5}
/>

<Float
  floatIntensity={1}
  floatingRange={[0, 1]}
>
  <T.Mesh
    position.y={1.2}
    position.z={-0.75}
  >
    <!-- <T.BoxGeometry /> -->
    <T.MeshStandardMaterial color="#0059BA" />
  </T.Mesh>
</Float>

<Float
  floatIntensity={1}
  floatingRange={[0, 1]}
>
  <T.Mesh
    position={[1.2, 1.5, 0.75]}
    rotation.x={5}
    rotation.y={71}
  >
    <!-- <T.TorusKnotGeometry args={[0.5, 0.15, 100, 12, 2, 3]} /> -->
    <T.MeshStandardMaterial color="#F85122" />
  </T.Mesh>
</Float>

<T.Mesh
  position={[0, 1, 0]}
  rotation={[-5, 128, 10]}
  scale={$scale}
  on:pointerenter={() => scale.set(1.2)}
  on:pointerleave={() => scale.set(1)}
  on:pointermove={(e) => {
    // cursorPosition.x = e.point.x
    // cursorPosition.y = e.point.y
    cursorPosition.x = e.pointer.x
    cursorPosition.y = e.pointer.y
    if (intentional) {
      camPos.x = e.camera.position.x
      camPos.y = e.camera.position.y
      camPos.z = e.camera.position.z
    }
  }}
  on:pointerdown={() => {
    intentional = true
  }}
  on:pointerup={() => {
    intentional = false
  }}
>
  <T.IcosahedronGeometry />
  <T.MeshStandardMaterial color="#F8EBCE" />
</T.Mesh>
