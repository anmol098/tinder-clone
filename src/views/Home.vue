<template>
  <div class="home">
    <swipe-cards/>
  </div>
</template>

<script>
// @ is an alias to /src
import swipeCards from '@/components/swipeCards'

export default {
  name: 'Home',
  components: {
    swipeCards
  }
}
</script>
