<template>
  <v-list-group
    :group="group"
    :prepend-icon="item.icon"
    :sub-group="subGroup"
    no-action
  >
    <core-item
      slot="activator"
      :text="item.text"
    />
    <template v-for="(child, i) in children">
      <core-sub-group
        v-if="child.group != null"
        :key="`sub-group-${i}`"
        :item="child"
      />
      <core-item
        v-else
        :chip="genChip(child)"
        :icon="child.icon"
        :key="`item-${i}`"
        :subtext="child.subtext"
        :to="child.to"
        :text="child.text"
      />
    </template>
  </v-list-group>
</template>

<script>
  // Utilities
  import kebabCase from 'lodash/kebabCase'
  import { genChip } from '@/util/helpers'

  export default {
    inheritAttrs: false,

    props: {
      item: {
        type: Object,
        default: () => ({
          text: '',
          group: '',
          children: []
        })
      },
      subGroup: {
        type: Boolean,
        default: false
      }
    },

    computed: {
      children () {
        return this.item.children.map(item => ({
          ...item,
          to: `${this.item.group}/${item.to}`
        }))
      },
      group () {
        return this.genGroup(this.item.children)
      }
    },

    methods: {
      genChip,
      genGroup (children) {
        return children.map(item => {
          let parent = item.group || this.item.group
          let group = `${parent}/${kebabCase(item.to)}`

          if (item.children) {
            group = `${group}|${this.genGroup(item.children)}`
          }

          return group
        }).join('|')
      }
    }
  }
</script>
