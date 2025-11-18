<script>
import MainH1 from '../components/MainH1.vue';
import MainButton from '../components/MainButton.vue';
import MainInput from '../components/MainInput.vue';
import AlertPop from '../components/AlertPop.vue';
import { login } from '../services/auth';

export default {
    name: 'Login',
    components: { MainH1, MainButton, AlertPop, MainInput },
    data() {
        return {
            user: {
                email: '',
                password: '',
            },
            loading: false,
            wrongCredentials: false,
        };
    },
    methods: {
        async handleSubmit() {
            this.loading = true;
            try {
                await login(this.user.email, this.user.password);
                this.$router.push({
                    path: '/perfil',
                });
            } catch (error) {
                console.error('[Login handleSubmit] ', error);
                this.wrongCredentials = true;
            }
            this.loading = false;
        }
    }
}
</script>

<template>
    <div class="min-h-screen flex items-center justify-center py-12 px-4">
        <div class="max-w-6xl w-full flex bg-white rounded-lg shadow overflow-hidden">

            <div class="hidden md:block md:w-1/2 bg-yellow-100">
                <img src="../img/catdog-hero.webp" alt="Animal companion" class="w-full h-full object-cover">
            </div>

            <div class="w-full md:w-1/2 p-8">
                <div class="text-center mb-8">
                    <MainH1>Iniciar Sesión</MainH1>
                    <p class="mt-2 text-gray-600">
                        Accedé a tu cuenta para disfrutar de Petbook
                    </p>
                </div>

                <form @submit.prevent="handleSubmit" class="space-y-6">
                    <MainInput id="email" type="email" label="Email" v-model="user.email" :disabled="loading" required
                        placeholder="tu@email.com" />

                    <MainInput id="password" type="password" label="Contraseña" v-model="user.password"
                        :disabled="loading" required placeholder="********" :minlength="6" />

                    <AlertPop v-if="wrongCredentials" class="mt-4">
                        La dirección de Email o la contraseña son incorrectas.
                    </AlertPop>

                    <div>
                        <MainButton type="submit" class="w-full" :disabled="loading">
                            <span v-if="!loading">Ingresar</span>
                            <span v-else>Iniciando sesión...</span>
                        </MainButton>
                    </div>
                </form>

                <div class="mt-6 text-center text-gray-600">
                    <p>
                        ¿No tenés una cuenta?
                        <router-link to="/register" class="text-yellow-600 hover:text-yellow-500">
                            Registrate
                        </router-link>
                    </p>
                </div>
            </div>
        </div>
    </div>
</template>