<script>
import { subscribeToAuth } from '../services/auth';
import { deletePost } from '../services/post';
import { getPostById } from '../services/post-content';

export default {
    name: 'DeletePost',
    data() {
        return {
            post: null,
            deleting: false,
            postLoaded: false,
        }
    },
    methods: {
        async confirmDelete() {
            if (!this.post || !this.post.id) {
                console.warn("Post aún no cargado, cancelando delete");
                return;
            }

            this.deleting = true;

            try {
                await deletePost(this.post.id);
                this.$router.push('/posts');
            } catch (error) {
                console.error('Error deleting post:', error);
                this.deleting = false;
            }
        }
        ,
        cancel() {
            this.$router.back();
        }
    },
    async mounted() {
        this.post = await getPostById(this.$route.params.id);
        this.postLoaded = true;
        this.unsubscribeFromAuth = subscribeToAuth(newUserData => this.authUser = newUserData);

    },
}
</script>

<template>
    <div v-if="postLoaded" class="min-h-screen bg-gray-50 flex items-center justify-center p-4">
        <div class="bg-white rounded-lg shadow-md p-6 max-w-md w-full">
            <h2 class="text-xl font-semibold text-gray-800 mb-4">Confirmar eliminación</h2>

            <p class="text-gray-600 mb-6">
                ¿Estás seguro de que querés eliminar <span class="font-bold">"{{ post.title }}"</span>?
            </p>

            <div class="flex gap-3 justify-end">
                <button @click="cancel" class="px-4 py-2 border border-gray-300 rounded text-gray-700 hover:bg-gray-50"
                    :disabled="deleting">
                    Cancelar
                </button>

                <button @click="confirmDelete"
                    class="px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700 disabled:bg-red-400"
                    :disabled="deleting">
                    {{ deleting ? 'Eliminando...' : 'Eliminar' }}
                </button>
            </div>
        </div>
    </div>
</template>