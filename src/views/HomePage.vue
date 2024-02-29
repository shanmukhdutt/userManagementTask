<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>USER MANAGEMENT</ion-title>
        <ion-buttons slot="end">
          <ion-button class="create-button" @click="showCreateModal">Create New User</ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content>
     <ion-searchbar v-model="searchQuery" placeholder="Search"></ion-searchbar>
     <ion-card v-for="(user,index) in filteredUsers" :key="index">
      <ion-card-title>
        <ion-grid>
          <ion-row>
            <ion-col size="3">FirstName:{{ user.firstName }}</ion-col>
            <ion-col size="3">LastName:{{ user.lastName }}</ion-col>
            <ion-col size="3">Mobile Number:{{ user.mobileNumber }}</ion-col>
            <ion-col size="3">Location:{{ user.location }}</ion-col>
          </ion-row>
          <ion-row>
            <ion-col>
              <ion-buttons class="buttons">
                <ion-button class="edit-button" @click="editUser(index)">Edit</ion-button>
                <ion-button class="delete-button" @click="deleteUser(index)">Delete</ion-button>
              </ion-buttons>
            </ion-col>
          </ion-row>
        </ion-grid>
      </ion-card-title>
     </ion-card>
    </ion-content>

    <ion-modal :is-open="showCreateModalState" @closed="showCreateModalState = false">
      <ion-content class="custom-modal">
        <div class="input-field">
          <ion-input v-model="firstName" placeholder="FirstName"></ion-input>
          <ion-input v-model="lastName" placeholder="LastName"></ion-input>
          <ion-input v-model="mobileNumber" type="number" placeholder="Mobile Number"></ion-input>
          <ion-input v-model="location" placeholder="Location"></ion-input>
        </div>
        <ion-buttons class="buttons">
          <ion-button class="save-button" @click="saveUser">Save</ion-button>
          <ion-button class="close-button" @click="closeModal">Close</ion-button>
        </ion-buttons>
      </ion-content>
    </ion-modal>
  </ion-page>
</template>

<script lang="ts">
import { IonButton, IonButtons,IonCard, IonCardTitle, IonContent, IonCol, IonGrid, IonHeader, IonInput, IonModal, IonPage, IonRow, IonSearchbar, IonTitle, IonToolbar, alertController  } from '@ionic/vue';
import {defineComponent, ref, watch, computed} from 'vue';

interface User{
  firstName: string;
  lastName: string;
  mobileNumber: number;
  location: string;
}

export default defineComponent({
  name:'HomePage',
  components:{
    IonButton,
    IonButtons,
    IonCard,
    IonCardTitle,
    IonContent,
    IonCol,
    IonGrid,
    IonHeader,
    IonInput,
    IonModal,
    IonPage,
    IonRow,
    IonSearchbar,
    IonTitle,
    IonToolbar
  },
  setup(){
    const showCreateModalState = ref(false);
    const firstName = ref('');
    const lastName = ref('');
    const mobileNumber =ref('');
    const location = ref('');
    const users = ref<User[]>([]);
    const searchQuery = ref('');
    
    watch(searchQuery,(newValue) => {
      filterUsers(newValue)
    });

    const filterUsers = (query: any) => {
      return users.value.filter(user => {
        const fullName = `${user.firstName} ${user.lastName}`.toLowerCase();
        return fullName.includes(query.toLowerCase())
      })
    };

    const filteredUsers = computed(() => {
      return filterUsers(searchQuery.value)
    })

    const showCreateModal = () =>{
      showCreateModalState.value = true;
      clearInputFields();
    };

    const closeModal = () => {
      showCreateModalState.value = false;
    }

    const saveUser = () => {
      const newUser = {
        firstName: firstName.value,
        lastName: lastName.value,
        mobileNumber: parseInt(mobileNumber.value),
        location: location.value
      };
      users.value.push(newUser);
      localStorage.setItem('users',JSON.stringify(users.value));
      clearInputFields()
      closeModal();
    };

    const editUser = async (index: number) => {
      const user = users.value[index];
      const alert = await alertController.create({
        header: 'Edit User',
        inputs: [
          {
            name: 'firstName',
            type: 'text',
            placeholder: 'First Name',
            value: user.firstName
          },
          {
            name: 'lastName',
            type: 'text',
            placeholder: 'Last Name',
            value: user.lastName
          },
          {
            name: 'mobileNumber',
            type: 'tel',
            placeholder: 'Mobile Number',
            value: user.mobileNumber
          },
          {
            name: 'location',
            type: 'text',
            placeholder: 'Location',
            value: user.location
          }
        ],
        buttons: [
          {
            text: 'Cancel',
            role: 'cancel'
          },
          {
            text: 'Save',
            handler: (data) => {
              users.value[index] = {
                firstName: data.firstName,
                lastName: data.lastName,
                mobileNumber: data.mobileNumber,
                location: data.location
              }
            }
          }
        ]
      });
      await alert.present();
    };

    const deleteUser = (index:number) => {
      users.value.splice(index,1)
    };

    const clearInputFields = () => {
      firstName.value = '';
      lastName.value = '';
      mobileNumber.value = '';
      location.value = '';
    };

    return{
      firstName,
      lastName,
      mobileNumber,
      location,
      searchQuery,
      filterUsers,
      filteredUsers,
      saveUser,
      closeModal,
      showCreateModal,
      showCreateModalState,
      editUser,
      deleteUser
    }

  }
})
</script>

<style scoped>
.input-field{
  margin-bottom: 15px;
};
.buttons{
 display: flex;
 flex-direction: row;
}
.save-button{
  background-color: #2196f3;
  color: white;
  margin-right: 10px;
  padding: 1px 2px;
  border-radius: 15px;;
}
.close-button{
  background-color: #f44336;
  color: white;
  padding: 1px 2px;
  border-radius: 15px;;
}
.create-button{
  padding: 10px ;
  border-radius: 20px;
  background-color: #c4f7c7;
  color: rgb(35, 66, 8);
}
.edit-button{
  padding: 0.5px;
  border-radius: 20px;
  background-color: #e2e462;
  color:black;
  margin-right: 10px;
}
.delete-button{
  padding: 0.5px;
  border-radius: 20px;
  background-color: #fe695f;
  color:white;
  margin-left: 10px;
}
</style>
