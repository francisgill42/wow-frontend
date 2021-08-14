<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      dark
      color=""
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >


       <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon class="white--text">{{ item.icon }}</v-icon>
          </v-list-item-action>
           <v-list-item-content>
            <v-list-item-title class="white--text"  v-text="item.title"/>
          </v-list-item-content>
        </v-list-item>
      </v-list>

       
    </v-navigation-drawer>
    <v-app-bar
      dark
      :clipped-left="clipped"
      fixed
      app
    >
      <v-app-bar-nav-icon  @click.stop="drawer = !drawer" />
      {{title}}
      <v-spacer />
      <span v-if="this.$auth.user" >
      Welcome, <b>{{this.$auth.user.name}}</b>
      <v-btn text @click="logout">      

        {{logout_btn.label}}
        <v-icon >{{ logout_btn.icon }}</v-icon>
      </v-btn>
     </span>
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
   
    <v-footer
      :fixed="fixed"
      app
    >
      <span>&copy; {{year}}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  mounted () {
    
  },
  data () {
    return {
      year: new Date().getFullYear(),
      clipped: false,
      drawer: true,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Home',
          to: '/'
        },
         {
          icon: 'mdi-chart-bubble',
          title: 'Category',
          to: '/category'
        },
          {
          icon: 'mdi-chart-bubble',
          title: 'Sub Category',
          to: '/subcategory'
        },
         {
          icon: 'mdi-chart-bubble',
          title: 'Brand',
          to: '/brand'
        },
         {
          icon: 'mdi-chart-bubble',
          title: 'Album',
          to: '/album'
        },
         {
          icon: 'mdi-chart-bubble',
          title: 'Color',
          to: '/color'
        },
        
  
       
      ],
      
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'WOW',
      logout_btn:{
        icon:'mdi-logout',
        label:'Logout'
      }
    }
  },
  methods:{
  async logout() {
    await this.$auth.logout();
  },
  }
}
</script>
