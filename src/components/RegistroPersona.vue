<!-- #################################################################################### -->
<!-- ###### Sección de HTML                                                        ###### -->
<!-- #################################################################################### -->
<template>
  <v-main style="padding: 0 1rem !important">
    <div class="titulo">
      <h1 class="titulo-texto">Registro de Personas</h1>
    </div>
    <!--Contenedor para mostrar la opción de agregar un nuevo registro -->
    <div class="d-flex pt-3 agregar" style="justify-content: center;">
     
      <div>
        <h3 class="ms-2">Añadir registro</h3>
      </div>

      <v-tooltip right color="success">
        <template v-slot:activator="{ on, attrs }">
          <v-btn small class="ms-2" fab color="success" v-bind="attrs"
            v-on="on" @mousedown.prevent="dialog = true">
            <v-icon> add </v-icon>
          </v-btn>
        </template>
        <span>Agregar</span>
      </v-tooltip>
    </div>

    <!-- dialogo para crear y actualizar un registro -->
    <v-dialog v-model="dialog" :editar="false" persistent transition="dialog-bottom-transition"
      max-width="25rem">
      <v-card>
        <v-card-title class="fondoDialog">
          <span class="text-h6">{{ tituloRegistro }}</span>
        </v-card-title>

        <!-- formulario para editar y crear un registro -->
        <v-card-text style="padding-top: 0.7rem">
          <v-form ref="nuevoRegistro" class="mt-3" lazy-validation>

            <!-- Campo para el nombre de la persona a registrar -->
            <div class="formulario-flex">
              <div class="d-flex align-items-center">
                <v-text-field v-model="nuevoRegistro.nombre" style="width: 21.3rem"
                  label="Nombre" dense outlined>
                </v-text-field>
                <div class="d-flex align-items-center"
                  style="width: 2.6rem; padding-bottom: 1.4rem; padding-left: 0.13rem;">
                  <v-tooltip right color="blue darken-3">
                    <template v-slot:activator="{ on, attrs }">
                      <v-icon dark color="blue darken-3" fab v-bind="attrs" v-on="on">
                        help
                      </v-icon>
                    </template>
                    <span>Nombre de la persona a registrar</span>
                  </v-tooltip>
                </div>
              </div>
            </div>

            <!-- Campo para el número teléfonico de la persona a registrar -->
            <div class="formulario-flex">
              <div class="d-flex align-items-center">
                <v-text-field v-model="nuevoRegistro.numeroTelefonico" style="width: 21.3rem"
                  label="Número telefónico" dense outlined @keypress="validarLetra($event)">
                </v-text-field>
                <div class="d-flex align-items-center"
                  style="width: 2.6rem; padding-bottom: 1.4rem; padding-left: 0.13rem;">
                  <v-tooltip right color="blue darken-3">
                    <template v-slot:activator="{ on, attrs }">
                      <v-icon dark color="blue darken-3" fab v-bind="attrs" v-on="on">
                        help
                      </v-icon>
                    </template>
                    <span>Número de teléfono de la persona a registrar</span>
                  </v-tooltip>
                </div>
              </div>
            </div>

            <!-- Campo para el correo electrónico de la persona a registrar -->
            <div class="formulario-flex">
              <div class="d-flex align-items-center">
                <v-text-field v-model="nuevoRegistro.correoElectronico" style="width: 21.3rem"
                  label="Correo electrónico" dense outlined placeholder="ejemplo@dominio.com">
                </v-text-field>
                <div class="d-flex align-items-center"
                  style="width: 2.6rem; padding-bottom: 1.4rem; padding-left: 0.13rem;">
                  <v-tooltip right color="blue darken-3">
                    <template v-slot:activator="{ on, attrs }">
                      <v-icon dark color="blue darken-3" fab v-bind="attrs" v-on="on">
                        help
                      </v-icon>
                    </template>
                    <span>Correo electrónico de la persona a registrar</span>
                  </v-tooltip>
                </div>
              </div>
            </div>

            <!-- Botones de cerrar, guardar y actualizar que se activan cuando se abre el dialogo para registro-->
            <div class="d-flex justify-end">
              <v-btn color="error" text @click="reset()"> CERRAR </v-btn>
              <v-btn v-if="!editar" depressed @click="crearRegistro()"
                 color="success" text>
                {{ textoBoton }}
              </v-btn>
              <v-btn v-else depressed @click="actualizarRegistro()"
                color="success" text>
                {{ textoBoton }}
              </v-btn>
            </div>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- Tabla que muestra los registros de personas -->
    <v-data-table :loading="loading" fixed-header :headers="headersRegistro" :items="listaDeRegistros"
      class="elevation mt-4" height="66vh" hide-default-footer>
      <template v-slot:body="{ items }">
        <tbody>
          <tr v-for="item in items" v-bind:key="item.id">
            <td class="text-center">{{ item.id }}</td>
            <td>{{ item.nombre }}</td>
            <td>{{ item.numeroTelefonico }}</td>
            <td>{{ item.correoElectronico }}</td>
            <td class="text-center">
              <v-icon color="orange" @click="editarRegistro(item)">border_color</v-icon>
            </td>
            <td class="text-center">
              <v-btn icon @click="abrirDialogoEliminar(item)">
                <v-icon medium color="error" >delete</v-icon>
              </v-btn>
            </td>
          </tr>
        </tbody>
      </template>
    </v-data-table>

    <!-- Campo para eliminar el registro -->
    <v-dialog v-model="dialogoEliminar" persistent transition="dialog-bottom-transition" max-width="22.7rem">
      <v-card>
        <v-card-title class="fondoDialog">
          <span class="text-h6"> ¿Desea eliminar el registro?
          </span>
        </v-card-title>
        <v-card-text>
          <div class="d-flex justify-end" style="padding-top: 1.3rem">
            <v-btn text color="error" @click="dialogoEliminar = false">No</v-btn>
            <v-btn depressed color="success" @click="eliminarRegistro(registroSeleccionado.id)">Si</v-btn>
          </div>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-main>
