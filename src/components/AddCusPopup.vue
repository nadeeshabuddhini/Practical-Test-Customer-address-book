
  <template>
  <div class="popup"> 
  <v-card v-show="open" class="custom-dialog" :style="{ 'max-width': '600px' }">
    <!--popup header--> 
    <v-icon class="close-icon" @click="$emit('close')">mdi mdi-close</v-icon>
    <v-card-title class="dialog-title">Add New Customer</v-card-title>
    <!--popup customer details form-->
    <v-card-text class="dialog-content">
      <v-text-field v-model="textField1" label="Customer name" variant="underlined"></v-text-field>
      <v-text-field v-model="textField1" label="Company" variant="underlined"></v-text-field>
      <v-text-field v-model="numberValue" label="Contact Phone" variant="underlined" :rules="numberRules"></v-text-field>
      <v-text-field v-model="textField1" label="E-mail" variant="underlined"></v-text-field>
      <v-text-field v-model="textField1" label="Country" variant="underlined"></v-text-field>
      <v-text-field type="date" v-model="dob" label="Date of Birth" variant="underlined"></v-text-field>
    </v-card-text>
    <!--popup address details form-->
    <v-card-subtitle class="dialog-subtitle">Address Details</v-card-subtitle>
    <v-card-text class="address-content" v-for="(address, index) in addressList" :key="index">
      <v-row class="address-label-row" v-if="index !== 0">
        <v-col cols="10">Address:<span class="address-line">{{index}}</span></v-col>
        <v-col cols="2">
          <v-btn class="delete-btn" elevation="0" @click="deleteAddress(index)">Delete</v-btn> 
        </v-col>
      </v-row>
      <v-row class="address-row-one" >
      <v-col cols="4">
        <v-text-field label="Number" v-model="address.number" variant="underlined"></v-text-field>
      </v-col>
      <v-col cols="8">
        <v-text-field label="Street" v-model="address.street" variant="underlined"></v-text-field>
      </v-col>
    </v-row>
    <v-row class="address-row-two">
      <v-col class="address-col">
        <v-text-field label="City, State" v-model="address.cityState" variant="underlined"></v-text-field>
      </v-col>
    </v-row>
    </v-card-text>  
     <v-btn class="add-btn" elevation="0" @click="addAddress">Add</v-btn> 
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn @click="$emit('close')" class="submit-btn" block>submit</v-btn>
    </v-card-actions>
  </v-card>
  </div>
</template>

<script>
import {ref} from 'vue';
export default {
  setup(){
    const numberValue = ref('');
    const addressList = ref([{ number: '', street: '', cityState: '' }]);
    //phone number validation here
    const numberRules = [
      v => !!v || 'Phone number is required',
      v => (v && v.length === 10 && /^\d+$/.test(v)) || 'Phone number must be 10 digits',
    ];
    //add the address form here
    const addAddress = () => {
      addressList.value.push({ number: '', street: '', cityState: '' });
    };
    //delete the address form here
    const deleteAddress = (index) => {
      addressList.value.splice(index, 1);
    };

    return {
      numberValue,
      numberRules,
      addressList,
      addAddress,
      deleteAddress,
    };
  },
  props: {
    open: {
      type: Boolean,
      required: true,
    },
  },
};
</script>

<style lang="scss" scoped>
.popup{
  .custom-dialog {
    background-color: white;
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 100%; 
    z-index: 1000;
    padding: 20px 40px;
    border-radius: 20px;
    max-height: 90vh;
    overflow-y:auto;
    .close-icon{
      position: absolute;
      top: 40px;
      right: 40px;
      cursor: pointer;
    }
    .dialog-title{
      font-size: 24px;
      font-weight: 600;
    }
    .dialog-content{
      .v-text-field{  
        height: 48px;
        ::v-deep .v-label {
        font-size:small !important;
        } 
      }
    }
    .dialog-subtitle{
      font-size: 18px;
      opacity: unset;
      font-weight: 500;
      margin-top: 6px;
    }
    .address-content{
      padding-top: unset;
      padding-bottom: unset;
      .v-text-field{  
        height: 48px !important;
        ::v-deep .v-label {
        font-size:small !important;
        }   
      }
      .address-row-two{
        margin-top: unset;
        margin-bottom: 12px;
        .address-col{
          padding-top: unset;
        }
      }
      .address-label-row{
        border-bottom: 1px solid;
        .delete-btn{
          color: #00AC4F;
          border: 2px solid #00AC4F;
          font-size: 12px;
          height: 24px;
        }
      } 
    }
    .add-btn{
      color: #00AC4F;
      border: 2px solid #00AC4F;
      width: 100px;
      font-size:12px;
      margin-left: 10px
    }
    .submit-btn{
      color: white;
      background: #00AC4F;
      font-size: 12px;
    }
}
}
</style>
