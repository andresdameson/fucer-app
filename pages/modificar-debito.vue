<template>
  <main id="contenido" class="modificar-datos">
    <section class="band">
    	<div class="container">
    		<nuxt-link :to="{ name: 'configuracion' }"><img src="~/assets/img/arrow-left.svg" alt="Volver" class="arrow-left"></nuxt-link>
    		<h2 ref="pageFocusTarget">Modificar débito automático</h2>

    		<mensaje :tipo="mensajeTipo" :texto="mensajeTexto" />

    		<form method="post" @submit.prevent="actualizarDatos" class="main__form">
          <fieldset>
            <label for="cbu">CBU</label>
            <input
              type="text"
              v-model.lazy="cbu"
              id="cbu"
              ref="cbu"
              name="cbu"
              v-validate="'required'"
              data-vv-as="CBU"
              :class="{'error': errors.has('cbu') }"
              onselectstart="return false"
              onpaste="return false"
              onCopy="return false"
              onCut="return false"
              onDrag="return false"
              onDrop="return false"
              autocomplete="off"
            />
            <span class="error" v-show="errors.has('cbu')">
              {{ errors.first('cbu') }}
            </span>
          </fieldset>

          <fieldset>
            <label for="cuit">CUIT</label>
            <input
              type="text"
              v-model.lazy="cuit"
              id="cuit"
              ref="cuit"
              name="cuit"
              v-validate="'required'"
              data-vv-as="CUIT"
              :class="{'error': errors.has('cuit') }"
            />
            <span class="error" v-show="errors.has('cuit')">
              {{ errors.first('cuit') }}
            </span>
          </fieldset>

          <fieldset>
            <label for="cuit">Código RS</label>
            <input
              type="text"
              v-model.lazy="rs"
              id="rs"
              ref="rs"
              name="rs"
              data-vv-as="Código RS"
              :class="{'error': errors.has('rs') }"
            />
            <span class="error" v-show="errors.has('rs')">
              {{ errors.first('rs') }}
            </span>
          </fieldset>

          <fieldset>
            <label for="nombre">Nombre</label>
            <input
              type="text"
              v-model.lazy="nombre"
              id="nombre"
              ref="nombre"
              name="nombre"
              data-vv-as="Nombre"
              v-validate="'required'"
              :class="{'error': errors.has('nombre') }"
            />
            <span class="error" v-show="errors.has('nombre')">
              {{ errors.first('nombre') }}
            </span>
          </fieldset>

          <fieldset>
            <label for="apellido">Apellido</label>
            <input
              type="text"
              v-model.lazy="apellido"
              id="apellido"
              ref="apellido"
              name="apellido"
              data-vv-as="Apellido"
              v-validate="'required'"
              :class="{'error': errors.has('apellido') }"
            />
            <span class="error" v-show="errors.has('apellido')">
              {{ errors.first('apellido') }}
            </span>
          </fieldset>

          <button type="submit" class="rounded__btn--full blue">
            {{ txtBtnSubmit}}
          </button>

        </form>
    	</div>
    </section>
  </main>
</template>

<script>
import {mapState} from 'vuex'
import mensaje from '~/mixins/mensaje'

export default {
  mixins: [mensaje],
  middleware: 'plan-no-ilimitado',
  data() {
    return {
      title: 'Modificar débito automático',
      cuit: this.$auth.user.suscripcion.metadata.cuit,
      rs: this.$auth.user.suscripcion.metadata.rs,
      cbu: this.$auth.user.suscripcion.metadata.cbu,
      nombre: this.$auth.user.suscripcion.metadata.nombre,
      apellido: this.$auth.user.suscripcion.metadata.apellido,
    }
  },

  computed: {
    ...mapState([
      'pagina'
    ]),
    txtBtnSubmit () {
      return this.pagina.cargando ? 'Cargando...' : 'Actualizar'
    },
  },

  beforeRouteEnter (to, from, next) {
    next(vm => {
      vm.$announcer.set(
        `${vm.title} ${vm.$announcer.options.complementRoute}`,
        vm.$announcer.options.politeness
      )
      vm.$utils.moveFocus(vm.$refs.pageFocusTarget.$el)
    })
  },
  
  mounted() {
    this.$refs.cbu.focus()
  },

  methods: {
    async actualizarDatos (event) {
      let valida = await this.$validator.validateAll()
      if (!valida) {
        return
      }

      try {
        await this.$axios.$patch('suscripciones/datos', {
          tipo: 'debito',
          datos: {
            rs: this.rs,
            cuit: this.cuit,
            cbu: this.cbu,
            nombre: this.nombre,
            apellido: this.apellido,
          }
        })
        await this.$auth.fetchUser()
        this.setMensaje('Los datos fueron actualizados', 'info', 3000)
      } catch(error) {
        this.setMensaje(error, 'error')
      }
    }
  },
	head () {
	  return {
	    title: this.title,
	  }
	}
}
</script>

<style lang="sass">@import 'sass/pages/modificar-datos.sass'</style>