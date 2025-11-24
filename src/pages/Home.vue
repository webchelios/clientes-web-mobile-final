<script>
import MainH1 from '../components/MainH1.vue';
import MainButton from '../components/MainButton.vue';
import MainH2 from '../components/MainH2.vue';
import PetPortrait from '../components/PetPortrait.vue';

export default {
  components: { MainH1, MainButton, MainH2, PetPortrait },
}
</script>

<template>
  <div class="min-h-screen">
    <div class="container mx-auto px-6 md:px-12 lg:px-24 pt-20 pb-32">
      <div class="text-center mb-16">
        <h1 class="text-6xl md:text-8xl lg:text-9xl font-bold text-yellow-600 mb-4">
          La red social <br /><span class="text-5xl md:text-7xl lg:text-8xl font-bold text-gray-800 italic">de las
            mascotas</span>
        </h1>

        <p class="text-xl md:text-2xl text-gray-600 mt-10 mb-8 max-w-2xl mx-auto">
          Conectá con otros amantes de mascotas y compartí los mejores momentos.
        </p>

        <RouterLink to="/posts">
          <MainButton>
            Comenzar la aventura
          </MainButton>
        </RouterLink>
      </div>


      <div class="flex flex-col xl:flex-row items-center xl:items-end justify-center gap-6 xl:gap-12 lg:gap-24 px-4">

        <PetPortrait image="catdog-hero" alt="Gato y perro juntos" text="Amigos inseparables" />

        <div class="relative my-8">
          <div class="bg-yellow-300 rounded-full w-80 h-80 overflow-hidden shadow-lg transform">
            <img src="../img/dog-hero.webp" alt="Perro hero" class="w-full h-full object-cover">
          </div>
          <div
            class="absolute -bottom-6 left-1/2 transform -translate-x-1/2 bg-white px-4 py-2 rounded-full shadow-md whitespace-nowrap">
            <span class="text-yellow-600 font-medium">Amigos grandes</span>
          </div>
        </div>

        <PetPortrait image="hamster-hero" alt="Hámster" text="Amigos pequeños" />
      </div>
    </div>

    <div class="bg-yellow-200 py-12">
      <div class="container mx-auto text-center px-6">
        <h2 class="text-3xl md:text-4xl font-bold text-yellow-600 mb-6">¿Qué esperás para unirte?</h2>
        <RouterLink to="/registro">
          <MainButton>
            Registrate gratis
          </MainButton>
        </RouterLink>
      </div>
    </div>
  </div>
</template>