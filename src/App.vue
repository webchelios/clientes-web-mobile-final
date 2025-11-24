<script>
import NavBar from './components/NavBar.vue';
import Home from './pages/Home.vue';
import { logout, subscribeToAuth } from './services/auth';
import { auth } from './services/firebase';

export default {
    name: 'App',
    components: { Home, NavBar },
    data() {
        return {
            authUser: {
                id: null,
                email: null,
                displayName: null,
                photoURL: null
            },
            isMobileMenuOpen: false
        };
    },
    methods: {
        handleLogout() {
            logout();
            this.$router.push({
                path: '/iniciar-sesion',
            });
            this.isMobileMenuOpen = false;
        },
        closeMobileMenu() {
            this.isMobileMenuOpen = false;
        },
        toggleMobileMenu() {
            this.isMobileMenuOpen = !this.isMobileMenuOpen;
        }
    },
    mounted() {
        subscribeToAuth(newUserData => this.authUser = newUserData);
    },
}
</script>

<template>
    <div class="min-h-screen flex flex-col">
        <header>
            <NavBar :authUser="authUser" :isMobileMenuOpen="isMobileMenuOpen" @toggleMobileMenu="toggleMobileMenu"
                @closeMobileMenu="closeMobileMenu" />
        </header>


        <main class="flex-grow">
            <section>
                <router-view class="max-w-7xl mx-auto px-4 py-6" />
            </section>
        </main>

        <footer class="bg-white border-t border-gray-200">
            <div class="max-w-7xl mx-auto py-6 px-4">
                <p class="text-center text-gray-500">
                    &copy; {{ new Date().getFullYear() }} Marcelo Anavia DWN4AV | Clientes Web Mobile
                </p>
            </div>
        </footer>
    </div>
</template>