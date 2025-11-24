<script>
import AlertPop from '../components/AlertPop.vue';
import MainH1 from '../components/MainH1.vue';
import MainButton from '../components/MainButton.vue';
import MainLabel from '../components/MainLabel.vue';
import Loader from '../components/Loader.vue';
import { updateUserPhoto } from '../services/auth.js';

export default {
  name: "ModifyProfilePhoto",
  components: { AlertPop, MainH1, MainButton, Loader, MainLabel },
  data() {
    return {
      photo: null,
      photoPreview: null,
      uploadingPhoto: false,
      showValidationError: false,
    }
  },
  methods: {
    handleFileSelection(event) {
      const file = event.target.files[0];
      if (!file) return;

      this.photo = file;
      this.showValidationError = false;

      const reader = new FileReader();
      reader.addEventListener('load', () => {
        this.photoPreview = reader.result;
      });
      reader.readAsDataURL(this.photo);
    },
    async handleFileUpload() {

      if (!this.photo) {
        this.showValidationError = true;
        return;
      }

      this.uploadingPhoto = true;
      this.showValidationError = false;

      try {
        await updateUserPhoto(this.photo);
        this.$router.push('/perfil');
      } catch (error) {
        console.error('[modifyProfilePhoto] Error al editar foto:', error);
        this.showValidationError = true;
      } finally {
        this.uploadingPhoto = false;
      }
    }
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
          <MainH1 class="text-2xl text-gray-900">Actualizar foto de perfil</MainH1>
          <p class="mt-2 text-gray-600">Seleccioná una nueva imagen para tu perfil</p>
        </div>

        <form @submit.prevent="handleFileUpload" class="space-y-6">
          <div>
            <MainLabel for="photo" class="block text-gray-700 mb-2">
              Seleccionar imagen <span class="text-red-500">*</span>
            </MainLabel>

            <div class="flex justify-center px-6 py-5 border-2 border-gray-300 border-dashed rounded">
              <div class="text-center">
                <div class="flex text-sm text-gray-600">
                  <label for="photo" class="cursor-pointer text-yellow-600 hover:text-yellow-500">
                    <span>Hacé click para subir un archivo</span>
                    <input id="photo" type="file" accept="image/*" @change="handleFileSelection"
                      :disabled="uploadingPhoto" class="sr-only">
                  </label>
                </div>
              </div>
            </div>
          </div>

          <div v-if="photoPreview" class="space-y-4">
            <h2 class="text-lg text-gray-900">Vista previa</h2>
            <div class="grid grid-cols-3 gap-4">
              <div class="flex flex-col items-center">
                <p class="text-sm text-gray-500 mb-2">Grande</p>
                <img :src="photoPreview" alt="Vista previa"
                  class="h-32 w-32 rounded-full border-4 border-white shadow object-cover">
              </div>
              <div class="flex flex-col items-center">
                <p class="text-sm text-gray-500 mb-2">Mediano</p>
                <img :src="photoPreview" alt="Vista previa"
                  class="h-20 w-20 rounded-full border-4 border-white shadow object-cover">
              </div>
              <div class="flex flex-col items-center">
                <p class="text-sm text-gray-500 mb-2">Pequeño</p>
                <img :src="photoPreview" alt="Vista previa"
                  class="h-12 w-12 rounded-full border-4 border-white shadow object-cover">
              </div>
            </div>
          </div>

          <AlertPop v-if="showValidationError" class="mt-2">
            Por favor seleccioná una imagen para continuar
          </AlertPop>

          <div class="pt-4">
            <button type="submit" :disabled="uploadingPhoto || !photoPreview"
              class="w-full py-2 px-4 rounded text-white bg-yellow-600 hover:bg-yellow-700 disabled:opacity-50">
              <span v-if="!uploadingPhoto">Actualizar foto</span>
              <span v-else class="flex items-center">
                <svg class="animate-spin -ml-1 mr-2 h-4 w-4 text-white" xmlns="http://www.w3.org/2000/svg" fill="none"
                  viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor"
                    d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
                  </path>
                </svg>
                Subiendo...
              </span>
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>