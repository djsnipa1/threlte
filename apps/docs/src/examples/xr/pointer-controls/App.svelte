<script lang='ts'>
  import { T, Canvas } from '@threlte/core'
  import { XR, VRButton } from '@threlte/xr'
  import Scene from './Scene.svelte'
</script>

<Canvas>
  <Scene />

  <XR>
    <T.PerspectiveCamera
      slot='fallback'
      makeDefault
      position={[0, 1.5, 4]}
      on:create={({ ref }) => ref.lookAt(0, 1.5, 0)}
    />
  </XR>

  <T.AmbientLight />
  <T.DirectionalLight intensity={1.5} position={[1, 1, 1]} />
</Canvas>

<VRButton />
