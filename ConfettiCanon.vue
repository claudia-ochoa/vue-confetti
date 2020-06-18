<template>
    <div class="confetti-canon"></div>
</template>

<script>
// CONVERTED FROM https://github.com/daniel-lundin/dom-confetti BY https://github.com/lavandongen

export default {
    name: 'ConfettiCanon',
    props: {
      config: {
        type: Object,
        default: () => {
         return {
            angle: 90,
            spread: 45,
            startVelocity: 45,
            elementCount: 50,
            dragFriction: 0.1,
            duration: 3000,
            stagger: 0,
            width: "10px",
            height: "10px",
            colors: ["#a864fd", "#29cdff", "#78ff44", "#ff718d", "#fdff6a"],
            random: Math.random
          }
        }
      }
    },
    data () {
      return {
        defaults: {
          angle: 90,
          spread: 45,
          startVelocity: 45,
          elementCount: 50,
          dragFriction: 0.1,
          duration: 3000,
          stagger: 0,
          width: "10px",
          height: "10px",
          colors: ["#a864fd", "#29cdff", "#78ff44", "#ff718d", "#fdff6a"],
          random: Math.random
        }
      }
    },
    methods: {
      createElements (root, elementCount, colors, width, height) {
        return Array.from({
          length: elementCount
        }).map((_, index) => {
          const element = document.createElement("div")
          element.id = 'confetti';
          element.class = 'text-center';
          const color = colors[index % colors.length]
          element.style["background-color"] = color
          element.style.width = width
          element.style.height = height
          element.style.position = "fixed"
          element.style.top = '50%'
          element.style.left = '50%'
          element.style.willChange = "transform, opacity"
          element.style.visibility = "hidden"
          root.appendChild(element)
          return element
        })
      },

      randomPhysics (angle, spread, startVelocity, random) {
        const radAngle = angle * (Math.PI / 180)
        const radSpread = spread * (Math.PI / 180)
        return {
          x: 0,
          y: 0,
          wobble: Math.random() * 10,
          wobbleSpeed: 0.1 + Math.random() * 0.1,
          velocity: startVelocity * 0.5 + Math.random() * startVelocity,
          angle2D: -radAngle + (0.5 * radSpread - Math.random() * radSpread),
          angle3D: -(Math.PI / 4) + Math.random() * (Math.PI / 2),
          tiltAngle: Math.random() * Math.PI,
          tiltAngleSpeed: 0.1 + Math.random() * 0.3
        }
      },
      updateFetti(fetti, progress, dragFriction, decay) {
        fetti.physics.x += Math.cos(fetti.physics.angle2D) * fetti.physics.velocity
        fetti.physics.y += Math.sin(fetti.physics.angle2D) * fetti.physics.velocity
        fetti.physics.z += Math.sin(fetti.physics.angle3D) * fetti.physics.velocity
        fetti.physics.wobble += fetti.physics.wobbleSpeed
        if (decay) {
          fetti.physics.velocity *= decay
        } else {
          fetti.physics.velocity -= fetti.physics.velocity * dragFriction
        }
        fetti.physics.y += 3
        fetti.physics.tiltAngle += fetti.physics.tiltAngleSpeed

        const {
          x,
          y,
          tiltAngle,
          wobble
        } = fetti.physics
        const wobbleX = x + 10 * Math.cos(wobble)
        const wobbleY = y + 10 * Math.sin(wobble)
        const transform = `translate3d(${wobbleX}px, ${wobbleY}px, 0) rotate3d(1, 1, 1, ${tiltAngle}rad)`

        fetti.element.style.visibility = "visible"
        fetti.element.style.transform = transform
        fetti.element.style.opacity = 1 - progress
      },

      animate(root, fettis, dragFriction, decay, duration, stagger) {
        let startTime = 0

        return new Promise(resolve => {
          const app = this

          function update(time) {
            if (!startTime) startTime = time
              const elapsed = time - startTime
            const progress = startTime === time ? 0 : (time - startTime) / duration
            fettis.slice(0, Math.ceil(elapsed / stagger)).forEach((fetti) => {
              app.updateFetti(fetti, progress, dragFriction, decay)
            })

            if (time - startTime < duration) {
              requestAnimationFrame(update)
            } else {
              fettis.forEach((fetti) => {
                if (fetti.element.parentNode === root) {
                  return root.removeChild(fetti.element)
                }
              })
              resolve()
            }
          }

          requestAnimationFrame(update)
        })
      },
      backwardPatch(config) {
        if (!config.stagger && config.delay) {
          config.stagger = config.delay
        }
        return config
      },
      confetti(root, config) {
        const {
          angle,
          spread,
          startVelocity,
          elementCount,
          width,
          height,
          colors,
          duration,
          stagger,
          dragFriction,
          decay,
          random
        } = Object.assign({}, this.defaults, this.backwardPatch(config))
        const elements = this.createElements(root, elementCount, colors, width, height)
        const fettis = elements.map(element => ({
          element,
          physics: this.randomPhysics(angle, spread, startVelocity, random)
        }))

        return this.animate(root, fettis, dragFriction, decay, duration, stagger)
      }
    },
    mounted () {
      setTimeout(() => {
        this.confetti(this.$el, this.config)
      }, 330)
    }
}
</script>

<style>
.confetti-canon {
    width: 1px;
    height: 1px;
}
</style>
