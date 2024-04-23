  <script setup>
  import Footer from "@/views/Footer.vue";
  import { RouterView } from "vue-router";
  import { useAuthStore } from "./stores/auth";
  import UserProfile from '@/layouts/UserProfile.vue'


  const auth = useAuthStore();
  </script>
  <template>
    <v-app>
        <v-app-bar app color="#162D4B" class="app-bar" >
          <div class="nav-container">
            <router-link :to="{ name: 'login' }"  class="logo-link">
              <img
                src="@/assets/ESCUELA DE NEGOCIOS_HORIZONTAL BLANCO.png"
                alt="Logo"
                height="80"
              />
            </router-link>
        
            <v-spacer></v-spacer>

            <div class="right-section">          
              <v-btn
              v-if="auth.isAuth"
              :to="{ name: 'admin-list-contabilidad' }" 
              class="mr-4"> 
              Admin 
            </v-btn>
                  
              <UserProfile v-if="auth.isAuth" />
        
      
              <v-btn 
              v-else
              :to="{ name: 'login' }"> Iniciar Sesión </v-btn>
            
          </div>
        </div>
        </v-app-bar>
        <v-main style="margin-bottom: 50px">
          <v-container  fluid="true">
            <router-view />
          </v-container>
        </v-main>
      <Footer />
    </v-app>
  </template>
  <script>
  export default {
    data: () => ({
      items: [
        { title: "Cursos", route: "cursos" },
        { title: "Webinar", route: "webinar" },
      ],
    }),
    methods: {
      redirectToView(route) {
        this.$router.push({ name: route });
      },
    },
  };
  </script>
  <style scoped>
  .nav-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
  }

  .logo-link {
    margin-right: 1rem; /* Ajusta esto para el espacio después del logo */
  }

  .right-section {
    display: flex;
    align-items: center;
    gap: 1rem; /* Ajusta esto para el espacio entre los elementos */
  }
  .logo-link {
    margin-right: 1rem; /* Ajusta esto para el espacio después del logo */
  }
  </style>