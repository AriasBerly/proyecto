<script setup>

import { computed } from 'vue';
import useContabilidad from '@/composables/useContabilidad'

const {
  contabilidadFecha,
  dialog,
  dialogDelete,
  editedItem,
  editedIndex,
  editItem,
  deleteItem,
  deleteItemConfirm,
  save,
  dialogFase,
  openFaseDialog,
  modificarFase
} = useContabilidad();

const tipo = ["Recibo", "Factura"];

function initialize() {
  // Tu código para inicializar o recargar los datos
}

const formTitle = computed(() => {
  return editedIndex.value === -1 ? 'Nuevo Registro' : 'Editar Registro';
});

const Phase = {
  REGISTRADO: 1,
  INACTIVO: 2,
  PENDIENTE: 3,
  FINALIZADO: 4,
  CANCELADO: 5
};

function getChipProperties(fase) {
  const fases = {
    [Phase.REGISTRADO]: { text: 'Registrado', color: 'green' },
    [Phase.INACTIVO]: { text: 'Inactivo', color: 'red' },
    [Phase.PENDIENTE]: { text: 'Pendiente', color: 'yellow' },
    [Phase.FINALIZADO]: { text: 'Finalizado', color: 'blue' },
    [Phase.CANCELADO]: { text: 'Cancelado', color: 'grey' }
  };
  return fases[fase] || { text: 'Desconocido', color: 'black' };
}




// Métodos para abrir los diálogos
const openEditDialog = (item) => {
  editItem(item);
  dialog.value = true;
};

const openDeleteDialog = (item) => {
  deleteItem(item);
  dialogDelete.value = true;
};

const closeDialog = () => {
  dialog.value = false;
  editedItem.value = {};
  editedIndex.value = -1;
};

const closeDeleteDialog = () => {
  dialogDelete.value = false;
  editedItem.value = {};
  editedIndex.value = -1;
};
const saveItem = () => {
  save();
  dialog.value = false;
};
const deleteItemConfirmDialog = () => {
  deleteItemConfirm();
  dialogDelete.value = false;
};

</script>

<script>

export default {
  data() {
    return {

      //Componentes de la tabla 
      dialog: false,
      dialogDelete: false,

      //estado de la columnas extra
      showExtraColumns: false,

      headers: [
        {
          title: 'Codigo registro',
          align: 'start',
          sortable: false,
          value: 'idRegCaja',
          hidden: true
        },
        { title: 'Nombres', value: 'nombre' },
        { title: 'Apellidos', value: 'apellido' },
        { title: 'Documento de identidad', value: 'nroCarnet' },
        { title: 'Correo', value: 'email',hidden: true },
        { title: 'Celular', value: 'celular' },
        { title: 'Edad', value: 'edad',hidden: true },
        { title: 'Profesión', value: 'profesion' },
        { title: 'Ciudad', value: 'ciudadR' },
        { title: 'Archivo', value: 'file' },
        { title: 'Fecha reg.', value: 'fecha',hidden: true },
        { title: 'Fase', text: 'Fase', value: 'fase' },
        { title: 'Estado', text: 'Estado', value: 'estado'},
        { title: 'Acciones', key: 'actions', sortable: false },
      ],
      //------------------------------------------------
    };
  },
  computed: {

    //Maneja las columnas visibles
  visibleHeaders() {
    if (this.showExtraColumns) {
      return this.headers;
    } else {
      return this.headers.filter(header => !header.hidden);
    }
  },
  // Muestra las columnas adicionales
  toggleExtraColumns() {
    // Ajusta la propiedad hidden basada en showExtraColumns
    this.headers.forEach(header => {
      if (header.hidden !== undefined) {
        header.hidden = !this.showExtraColumns;
      }
    });
  }
},
methods: {
  downloadFile(url) {
    window.open(url, '_blank');
  }
}
}
</script>

