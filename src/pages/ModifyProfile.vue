<script>
import AlertPop from '../components/AlertPop.vue';
import SendButton from '../components/SendButton.vue';
import MainH1 from '../components/MainH1.vue';
import MainLabel from '../components/MainLabel.vue';
import MainButton from '../components/MainButton.vue';
import { subscribeToAuth, updateUser } from '../services/auth';

export default {
  name: 'ModifyProfile',
  components: { AlertPop, SendButton, MainH1, MainLabel, MainButton },
  data() {
    return {
      userProfile: {
        displayName: '',
        pet: '',
        petBio: '',
      },
      authUser: {
        id: '',
        email: '',
        displayName: '',
        pet: '',
        petBio: '',
        photoURL: null,
      },
      unsubscribeFromAuth: () => { },
      editingProfile: false,
      showValidationError: false,
    }
  },
  methods: {
    async sendChanges() {

      if (!this.userProfile.displayName.trim() || !this.userProfile.pet.trim()) {
        this.showValidationError = true;
        return;
      }

      this.editingProfile = true;
      this.showValidationError = false;

      try {
        await updateUser({
          displayName: this.userProfile.displayName,
          pet: this.userProfile.pet,
          petBio: this.userProfile.petBio,
        });
        this.$router.push('/perfil');
      } catch (error) {
        console.error('[modifyProfile] Error al editar perfil:', error);
        this.showValidationError = true;
      } finally {
        this.editingProfile = false;
      }
    }
  },
  async mounted() {
    this.unsubscribeFromAuth = subscribeToAuth(newUserData => {
      this.authUser = newUserData;

      this.userProfile.displayName = this.authUser.displayName;
      this.userProfile.pet = this.authUser.pet;
      this.userProfile.petBio = this.authUser.petBio;
    });
  },
  unmounted() {
    this.unsubscribeFromAuth();
  }
}
</script>

<template>
  <div class="min-h-screen bg-gray-50 py-8 px-4">
    <div class="max-w-2xl mx-auto">

      <router-link :to="'/perfil'"
        class="inline-flex items-center px-4 py-2 border border-gray-300 rounded text-gray-700 bg-white hover:bg-gray-50 mb-6">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
          <path fill-rule="evenodd"
            d="M9.707 16.707a1 1 0 01-1.414 0l-6-6a1 1 0 010-1.414l6-6a1 1 0 011.414 1.414L5.414 9H17a1 1 0 110 2H5.414l4.293 4.293a1 1 0 010 1.414z"
            clip-rule="evenodd" />
        </svg>
        Volver al perfil
      </router-link>


      <div class="bg-white rounded-lg shadow p-6">

        <div class="mb-8 text-center">
          <MainH1 class="text-2xl text-gray-900">Editar Perfil</MainH1>
          <p class="mt-2 text-gray-600">{{ authUser.email }}</p>
        </div>

        <form @submit.prevent="sendChanges" class="space-y-6">

          <div>
            <MainLabel for="displayName" class="block text-gray-700 mb-1">
              Tu nombre <span class="text-red-500">*</span>
            </MainLabel>
            <input type="text" id="displayName" v-model="userProfile.displayName" :disabled="editingProfile" :class="{
              'border-gray-300': !showValidationError || userProfile.displayName.trim(),
              'border-red-500': showValidationError && !userProfile.displayName.trim(),
              'bg-gray-100': editingProfile
            }" class="w-full px-3 py-2 border rounded" placeholder="Tu nombre completo" />
          </div>

          <div>
            <MainLabel for="pet" class="block text-gray-700 mb-1">
              Nombre de tu mascota <span class="text-red-500">*</span>
            </MainLabel>
            <input type="text" id="pet" v-model="userProfile.pet" :disabled="editingProfile" :class="{
              'border-gray-300': !showValidationError || userProfile.pet.trim(),
              'border-red-500': showValidationError && !userProfile.pet.trim(),
              'bg-gray-100': editingProfile
            }" class="w-full px-3 py-2 border rounded" placeholder="Nombre de tu mascota" />
          </div>

          <div>
            <MainLabel for="petBio" class="block text-gray-700 mb-1">
              Sobre tu mascota
            </MainLabel>
            <textarea id="petBio" rows="4" v-model="userProfile.petBio" :disabled="editingProfile"
              class="w-full px-3 py-2 border border-gray-300 rounded disabled:bg-gray-100"
              placeholder="Cuéntanos sobre tu mascota (edad, raza, personalidad...)"></textarea>
          </div>

          <AlertPop v-if="showValidationError" class="mt-2">
            Por favor completa todos los campos obligatorios (marcados con *)
          </AlertPop>

          <div class="pt-4">
            <SendButton type="submit" :disabled="editingProfile"
              class="w-full py-2 px-4 rounded text-white bg-yellow-600 hover:bg-yellow-700 disabled:opacity-50">
              <span v-if="!editingProfile">Guardar cambios</span>
              <span v-else class="flex items-center">
                <svg class="animate-spin -ml-1 mr-2 h-4 w-4 text-white" xmlns="http://www.w3.org/2000/svg" fill="none"
                  viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor"
                    d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
                  </path>
                </svg>
                Guardando...
              </span>
            </SendButton>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>