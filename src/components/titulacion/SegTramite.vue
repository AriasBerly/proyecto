<script setup>

import { useForm, useField } from "vee-validate";
import { collection, setDoc ,getDocs ,doc} from "firebase/firestore";
import { useFirestore } from "vuefire";
import { useRouter } from "vue-router";
import { validationSchema, imageSchema } from "@/validation/contabilidadSchema.js";
import useImage from "@/composables/useImage";
import { ref } from 'vue';




const fechaFin = ref(new Date().toISOString().substr(0, 10));
const db = useFirestore();
const router = useRouter();
const textoCancelar = "Cancelar";
const textoSeleccionar = "Seleccionar";

const { handleSubmit } = useForm({
  validationSchema: {
    ...validationSchema,
    ...imageSchema,
  },
});

const { url, uploadImage, image } = useImage();

const nroCarnet = useField("nroCarnet");
const detalle = useField("detalle");
const tipoPago = useField("tipoPago");
const tipo = ["Recibo", "Factura"];
const imagen = useField("imagen");

const submit = handleSubmit(async (values) => {

  const { imagen, ...contabilidad_rc } = values
  
  let originalId = "COD-CC-"; 
  let contador = 1; 

  const queryID = await getDocs(collection(db, "contabilidad_rc"));
    queryID.forEach((doc) => {
        const id = doc.id;
        const partes = id.split("-");
const secuencia = partes[partes.length - 1]; // Obtiene el último elemento del array
        if (secuencia && !isNaN(secuencia)) {
            const numSecuencia = parseInt(secuencia);
            if (numSecuencia >= contador) {
                contador = numSecuencia + 1;

            }
        }
    });

  const generateNewId = () => {
        return `${originalId}${contador}`;
    };
    let generatedId = generateNewId();
    try {
  const docRef = await setDoc(doc(collection(db, 'contabilidad_rc'),generatedId), {
    ...contabilidad_rc,
    image: url.value,
    idRegCaja: generatedId,
    fecha: fechaFin.value,
    estado: true, 
  });
  console.log("Documento guardado correctamente.");

// Redirige al usuario a la lista de registros
router.push({ name: "admin-list-contabilidad" });

} catch (error) {
  console.error('Error al guardar el documento en Firestore:', error);
}
});

</script>
    

<template>
  <v-card max-width="1100" flat class="mx-auto my-10" elevation="3">
    <v-card-title class="text-h4 font-weight-bold" tag="h3">
      Tramites
    </v-card-title>

    <v-card-subtitle class="text-h5 py-5">
     Seguimineto de titulación
    </v-card-subtitle>
  <v-row>
    <v-col cols="12">
      <v-card>
        <v-card-title class="d-flex">
        </v-card-title>
        <VDivider />

        <v-card-title>
          <!-- Formulario -->
          <VForm class="mt-6">
            <v-row>
              <!-- Documento de identidad -->
              <v-col
                md="6"
                cols="12"
              >
              <v-text>C.I. N°</v-text>
                <v-text-field
                v-model="nroCarnet.value.value"
                label="Documento de identidad"
                variant="outlined"
                persistent-hint
                ></v-text-field>
              </v-col>

              <!-- Fecha -->
              <v-col
                md="6"
                cols="12"
              >
              <v-text>Fecha de nacimiento</v-text>
              <VueDatePicker
              v-model="fechaFin"
              locale="es"
              :teleport="true"
              :enable-time-picker="false"
              placeholder="Fecha de fin"
              :year-range="[2021, 2060]"
              :cancel-text="textoCancelar"
              :select-text="textoSeleccionar"
            >
            </VueDatePicker>
              </v-col>

              <!-- Acciones del Formulario -->
              <v-col
                cols="12"
                class="d-flex flex-wrap  justify-center gap-4"
              >
                <VBtn  
                color="secondary"
                @click="submit"
                >Buscar</VBtn>
                
                <VBtn
                  color="red"
                  variant="outlined"
                  type="reset"
                  @click.prevent=""
                >
                  Borrar  
                </VBtn>
              </v-col>
            </v-row>
          </VForm>
        </v-card-title>
      </v-card>
    </v-col>
  </v-row>
</v-card>
<v-card max-width="1100" flat class="mx-auto my-10" elevation="3">
    <v-card-title class="text-h4 font-weight-bold" tag="h3">
   
    </v-card-title>

    <v-card-subtitle class="text-h5 py-5">
    Resultados de la búsqueda
    </v-card-subtitle>
  <v-row>
    <v-col cols="12">
      <v-card>
        <v-card-title class="d-flex">
        </v-card-title>
        <VDivider />

        <v-card-title>
          <!-- Formulario -->
          <VForm class="mt-6">
            <v-row>
              <!-- Documento de identidad -->
              <v-col
                md="6"
                cols="12"
              >
              <v-text>C.I. N°
                <v-text-field
                v-model="nroCarnet.value.value"
                variant="outlined"
                persistent-hint
                ></v-text-field>
            </v-text>
              </v-col>
              <!-- Documento de identidad -->
              <v-col
                md="3"
                cols="6"
              >
              <v-text>C.I. N°
                <v-text-field
                v-model="nroCarnet.value.value"
            
                variant="outlined"
                persistent-hint
                ></v-text-field>
            </v-text>
              </v-col>
            <!-- Documento de identidad -->
            <v-col
                md="3"
                cols="12"
              >
              <v-text>C.I. N°
                <v-text-field
                v-model="nroCarnet.value.value"
                variant="outlined"
                persistent-hint
                ></v-text-field>
            </v-text>
              </v-col>
              <!-- Fecha -->
              <v-col
                md="3"
                cols="12"
              >
              <v-text>Fecha de nacimiento</v-text>
              <VueDatePicker
              v-model="fechaFin"
              locale="es"
              :teleport="true"
              :enable-time-picker="false"
              placeholder="Fecha de fin"
              :year-range="[2021, 2060]"
              :cancel-text="textoCancelar"
              :select-text="textoSeleccionar"
            >
            </VueDatePicker>
              </v-col>

              <!-- Acciones del Formulario -->
              <v-col
                cols="12"
                class="d-flex flex-wrap  justify-center gap-4"
              >
                <VBtn  
                color="secondary"
                @click="submit"
                >Buscar</VBtn>
                
                <VBtn
                  color="red"
                  variant="outlined"
                  type="reset"
                  @click.prevent=""
                >
                  Borrar  
                </VBtn>
              </v-col>
            </v-row>
          </VForm>
        </v-card-title>
      </v-card>
    </v-col>
  </v-row>
</v-card>
</template>

<style>
.card {
  border-right: solid;
  border-width: solid;
  color: #cdcdcd;
}
</style>