<template>
  <v-btn color="blue" variant="flat" :to="{ name: 'nuevo-reg-contabilidad' }">Nuevo Registro</v-btn>

  <h2 class="text-center text-h3 my-5 font-weight-bold">Lista de registros</h2>
  <v-checkbox
    label="Mostrar detalles adicionales"
    v-model="showExtraColumns"
    @change="toggleExtraColumns"
  ></v-checkbox>

  <v-data-table v-if="contabilidadFecha.length" :headers="visibleHeaders" :items="contabilidadFecha"
    :sort-by="[{ key: 'idRegCaja', order: 'asc' }]">
    
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>
          Lista de registros
        </v-toolbar-title>
        <v-spacer></v-spacer>
        <v-btn color="primary" dark class="mb-2" @click="initialize">
          Actualizar
        </v-btn>
      </v-toolbar>
    </template>

    <template v-slot:item.file="{ item }">
  <v-tooltip bottom>
    <template v-slot:activator="{ on, attrs }">
      <v-icon
        small
        color="blue darken-2"
        v-bind="attrs"
        v-on="on"
        @click="downloadFile(item.file)"  
        
      ><!-- Asegúrate que `item.archivo` es la URL del archivo -->
        mdi-file-download
      </v-icon>
    </template>
    <span>Descargar archivo</span>
  </v-tooltip>
</template>

    <template v-slot:item.fase="{ item }">
    <div class="mr-2">
      <v-chip 
      :color="getChipProperties(item.fase).color"
      :text="getChipProperties(item.fase).text"
      class="text-uppercase"
      size="small"
      label
      >
        {{ getChipProperties(item.fase).text }}
      </v-chip>
    </div>
  </template>
    <template v-slot:item.estado="{ item }">
    <div class="mr-2">
      <v-chip
        :color="item.estado ? 'green' : 'red'"
        :text="item.estado ? 'Activo' : 'Inactivo'"
        class="text-uppercase"
        size="small"
        label
      ></v-chip>
    </div>
  </template>
   
    <template v-slot:item.actions="{ item }">
      <v-icon 
      color="green-accent-3"
      size="small" 
      class="mr-2" 
      @click="openFaseDialog(item)">mdi-cached</v-icon>

      <v-icon 
      color="blue-darken-2"
      size="small" 
      class="mr-2" 
      @click="showDetails(item)"> mdi-eye</v-icon>

      <v-icon 
      color="blue-grey-darken-2"
      size="small" 
      class="mr-2" 
      @click="openEditDialog(item)"> mdi-pencil</v-icon>

      <v-icon
      color="red-darken-2"
      size="small" 
      class="mr-2"
       @click="openDeleteDialog(item)"> mdi-delete</v-icon>

    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">
        Reiniciar
      </v-btn>
    </template>

  </v-data-table>
  <!-- Diálogo para editar registros -->
  <v-dialog v-model="dialog" max-width="600px">
    <v-card>
      <v-card-title>
        <span class="headline">{{ formTitle }}</span>
      </v-card-title>
      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="12" sm="6">
              <v-text-field v-model="editedItem.monto" label="Monto (Bs.)"></v-text-field>
            </v-col>
            <v-col cols="12" sm="6">
              <v-select v-model="editedItem.tipoPago"  :items="tipo"  label="Tipo"></v-select>
            </v-col>
            <v-col cols="12" sm="6">
              <v-text-field v-model="editedItem.fecha" label="Fecha" type="date"></v-text-field>
            </v-col>
            <v-col cols="12" sm="6">
              <v-text-field v-model="editedItem.detalle" label="Detalle" multiline></v-text-field>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" text @click="closeDialog">Cancelar</v-btn>
        <v-btn color="blue darken-1" text @click="saveItem">Guardar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <!-- Diálogo para cambiar de fase de los registros -->


  <v-dialog v-model="dialogFase" max-width="300px">
    <v-card>
      <v-card-title class="text-h5">Cambiar Fase</v-card-title>
      <v-card-text>
        ¿Deseas subir o bajar la fase del registro?
      </v-card-text>
      <v-card-actions>
        <v-btn color="green" text @click="modificarFase(1)">Subir</v-btn>
        <v-btn color="red" text @click="modificarFase(-1)">Bajar</v-btn>
        <v-spacer></v-spacer>
        <v-btn text @click="dialogFase = false">Cancelar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>


  <!-- Diálogo para confirmar la eliminación de registros -->
  <v-dialog v-model="dialogDelete" max-width="500px">
    <v-card>
      <v-card-title class="text-h5">¿Estás seguro?</v-card-title>
      <v-card-text>
        Esta acción no se puede deshacer.
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="green darken-1" text @click="closeDeleteDialog">Cancelar</v-btn>
        <v-btn color="red darken-1" text @click="deleteItemConfirmDialog">Eliminar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>
