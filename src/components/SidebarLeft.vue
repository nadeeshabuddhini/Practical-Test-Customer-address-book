
<template>
  <v-navigation-drawer class="nav-drawer" app v-model="drawer" :class="{'nav-popup-open':open}">
  <!--side bar header-->
  <template v-slot:prepend>
    <v-list-item class="sidebar-header">
      <v-list-item-content class="sidebar-header-content d-flex">
        <v-icon size="36" class="pr-2">mdi mdi-hexagon-slice-6</v-icon>
        <v-list-item-title class="sidebar-header-title">Dashboard</v-list-item-title>
        <v-list-item-subtitle><sub>v.01</sub></v-list-item-subtitle>
      </v-list-item-content>
    </v-list-item>
  </template>
  <!--side bar content-->
  <v-list class="sidebar-content" nav>
    <v-list-item link v-for="(item, i) in items" :key="i" 
      @click="activateItem(i)" 
      :class="{ 'sidebar-item-active': activeItem === i || (item.text === 'Customers' && activeItem === null) }">
      <template v-slot:prepend>
        <v-icon class="sidebar-content-icon" size="20" :icon="item.icon"></v-icon>
      </template>
      <v-list-item-content class="sidebar-content-wrapper d-flex" >
        <v-list-item-title class="sidebar-content-title">
          {{ item.text }}
        </v-list-item-title>
        <v-icon class="sidebar-arrow-icon" v-if="i !==0">mdi mdi-chevron-right</v-icon>
      </v-list-item-content>
    </v-list-item>    
  </v-list>
  </v-navigation-drawer>
</template>
<script>
import { ref, watchEffect, defineComponent } from 'vue';
export default defineComponent({
  emits: ['toggleDrawer'],
  setup(props, { emit }) {
    const activeItem = ref(null);
    const items = [
      { icon: 'mdi mdi-shield-key-outline', text: 'Dashboard', route: '/dashboard' },
      { icon: 'mdi mdi-cube-scan', text: 'Product', route: '/product' },
      { icon: 'mdi mdi-account-box-outline', text: 'Customers', route: '/customers' },
      { icon: 'mdi mdi-air-humidifier', text: 'Income', route: '/income' },
      { icon: 'mdi mdi-brightness-percent', text: 'Promote', route: '/promote' },
      { icon: 'mdi mdi-comment-question', text: 'Help', route: '/help' },
    ];
    const activateItem = (index) => {
      activeItem.value = index;
    };
    const toggleDrawer = () => {
      emit('toggleDrawer');
    };
    watchEffect(() => {
        if (!props.drawer) {
          activeItem.value = null;
        }
    });

    return {
      items,
      activateItem,
      activeItem,
      toggleDrawer,
    };
  },
   props: {
    drawer: Boolean,
    open: {
      type: Boolean,
      required: true,
    },
  },
});
</script>

<style lang="scss" scoped>
.nav-drawer{
  .sidebar-header{
    margin-top:20px;
    .sidebar-header-content{
      align-items: center;
      .sidebar-header-title{
        font-size: 18px;
        font-weight: 600;
        padding-right: 2px;
      }
    }
  }
  .sidebar-content{
    margin-top: 18px;
    .sidebar-content-icon{
      color: #ADB1C6;
    }
    .sidebar-content-wrapper{
      justify-content: space-between;
      align-items: center;
      .sidebar-content-title{
        font-size: 14px;
        color: #ADB1C6;
      }
      .sidebar-arrow-icon{
        color: #ADB1C6;
      }
    }
    .sidebar-item-active {
        background-color: #5932EA;
    }
  }
}
.nav-popup-open{
  opacity: 0.2;
}

// mobile responsiveness
@media only screen and (min-width: 900px){
  .nav-drawer{
    transform: translateX(0%) !important;
    top:0 !important;
    height: unset !important;
  }
}
</style>
