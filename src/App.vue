<template>
  <v-app>
    <v-main>
      <swipe-cards v-if="isMobile"></swipe-cards>
      <v-overlay :value="!isMobile" opacity="1">
        <v-card
            elevation="2"
        >
          Use mobile device for better experience or resize this window
        </v-card>
      </v-overlay>
    </v-main>
  </v-app>
</template>

<script>
import swipeCards from '@/components/swipeCards'

export default {
  name: 'App',

  components: {
    swipeCards,
  },
  computed:{
    isMobile(){
      switch (this.$vuetify.breakpoint.name) {
        case 'xs':
          return true
        case 'sm':
          return true
        case 'md':
          return true
        case 'lg':
          return false
        case 'xl':
         return false
        default:
          return true
      }
    }
  },
  created() {
    let myWindow
    console.log(this.$vuetify.breakpoint.name);

    //let myWindow = window.open(window.location.href, "", "width=250, height=250");
  },

  data: () => ({
    //
  }),
};
</script>
