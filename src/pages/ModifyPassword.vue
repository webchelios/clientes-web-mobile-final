<script>
import { getAuth, reauthenticateWithCredential, EmailAuthProvider, updatePassword } from 'firebase/auth';

export default {
  name: 'ChangePassword',
  data() {
    return {
      currentPassword: '',
      newPassword: '',
      confirmPassword: '',
      loading: false,
      error: '',
      success: ''
    };
  },
  methods: {
    async handleChangePassword() {
      // Validación básica
      if (!this.currentPassword) {
        this.error = 'Por favor ingresa tu contraseña actual';
        return;
      }

      if (this.newPassword !== this.confirmPassword) {
        this.error = 'Las contraseñas no coinciden';
        return;
      }

      if (this.newPassword.length < 6) {
        this.error = 'La contraseña debe tener al menos 6 caracteres';
        return;
      }

      this.loading = true;
      this.error = '';
      this.success = '';

      try {
        const auth = getAuth();
        const user = auth.currentUser;

        if (!user || !user.email) {
          throw new Error('No hay usuario autenticado');
        }

        // Reautenticación
        const credential = EmailAuthProvider.credential(
          user.email,
          this.currentPassword
        );

        await reauthenticateWithCredential(user, credential);

        // Cambio de contraseña
        await updatePassword(user, this.newPassword);

        this.success = '¡Contraseña cambiada exitosamente!';
        this.resetForm();

      } catch (error) {
        console.error('Error al cambiar contraseña:', error);
        this.error = this.getFirebaseErrorMessage(error);
      } finally {
        this.loading = false;
      }
    },

    getFirebaseErrorMessage(error) {
      const errorCode = error.code || '';

      switch (errorCode) {
        case 'auth/wrong-password':
          return 'La contraseña actual es incorrecta';
        case 'auth/requires-recent-login':
          return 'Debes volver a iniciar sesión para realizar esta acción';
        case 'auth/weak-password':
          return 'La contraseña debe tener al menos 6 caracteres';
        case 'auth/user-not-found':
          return 'Usuario no encontrado';
        case 'auth/too-many-requests':
          return 'Demasiados intentos fallidos. Intenta más tarde';
        default:
          return error.message || 'Ocurrió un error al cambiar la contraseña';
      }
    },

    resetForm() {
      this.currentPassword = '';
      this.newPassword = '';
      this.confirmPassword = '';
    }
  }
};
</script>

<template>
  <div class="min-h-screen bg-gray-50">
    <router-link :to="'/perfil'"
      class="inline-flex items-center px-4 py-2 border border-gray-300 rounded text-gray-700 bg-white hover:bg-gray-50 mb-6">
      <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
        <path fill-rule="evenodd"
          d="M9.707 16.707a1 1 0 01-1.414 0l-6-6a1 1 0 010-1.414l6-6a1 1 0 011.414 1.414L5.414 9H17a1 1 0 110 2H5.414l4.293 4.293a1 1 0 010 1.414z"
          clip-rule="evenodd" />
      </svg>
      Volver al perfil
    </router-link>

    <div class="flex flex-col justify-center py-8 px-4">
      <div class="mx-auto w-full max-w-md">
        <div class="text-center">
          <h2 class="text-3xl text-gray-900">Cambiar Contraseña</h2>
          <p class="mt-2 text-gray-600">Ingresá tu contraseña actual y la nueva contraseña</p>
        </div>

        <div class="mt-8 bg-white py-8 px-4 shadow rounded-lg">
          <form class="space-y-6" @submit.prevent="handleChangePassword">
            <div>
              <label for="currentPassword" class="block text-gray-700">
                Contraseña Actual
              </label>
              <div class="mt-1">
                <input id="currentPassword" v-model="currentPassword" type="password" required
                  class="block w-full px-3 py-2 border border-gray-300 rounded" :class="{ 'border-red-500': error }">
              </div>
            </div>

            <div>
              <label for="newPassword" class="block text-gray-700">
                Nueva Contraseña
              </label>
              <div class="mt-1">
                <input id="newPassword" v-model="newPassword" type="password" required
                  class="block w-full px-3 py-2 border border-gray-300 rounded" :class="{ 'border-red-500': error }">
              </div>
              <p class="mt-1 text-xs text-gray-500">Mínimo 6 caracteres</p>
            </div>

            <div>
              <label for="confirmPassword" class="block text-gray-700">
                Confirmar Nueva Contraseña
              </label>
              <div class="mt-1">
                <input id="confirmPassword" v-model="confirmPassword" type="password" required
                  class="block w-full px-3 py-2 border border-gray-300 rounded" :class="{ 'border-red-500': error }">
              </div>
            </div>

            <div v-if="error" class="rounded bg-red-50 p-4">
              <div class="flex">
                <div>
                  <svg class="h-5 w-5 text-red-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"
                    fill="currentColor">
                    <path fill-rule="evenodd"
                      d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z"
                      clip-rule="evenodd" />
                  </svg>
                </div>
                <div class="ml-3">
                  <h3 class="text-sm text-red-800">{{ error }}</h3>
                </div>
              </div>
            </div>

            <div v-if="success" class="rounded bg-green-50 p-4">
              <div class="flex">
                <div>
                  <svg class="h-5 w-5 text-green-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"
                    fill="currentColor">
                    <path fill-rule="evenodd"
                      d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z"
                      clip-rule="evenodd" />
                  </svg>
                </div>
                <div class="ml-3">
                  <h3 class="text-sm text-green-800">{{ success }}</h3>
                </div>
              </div>
            </div>

            <div>
              <button type="submit" :disabled="loading"
                class="w-full py-2 px-4 rounded text-white bg-yellow-600 hover:bg-yellow-700 disabled:opacity-50">
                <span v-if="!loading">Cambiar Contraseña</span>
                <span v-else class="flex items-center">
                  <svg class="animate-spin -ml-1 mr-2 h-4 w-4 text-white" xmlns="http://www.w3.org/2000/svg" fill="none"
                    viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor"
                      d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
                    </path>
                  </svg>
                  Procesando...
                </span>
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>
