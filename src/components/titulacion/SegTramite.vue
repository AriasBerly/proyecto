<script setup>
import { useForm, useField } from "vee-validate";
import { collection, setDoc, getDocs, doc } from "firebase/firestore";
import { useFirestore } from "vuefire";
import { useRouter } from "vue-router";
import {
  validationSchema,
  imageSchema,
} from "@/validation/contabilidadSchema.js";
import useImage from "@/composables/useImage";
import { ref } from "vue";

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
  const { imagen, ...contabilidad_rc } = values;

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
    const docRef = await setDoc(
      doc(collection(db, "contabilidad_rc"), generatedId),
      {
        ...contabilidad_rc,
        image: url.value,
        idRegCaja: generatedId,
        fecha: fechaFin.value,
        estado: true,
      }
    );
    console.log("Documento guardado correctamente.");

    // Redirige al usuario a la lista de registros
    router.push({ name: "admin-list-contabilidad" });
  } catch (error) {
    console.error("Error al guardar el documento en Firestore:", error);
  }
});
</script>
    <script>
  export default {
    data () {
      return {
        tickLabels: {
          0: 'Figs',
          1: 'Lemon',
          2: 'Pear',
          3: 'Apple',
          4: 'Figs',
          5: 'Lemon',
          6: 'Pear',
          7: 'Apple',
        },
      
      }
      
    },
  }
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
          <v-card-title class="d-flex"> </v-card-title>
          <VDivider />

          <v-card-title>
            <!-- Formulario -->
            <VForm class="mt-6">
              <v-row>
                <!-- Documento de identidad -->
                <v-col cols="12" md="6">
                  <v-text>C.I. N°</v-text>
                  <v-text-field
                    class="text-field-search"
                    v-model="nroCarnet.value.value"
                    label="Documento de identidad"
                    variant="outlined"
                    persistent-hint
                    margin="dense"
                  ></v-text-field>
                </v-col>

                <!-- Fecha nacimiento -->
                <v-col cols="12" md="6">
                  <v-text>Fecha de nacimiento</v-text>
                  <VueDatePicker
                    class="flex-grow-1"
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
                <v-col cols="12" class="d-flex flex-wrap justify-center gap-4">
                  <VBtn color="secondary" @click="submit">Buscar </VBtn>
                </v-col>
              </v-row>
            </VForm>
          </v-card-title>
        </v-card>
      </v-col>
    </v-row>
  </v-card>
  <v-card max-width="1100" flat class="mx-auto my-10" elevation="3">
    <v-card-title class="text-h4 font-weight-bold" tag="h3"> </v-card-title>

    <v-card-subtitle class="text-h5 py-5">
      Resultados de la búsqueda
    </v-card-subtitle>
    <v-row>
      <v-col cols="12">
        <v-card>
          <v-card-title class="d-flex"> </v-card-title>
          <VDivider />

          <v-card-title>
            <!-- Formulario -->
            <VForm class="mt-6">
              <v-row>
                <!-- Documento de identidad -->
                <v-col md="4" cols="12" class="col-etiqueta">
                  <v-row>
                    <v-text class="text-etiqueta">Codigo de empaste</v-text>

                    <v-text-field
                      class="field-etiqueta"
                      v-model="nroCarnet.value.value"
                      variant="outlined"
                      persistent-hint
                      disabled
                    ></v-text-field>
                  </v-row>
                </v-col>
                <!-- Documento de identidad -->
                <v-col class="col-etiqueta" md="5" cols="12">
                  <v-row>
                    <v-text class="text-etiqueta">Nombre completo</v-text>

                    <v-text-field
                      class="field-etiqueta"
                      v-model="nroCarnet.value.value"
                      variant="outlined"
                      persistent-hint
                      disabled
                    ></v-text-field>
                  </v-row>
                </v-col>
                <!-- Documento de identidad -->
                <v-col class="col-etiqueta" md="3" cols="12">
                  <v-row>
                    <v-text class="text-etiqueta">C.I. N°</v-text>

                    <v-text-field
                      class="field-etiqueta"
                      v-model="nroCarnet.value.value"
                      variant="outlined"
                      persistent-hint
                      disabled
                    ></v-text-field>
                  </v-row>
                </v-col>
              </v-row>
              <v-row>
                <!-- Programa -->
                <v-col md="4" cols="12" class="col-etiqueta">
                  <v-row>
                    <v-text class="text-etiqueta">Programa</v-text>
                    <v-text-field
                      class="field-etiqueta"
                      v-model="nroCarnet.value.value"
                      variant="outlined"
                      persistent-hint
                      disabled
                    ></v-text-field>
                  </v-row>
                </v-col>
                <!-- Sede -->
                <v-col class="col-etiqueta" md="4" cols="12">
                  <v-row>
                    <v-text class="text-etiqueta">Sede</v-text>
                    <v-text-field
                      class="field-etiqueta"
                      v-model="nroCarnet.value.value"
                      variant="outlined"
                      persistent-hint
                      disabled
                    ></v-text-field>
                  </v-row>
                </v-col>
                <!-- Inicio de trámite -->
                <v-col class="col-etiqueta" md="4" cols="12">
                  <v-row>
                    <v-text class="text-etiqueta">Inicio de trámite</v-text>

                    <v-text-field
                      class="field-etiqueta"
                      v-model="nroCarnet.value.value"
                      variant="outlined"
                      persistent-hint
                      disabled
                    ></v-text-field>
                  </v-row>
                </v-col>
              </v-row>
              <v-row>
                <!-- Estado -->
                <v-col md="4" cols="12" class="col-etiqueta">
                  <v-row>
                    <v-text class="text-etiqueta">Estado</v-text>

                    <v-text-field
                      class="field-etiqueta"
                      v-model="nroCarnet.value.value"
                      variant="outlined"
                      persistent-hint
                      disabled
                    ></v-text-field>
                  </v-row>
                </v-col>
                <!-- Etapa del trámite -->
                <v-col class="col-etiqueta" md="4" cols="12">
                  <v-row>
                    <v-text class="text-etiqueta">Etapa del trámite</v-text>

                    <v-text-field
                      class="text-field-results"
                      v-model="nroCarnet.value.value"
                      variant="outlined"
                      persistent-hint
                      disabled
                    ></v-text-field>
                  </v-row>
                </v-col>
              </v-row>
              <v-row>
                <div class="text-caption">Tick labels</div>
                <v-slider
                  :max="7"
                  :ticks="tickLabels"
                  show-ticks="always"
                  step="1"
                  tick-size="6"
                  color="orange"
                >
              </v-slider>
            </v-row>
            </VForm>
          </v-card-title>
        </v-card>
      </v-col>
    </v-row>
  </v-card>
</template>

<style scoped>
.text-field-search {
  height: 56px;
  width: 100%;
}
.card {
  border-right: solid;
  border-width: solid;
  color: #cdcdcd;
}
.text-etiqueta {
  padding: 2%;
}
.field-etiqueta {
  padding: 1;
}
.col-etiqueta {
  padding-right: 3%;
}
.text-field-results {
}
</style>