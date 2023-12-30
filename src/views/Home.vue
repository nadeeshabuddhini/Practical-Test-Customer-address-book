<template>
  <v-app :style="{ background: $vuetify.theme.themes.light.colors.primary}" :class="{ 'home-background': isAddCusPopupOpen }">
    <!--mobile app bar--> 
    <v-app-bar app class="mobile-app-bar">
      <v-app-bar-nav-icon @click.stop="toggleDrawer"></v-app-bar-nav-icon>
      <v-spacer></v-spacer>
    </v-app-bar>

    <!--dashboard component-->
    <SidebarLeft :drawer="drawer" @toggleDrawer="toggleDrawer" :open="isAddCusPopupOpen" />

    <!--home page start-->
    <v-main class="home-main">
      <v-container class="home-wrapper">
        <!--start home header-->
        <v-row class="header-wrapper">
          <v-col cols="10" sm="8">
            <div>
              <span class="header-content">Hello Evano </span>
              <img :src="imgSource" alt="Wave the hand" width="24" height="24">
              <span>,</span>
            </div>
          </v-col>
          <v-col cols="2" class="d-flex" sm="4">
            <v-avatar size="24" class="popup-btn-wrapper ml-auto">
            <v-icon class="open-popup-btn" @click="openAddCusPopup" size="32">mdi mdi-plus-circle-outline</v-icon>
            </v-avatar>
          </v-col>
        </v-row>

        <!--start home sub header-->
        <v-row class="header-subcontent">
          <v-col cols="12">
             <v-card class="card-subcontent d-flex" width="auto">
              <v-row v-for="(card,id) in cards" :key="id" class="subcontent-row">
                <v-col cols="12" sm="4" class="subcontent-icon">
                  <v-avatar size="64" class="icon-wrapper">
                   <v-icon class="subcontent-icon" :icon="card.icon" size="56"></v-icon>
                  </v-avatar>
                </v-col>
                <v-col cols="12" sm="8">
                  <v-card-subtitle>{{card.subtitle}}</v-card-subtitle>
                  <v-card-title class="subcontent-count">{{card.title}}</v-card-title>
                  <v-card-text  v-if="id !== 2">
                    <v-icon :icon="card.arrow"
                     :style="{ color: card.arrow === 'mdi mdi-arrow-down' ? '#D0004B' : '#00AC4F' }">
                    </v-icon>
                    <span :style="{ color: card.arrow === 'mdi mdi-arrow-down' ? '#D0004B' : '#00AC4F' }">
                      {{card.percentage}}</span> this month
                  </v-card-text>
                  <v-card-text v-else>
                    <v-row class="image-row">
                      <v-col cols="1" v-for="(image, index) in imageGroup" :key="index" class="image-container">
                        <v-img
                          :src="image.url"
                          alt="Image"
                          aspect-ratio="1.5"
                          width="24"
                          height="24"
                          class="overlay-image"
                        ></v-img>
                      </v-col>
                    </v-row>
                  </v-card-text>
                </v-col>
              </v-row>
             </v-card>
          </v-col>
        </v-row>

        <!--start table view-->
        <v-row>
          <v-col>
            <v-card class="table-view-content">
              <v-card-text class="table-wrapper">
                <v-row>
                  <v-col cols="12" sm="6">
                    <v-card-title class="table-title">All Customers</v-card-title>
                    <v-card-subtitle class="table-subtitle">Active Members</v-card-subtitle>
                  </v-col>
                  <v-row class="table-field-content">
                    <v-col cols="6">
                      <v-text-field 
                      v-model="search"
                      density="comfortable" 
                      label="Search" 
                      prepend-inner-icon="mdi mdi-magnify" 
                      class="search-bar"
                      bg-color="#F7F9FF"
                      variant="plain">
                      </v-text-field>
                    </v-col>
                    <v-col cols="6">
                      <v-select
                      :items="select"
                      density="comfortable"
                      label="Short by:"
                      bg-color="#F7F9FF"
                      variant="plain"></v-select>
                    </v-col>
                  </v-row>
                </v-row>
                <v-table class="customer-table">
                  <thead>
                    <tr>
                      <th v-for="column in tableColumns" 
                      :key="column.key" 
                      class="customer-table-header">
                        {{ column.label }}
                      </th>
                    </tr>
                  </thead>
                  <tbody>
                    <template v-for="(item, index) in customers" :key="item.name">
                      <tr class="clickable-row"  @click="toggleRow(index)">
                        <td v-for="column in tableColumns" :key="column.key">
                          <template v-if="column.key==='status'">
                            <v-btn class="status-btn" 
                            :class="{'inactive-class':item.status==='Inactive'}">
                            {{ item[column.key] }}</v-btn>
                          </template>
                          <template v-else>
                            {{item[column.key]}}
                          </template>
                        </td>
                      </tr>
                    <template v-for="list in expandDetails" :key="list">
                      <tr v-if="expandedRows.includes(index)" class="expanded-row">
                        <td></td>
                        <td>{{list.address}}</td>
                        <td>{{list.number}}</td>
                        <td>{{list.street}}</td>
                        <td>{{list.country}}</td>
                        <td></td>
                      </tr>
                    </template>
                    </template>  
                  </tbody>
                </v-table>
                <!--start pagination row-->
                <v-row class="pagination-row">
                  <v-col cols="12" sm="6">
                    <v-card-subtitle class="pagination-text">
                      Showing data 1 to 8 of 256k entries
                    </v-card-subtitle>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-pagination
                        v-model="page"
                        :total-visible="5"
                        :length="40"
                        active-color="#5932EA">
                    </v-pagination>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card> 
          </v-col>  
        </v-row>

        <!--customer popup open-->
        <AddCusPopup :open="isAddCusPopupOpen" @close="closeAddCusPopup"/>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import { ref, onMounted, defineComponent} from 'vue';

