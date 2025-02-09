<script lang="ts">
  import { T, useFrame } from '@threlte/core'
  import { Environment, GLTF, interactivity, onReveal, useInteractivity } from '@threlte/extras'
  import { SheetObject } from '@threlte/theatre'
  import { cubicOut } from 'svelte/easing'
  import { spring, tweened } from 'svelte/motion'
  import { DEG2RAD } from 'three/src/math/MathUtils.js'

  interactivity()
  const { pointer } = useInteractivity()

  const scale = tweened(0.9, {
    duration: 3e3,
    easing: cubicOut
  })

  const initialRotation = tweened(-30 * DEG2RAD, {
    duration: 3e3,
    easing: cubicOut
  })

  onReveal(() => {
    const timeout = setTimeout(() => {
      initialRotation.set(0 * DEG2RAD)
      scale.set(1)
    }, 300)
    return () => clearTimeout(timeout)
  })

  const pointerSpring = spring(
    { x: $pointer.x, y: $pointer.y },
    { precision: 0.00001, damping: 0.98, stiffness: 0.02 }
  )
  $: pointerSpring.set({ x: $pointer.x, y: $pointer.y })

  let autoRotation = 0
  useFrame((_, delta) => {
    autoRotation += 0.2 * delta
  })
</script>

<svelte:window on:pointerout={() => pointerSpring.set({ x: 0, y: 0 })} />

<T.PerspectiveCamera
  makeDefault
  position={[0, 8, 30]}
  on:create={({ ref }) => {
    ref.lookAt(0, 0, 0)
  }}
  fov={20}
/>

<GLTF
  url="/models/wave-ring/Pedestal.glb"
  position.y={-16.5}
  on:create={({ ref }) => {
    ref.children.forEach((child) => {
      child.receiveShadow = true
    })
  }}
/>

<SheetObject
  key="fill-light"
  let:Transform
  let:Sync
>
  <Transform>
    <T.DirectionalLight
      target.position={[0]}
      castShadow
    >
      <Sync
        color
        intensity
      />
    </T.DirectionalLight>
  </Transform>
</SheetObject>

<SheetObject
  key="key-light"
  let:Transform
  let:Sync
>
  <Transform>
    <T.DirectionalLight target.position={[0]}>
      <Sync
        color
        intensity
      />
    </T.DirectionalLight>
  </Transform>
</SheetObject>

<SheetObject
  key="key-fill-light"
  let:Transform
  let:Sync
>
  <Transform>
    <T.DirectionalLight target.position={[0]}>
      <Sync
        color
        intensity
      />
    </T.DirectionalLight>
  </Transform>
</SheetObject>

<SheetObject
  key="rim-light"
  let:Transform
  let:Sync
>
  <Transform>
    <T.DirectionalLight target.position={[0]}>
      <Sync
        color
        intensity
      />
    </T.DirectionalLight>
  </Transform>
</SheetObject>

<SheetObject
  key="counter-light"
  let:Transform
  let:Sync
>
  <Transform>
    <T.DirectionalLight target.position={[0]}>
      <Sync
        color
        intensity
      />
    </T.DirectionalLight>
  </Transform>
</SheetObject>

<GLTF
  on:create={({ ref }) => {
    ref.children.forEach((child) => {
      child.castShadow = true
      child.receiveShadow = true
    })
  }}
  position.y={0.9}
  rotation.y={180 * DEG2RAD + $initialRotation + $pointerSpring.x * 75 * DEG2RAD}
  scale={0.15 * $scale}
  url="/models/wave-ring/Ring.glb"
/>