</template>

<!-- #################################################################################### -->
<!-- ###### Sección de scripts                                                     ###### -->
<!-- #################################################################################### -->

<script>
import axios from 'axios';

export default {
  name: "RegistroPersona",

  data() {
    return {
      showTooltip: false,
      tituloRegistro: "Crear registro",
      textoBoton: "GUARDAR",
      dialog: false,
      editar: false,
      registroSelecionado: {},
      disabled: 0,
      dialogoEliminar: false,
      nuevoRegistro: {
        id: null,
        nombre: "",
        numeroTelefonico: "",
        correoElectronico: "",
      },
      listaDeRegistros: [],
      loading: false,
      headersRegistro: [
        { text: "NÚMERO", align: "center", sortable: false, width: "5rem" },
        { text: "NOMBRE", sortable: false, width: "11rem" },
        { text: "NÚMERO TELEFÓNICO", align: "center", sortable: false, width: "11rem"},
        { text: "CORREO ELECTRÓNICO", align: "center", sortable: false, width: "11rem"},
        { text: "ACTUALIZAR", align: "center", sortable: false, width: "5.8rem" },
        { text: "ELIMINAR", align: "center", sortable: false, width: "5rem" },
      ],
    };
  },

  created() {
    // Método que se ejecuta cuando se crea el componente.
    // Lista los registros al cargar la página.
    this.listarRegistros();
  },

  methods: {

    /**
    * Valida que el campo no permita el ingreso de caracteres que no sean numéricos y solo permite 15 numeros.
    * @param {Event} event - Evento del teclado.
    */
    validarLetra(event) {
      const teclaPresionada = event.key;

      if (
        teclaPresionada.toLowerCase() !== "e" &&
        teclaPresionada !== "+" &&
        teclaPresionada !== ","
      ) {
        const regex = /[0-9$]/g;
        if (!regex.test(teclaPresionada)) {
          event.preventDefault();
        } else if (this.nuevoRegistro.numeroTelefonico.length >= 15) {
          event.preventDefault();
        }
      } else {
        event.preventDefault();
      }
    },


    /**
    * Crea un nuevo registro en el formulario y lo guarda en la base de datos.
    * Imprime en consola el registro registrado y realiza la actualización de la lista de registros.
    */
    crearRegistro() {
      const registro = {
        nombre: this.nuevoRegistro.nombre,
        numeroTelefonico: this.nuevoRegistro.numeroTelefonico,
        correoElectronico: this.nuevoRegistro.correoElectronico,
      }
      axios
        .post("http://localhost:8080/person/crear", registro)
        .then(response => {
          console.log("registro registrado: " + response);
          this.listarRegistros();
          this.reset();
        })
        .catch((error) => {
          console.log(error);
        });
    },

    /**
    * Actualiza un registro existente en la base de datos.
    * Imprime en consola el registro actualizado y realiza la actualización de la lista de registros.
    */
    actualizarRegistro() {
      const registroActualizado = {
        id: this.nuevoRegistro.id,
        nombre: this.nuevoRegistro.nombre,
        numeroTelefonico: this.nuevoRegistro.numeroTelefonico,
        correoElectronico: this.nuevoRegistro.correoElectronico,
      }
      axios
        .put("http://localhost:8080/person/actualizar", registroActualizado)
        .then(response => {
          console.log("registro actualizado: " + response);
          this.listarRegistros();
          this.reset();
        })
        .catch((error) => {
          console.log(error);
        });
    },

    /**
    * Edita el formulario del registro seleccionado.
    * @param {Object} item - Registro a editar.
    */
    editarRegistro(item) {
      this.nuevoRegistro = { ...item };
      this.tituloRegistro = "Actualizar registro";
      this.textoBoton = "ACTUALIZAR";
      this.dialog = true;
      this.editar = true;
      this.disabled = 1;
    },

    /**
    * Lista los registros de la base de datos.
    * Realiza una petición GET al backend y muestra los registros en una tabla paginada.
    */
    listarRegistros() {
      this.loading = true;
      axios
        .get(`http://localhost:8080/person/listar`)
        .then( response  => {
          this.listaDeRegistros = response.data;
          this.loading = false;
        })
        .catch((error) => {
          console.log(error);
          this.loading = false;
        });
    },

    /**
    * Abre el diálogo de confirmación para eliminar un registro.
    * @param {Object} registro - Registro a eliminar.
    */
    abrirDialogoEliminar(registro) {
      this.registroSeleccionado = registro;
      this.dialogoEliminar = true;
    },

    /**
    * Elimina un registro de la base de datos.
    * @param {number} id - ID del registro a eliminar.
    */
    eliminarRegistro(id) {
      axios
        .delete(`http://localhost:8080/person/eliminar/${id}`)
        .then(() => {
          this.dialogoEliminar = false;
          this.listarRegistros();
        })
        .catch((error) => {
          console.log(error);
        });
    },

    /**
    * Limpia el formulario de registro y reinicia los valores.
    * Restablece los valores iniciales del formulario y oculta el diálogo.
    */
    reset() {
      this.nuevoRegistro.id = "";
      this.nuevoRegistro.nombre = "";
      this.nuevoRegistro.numeroTelefonico = "";
      this.nuevoRegistro.correoElectronico = "";
      this.tituloRegistro = "Crear registro";
      this.textoBoton = "GUARDAR";
      this.dialog = false;
      this.editar = false;
      this.disabled = 0;
      this.dialogoEliminar = false;
      this.$refs.observer.reset();
    },
  },
};
</script>


