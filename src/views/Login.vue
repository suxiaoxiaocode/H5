<!-- home -->
<template>
  <div class="home">
    登录
    <img src="@/assets/images/car.jpg" alt />
  </div>
</template>

<script>
import { login } from '@/api/user'
export default {
  data() {
    return {}
  },

  computed: {},

  mounted() {
    login({ phone: 13955555552 }).then(res => {
      console.log(res, '0000')
    })
    // this.$loading.show()
  },

  methods: {}
}
</script>
<style lang="scss" scoped></style>
