<template>
  <ul :class="paginationClasses.ul">
    <li
      v-if="paginationLabels.prev"
      :class="`${paginationClasses.li} ${hasFirst ? paginationClasses.liDisable : ''}`"
    >
      <button
        :disabled="hasFirst"
        :class="`${paginationClasses.button} ${hasFirst ? paginationClasses.buttonDisable : ''}`"
        @click="prev"
      >
        <span v-html="paginationLabels.prev" />
      </button>
    </li>

    <li
      v-for="page in items"
      :key="page.label"
      :class="`${paginationClasses.li} ${page.active ? paginationClasses.liActive : ''} ${page.disable ? paginationClasses.liDisable : ''}`"
    >
      <span
        v-if="page.disable"
        :class="`${paginationClasses.button} ${paginationClasses.buttonDisable}`"
      >
        ...
      </span>
      <button
        v-else
        :class="`${paginationClasses.button} ${page.active ? paginationClasses.buttonActive : ''}`"
        @click="goto(page.label)"
      >
        {{ page.label }}
      </button>
    </li>

    <li
      v-if="paginationLabels.next"
      :class="`${paginationClasses.li} ${hasLast ? paginationClasses.liDisable : ''}`"
    >
      <button
        :disabled="hasLast"
        :class="`${paginationClasses.button} ${hasLast ? paginationClasses.buttonDisable : ''}`"
        @click="next"
      >
        <span v-html="paginationLabels.next" />
      </button>
    </li>
  </ul>
</template>

<script>
// https://vuejsexamples.com/pagination-component-for-vue-js-2/ Исходники тут
const defaultClasses = {
  ul: 'pagination',
  li: 'pagination__item',
  liActive: 'pagination__item--active',
  liDisable: 'pagination__item--disable',
  button: 'pagination__link',
  buttonActive: 'pagination__link--active',
  buttonDisable: 'pagination__link--disable'
}

const defaultLabels = {
  first: '&laquo;',
  prev: '&lsaquo;',
  next: '&rsaquo;',
  last: '&raquo;'
}

export default {
  name: 'VuePagination',
  props: {
    value: {
      type: Number,
      required: true
    },
    pageCount: {
      type: Number,
      required: true
    },
    classes: {
      type: Object,
      required: false,
      default: () => ({})
    },
    labels: {
      type: Object,
      required: false,
      default: () => ({})
    }
  },

  data () {
    return {
      paginationClasses: {
        ...defaultClasses,
        ...this.classes
      },
      paginationLabels: {
        ...defaultLabels,
        ...this.labels
      }
    }
  },
  computed: {
    items () {
      const valPrev = this.value > 1 ? (this.value - 1) : 1
      const valNext = this.value < this.pageCount ? (this.value + 1) : this.pageCount
      const dotsBefore = valPrev > 3 ? 2 : null
      const dotsAfter = valNext < (this.pageCount - 2) ? (this.pageCount - 1) : null

      const output = []

      for (let i = 1; i <= this.pageCount; i += 1) {
        if ([1, this.pageCount, this.value, valPrev, valNext, dotsBefore, dotsAfter].includes(i)) {
          output.push({
            label: i,
            active: this.value === i,
            disable: [dotsBefore, dotsAfter].includes(i)
          })
        }
      }

      return output
    },

    hasFirst () {
      return (this.value === 1)
    },

    hasLast () {
      return (this.value === this.pageCount)
    },
    hasCurrent () {
      return this.value
    }
  },
  mounted () {
    if (this.value > this.pageCount) {
      this.$emit('input', this.pageCount)
    }
  },
  methods: {
    first () {
      if (!this.hasFirst) {
        this.$emit('input', 1)
      }
    },

    prev () {
      if (!this.hasFirst) {
        this.$emit('input', (this.value - 1))
      }
    },

    goto (page) {
      this.$emit('input', page)
    },

    next () {
      if (!this.hasLast) {
        this.$emit('input', (this.value + 1))
      }
    },

    last () {
      if (!this.hasLast) {
        this.$emit('input', this.pageCount)
      }
    }
  }
}
</script>