<!-- #################################################################################### -->
<!-- ###### Sección de estilos                                                     ###### -->
<!-- #################################################################################### -->
<style scoped>
::v-deep .elevation div table thead tr th {
  background-color: rgb(223, 223, 223) !important;
}

::v-deep .elevation div table thead tr th span {
  font-weight: bold;
  color: black !important;
}

::v-deep .elevation .v-data-footer {
  width: 100%;
}

::v-deep .elevation .v-data-footer__select .v-select {
  margin: 5px;
  margin-left: 10px;
}

::v-deep .elevation .v-data-table__wrapper {
  border: 1px solid #f7f6f6;
}

.fondoDialog {
  background-color: #1867c0;
  color: white;
}

::v-deep #objeto {
  margin: 5px;
}

.formulario-flex {
  flex-wrap: nowrap;
}

@media (max-width: 700px) {
  .formulario-flex {
    flex-wrap: wrap !important;
  }
}
.titulo {
  background-color: #1867c0;
  text-align: center;
  padding: 10px;
}

.titulo-texto {
  font-weight: bold;
  font-size: 24px;
  color: white;
}
.agregar {
  background-color: #f1f1f1;
  padding: 10px;
  margin-top: 20px;
}

.agregar h3 {
  font-weight: bold;
  font-size: 20px;
  margin-right: 10px;
}
</style> 