// Components
import SidebarLeft from '../components/SidebarLeft.vue';
import AddCusPopup from '../components/AddCusPopup.vue'


export default defineComponent({
  name: 'HomeView',

  components: {
    SidebarLeft,
    AddCusPopup,
  },
  setup() {
    const imgSource = ref('');
    const isAddCusPopupOpen = ref(false);
    const page = ref(1);
    const drawer = ref(false);
    const expandedRows = ref([])
    //profile image list
    const imageGroup = ref([
        {
          url: 'https://media.istockphoto.com/id/1393750072/vector/flat-white-icon-man-for-web-design-silhouette-flat-illustration-vector-illustration-stock.jpg?s=1024x1024&w=is&k=20&c=r--oPfS14d-ybe3adW-c_oy6q1tCz1c16SN8h5EdoKk=',
        },
        {
          url: 'https://media.istockphoto.com/id/587805156/vector/profile-picture-vector-illustration.jpg?s=1024x1024&w=is&k=20&c=N14PaYcMX9dfjIQx-gOrJcAUGyYRZ0Ohkbj5lH-GkQs=',
        },
        {
          url: 'https://media.istockphoto.com/id/1313958250/vector/user-avatar-profile-icon-black-vector-illustration-on-transparent-background-website-or-app.jpg?s=1024x1024&w=is&k=20&c=uUqP7QXNIcLTkQpRLwtnjU6z1bDC50ISz9BJyO41hNA=',
        },
        {
          url: 'https://media.istockphoto.com/id/1390839796/vector/user-profil-icon-avatar-symbol-sign-account-vector.jpg?s=1024x1024&w=is&k=20&c=tl9-5hRoP0Sfl-T6WJ41DwwgwQQ9bFK2IL2JEmcGVkI=',
        },
        {
          url: 'https://media.istockphoto.com/id/1393750072/vector/flat-white-icon-man-for-web-design-silhouette-flat-illustration-vector-illustration-stock.jpg?s=1024x1024&w=is&k=20&c=r--oPfS14d-ybe3adW-c_oy6q1tCz1c16SN8h5EdoKk=',
        },
      ])
    //home sub header content
    const cards =[
      {icon:'mdi mdi-account-multiple', title:'5,423', subtitle:'Total Customers', arrow:'mdi mdi-arrow-up', percentage:'18%'},
      {icon:'mdi mdi-account-check-outline', title:'1,893', subtitle:'Members', arrow:'mdi mdi-arrow-down', percentage:'1%'},
      {icon:'mdi mdi-monitor', title:'189', subtitle:'Active Now'}
    ];
    //dropdown button menu
    const select=['Newset','Old','Middle'];
    //customer table header
    const tableColumns=[
      { key: 'name', label: 'Customer Name' },
      { key: 'company', label: 'Company' },
      { key: 'number', label: 'Phone Number' },
      { key: 'email', label: 'Email' },
      { key: 'country', label: 'Country' },
      { key: 'status', label: 'Status' },
    ];
    //customer table row data
    const customers=[
      { name: 'Jane Cooper', company:'Microsoft', number:'(225) 555-0118', email:'jane@microsoft.com', country: 'United States', status: 'Active',description:'test' },
      { name: 'Floyd Miles', company:'Yahoo', number:'(205) 555-0100', email: 'floyd@yahoo.com', country:'Kiribati', status: 'Inactive' },
    ];
    //expanded table address row data
    const expandDetails=[
      {address:'Address:1', number:'No 279', street:'Welligton Street', country:'Melbourne, VIC'},
      {address:'Address:2', number:'No 29', street:'Blackburn Rd', country:'Melbourne, VIC'},
    ];
    //expanded table row
    const toggleRow = index => {
      if (expandedRows.value.includes(index)) {
        expandedRows.value = expandedRows.value.filter(i => i !== index)
      } else {
        expandedRows.value = [...expandedRows.value, index]
      }
    }
    //navigation drawer
    const toggleDrawer = () => {
      drawer.value = !drawer.value;
    };
    //open the popup
    const openAddCusPopup = () => {
      isAddCusPopupOpen.value = true;
    }; 
    //close the popup
    const closeAddCusPopup = () => {
      isAddCusPopupOpen.value = false;
    };

    onMounted(() => {
      import('@/assets/images/hand-wave.png')
        .then((module) => {
          imgSource.value = module.default;
        })
        .catch((error) => {
          console.error('Error loading image:', error);
        });  
    });

    return {
      imgSource,
      cards,
      select,
      tableColumns,
      customers,
      openAddCusPopup,
      closeAddCusPopup,
      isAddCusPopupOpen,
      page,
      drawer,
      toggleDrawer,
      expandedRows,
      toggleRow,
      expandDetails,
      imageGroup,   
    };
  },
});
</script>

