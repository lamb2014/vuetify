<template lang="pug">
  li(class="sidebar__group")
    a(
      class="sidebar__group-header"
      v-bind:class="classes"
      v-bind:href="item.href"
      v-on:click.prevent="toggle"
    )
      template(v-if="item.icon")
        v-icon {{ item.icon }}
      span(v-text="item.text")
    transition(
      v-on:enter="enter"
      v-on:leave="leave"
    )
      v-sidebar-items(
        v-show="active"
        v-bind:items="items"
        ref="group"
      )
        slot
</template>

<script>
  import Eventable from '../../mixins/eventable'
  import { closest, addOnceEventListener } from '../../util/helpers'

  export default {
    name: 'sidebar-group',

    mixins: [Eventable],

    data () {
      return {
        active: false,
        height: 0
      }
    },

    props: {
      item: {
        type: Object,
        default () {
          return { href: '#!', text: '',icon: false }
        }
      },

      items: {
        type: Array,
        default: () => []
      }
    },
    
    computed: {
      classes () {
        return {
          'sidebar__group-header--active': this.active
        }
      },

      events () {
        return [
          [`sidebar-group:close:${this.sidebar}`, this.close],
          [`sidebar-group:open:${this.sidebar}`, this.open]
        ]
      },

      sidebar () {
        let sidebar = closest.call(this, 'sidebar')

        return sidebar ? sidebar.id : null
      }
    },

    mounted () {
      if (this.$refs.group.$el.querySelector('.sidebar__item--active')) {
        this.active = true
      }
    },

    methods: {
      enter (el, done) {
        el.style.display = 'block'
        el.style.height = 0
        
        setTimeout(() => el.style.height = `${el.scrollHeight}px`, 0)

        addOnceEventListener(el, done, 'transitionend')
      },

      leave (el, done) {
        el.style.height = 0
        
        addOnceEventListener(el, done, 'transitionend')
      },

      open () {
        this.active = true
      },

      toggle () {
        this.active = !this.active
      },

      close (uid) {
        this.active = uid === this._uid
      }
    }
  }
</script>