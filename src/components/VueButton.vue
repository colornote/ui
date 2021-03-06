<template>
  <component
    :is="component"
    class="vue-ui-button"
    :class="[
      component,
      {
        disabled: finalDisabled,
        loading,
        ghost,
      }
    ]"
    v-bind="$attrs"
    :type="type"
    :tabindex="ghost ? -1 : 0"
    role="button"
    :aria-disabled="ghost"
    @click.capture="handleClick"
    @click.capture.native="handleClick"
  >
    <VueLoadingIndicator
      v-if="loading"
    />

    <span
      class="content"
    >
      <VueLoadingIndicator
        v-if="loadingSecondary"
        class="inline small loading-secondary"
      />
      <VueIcon
        v-else-if="iconLeft"
        :icon="iconLeft"
        class="button-icon left"
      />

      <span class="default-slot">
        <slot>
          {{ label }}
        </slot>
      </span>

      <div v-if="tag != null" class="tag-wrapper">
        <div class="tag">{{ tag }}</div>
      </div>

      <VueIcon
        v-if="iconRight"
        :icon="iconRight"
        class="button-icon right"
      />
    </span>
  </component>
</template>

<script>
import DisabledChild from '../mixins/DisabledChild'

export default {
  name: 'VueButton',

  inheritAttrs: false,

  mixins: [
    DisabledChild,
  ],

  props: {
    iconLeft: {
      type: String,
      default: null,
    },

    iconRight: {
      type: String,
      default: null,
    },

    label: {
      type: String,
      default: null,
    },

    loading: {
      type: Boolean,
      default: false,
    },

    loadingSecondary: {
      type: Boolean,
      default: false,
    },

    type: {
      type: String,
      default: 'button',
    },

    tag: {
      type: [Number, String],
      default: null,
    },
  },
  computed: {
    component () {
      if (this.$attrs.to) {
        return 'router-link'
      } else if (this.$attrs.href) {
        return 'a'
      } else {
        return 'button'
      }
    },

    ghost () {
      return this.finalDisabled || this.loading || this.loadingSecondary
    },
  },

  methods: {
    handleClick (event) {
      if (this.ghost) {
        event.preventDefault()
        event.stopPropagation()
        event.stopImmediatePropagation()
      } else {
        this.$emit('click', event)
      }
    },
  },
}
</script>

<style lang="stylus">
// Base
@import "../style/base"

@import "../style/imports"

$focus-color = $vue-ui-color-dark

colors($dark, $light, $invert = false)
  if $invert
    $foreground = $light
    $background = $dark
  else
    $foreground = $dark
    $background = $light
  button-colors($foreground, $background)
  &.flat
    button-colors($dark, transparent)
    &:not(.ghost)
      &:hover,
      &:active,
      .vue-ui-dropdown.open .dropdown-trigger &
        button-colors($foreground, $background)

.vue-ui-button
  display inline-block
  vertical-align middle
  border none
  font-family inherit
  text-decoration none
  cursor pointer
  user-select none
  position relative
  box-sizing border-box
  border-radius $br
  padding 0 14px
  font-size 14px
  line-height 16px
  height 32px
  > .content
    height 100%
    h-box()
    box-center()
    > .tag-wrapper
      display flex
      box-center()
      margin-left 6px
      > .tag
        padding 2px 2px 0
        border-radius 4px
        font-size 10px
        line-height 10px
        // font-weight bold
        font-family monospace
  &.big
    padding 0 18px
    font-size 16px
    height 44px
    .vue-ui-icon
      width 24px
      height @width
    > .content
      > .tag-wrapper
        > .tag
          padding 2px 4px 0
          border-radius 7px
          font-size 12px
          line-height 12px
  &.round
    border-radius 17px
    // Focus
    &:focus:focus-visible::after
      border-radius (@border-radius + 1px)
    // Big button
    &.big
      border-radius 22px
      // Focus
      &:focus:focus-visible::after
        border-radius (@border-radius + 1px)
  &.flat
    button-transitions()
  &:not(.icon-button)
    > .content
      > .button-icon
        position relative
        &.left
          margin-right 6px
          left -2px
        &.right
          margin-left 6px
          left 2px
      > .loading-secondary
        margin-right 6px
  &.icon-button
    padding 0
    width 32px
    height @width
    &.big
      padding 0
      width 44px
      height @width
    > .content
      width 100%
      > .tag-wrapper
        position absolute
        right 2px
        bottom @right
  &.big-tag
    > .content
      > .tag-wrapper
        right 6px
        bottom @right
  // Round style
  &.ghost
    cursor default
  &.disabled:not(.tab-button)
    opacity .5
    filter grayscale(50%)
  &.loading
    > .content
      visibility hidden
  > .vue-ui-loading-indicator
    position absolute
    top 0
    bottom 0
    left 0
    right 0
  // Colors
  colors($vue-ui-color-dark, $vue-ui-color-light-neutral)
  .vue-ui-dark-mode &
    colors($vue-ui-color-light, $vue-ui-color-dark)
  &.primary
    colors($vue-ui-color-primary, $vue-ui-color-light, true)
  &.accent
    colors($vue-ui-color-accent, $vue-ui-color-light, true)
    .vue-ui-dark-mode &
      colors(lighten($vue-ui-color-accent, 60%), $vue-ui-color-dark, true)
  &.danger
    colors($vue-ui-color-danger, $vue-ui-color-light, true)
  &.warning
    colors($vue-ui-color-warning, $vue-ui-color-light, true)
  &.info
    colors($vue-ui-color-info, $vue-ui-color-light-neutral)
    .vue-ui-dark-mode &
      colors($vue-ui-color-info, $vue-ui-color-dark)
  &.success
    colors($vue-ui-color-success, $vue-ui-color-light-neutral)
    .vue-ui-dark-mode &
      colors($vue-ui-color-success, $vue-ui-color-dark)
  disable-focus-styles()
  // Keyboard focus style
  &:focus:focus-visible
    z-index 1
    &::after
      content ''
      display block
      position absolute
      top 0
      bottom @top
      left @top
      right @top
      border solid 1px
      border-radius ($br + 1px)
      animation vue-ui-focus .3s forwards
</style>