<style lang="scss" scoped>
.home-wrapper{
  .header-wrapper{
    justify-content: space-between;
   .header-content{
    font-weight:500;
   }
  .popup-btn-wrapper{
    background: #E7FFF2;
    border-radius: 50%;
    .open-popup-btn{
      color:#00AC4F;
    }
  }
  }
  .header-subcontent{
    .card-subcontent{
      border-radius: 18px;
      .subcontent-row{
        display: flex;
        margin-top: unset;
        align-items: center;
        .subcontent-icon{
          display: flex;
          justify-content:end;
          .icon-wrapper{
            background-color: #E7FFF2;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            .subcontent-icon{
              color:#00AC4F;
            }
          }
        }
        .subcontent-count{
          padding-top:unset;
          padding-bottom:unset;
          font-weight: 600;
        }
        .image-row{
          margin-left: 2px;
          .image-container {
            position: relative;
            padding-right: 0;
            .overlay-image {
              position: absolute;
              top: 4px;
              left: 0;
            }
          }
        }
      }
    }
  }
  .table-view-content{
    border-radius: 18px;
    .table-wrapper{
      padding: 36px;
    }
    .table-title{
      font-size:16px;
      font-weight: 600;
      padding-bottom: unset;
    }
    .table-subtitle{
      font-size: 12px;
      color:#00AC4F;
    }
    .table-field-content{
      margin-top:20px;
      .search-bar{
        color: #00AC4F;
      }
    }
    .customer-table{
      font-size: 12px;
      .customer-table-header{
        color: #B5B7C0;
      }
      .status-btn{
        width: 80px;
        height: 24px;
        font-size: 12px;
        text-transform: capitalize;
        color: #008767;
        background: #A6E7D8;
        border: 1px solid #008767;
      }
      .inactive-class{
          color: #E73434;
          background: #FFC5C5;
          border: 1px solid #E73434;
      }
      .expanded-row{
        color: #BFBFBF;
      }  
    }
    .pagination-row{
      margin-top:10px;
      .pagination-text{
        font-size: 12px;
      }
      .v-pagination{
        ::v-deep .v-pagination__list {
          justify-content: end;
        }
        ::v-deep .v-btn{
          height: 24px;
          width: 24px;
          background: #f5f4f2;
        }
      }
    }
  }
}

.mobile-app-bar {
  display: none;
}

//mobile responsiveness
@media only screen and (max-width:900px) {
  .mobile-app-bar{
    display: flex;
  }
  .subcontent-row{
    display: inline !important;
    .subcontent-icon{
      justify-content: center !important;
    }
  }
  .pagination-row{
    display: block !important;
    .v-pagination{
      ::v-deep .v-pagination__list {
        justify-content: start !important;
      }
    } 
  }  
}
.home-background{
  background:rgb(180, 178, 179) !important; 
  .header-wrapper,.header-subcontent,.table-view-content,.nav-drawer{
    opacity: 0.2;
  }
}
@media only screen and (min-width: 900px){
  .home-main{
  padding-left: 256px !important;
  padding-top: 0 !important;
  }
}
</style>