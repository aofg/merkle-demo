<template>
  <div class="merkle">
    <!-- <div class="merkle__hash">{{ branch.hash }}</div> -->
    <div class="merkle__shot" ref="shot"></div>
    <div class="merkle__children" v-if="branch.branches && branch.branches.length">
      <merkle-tree v-for='(child, childIndex) in branch.branches' :key='child.hash + childIndex + level' :branch='child' :depth='level + 1'></merkle-tree>
    </div>
  </div>
</template>
<script>
import blockies from 'blockies'
export default {
  name: 'merkle-tree',
  props: ['branch', 'depth'],
  computed: {
    level () {
      return this.depth || 1
    }
  },

  watch: {
    branch: 'blockit'
  },

  mounted () {
    this.blockit()
  },

  methods: {
    blockit () {
      const shot = blockies({ // All options are optional
        seed: this.branch.hash, // seed used to generate icon data, default: random
        size: 10, // width/height of the icon in blocks, default: 10
        scale: 2 // width/height of each block in pixels, default: 5
      })

      this.$refs.shot.innerHTML = ''
      this.$refs.shot.appendChild(shot)
    }
  }
}
</script>

<style lang="scss">
.merkle {
  flex-basis: 25%;
}

.merkle__children {
  position: relative;
  display: flex;

  &:before {
    content: "";
    display: block;
    position: absolute;
    border-bottom: 1px solid gray;
    left: 0; right: 0;
    top: 5px;
  }
}
</style>
